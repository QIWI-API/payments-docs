# Payment Through Merchant Web Form {#merchant-api-integration}

<aside class="warning">
When you integrate card payments with the merchant payment form, you send requests to the API with full card numbers and CVV/CVV2 code. This is only allowed if merchant's organization has a PCI DSS certificate.

PCI DSS is an information security standard adopted in the Visa and Mastercard payment card industry. All companies that accept cards are required to comply with the requirements of the standard.
</aside>

<aside class="notice">
According to Russian Central Bank 13-MP rule the compliance of the partner’s actual activities with those declared by him at the conclusion of the contract is checked. For this, as well as to reduce the risk of invalid and fraudulent transactions, the partner needs to <a href="/explain/en/qiwi-payin/partner-pay-form/fingerprint/">integrate a script</a> designed to collect additional client data when making payments.
</aside>

[Bank Card](#qiwi-form-card) payment method is available for integration by default. The following payment methods are enabled on demand:

* [Card payment token](#merchant-token-pay).
* [Faster Payments System](#merchant-form-sbp).

## Payment process {#flow-payment-merchant-form}

<div class="mermaid">
sequenceDiagram
participant customer as Customer
participant store as Merchant
participant qb as QIWI (acquirer)
participant ips as Issuer
customer->>store:Choose goods, start payment,<br/>enter card data
activate store
store->>qb:API: Payment request<br/>One-step payment — all payment methods<br/>Two-step payment — only cards
activate qb
qb->>store:Operation status, 3DS data or<br/>QR code for Faster Payment System
rect rgb(255, 238, 223)
Note over customer, ips:3-D Secure
store->>customer:Redirecting customer to acsUrl<br/>or to the bank app (Fast Payments System)
activate ips
ips->>customer:Customer authentication:<br/>3DS — cards,<br/>Faster Payment System — confirming the operation in the card issuer app
customer->>ips:Authentication
ips->>store:Result of the authentication (PaRes)
store->>qb:API: Completing customer authentication request
end
qb->>ips:Request for charging funds
activate ips
ips->>qb:Operation status
qb->>store:Notification on the status of the operation
store->>qb: Check operation status<br/>API: Payment status request
qb->>store: Operation status
rect rgb(255, 238, 223)
Note over store, ips:Two-step payment
store->>qb:API: Payment confirmation request
qb->>ips:Confirming card charging
deactivate ips
qb->>store:Notification on the payment confirmation
store->>qb: Check operation status<br/>API: Payment confirmation status request
qb->>store: Operation status
end
deactivate qb
deactivate store
</div>

To create a payment, send the following data in API request [Payment](#payments):

* [API access key](#api-auth);
* amount of payment;
* payment method;
* other information for payment creation.

## Bank card payment {#merchant-form-card}

The payment protocol supports both a two-step payment with holding funds on the customer's card, and a one-step payment without the authentication of the customer.

### Payment creation {#merchant-form-hold}

>Example of a payment with subsequent funds hold (two-step)

~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
  "amount": {
    "currency": "RUB",
    "value": 1.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "unknown cardholder"
  }
}
~~~

>Example of a payment with immediate client charging (one-step payment)

~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
  "amount": {
    "currency": "RUB",
    "value": 1.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "unknown cardholder"
  },
  "flags": [ "SALE" ]
}
~~~

To start payment with subsequent hold of funds on the client card (two-step payment), send the following data in API request [Payment](#payments):

* API access key;
* payment amount data;
* payment method parameters in `paymentMethod` object:
  * `type` — `CARD`;
  * `pan` — card number;
  * `expiryDate` – card expiry date in `MM/YY` format;
  * `cvv2` — card CVV2/CVC2;
  * `holderName` – card holder name (Latin letters);
* other information about the payment.

When the customer card was previously tokenized on merchant's side, the following parameters in `paymentMethod` object should be included:

* `cardTokenPaymentType` – parameter for correct processing of transactions in the payment systems. Allowed values:
  * `FIRST_PAYMENT` — when you are going to save the card data on the merchant's side;
  * `INITIATED_BY_CLIENT` — transaction on the saved card initiated by a client;
  * `INITIATED_BY_MERCHANT` — transaction on the saved card initiated by the merchant;
  * `RECURRING_PAYMENT` — recurring operation on the saved card;
  * `INSTALLMENT` — repeated operation on the saved card in accordance with payment schedule for credit repayment.
* `firstTransaction` – (optional) JSON-object with data of the transaction when card was saved before. Contains fields:
  * `paymentId` – unique payment identifier in RSP information system;
  * `trnId` – unique payment identifier in NSPK information system.

For the two-step payment (default option), the [reimbursement](#reimburse) is formed only after the [order confirmation](#merchant-capture).

<a name="one-step"></a>

<aside class="notice">
By default, when holding funds, the service expects <a href="#merchant-capture">confirmation of the payment</a> within 72 hours. At the end of the term, the payment is self-confirmed. To increase or reduce the waiting period, or to set up a payment auto-reversal, contact <a href="mailto:payin@qiwi.com">QIWI Support</a>. The waiting period may not last more than 5 days.
</aside>

To make one-step payment without the funds holding, include the `"flags":["SALE"]` parameter in the API request [Payment](#payments). If you do not pass this parameter, the funds holding for the payment is made and service waits for <a href="#merchant-capture">confirmation of the payment</a>.

### Awaiting the customer authentication (3-D Secure) {#merchant-threeds}

>Example of response with customer authentication requirement

~~~json
{
    "paymentId": "1811",
    "billId": "autogenerated-a29ea8c9-f9d9-4a60-87c2-c0c4be9affbc",
    "createdDateTime": "2019-08-15T13:28:26+03:00",
    "amount": {
        "currency": "RUB",
        "value": 1.00
    },
    "capturedAmount": {
        "currency": "RUB",
        "value": 0.00
    },
    "refundedAmount": {
        "currency": "RUB",
        "value": 0.00
    },
    "paymentMethod": {
        "type": "CARD",
        "maskedPan": "444444******1049",
        "rrn": "123",
        "authCode": "181218",
        "type": "CARD"
    },
    "status": {
        "value": "WAITING",
        "changedDateTime": "2019-08-15T13:28:26+03:00"
    },
    "requirements" : {
        "threeDS" : {
          "pareq" : "eJyrrgUAAXUA+Q==",
          "acsUrl" : "https://test.paymentgate.ru/acs/auth/start.do"
        }
    }
}
~~~

> Redirecting for 3-D Secure authentication

~~~html
<form name="form" action="{ACSUrl}" method="post" >
        <input type="hidden" name="TermUrl" value="{TermUrl}" >
        <input type="hidden" name="MD" value="{MD}" >
        <input type="hidden" name="PaReq" value="{PaReq}" >
</form>
~~~

> Finishing customer authentication

~~~http
POST /partner/payin/v1/sites/test-01/payments/1811/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
  "threeDS": {
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
  }
}
~~~

If bank requires 3-D Secure authentication of the customer, the payment response contains the `requirements.threeDS` JSON-object with fields:

- `acsUrl` — 3-D Secure authentication server URL to redirect to the issuer's confirmation page;
- `pareq` — an encrypted 3-D Secure authentication request.

To proceed with additional authentication from the issuer, send a POST form to the 3-D Secure authentication server URL with parameters:

- `TermUrl` — customer redirection URL after successful 3-D Secure authentication;
- `MD` — a unique transaction identifier;
- `PaReq` — the `pareq` value from the response to the payment request.

To maintain backward compatibility, using 3-D Secure 1.0 or 3-D Secure 2.0 does not affect merchant's integration with the API.

The customer's information is passed to the card payment system. The issuer bank either grants permission to charge funds without authentication (frictionless flow) or decides whether to authenticate with a single-time password (challenge flow). After the authentication is passed, the customer is redirected to `TermUrl` URL with the encrypted result of the authentication in the `PaRes` parameter.

To complete the authentication of the customer, pass on the API request [Completing customer authentication](#payment_complete):

* unique RSP ID;
* payment number (`paymentId` option) from the response to the payment request;
* 3-D Secure verification value (`PaRes` value)

### Confirm payment {#merchant-capture}

>Notification example

~~~json
{
  "payment":{
    "paymentId":"804900",  <==paymentId required for 'capture'
    "type":"PAYMENT",
    "createdDateTime":"2020-11-28T12:58:49+03:00",
    "status":{
        "value":"SUCCESS",
        "changedDateTime":"2020-11-28T12:58:53+03:00"
    },
    "amount":{
      "value":100.00,
      "currency":"RUB"
    },
    "paymentMethod":{
      "type":"CARD",
      "maskedPan":"444444XXXXXX4444",
      "rrn":null,
      "authCode":null,
      "type":"CARD"
    },
    "merchantSiteUid":"test-00",
    "customer":{
      "phone":"75167693659"
    },
    "gatewayData":{
      "type":"ACQUIRING",
      "eci":"6",
      "authCode":"181218"
    },
    "billId":"autogenerated-a51d0d2c-6c50-405d-9305-bf1c13a5aecd",
    "flags":[]
  },
  "type":"PAYMENT",
  "version":"1"
}
~~~

>Capture example

~~~http
PUT /partner/payin/v1/sites/{siteId}/payments/804900/captures/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com
~~~

The capture step is required only for two-step payments with holding funds.

To confirm payment:

* Get `paymentId` of the payment:
  * From [notification](#payment-callback) after successful holding funds.
  * From the response to [Payment status](#payment_status) API request.
* Send API request [Confirm payment](#capture) with received `paymentId` value.

## Payment token {#merchant-token-pay}

>Using payment token in a payment request

~~~http
PUT /partner/payin/v1/sites/test-02/payments/1815 HTTP/1.1
Accept: application/json
Authorization: Bearer 7uc4b25xx93xxx5d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
  "amount": {
    "currency": "RUB",
    "value": 2000.00
  },
  "paymentMethod" : {
    "type": "TOKEN",
    "paymentToken" : "66aebf5f-098e-4e36-922a-a4107b349a96"
  },
  "customer": {
        "account": "token324"
  }
}
~~~

The payment tokens are used for charging a customer balance without entering card details. By default, the use of payment tokens is disabled. Contact your manager in QIWI Support to enable that.

When using payment token for the payment, customer does not have to enter its card data and proceed with 3-D Secure authentication.

The issue of a payment token is described [in this section](#payment-token-issue).

<aside class="warning">
Customer is able to make payment by payment token only on the site where payment token was issued.

To make the payment token valid on other sites, send a request to <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

To pay for the customer order with its payment token, send the following data in API request [Payment](#payments):

* API access key;
* payment amount data;
* payment method parameters in `paymentMethod` object:
  * `type` – `TOKEN`;
  * `paymentToken` – payment token string;
* additional information about the saved card in `paymentMethod` object:
  * `cardTokenPaymentType` – parameter for correct processing of transactions in the payment systems. Allowed values:
    * `INITIATED_BY_CLIENT` — transaction on the saved card initiated by a client;
    * `INITIATED_BY_MERCHANT` — transaction on the saved card initiated by the merchant;
    * `RECURRING_PAYMENT` — recurring operation on the saved card;
    * `INSTALLMENT` — repeated operation on the saved card in accordance with payment schedule for credit repayment.
  * `firstTransaction` – JSON-object with transaction identifier info where card was saved. Contains fields:
    * `paymentId` – unique payment identifier in RSP information system;
    * `trnId` – unique payment identifier in NSPK information system.
* customer identifier in the RSP system for which the payment token was issued, in `customer.account` parameter (**without this parameter, you cannot pay with the payment token.**);
* other information about the payment.

## Faster Payments System {#merchant-form-sbp}

**Payment protocol** supports charging funds from the customer by [Faster Payments System](https://sbp.nspk.ru/) (FPS). With FPS, payment can be made to commercial organizations, including QR coded ones.

By default, FPS payment method is turned off. To use this method, contact your manager in QIWI Support.

### Receiving QR code {#qr-sbp}

> Example of request for FPS payment

~~~json
{
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
    "image": {
      "mediaType": "image/png",
      "width": 300,
      "height": 300
    }
  },
  "paymentPurpose": "Flower for my girlfriend",
  "redirectUrl": "http://someurl.com"
}
~~~

> Example of response with QR code

~~~json
{
  "qrCodeUid": "Test12",
  "amount": {
    "currency": "RUB",
    "value": "100.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
    "image": {
        "mediaType": "image/png",
        "width": 300,
        "height": 300,
        "content": "iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAYAA"
    },
    "payload": "https://qr.nspk.ru/AD10006M8KH234K782OQM0L13JI31LQDtype=02bank=100000000009&sum=200&cur=RUB&crc=C63A",
    "status": "CREATED"
  },
  "createdOn": "2022-08-11T20:10:32+03:00"
}
~~~

When paying with Faster Payment System a customer scans a QR code and receives a payment link to open and make payment in her bank application.

To create a QR code for the payment, send the API request [Faster Payment System QR code](#qr-code-sbp) with specific parameters:

* Unique QR code API request identifier.
* `qrCode` object with QR code parameters:
   * `qrCode.type`— `DYNAMIC`.
   * `qrCode.ttl` —  code lifetime (minutes). The QR code is deactivated after the period ends. Default value is 72 hours.
   * `qrCode.image` — QR code image size and type.
* `amount` — payment amount.
* `paymentPurpose` — payment details. If it is empty customer bank app displays merchant's store title.

In the response, the JSON object `qrCode` contains the data of the QR code:

* `image.content` — base64-encoded QR code. After decryption, you get an image to show to the customer.
* `payload` — URL-based QR for customer redirection to its bank app.

### FPS payment status {#sbp-status}

When payment status becomes final, the [notification](#payment-callback) will be sent with the corresponding QR code API request identifier in `qrCodeUid` field. Payment status can be determined with `paymentId` identifier from the same notification by the [API request](#payment_status).

### QR code status {#qr-code-status}

> Example of response for FPS QR code status request

~~~json
{
  "qrCodeUid": "Test",
  "amount": {
    "currency": "RUB",
    "value": "1.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
    "payload": "https://qr.nspk.ru/AD10006M8KH234K782OQM0L13JI31LQDtype=02bank=100000000009&sum=200&cur=RUB&crc=C63A",
    "status": "PAYED"
  },
  "payment": {
    "paymentUid": "A22231710446971300200933E625FCB3",
    "paymentStatus": "COMPLETED"
  },
  "createdOn": "2022-08-11T20:10:32+03:00"
}
~~~

To get QR code information you can use the [Faster Payment System QR Code Status](#qr-code-sbp-get) API request. Response contains QR code details including its current status so you can check if it is still a valid one.

### FPS token payment {#token-sbp-payment}

> Request body for payment with FPS token

~~~json
{
  "tokenizationAccount": "customer123",
  "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6"
}
~~~

See details about FPS payment token issue in [this section](#merchant-form-token-issue-sbp).

<aside class="warning">
Customer is able to make payment by payment token only on the site where payment token was issued.

To make the payment token valid on other sites, send a request to <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

Use [Payment by FPS token](#payment-sbp-token) API method with the following parameters:

* `token` — FPS payment token;
* `tokenizationAccount` — client identifier for which the token was issued.

### Testing operations {#test-fps}

See information in [this section](#test_data_sbp).

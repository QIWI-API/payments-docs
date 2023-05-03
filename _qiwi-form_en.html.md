# Payment Through QIWI Form {#invoicing}

When you integrate payments through the QIWI form, the only available payment method is by bank cards. The following payment methods are enabled on demand:

* [Card payment token](#qiwi-token-pay).
* Faster Payments System.
* QIWI Wallet.

To create invoice for a payment, use [API to issue an invoice](#qiwi-form-invoice-api) or simply redirect customer to the QIWI Form by the [direct link with the invoice data](#https-qiwi-form).

## Payment process {#flow-payment-qiwi-form}

<div class="mermaid">
sequenceDiagram
participant customer as Customer
participant store as Merchant
participant qb as QIWI (acquirer)
participant ips as Issuer
customer->>store:Choose goods, Start payment
activate store
store->>qb:API: Invoice request<br/>One-step payment — all payment methods<br/>Two-step payment — only cards
activate qb
qb->>store:Response with QIWI Payment form URL (payUrl)
store->>customer:Redirecting customer to payUrl
customer->>qb:Open Payment form,<br/>choose payment method,<br/>enter details for chosen method
qb->>customer:Customer authentication:<br/>3-D Secure for cards
customer->>qb:Authentication
qb->>ips:Request for funds charging
activate ips
ips->>qb:Operation status
qb->>store:Notification with operation status
qb->>customer: Return to the merchant site on successful payment (successUrl)
store->>qb: Check operation status<br/>API: Payment status request
qb->>store: Operation status
rect rgb(255, 238, 223)
Note over store, ips:Two-step payment
store->>qb:Payment confirmation<br/>API: Payment confirmation request
qb->>ips:Confirming card charging
deactivate ips
qb->>store:Notification on payment confirmation
store->>qb: Check operation status<br/>API: Payment confirmation status request
qb->>store: Operation status
end
deactivate qb
deactivate store
</div>

## QIWI Payment Form integration without API {#https-qiwi-form}

For merchants, this is the way to integrate without API methods implementation.

Invoice parameters are included into the Payment Form URL — see [examples and parameters list](#payform_flow) below. When the form is opened, the customer is automatically billed for the order.

<aside class="success">
Another way to integrate without API is to implement the <a href="#createpopup">invoice method of Popup library</a>.
</aside>

<aside class="notice">
QIWI Form interface language (Russian by default) can be changed to English. Contact your manager in QIWI Support.
</aside>

When paying an invoice issued this way the payment is authorized without the participation of the merchant. As the two-step payment scheme is used (authorization and confirmation), you need to confirm payment in your [Account Profile](https://kassa.qiwi.com/service/core/merchants?). By default, the service waits for the payment confirmation in 72 hours. When time is up the payment is confirmed automatically.

<aside class="notice">
To reduce the waiting time, or to set up a payment auto-refund, contact <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

<h3 class="request method" id="payform_flow">GET → </h3>

> Link to the form with payment amount

~~~shell
https://oplata.qiwi.com/create?publicKey=5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3ceEvMvCq55ToeErzhvK6rVkQLaCrYUQcYF5QkS8nCrjnPsLQgsLxqrpQgJ7hg2ZHmEHXFjaG8qjvgcep&extras[cf1]=Order_123&extras[cf3]=winnie@pooh.ru&readonly_extras=cf1&comment=some%20comment&amount=100.00
~~~

> Link to the form without payment amount (it is filled in by the customer)

~~~shell
https://oplata.qiwi.com/create?publicKey=5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3ceEvMvCq55ToeErzhvK6rVkQLaCrYUQcYF5QkS8nCrjnPsLQgsLxqrpQgJ7hg2ZHmEHXFjaG8qjvgcep&extras[cf1]=Order_123&extras[cf3]=winnie@pooh.ru&readonly_extras=cf1
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/create?publicKey={key}&{parameter}={value}</span></h3></li>
</ul>

<aside class="notice">
When opening Payment Form in Webview on Android, you should enable <code>settings.setDomStorageEnabled(true)</code> option.
</aside>

<ul class="nestedList params">
    <li><h3>Parameters</h3><span>Invoice parameters put to the URL query.</span></li>
</ul>

Parameter         | Description                                                                                                                                                                                                                                        | Type
------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------
publicKey         | **Required**. Merchant identification key. For each `siteId` the unique key is produced. You [can get the key](/en/payments-lk-guide/#settings) in your [Account Profile](https://kassa.qiwi.com/service/core/merchants?) in **Settings** section. | String
billId            | Unique invoice identifier in the merchant's system. It must be generated on your side with any means. Arbitrary sequence of digits and letters, `_` and `-` symbols as well. If not used, on each URL opening a new invoice is created.            | URL-encoded string String(200)
amount            | Amount of customer order rounded down to 2 digits (always in rubles)                                                                                                                                                                               | Number(6.2)
currency          | Currency code for the purchase. Possible values: `RUB`, `EUR`, `USD`. Default value `RUB`                                                                                                                                                          | String(3)
phone             | Customer phone number (international format)                                                                                                                                                                                                       | URL-encoded string
email             | Customer e-mail                                                                                                                                                                                                                                    | URL-encoded string
comment           | Comment to the invoice                                                                                                                                                                                                                             | URL-encoded string String(255)
successUrl        | URL for a customer return to the merchant site after the successful payment. URL should be UTF-8 encoded.                                                                                                                                          | URL-encoded string
paymentMethod     | Payment method suggested to the customer on the QIWI form. Possible values: `CARD`, `SBP`, `QIWI_WALLET`. If the method is not enabled for the merchant, an available method is suggested. By default, `CARD`.                                     | String
extras[cf1]       | Extra field to add any information to invoice data                                                                                                                                                                                                 | URL-encoded string
extras[cf2]       | Extra field to add any information to invoice data                                                                                                                                                                                                 | URL-encoded string
extras[cf3]       | Extra field to add any information to invoice data                                                                                                                                                                                                 | URL-encoded string
extras[cf4]       | Extra field to add any information to invoice data                                                                                                                                                                                                 | URL-encoded string
extras[cf5]       | Extra field to add any information to invoice data                                                                                                                                                                                                 | URL-encoded string
extras[themeCode] | Extra field with [code style of the Payment form](#custom)                                                                                                                                                                                         | URL-encoded string
readonly_extras   | List of the extra fields which customer cannot edit on the invoice pay form                                                                                                                                                                        | String, separator of the field names `,`. Example: `cf1,cf3`

By default, the customer is automatically authenticated after the invoice is paid. Authentication is also automatically completed.

## API invoice issue and QIWI payment form {#qiwi-form-invoice-api}

>Invoice creation when payment is put on hold (two-step payment)

~~~http
PUT /partner/payin/v1/sites/{siteId}/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 42.24
   },
   "billPaymentMethodsType": [
      "QIWI_WALLET",
      "SBP"
   ],
   "comment": "Spasibo",
   "expirationDateTime": "2019-09-13T14:30:00+03:00",
   "customFields": {}
}
~~~

> Notification with paymentId

~~~json
  "payment": {
    "type": "PAYMENT",
    "paymentId": "824c7744-1650-4836-abaa-842ca7ca8a74",  <== paymentId necessary for confirmation
    "createdDateTime": "2022-07-27T12:43:35+03:00",
    "status": {
        "value": "SUCCESS",
        "changedDateTime": "2022-07-27T12:43:47+03:00"
    },
    "amount": {
        "value": 1.00,
        "currency": "RUB"
    },
    "paymentMethod": {
        "type": "CARD",
        "maskedPan": "512391******6871",
        "cardHolder": null,
        "cardExpireDate": "3/2030"
    },
    "tokenData": {
        "paymentToken": "cc123da5-2fdd-4685-912e-8671597948a3",
        "expiredDate": "2030-03-01T00:00:00+03:00"
    },
    "customFields": {
        "cf2": "dva",
        "cf1": "1",
        "cf4": "4",
        "cf3": "tri",
        "cf5": "5",
        "full_name": "full_name",
        "phone": "phone",
        "contract_id": "contract_id",
        "comment": "test",
        "booking_number": "booking_number"
    },
    "paymentCardInfo": {
        "issuingCountry": "643",
        "issuingBank": "Tinkoff Bank",
        "paymentSystem": "MASTERCARD",
        "fundingSource": "UNKNOWN",
        "paymentSystemProduct": "TNW|TNW|Mastercard® New World—Immediate Debit|TNW|Mastercard New World-Immediate Debit"
    },
    "customer": {
        "email": "darta@mail.ru",
        "account": "11235813",
        "phone": "79850223243"
    },
    "gatewayData": {
        "type": "ACQUIRING",
        "eci": "2",
        "authCode": "0123342",
        "rrn": "001239598011"
    },
    "billId": "191616216126154",
    "flags": [
        "AFT"
    ]
  },
  "type": "PAYMENT",
  "version": "1"
}
~~~

> Payment confirmation

~~~http
PUT /partner/payin/v1/sites/{siteId}/payments/804900/capture/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

>Invoice creation when payment is processed without customer authentication (one-step payment)

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 100.00
   },
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "flags": [
     "SALE"
    ]
}
~~~

<aside class="notice">
On the QIWI Payment Form, by default, the customer can only pay by bank card. To connect the other payment methods contact your manager in QIWI Support.
</aside>

The payment protocol supports both a two-step payment with holding funds on the customer's card and a one-step payment without the authentication of the customer.

In two-step payment scenario:

1. Create an invoice using the API request [Invoice](#invoice_put) with parameters:

   * The [API access key](#api-auth).
   * The amount of the invoice (`amount`).
   * The date before which the invoice must be paid (`expirationDateTime`).
   * (optional) Other invoice data:
     * Customer data (`customer`, `address`).
     * Comment on the invoice (`comment`).
     * Other information (`customFields`).

   To limit payment methods accessible for the customer on the Payment form, specify them in `billPaymentMethodsType` API parameter. Listed methods should be enabled for `siteId` of the API request.

2. [Redirect the customer to QIWI Payment form](#qiwi-redirect) using URL from `payUrl` parameter of the API response, or use [Popup JavaScript library](#popup) to [open the form in a popup window](#openpopup).
3. Get `paymentId` identifier of the payment:
   * From [server notification](#payment-callback) after successful holding of funds.
   * From the response to the [Invoice payments list](#invoice-payments) request.
4. Send API request [Payment confirmation](#capture) with received `paymentId` identifier.
5. Wait for the payment confirmation. Either you receive a [notification](#capture-callback), or send [Capture status](#capture_status) API request in cycle to get an information about the capture.

The [reimbursement](#reimburse) is formed only after the payment confirmation.

<aside class="notice">
By default, when holding funds, the service expects confirmation of payment within 72 hours. At the end of the term, the payment is self-confirmed. To increase or reduce the waiting period, or to set up a payment auto-reversal, contact <a href="mailto:payin@qiwi.com">QIWI Support</a>. The waiting period may not last more than 5 days.
</aside>

<a name="one-step">

In one-step payment scenario:

1. Create an invoice using the API request [Invoice](#invoice_put) with parameters:

   * The [API access key](#api-auth).
   * The amount of the invoice (`amount`).
   * The date before which the invoice must be paid (`expirationDateTime`).
   * The flag for one-step scenario `"flags":["SALE"]`.
   * (optional) Other invoice data:
     * Customer data (`customer`, `address`).
     * Comment on the invoice (`comment`).
     * Other information (`customFields`).

   To limit payment methods accessible for the customer on the Payment form, specify them in `billPaymentMethodsType` API parameter. Listed mods should be enabled for `siteId` of the API request.

2. [Redirect the customer to QIWI Payment form](#qiwi-redirect) using URL from `payUrl` parameter of the API response, or use [Popup JavaScript library](#popup) to [open the form in a popup window](#openpopup).
3. Wait for the payment completion. Either you receive a [notification](#payment-callback), or send [Invoice status](#invoice-details) API request in cycle to get an information about the payment.

### Payment token {#qiwi-token-pay}

> Invoice payable with payment token

~~~http
PUT /partner/payin/v1/sites/test-02/bills/1815 HTTP/1.1
Accept: application/json
Authorization: Bearer 7uc4b25xx93xxx5d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {
     "account": "token234"
   },
   "customFields": {
    "cf1": "Some data"
   }
   }
}
~~~

The payment tokens are used for charging a customer balance without entering card details or QIWI Wallet number. By default, the use of payment tokens is disabled. Contact your manager in QIWI Support to enable that.

See details of the issue of a payment token [in this section](#payment-token-issue).

<aside class="warning">
Customer will be able to make payment by payment token only on the site where payment token was issued.

To make the payment token valid on other sites, send a request to <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

To create an invoice payable with payment token, send in API request [Invoice](#invoice_put) the following data:

* API access key.
* Amount of the invoice (`amount`).
* Last date of payment for the invoice (`expirationDateTime`).
* Customer identifier for which the payment token was issued, in `customer.account` parameter. **Payment by payment token is not possible without this parameter.**
* Other information about the invoice (`comment`, `customFields`).

Then [redirect the customer to QIWI Payment form](#qiwi-redirect) using URL from `payUrl` parameter of the API response, or use [Popup JavaScript library](#popup) to [open the form in a popup window](#openpopup). If one or more payment tokens have been issued for the customer, the Payment form would display their linked cards.

![qiwi-form-tokens](/images/payin/qiwi-form-token-en.png)

To use the payment token, the customer chooses a card from the drop-down list. Card data or 3-D Secure authentication is not required.

<aside class="warning">
Contact your manager in QIWI Support to enable displaying list of cards linked to the payment tokens. Otherwise a customer may not use linked cards on the Form. A list of cards is displayed for payments from the same site for which the tokens are issued.
</aside>

To charge funds on a payment token without the customer's participation, use the API method [Payment](#payment). See details in section [Using payment token](#merchant-token-pay) for the merchant's payment form.

## Redirect to QIWI Form {#qiwi-redirect}

>Response with payUrl parameter

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "siteId": "test-01",
    "billId": "gg",
    "amount": {
        "currency": "RUB",
        "value": 42.24
    },
    "status": {
        "value": "WAITING",
        "changedDateTime": "2019-08-28T16:26:36.835+03:00"
    },
    "customFields": {},
    "comment": "Spasibo",
    "creationDateTime": "2019-08-28T16:26:36.835+03:00",
    "expirationDateTime": "2019-09-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=78d60ca9-7c99-481f-8e51-0100c9012087"
}
~~~

After invoice creation in API, the URL of the QIWI Form is taken from `payUrl` field of the API response.

<aside class="notice">
When opening Payment Form in Webview on Android, you should enable <code>settings.setDomStorageEnabled(true)</code> option.
</aside>

> Example of the URL with successUrl parameter

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://example.com/payments/#introduction
~~~

You can add the following parameter to the URL:

| Parameter     | Description                                                                                                                                                         | Type                         |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| successUrl    | URL for a customer return to the merchant site after the successful payment. Redirect proceeds after the successful 3DS authentication. URL should be UTF-8 encoded.| URL-encoded string           |
| lang          | Interface language of the QIWI form. By default, `ru`                                                                                                               | `ru`, `eng`                  |
| paymentMethod | Payment method suggested to the customer on the QIWI form. If the method is not enabled for the merchant, some available method is suggested. By default, `CARD`.   | `CARD`, `SBP`, `QIWI_WALLET` |

> Example of event listener for iframe

~~~javascript
window.addEventListener('message', function (event) {
    switch (event.data) {
        case 'INITIALIZED':
            // Form loaded
            break;
        case 'PAYMENT_ATTEMPT':
            // Payment attempt
            break;
        case 'PAYMENT_SUCCEEDED':
            // Payment successful
            break;
        case 'PAYMENT_FAILED':
            // Payment failed
            break;
    }
}, false)
~~~

By default, 3-D Secure is required on the QIWI Form.

When opening URL of the QIWI Form in `<iframe>`, use additional parameter `allow`:

`<iframe allow="payment" src="<payUrl link> ..." />`

You can use `postMessage` [method](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) to listen events in the Form.

Possible values of the Form state:

* `INITIALIZED` — Form loaded.
* `PAYMENT_ATTEMPT` — Payment attempt was performed.
* `PAYMENT_SUCCEEDED` — Payment successfully processed.
* `PAYMENT_FAILED` — Payment failed.
* `INITIALIZATION_FAILED` — Error on the Form loading.

<aside class="warning">
QIWI Wallet payment may not be possible on the Payment form in iframe on the merchant's site. After QIWI Wallet authentication qiwi.com domain cookies have to be installed; some browsers block installation of cookies from third-party domains. See details in the <a href="https://webkit.org/tracking-prevention/#intelligent-tracking-prevention-itp">WebKit documentation</a>.
</aside>

## Checkout Popup Library {#popup}

The library helps to use QIWI Payment Form in a popup. It has two methods:

* [Create a new invoice](#createpopup).
* [Open an existing invoice](#openpopup).

<button id="pop" class="button-popup">Demo popup</button>

To install the library, add the following script into your web-page:

`<script src='https://oplata.qiwi.com/popup/v2.js'></script>`

<aside class="warning">
QIWI Wallet payment may not be possible on the Payment form in popup on the merchant's site. After QIWI Wallet authentication, qiwi.com domain cookies have to be installed, but some browsers block installation of cookies from third-party domains. See details in the <a href="https://webkit.org/tracking-prevention/#intelligent-tracking-prevention-itp">WebKit documentation</a>.
</aside>

### Create invoice {#createpopup}

>Create new invoice

~~~javascript
params = {
    publicKey: '5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3c******',
    amount: 10.00,
    phone: '79123456789',
    email: 'test@example.com',
    account: 'account1',
    comment: 'Payment',
    customFields: {
        data: 'data'
    },
    lifetime: '2022-04-04T1540'
}

QiwiCheckout.createInvoice(params)
    .then(data => {
        // ...
    })
    .catch(error => {
        // ...
    })
~~~

To create invoice and open its payment form in a popup, call method  `QiwiCheckout.createInvoice`. Method has the following parameters:

| Parameter    | Description                                                                                                                                                                                                                                        | Format                               |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| publicKey    | **Required**. Merchant identification key. For each `siteId` the unique key is produced. You [can get the key](/en/payments-lk-guide/#settings) in your [Account Profile](https://kassa.qiwi.com/service/core/merchants?) in **Settings** section. | String                               |
| amount       | **Required**. Amount of the invoice rounded down on two decimals                                                                                                                                                                                   | Number(6.2)                          |
| phone        | Phone number of the client to which the invoice is issuing (international format)                                                                                                                                                                  | String                               |
| email        | E-mail of the client where the invoice payment link will be sent                                                                                                                                                                                   | String                               |
| account      | Client identifier in merchant’s system                                                                                                                                                                                                             | String                               |
| comment      | Invoice commentary                                                                                                                                                                                                                                 | String(255)                          |
| customFields | Additional invoice data. Obtain full list of data fields in the description of the same parameter in [invoice API request](#invoice_put)                                                                                                           | Object                               |
| lifetime     | Invoice payment’s due date. If the invoice is not paid before that date, it assigns final status and becomes void                                                                                                                                  | URL-encoded string `YYYY-MM-DDThhmm` |

### Open invoice {#openpopup}

>Open an existing invoice in popup

~~~javascript
params = {
    payUrl: '<URL of the invoice Pay form>'
}

QiwiCheckout.openInvoice(params)
    .then(data => {
        // ...
    })
    .catch(error => {
        // ...
    })
~~~

To open existing invoice payment form in a popup, call method  `QiwiCheckout.openInvoice`. Method has single parameter:

| Parameter | Description                               | Type   |
|-----------|-------------------------------------------|--------|
| payUrl    | **Required**. URL of the invoice Pay form | String |

## Customization of QIWI Payment Form  {#custom}

>Example of calling Custom Payment form

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {},
   "customFields": {
     "themeCode":"merchant01-style01"
    }
}
~~~

Add your style, customizable logo, background, and color of the buttons to the Payment form on the QIWI side. To do so, contact [QIWI Support](mailto:payin@qiwi.com) and provide the following information:

* Unique alias for the Payment form (latin letters, digits, and `-` dash symbol).
* Merchant name to be displayed on the Form.
* Short description of your service (no more than 120 symbols).
* Logo in PNG or SVG format with 48x48 size or proportionally larger.
* Color of background and buttons, in HEX.

Some optional data are also used:

* Background image in PNG or SVG format with 382x560 size or proportionally larger.
* A list of three preloaded (the most often) amount of payment.
* Contact e-mail to display on the Form.
* Success payment redirection URL.
* Yandex.Metrika counter identifier.
* Offer reference.

To use your Custom Payment form:

* Send the alias for the Payment form in `"themeCode"` field of `customFields` object of API request [Invoice](#invoice_put):

     `"themeCode":"merchant01-theme01"`.

     URL received in `payUrl` field of the API response points to the Custom Payment form.

* Send the alias for the Payment form [in direct call of the form](#https-qiwi-form) in `extras[themeCode]` parameter:

   `...&extras[themeCode]=merchant01-theme01`.

Example of the customized Payment form:

![Customer form](/images/payin/qiwi-form-custom-en.png)

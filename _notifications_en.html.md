# Server Notifications {#callback}

QIWI server notification is an incoming HTTP POST request. The JSON-formatted notification message in the request body contains payment/invoice data encoded in UTF-8 codepage.

The Protocol supports the following notification types for API events: 

* [`PAYMENT`](#payment-callback) — sends when a payment operation is made;
* [`CAPTURE`](#capture-callback) — sends when a payment confirmation is made;
* [`REFUND`](#refund-callback) — sends when a refund for payment is made;
* [`CHECK_CARD`](#checkcard-callback) — send when a card verification is made.

<aside class="notice">
There is no specific sequence of sending different types' notifications for the same operation. The sequence may vary for different operations.
</aside>

Specify the notification server address in your [Account Profile](https://kassa.qiwi.com/service/core/merchants?) in **Settings** section.

To put different notification address for a separate operation, use the following parameters in the API requests:

* `callbackUrl` parameter — in the [Payment](#payments), [Payment confirmation](#capture), [Refund](#refund) API requests.
* `customFields.invoice_callback_url` parameter — in the [Invoice](#invoice_put) API request.

The URL for notifications should start with `https`, as notifications are sent by HTTPS to port 443.
The site certificate must be issued by a trusted certification center (e.g. Comodo, Verisign, Thawte, etc.)

<aside class="notice">
If you don't receive a transaction notification within 10 minutes of the transaction, you must request the status of the transaction by <a href="#invoice-details">requesting invoice status</a> or <a href="#payment_status">requesting payment status</a> (depending on which way you use the API).
</aside>

To make sure the notification is from QIWI, we recommend you to accept messages only from the following IP addresses belonging to QIWI:

* 79.142.16.0/20
* 195.189.100.0/22
* 91.232.230.0/23
* 91.213.51.0/24

To treat notification as successfully delivered, we need your notification server to respond with HTTP code `200 OK`. Until this moment, QIWI system will try to resend the notification message several times with growing interval during the day from the operation started.

<aside class="success">
If by any reason RSP server accepts the notification message but responds incorrectly, then on receiving notification with the same data next time it should not be treated as a new event.
</aside>

## Notification Authorization {#notifications-auth}

> Notification headers example

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-Type: application/json
Signature: j4wnfnzd***v5mv2w=
Host: example.com
~~~

The notification contains a digital signature of the request data which RSP should verify on its side to secure from notification fraud.

<aside class="warning">
The responsibility for any financial losses due to omitted verification of the signature parameter lies solely on RSP.
</aside>

The UTF-8 encoded digital signature is placed into HTTP header `Signature` of the notification message.

To validate the signature, HMAC integrity check with SHA256-hash is used.

Implement the following algorithm to verify notification signature:

1. Join values of some parameters from the notification with the pipe "\|" character as a separator. For example:

   `parameters = {payment.paymentId}|{payment.createdDateTime}|{payment.amount.value}`

   where `{*}` – notification parameter value. All values must be converted to UTF-8 encoded string representation.

   Signature should be verified for those notification fields:

      * `PAYMENT` type:`payment.paymentId|payment.createdDateTime|payment.amount.value`
      * `REFUND` type:`refund.refundId|refund.createdDateTime|refund.amount.value`
      * `CAPTURE` type:`capture.captureId|capture.createdDateTime|capture.amount.value`
      * `CHECK_CARD` type: `checkPaymentMethod.requestUid|checkPaymentMethod.checkOperationDate`

2. Calculate hash HMAC value with SHA256 algorithm (signature string and secret key should be UTF8-encoded):

   `hash = HMAC(SHA256, secret, parameters)`

   where:

      * `secret` – HMAC hash key. It is the server notification key in **Settings** section of the Merchant Account Profile.
      * `parameters` – string from step 1.

3. Compare the notification signature from `Signature` HTTP-header with the result of step 2. If there is no difference, the validation is successful.

## Frequency of notification sending {#notification-rate}

QIWI notification service sorts unsuccessful notifications on the following queues:

* One attempt on waiting 5 seconds
* One attempt on waiting 1 minutes
* Three attempts on waiting 5 minutes

Time of secondary sending notification may slightly shift upward.

## PAYMENT Notification Format {#payment-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> PAYMENT Notification example

~~~json
{
  "payment": {
    "paymentId": "A22170834426031500000733E625FCB3",
    "customFields": {},
    "type": "PAYMENT",
    "createdDateTime": "2022-08-05T11:34:42+03:00",
    "status": {
      "value": "SUCCESS",
      "changedDateTime": "2022-08-05T11:34:44+03:00"
    },
    "amount": {
      "value": 5,
      "currency": "RUB"
    },
    "paymentMethod": {
      "type": "SBP",
      "phone": "79111112233"
    },
    "customer": {
      "phone": "0",
      "bankAccountNumber": "4081710809561219555",
      "bic": "044525974",
      "lastName": "IVANOV",
      "firstName": "IVAN",
      "middleName": "IVANOVICH",
      "simpleAddress": ""
    },
    "billId": "autogenerated-6cd20922-b1d0-4e67-ba61-e2b7310c4006",
    "flags": [
      "SALE"
    ]
  },
  "type": "PAYMENT",
  "version": "1"
}
~~~

Notification field | Description | Type | When present
--------|--------|---|----
payment | Payment information | Object | Always
----|------|-------|--------
payment.<br>type| [Operation type](#operation-types)|String(200)|Always
payment.<br>paymentId|Payment operation unique identifier in RSP's system|String(200)|Always
payment.<br>createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`|Always
payment.<br>billId| Corresponding invoice ID for the operation| String(200)|Always
payment.<br>qrCodeUid| QR-code issue operation identifier in RSP's system|String|In case of operation with Faster Payment System
payment.<br>amount|Object| Operation amount data|Always
----|------|-------|--------
payment.<br>amount.<br>value | Operation amount rounded down to two decimals | Number(6.2)|Always
payment.<br>amount.<br>currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)|Always
----|------|-------|--------
payment.<br>status | Operation status data | Object|Always
----|------|-------|--------
payment.<br>status.<br>value |[Operation status value](#notification-statuses) | String|Always
payment.<br>status.<br>changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`|Always
payment.<br>status.<br>reasonCode| [Rejection reason code](#reason-codes)| String(200)|In case of operation rejection
payment.<br>status.<br>reasonMessage| Rejection reason description| String(200)|In case of operation rejection
payment.<br>status.<br>errorCode| Error code| Number|In case of error
----|------|-------|--------
payment.<br>paymentMethod| Payment method data| Object|Always
----|------|-------|--------
payment.<br>paymentMethod.<br>type| Payment method type: `CARD` — bank card, `TOKEN` — payment token, `SBP` — Fast Payments System, `QIWI_WALLET` — QIWI Wallet balance| String|Always
payment.<br>paymentMethod.<br>paymentToken| Card payment token| String | When payment token is used for the payment
payment.<br>paymentMethod.<br>maskedPan| Masked card PAN| String|When card or payment token is used for the payment
payment.<br>paymentMethod.<br>rrn| Payment RRN (ISO 8583)| Number| When card or payment token is used for the payment
payment.<br>paymentMethod.<br>authCode| Payment Auth code| Number| When card or payment token is used for the payment
payment.<br>paymentMethod.<br>phone| Phone number linked to the customer's card, or QIWI Wallet number|String|When paying through Fast Payments System, or from QIWI Wallet balance
---|---|---|-------
payment.<br>paymentCardInfo | Card information | Object|Always
----|------|-------|--------
payment.<br>paymentCardInfo.<br>issuingCountry | Issuer country code | String(3)|Always
payment.<br>paymentCardInfo.<br>issuingBank | Issuer name | String|Always
payment.<br>paymentCardInfo.<br>paymentSystem | Card's payment system | String|Always
payment.<br>paymentCardInfo.<br>fundingSource | Card's type (debit/credit/..) | String|Always
payment.<br>paymentCardInfo.<br>paymentSystemProduct | Card's category | String|Always
----|------|-------|--------
payment.<br>customer | Customer data | Object|Always
----|------|-------|--------
payment.<br>customer.<br>phone |Customer phone number |String|Always
payment.<br>customer.<br>email| Customer e-mail|String|Always
payment.<br>customer.<br>account| Customer ID in RSP system |String|Always
payment.<br>customer.<br>ip| Customer IP address |String|Always
payment.<br>customer.<br>country| Customer country from address string |String|Always
payment.<br>customer.<br>bankAccountNumber|Payer's bank account number|String|When paying through Fast Payments System
payment.<br>customer.<br>bic|Issuer BIC|String|When paying through Fast Payments System
payment.<br>customer.<br>lastName|Customer's last name|String|When paying through Fast Payments System
payment.<br>customer.<br>firstName|Customer's first name|String|When paying through Fast Payments System
payment.<br>customer.<br>middleName|Customer's middle name|String|When paying through Fast Payments System
payment.<br>customer.<br>simpleAddress|Customer's address|String|When paying through Fast Payments System
payment.<br>customer.<br>inn|Customer's TIN|String|When paying through Fast Payments System
----|------|-------|--------
payment.<br>customFields | Fields with additional information for the operation | Object|Always
----|------|-------|--------
payment.<br>customFields.<br>cf1 | Extra field with some information to operation data | String(256)|Always
payment.<br>customFields.<br>cf2 | Extra field with some information to operation data | String(256)|Always
payment.<br>customFields.<br>cf3 | Extra field with some information to operation data | String(256)|Always
payment.<br>customFields.<br>cf4 | Extra field with some information to operation data | String(256)|Always
payment.<br>customFields.<br>cf5 | Extra field with some information to operation data | String(256)|Always
----|------|-------|--------
payment.<br>flags| Additional API commands| Array of Strings. Possible elements: `SALE`, `REVERSAL`|Always
payment.<br>tokenData | [Payment token data](#token_issue) | Object|When payment token issue was requested
----|------|-------|--------
payment.<br>tokenData.<br>paymentToken | Card payment token | String|When payment token issue was requested
payment.<br>tokenData.<br>expiredDate | Payment token expiration date. ISO-8601 Date format:<br/> `YYYY-MM-DDThh:mm:ss±hh:mm` | String|When payment token issue was requested
----|------|-------|--------
payment.<br>chequeData | Fiscal receipt description |[ChequeData](#receipt-data) | When [data for a fiscal receipt](#cheque) were sent in the operation payload
payment.<br>paymentSplits | Split payments description | Array(Objects) | For [split payments](#payments_split)
-----|------|------|-----
payment.<br>paymentSplits.<br>type | Data type. Always `MERCHANT_DETAILS` | String|For [split payments](#payments_split)
payment.<br>paymentSplits.<br>siteUid | [Merchant ID](#split-boarding) | String|For [split payments](#payments_split)
payment.<br>paymentSplits.<br>splitAmount | Merchant reimbursement | Object|For [split payments](#payments_split)
----|----|-----|-----
payment.<br>paymentSplits.<br>splitAmount.<br>value | Amount of reimbursement | Number|For [split payments](#payments_split)
payment.<br>paymentSplits.<br>splitAmount.<br>currency | Text code of reimbursement currency, by ISO | String(3)|For [split payments](#payments_split)
----|----|-----|-----
payment.<br>paymentSplits.<br>splitCommissions | Commission data | Object|For [split payments](#payments_split)
-----|----|-----|-----
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms | Commission from merchant | Object|For [split payments](#payments_split)
----|-----|----|-----
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms.<br>value | Amount of commission | Number|For [split payments](#payments_split)
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms.<br>currency |Text code of commission currency, by ISO | String(3)|For [split payments](#payments_split)
-----|-----|-----|-----
payment.<br>paymentSplits.<br>orderId | Order number| String|For [split payments](#payments_split)
payment.<br>paymentSplits.<br>comment | Comment for the order| String|For [split payments](#payments_split)
-----|-----|-----|-----
type | Notification type (`PAYMENT`)|String(200)|Always
version | Notification version | String | Always

## CAPTURE Notification Format {#capture-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Notification field | Description | Type
--------|--------|---
capture | Capture information | Object
----|------|-------
capture.<br>type| [Operation type](#operation-types)|String(200)
capture.<br>captureId|Capture operation unique identifier in RSP's system|String(200)
capture.<br>createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`
capture.<br>amount|Object| Operation amount data
----|------|-------
capture.<br>amount.<br>value | Operation amount rounded down to two decimals | Number(6.2)
capture.<br>amount.<br>currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)
----|------|-------
capture.<br>billId| Corresponding invoice ID for the operation| String(200)
capture.<br>status | Operation status data | Object
----|------|-------
capture.<br>status.<br>value |[Operation status value](#notification-statuses) | String
capture.<br>status.<br>changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
capture.<br>status.<br>reasonCode| [Rejection reason code](#reason-codes)| String(200)
capture.<br>status.<br>reasonMessage| Rejection reason description| String(200)
capture.<br>status.<br>errorCode| Error code| Number
----|------|-------
capture.<br>paymentMethod| Payment method data| Object
----|------|-------
capture.<br>paymentMethod.<br>type| Payment method type| String
capture.<br>paymentMethod.<br>maskedPan| Masked card PAN| String
capture.<br>paymentMethod.<br>rrn| Payment RRN (ISO 8583)| Number
capture.<br>paymentMethod.<br>authCode| Payment Auth code| Number
----|------|-------
capture.<br>customer | Customer data | Object
----|------|-------
capture.<br>customer.<br>phone |Customer phone number |String
capture.<br>customer.<br>email| Customer e-mail |String
capture.<br>customer.<br>account| Customer ID in RSP system|String
capture.<br>customer.<br>ip| Customer's IP address |String
capture.<br>customer.<br>country| Customer country from address string |String
----|------|-------
capture.<br>customFields | Fields with additional information for the operation | Object
----|------|-------
capture.<br>customFields.<br>cf1 | Extra field with some information to operation data | String(256)
capture.<br>customFields.<br>cf2 | Extra field with some information to operation data | String(256)
capture.<br>customFields.<br>cf3 | Extra field with some information to operation data | String(256)
capture.<br>customFields.<br>cf4 | Extra field with some information to operation data | String(256)
capture.<br>customFields.<br>cf5 | Extra field with some information to operation data | String(256)
----|------|-------
capture.<br>flags| Additional API commands| Array of Strings. Possible elements: `SALE`, `REVERSAL`
type | Notification type (`CAPTURE`)|String(200)
version | Notification version | String

## REFUND Notification Format {#refund-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Notification field | Description | Type | When present
--------|--------|---|----
refund | Refund information | Object | Always
----|------|-------|--------
refund.<br>type| [Operation type](#operation-types)|String(200)|Always
refund.<br>refundId|Refund operation unique identifier in RSP's system|String(200) | Always
refund.<br>createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`|Always
refund.<br>amount|Object| Operation amount data|Always
----|------|-------|--------
refund.<br>amount.<br>value | Operation amount rounded down to two decimals | Number(6.2)|Always
refund.<br>amount.<br>currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)|Always
----|------|-------|--------
refund.<br>billId| Corresponding invoice ID for the operation| String(200)|Always
refund.<br>status | Operation status data | Object|Always
----|------|-------|--------
refund.<br>status.<br>value |[Operation status value](#notification-statuses) | String|Always
refund.<br>status.<br>changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`|Always
refund.<br>status.<br>reasonCode| [Rejection reason code](#reason-codes)| String(200)|In case of operation rejection
refund.<br>status.<br>reasonMessage| Rejection reason description| String(200)|In case of operation rejection
refund.<br>status.<br>errorCode| Error code| Number|In case of error
----|------|-------|--------
refund.<br>paymentMethod| Payment method data| Object|Always
----|------|-------|--------
refund.<br>paymentMethod.<br>type| Payment method type| String|Always
refund.<br>paymentMethod.<br>maskedPan| Masked card PAN| String|IAlways
refund.<br>paymentMethod.<br>rrn| Payment RRN (ISO 8583)| Number|Always
refund.<br>paymentMethod.<br>authCode| Payment Auth code| Number|Always
----|------|-------|--------
refund.<br>customer | Customer data | Object|Always
----|------|-------|--------
refund.<br>customer.<br>phone |Customer phone number  |String|Always
refund.<br>customer.<br>email| Customer e-mail |String|Always
refund.<br>customer.<br>account| Customer ID in RSP system |String|Always
refund.<br>customer.<br>ip| Customer IP address |String|Always
refund.<br>customer.<br>country| Customer country from address string |String|Always
----|------|-------|--------
refund.<br>customFields | Fields with additional information for the operation | Object|Always
----|------|-------|--------
refund.<br>customFields.<br>cf1 | Extra field with some information to operation data | String(256)|Always
refund.<br>customFields.<br>cf2 | Extra field with some information to operation data | String(256)|Always
refund.<br>customFields.<br>cf3 | Extra field with some information to operation data | String(256)|Always
refund.<br>customFields.<br>cf4 | Extra field with some information to operation data | String(256)|Always
refund.<br>customFields.<br>cf5 | Extra field with some information to operation data | String(256)|Always
----|------|-------|--------
refund.<br>flags| Additional API commands| Array of Strings. Possible elements: `SALE`, `REVERSAL`|Always
refund.<br>chequeData | Fiscal receipt description |[ChequeData](#receipt-data) | When [data for a fiscal receipt](#cheque) were sent in the operation payload
refund.<br>refundSplits | Refund of split payments description | Array(Objects) | For [refund split payments](#split-refund)
-----|------|------|-----
refund.<br>refundSplits.<br>type | Data type. Always `MERCHANT_DETAILS` | String|For [refund split payments](#split-refund)
refund.<br>refundSplits.<br>siteUid | [Merchant ID](#split-boarding) | String|For [refund split payments](#split-refund)
refund.<br>refundSplits.<br>splitAmount | Data on reimbursement refund for the merchant| Object|For [refund split payments](#split-refund)
----|----|-----|-----
refund.<br>refundSplits.<br>splitAmount.<br>value | Amount of reimbursement refund | Number|For [refund split payments](#split-refund)
refund.<br>refundSplits.<br>splitAmount.<br>currency | Text code of reimbursement refund currency, by ISO | String(3)|For [refund split payments](#split-refund)
----|----|-----|-----
refund.<br>refundSplits.<br>splitCommissions | Commission data | Object|For [refund split payments](#split-refund)
-----|----|-----|-----
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms | Commission from the merchant | Object|For [refund split payments](#split-refund)
----|-----|----|-----
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms.<br>value | Commission amount | Number|For [refund split payments](#split-refund)
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms.<br>currency |Text code of commission currency, by ISO | String(3)|For [refund split payments](#split-refund)
-----|-----|-----|-----
refund.<br>refundSplits.<br>orderId | Order number | String|For [refund split payments](#split-refund)
refund.<br>refundSplits.<br>comment | Comment to the order | String|For [refund split payments](#split-refund)
-----|-----|-----|-----
type | Notification type (`REFUND`)|String(200)|Always
version | Notification version | String | Always

## CHECK_CARD Notification Format {#checkcard-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> CHECK_CARD notification example

~~~json
{
    "checkPaymentMethod": {
        "status": "SUCCESS",
        "isValidCard": true,
        "threeDsStatus": "PASSED",
        "cardInfo": {
            "issuingCountry": "RUS",
            "issuingBank": "Альфа-банк",
            "paymentSystem": "MASTERCARD",
            "fundingSource": "PREPAID",
            "paymentSystemProduct": "TNW|TNW|Mastercard® New World—Immediate Debit|TNW|Mastercard New World-Immediate Debit"
        },
        "createdToken": {
            "token": "7653465767c78-a979-5bae621db96f",
            "name": "54**********47",
            "expiredDate": "2022-12-30T00:00:00+03:00",
            "account": "acc1"
        },
        "requestUid": "uuid1-uuid2-uuid3-uuid4",
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "54************47",
            "cardHolder": null,
            "cardExpireDate": "12/2022"
        },
        "checkOperationDate": "2021-08-16T14:15:07+03:00"
    },
    "type": "CHECK_CARD",
    "version": "1"
}
~~~

Notification field | Description | Type
--------|--------|---
checkPaymentMethod | Card verification result | Object
----|------|-------
checkPaymentMethod.<br>checkOperationDate| System date of the operation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
checkPaymentMethod.<br>requestUid|Card verification [operation unique identifier](#how-to-check-card)|String(200)
checkPaymentMethod.<br>status | [Card verification status](#card-check-statuses) | String
checkPaymentMethod.<br>isValidCard |`true` means card can be charged | Bool
checkPaymentMethod.<br>threeDsStatus|Information on customer authentication status. Possible values: `PASSED` (3-D Secure passed), `NOT_PASSED` (3-D Secure not passed), `WITHOUT` (3-D Secure not required) | String
----|------|-------
checkPaymentMethod.<br>paymentMethod| Payment method data| Object
----|------|-------
checkPaymentMethod.<br>paymentMethod.<br>type| Payment method type| String
checkPaymentMethod.<br>paymentMethod.<br>maskedPan| Masked card PAN| String
checkPaymentMethod.<br>paymentMethod.<br>cardExpireDate| Card expiration date (MM/YY)| String
checkPaymentMethod.<br>paymentMethod.<br>cardHolder| Cardholder name| String
----|------|-------
checkPaymentMethod.<br>cardInfo | Card information | Object
----|------|-------
checkPaymentMethod.<br>cardInfo.<br>issuingCountry | Issuer country code | String(3)
checkPaymentMethod.<br>cardInfo.<br>issuingBank | Issuer name | String
checkPaymentMethod.<br>cardInfo.<br>paymentSystem | Card's payment system | String
checkPaymentMethod.<br>cardInfo.<br>fundingSource | Card's type (debit/credit/..) | String
checkPaymentMethod.<br>cardInfo.<br>paymentSystemProduct | Card's category | String
----|------|------
checkPaymentMethod.<br>createdToken | [Payment token data](#token_issue) | Object
----|------|-------
checkPaymentMethod.<br>createdToken.<br>token | Card payment token | String
checkPaymentMethod.<br>createdToken.<br>name | Masked card PAN for which payment token issued| String
checkPaymentMethod.<br>createdToken.<br>expiredDate | Payment token expiration date. ISO-8601 Date format:<br/> `YYYY-MM-DDThh:mm:ss±hh:mm` | String
checkPaymentMethod.<br>createdToken.<br>account | Customer account for which payment token issued| String
----|------|-------
type | Notification type|String(200)
version | Notification version | String

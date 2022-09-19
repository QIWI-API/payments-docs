# Server Notifications {#callback}

> Notification example

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-Type: application/json
Signature: j4wnfnzd***v5mv2w=
Host: example.com
~~~

~~~json
{
   "payment":{
      "paymentId":"4504751",
      "tokenData":{
         "paymentToken":"4cc975be-483f-8d29-2b7de3e60c2f",
         "expiredDate":"2021-12-31t00:00:00+03:00"
      },
      "type":"payment",
      "createdDateTime":"2019-10-08t11:31:37+03:00",
      "status":{
         "value":"success",
         "changedDateTime":"2019-10-08t11:31:37+03:00"
      },
      "amount":{
         "value":2211.24,
         "currency":"RUB"
      },
      "paymentMethod":{
         "type":"CARD",
         "maskedPan":"220024******5036",
         "rrn":"124",
         "authCode":"181211"
      },
      "paymentCardInfo": {
         "issuingCountry": "810",
         "issuingBank": "QiwiBank",
         "paymentSystem": "VISA",
         "fundingSource": "CREDIT",
         "paymentSystemProduct": "P|Visa Gold"
      },
      "customer":{
         "ip":"79.142.20.248",
         "account":"token32",
         "phone":"0"
      },
      "billId":"testing122",
      "customFields":{},
      "flags":[
         "SALE"
      ]
   },
   "type":"PAYMENT",
   "version":"1"
}
~~~

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

   `hash = HMAС(SHA256, secret, parameters)`

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

> Notification

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: example.com
~~~

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
      "lastName": "ИВАНОВ",
      "firstName": "ИВАН",
      "middleName": "ИВАНОВИЧ",
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
--------|--------|---
payment | Payment information | Object | Always
----|------|-------|--------
type| [Operation type](#operation-types)|String(200)|Always
paymentId|Payment operation unique identifier in RSP's system|String(200)|Always
createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`|Always
billId| Corresponding invoice ID for the operation| String(200)|Always
qrCodeUid| QR-code issue operation identifier in RSP's system|String|In case of operation with Faster Payment System
amount|Object| Operation amount data|Always
----|------|-------|--------
value | Operation amount rounded down to two decimals | Number(6.2)|Always
currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)|Always
----|------|-------|--------
status | Operation status data | Object|Always
----|------|-------|--------
value |[Operation status value](#operation-statuses) | String|Always
changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`|Always
reasonCode| [Rejection reason code](#reason-codes)| String(200)|In case of operation rejection
reasonMessage| Rejection reason description| String(200)|In case of operation rejection
errorCode| Error code| Number|In case of error
----|------|-------|--------
paymentMethod| Payment method data| Object|Always
----|------|-------|--------
type| Payment method type: `CARD` — bank card, `TOKEN` — payment token, `SBP` — Fast Payments System, `QIWI_WALLET` — QIWI Wallet balance| String|Always
paymentToken| Card payment token| String | When payment token is used for the payment
maskedPan| Masked card PAN| String|When card or payment token is used for the payment
rrn| Payment RRN (ISO 8583)| Number| When card or payment token is used for the payment
authCode| Payment Auth code| Number| When card or payment token is used for the payment
phone| Phone number linked to the customer's card, or QIWI Wallet number|String|When paying through Fast Payments System, or from QIWI Wallet balance
---|---|---|-------
paymentCardInfo | Card information | Object|Always
----|------|-------|--------
issuingCountry | Issuer country code | String(3)|Always
issuingBank | Issuer name | String|Always
paymentSystem | Card's payment system | String|Always
fundingSource | Card's type (debit/credit/..) | String|Always
paymentSystemProduct | Card's category | String|Always
----|------|-------|--------
customer | Customer data | Object|Always
----|------|-------|--------
phone |Customer phone number |String|Always
email| Customer e-mail|String|Always
account| Customer ID in RSP system |String|Always
ip| Customer IP address |String|Always
country| Customer country from address string |String|Always
bankAccountNumber|Payer's bank account number|String|When paying through Fast Payments System
bic|Issuer BIC|String|When paying through Fast Payments System
lastName|Customer's last name|String|When paying through Fast Payments System
firstName|Customer's first name|String|When paying through Fast Payments System
middleName|Customer's middle name|String|When paying through Fast Payments System
simpleAddress|Customer's address|String|When paying through Fast Payments System
inn|Customer's TIN|String|When paying through Fast Payments System
----|------|-------|--------
customFields | Fields with additional information for the operation | Object|Always
----|------|-------|--------
cf1 | Extra field with some information to operation data | String(256)|Always
cf2 | Extra field with some information to operation data | String(256)|Always
cf3 | Extra field with some information to operation data | String(256)|Always
cf4 | Extra field with some information to operation data | String(256)|Always
cf5 | Extra field with some information to operation data | String(256)|Always
`FROM_MERCHANT_CONTRACT_ID`| Contract ID between the merchant and the customer |String(256)|Always
`FROM_MERCHANT_BOOKING_NUMBER` | Booking number for the customer  |String(256)|Always
`FROM_MERCHANT_PHONE` | Phone number of the customer | String(256)|Always
`FROM_MERCHANT_FULL_NAME` | Full name of the customer |String(256)|Always
----|------|-------|--------
flags| Additional API commands| Array of Strings. Possible elements — `SALE` / `REVERSAL`|Always
tokenData | [Payment token data](#token_issue) | Object|When payment token issue was requested
----|------|-------|--------
paymentToken | Card payment token | String|When payment token issue was requested
expiredDate | Payment token expiration date. ISO-8601 Date format:<br/> `YYYY-MM-DDThh:mm:ss±hh:mm` | String|When payment token issue was requested
----|------|-------|--------
paymentSplits | Split payments description | Array(Objects) | For [split payments](#payments_split)
-----|------|------|-----
type | Data type. Always `MERCHANT_DETAILS` | String|For [split payments](#payments_split)
siteUid | [Merchant ID](#split-boarding) | String|For [split payments](#payments_split)
splitAmount | Merchant reimbursement | Object|For [split payments](#payments_split)
----|----|-----|-----
value | Amount of reimbursement | Number|For [split payments](#payments_split)
currency | Text code of reimbursement currency, by ISO | String(3)|For [split payments](#payments_split)
----|----|-----|-----
splitCommissions | Commission data | Object|For [split payments](#payments_split)
-----|----|-----|-----
merchantCms | Commission from merchant | Object|For [split payments](#payments_split)
----|-----|----|-----
value | Amount of commission | Number|For [split payments](#payments_split)
currency |Text code of commission currency, by ISO | String(3)|For [split payments](#payments_split)
-----|-----|-----|-----
orderId | Order number| String|For [split payments](#payments_split)
comment | Comment for the order| String|For [split payments](#payments_split)
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
type| [Operation type](#operation-types)|String(200)
captureId|Capture operation unique identifier in RSP's system|String(200)
createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`
amount|Object| Operation amount data
----|------|-------
value | Operation amount rounded down to two decimals | Number(6.2)
currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)
----|------|-------
billId| Corresponding invoice ID for the operation| String(200)
status | Operation status data | Object
----|------|-------
value |[Operation status value](#operation-statuses) | String
changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
reasonCode| [Rejection reason code](#reason-codes)| String(200)
reasonMessage| Rejection reason description| String(200)
errorCode| Error code| Number
----|------|-------
paymentMethod| Payment method data| Object
----|------|-------
type| Payment method type| String
maskedPan| Masked card PAN| String
rrn| Payment RRN (ISO 8583)| Number
authCode| Payment Auth code| Number
----|------|-------
customer | Customer data | Object
----|------|-------
phone |Customer phone number |String
email| Customer e-mail |String
account| Customer ID in RSP system|String
ip| Customer's IP address |String
country| Customer country from address string |String
----|------|-------
customFields | Fields with additional information for the operation | Object
----|------|-------
cf1 | Extra field with some information to operation data | String(256)
cf2 | Extra field with some information to operation data | String(256)
cf3 | Extra field with some information to operation data | String(256)
cf4 | Extra field with some information to operation data | String(256)
cf5 | Extra field with some information to operation data | String(256)
`FROM_MERCHANT_CONTRACT_ID`| Contract ID between the merchant and the customer |String(256)
`FROM_MERCHANT_BOOKING_NUMBER` | Booking number for the customer  |String(256)
`FROM_MERCHANT_PHONE` | Phone number of the customer | String(256)
`FROM_MERCHANT_FULL_NAME` | Full name of the customer |String(256)
----|------|-------
flags| Additional API commands| Array of Strings. Possible elements — `SALE` / `REVERSAL`
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
type| [Operation type](#operation-types)|String(200)|Always
refundId|Refund operation unique identifier in RSP's system|String(200) | Always
createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`|Always
amount|Object| Operation amount data|Always
----|------|-------|--------
value | Operation amount rounded down to two decimals | Number(6.2)|Always
currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)|Always
----|------|-------|--------
billId| Corresponding invoice ID for the operation| String(200)|Always
status | Operation status data | Object|Always
----|------|-------|--------
value |[Operation status value](#operation-statuses) | String|Always
changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`|Always
reasonCode| [Rejection reason code](#reason-codes)| String(200)|In case of operation rejection
reasonMessage| Rejection reason description| String(200)|In case of operation rejection
errorCode| Error code| Number|In case of error
----|------|-------|--------
paymentMethod| Payment method data| Object|Always
----|------|-------|--------
type| Payment method type| String|Always
maskedPan| Masked card PAN| String|IAlways
rrn| Payment RRN (ISO 8583)| Number|Always
authCode| Payment Auth code| Number|Always
----|------|-------|--------
customer | Customer data | Object|Always
----|------|-------|--------
phone |Customer phone number  |String|Always
email| Customer e-mail |String|Always
account| Customer ID in RSP system |String|Always
ip| Customer IP address |String|Always
country| Customer country from address string |String|Always
----|------|-------|--------
customFields | Fields with additional information for the operation | Object|Always
----|------|-------|--------
cf1 | Extra field with some information to operation data | String(256)|Always
cf2 | Extra field with some information to operation data | String(256)|Always
cf3 | Extra field with some information to operation data | String(256)|Always
cf4 | Extra field with some information to operation data | String(256)|Always
cf5 | Extra field with some information to operation data | String(256)|Always
`FROM_MERCHANT_CONTRACT_ID`| Contract ID between the merchant and the customer |String(256)|Always
`FROM_MERCHANT_BOOKING_NUMBER` | Booking number for the customer  |String(256)|Always
`FROM_MERCHANT_PHONE` | Phone number of the customer | String(256)|Always
`FROM_MERCHANT_FULL_NAME` | Full name of the customer |String(256)|Always
----|------|-------|--------
flags| Additional API commands| Array of Strings. Possible elements — `SALE` / `REVERSAL`|Always
refundSplits | Refund of split payments description | Array(Objects) | For [refund split payments](#split-refund)
-----|------|------|-----
type | Data type. Always `MERCHANT_DETAILS` | String|For [refund split payments](#split-refund)
siteUid | [Merchant ID](#split-boarding) | String|For [refund split payments](#split-refund)
splitAmount | Data on reimbursement refund for the merchant| Object|For [refund split payments](#split-refund)
----|----|-----|-----
value | Amount of reimbursement refund | Number|For [refund split payments](#split-refund)
currency | Text code of reimbursement refund currency, by ISO | String(3)|For [refund split payments](#split-refund)
----|----|-----|-----
splitCommissions | Commission data | Object|For [refund split payments](#split-refund)
-----|----|-----|-----
merchantCms | Commission from the merchant | Object|For [refund split payments](#split-refund)
----|-----|----|-----
value | Commission amount | Number|For [refund split payments](#split-refund)
currency |Text code of commission currency, by ISO | String(3)|For [refund split payments](#split-refund)
-----|-----|-----|-----
orderId | Order number | String|For [refund split payments](#split-refund)
comment | Comment to the order | String|For [refund split payments](#split-refund)
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

Notification field | Description | Type
--------|--------|---
checkPaymentMethod | Card verification result | Object
----|------|-------
checkOperationDate| System date of the operation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
requestUid|Card verification [operation unique identifier](#how-to-check-card)|String(200)|PAYMENT
status | Card verification status | String
isValidCard |Logical flag means card is valid for purchases | Bool
threeDsStatus|Information on customer authentication status. Possible values — `PASSED` (3-D Secure passed), `NOT_PASSED` (3-D Secure not passed), `WITHOUT` (3-D Secure not required)
----|------|-------
paymentMethod| Payment method data| Object
----|------|-------
type| Payment method type| String
maskedPan| Masked card PAN| String
cardExpireDate| Card expiration date (MM/YY)| String
cardHolder| Cardholder name| String
----|------|-------
cardInfo | Card information | Object
----|------|-------
issuingCountry | Issuer country code | String(3)
issuingBank | Issuer name | String
paymentSystem | Card's payment system | String
fundingSource | Card's type (debit/credit/..) | String
paymentSystemProduct | Card's category | String
----|------|------
createdToken | [Payment token data](#token_issue) | Object
----|------|-------
token | Card payment token | String
name | Masked card PAN for which payment token issued| String
expiredDate | Payment token expiration date. ISO-8601 Date format:<br/> `YYYY-MM-DDThh:mm:ss±hh:mm` | String
account | Customer account for which payment token issued| String
----|------|-------
type | Notification type|String(200)
version | Notification version | String

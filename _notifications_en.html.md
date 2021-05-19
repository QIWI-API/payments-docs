# Server Notifications {#callback}

QIWI server notification is an incoming HTTP POST request. The JSON-formatted notification message in the request body contains payment/invoice data encoded in UTF-8 codepage.

The Protocol supports the following notification types for API events: `PAYMENT`, `CAPTURE`, and `REFUND`. These notifications are sending when payment operation, payment confirmation, and refund for payment are made, accordingly.

<aside class="notice">
There is no specific sequence of sending different types' notifications for the same operation. The sequence may vary for different operations.
</aside>

Specify the notification server address in your Personal Profile on [kassa.qiwi.com](https://kassa.qiwi.com/) web-site in **Settings** section. You may also specify the address for a separate operation in optional `callbackUrl` parameter of [API requests](#api_requests).

The URL for notifications should start with `https`, as notifications are sent by HTTPS to port 443.
The site certificate must be issued by a trusted certification center (e.g. Comodo, Verisign, Thawte, etc.)

<aside class="notice">
If you don't receive a transaction notification within 10 minutes of the transaction, you must request the status of the transaction by <a href="#invoice_get">requesting invoice status</a> or <a href="#payment_status">requesting payment status</a> (depending on which way you use the API).
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

## Notification Authorization {#notifications_auth}

The notification contains a digital signature of the request data which RSP should verify on its side to secure from notification fraud.

<aside class="warning">
The responsibility for any financial losses due to omitted verification of the signature parameter lies solely on RSP.
</aside>

The UTF-8 encoded digital signature is placed into HTTP header `Signature` of the notification message.

To validate the signature, HMAC integrity check with SHA256-hash is used.

Implement the following algorithm to verify notification signature:

1. Join values of some parameters from the notification with the pipe "\|" character as a separator. For example:

   `parameters = {payment.paymentId}|{payment.createdDateTime}|{payment.amount.value}`

   where `{*}` – notification parameter value. All values are treated as strings. Make sure strings are UTF-8 encoded.

   Signature should be verified for those notification fields:

      * `PAYMENT` type:`payment.paymentId|payment.createdDateTime|payment.amount.value`
      * `REFUND` type:`refund.refundId|refund.createdDateTime|refund.amount.value`
      * `CAPTURE` type:`capture.captureId|capture.createdDateTime|capture.amount.value`

2. Calculate hash HMAC value with SHA256 algorithm (signature string and secret key should be UTF8-encoded):

   `hash = HMAС(SHA256, token, parameters)`

   where:

      * `token` – HMAC hash key. Coincides with the [API access key](#auth) used in the original API request for the operation.
      * `parameters` – string from step 1.

3. Compare the notification signature from `Signature` HTTP-header with the result of step 2. If there is no difference, the validation is successful.


## Notification Format {#payment_callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Notification example

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: server.ru

{
   "payment":{
      "paymentId":"4504751",
      "tokenData":{
         "paymentToken":"4cc975be-483f-8d29-2b7de3e60c2f",
         "expiredDate":"2021-12-31T00:00:00+03:00"
      },
      "type":"PAYMENT",
      "createdDateTime":"2019-10-08T11:31:37+03:00",
      "status":{
         "value":"SUCCESS",
         "changedDateTime":"2019-10-08T11:31:37+03:00"
      },
      "amount":{
         "value":2211.24,
         "currency":"RUB"
      },
      "paymentMethod":{
         "type":"CARD",
         "maskedPan":"220024/*/*/*/*/*/*5036",
         "rrn":null,
         "authCode":null,
         "type":"CARD"
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

Notification field | Description | Type | In which notifications is used
--------|--------|---
payment | Payment information | Object | PAYMENT
capture | Capture information | Object | CAPTURE
refund | Refund information | Object | REFUND
----|------|-------|--------
type| Operation type|String(200)|In all notification types
paymentId|Payment operation unique identifier in RSP's system|String(200)|PAYMENT
captureId|Capture operation unique identifier in RSP's system|String(200)| CAPTURE
refundId|Refund operation unique identifier in RSP's system|String(200) | REFUND
createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`|In all notification types
amount|Object| Operation amount data|In all notification types 
----|------|-------|--------
value | Operation amount rounded down to two decimals | Number(6.2)|In all notification types
currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)|In all notification types
----|------|-------|--------
----|------|-------|--------
billId| Corresponding invoice ID for the operation| String(200)|In all notification types
status | Operation status data | Object|In all notification types
----|------|-------|--------
----|------|-------|--------
value |Invoice status value | String|In all notification types
changedDatetime|Date of operation status update| URL-encoded string<br>`YYYY-MM-DDThh:mm:ssZ`|In all notification types
reasonCode| Rejection reason code| String(200)|In all notification types
reasonMessage| Rejection reason description| String(200)|In all notification types
errorCode| Error code| Number|In all notification types
----|------|-------|--------
----|------|-------|--------
paymentMethod| Payment method data| Object|In all notification types
----|------|-------|--------
----|------|-------|--------
type| Payment method type| String
paymentToken| Card payment token| String | PAYMENT, when payment token is used for the operation
maskedPan| Masked card PAN| String|In all notification types
rrn| Payment RRN (ISO 8583)| Number|In all notification types
authCode| Payment Auth code| Number|In all notification types
paymentCardInfo | Card information | Object|PAYMENT
----|------|-------|--------
issuingCountry | Issuer country code | String(3)|PAYMENT
issuingBank | Issuer name | String|PAYMENT
paymentSystem | Card's payment system | String|PAYMENT
fundingSource | Card's type (debit/credit/..) | String|PAYMENT
paymentSystemProduct | Card's category | String|PAYMENT
----|------|-------|--------
customer | Customer data | Object|In all notification types
----|------|-------|--------
phone |Phone number to which invoice issued (if specified in the original operation) |String|In all notification types
email| E-mail to which invoice issued (if specified in the original operation)|String|In all notification types
account| Customer ID in RSP system (if specified in the original operation)|String
ip| IP address |String|In all notification types
country| Country from address string |String|In all notification types
----|------|-------|--------
customFields | Fields with additional information for the operation | Object|In all notification types
----|------|-------|--------
cf1 | Extra field with some information to operation data | String(256)|In all notification types
cf2 | Extra field with some information to operation data | String(256)|In all notification types
cf3 | Extra field with some information to operation data | String(256)|In all notification types
cf4 | Extra field with some information to operation data | String(256)|In all notification types
cf5 | Extra field with some information to operation data | String(256)|In all notification types 
----|------|-------|--------
flags| Additional API commands| Array of Strings. Possible elements — `SALE` / `REVERSAL`|In all notification types
tokenData | [Payment token data](#token_issue) | Object|PAYMENT, if payment token issue was requested
----|------|-------|--------
paymentToken | Card payment token | String|PAYMENT, if payment token issue was requested
expiredDate | Payment token expiration date. ISO-8601 Date format:<br/> `YYYY-MM-DDThh:mm:ss±hh:mm` | String|PAYMENT, if payment token issue was requested
----|------|-------|--------
----|------|-------|--------
paymentSplits | Split payments description | Array(Objects) | PAYMENT, for [split payments](#payments_split) only
-----|------|------|-----
type | Data type. Always `MERCHANT_DETAILS` | String|PAYMENT, for [split payments](#payments_split) only
siteUid | [Merchant ID](#split-boarding) | String|PAYMENT, for [split payments](#payments_split) only
splitAmount | Merchant reimbursement | Object|PAYMENT, for [split payments](#v) only
----|----|-----|-----
value | Amount of reimbursement | Number|PAYMENT, for [split payments](#payments_split) only
currency | Text code of reimbursement currency, by ISO | String(3)|PAYMENT, for [split payments](#payments_split) only
----|----|-----|-----
splitCommissions | Commission data | Object|PAYMENT, for [split payments](#payments_split) only
-----|----|-----|-----
merchantCms | Commission from merchant | Object|PAYMENT, for [split payments](#payments_split) only
----|-----|----|-----
value | Amount of commission | Number|PAYMENT, for [split payments](#payments_split) only
currency |Text code of commission currency, by ISO | String(3)|PAYMENT, for [split payments](#payments_split) only
-----|-----|-----|-----
orderId | Order number| String|PAYMENT, for [split payments](#payments_split) only
comment | Comment for the order| String|PAYMENT, for [split payments](#payments_split) only
-----|-----|-----|-----
refundSplits | Refund of split payments description | Array(Objects) | REFUND, for [refund split payments](#split-refund) only
-----|------|------|-----
type | Data type. Always `MERCHANT_DETAILS` | String|REFUND, for [refund split payments](#split-refund)
siteUid | [Merchant ID](#split-boarding) | String|REFUND, for [refund split payments](#split-refund) only
splitAmount | Data on reimbursement refund for the merchant| Object|REFUND, for [refund split payments](#split-refund) only
----|----|-----|-----
value | Amount of reimbursement refund | Number|REFUND, for [refund split payments](#split-refund) only
currency | Text code of reimbursement refund currency, by ISO | String(3)|REFUND, for [refund split payments](#split-refund) only
----|----|-----|-----
splitCommissions | Commission data | Object|REFUND, for [refund split payments](#split-refund) only
-----|----|-----|-----
merchantCms | Commission from the merchant | Object|REFUND, for [refund split payments](#split-refund) only
----|-----|----|-----
value | Commission amount | Number|REFUND, for [refund split payments](#split-refund) only
currency |Text code of commission currency, by ISO | String(3)|REFUND, for [refund split payments](#split-refund) only
-----|-----|-----|-----
orderId | Order number | String|REFUND, for [refund split payments](#split-refund) only
comment | Comment to the order | String|REFUND, for [refund split payments](#split-refund) only
-----|-----|-----|-----
type | Notification type|String(200)|In all notification types
version | Notification version | String | In all notification types

## Frequency of notification sending {#notification-rate}

QIWI notification service sorts unsuccessful notifications on the following queues:

* One attempt on waiting 5 seconds
* One attempt on waiting 1 minutes
* Three attempts on waiting 5 minutes

Time of secondary sending notification may slightly shift upward.

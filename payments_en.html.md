---
title: Online payments protocol

metatitle: Online payments protocol

metadescription: Online payments protocol allows RSP to start accepting  secure payments from their customers' credit cards.

language_tabs:
  - json: Examples

toc_footers:
  - <a href='/'>To main page</a>
  - <a href='mailto:bss@qiwi.com'>Feedback</a>

search: true
---

*[3DS]: 3-D Secure - a messaging protocol to exchange data during e-commerce transaction for consumer authentication purposes.
*[API]: Application Programming Interface -  a set of ready-for-use methods that allow the creation of applications which access the features or data of an application or other service.
*[RSP]: Retail Service Provider
*[PCI DSS]: Payment Card Industry Data Security Standard -  The Payment Card Industry Data Security Standard – a proprietary information security standard for storing, processing and transmitting credit card data.
*[REST]: Representational State Transfer -  a software architectural pattern for Network Interaction between distributed application components.
*[JSON]: JavaScript Object Notation - a lightweight data-interchange format based on JavaScript.
*[Luhn]: Luhn Algorithm - a checksum formula used for verifying and validating identification numbers against accidental errors.
*[HTTPS]: HTTP protocol extension to encrypt and enforce security. In HTTPS protocol, data transfer over SSL and TLS cryptographic protocols. In contrast with HTTP on port 80, the TCP 443 port is used by default for HTTPS.


# General Information {#intro}

###### Last update: 2020-04-30 | [Edit on GitHub](https://github.com/QIWI-API/payments-docs/blob/master/payments_en.html.md)

**Online payments protocol** allows RSP to start accepting fast and secure payments from credit cards.

The protocol provides fully functional API for payment operations. API implements REST principles and uses HTTPS application protocol. Parameters are transferred by HTTP PUT method in the request body JSON data.

## Ways of Integration {#integration}

Online payments protocol provides several ways of integration:

* Checkout - Payment Form on QIWI side.
* API - Fully functional API for payment operations.

<aside class="warning">
API requests with full card numbers are allowed by PCI DSS-certified merchants only, as Card Data are processed on the merchant's side.
</aside>

### Available API Methods

Method|API|Checkout
-----|---------|--------------
One-step funds authorization|+|+
Two-steps funds authorization|+|+
Card tokenization |+|+
Token payment |+|-



## Steps to Start {#start}

To start using Protocol, you need to complete three simple steps.

**Step 1. Leave request to integrate on [b2b.qiwi.com](https://b2b.qiwi.com)**

After processing requests, our personnel will contact you to discuss possible ways of integration, collect the necessary documents and start integration.

<a name="auth"></a>

**Step 2. Get access to your Account**

Upon connecting to the Protocol, we provide your ID and access to your [Account](https://kassa.qiwi.com/service/core/merchants?) in our system. We send the Account credentials to your e-mail address specified on Step 1.

**Step 3. Issue Token for the integration**

To use API and requests you need authorization parameter `Token`. Send its value in `Authorization` HTTP header as `Bearer Token`.

## Test and Production Mode {#test_mode}

Send all requests to URL:

`https://api.qiwi.com/partner{API_REQUEST}`

The URL pathname's variable part `{API_REQUEST}` is request-specific. In this documentation, only `{API_REQUEST}` part is specified in the requests description.

Upon completion of [three steps](#start), your ID is in test mode by default. You can proceed operations, but without debiting credit card. See  [Test Data](#test_data) for details.

Test environment has restrictions on the total amount and number of operations. By default, maximum amount of a test transaction is 10 rubles. Maximum number of test transactions is 100 per day (MSK time zone). Only test transactions within allowed amount are taken into account.

When integration on your side is completed, we turn your ID to production mode (cards are debited).


# Checkout {#invoicing}


## Quick Start {#invoicing_quick_start}


### Issue invoice to customer

First, obtain a link to Payment Form and redirect the customer there.

<!-- Request body -->
>Request example

~~~http
PUT /partner/bill/v1/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 100.00
   },
   "expirationDateTime": "2018-04-13T14:30:00+03:00"
}
~~~

Send HTTP PUT-request to API URL with `{API_REQUEST}`:

`/bill/v1/bills/{billId}`

with parameters:

* **{billId}** - unique identifier in the RSP system
* **amount** - invoice amount (`amount.value`) and currency (`currency`) data
* **expirationDateTime** - invoice due date. Time should be specified with time zone. When date is overdue, invoice status becomes `EXPIRED` final status and invoice payment is not possible.


[Request details](#invoice_put)


In response you receive the following data:


>Response example

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "siteId": "23044",
    "billId": "893794793973",
    "amount": {
      "value": 100.00,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "changedDateTime": "2018-03-05T11:27:41+03:00"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
}
~~~



Parameter|Type|Description
--------|---|--------
billId|String| Unique invoice identifier in the merchant's system
siteId|String| Merchant's site identifier in QIWI Kassa
amount|Object| Invoice amount data
amount.value|Number| Invoice amount. The number is rounded down to two decimals
amount.currency	|String| Invoice currency identifier (Alpha-3 ISO 4217 code:  `RUB`, `USD`, `EUR`)
status|Object|Invoice current status data
status.value	|String|String representation of the [status](#invoice_status)
status.changedDateTime|String|Status refresh date. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`
customFields|Object|Additional data
customer|Object|Customer data. Possible elements:<br> `email`, `phone`, `account`
comment|String|Comment to the invoice
creationDateTime|String| System date of the invoice issue. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`
payUrl|String|New Payment Form URL
expirationDateTime|String|Expiration date of the Payment Form link (invoice payment's due date). Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`

### Redirect the customer to Payment Form URL received in response

To pay for the order, the customer has to open the link specified in `payUrl` parameter of the response. Redirect the customer and wait for payment callback.

You can add the parameter for the Payment Form URL:

<aside class="notice">
When opening Payment Form URL in Webview on Android, you should enable <code>settings.setDomStorageEnabled(true)</code>
</aside>


> Invoice URL example with extra parameter

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://developer.qiwi.com/ru/payments/#introduction
~~~

| Parameter | Description | Type |
|--------------|------------------|-------------|
| successUrl | The URL to which the client will be redirected in case of successful payment. Redirect happens after successful 3DS authentication. URL must be UTF8-encoded. | UTF-8 encoded string |


### Wait until the notification

When customer pays for the invoice, we send two server callbacks - first on the successfully processed payment, second - on the invoice payment.




**Processed Payment Notifications**

>Processed payment notification example

~~~json
{
 "payment":{
   "paymentId":"9999999",
   "type":"PAYMENT",
   "createdDateTime":"2019-06-03T08:19:16+03:00",
   "status":{
     "value":"SUCCESS",
     "changedDateTime":"2019-06-03T08:19:16+03:00"},
   "amount":{
     "value":111.11,
     "currency":"RUB"},
   "paymentMethod":{
     "type":"CARD",
     "cardHolder":"CARD HOLDER",
     "cardExpireDate":"1/2025",
     "maskedPan":"411111******0001"},
   "gatewayData":{
     "type":"ACQUIRING",
     "authCode":"181218",
     "rrn":"123"},
   "customer":{},
   "billId":"f1e1a1f11ae111a11a111111e1111111",
   "flags":["SALE"]
 },
 "type":"PAYMENT",
 "version":"1"
}
~~~

Parameter|Type|Description
--------|---|--------
paymentId|String| Unique payment identifier in the merchant's system
type|String | Operation type `PAYMENT`
createdDateTime|String| System date of the payment creation. Date format:<br>`YYYY-MM-DDThh:mm:ssZ`
amount|Object| Payment amount data
amount.value|Number| Payment amount. The number is rounded down to two decimals
amount.currency	|String| Payment currency identifier (Code Alpha-3 ISO 4217: `RUB`, `USD`, `EUR`)
status|Object|Payment status data
status.value	|String|Current [payment status](#payment_status)
status.changedDateTime|String|Status refresh date. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`
paymentMethod|Object|Payment method data
paymentMethod.type|String|Payment method type. Possible values: `TOKEN`, `CARD`
paymentMethod.maskedPan|String| Masked card PAN
paymentMethod.cardHolder|String|Card holder name
paymentMethod.cardExpireDate|String|Card expiration date
gatewayData|String| Payment gateway data
gatewayData.type|String| Gateway type. Possible value: `ACQUIRING`
gatewayData.authcode|String| Auth code
gatewayData.rrn|String| RRN value (ISO 8583)
customer|Object| Customer identifiers. Possible elements: `email`, `phone`, `account`
billId|String| Corresponding invoice ID
flags|Array of Strings|  Operation flags: `SALE` - one-step payment scenario

>Invoice payment notification

~~~json

{ 
  "bill": {  
     "siteId":"23044",
     "billId":"1519892138404fhr7i272a2",
     "amount":{  
        "value":100.00,
        "currency":"RUB"
     },
     "status": {  
        "value":"PAID",
        "datetime":"2018-03-01T11:16:12"
     },
     "customer": {},
     "customFields": {},
     "creationDateTime":"2018-03-01T11:15:39",
     "expirationDateTime":"2018-04-01T11:15:39+03:00"
  },
  "version":"1"
}
~~~

**Invoice Payment Notification**

Parameter|Type|Description
--------|---|--------
billId|String|Unique invoice identifier in the merchant's system
siteId|String|Merchant's site identifier in QIWI Kassa
amount|Object|Invoice amount data
amount.value|Number|Invoice amount. The number is rounded down to two decimals
amount.currency	|String|Invoice currency (Code Alpha-3 ISO 4217: `RUB`, `USD`, `EUR`)
status|Object|Invoice status data
status.value	|String|Current [invoice status](#invoice_status)
status.changedDateTime|String|Status refresh date. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`
customFields|Object|Additional fields
customer|Object|Customer data. Possible elements: `email`, `phone`, `account`
comment|String|Comment to the invoice
creationDateTime|String| System date of the invoice creation. Date format:<br>`YYYY-MM-DDThh:mm:ss`
payUrl|String|Payment Form link
expirationDateTime|String|Expiration date of the pay form link (invoice payment's due date). Date format:<br>`YYYY-MM-DDThh:mm:ss+\-hh:mm`


See description of server callbacks and callback types in [Server Notifications](#callback).

### Make refund to the customer

To make refund of the operation, send refund request.

Send HTTP PUT request to API URL with `{API_REQUEST}`:

`/bill/v1/bills/{billId}/refunds/{refundId}`


>Request refund example

~~~http
PUT /partner/bill/v1/bills/893794793973/refund/1 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "amount": {
         "currency": "RUB",
         "value": 42.24
    }
}
~~~


Parameter|Type|Description
--------|---|--------
billId|String| Unique invoice identifier
refundId|String|Unique refund identifier in the merchant's system
amount|Object|Invoice amount data
amount.value|Number|Invoice amount rounded down to two decimals
amount.currency	|String|Invoice currency (Code Alpha-3 ISO 4217)

[Request details](#invoice_refund)

## Two-Step Scenario {#two_step}


**How to hold funds**

>Hold request example

~~~http
PUT /partner/bill/v1/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 42.24
   },
   "comment": "Spasibo",
   "expirationDateTime": "2019-09-13T14:30:00+03:00",
   "customer": {},
   "customFields": {},
   "paymentFlags":["AUTH"]
}
~~~


Add to the invoice request body the following parameter: `"paymentFlags":["AUTH"]`.

Parameter|Type|Description
--------|--------|-------
billId|String|Unique identifier in the RSP system
amount|Object|Invoice amount data
amount.value| Number(6.2)| Amount of invoice rounded down to two decimals
amount.currency |String| Invoice currency (Code Alpha-3 ISO 4217: `RUB`, `USD`, `EUR`)
expirationDateTime |URL encoded string<br>`YYYY-MM-DDhh:mm+\-hh:mm` |  Invoice due date. Time should be specified with time zone. When date is overdue, invoice status becomes `EXPIRED` final status and invoice payment is not possible.
paymentFlags|Array of strings|Additional payment options.<br>Use value `AUTH` to perform two-step scenario of funds authorization

>Response example:

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
    "customFields": {
        "AUTH": "true"
    },
    "comment": "Spasibo",
    "creationDateTime": "2019-08-28T16:26:36.835+03:00",
    "expirationDateTime": "2019-09-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=78d60ca9-7c99-481f-8e51-0100c9012087"
}
~~~

<a name="hold"></a>

You receive an URL of the Payment Form in the `payUrl` parameter of the response.

**Obtain transaction id for capture operation**

>Callback example

~~~json
{
  "payment":
  {
    "paymentId":"804900",  <==paymentId necessary for capture operation
    "type":"PAYMENT",
    "createdDateTime":"2019-08-28T12:58:49+03:00",
    "status":{
        "value":"SUCCESS",
        "changedDateTime":"2019-08-28T12:58:53+03:00"
    },
    "amount":{
      "value":1.00,
      "currency":"RUB"
    },
    "paymentMethod":{
      "type":"CARD",
      "maskedPan":"444444\*\*\*\*\*\*4444",
      "rrn":null,
      "authCode":null,
      "type":"CARD"
    },
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

When payment is successfully processed, you will receive [server callback](#payment_callback). Take `paymentId` parameter from the callback for  `capture` operation.

You also may use the [invoice status](#invoice_get) method to get actual payment status and `paymentId` parameter.


**Confirm operation**

Using operation ID (`paymentId`), you can make `capture` request which confirms the operation.

Request structure:

HTTP PUT request with empty body to API URL with `{API_REQUEST}`:

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

where:

* **{siteId}** - unique RSP ID which you see in  [response](#hold) to the request for holding funds
* **{paymentId}** - operation ID, string, obtained from the payment callback
* **{captureId}** - confirmation ID, string, unique identifier for the RSP which generates it by itself

[Request details](#capture_invoice)


## Requests, Statuses and Errors {#invoicing_requests}

### Invoice Creation {#invoice_put}

<div id="bill_v1_bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "bill/v1/bills/{billId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~json
{
   "amount": {  
     "currency": "RUB",  
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {},
   "customFields": {}  
   }
}
~~~


<!-- 200 -->
~~~json
{
    "siteId": "23044",
    "billId": "893794793973",
    "amount": {
      "value": 100.00,
      "currency": "RUB"
    },
    "status": {
      "value": "WAITING",
      "changedDateTime": "2018-03-05T11:27:41+03:00"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
}
~~~


<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### Invoice status {#invoice_get}

<div id="payin_v1_sites__siteId__bills__billId__get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
            "get",
            ['200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>


<!-- 200 -->
~~~json
[
    {
        "paymentId": "12600406",
        "billId": "d35cf63943e54f50badc75f49a5aac7c",
        "createdDateTime": "2020-03-26T19:31:49+03:00",
        "amount": {
            "currency": "RUB",
            "value": 10.00
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": 10.00
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": 0.00
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "427638******1410",
            "type": "CARD"
        },
        "createdToken": {
            "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6",
            "name": "427638******1410"
        },
        "customer": {
            "account": "1",
            "phone": "0",
            "address": {}
        },
        "requirements": {
            "threeDS": {
                "pareq": "eJxVUWFvgjAQX7gM3fq+hNqO0oI5prexilN1UDEMwl6FcHZZ19m7v63DtRY=",
                "acsUrl": "https://ds1.mirconnect.ru:443/vbv/pareq"
            }
        },
        "status": {
            "value": "DECLINED",
            "changedDateTime": "2020-03-26T19:32:09+03:00",
            "reason": "ACQUIRING_NOT_PERMITTED"
        },
        "customFields": {
            "customer_account": "1",
            "customer_phone": "0"
        }
    },
    {
        "paymentId": "12600433",
        "billId": "d35cf63943e54f50badc75f49a5aac7c",
        "createdDateTime": "2020-03-26T19:32:22+03:00",
        "amount": {
            "currency": "RUB",
            "value": 10.00
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": 10.00
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": 0.00
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "427638******1410",
            "type": "CARD"
        },
        "createdToken": {
            "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6",
            "name": "427638******1410"
        },
        "customer": {
            "account": "1",
            "phone": "0",
            "address": {}
        },
        "requirements": {
            "threeDS": {
                "pareq": "eJxVUWFvgjAQ52lBUtjD3M9++qFgCxl0i/OtJv2WT/tv8LXqG0vw==",
                "acsUrl": "https://ds1.mirconnect.ru:443/vbv/pareq"
            }
        },
        "status": {
            "value": "DECLINED",
            "changedDateTime": "2020-03-26T19:32:54+03:00",
            "reason": "ACQUIRING_NOT_PERMITTED"
        },
        "customFields": {
            "customer_account": "1",
            "customer_phone": "0"
        }
    },
    {
        "paymentId": "12601084",
        "billId": "d35cf63943e54f50badc75f49a5aac7c",
        "createdDateTime": "2020-03-26T19:46:21+03:00",
        "amount": {
            "currency": "RUB",
            "value": 10.00
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": 10.00
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": 0.00
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "427638******1410",
            "rrn": "008692274763",
            "authCode": "242847",
            "type": "CARD"
        },
        "createdToken": {
            "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6",
            "name": "427638******1410"
        },
        "customer": {
            "account": "1",
            "phone": "0",
            "address": {}
        },
        "requirements": {
            "threeDS": {
                "pareq": "eJxVUdtuwjAM7b6t/1fcku04w==",
                "acsUrl": "https://ds1.mirconnect.ru:443/vbv/pareq"
            }
        },
        "status": {
            "value": "COMPLETED",
            "changedDateTime": "2020-03-26T19:46:43+03:00"
        },
        "customFields": {
            "customer_account": "1",
            "customer_phone": "0"
        },
        "flags": [
            "AFT"
        ]
    }
]
~~~


<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

### Refund {#invoice_refund}




<div id="bill_v1_bills__billId__refunds__refundId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-refund-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "bill/v1/bills/{billId}/refunds/{refundId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~json
{
  "amount": {
    "value": 2.34,
    "currency": "RUB"
  }
}
~~~




<!-- 200 -->
~~~json
{
    "amount": {
      "value": 50.50,
      "currency": "RUB"
    },
    "datetime": "2018-03-01T16:06:57+03",
    "refundId": "1",
    "status": "PARTIAL"
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~


<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

###  Invoice Payment Confirmation For Two-Step Scenario {#capture_invoice}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-capture-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~json
{
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example capture"
}
~~~

<!-- 200 -->
~~~json
{
  "captureId": "bxwd8096",
  "createdDatetime": "2018-11-20T16:29:58.96+03:00",
  "amount": {
    "currency": "RUB",
    "value": 6.77
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:29:58.963+03:00"
  }
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

# API {#api}


## Quick Start {#api_quick_start}


>Request example

~~~http
PUT /partner/payin/v1/sites/Obuc-00/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

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

To make your customer perform payment, create a payment. Make the following actions:

**1. Authorize payment**

Send HTTP PUT request to API URL with `{API_REQUEST}`:

`/payin/v1/sites/{siteId}/payments/{paymentId}`

with parameters:


* **{siteId}**: your RSP ID
* **{paymentId}**: unique payment ID for RSP
* **paymentMethod**: payment data in JSON body
* **amount**: object with payment amount (`value` field) and currency (`currency` field) in JSON body

[Request details](#payments)

You get in response:

>Response example with 3DS requirements

~~~http
HTTP/1.1 200 OK
Content-Type: application/json

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



Parameter|Type|Description
--------|---|--------
paymentId|String|Unique payment ID in RSP's system, the same as in the request
createdDateTime|String| System date of the payment creation. Date format:<br>`YYYY-MM-DDThh:mm:ss`
amount|Object|Payment amount data
amount.value|Number|Payment amount rounded down to two decimals
amount.currency	|String|Payment currency (Code Alpha-3 ISO 4217)
status|Object|Payment status data
status.value	|String|Current [payment status](#payment_status)
status.changedDateTime|String|Status refresh date. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh`
customer|Object|Customer identifiers. Possible elements: `email`, `phone`, `account`


To create a payment, most often you need to proceed additional authentication for the customer.  This is the case when:

* Card issuer requires CVV for the payment
* 3DS authentication is needed


In this case, in response you will receive an additional `requirements` object with fields:

* `threeDS` element: `pareq` and `acsUrl` for redirection to the card issuer verification page
* `cvv` element




**2. Complete customer authentication (optional)**

~~~http
POST /partner/payin/v1/sites/Obuc-00/payments/8937947/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "threeDS": {
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
  },
  "cvv2": {
    "cvv2": "string"
  }
}
~~~

Send HTTP POST request to API URL with `{API_REQUEST}`

`/payin/v1/sites/{siteId}/payments/{paymentId}/complete`

 with the parameters:

* card CVV
* 3DS data

[Request details](#payment_complete)

**3. Confirm payment**

Using operation ID (`paymentId`), you can make `capture` to confirm the payment operation.

Send HTTP PUT request with empty body to API URL with `{API_REQUEST}`

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

where:

* **{siteId}** - unique ID on RSP side which you get from [funds hold request](#two_step)
* **{paymentId}** - string operation ID used in action **1**
* **{captureId}** - string capture ID, unique ID generated on RSP's side

[Request details](#capture)

> Capture example

~~~shell
curl https://api.qiwi.com/partner/payin/v1/sites/Obuc-00/payments/8937947/captures/43234 \
-X PUT \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer NDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE' \
-d '{}'
~~~

<!--
**4. Произведите возврат**
-->



## Requests, Statuses and Errors {#api_requests}

### Payment {#payments}

<div id="payin_v1_sites__siteId__payments__paymentId__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-payment-put.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~ json
{
  "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "CARDHOLDER NAME"
  },
  "amount": {
    "currency": "RUB",
    "value": 200.00
  },
  "billId": "string",
  "customer": {
    "account": "string",
    "address": {
      "city": "Moscow",
      "country": "Russian Federation",
      "details": "Severnoe chertanovo microdistrict 1a 1",
      "region": "Moscow city"
    },
    "email": "customer@example.com",
    "phone": "+79991234567"
  },
  "deviceData": {
    "datetime": "2017-09-03T14:30:00+03:00",
    "fingerprint": "TW96aWxsYS81LjAgKHBsYXRmb3JtOyBydjpnZWNrb3ZlcnNpb24p",
    "ip": "127.0.0.1",
    "screenResolution": "1280x1024",
    "timeOnPage": 1440,
    "userAgent": "Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion"
  },
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example payment",
  "customFields": {},
  "flags": [
    "SALE"
  ]
}
~~~

<!--
~~~shell
  user@server:~$ curl "https://api.qiwi.com/partner/pay/v1/sites/112/payments/223E"
    -X PUT --header "Accept: application/json"
    --header "Authorization: Bearer ***"
    --header "Content-type: application/json"
    -d "{
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example payment",
  "paymentId": "223E",
  "billId": "string",
  "amount": {
    "currency": "RUB",
    "value": 200
  },
  "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "CARDHOLDER NAME"
  },
  "customer": {
    "account": "string",
    "address": {
      "city": "Moscow",
      "country": "Russian Federation",
      "details": "Severnoe chertanovo microdistrict 1a 1",
      "region": "Moscow city"
    },
    "email": "customer@example.com",
    "phone": "+79991234567"
  },
  "deviceData": {
    "datetime": "2017-09-03T14:30:00+03:00",
    "fingerprint": "TW96aWxsYS81LjAgKHBsYXRmb3JtOyBydjpnZWNrb3ZlcnNpb24p",
    "ip": "127.0.0.1",
    "screenResolution": "1280x1024",
    "timeOnPage": 1440,
    "userAgent": "Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion"
  },
  "customFields": {},
  "flags": [
    "SAVE_CARD"
  ]
} "
~~~
-->

<!-- 200 -->
~~~json
{
  "paymentId" : "223E",
  "createdDatetime" : "2018-11-01T17:10:31.284+03:00",
  "amount" : {
    "currency" : "RUB",
    "value" : 200.00
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "maskedPan" : "444444******1049"
  },
  "customer" : { },
  "deviceData" : { },
  "requirements" : {
    "threeDS" : {
      "pareq" : "eJyrrgUAAXUA+Q==",
      "acsUrl" : "https://test.paymentgate.ru/acs/auth/start.do"
    }
  },
  "status" : {
    "value" : "WAITING",
    "changedDateTime" : "2018-11-01T17:10:32.607+03:00"
  },
  "customFields" : { },
  "flags" : [ ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### Payment status {#payments_status}

<div id="payin_v1_sites__siteId__payments__paymentId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}",
            "get",
            ['200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- 200 -->
~~~json
{
  "amount" : {
    "currency" : "RUB",
    "value" : 200.00
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "maskedPan" : "444444******1049"
  },
  "createdDatetime" : "2018-11-01T17:10:31.284+03:00",
  "customer" : { },
  "deviceData" : { },
  "requirements" : {
    "threeDS" : {
      "pareq" : "eJyrrgUAAXUA+Q==",
      "acsUrl" : "https://test.paymentgate.ru/acs/auth/start.do"
    }
  },
  "status" : {
    "value" : "WAITING",
    "changedDateTime" : "2018-11-01T17:10:32.607+03:00"
  },
  "customFields" : { },
  "flags" : [ ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### Completing authentication {#payment_complete}

<div id="payin_v1_sites__siteId__payments__paymentId__complete_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-complete-post.json', function( data ) {
        window.requestUI(
          data,
          "api",
          "payin/v1/sites/{siteId}/payments/{paymentId}/complete",
          "post",
          ['RequestBody', '200', '4xx', '5xx']
        )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~json
{
  "threeDS": {
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
  },
  "cvv2": {
    "cvv2": "string"
  }
}
~~~

<!-- 200 -->
~~~json
{
  "paymentId" : "223E",
  "createdDatetime" : "2018-11-01T17:10:31.284+03:00",
  "amount" : {
    "currency" : "RUB",
    "value" : 200.00
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : 0.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "maskedPan" : "444444******1049"
  },
  "customer" : { },
  "deviceData" : { },
  "requirements" : {
    "threeDS" : {
      "pareq" : "eJyrrgUAAXUA+Q==",
      "acsUrl" : "https://test.paymentgate.ru/acs/auth/start.do"
    }
  },
  "status" : {
    "value" : "COMPLETED",
    "changedDateTime" : "2018-11-01T17:10:32.607+03:00"
  },
  "customFields" : { },
  "flags" : [ ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

<!--
~~~shell
user@server:~$ curl -X POST "https://api.qiwi.com/partner/pay/v1/sites/112/payments/332121DS/complete"
  --header "Accept: application/json"
  --header "Content-Type: application/json"
  --header "Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9"
  -d '{
  "threeDS": {
    "pares": "eJxVUmtvgjAUuG79oClYe51uDcsi2B...."
  },
  "cvv2": {
    "cvv2": "string"
  }
}'
~~~ -->

<!-- ~~~http
POST /payin-core/v1/sites/112/payments/332121DS/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: edge.qiwi.com

{
  "threeDS": {
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
  },
  "cvv2": {
    "cvv2": "string"
  }
}
~~~
-->

### Payment confirmation {#capture}


<!--
~~~shell
user@server:~$ curl -X PUT "https://api.qiwi.com/partner/pay/v1/sites/112/payments/332121DS/captures/C332121DS"
  --header "Accept: application/json"
  --header "Content-Type: application/json"
  --header "Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9"
  -d '{
{
  "callbackUrl": "https://example.com/callbacks/capture",
  "comment": "Example capture",
  "amount": {
    "currency": "RUB",
    "value": 200
  }
}'
~~~
-->


<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-capture-put.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~json
{
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example capture"
}
~~~

<!-- 200 -->
~~~json
{
  "captureId": "bxwd8096",
  "createdDatetime": "2018-11-20T16:29:58.96+03:00",
  "amount": {
    "currency": "RUB",
    "value": 6.77
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:29:58.963+03:00"
  }
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~


### Capture status {#capture_status}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-capture-get.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}",
            "get",
            ['200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- 200 -->
~~~json
{
  "captureId": "bxwd8096",
  "createdDatetime": "2018-11-20T16:29:58.96+03:00",
  "amount": {
    "currency": "RUB",
    "value": 6.77
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:29:58.963+03:00"
  }
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

<!--
### Статус подтверждений

<div id="v1_sites__siteUid__payments__paymentId__captures_get">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-captures-get.json', function( data ) {
          window.requestUI(
              data,
              "v1/sites/{siteUid}/payments/{paymentId}/captures",
              "get",
              [200, 400, 401, 403, 404, 500, 501, 503]
              )
          })
    });
  </script>
</div>
-->

<!-- 200 -->
<!--
~~~json
{
  "captureId": "bxwd8096",
  "createdDatetime": "2018-11-20T16:29:58.96+03:00",
  "amount": {
    "currency": "RUB",
    "value": "6.77"
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:29:58.963+03:00"
  }
}
~~~
-->
<!-- 400 -->
<!--
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83",
  "cause" : {
    "amount" : [ "Invalid format. Amount value must be greater then zero" ]
  }
}
~~~
-->
<!-- 401 -->
<!--
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~
-->
<!-- 403 -->
<!--
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~
-->
<!-- 404 -->
<!--
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~
-->
<!-- 500 -->
<!--
~~~json
{
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~
-->
<!-- 501 -->
<!--
~~~json
{
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
 }
~~~
-->
<!-- 503 -->
<!--
~~~json
{
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
 }
~~~
-->

### Refund {#refund}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refund-put.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>


<!-- Request body -->
~~~json
{
  "amount": {
    "value": 2.34,
    "currency": "RUB"
  }
}
~~~



<!-- 200 -->
~~~json
{
  "refundId": "tcwv3132",
  "createdDatetime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": 2.34
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:32:55.55+03:00"
  },
  "flags": [
    "REVERSAL"
  ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### Refund status {#refund_status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refund-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}",
            "get",
            ['200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- 200 -->
~~~json
{
  "refundId": "tcwv3132",
  "createdDatetime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": 2.34
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:32:55.55+03:00"
  },
  "flags": [
    "REVERSAL"
  ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### All refunds status {#refunds_status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds_get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refunds-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds",
            "get",
            ['200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- 200 -->
~~~json
[
 {
  "refundId": "tcwv3132",
  "createdDatetime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": 2.34
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2018-11-20T16:32:55.55+03:00"
  },
  "flags": [
    "REVERSAL"
  ]
 }
]
~~~

<!-- 4xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "validation.error",
  "description" : "Validation error",
  "userMessage" : "Validation error",
  "dateTime" : "2018-11-13T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

<!-- 5xx -->
~~~json
{
  "serviceName" : "payin-core",
  "errorCode" : "payin.resource.not.found",
  "userMessage" : "Resource not found",
  "description" : "Resource not found",
  "traceId" : "c3564ba25e221fe3",
  "dateTime" : "2018-11-13T16:30:52.464+03:00"
}
~~~

### Reasons for rejection {#reasons}

Code of the reason for the payment rejection is returned in `status.reason` field of request responses and in `status.reasonCode` field of notifications.

Rejection reason code| Description
------------------|--------
INVALID_STATE| Incorrect transaction status
INVALID_AMOUNT| Incorrect payment amount
DECLINED_BY_MPI | Rejected by MPI
DECLINED_BY_FRAUD| Rejected by fraud monitoring
GATEWAY_INTEGRATION_ERROR| Acquirer integration error
GATEWAY_TECHNICAL_ERROR| Technical error on acquirer side
ACQUIRING_MPI_TECH_ERROR| Technical error on 3DS authentication
ACQUIRING_GATEWAY_TECH_ERROR| Technical error
ACQUIRING_ACQUIRER_ERROR| Technical error
ACQUIRING_AUTH_TECHNICAL_ERROR| Error on fund authorization
ACQUIRING_ISSUER_NOT_AVAILABLE| Issuer error. Issuer is not available at the moment
ACQUIRING_SUSPECTED_FRAUD| Issuer error. Fraud suspicion
ACQUIRING_LIMIT_EXCEEDED| Issuer error. Some limit exceeded
ACQUIRING_NOT_PERMITTED| Issuer error. Operation not allowed
ACQUIRING_INCORRECT_CVV| Issuer error. Incorrect CVV
ACQUIRING_EXPIRED_CARD| Issuer error. Incorrect card expiration date
ACQUIRING_INVALID_CARD| Issuer error. Verify card data
ACQUIRING_INSUFFICIENT_FUNDS| Issuer error. Not enough funds
ACQUIRING_UNKNOWN| Unknown error
BILL_ALREADY_PAID| Bill already paid
PAYIN_PROCESSING_ERROR| Payment processing error



<!--# Service Information -->

# Server Notifications {#callback}

The Protocol supports the following notification types: `PAYMENT`, `CAPTURE`, `REFUND`, and `BILL`.

A notification is an incoming POST request with body containing JSON serialized payment data in UTF-8 codepage.

<aside class="success">
Payment notification (callback) sends exclusively by HTTPS protocol to port 443. The server certificate must be issued by trusted certification center like Comodo, Verisign, Thawte etc.
</aside>

The notification contains a [request signature](#notifications_auth) which RSP should verify on its side to secure from notification fraud.

To make sure the notification is from QIWI, you ought to accept notifications from the following IP addresses belonging to QIWI:

* 79.142.16.0/20
* 195.189.100.0/22
* 91.232.230.0/23
* 91.213.51.0/24

Notification is treated as successfully delivered if RSP server responds with HTTP code `200 OK`.
Therefore, our system will repeat notification delivery attempts with incremental time during the day until it receives `200 OK` HTTP code server response.

<aside class="notice">
If by any reason RSP server accepts the notification but responds not as <code>200 OK</code>, then it should treat the repeated transaction notification as already received one.
</aside>

<aside class="warning">
The responsibility for any financial losses due to omitted verification of the <a href="#notifications_auth">signature</a> parameter lies solely on RSP.
</aside>

RSP's notification server address is specified in your Personal Profile on <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> site in <b>Settings</b> section. You may also specify the address in optional `callbackUrl` parameter of [API](#api_requests) requests.

## Notification Authorization {#notifications_auth}

You need to verify the inbound notification's digital signature. It is placed in `Signature` HTTP header with UTF-8 encoding for `PAYMENT`, `CAPTURE`, `REFUND` notifications and in `X-Api-Signature-SHA256` HTTP header with UTF-8 encoding for `BILL` notifications. We use HMAC integrity test with SHA256 hash.

Use the signature verification algorithm:

1. Join values of some parameters from the notification with "\|" separator. For example:

   `parameters = {amount.currency}|{amount.value}|{billId}|{siteId}|{status.value}`

   where `{*}` – notification parameter value. All values are treated as strings. Make sure strings are UTF-8 encoded. **Parameters to join are specified in the notification description**.


2. Calculate hash HMAC value with SHA256 algorithm (signature string and secret key are UTF8-encoded):

   `hash = HMAС(SHA256, token, parameters)`
   where:

   * `token` – HMAC function key which you can obtain in your [Account](https://kassa.qiwi.com/service/core/merchants?);
   * `parameters` – string from step 1;

3. Compare `X-Api-Signature-SHA256` HTTP header value with the result of step 2.


## PAYMENT, CAPTURE, REFUND Notification Types


<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>


>PAYMENT/REFUND/CAPTURE Notification example

~~~json
{
   "payment/refund/capture":{
      "paymentId/refundId/captureId":"4504751",
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
      "customer":{
         "ip":"79.142.20.248",
         "account":"token32",
         "phone":"0"
      },
      "billId":"testing122",
      "flags":[
         "SALE"
      ]
   },
   "type":"PAYMENT",
   "version":"1"
}
~~~


Parameter|Description|Type
--------|--------|---
paymentId/refundId/captureId|Payment/refund/capture operation unique identifier in RSP's system|String(200)
type| Operation type|String(200)
createdDateTime| System date of the operation creation | URL-encoded string<br>`YYYY-MM-DDThh:mm:ss`
amount|Object| Operation amount data
amount.value | Operation amount rounded down to two decimals | Number(6.2)
amount.currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)
billId| Corresponding invoice ID| String(200)
status | Operation status data | Object
status.value |Invoice status value | String
status.changedDatetime|Date of operation status update| URL encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
status.reasonCode| Rejection reason code| String(200)
status.reasonMessage| Rejection reason description| String(200)
status.errorCode| Error code| Number
paymentMethod| Payment method data| Object
paymentMethod.type| Payment method type| String
paymentMethod.maskedPan| Masked card PAN| String
paymentMethod.rrn| Payment RRN| Number
paymentMethod.authCode| Payment Auth code| Number
customer | Customer data | Object
customer.phone |Phone number to which invoice issued (if specified) |String
customer.email| E-mail to which invoice issued (if specified)|String
customer.account| Customer ID in RSP system (if specified)|String
customer.ip| IP address |String
customer.country| Country from address string |String
flags| Additional API commands| Array of Strings. Possible values - `SALE` , `REVERSAL`
version | Callback version | String


By default, signature is [verified](#notifications_auth) for those notification fields:


PAYMENT:
`payment.paymentId|payment.createdDateTime|payment.amount.value`

REFUND:
`refund.refundId|refund.createdDateTime|refund.amount.value`

CAPTURE:
`capture.captureId|capture.createdDateTime|capture.amount.value`




Callback service sorts unsuccessful notifications on the following queues:

* Two attempts on waiting 5 seconds

* Two attempts on waiting 1 minutes

* Two attempts on waiting 5 minutes

Time of secondary sending notification may slightly shift upward.



<!--
--===< NOTIFICATION_STRUCTURE >===--
{
  :
  {
    "paymentId/refundId/captureId":"9999999",
    "createdDateTime":"2019-06-03T08:19:16+03:00",
    "status":{
      "value":"SUCCESS/WAITING/DECLINE",
      "changedDateTime":"2019-06-03T08:19:16+03:00",
      "reasonCode":"payin.gateway.acquiring.declined-fraud",
      "reasonMessage":"Declined by fraud",
      "errorCode":"1234"
    },
    "amount":{
      "value":111.11,
      "currency":"RUB"},
    "paymentMethod": <PAYMENT_METHOD>,
    "customer":{
      "ip":"xxx.xxx.xxx.xxx",
      "email":"my_mail@mail.com",
      "account":"dasd32d2d2",
      "phone":"79991112233",
      "country":"Russian Federation",
      "city":"some city",
      "region":"some region"
    },
    "gatewayData": <GATEWAY_DATA>,
    "billId":"f1e1a1f11ae111a11a111111e1111111",
    "flags":["SALE/REVERSAL"]
  },
  "type":"PAYMENT/REFUND/CAPTURE",
  "version":"1"
}




--===< PAYMENT_METHOD >===--
"paymentMethod":{
  "type":"CARD",
  "maskedPan":"411111\*\*\*\*\*\*0001"
}
"paymentMethod":{
  "type":"QIWI_WALLET",
  "phone":"79991112233"
}
"paymentMethod":{
  "type":"SAVED_CARD",
  "token":"12341234123412341234"
}
"paymentMethod":{
  "type":"MOBILE_COMMERCE",
  "phone":"79991112233"
}
-->


## BILL Notifications

These notifications are sent when you use [Checkout](#invoicing). The notification is sent as soon as the invoice is paid.


<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>X-Api-Signature-SHA256: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

 >BILL Notification example

~~~ json
{
   "bill":{
      "siteId":"Obuc-00",
      "billId":"testing122",
      "amount":{
         "value":"2211.24",
         "currency":"RUB"
      },
      "status":{
         "value":"PAID",
         "changedDateTime":"2019-10-08T11:31:39+03"
      },
      "customer":{
         "account":"account42"
      },
      "customFields":{},
      "comment":"Spasibo",
      "creationDateTime":"2019-10-08T11:30:16+03",
      "expirationDateTime":"2019-10-13T14:30:00+03"
   },
   "version":"1"
}
~~~


Parameter|Description|Type
--------|--------|---
billId|Invoice operation unique identifier in RSP's system|String(200)
siteId| RSP' site identifier in QIWI Kassa|Number
amount|Object| Operation amount data
amount.value | Operation amount rounded down to two decimals | Number(6.2)
amount.currency | Operation currency (Code: Alpha-3 ISO 4217) | String(3)
status | Invoice status data | Object
status.value |Invoice status value | String
status.changedDatetime|Date of invoice status update| URL encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
customer | Customer data | Object
customer.phone |Phone number to which invoice issued (if specified) |String
customer.email| E-mail to which invoice issued (if specified)|String
customer.account| Customer ID in RSP system (if specified)|String
creationDatetime | Invoice creation date | URL encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
expirationDateTime | Invoice payment due date | URL encoded string<br>`YYYY-MM-DDThh:mm:ssZ`
comment | Invoice comment | String(255)
customFields | Additional invoice data (if specified)| Object
version | Callback version | String

By default, signature is [verified](#notifications_auth) for those notification fields:

`amount.currency|amount.value|billId|siteId|status.value`

<!--
### Заголовки

*  `Content-type: application/json`

Уведомление доставляется на переданный в запросе параметр `callbackUrl` в виде POST запроса с параметрами, указанными ниже.


Параметр|Тип данных| Участвует в sign | Описание
--------|----------|------------------|---------
sign | string(64) | - | [Контрольная сумма](#sign) переданных параметров. _Контрольная сумма передается в верхнем регистре._




## Доступные валюты и страны

-->

# Electronic Receipt Transfer (for Fed.Rule 54) {#cheque}


The payment receipt is sent in `cheque` optional field of `bill` and `payment` operation requests in [Checkout](#invoice_put) and [API](#payments) methods, respectively.


<aside class="notice">
To enable fiscal data service, provide your organization's tax identification number (TIN) used for registration in online cash service.
For ATOL service, provide the following data also:<ul>
<li>RSP's email for receipt</li>
<li>full title of the organization</li>
<li>Fiscal data operator details (name, TIN, URL)</li>
<li>login / password for token generation</li>
<li>commodity group code</li></ul>
</aside>

<!--<aside class="notice">
Пока аккаунт находится в тестовом режиме, чек так также будет проходить по тестовому контуру.
</aside>
-->


## Receipt Description

~~~ json
{
 ...
 "cheque" : {
  "sellerId" : 3123011520,
	"customerContact" : "Test customer contact",
  "chequeType" : "COLLECT",
	"taxSystem" : "OSN",
	"positions" : [
    {
      "quantity" : 1,
      "price" : {
        "value" : 7.82,
        "currency" : "RUB"
      },
      "tax" : "NDS_0",
      "paymentSubject" : "PAYMENT",
      "paymentMethod" : "FULL_PAYMENT",
      "description" : "Test description"
    }
	]
 }
}
~~~

Parameter|Req.|Type|Description
--------|-------|----------|--------
sellerId|Y|Number|Organization's TIN
chequeType|Y|Number|Cash operation (1054 fiscal tag):<br> `COLLECT` – Incoming cash <br> `COLLECT_RETURN` - Cash return <br> `CONSUME` - Outcoming cash <br> `CONSUME_RETURN` - Outcoming cash return
customerContact|Y|String(64)|Customer's phone or e-mail  (fiscal tag 1008)
taxSystem|Y|Number|Tax system (fiscal tag 1055):<br> `OSN` - General, "ОСН" <br>`USN` – Simplified income, "УСН доход" <br>`USN_MINUS_CONSUM`  – Simplified income minus expense, "УСН доход - расход" <br>`ENVD` – United tax to conditional income, "ЕНВД" <br>`ESN` - United agriculture tax, "ЕСН" <br>`PATENT` – Patent tax system, "Патент"
positions|Y|array|Commodities list
--------|-------|----------|--------
quantity|Y|Number|Quantity (fiscal tag 1023)
price|Y|Number|One item price, with discounts and extra charges (fiscal tag 1079)
tax|Y|Number|VAT rate (fiscal tag 1199):<br>`NDS_CALC_18_118` - VAT rate 18% (18/118) <br>`NDS_CALC_10_110` – VAT rate 10% (10/110) <br>`NDS_0` – VAT rate 0% <br>`NO_NDS` – VAT not applicable<br>`NDS_CALC_20_120` – VAT rate 20% (20/120) (20/120)
description|Y|string(128)|Name
paymentMethod|Y|Number|Cash type (fiscal tag 1214):<br>`ADVANCED_FULL_PAYMENT` – payment in advance 100%. Full payment in advance before commodity provision<br>`PARTIAL_ADVANCE_PAYMENT`  – payment in advance. Partial payment before commodity provision<br>`ADVANCE` – prepayment<br>`FULL_PAYMENT` – full payment, taking into account prepayment, with commodity provision<br>`PARTIAL_PAYMENT` – partial payment and credit payment. Partial payment for the commodity at the moment of delivery, with future credit payment.<br>`CREDIT`  – credit payment. Commodity is delivered with no payment at the moment and future credit payment is expected.<br>`CREDIT_PAYMENT` – payment for the credit. Commodity payment after its delivery with future credit payment.
paymentSubject|Y|Number|Payment subject (fiscal tag 1212):<br>`COMMODITY` – commodity except excise commodities (name and other properties describing the commodity).<br>`EXCISE_COMMODITY` – excise commodity (name and other properties describing the commodity).<br>`WORK` – work (name and other properties describing the work). .<br>`SERVICE` – service (name and other properties describing the service).<br>`GAMBLING_RATE` – gambling rate (in any gambling activities).<br>`GAMBLING_PRIZE` – gambling prize payment (in any gambling activities)в.<br>`LOTTERY_TICKET` – lottery ticket payment (in accepting payments for lottery tickets, including electronic ones, lottery stakes in any lottery activities).<br>`LOTTERY_PRIZE` – lottery prize payment (n any lottery activities). <br>`GRANTING_RESULTS_OF_INTELLECTUAL_ACTIVITY` – provision of rights to use intellectual activity results.<br>`PAYMENT` – payment (advance, pre-payment, deposit, partial payment, credit, fine, bonus, reward, or any similar payment subject).<br>`AGENCY_FEE` – agent's commission (in any fee to payment agent, bank payment agent, commissioner or other agent service).<br>`COMPAUND_PAYMENT_SUBJECT` – multiple payment subject (in any payment constituent subject to any of the above).<br>`OTHER_PAYMENT_SUBJECT` – other payment subject not related to any of the above.


# Tokenization {#token}

The **Protocol** supports card tokenization. It allows you to save encrypted customer's cards and use it for recurring payments.

By default, tokenization in the protocol is disabled. Contact your personal manager to enable this option.

## Payment Token Issue

>Request body example

~~~json
{
   "amount": {  
     "currency": "RUB",  
     "value": 2211.24
   },
   "customer": {
   	"account":"token324"
   },
   "flags":["BIND_PAYMENT_TOKEN"]
}
~~~


To issue payment card token, you need to add the following fields in the [payment request](#payments):

* payment option `flags:["BIND_PAYMENT_TOKEN"]` - a command indicating the issue of payment token
* `customer.account` - Customer unique ID in RSP's information system

<aside class="warning">
Do not use the same <code>account</code> value for your Customers. It may allow any Customer to pay from another's cards.
</aside>

>Token notification example

~~~json
{
  "payment":{
    "paymentId":"9790769",
    "tokenData":{
      "paymentToken":"66aebf5f-098e-4e36-922a-a4107b349a96",
      "expiredDate":"2021-12-31T00:00:00+03:00"
    },
    "type":"PAYMENT",
    "createdDateTime":"2020-01-23T15:07:35+03:00",
    "status":{
      "value":"SUCCESS",
      "changedDateTime":"2020-01-23T15:07:36+03:00"
    },
    "amount":{
        "value":2211.24,
        "currency":"RUB"
    },
    "paymentMethod":{
      "type":"CARD",
      "maskedPan":"4111111111",
      "cardHolder":"CARD HOLDER",
      "cardExpireDate":"12/2021",
      "type":"CARD"
    },
    "customer":{
      "ip":"79.142.20.248",
      "account":"token324",
      "phone":"0"
    },
    "billId":"testing1222213",
    "flags":["SALE"]
  },
  "type":"PAYMENT",
  "version":"1"
}
~~~

You would receive the following data in subsequent `payment` type notification:

Parameter|Type|Description
--------|-------|----------
tokenData|Object| Card token data
tokenData.paymentToken|String| Card token
tokenData.expiredDate|Object|Token expiration date. Date format:<br>`YYYY-MM-DDThh:mm:ss±hh:mm`

You should use the obtained data for the Customer's payments.


## How To Pay By Token {#token_pay}

You can pay by token using [API](#api) methods only.

Instead of card payment data, use the following parameters in  `paymentMethod` object:

>Token payment request example

~~~json
{
  "amount": {
    "currency": "RUB",
    "value": 2000.00
  },
  "paymentMethod" : {
    "type": "TOKEN",
    "paymentToken" : "f42abb6c-4b6b-464e-adcc-fbdc197bd24d"
  },
  "customer": {
        "account": "token324"
  }
}
~~~

Partameter|Type|Description
--------|---|--------
type|String|Operation type, use `TOKEN` only
paymentToken|String| Card token

Make sure you specify unique customer ID related to the payment token in `account` field. Without this field, payment by token is impossible.

# Payouts {#payouts}

By default, we make payouts for processed operations once every two days when 10,000 rubles reached. If you need a custom schedule for payouts, contact your personal manager.

QIWI holds a commission for each confirmed operation. If operation is rejected before the confirmation, commission will not hold.  If partial refund is made before the confirmation, commission will be recalculated.


# Test Data {#test_data}

Use [production url](#test_mode) for testing purposes.

By default, for each new RSP `siteId` is created in the system in testing mode. You can ask your manager to make testing mode of any of your  `siteId`, or add new `siteId` in testing mode.

* To make tests for payment operations, you may use any card number complied with Luhn algorithm.
* In testing mode, you may use only Russian ruble (643 code) for the currency.
* CVV in testing mode may be arbitrary (3 digits).


<h4>Test card numbers</h4>
<h4 id="visa">
</h4>
<h4 id="mc">
</h4>
<button class="button-popup" id="generate">Get more cards</button>

To make tests of various payment methods and responses, use different expiry dates:

* If month of expiry date is `02`, then operation is treated as unsuccessful.
* If month of expiry date is `03`, then operation will process successfully with 3 seconds timeout.
* If month of expiry date is `04`, then operation will process unsuccessfully with 3 seconds timeout.
* In all other cases, operation is treated as successful.

<a name="test_limit"></a>
Test environment has restrictions on the total amount and number of operations. By default, maximum amount of a test transaction is 10 rubles. Maximum number of test transactions is 100 per day (MSK time zone). Only test transactions within allowed amount are taken into account.

To process 3DS operation, use `unknown name` as card holder name.

3DS in testing mode may be properly tested on real card number only.

# Error Codes {#errors}

The Online Payment Protocol API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- The API request is hidden.
404 | Not Found -- The specified resource could not be found.
405 | Method Not Allowed -- You tried to create a payment with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The resource requested has been removed from our servers.
429 | Too Many Requests -- You're requesting too many payments.
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

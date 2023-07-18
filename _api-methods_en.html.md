# API Method Reference {#references}

<aside class="notice">Additional fields might be added to the requests and responses. Check <a href="https://github.com/QIWI-API/payments-docs/blob/master/payments_en.html.md">our Github</a> for updates.</aside>

## Invoice {#invoice_put}

<div id="payin_v1_sites__siteId__bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
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
   "billPaymentMethodsType": [
      "QIWI_WALLET",
      "SBP"
   ],
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {},
   "customFields": {
    "cf1": "Some data"
   }
}
~~~

<!-- 200 -->
~~~json
{
    "billId": "893794793973",
    "invoiceUid": "d875277b-6f0f-445d-8a83-f62c7c07be77",
    "amount": {
      "value": "100.00",
      "currency": "RUB"
    },
    "status": {
      "value": "CREATED",
      "changedDateTime": "2018-03-05T11:27:41"
    },
    "comment": "Text comment",
    "customFields": {
      "cf1": "Some data"
    },
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
}

Response when the invoice exists and has been already expired

{
 "billId": "12345",
 "invoiceUid": "3b39ad6d-f111-401d-8108-ed11af920a65",
 "amount": {
   "currency": "RUB",
   "value": "1.00"
 },
 "expirationDateTime": "2023-03-21T13:02:00+03:00",
 "status": {
   "value": "EXPIRED",
   "changedDateTime": "2023-03-21T13:02:00+03:00"
 },
 "comment": "Text comment",
 "flags": [
   "TEST"
 ],
 "payUrl": "https://oplata.qiwi.com/form?invoiceUid=3b39ad6d-f211-401d-8008-ed11af920a65"
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

## Invoice status {#invoice-details}

<div id="payin_v1_sites__siteId__bills__billId__details_get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-details-get.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}/details",
            "get",
            ['RequestBody', '200', '4xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/site-01/bills/3a3d0286cefe645d2b11/details HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
    "billId": "3a3d0286cefe645d2b11",
    "invoiceUid": "235d8d5a-38ed-11fc-9ab6-8b5a65d7e2f8",
    "amount": {
        "currency": "RUB",
        "value": "3000.00"
    },
    "expirationDateTime": "2023-05-07T19:25:36+03:00",
    "status": {
        "value": "PAID",
        "changedDateTime": "2023-04-07T19:28:12+03:00"
    },
    "comment": "Детская футбольная школа «Тигры»",
    "flags": [
        "SALE"
    ],
    "payUrl": "https://oplata.qiwi.com/form?invoiceUid=235d8d5a-11ed-46fc-9ab6-8b5a65d7e2f8",
    "payments": [
        {
            "paymentId": "cd4a4ade-011e6-484d-87c8-40a7f48326fa",
            "billId": "3a3d0286cefe645d2b11",
            "createdDateTime": "2023-04-07T19:27:52+03:00",
            "amount": {
                "currency": "RUB",
                "value": "3000.00"
            },
            "capturedAmount": {
                "currency": "RUB",
                "value": "3000.00"
            },
            "refundedAmount": {
                "currency": "RUB",
                "value": "0.00"
            },
            "paymentMethod": {
                "type": "CARD",
                "maskedPan": "422264******1232",
                "rrn": "309711196151",
                "authCode": "231181"
            },
            "status": {
                "value": "COMPLETED",
                "changedDateTime": "2023-04-07T19:28:12+03:00"
            },
            "comment": "Детская футбольная школа «Тигры»",
            "customFields": {
                "auto_capture": "true",
                "invoice_creation_type": "PUBLIC_KEY"
            },
            "paymentCardInfo": {
                "issuingCountry": "643",
                "issuingBank": "Сбербанк России",
                "paymentSystem": "VISA",
                "fundingSource": "DEBIT",
                "paymentSystemProduct": "N1|Visa Rewards"
            }
        },
        {
            "paymentId": "A30971626215731E01110841111138B2",
            "billId": "3a3d0286cefe645d2b11",
            "createdDateTime": "2023-04-07T19:26:21+03:00",
            "amount": {
                "currency": "RUB",
                "value": "3000.00"
            },
            "capturedAmount": {
                "currency": "RUB",
                "value": "3000.00"
            },
            "refundedAmount": {
                "currency": "RUB",
                "value": "0.00"
            },
            "paymentMethod": {
                "type": "SBP",
                "phone": "0079031232001"
            },
            "status": {
                "value": "DECLINED",
                "changedDateTime": "2023-04-07T19:26:23+03:00",
                "reason": "GATEWAY_INTEGRATION_ERROR",
                "reasonMessage": "I07999 OPKC_TECH_ERROR"
            },
            "comment": "Детская футбольная школа «Тигры»",
            "customFields": {
                "auto_capture": "true",
                "invoice_creation_type": "PUBLIC_KEY"
            }
        }
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
  "dateTime" : "2022-03-05T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

## List of payments on the invoice {#invoice-payments}

<div id="payin_v1_sites__siteId__bills__billId__get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/site-01/bills/3a3d0286cefe645d2b11 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
[
    {
        "paymentId": "cd4a4ade-12e6-484d-87c8-40a7f48326fa",
        "billId": "3a3d0286cefe645d2b11",
        "createdDateTime": "2023-04-07T19:27:52+03:00",
        "amount": {
            "currency": "RUB",
            "value": "3000.00"
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": "3000.00"
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": "0.00"
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "422264******1232",
            "rrn": "309711234151",
            "authCode": "232481"
        },
        "status": {
            "value": "COMPLETED",
            "changedDateTime": "2023-04-07T19:28:12+03:00"
        },
        "comment": "Детская футбольная школа «Тигры»",
        "customFields": {
            "auto_capture": "true",
            "invoice_creation_type": "PUBLIC_KEY"
        },
        "paymentCardInfo": {
            "issuingCountry": "643",
            "issuingBank": "Сбербанк России",
            "paymentSystem": "VISA",
            "fundingSource": "DEBIT",
            "paymentSystemProduct": "N1|Visa Rewards"
        }
    },
    {
        "paymentId": "A30971626215731E000008415C2D38B2",
        "billId": "3a3d0286cefe645d2b00",
        "createdDateTime": "2023-04-07T19:26:21+03:00",
        "amount": {
            "currency": "RUB",
            "value": "3000.00"
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": "3000.00"
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": "0.00"
        },
        "paymentMethod": {
            "type": "SBP",
            "phone": "0079099922001"
        },
        "status": {
            "value": "DECLINED",
            "changedDateTime": "2023-04-07T19:26:23+03:00",
            "reason": "GATEWAY_INTEGRATION_ERROR",
            "reasonMessage": "I07999 OPKC_TECH_ERROR"
        },
        "comment": "Детская футбольная школа «Тигры»",
        "customFields": {
            "auto_capture": "true",
            "invoice_creation_type": "PUBLIC_KEY"
        }
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

## Payment {#payments}

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
~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "billId": "testBillId28",
   "amount": {
    "currency": "RUB",
    "value": 200.00
  },
 "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "CARDHOLDER NAME",
    "cardTokenPaymentType" : "INSTALLMENT",     
    "firstTransaction" : {         
      "paymentId" : "testPaymentId28"     
    } 
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
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example payment",
  "customFields": {
    "cf1": "Some data"
  },
  "flags": [
    "SALE"
  ]
}
~~~

<!-- 200 -->
~~~json
{
  "paymentId" : "223E",
  "createdDateTime" : "2018-11-01T17:10:31.284+03:00",
  "amount" : {
    "currency" : "RUB",
    "value" : "200.00"
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
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
  "paymentCardInfo": {
    "issuingCountry": "810",
    "issuingBank": "QiwiBank",
    "paymentSystem": "VISA",
    "fundingSource": "CREDIT",
    "paymentSystemProduct": "P|Visa Gold"
  },
  "customFields" : {
    "cf1": "Some data"
  },
  "flags" : [ ]
}
~~~

<!-- 4xx -->
~~~json
{
  "serviceName":"payin-core",
  "errorCode":"validation.error",
  "description":"Validation error",
  "userMessage":"Validation error",
  "dateTime":"2022-11-13T16:49:59.166+03:00",
  "traceId":"fd0e2a08c63ace83",
  "cause":{
    "paymentToken": [
      "Exchange token error. Token disabled, please create new one"
    ]
  }
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

## Payment status {#payment_status}

<div id="payin_v1_sites__siteId__payments__paymentId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "amount" : {
    "currency" : "RUB",
    "value" : "200.00"
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
  },
  "paymentMethod" : {
    "type" : "CARD",
    "maskedPan" : "444444******1049",
    "rrn": "124",
    "authCode": "182817",
  },
  "createdDateTime" : "2018-11-01T17:10:31.284+03:00",
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
  "serviceName":"payin-core",
  "errorCode":"validation.error",
  "description":"Validation error",
  "userMessage":"Validation error",
  "dateTime":"2022-11-13T16:49:59.166+03:00",
  "traceId":"fd0e2a08c63ace83",
  "cause":{
    "paymentToken": [
      "Exchange token error. Token disabled, please create new one"
    ]
  }
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

## Completing customer authentication {#payment_complete}

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
~~~http
POST /partner/payin/v1/sites/test-01/payments/1811/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "threeDS": {
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
  }
}
~~~

<!-- 200 -->
~~~json
{
  "paymentId" : "223E",
  "createdDateTime" : "2018-11-01T17:10:31.284+03:00",
  "amount" : {
    "currency" : "RUB",
    "value" : "200.00"
  },
  "capturedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
  },
  "refundedAmount" : {
    "currency" : "RUB",
    "value" : "0.00"
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

## Payment confirmation {#capture}

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
~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811/captures/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example capture"
}
~~~

<!-- 200 -->
~~~json
{
  "captureId": "bxwd8096",
  "createdDateTime": "2018-11-20T16:29:58.96+03:00",
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

## Payment confirmation status {#capture_status}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-capture-get.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/payments/1811/captures/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "captureId": "bxwd8096",
  "createdDateTime": "2018-11-20T16:29:58.96+03:00",
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

## Faster Payment System QR Code {#qr-code-sbp}

### POST Methods {#qr-code-sbp-post}

<div id="payin_v1_sites__siteId__sbp_qrCodes_post_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-sbp-post.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/sbp/qrCodes",
            "post",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
POST /partner/payin/v1/sites/test-01/sbp/qrCodes HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "qrCodeUid": "Test12",
  "amount": {
    "value": "1.00",
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 60,
    "image": {
      "mediaType": "image/png",
      "width": 300,
      "height": 300
    }
  },
  "paymentPurpose": "Flower for my girlfriend",
  "redirectUrl": "http://example.com"
}
~~~

<!-- 200 -->
~~~json
{
  "qrCodeUid": "Test12",
  "amount": {
    "currency": "RUB",
    "value": "1.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 60,
    "image": {
      "mediaType": "image/png",
      "width": 300,
      "height": 300,
      "content": "iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAYAA"
    },
    "payload": "https://qr.nspk.ru/AD10006M8KH234K782OQM0L13JI31LQD?type=02&bank=100000000009&sum=200&cur=RUB&crc=C63A",
    "status": "CREATED"
  },
  "createdOn": "2022-08-11T20:10:32+03:00"
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

### PUT Method {#qr-code-sbp-put}

<div id="payin_v1_sites__siteId__sbp_qrCodes__qrCodeUid__put_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-sbp-put.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/sbp/qrCodes/{qrCodeUid}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
PUT /partner/payin/v1/sites/test-01/sbp/qrCodes/Test12 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "amount": {
    "value": "1.00",
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 60,
    "image": {
      "mediaType": "image/png",
      "width": 300,
      "height": 300
    }
  },
  "paymentPurpose": "Flower for my girlfriend",
  "redirectUrl": "http://example.com"
}
~~~

<!-- 200 -->
~~~json
{
  "qrCodeUid": "Test12",
  "amount": {
    "currency": "RUB",
    "value": "1.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 60,
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

## Faster Payment System QR Code Status {#qr-code-sbp-get}

<div id="payin_v1_sites__siteId__sbp_qrCodes__qrCodeUid__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../eui_jsons/payin-sbp-get.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/sbp/qrCodes/{qrCodeUid}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/sbp/qrCodes/Test HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "qrCodeUid": "Test",
  "amount": {
    "currency": "RUB",
    "value": "1.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 60,
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

## Refund {#refund}

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
~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811/refunds/tcwv3132 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

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
  "createdDateTime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": "2.34"
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

## Refund status {#refund_status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refund-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/payments/1811/refunds/tcwv3132 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "refundId": "tcwv3132",
  "createdDateTime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": "2.34"
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

## All refunds status {#refunds_status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds_get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refunds-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/payments/1811/refunds HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
[
 {
  "refundId": "tcwv3132",
  "createdDateTime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": "2.34"
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

## Refund decline {#refund_decline}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__decline_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-refund-decline.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}/decline",
            "post",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
POST /partner/payin/v1/sites/test-01/payments/1811/refunds/tcwv3132/decline HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "refundId": "tcwv3132",
  "createdDateTime": "2018-11-20T16:32:55.547+03:00",
  "amount": {
    "currency": "RUB",
    "value": "2.34"
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

## Card verification {#card-check-api}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-card-check-put.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/validation/card/requests/{requestUid}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "cardData": {
        "pan": "1111222233334444",
        "expiryDate": "12/34",
        "cvv2": "123",
        "holderName": "Super Man"
    },
    "tokenizationData": {
        "account": "cat_girl"
    }
}
~~~

<!-- 200 -->
~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "SUCCESS",
    "isValidCard": true,
    "threeDsStatus": "WITHOUT",
    "checkOperationDate": "2021-07-29T16:30:00+03:00",
    "cardInfo": {
        "issuingCountry": "RUS",
        "issuingBank": "Qiwi bank",
        "paymentSystem": "VISA",
        "fundingSource": "DEBIT",
        "paymentSystemProduct": "Platinum..."
    },
    "createdToken": {
        "token": "1a77343a-dd8a-11eb-ba80-0242ac130004",
        "name": "111122******4444",
        "expiredDate": "2034-12-01T00:00:00+03:00",
        "account": "cat_girl"
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

## Card verification status {#card-check-info}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-card-check-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/validation/card/requests/{requestUid}",
            "get",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/test-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "SUCCESS",
    "isValidCard": true,
    "threeDsStatus": "WITHOUT",
    "checkOperationDate": "2021-07-29T16:30:00+03:00",
    "cardInfo": {
        "issuingCountry": "RUS",
        "issuingBank": "Qiwi bank",
        "paymentSystem": "VISA",
        "fundingSource": "DEBIT",
        "paymentSystemProduct": "Platinum..."
    },
    "createdToken": {
        "token": "1a77343a-dd8a-11eb-ba80-0242ac130004",
        "name": "111122******4444",
        "expiredDate": "2034-12-01T00:00:00+03:00",
        "account": "cat_girl"
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

## Complete 3DS on card verification {#card-check-complete}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__complete_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-card-check-complete-post.json', function( data ) {
        window.requestUI(
          data,
          "api",
          "payin/v1/sites/{siteId}/validation/card/requests/{requestUid}/complete",
          "post",
          ['RequestBody', '200', '4xx', '5xx']
        )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
POST /partner/payin/v1/sites/test-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "pares": "eJzVWFevo9iyfu9fMZrzaM0QjWHk3tIiGptgooE3cgabYMKvv3jvTurTc3XOfbkaJMuL...."
}
~~~

<!-- 200 -->
~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "SUCCESS",
    "isValidCard": true,
    "threeDsStatus": "PASSED",
    "checkOperationDate": "2021-07-29T16:30:00+03:00",
    "cardInfo": {
        "issuingCountry": "RUS",
        "issuingBank": "Qiwi bank",
        "paymentSystem": "VISA",
        "fundingSource": "DEBIT",
        "paymentSystemProduct": "Platinum..."
    },
    "createdToken": {
        "token": "1a77343a-dd8a-11eb-ba80-0242ac130004",
        "name": "111122******4444",
        "expiredDate": "2034-12-01T00:00:00+03:00",
        "account": "cat_girl"
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

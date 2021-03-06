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
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {},
   "customFields": {
    "cf1": "Some data",
    "FROM_MERCHANT_CONTRACT_ID": "1234"
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
      "value": "CREATED",
      "changedDateTime": "2018-03-05T11:27:41"
    },
    "comment": "Text comment",
    "customFields": {
      "cf1": "Some data",
      "FROM_MERCHANT_CONTRACT_ID": "1234"
    },
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00",
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


## Invoice for QIWI Wallet payment {#invoice_qw_put}

<div id="bill_v1_bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../eui_jsons/payin-checkout-payment-qw-put.json', function( data ) {
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
      "value": "CREATED",
      "changedDateTime": "2018-03-05T11:27:41"
    },
    "comment": "Text comment",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00",
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

## Invoice status {#invoice_get}

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
            "value": "DECLINE",
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
            "value": "DECLINE",
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
  "customFields": {
    "cf1": "Some data",
    "FROM_MERCHANT_CONTRACT_ID": "1234"
  },
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
  "paymentCardInfo": {
    "issuingCountry": "810",
    "issuingBank": "QiwiBank",
    "paymentSystem": "VISA",
    "fundingSource": "CREDIT",
    "paymentSystemProduct": "P|Visa Gold"
  },
  "customFields" : {
    "cf1": "Some data",
    "FROM_MERCHANT_CONTRACT_ID": "1234"
  },
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

## Completing authentication {#payment_complete}

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
  }
}
~~~
-->

## Payment confirmation {#capture}


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

<!--
## Refund for QIWI Wallet payment {#refund_qw}


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
-->
<!-- Request body -->
<!--
~~~json
{
  "amount": {
    "value": 2.34,
    "currency": "RUB"
  }
}
~~~


-->

<!-- 200 -->
<!--
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
-->
<!-- 4xx -->
<!--
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

-->
<!-- 5xx -->
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

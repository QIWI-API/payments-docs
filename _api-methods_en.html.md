# Method Reference {#references}

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

## Marketplace registration {#split-boarding}

For split payments, proceed with the following steps to onboard your marketplace in our servie.

### Key issue {#split-api-keys}

> Request

~~~shell
curl --location --request POST 'https://kassa.qiwi.com/api/users/edge_login' \
--header 'Content-Type: application/json' \
--data-raw '{
"username": "test@test.com",
"password": "111111111111"
}'
~~~

> Response

~~~http
HTTP/1.1 200 OK
Content-Type: application/json
Cookie: TokenTail:11w-www1-wwww1111wwww; 

{
"username": "test@test.com",
"user_uid": "1111ffff-22ss-1111-aaaa-12222weeeeeq",
"status_code": "ACTIVE",
"token_head": "kassa-11ww11ee-11ww-1",
"grants": []
}
~~~

To authorize in the API for marketplaces, send POST request to the URL:

`https://kassa.qiwi.com/api/users/edge_login`

Send the parameters in the request body:

* `username` (string) — login for your profile. It is issued on the start of the onboarding;
* `password` (string) — specified password.

In the response, there will be Bearer-token separated into two parts, for future requests.

To produce Bearer-token, concatenate values from `token_head` field in the response and Cookie `TokenTail`. In the example you will get API key: 

`kassa-11ww11ee-11ww-111w-www1-wwww1111wwww`

Obtained token is valid for a month. After a month, you must re-authorize in API.

Possible response statuses:

* 401 — wrong login-password pair;
* 200 — ОК.

### Transfer data of the merchant {#split-register-merchant}

> Request example

~~~shell
curl --location --request POST 'https://kassa.qiwi.com/edge/kassa-api/api/requests/v2' \
--header 'accept: application/json' \
--header 'Authorization: Bearer token' \
--data-raw '{
   "partner_name":"test_split",
   "partner_type_code":"FIRM",
   "workflow_code":"RU",
   "request_site":"http://split7_prod.ru",
   "request_site_name":"split7_prod",
   "request_type_code":"PAYIN_SPLIT",
   "request_inn":112121212121,
   "extras":[
      {
         "extra_code":"MARKET_SITE_ID",
         "extra_value":"split7_prod_market"
      },
      {
         "extra_code":"MARKET_CMS",
         "extra_value":"2.00"
      },
      {
         "extra_code":"MERCHANT_CMS",
         "extra_value":"4.30"
      },
      {
         "extra_code":"KPP",
         "extra_value":"112233445"
      },
      {
         "extra_code":"OPER_ACCOUNT",
         "extra_value":1122334456666678900
      },
      {
         "extra_code":"BIC",
         "extra_value":112233445666
      },
      {
         "extra_code":"CORR_ACCOUNT",
         "extra_value":1122334456666678900
      },
      {
         "extra_code":"BANK_NAME",
         "extra_value":"Сбер"
      },
      {
         "extra_code":"CHIEF_NEW",
         "extra_value":"{\"surname\": \"Иванов\", \"name\": \"Иван\",\"patronymic\": \"Иванович\", \"phone\": 79022222222}"
      },
      {
         "extra_code":"CONFIDANT_NEW",
         "extra_value":"{\"surname\": \"Иванов\", \"name\": \"Иван\",\"patronymic\": \"Иванович\", \"phone\": 79022222222}"
      }
   ]
}'
~~~

Pass the set of data for the merchant. The set is not complete and will be expanded.

Send a POST request to URL:

`https://kassa.qiwi.com/edge/kassa-api/api/requests/v2`

The value of [the API token](#split-api-keys) is indicated in the HTTP header `Authorization`, which is formed as

`Bearer "Token"`.

JSON-body request parameters:

Parameter | Type | Description
----|----|-----
partner_name | string | The name (brand) of the final merchant, Latin letters
partner_type_code | string | By default, `FIRM`
workflow_code | string | By default, `RU`
request_site | string | The URL of the site where the goods will be purchased in favor of this merchant as part of the split payments
request_site_name | string | Site name, Latin letters
request_type_code | string | By default, `PAYIN_SPLIT` for registering merchant to split payments
request_inn | number | Merchant's TIN
extras | JSON-object | An object containing all the other merchant data, the set is not final. The set of extras is strictly defined.
extras.extra_code | string | The name of the extra containing merchant data
extras.extra_value | string | Extra value

**JSON-object extras**

extra_code | Type | Description
-----|-----|------
MARKET_SITE_ID | string | Marketplace ID, which will be used in split payments
MERCHANT_CMS | string | The commission in percentages for the merchant under the contract. Rounded to two digits after the comma. 
MARKET_CMS | string | The marketplace commission in percentages. Rounded to two digits after the comma. 
KPP	| number	| PPC for end-merchant legal entities
OPER_ACCOUNT	| number	| Merchant's account
BIC	| number	| Bank identifier (BIK) of the merchant
CORR_ACCOUNT|number|	Correspondent account of the merchant
BANK_NAME	|string	| Settlement bank name for the merchant
CHIEF_NEW	| object| Company CEO personal data
----|----|-----
surname | string | Surname
name | string | Name
patronymic | string | Patronymic
phone  |number | Phone number
----|----|-----
CONFIDANT_NEW	| object	| Confidant personal data (if any)
----|----|-----
surname |string |Surname
name |string|Name
patronymic |string|Patronymic
phone |number| Phone number

> Response

~~~json
{
"request_uid": "3635e988-a79e-44bd-b6c0-50eb793b4acf",
"partner_uid": "afcf86e0-f354-4e88-9f31-0193f8f182d6"
}
~~~

You get the following data in response:

* `request_uid` (string) — Uid of the created application to connect the merchant. In the future, it is used to transfer documents on application.
* `partner_uid` (string) — Uid of the registered merchant. This Uid should be used to obtain the processing identifiers you have created.

### Transfer of documents

Next, you need to pass the package of documents of the merchant. Each document is sent in a separate request.

Send POST request to the URL:

`https://kassa.qiwi.com/edge/kassa-api/api/files/upload`

Value of [API token](#split-api-keys) is present in `Authorization` header, which is constructed as `Bearer "Token"`.

> Request to transfer document

~~~shell
curl --location --request POST 'https://kassa.qiwi.com/edge/kassa-api/api/files/upload' \
--header 'Authorization: Bearer kassa-token \
--header 'Content-Type: multipart/form-data' \
--form 'file=@your_file; type=application/pdf' \
--form 'properties={"request_uid":"c852ae0bf","file_code":"SPLIT_STATEMENT"}; type=application/json'
~~~

Request parameters are sent as `formData`:

Parameter | Parameter type | Data type | Description
----|----|-----
fileValueObject | formData | object | JSON-object with description of the document file. It includes parameters:<br/>`request_uid` — uid of the created application from [the previous response](#split-register-merchant) <br/>`file_code` — code of the document (see below the possible codes).
multipartFile | formData | file | Transferred document

Possible codes for the document:

* `SPLIT_STATEMENT` — Application for joining the offer, signed by the merchant and the marketplace;
* `CLIENT_QUESTIONNAIRE` — Merchant questionnaire (the questionnaire format is described in the ["Annex No.2 of the document"](https://static.qiwi.com/ru/doc/ishop/public-offer-ishop-new.pdf))

A list of codes will be extended in the future.

> Response example

~~~shell
"861d0207-54b3-4e3a-bcfb-6f379f329328"
~~~

You get uid of the added extra in response.

### Getting information about the progress of the merchant registration {#check-merchant-registration}

> Example of the current status of registration

~~~json
{
    "request_id": 17191,
    "request_site": "http://split7_prod.ru",
    "request_site_name": "split7_prod",
    "request_user_message": null,
    "request_manager_message": null,
    "request_inn": "112121212121",
    "request_qiwi_account": null,
    "request_type": {
        "request_type_code": "PAYIN_SPLIT",
        "request_type_name": "Подключение поставщиков по сплитам PayIn",
        "options": []
    },
    "request_status": {
        "request_status_code": "USER_WORK",
        "request_status_name": "Клиент заполняет"
    },
    "request_step": {
        "request_step_code": "PAYIN_SPLIT_START",
        "request_step_name": "Начальная заявка на подключение поставщика",
        "extras": [
            {
                "extra_code": "MARKET_SITE_ID",
                "extra_name": "Ид маркетплейса для сплитов",
                "extra_order": 100,
                "extra_is_unique": true,
                "extra_type_code": "STRING",
                "extra_values": [
                    {
                        "uid": "66ecc03c-dd16-426a-9975-a67d331dc5d0",
                        "value": "split7_prod_market"
                    }
                ]
            },
            {
                "extra_code": "MARKET_CMS",
                "extra_name": "Ставка маркетпейса для сплитов",
                "extra_order": 200,
                "extra_is_unique": true,
                "extra_type_code": "STRING",
                "extra_values": [
                    {
                        "uid": "7fc0a4bd-ee62-4157-8bd3-75d78a3617e1",
                        "value": "2.00"
                    }
                ]
            },
            {
                "extra_code": "MERCHANT_CMS",
                "extra_name": "Ставка мерчанта для сплитов",
                "extra_order": 300,
                "extra_is_unique": true,
                "extra_type_code": "STRING",
                "extra_values": [
                    {
                        "uid": "fd1b0dfa-b04d-43b1-b974-0f161ad76c21",
                        "value": "4.30"
                    }
                ]
            }
        ]
    },
    "request_flow": {
        "next_request_status_code": "USER_WORK",
        "back_request_status_code": null,
        "next_request_step_code": "PAYIN_SPLIT_DOCS",
        "back_request_step_code": null
    },
    "request_uid": "ac523fd1-ff43-46f7-9eea-295ddd22a403",
    "request_updated_dtime": 1611691165406,
     "merchant_site_external_ids": [
        "ereerertu-00"
    ],
    "merchant_uids": [
        "c9e6314a-f6dd-490d-b4c1-8ad659d3699e"
    ],
    "check_failures": []
}
~~~

To find out the status of the merchant registration, send a GET request to the URL:

`https://kassa.qiwi.comedge/kassa-api/api/requests?request_uid={application uid}`

You get the list of the registration data, the current step, and status of the application.

Field in response | Type | Description
-----|-----|------
request_id | number | Numerical ID of the application.<br/>This ID can be used to contact Support to clarify registration issues.
request_site | string | Site name that is transferred in [the request](#split-register-merchant)
request_site_name | string|	Merchant name that is sent in [the request](#split-register-merchant)
request_user_message |string|The field is not used
request_manager_message| string|	The field is not used
request_inn| string	| Merchant TIN that is sent in [the request](#split-register-merchant)
request_qiwi_account | string|	The field is not used
request_type| object	|Service data, description of the type of application.<br/>The following value is always returned<br/>`"request_type": {`<br/>`"request_type_code": "PAYIN_SPLIT",`<br/>`"request_type_name": "Подключение поставщиков`<br/>`по сплитам PayIn",`<br/>`"options": []`<br/>`}`
request_status | object	|JSON-object with information about status of the application.
-----|-----|------
request_status_code | string| Status code.<br/>Possible values:<br/>MANAGER_WORK — application pending<br/>REJECTED — application rejected<br/>ACCEPTED — application confirmed, merchant transferred to production environment
request_status_name | string |Russian name for status.
-----|-----|------
request_step | object	| The object that characterizes the current step of the application.
-----|-----|------
request_step_code |string|Service data
request_step_name | string|Service data
extras | object | List of previously transmitted data
-----|-----|------
extra_code | string | The name of the extras containing merchant data
extra_name | string |  Russian name of the extras containing merchant data
extra_order | number | Service data
extra_is_unique | bool | Service data
extra_type_code | string| Type of data for the extras
extra_values | array of objects| Extras value
-----|-----|------
uid | string | Internal ID to store this extra value
value | string | Transferred extras value
-----|-----|------
request_flow | object	| Service data 
request_uid | string	| Uid of the created application
request_updated_dtime | number	| Unix-time date of the last update of the application data and its status
merchant_site_external_ids | object	| An object containing data on the created `MerchantSiteId` in QIWI processing for the merchant. This ID does not come immediately, you need to repeat this request before receiving the ID, or REJECTED status.
merchant_uids | object	| Service data
check_failures | object	| The field is not used

To track the status of the application, check the value of the `request_status.request_status_code` field in response:

* `REJECTED` — The registration of the merchant is rejected. By `request_id` you can ask for more details in the Support.
* `ACCEPTED` — registration of the merchant was successful, it was transferred to production environment.
* `MANAGER_WORK` — merchant registration is underway, the processing ID of the merchant is in test mode.

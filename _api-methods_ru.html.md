# Справочник методов {#api-reference}

<aside class="notice">В запросы и ответы могут добавляться новые параметры. Следите за обновлениями на <a href="https://github.com/QIWI-API/payments-docs/blob/master/payments_ru.html.md">Github</a>.</aside>


## Создание счета {#invoice_put}

<div id="payin_v1_sites__siteId__bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
            "put",
            ['RequestBody', '200', '4xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
PUT /partner/payin/v1/sites/{siteId}/bills/893794793973 HTTP/1.1
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
}
~~~


## Создание счета для оплаты с QIWI Кошелька {#invoice_qw_put}

<div id="bill_v1_bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-qw-put.json', function( data ) {
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

## Статус счета {#invoice_get}

<div id="payin_v1_sites__siteId__bills__billId__get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
            "get",
            ['RequestBody', '200', '4xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
GET /partner/payin/v1/sites/{siteId}/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

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

## Платеж {#payments}

<div id="payin_v1_sites__siteId__payments__paymentId__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-payment-put.json', function( data ) {
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
  "billId": "string",
  "amount": {
    "currency": "RUB",
    "value": 200.00
  },
  "paymentMethod" : {
    "type" : "CARD",
    "pan" : "4444443616621049",
    "expiryDate" : "12/19",
    "cvv2" : "123",
    "holderName" : "CARDHOLDER NAME"
  },
  "callbackUrl": "https://example.com/callbacks",
  "comment": "Example payment",
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
  "customFields" : { },
  "flags" : [ ]
}
~~~

<!-- 4xx-->
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


## Статус платежа {#payment_get}

<div id="payin_v1_sites__siteId__payments__paymentId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-payment-get.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~

## Завершение аутентификации клиента {#payment_complete}


<div id="payin_v1_sites__siteId__payments__paymentId__complete_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-complete-post.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

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

## Подтверждение покупки {#capture}


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
      $.getJSON('../../rui_jsons/payin-capture-put.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~

## Статус подтверждения {#capture_get}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-capture-get.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

<!--
### Статус подтверждений

<div id="v1_sites__siteUid__payments__paymentId__captures_get">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-captures-get.json', function( data ) {
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

## Операция возврата {#refund-api}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refund-put.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~

<!--
## Возврат по платежу с QIWI Кошелька {#invoice_refund_qw}

<div id="bill_v1_bills__billId__refunds__refundId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-refund-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "bill/v1/bills/{billId}/refunds/{refundId}",
            "put",
            ['RequestBody', '200', '4xx']
          )
      })
    });
  </script>
</div>
-->
<!-- Request body -->
<!--
~~~http
PUT /partner/bill/v1/bills/89123232/refunds/tcwv3132 HTTP/1.1
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
    "refundId": "tcwv3132",
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

## Статус возврата {#refund-api-status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refund-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}",
            "get",
            ['RequestBody', '200', '4xx']
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

## Статус возвратов {#refunds-api-status}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds_get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refunds-get.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

## Регистрация маркетплейсов {#split-boarding}

Чтобы подключить сплитование платежей, выполните онбординг маркетплейса в нашем сервисе.

### Выпуск ключа {#split-api-keys}

~~~shell
curl --location --request POST 'https://kassa.qiwi.com/api/users/edge_login' \
--header 'Content-Type: application/json' \
--data-raw '{
"username": "test@test.com",
"password": "111111111111"
}'
~~~

> Ответ

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

Чтобы авторизоваться в API для маркетплейсов, отправьте POST-запрос на URL:

`/api/users/edge_login`

В теле запроса укажите параметры:

* `username` (string) — логин к вашему ЛК. Данный логин появляется в процессе онбординга маркетплейса;
* `password` (string) — заданный пользователем пароль к ЛК.

В ответе придут части Bearer-токена для последующих запросов.

Чтобы сформировать Bearer-токен, выполните конкатенацию значений в `token_head` и Cookie `TokenTail`. В примере ответа получится такой ключ API: 

`kassa-11ww11ee-11ww-111w-www1-wwww1111wwww`

Токен действителен 1 месяц. По истечении месяца необходимо повторно выполнить авторизацию в API.

Возможные статусы ответов:

* 401 — указана неверная пара логин-пароль;
* 200 — ОК.

### Передача данных по конечному поставщику {#split-register-merchant}

> Пример запроса

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


Передайте набор данных по конечному поставщику. Набор данных не полный и будет расширяться.

Отправьте POST-запрос на URL:

`edge/kassa-api/api/requests/v2`

Значение [токена API](#split-api-keys) указывается в заголовке `Authorization`, который формируется как `Bearer "Token"`.

Параметры JSON-тела запроса:

Параметр | Тип | Описание
----|----|-----
partner_name | string | Название конечного мерчанта/поставщика на латинице (бренд)
partner_type_code | string | Значение по умолчанию `FIRM`
workflow_code | string | Значение по умолчанию `RU`
request_site | string | URL сайта, на котором будет производиться покупка товаров в пользу этого поставщика в рамках сплитования
request_site_name | string | Название сайта на латинице
request_type_code | string | Значение по умолчанию `PAYIN_SPLIT` для подключения поставщика на сплитование
request_inn | number | ИНН поставщика
extras | json-object | Объект, содержащий все остальные данные по поставщику, будет расширяться. Набор экстр строго определен.
extras.extra_code | string | Название экстры, содержащей данные по поставщику
extras.extra_value | string | Значение экстры

**Элементы объекта extras**

extra_code | Тип | Описание
-----|-----|------
MARKET_SITE_ID | string | Идентификатор маркетплейса, в рамках которого будет производиться сплитование
MERCHANT_CMS | string | Комиссия в процентах с конечного поставщика по договору. Округленная до двух знаков после запятой. 
MARKET_CMS | string | Комиссия маркетплейса в процентах, по договору. Округленная до двух знаков после запятой. 
KPP	| number	| КПП (для конечных поставщиков-юридических лиц)
OPER_ACCOUNT	| number	| Расчетный счет конечного мерчанта
BIC	| number	| БИК банка конечного мерчанта
CORR_ACCOUNT|number|	Корреспондентский счет конечного мерчанта
BANK_NAME	|string	|Название банка конечного мерчанта
CHIEF_NEW	| object| Данные о руководителе компании
----|----|-----
surname | string |
name | string |
patronymic | string |
phone  |number |
----|----|-----
CONFIDANT_NEW	| object	| Данные о доверенном лице (если есть)
----|----|-----
surname |string |
name |string|
patronymic |string|
phone |number|

> Пример ответа

~~~json
{
"request_uid": "3635e988-a79e-44bd-b6c0-50eb793b4acf",
"partner_uid": "afcf86e0-f354-4e88-9f31-0193f8f182d6"
}
~~~

В ответ вы получите сообщение со следующими данными:

* `request_uid` (string) — Uid созданной заявки на подключение конечного поставщика. В дальнейшем используется для передачи документов по заявке.
* `partner_uid` (string) — Uid созданного конечного поставщика. Данный Uid необходимо использовать для получения созданных процессинговых идентификаторов.

### Передача документов

Далее необходимо передать пакет документов по подключаемому поставщику. Каждый документ отправляется отдельным запросом.

Отправьте POST-запрос на URL

`/edge/kassa-api/api/files/upload`

Значение [токена API](#split-api-keys) указывается в заголовке `Authorization`, который формируется как `Bearer "Token"`.

~~~shell
curl --location --request POST 'https://kassa.qiwi.com/edge/kassa-api/api/files/upload' \
--header 'Authorization: Bearer kassa-token \
--header 'Content-Type: multipart/form-data' \
--form 'file=@your_file; type=application/pdf' \
--form 'properties={"request_uid":"c852ae0bf","file_code":"SPLIT_STATEMENT"}; type=application/json'
~~~

Параметры запроса передаются как `formData`:

Параметр | Тип параметра | Тип данных | Описание
----|----|-----
fileValueObject | formData | object | Объект, содержащий описание файла, принимает параметры:<br/>request_uid — uid заявки из предыдущего запроса<br/>file_code — код передаваемого документа (см. описание ниже). Набор передаваемых кодов также будет расширяться.
multipartFile | formData | file | Передаваемый документ

Возможные значения кода документа:

* `SPLIT_STATEMENT` — Заявление о присоединении к оферте, подписанное  конечным поставщиком и маркетплейсом;
* `CLIENT_QUESTIONNAIRE` — Анкета конечного поставщика (формат анкеты описан в [Приложении №2 документа](https://static.qiwi.com/ru/doc/ishop/public-offer-ishop-new.pdf))

> Пример ответа

~~~shell
"861d0207-54b3-4e3a-bcfb-6f379f329328"
~~~

В ответе вы получите uid добавленной экстры.

### Получение информации о ходе подключения конечного поставщика

> Пример ответа

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

Чтобы узнать статус подключения конечного поставщика, отправьте GET-запрос на URL

`edge/kassa-api/api/requests?request_uid={uid созданной заявки}`

В ответ вы получите перечень отправленных данных, а также шаг и статус заявки на подключение.

Поле ответа | Тип | Описание
-----|-----|------
request_id | number | Численная ID заявки на подключение. Данная ID может быть использована для обращения в Службу поддержки для уточнения вопросов по подключению.
request_site | string | Переданный в [запросе](#split-register-merchant) сайт
request_site_name | string|	Переданное в [запросе](#split-register-merchant) название конечного поставщика
request_user_message |string|	Поле не используется
request_manager_message| string|	Поле не используется
request_inn| string	| Переданный в [запросе](#split-register-merchant) ИНН конечного поставщика
request_qiwi_account | string|	Поле не используется
request_type| object	|Служебные данные, описание типа заявки на подключение. Всегда возвращается значение<br/>"request_type": {<br/>"request_type_code": "PAYIN_SPLIT",<br/>"request_type_name": "Подключение поставщиков по сплитам PayIn",<br/>"options": []<br/>}
request_status | object	|Объект, характеризующий статус заявки на подключение.
-----|-----|------
request_status_code | string| Код статуса.<br/>Возможные коды статуса:<br/>MANAGER_WORK — заявка на рассмотрении<br/>REJECTED — заявка отклонена<br/>ACCEPTED — заявка подтверждена, конечный мерчант переведен в производственную среду
request_status_name | string |Русское название для статуса.
-----|-----|------
request_step | object	| Объект, характеризующий шаг заявки на подключение.
-----|-----|------
request_step_code |string|Служебные данные
request_step_name | string| Служебные данные
extras | object | Список переданных ранее данных
-----|-----|------
extra_code | string | Название экстры, содержащей данные по поставщику
extra_name | string |  Русское название экстры, содержащей данные по поставщику
extra_order | number | Служебные данные
extra_is_unique | bool | Служебные данные
extra_type_code | string| Тип данных экстры
extra_values | array of objects| Значение экстры
-----|-----|------
uid | string | Внутренний идентификатор для хранения данного значения экстры
value | string | пПреданное значение экстры
-----|-----|------
request_flow | object	| Служебные данные 
request_uid | string	| Uid созданной заявки заявки на подключение
request_updated_dtime | number	| Unix-time даты последнего обновления данных по заявке и ее статуса
merchant_site_external_ids | object	| Объект, содержащий данные о созданных процессинговых MerchantSiteId для конечного поставщика. Данный ID приходит не сразу, необходимо повторять данный запрос до получения ид, либо статуса REJECTED.
merchant_uids | object	| Служебные данные.
check_failures | object	| Не используется

Для отслеживания статуса заявки проверьте значение поля `request_status.request_status_code` в ответе:

* `REJECTED` — регистрация мерчанта отклонена. По номеру request_id вы можете обратиться в поддержку за уточнением подробностей.
* `ACCEPTED` — регистрация мерчанта прошла успешно, он переведен в боевой режим. 
* `MANAGER_WORK` — идет регистрация мерчанта, процессинговый идентификатор поставщика находится в тестовом режиме.

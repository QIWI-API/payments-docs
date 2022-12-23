# Справочник методов API {#api-reference}

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
PUT /partner/payin/v1/sites/site-01/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

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
   "expirationDateTime": "2022-04-13T14:30:00+03:00",
   "customer": {},
   "customFields": {
     "cf1": "Some data"
   }
}
~~~

<!-- 200 -->
~~~json
{
    "siteId": "23044",
    "billId": "893794793973",
    "invoiceUid": "d875277b-6f0f-445d-8a83-f62c7c07be77",
    "amount": {
      "value": 100.00,
      "currency": "RUB"
    },
    "status": {
      "value": "CREATED",
      "changedDateTime": "2022-04-05T11:27:41"
    },
    "comment": "Text comment",
    "customFields": {
      "cf1": "Some data"
    },
    "creationDateTime": "2022-03-05T11:27:41",
    "expirationDateTime": "2022-04-13T14:30:00",
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
  "dateTime" : "2022-03-05T11:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

## Статус счета {#invoice-details}

<div id="payin_v1_sites__siteId__bills__billId__details_get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-details-get.json', function( data ) {
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
GET /partner/payin/v1/sites/site-01/bills/d35cf63943e54f50badc75f49a5aac7c/details HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
{
  "billId": "d35cf63943e54f50badc75f49a5aac7c",
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "status": {
    "value": "PAID",
    "changedDateTime": "2022-03-05T11:27:41"
  },
  "comment": "Text comment",
  "customFields": {
    "cf1": "Some data"
  },
  "expirationDateTime": "2022-04-13T14:30:00",
  "payUrl": "https://oplata.qiwi.com/form/invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77",
  "payments": [
    {
        "siteId": "site-01",
        "billId": "d35cf63943e54f50badc75f49a5aac7c",
        "createdDateTime": "2022-03-05T11:23:22+03:00",
        "amount": {
            "currency": "RUB",
            "value": 100.00
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": 100.00
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": 0.00
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "427638******1410"
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
            "changedDateTime": "2022-03-05T11:23:54+03:00",
            "reason": "ACQUIRING_NOT_PERMITTED"
        },
        "customFields": {
            "customer_account": "1",
            "customer_phone": "0"
        }
    },
    {
        "siteId": "site-01",
        "billId": "d35cf63943e54f50badc75f49a5aac7c",
        "createdDateTime": "2022-03-05T11:26:21+03:00",
        "amount": {
            "currency": "RUB",
            "value": 100.00
        },
        "capturedAmount": {
            "currency": "RUB",
            "value": 100.00
        },
        "refundedAmount": {
            "currency": "RUB",
            "value": 0.00
        },
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "427638******1410"
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
            "changedDateTime": "2022-03-05T11:34:43+03:00"
        },
        "customFields": {
            "customer_account": "1",
            "customer_phone": "0"
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
  "dateTime" : "2022-03-05T16:49:59.166+03:00",
  "traceId" : "fd0e2a08c63ace83"
}
~~~

## Получение списка платежей по счету {#invoice-payments}

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
GET /partner/payin/v1/sites/site-01/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

<!-- 200 -->
~~~json
[
  {
    "paymentId": "824c7744-1650-4836-abaa-842ca7ca8a74",
    "billId": "191616216126154",
    "createdDateTime": "2022-07-27T12:43:35+03:00",
    "amount": {
        "currency": "RUB",
        "value": "1.00"
    },
    "capturedAmount": {
        "currency": "RUB",
        "value": "0.00"
    },
    "refundedAmount": {
        "currency": "RUB",
        "value": "0.00"
    },
    "paymentMethod": {
        "type": "CARD",
        "maskedPan": "561251******6871",
        "rrn": "002612398011",
        "authCode": "067842"
    },
    "createdToken": {
        "token": "cc2451a5-2fdd-4685-912e-8671597948a3",
        "name": "561251******6871",
        "expiredDate": "2030-03-01T00:00:00+03:00"
    },
    "customer": {
        "account": "11235813",
        "email": "darta@mail.ru",
        "phone": "79850223243"
    },
    "status": {
        "value": "COMPLETED",
        "changedDateTime": "2022-07-27T12:43:47+03:00"
    },
    "callbackUrl": "https://qiwi.com",
    "comment": "test",
    "customFields": {
        "customer_email": "darta@mail.ru",
        "customer_account": "11235813",
        "customer_phone": "79850223243",
        "cf1": "1",
        "cf2": "dva",
        "cf3": "tri",
        "cf4": "4",
        "cf5": "5",
        "BIND_PAYMENT_TOKEN": "true",
        "themeCode": "customization_OK",
    },
    "paymentCardInfo": {
        "issuingCountry": "643",
        "issuingBank": "Тинькофф банк",
        "paymentSystem": "MASTERCARD",
        "fundingSource": "UNKNOWN",
        "paymentSystemProduct": "TNW|TNW|Mastercard® NewWorld—ImmediateDebit|TNW|Mastercard New World-ImmediateDebit"
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
    "cf1": "Some data"
  },
  "flags" : [ ]
}
~~~

<!-- 4xx-->
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
    "maskedPan" : "444444******1049",
    "rrn": "124",
    "authCode": "182817",
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

## Подтверждение платежа {#capture}

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

## Получение QR-кода СБП {#qr-code-sbp}

### Метод PUT {#qr-code-sbp-put}

<div id="payin_v1_sites__siteId__sbp_qrCodes__qrCodeUid__put_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-sbp-put.json', function( data ) {
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
    "value": 1.00,
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

### Метод POST {#qr-code-sbp-post}

<div id="payin_v1_sites__siteId__sbp_qrCodes_post_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-sbp-post.json', function( data ) {
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
    "value": 1.00,
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

## Статус QR-кода СБП {#qr-code-sbp-get}

<div id="payin_v1_sites__siteId__sbp_qrCodes__qrCodeUid__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-sbp-get.json', function( data ) {
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

## Платеж токеном СБП {#payment-sbp-token}

<aside class="warning">
Метод находится в разработке.
</aside>

<div id="payin_v1_sites__siteId__sbp_qrCodes__qrCodeUid__payments__paymentId__put_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-sbp-token-put.json', function( data ) {
          window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/sbp/qrCodes/{qrCodeUid}/payments/{paymentId}",
            "put",
            ['RequestBody', '200', '4xx', '5xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~http
PUT /partner/payin/v1/sites/test-01/sbp/qrCodes/adghj17d1g8/payments/11212334csd HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "tokenizationAccount": "customer123",
  "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6"
}
~~~

<!-- 200 -->
~~~json
{
  "qrCodeUid": "adghj17d1g8",
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "paymentPurpose": "Flower for my girlfriend",
  "redirectUrl": "http://someurl.com",
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
    "status": "CREATED",
    "payload": "",
    "image": {
      "content": "Base64 string",
      "mediaType": "image/png",
      "width": 300,
      "height": 300
    }
  },
  "payment": {
    "paymentUid": "12s1s21",
    "paymentStatus": "WAITING",
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

## Отмена возврата {#decline-refund-api}

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__decline_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refund-decline.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}/decline",
            "post",
            ['RequestBody', '200', '4xx']
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

## Проверка карты {#card-check-api}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__put_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-card-check-put.json', function( data ) {
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

## Статус проверки карты {#card-check-info}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-card-check-get.json', function( data ) {
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

## Завершение аутентификации при проверке карты {#card-check-complete}

<div id="payin_v1_sites__siteId__validation_card_requests__requestUid__complete_post_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-card-check-complete-post.json', function( data ) {
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

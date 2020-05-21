---
title: Протокол онлайн платежей

metatitle: Протокол онлайн платежей

metadescription: Протокол онлайн платежей позволяет начать приём безопасных платежей с банковских карт от клиентов.

language_tabs:
  - json: Примеры

toc_footers:
  - <a href='/'>На главную</a>
  - <a href='mailto:bss@qiwi.com'>Обратная связь</a>

includes:

search: true
---


 *[3DS]: 3-D Secure - протокол защиты, используемый для аутентификации держателя банковской карты во время совершения платежной операции посредством сети Интернет.
 *[API]: Application Programming Interface -  набор готовых методов, предоставляемых приложением системой для использования во внешних программных продуктах.
 *[ТСП]: Торгово-сервисное предприятие
 *[MPI]: Merchant Plug-In - модуль системы выполняющий 3DS аутентификацию.
 *[PCI DSS]: Payment Card Industry Data Security Standard -  стандарт безопасности данных индустрии платёжных карт, учреждённым международными платёжными системами Visa, MasterCard, American Express, JCB и Discover.
 *[REST]: Representational State Transfer -  архитектурный стиль взаимодействия компонентов распределённого приложения в сети.
 *[JSON]: JavaScript Object Notation - текстовый формат обмена данными, основанный на JavaScript.
 *[Luhn]: Алгоритм Луна - алгоритм вычисления контрольной цифры номера пластиковой карты, предназначен для выявления ошибок, вызванных непреднамеренным искажением данных.
 *[МПС]: Международные платежные системы: Visa, MasterCard, МИР, AMEX, China UnionPay и т.д.
 *[HTTPS]: Расширение протокола HTTP для поддержки шифрования в целях повышения безопасности. Данные в протоколе HTTPS передаются поверх криптографических протоколов SSL или TLS. В отличие от HTTP с TCP-портом 80, для HTTPS по умолчанию используется TCP-порт 443.
 *[UUID]: это стандарт идентификации, представляет собой 16-байтный (128-битный) номер. Описан в *RFC4122*.



# Общая информация {#intro}

###### Последнее обновление: 2020-04-29 | [Редактировать на GitHub](https://github.com/QIWI-API/payments-docs/blob/master/payments_ru.html.md)


**Протокол онлайн платежей** позволяет начать быстро и безопасно принимать платежи с банковских карт.

Протокол представляет собой  полнофункциональное API для платежных операций. API использует REST-архитектуру. Параметры передаются методом PUT в теле запроса в формате JSON.

## Способы взаимодействия {#methods}
Протокол онлайн платежей позволяет использовать несколько вариантов взаимодействия:

* Checkout - платежная форма на стороне QIWI.
* API - полнофункциональное API для платежных операций.

<aside class="warning">
Выполнять запросы через API, в которых передаются полные номера карт, допускается только в том случае, если ТСП имеет PCI DSS сертификат, т.к. в этом случае принимает и обрабатывает на своей стороне карточные данные.
</aside>

### Доступные методы

Метод|API|Checkout
-----|---------|--------------
Одношаговая авторизация средств|+|+
Двухшаговая авторизация средств|+|+
Токенизация |+|+
Оплата токеном |+|-



## Начало работы {#start}

Для того, чтобы начать работу с протоколом, необходимо выполнить 3 простых шага.

**Шаг 1. Оставить заявку на подключение [b2b.qiwi.com](https://b2b.qiwi.com)**

После обработки заявки с вами свяжется менеджер, чтобы обсудить возможные варианты подключения, собрать необходимые документы и начать интеграцию.

<a name="auth"></a>

**Шаг 2. Получить доступ к личному кабинету**

При подключении к Протоколу онлайн платежей вы получите ваш id и доступ в личный кабинет. Доступ отправляется по email на указанный вами адрес. Более подробно с функциональностью можно ознакомиться в  [Личном кабинете](https://kassa.qiwi.com/service/core/merchants?).

**Шаг 3. Выпустить токен для взаимодействия**

Для использования API и отправки запросов используется параметр авторизации - Token. Значение параметра "Token" указывается в заголовке `Authorization`, который формируется как `Bearer "Token"`.

## Тестовый и боевой режим {#test_mode}

Все запросы в Протоколе онлайн платежей необходимо отправлять на URL:

`https://api.qiwi.com/partner{API_REQUESTS}`

Далее при описании всех запросов указывается только часть пути `{API_REQUESTS}`.

При подключении для вас создается id, которая находится в тестовом режиме. При этом вы можете проводить операции без списания средств с банковской карты. Подробнее о тестовом режиме можно узнать в разделе [Тестовые данные](#test_data).

На тестовой среде установлено ограничение на сумму и количество тестовых операций. По умолчанию максимальная сумма тестовых транзакций - 10 рублей. Максимум - 100 транзакций в сутки (учитываются операции за текущие сутки, время по МСК). Учитываются операции с суммой не более установленного лимита.

Когда интеграция на вашей стороне закончена, мы переводим id в боевой режим. В боевом режиме происходят реальные списания с карт.


# Checkout {#invoicing}


## Быстрый старт {#invoicing_quick_start}


### Выставите счет покупателю

Прежде всего, необходимо получить ссылку на платежную форму и перенаправить покупателя по этой ссылке.

<!-- Request body -->
>Пример запроса

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

Отправьте PUT-запрос на URL 

`/bill/v1/bills/{billId}`

с параметрами:

* **{billId}** - уникальный идентификатор счета в вашей системе
* **amount** - информация о сумме (`amount.value`) и валюте (`amount.currency`) счета
* **expirationDateTime** - дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна


[Подробнее](#invoice_put)

В ответ вы получите сообщение со следующими данными:


>Пример ответа

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
    "comment": "Test",
    "creationDateTime": "2018-03-05T11:27:41",
    "expirationDateTime": "2018-04-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
}
~~~


Параметр|Тип|Описание
--------|---|--------
billId|String| Уникальный идентификатор счета в вашей системе
siteId|String| Идентификатор вашего сайта в QIWI Кассе
amount|Object| Информация о сумме счета
amount.value|Number| Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String| Идентификатор валюты счета (Alpha-3 ISO 4217 код: `RUB`, `USD`, `EUR`)
status|Object | Информация о статусе счета
status.value	|String| Текущий [статус счета](#invoice_status)
status.changedDateTime|String|Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
customFields|Object| Дополнительные поля
customer|Object| Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`
comment|String| Комментарий к счету
creationDateTime |String| Системная дата создания счета. Формат даты:<br>`YYYY-MM-DDThh:mm:ss`
payUrl|String| Ссылка на созданную платежную форму
expirationDateTime|String| Срок действия созданной формы для оплаты. Формат даты:<br>`YYYY-MM-DDThh:mm:ss+\-hh:mm`

### Перенаправьте клиента на полученную в ответе ссылку

Для того, чтобы оплатить заказ, клиенту необходимо перейти по ссылке, указанной в поле `payUrl` в ответе. Перенаправьте клиента по ссылке и ожидайте получения уведомления об оплате.

К ссылке можно добавить следующий параметр:

<aside class="notice">
При открытии ссылки в webview на android обязательно включать <code>settings.setDomStorageEnabled(true)</code>
</aside>

> Пример ссылки

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://developer.qiwi.com/ru/payments/#introduction
~~~

| Параметр | Описание | Тип |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|
| successUrl | URL для переадресации в случае успешной оплаты. Переадресация произойдет после успешной 3DS аутентификации. Ссылку необходимо зашифровать в utf-8 формат. | URL-закодированная строка |

### Дождитесь уведомления об оплате

После того, как клиент оплатит выставленный вами счет, вам будет отправлены два [серверных уведомления](#callback). Первое уведомление (тип уведомления `PAYMENT`) - об успешно прошедшем платеже. Второе уведомление (тип уведомления `BILL`) - уведомление об оплате выставленного счета.




**Уведомление о проведении платежа**

>Пример уведомления о проведении платежа

~~~json
{
  "payment": {
    "paymentId":"9999999",
    "type":"PAYMENT",
    "createdDateTime":"2019-06-03T08:19:16+03:00",
    "status": {
      "value":"SUCCESS",
      "changedDateTime":"2019-06-03T08:19:16+03:00"
    },
    "amount": {
      "value":111.11,
      "currency":"RUB"},
    "paymentMethod": {
      "type":"CARD",
      "cardHolder":"CARD HOLDER",
      "cardExpireDate":"1/2025",
      "maskedPan":"411111******0001"},
    "gatewayData": {
      "type":"ACQUIRING",
      "authCode":"181218",
      "rrn":"123"
    },
    "customer": {},
    "billId":"f1e1a1f11ae111a11a111111e1111111",
    "flags": ["SALE"]
  },
  "type":"PAYMENT",
  "version":"1"
}
~~~

Параметр|Тип|Описание
--------|---|--------
paymentId|String| Уникальный идентификатор платежа в вашей системе
type|String| Тип операции `PAYMENT`
createdDateTime|String| Системная дата создания платежа. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
amount|Object| Информация о сумме платежа
amount.value|Number| Сумма платежа, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String| Валюта платежа (Alpha-3 ISO 4217 код: `RUB`, `USD`, `EUR`)
status|Object| Информация о статусе платежа
status.value	|String|Текущий [статус платежа](#payment_status)
status.changedDateTime|String|Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
paymentMethod|Object|Информация о средстве платежа
paymentMethod.type|String|Тип средства платежа. Возможные значения: `TOKEN`,`CARD`
paymentMethod.maskedPan|String| Маскированный PAN карты
paymentMethod.cardHolder|String| Имя владельца карты
paymentMethod.expityDate|String| Дата истечения действия срока карты
gatewayData|String|Данные платежного шлюза
gatewayData.type|String| Тип шлюза. Возможные значения: `ACQUIRING`
gatewayData.authcode|String|Код авторизации
gatewayData.rrn|String| Значение RRN (ISO 8583)
customFields|Object|Дополнительные поля
customer|Object|Идентификаторы пользователя. Возможные опции: `email`, `phone`, `account`, `ip`
billId|String|Идентификатор счета
flags|Array of strings| Флаги операции: `SALE` - одношаговый сценарий


>Пример уведомления об оплате счета

~~~json

{ 
  "bill":{  
     "siteId":"23044",
     "billId":"1519892138404fhr7i272a2",
     "amount":{  
        "value":100.00,
        "currency":"RUB"
     },
     "status":{  
        "value":"PAID",
        "datetime":"2018-03-01T11:16:12+03"
     },
     "customer":{},
     "customFields":{},
     "creationDateTime":"2018-03-01T11:15:39+03",
     "comment":"Test",
     "expirationDateTime":"2018-04-01T11:15:39+03:00+03"
   },
  "version":"1"
}
~~~

**Уведомление об оплате счета**

Параметр|Тип| Описание
--------|---|--------
billId|String| Уникальный идентификатор счета в вашей системе
siteId|String| Идентификатор вашего сайта в QIWI Кассе
amount|Object| Информация о сумме счета
amount.value|Number| Сумма счета, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String| Идентификатор валюты счета (Alpha-3 ISO 4217 код: `RUB`, `USD`, `EUR`)
status|Object| Информация о статусе счета
status.value	|String| Текущий [статус счета](#invoice_status)
status.changedDateTime|String| Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
customFields|Object| Дополнительные поля
comment|String| Комментарий к счету
creationDateTime|String| Системная дата создания счета. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`
expirationDateTime|String| Срок действия созданной формы для оплаты. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`




Подробнее о настройке серверных уведомлений, а также о различных типах уведомлений можно узнать в разделе [Серверные уведомления](#callback).

### Сделать возврат покупателю

Для того, чтобы сделать возврат по операциям, необходимо отправить PUT-запрос на возврат.

Запрос отправляется на URL

`/bill/v1/bills/{billId}/refunds/{refundId}`


>Пример запроса на возврат:

~~~http
PUT /partner/bill/v1/bills/893794793973/refunds/1 HTTP/1.1
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


Параметр|Тип|Описание
--------|---|--------
billId|String| Уникальный идентификатор счета
refundId|String|Уникальный идентификатор возврата в вашей системе
amount.value|Number|Сумма счета, округленная до 2 знаков после точки в меньшую сторону
amount.currency	|String| Идентификатор валюты счета (Alpha-3 ISO 4217 код)

[Подробнее](#invoice_refund)

## Дополнительно {#addon_invoicing}

### Двухшаговый сценарий {#two_step}


**Как произвести холдирование средств**

>Пример запроса на холдирование:

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
   "customFields": {},
   "paymentFlags":["AUTH"]
}
~~~


Для этого необходимо добавить в запрос выставления счета дополнительный параметр: `"paymentFlags":["AUTH"]`.

Параметр|Тип|Описание
--------|--------|-------
billId|string|Уникальный идентификатор счета в вашей системе
amount | Object | Информация о сумме счета
amount.value| Number(6.2)| Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков
amount.currency| String |  Идентификатор валюты счета (Alpha-3 ISO 4217 код:  `RUB`, `USD`, `EUR`)
expirationDateTime|URL-закодированная строка<br>`YYYY-MM-DDThh:mm:ss±hh`| Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна
paymentFlags| Array of strings|Дополнительные платежные флаги <br>Доступные значения: "AUTH" - выполнить двухшаговый сценарий авторизации средств

>Пример ответа:

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

В параметре `payUrl` ответа вы получите ссылку на платежную форму.

**Узнать id транзакции для подтверждения**

>Пример уведомления

~~~json
{
  "payment":{
    "paymentId":"804900",  <==paymentId, необходимый для capture
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

После того, как платеж успешно совершен,  вам придет [серверное уведомление](#payment_callback).

Используйте для подтверждения операции (`capture`) параметр `paymentId` из уведомления.

Также существует метод [Статус счета](#invoice_get). С его помощью можно узнать статус платежа и параметр `paymentId`.

**Подтвердить операцию**

Используя номер операции (`paymentId`), можно выполнить `capture` - подтверждение операции.

Выполните PUT-запрос c пустым телом на URL

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

где:

* **{siteId}** - уникальный ID ТСП, можно увидеть в [ответе](#hold) на запрос холдирования средств
* **{paymentId}** - ID операции, string, полученный в серверном уведомлении
* **{captureId}** - ID подтверждения, string, уникальный идентификатор на стороне ТСП, ТСП указывает его самостоятельно


[Подробнее](#invoice_capture)


## Запросы, Статусы и Ошибки {#invoicing_requests}

### Создание счета {#invoice_put}

<div id="bill_v1_bills__billId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "bill/v1/bills/{billId}",
            "put",
            ['RequestBody', '200', '4xx']
          )
      })
    });
  </script>
</div>

<!-- Request body -->
~~~ json
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


### Статус счета {#invoice_get}

<div id="payin_v1_sites__siteId__bills__billId__get_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-payment-get.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/bills/{billId}",
            "get",
            ['200', '4xx']
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

### Операция возврата {#invoice_refund}


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

### Операция подтверждения (для двухшагового сценария) {#invoice_capture}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__put_checkout">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-checkout-capture-put.json', function( data ) {
        window.requestUI(
            data,
            "checkout",
            "payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}",
            "put",
            ['RequestBody', '200', '4xx']
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


# API {#api}


## Быстрый старт {#api_quick_start}


>Пример запроса

~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
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

Для того, чтобы позволить вашему клиенту произвести оплату, необходимо создать платеж. Выполните следующие действия:

**1. Авторизуйте платеж**

Отправьте PUT-запрос на URL

`/payin/v1/sites/{siteId}/payments/{paymentId}`

с параметрами:


* **{siteId}**: уникальный ID ТСП
* **{paymentId}**: номер платежа, уникальный на стороне ТСП
* **paymentMethod**: описание платежных данных
* **amount**: сумма (`value`) и валюта (`currency`) платежа

[Подробнее](#payments)

В ответе вы получите следующие данные:

>Пример тела ответа с требованием 3DS

~~~json
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


Параметр|Тип|Описание
--------|---|--------
paymentId|String|Уникальный идентификатор платежа в вашей системе, такой же как в запросе
createdDateTime|String| Системная дата создания платежа. Формат даты:<br>`YYYY-MM-DDThh:mm:ss+hh:ss`
amount|Object|Информация о сумме платежа
amount.value|Number|Сумма платежа, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String|Валюта платежа (Alpha-3 ISO 4217 код)
status|Object|Информация о статусе платежа
status.value	|String|Текущий [статус платежа](#payment_status)
status.changedDateTime|String|Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`



Чаще всего для создания платежа вам понадобится пройти дополнительную аутентификацию. Такое может произойти, если:

* Для платежа обязателен CVV
* Необходимо произвести 3DS аутентификацию


Если есть необходимость в дополнительной аутентификации, в ответе вы получите дополнительный объект `requirements` c полями:

* `threeDS`: параметры `pareq` и `acsUrl` для перенаправления на страницу подтверждения от эмитента
* `cvv`: данные CVV



**2. (опционально) Завершите аутентификацию клиента**

~~~http
POST /partner/payin/v1/sites/test-01/payments/1811/complete HTTP/1.1
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

Отправьте POST-запрос на URL

`/payin/v1/sites/{siteId}/payments/{paymentId}/complete`

и укажите параметры:

* CVV
* Данные 3DS

[Подробнее](#payment_complete)

**3. Подтвердите платеж**

Используя номер операции (`paymentId`), можно произвести `capture` - подтверждение операции.

Отправьте PUT-запрос c пустым телом на URL

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

где:

* **{siteId}** - ваш ID
* **{paymentId}** - ID операции, string, заданный в запросе авторизации **1**
* **{captureId}** - ID подтверждения, string, уникальный идентификатор на стороне ТСП, ТСП указывает его самостоятельно

[Подробнее](#capture)

> Пример подтверждения

~~~shell
curl https://api.qiwi.com/partner/payin/v1/sites/test-01/payments/2820220333/captures/43234 \
-X PUT \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer NDQzNGHJK43JFJDK595FJFJMjlCRkFFRDM5OE' \
-d '{}'
~~~

<!--
**4. Произведите возврат**
-->



## Запросы, Статусы и Ошибки {#api_requests}

### Платеж {#payments}

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
~~~ json
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


### Статус платежа {#payment_get}

<div id="payin_v1_sites__siteId__payments__paymentId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-payment-get.json', function( data ) {
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

### Завершение аутентификации {#payment_complete}


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

### Подтверждение покупки {#capture}


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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~

### Статус подтверждения {#capture_get}

<div id="payin_v1_sites__siteId__payments__paymentId__captures__captureId__get_api">
  <script>
    $(document).ready(function(){
        $.getJSON('../../rui_jsons/payin-capture-get.json', function( data ) {
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

### Создание возврата {#refund}

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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"

}
~~~

### Статус возврата

<div id="payin_v1_sites__siteId__payments__paymentId__refunds__refundId__get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refund-get.json', function( data ) {
        window.requestUI(
            data,
            "api",
            "payin/v1/sites/{siteId}/payments/{paymentId}/refunds/{refundId}",
            "get",
            ['200', '4xx']
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

### Статус возвратов

<div id="payin_v1_sites__siteId__payments__paymentId__refunds_get_api">
  <script>
    $(document).ready(function(){
      $.getJSON('../../rui_jsons/payin-refunds-get.json', function( data ) {
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
  "serviceName":"payin-core",
  "errorCode":"internal.error",
  "userMessage":"Internal error",
  "description":"Internal error",
  "traceId":"3fb3420ee1795dcf",
  "dateTime":"2020-02-12T21:28:01.813+03:00"
}
~~~

### Причины отклонения {#reasons}

Причины отклонения передаются в ответах на запросы в поле `status.reason` и в поле `status.reasonCode` уведомления.

Причина отклонения|Описание
------------------|--------
INVALID_STATE| Некорректный статус транзакции
INVALID_AMOUNT| Некорректная сумма
DECLINED_BY_MPI | Отклонено MPI
DECLINED_BY_FRAUD| Отклонено fraud-мониторингом
GATEWAY_INTEGRATION_ERROR| Ошибка взаимодействия с банком
GATEWAY_TECHNICAL_ERROR| Техническая ошибка на стороне банка
ACQUIRING_MPI_TECH_ERROR| Техническая ошибка при проведение 3DS аутентификации
ACQUIRING_GATEWAY_TECH_ERROR| Техническая ошибка
ACQUIRING_ACQUIRER_ERROR| Техническая ошибка
ACQUIRING_AUTH_TECHNICAL_ERROR| Ошибка при проведении авторизации средств
ACQUIRING_ISSUER_NOT_AVAILABLE| Ошибка эмитента. Банк-эмитент не доступен
ACQUIRING_SUSPECTED_FRAUD| Ошибка эмитента. Подозрение на мошенничество
ACQUIRING_LIMIT_EXCEEDED| Ошибка эмитента. Превышен один из лимитов
ACQUIRING_NOT_PERMITTED| Ошибка эмитента. Операция не разрешена
ACQUIRING_INCORRECT_CVV| Ошибка эмитента. Некорректный CVV
ACQUIRING_EXPIRED_CARD| Ошибка эмитента. Неверный срок действия карты
ACQUIRING_INVALID_CARD| Ошибка эмитента. Проверьте корректность введенных данных
ACQUIRING_INSUFFICIENT_FUNDS| Ошибка эмитента. Недостаточно средств
ACQUIRING_UNKNOWN| Неизвестная ошибка
BILL_ALREADY_PAID| Счет уже оплачен
PAYIN_PROCESSING_ERROR| Ошибка при проведении платежа



<!-- # Дополнительно -->

# Серверные уведомления {#callback}

Протокол поддерживает 4 типа уведомлений: `PAYMENT`, `CAPTURE`, `REFUND` и `BILL`.

Уведомление представляет собой входящий POST-запрос. Тело запроса содержит JSON-сериализованные данные платежа/счета (кодировка UTF-8).

<aside class="success">
Уведомление (callback) отправляется только по протоколу HTTPS и только на 443 порт.
Сертификат должен быть выдан доверенным центром сертификации (например Comodo, Verisign, Thawte и т.п.)
</aside>

В  уведомлении присутствует [подпись запроса](#notifications_auth), которую ТСП необходимо проверять на своей стороне для исключения возможности подделки уведомления.

Для дополнительной уверенности следует принимать уведомления о платежах только с указанных ниже IP-адресов компании QIWI.

* 79.142.16.0/20
* 195.189.100.0/22
* 91.232.230.0/23
* 91.213.51.0/24

Уведомление считается успешно доставленным, если сервер ТСП ответил HTTP кодом состояния `200 OK`.
Таким образом, до тех пор пока не будет ответа сервера с кодом состояния `200 OK`, система будет осуществлять попытки доставки уведомления через увеличивающиеся интервалы времени в течение суток с момента операции.

<aside class="notice">
Если по какой-либо причине сервер ТСП успешно принял уведомление, но ответил не `200 OK`, то при повторной попытке доставки он не должен воспринимать данную транзакцию как новую.
</aside>

<aside class="warning">
Вся ответственность за возможные финансовые потери, случившиеся вследствие отсутствия валидации <a href="#notifications_auth">подписи</a> в нелегитимном уведомлении, лежит полностью на ТСП.
</aside>


Адрес сервера для уведомлений указывается в Личном Кабинете на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> в разделе "Настройки". Можно также указать адрес сервера в опциональном параметре `callbackUrl` в запросе к [API](#api_requests).

## Авторизация уведомлений {#notifications_auth}

После получения входящего уведомления необходимо проверить подлинность данного уведомления. Для этого используется механизм цифровой подписи. 

Подпись уведомлений  `PAYMENT`, `CAPTURE`, `REFUND` отправляется в заголовке `Signature`. Подпись уведомлений `BILL` отправляется в заголовке `X-Api-Signature-SHA256`. Для заголовков используется кодировка UTF-8.

Для формирования подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256.

Алгоритм проверки подписи:

1. Объединить значения определенных параметров в одну строку с разделителем "\|". Например:

   `parameters = {amount.currency}|{amount.value}|{billId}|{siteId}|{status.value}`

   где `{*}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки. **В описании соответствующих уведомлений указаны параметры для расчета подписи.**


2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAС(SHA256, token, parameters)`
   Где:

   * `token` – ключ функции (UTF-8), который можно получить в Личном Кабинете на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> в разделе "Настройки";
   * `parameters` – строка из п.1 (UTF-8);

3. Сравнить значение подписи из уведомления с результатом из п.2.


## Уведомления PAYMENT, CAPTURE, REFUND {#payment_callback}


<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

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


Параметр|Описание|Тип
--------|--------|---
paymentId/refundId/captureId|Уникальный идентификатор платежа/возврата/подтверждения в системе ТСП|String(200)
type| Тип операции|String(200)
createdDatetime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
amount | Информация о сумме операции |Object
amount.value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
amount.currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)
billId| ID счета, соответствующего операции| String(200)
status | Информация о статусе операции | Object
status.value |Строковое значение статуса | String
status.changedDatetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
status.reasonCode| Код причины отклонения| String(200)
status.reasonMessage| Описание причины отклонения| String(200)
status.errorCode| Код ошибки| Number
paymentMethod| Информация о средстве платежа| Object
paymentMethod.type| Тип метода оплаты| String
paymentMethod.maskedPan| Маскированный PAN карты| String
paymentMethod.rrn| RRN платежа (по ISO 8583)| Number
paymentMethod.authCode| Auth-code платежа| Number
customer | Информация о пользователе, на которого был выставлен счет| Object
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе ТСП (если был указан при выставлении счета)|String
customer.ip| IP адрес пользователя |String
customer.country| страна адрес пользователя |String
flags| Дополнительные команды для API| Массив. Доступные значения - `SALE`/`REVERSAL`
version | Версия уведомлений | String


Подпись считается для следующих полей:


тип PAYMENT:
`payment.paymentId|payment.createdDateTime|payment.amount.value`

тип REFUND:
`refund.refundId|refund.createdDateTime|refund.amount.value`

тип CAPTURE:
`capture.captureId|capture.createdDateTime|capture.amount.value`




Сервис нотификаций перекладывает неуспешные уведомления по очередям:

* 2 попытки с отложенным временем по 5 секунд

* 2 попытки с отложенным временем по 1 минуте

* 2 попытки с отложенным временем по 5 минут

Время повторной отправки может немного сдвигаться в бОльшую сторону.



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


## Уведомления BILL {#bill_callback}

Данный тип уведомления отправляется только при использовании [Checkout](#invoicing). Нотификация будет отправлена, как только выставленный счет будет оплачен.


<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>X-Api-Signature-SHA256: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

 >Пример уведомления

~~~ json
{
   "bill":{
      "siteId":"Obuc-00",
      "billId":"testing122",
      "amount":{
         "value":2211.24,
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

Параметр|Описание|Тип
---------|--------|---
billId|Уникальный идентификатор платежа в системе ТСП|String(200)
siteId|Идентификатор сайта ТСП в QIWI Кассе| Number
amount|Информация о сумме счета|Object
amount.value | Сумма счета, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
amount.currency | Идентификатор валюты счета (Alpha-3 ISO 4217 код) | String(3)
status | Информация о статусе счета | Object
status.value |Строковое значение статуса | String
status.changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
customer | Информация о пользователе, на которого был выставлен счет| Object
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе ТСП (если был указан при выставлении счета)|String
creationDatetime | Дата создания счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
expirationDateTime | Срок оплаты счета | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
comment | Комментарий к счету | String(255)
customFields | Дополнительные данные счета (если были указаны при выставлении счета).| Object
version | Версия уведомлений | String

Подпись считается для следующих полей:

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

# Передача чека (54-ФЗ) {#cheque}

Чек передается в параметре `cheque` в операциях [выставления счета ](#invoicing_requests) и [платежа](#api_requests) при работе через Checkout и API, соответственно.


<aside class="notice">
Для активации сервиса приема фискальных данных необходимо сообщить менеджеру номер ИНН, с которым ваша организация зарегистрирована в сервисе по аренде онлайн касс.
Если используется Атол, то дополнительно необходимо предоставить:<ul>
<li>email ТСП для печати в чеке</li>
<li>полное название организации</li>
<li>реквизиты ОФД (название, ИНН, url)</li>
<li>login и password для генерации токена</li>
<li>код группы</li></ul>
</aside>

<!--<aside class="notice">
Пока аккаунт находится в тестовом режиме, чек так также будет проходить по тестовому контуру.
</aside>
-->


## Описание чека

~~~json
{
 .. 
"cheque" : {
	"sellerId" : 3123011520,
	"customerContact" : "Test customer contact",
  "chequeType" : "COLLECT",
	"taxSystem" : "OSN",
	"positions" : [ {
		"quantity" : 1,
		"price" : {
			"value" : 7.82,
			"currency" : "RUB"
		},
		"tax" : "NDS_0",
		"paymentSubject" : "PAYMENT",
		"paymentMethod" : "FULL_PAYMENT",
		"description" : "Test description"
	} ]
}
}
~~~

Параметр|Условие|Тип данных|Описание
--------|-------|----------|--------
sellerId|Обязательно|decimal|ИНН организации, для которой пробивается чек
chequeType|Обязательно|decimal|Признак расчета (тэг 1054):<br> `COLLECT` – Приход <br> `COLLECT_RETURN` - Возврат прихода <br> CONSUME - Расход <br> `CONSUME_RETURN` -Возврат расхода
customerContact|Обязательно|string(64)|Телефон или электронный адрес покупателя (тэг 1008)
taxSystem|Обязательно|decimal|Система налогообложения (тэг 1055):<br> `OSN` - Общая, ОСН <br>`USN` – Упрощенная доход, УСН доход <br>`USN_MINUS_CONSUM`  – Упрощенная доход минус расход, УСН доход - расход <br>`ENVD` – Единый налог на вмененный доход, ЕНВД <br>`ESN` - Единый сельскохозяйственный налог, ЕСН <br>`PATENT` – Патентная система налогообложения, Патент
positions|Обязательно|array|Массив товаров
--------|-------|----------|--------
quantity|Обязательно|decimal|Количество предмета расчета (тэг 1023)
price|Обязательно|decimal|Цена за единицу предмета расчета с учетом скидок и наценок (тэг 1079)
tax|Обязательно|decimal|Ставка НДС (тэг 1199):<br>`NDS_CALC_18_118` - с учетом НДС 18% (18/118) <br>`NDS_CALC_10_110` – с учетом НДС 10% (10/110) <br>`NDS_0` – ставка НДС 0% <br>`NO_NDS` – НДС не облагается<br>`NDS_CALC_20_120` – с учетом НДС 20% (20/120) (20/120)
description|Обязательно|string(128)|Наименование предмета расчета
paymentMethod|Обязательно|decimal|Признак способа расчёта (тэг 1214):<br>`ADVANCED_FULL_PAYMENT` – предоплата 100%. Полная предварительная оплата до момента передачи предмета расчета.<br>`PARTIAL_ADVANCE_PAYMENT`  – предоплата. Частичная предварительная оплата до момента передачи предмета расчета.<br>`ADVANCE` – аванс.<br>`FULL_PAYMENT` – полный расчет. Полная оплата, в том числе с учетом аванса (предварительной оплаты) в момент передачи предмета расчета.<br>`PARTIAL_PAYMENT` – частичный расчет и кредит. Частичная оплата предмета расчета в момент его передачи с последующей оплатой в кредит.<br>`CREDIT`  – передача в кредит. Передача предмета расчета без его оплаты в момент его передачи с последующей оплатой в кредит.<br>`CREDIT_PAYMENT` – оплата кредита. Оплата предмета расчета после его передачи с оплатой в кредит (оплата кредита).
paymentSubject|Обязательно|decimal|Признак предмета расчёта (тэг 1212):<br>`COMMODITY` – товар. О реализуемом товаре, за исключением подакцизного товара (наименование и иные сведения, описывающие товар).<br>`EXCISE_COMMODITY` – подакцизный товар. О реализуемом подакцизном товаре (наименование и иные сведения, описывающие товар).<br>`WORK` – работа. О выполняемой работе (наименование и иные сведения, описывающие работу).<br>`SERVICE` – услуга. Об оказываемой услуге (наименование и иные сведения, описывающие услугу).<br>`GAMBLING_RATE` – ставка азартной игры. О приеме ставок при осуществлении деятельности по проведению азартных игр.<br>`GAMBLING_PRIZE` – выигрыш азартной игры. О выплате денежных средств в виде выигрыша при осуществлении деятельности по проведению азартных игр.<br>`LOTTERY_TICKET` – лотерейный билет. О приеме денежных средств при реализации лотерейных билетов, электронных лотерейных билетов, приеме лотерейных ставок при осуществлении деятельности по проведению лотерей.<br>`LOTTERY_PRIZE` – выигрыш лотереи. О выплате денежных средств в виде выигрыша при осуществлении деятельности по проведению лотерей. <br>`GRANTING_RESULTS_OF_INTELLECTUAL_ACTIVITY` – предоставление результатов интеллектуальной деятельности. О предоставлении прав на использование результатов интеллектуальной деятельности или средств индивидуализации.<br>`PAYMENT` – платеж. Об авансе, задатке, предоплате, кредите, взносе в счет оплаты, пени, штрафе, вознаграждении, бонусе и ином аналогичном предмете расчета.<br>`AGENCY_FEE` – агентское вознаграждение. О вознаграждении пользователя, являющегося платежным агентом (субагентом), банковским платежным агентом (субагентом), комиссионером, поверенным или иным агентом.<br>`COMPAUND_PAYMENT_SUBJECT` – составной предмет расчета. О предмете расчета, состоящем из предметов, каждому из которых может быть присвоено значение выше перечисленных признаков.<br>`OTHER_PAYMENT_SUBJECT` – иной предмет расчета. О предмете расчета, не относящемуся к выше перечисленным предметам расчета.


# Токенизация {#token}

**Протокол онлайн платежей** поддерживает механизм токенизации карт. Токенизация карт позволяет сохранять карты пользователей в зашифрованном виде и в дальнейшем использовать их для безакцептных платежей.

По умолчанию токенизация карт отключена. Если Вам необходимо подключить данную функцию, обратитесь к своему сопровождающему менеджеру.

## Выпуск платежного токена {#token_issue}

>Пример тела запроса

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


Для того, чтобы выпустить платежный токен, необходимо в запросе на [создание платежа](#payments) указать:

* `flags: ["BIND_PAYMENT_TOKEN"]` - команда, указывающая системе на необходимость выпуска платежного токена
* `customer.account` - уникальный идентификатор Покупателя в системе ТСП

<aside class="warning">
Не указывайте один и тот же <code>account</code> для всех Ваших пользователей. Это может привести к тому, что пользователи смогут произвести оплату чужими картами.
</aside>

>Пример нотификации с токеном

~~~json
{
  "payment":
  {
    "paymentId":"9790769",
    "tokenData": {
      "paymentToken":"66aebf5f-098e-4e36-922a-a4107b349a96",
      "expiredDate":"2021-12-31T00:00:00+03:00"
    },
    "type":"PAYMENT",
    "createdDateTime":"2020-01-23T15:07:35+03:00",
    "status": {
      "value":"SUCCESS",
      "changedDateTime":"2020-01-23T15:07:36+03:00"
    },
    "amount": {
      "value":2211.24,
      "currency":"RUB"
    },
    "paymentMethod": {
      "type":"CARD",
      "maskedPan":"4111111111",
      "cardHolder":"CARD HOLDER",
      "cardExpireDate":"12/2021",
      "type":"CARD"
    },
    "customer": {
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

В ответ в [нотификации](#payment_callback) типа `payment` вы получите следующие данные:

Параметр|Тип данных|Описание
--------|-------|----------
tokenData|Object| Объект, содержащий данные о выпущенном токене карты
tokenData.paymentToken|String| Токен карты
tokenData.expiredDate|String| Дата окончания срока действия токена. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh:mm`

Полученные данные необходимо использовать при оплате.


## Оплата платежным токеном {#token_pay}

Произвести оплату с помощью выпущенного токена можно только через [API](#payments).

Для этого в объекте `paymentMethod` вместо платежных данных укажите параметры:

>Пример платежного запроса с токеном

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

Параметр|Тип|Описание
--------|---|--------
type|String|Тип операции. Необходимо указать `TOKEN`
paymentToken|String| Токен карты

Также не забудьте указать `account` - уникальный идентификатор пользователя, для которого был выпущен токен. Без данного параметра оплата с помощью токена невозможна.


# Выплаты

По умолчанию, выплаты по проведенным операциям производятся раз в 2 дня и  минимальным порогом 10.000 рублей. Если вам необходимо особое расписание, обратитесь к вашему курирующему менеджеру.

КИВИ взимает комиссию за каждую подтвержденную операцию. Если отмена операции была произведена до подтверждения, комиссия не взимается. Если была произведена частичная отмена до подтверждения операции, комиссия будет пересчитана.


# Тестовые данные {#test_data}

Для тестирования операций оплаты используются [боевые URL](#test_mode) сервисов оплаты. Для каждого нового ТСП в системе создаются `siteId` и по умолчанию включаются в режим тестирования. Также можно запросить переключение в режим тестирования любого своего `siteId`, либо добавление нового `siteId` в тестовом режиме через своего менеджера.

В тестовом режиме можно использовать любой номер карты, удовлетворяющий алгоритму Luhn.

<h4>Тестовые номера карт</h4>
<h4 id="visa">
</h4>
<h4 id="mc">
</h4>
<button class="button-popup" id="generate">Получить номера карт</button>

В режиме тестирования из валют (параметр `currency`) разрешен только рубль РФ (`643`).

CVV в режиме тестирования может быть любым (произвольные 3 цифры).

Для тестирования различных вариантов оплаты и ответов необходимо использовать различные сроки действия карты:

* Если месяц срока действия - `02`, то операция будет проведена неуспешно.
* Если месяц срока действия - `03`, то операция будет проведена успешно с задержкой в 3 секунды.
* Если месяц срока действия - `04`, то операция будет проведена неуспешно с задержкой в 3 секунды.
* Во всех остальных случая операция выполняется успешно.

На тестовой среде установлено ограничение на сумму и количество тестовых операций. По умолчанию максимальная сумма тестовых транзакций - 10 рублей. Максимум - 100 транзакций в сутки (учитываются операции за текущие сутки, время по МСК). Учитываются операции с суммой не более установленного лимита.

Для проведения операции с 3DS необходимо использовать строку `unknown name` в имени держателя карты. 3DS в тестовом режиме можно проверить только при вводе номера реальной карты.

# Коды ошибок {#errors}

Протокол онлайн платежей использует для запросов API следующие HTTP-коды ошибок:

Код ошибки | Описание
---------- | -------
400 | Bad Request -- Ваш запрос некорректен (ошибка данных, формата).
401 | Unauthorized -- Неправильный ключ авторизации API.
403 | Forbidden -- Доступ к API запрещен.
404 | Not Found -- Указанный ресурс не найден.
405 | Method Not Allowed -- Для создания платежа использовался неправильный метод.
406 | Not Acceptable -- Формат данных отличается от JSON.
410 | Gone -- Запрашиваемый ресурс удален.
429 | Too Many Requests -- Слишком много запросов.
500 | Internal Server Error -- Внутренняя ошибка сервиса.
503 | Service Unavailable -- Сервер временно недоступен по техническим причинам, попробуйте позже.

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

 *[Токен API]: Символьная строка для аутентификации мерчанта в API по стандарту OAuth 2.0 RFC 6749, RFC 6750.
 *[Платежный токен]: Символьная строка, созданная по данным карты для безакцептных платежей
 *[токен API]: Символьная строка для аутентификации мерчанта в API по стандарту OAuth 2.0 RFC 6749, RFC 6750.
 *[платежный токен]: Символьная строка, созданная по данным карты для безакцептных платежей
 *[3DS]: 3-D Secure - протокол защиты, используемый для аутентификации держателя банковской карты во время совершения платежной операции посредством сети Интернет.
 *[ТСП]: Торгово-сервисное предприятие
 *[MPI]: Merchant Plug-In - модуль системы, выполняющий 3DS аутентификацию.
 *[PCI DSS]: Payment Card Industry Data Security Standard -  стандарт безопасности данных индустрии платёжных карт, учреждённым международными платёжными системами Visa, MasterCard, American Express, JCB и Discover.
 *[REST]: Representational State Transfer -  архитектурный стиль взаимодействия компонентов распределённого приложения в сети.
 *[JSON]: JavaScript Object Notation - текстовый формат обмена данными, основанный на JavaScript.
 *[Luhn]: Алгоритм Луна - алгоритм вычисления контрольной цифры номера пластиковой карты, предназначен для выявления ошибок, вызванных непреднамеренным искажением данных.
 *[HTTPS]: Расширение протокола HTTP для поддержки шифрования в целях повышения безопасности. Данные в протоколе HTTPS передаются поверх криптографических протоколов SSL или TLS. В отличие от HTTP с TCP-портом 80, для HTTPS по умолчанию используется TCP-порт 443.


# Общая информация {#intro}

###### Последнее обновление: 2021-01-18 | [Редактировать на GitHub](https://github.com/QIWI-API/payments-docs/blob/master/payments_ru.html.md)


**Протокол онлайн платежей** позволяет быстро и безопасно принимать платежи с банковских карт.

Протокол предоставляет доступ как к платежной форме, так и к полнофункциональному API для платежных операций. API использует REST-архитектуру.

## Термины и сокращения в описании {#articles}

**Токен API**: Символьная строка для аутентификации мерчанта в API согласно стандарту OAuth 2.0 [[RFC 6749]](https://tools.ietf.org/html/rfc6749) [[RFC 6750]](https://tools.ietf.org/html/rfc6750)  
**Платежный токен**: Символьная строка, созданная по данным карты для безакцептных платежей  
**API**: Application Programming Interface -  набор готовых методов, предоставляемых приложением (системой) для использования во внешних  программных продуктах  
**REST**: Representational State Transfer -  архитектурный стиль взаимодействия компонентов распределённого приложения в сети.  
**JSON**: JavaScript Object Notation - текстовый формат обмена данными, основанный на JavaScript [[RFC 7159]](https://tools.ietf.org/html/rfc7159)  
**3DS**: 3-D Secure - протокол защиты, используемый для аутентификации держателя банковской карты во время совершения платежной операции посредством сети Интернет.  
**ТСП**: Торгово-сервисное предприятие  
**MPI**: Merchant Plug-In - модуль системы, выполняющий 3DS аутентификацию.  
**PCI DSS**: Payment Card Industry Data Security Standard -  стандарт безопасности данных индустрии платёжных карт, учреждённым международными платёжными системами Visa, MasterCard, American Express, JCB и Discover.

## Способы взаимодействия {#methods}

Протокол онлайн платежей позволяет использовать несколько вариантов взаимодействия:

* [Checkout](#invoicing) - платежная форма на стороне QIWI.
* [API](#api) - полнофункциональное API для платежных операций.

<aside class="warning">
Выполнять запросы через API, в которых передаются полные номера карт, допускается только в том случае, если ТСП имеет PCI DSS сертификат, т.к. в этом случае принимает и обрабатывает на своей стороне карточные данные.
</aside>

### Доступные методы

Метод|API|Checkout
-----|---------|--------------
Одношаговая авторизация средств|+|+
Двухшаговая авторизация средств|+|+
Выпуск платежного токена |+|+
Оплата через СБП |+|-
Оплата платежным токеном |+|-
Оплата с баланса КИВИ Кошелька | - |+


## Начало работы {#start}

Для того, чтобы начать работу с протоколом, необходимо выполнить 3 простых шага.

**Шаг 1. Оставить заявку на подключение [b2b.qiwi.com](https://b2b.qiwi.com)**

После обработки заявки с вами свяжется менеджер поддержки, чтобы обсудить возможные варианты подключения, собрать необходимые документы и начать интеграцию.

<a name="auth"></a>

**Шаг 2. Получить доступ к личному кабинету**

При подключении к Протоколу онлайн платежей вы получите ваш идентификатор и доступ в личный кабинет. Доступ отправляется по email на указанный вами адрес. Более подробно с функциональностью можно ознакомиться в  [Личном кабинете](https://kassa.qiwi.com/service/core/merchants?).

**Шаг 3. Выпустить токен API для взаимодействия**

Для взаимодействия с API используется токен API. Значение токена указывается в заголовке `Authorization`, который формируется как `Bearer "Token"`.

## Тестовый и производственный режим {#test_mode}

Все запросы в Протоколе онлайн платежей необходимо отправлять на URL:

`https://api.qiwi.com/partner{API_REQUESTS}`

При описании всех запросов указывается только часть пути `{API_REQUESTS}`.

При подключении для вас создается идентификатор `siteId`, который находится в тестовом режиме. При этом вы можете проводить операции без списания средств с банковской карты. Подробнее о тестовом режиме можно узнать в разделе [Тестовые данные](#test_data).

В тестовой среде установлено ограничение на сумму и количество тестовых операций. По умолчанию максимальная сумма тестовых транзакций - 10 рублей. Максимум - 100 транзакций в сутки (учитываются операции за текущие сутки, время по МСК). Учитываются операции с суммой не более установленного лимита.

Когда интеграция на вашей стороне закончена, мы переводим `siteId` в производственный режим. В производственном режиме выполняются реальные списания средств с карт.


# Checkout {#invoicing}


## Быстрый старт {#invoicing_quick_start}

Основной сценарий платежа включает в себя следующие шаги:

1. Холдирование средств на карте.
2. Подтверждение платежа.

<a name="hold"></a>

**1. Выполните холдирование средств**

>Пример запроса на холдирование:

~~~http
PUT /partner/payin/v1/sites/{siteId}/bills/893794793973 HTTP/1.1
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
   "customFields": {}
}
~~~


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
    "customFields": {},
    "comment": "Spasibo",
    "creationDateTime": "2019-08-28T16:26:36.835+03:00",
    "expirationDateTime": "2019-09-13T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=78d60ca9-7c99-481f-8e51-0100c9012087"
}
~~~

Отправьте PUT-запрос выставления счета на URL:

`/payin/v1/sites/{siteId}/bills/{billId}`

Параметры запроса:

* **{siteId}** - уникальный ID ТСП;
* **{billId}** - уникальный идентификатор счета в вашей системе

Обязательные параметры JSON-тела запроса:

Название|Тип|Описание
--------|--------|-------
amount | Object | Информация о сумме счета
amount.value| Number(6.2)| Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков
amount.currency| String |  Идентификатор валюты счета (Alpha-3 ISO 4217 код:  `RUB`, `USD`, `EUR`)
expirationDateTime|URL-закодированная строка<br>`YYYY-MM-DDThh:mm:ss±hh`| Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна

В поле `payUrl` ответа вы получите ссылку на платежную форму. Перенаправьте покупателя по этой ссылке.

[Подробнее о параметрах запроса](#invoice_put)

**1а. Получите id транзакции для подтверждения**

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
      "maskedPan":"444444XXXXXX4444",
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

После того, как платеж успешно совершен,  вам придет [серверное уведомление](#payment_callback). Подробно о настройке серверных уведомлений читайте в разделе [Серверные уведомления](#callback).

Для подтверждения операции (`capture`) возьмите параметр `paymentId` из уведомления.

Также статус платежа и параметр `paymentId` можно запросить методом [Статус счета](#invoice_get).

**2. Подтвердите операцию**

Используя номер операции (`paymentId`), отправьте PUT-запрос подтверждения операции `capture` c пустым телом на URL:

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

где:

* **{siteId}** - уникальный ID ТСП, содержится в ответе на запрос холдирования средств из [шага 1](#hold);
* **{paymentId}** - ID операции, полученный в серверном уведомлении;
* **{captureId}** - ID подтверждения, уникальный идентификатор на стороне ТСП (ТСП указывает его самостоятельно).


[Подробнее о параметрах запроса](#invoice_capture)

<!--
**Как сделать возврат покупателю**

Чтобы сделать возврат средств по операции, необходимо отправить PUT-запрос на возврат.

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

[Подробнее о возможных параметрах запроса](#invoice_refund)
-->

## Платежная форма {#payform}

### Ссылка на платежную форму {#http_oplata}

Чтобы оплатить заказ, клиенту необходимо перейти по ссылке, указанной в поле `payUrl` ответа на [запрос выставления счета](#invoice_put).

<aside class="notice">
При открытии ссылки в webview на android обязательно включать <code>settings.setDomStorageEnabled(true)</code>
</aside>

К ссылке можно добавить следующий параметр:

> Пример ссылки

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://developer.qiwi.com/ru/payments/#introduction
~~~

| Параметр | Описание | Тип |
|--------------|--------|-------------|
| successUrl | URL для переадресации в случае успешной оплаты. Переадресация произойдет после успешной 3DS аутентификации. Ссылку необходимо зашифровать в utf-8 формат. | URL-закодированная строка |

### Персонализация {#custom}

Персонализация  позволяет адаптировать платежную форму под ваш стиль. Настраивается лого, фон и цвет кнопок.

Создать стили можно обратившись в поддержку по адресу <a href='mailto:payin@qiwi.com'>payin@qiwi.com</a>. При настройке задается имя параметра (например, `кодСтиля`), привязанное к стилю. 

Для использования стиля на платежной форме необходимо передать в параметре `customFields` [запроса создания счета](#invoice_put) переменную с заданным именем параметра: `"themeCode":"кодСтиля"`.

 >Пример передачи параметра

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
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
   "customFields": {"themeCode":"кодСтиля"}
}
~~~

![Customer form](/images/Custom.png)

## Popup SDK

Всплывающее окно (popup) позволяет открыть форму оплаты для выставленного счета поверх вашего сайта. 

<button id="pop" class="button-popup" onclick="testPopup();">Пример работы popup</button>

Как использовать Popup SDK:

1. Выставить счет
2. Открыть счет с помощью Checkout Popup SDK


[Скачать библиотеку QIWI Checkout Popup](https://github.com/QIWI-API/bill-payments-popup-js-sdk)

Установка и подключение SDK:

`<script src='https://oplata.qiwi.com/popup/v1.js'></script>`

###  Открытие счета {#openpopup}

Метод  `QiwiCheckout.openInvoice`

>Пример открытия созданного счета в popup

~~~javascript
params = {
    payUrl: 'https://oplata.qiwi.com/form?invoiceUid=06df838c-0f86-4be3-aced-a950c244b5b1'
}

QiwiCheckout.openInvoice(params)
    .then(data => {
        // ...
    })
    .catch(error => {
        // ...
    })
~~~

| Параметр | Описание | Тип | Обязательное |
|--------------|--------------|-------------|--------------|
| payUrl | URL счета, указанная в поле `payUrl` ответа на [запрос выставления счета](#invoice_put) | String | + |

## Дополнительно {#addon_invoicing}

### Одношаговый сценарий {#one_step}

Исользуйте сценарий для проведения платежа без предварительного холдирования средств.

**1. Выставите счет покупателю**

Для выставления счета получите ссылку на платежную форму и перенаправьте покупателя по этой ссылке.

<!-- Request body -->
>Пример запроса

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 100.00
   },
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "flags": [
              "SALE"
			]
}
~~~

Отправьте PUT-запрос с дополнительным параметром:

`"flags":["SALE"]`

на URL:

`/payin/v1/sites/{siteId}/bills/{billId}`

Параметры URL запроса:

* **{siteId}** - уникальный ID ТСП;
* **{billId}** - уникальный идентификатор счета в вашей системе;

Обязательные параметры JSON-тела запроса:
 
* **amount** - информация о сумме (`amount.value`) и валюте (`amount.currency`) счета;
* **expirationDateTime** - дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, ему присваивается финальный статус `EXPIRED` и последующая оплата станет невозможна.
* **flags** - массив строк с единственным элементом "SALE".

[Подробнее о параметрах запроса](#invoice_put)

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


Поле ответа|Тип|Описание
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

[Перенаправьте](#http_oplata) покупателя по ссылке, указанной в поле `payUrl` ответа.

**2. Дождитесь уведомления об оплате**

После того, как клиент оплатит выставленный вами счет, вам будет отправлено серверное уведомление (тип уведомления `PAYMENT`) об успешно выполненном платеже. 


<!--
**2а. Уведомление о проведении платежа**
-->

>Пример тела уведомления о проведении платежа

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

Поле уведомления|Тип|Описание
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

<!--

>Пример тела уведомления об оплате счета
	
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

**2б. Уведомление об оплате счета**

Поле уведомления|Тип| Описание
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
-->

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

<a name="auth_api"></a>

**1. Авторизуйте платеж**

Отправьте PUT-запрос на URL:

`/payin/v1/sites/{siteId}/payments/{paymentId}`

с параметрами:

* **{siteId}**: уникальный ID ТСП;
* **{paymentId}**: номер платежа, уникальный на стороне ТСП;
* **paymentMethod**: описание платежных данных;
* **amount**: сумма (`value`) и валюта (`currency`) платежа.

[Подробнее о параметрах запроса](#payments)

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


Поле ответа|Тип|Описание
--------|---|--------
paymentId|String|Уникальный идентификатор платежа в вашей системе, такой же как в запросе
createdDateTime|String| Системная дата создания платежа. Формат даты:<br>`YYYY-MM-DDThh:mm:ss+hh:ss`
amount|Object|Информация о сумме платежа
amount.value|Number|Сумма платежа, округленная до 2 знаков после запятой в меньшую сторону
amount.currency	|String|Валюта платежа (Alpha-3 ISO 4217 код)
status|Object|Информация о статусе платежа
status.value	|String|Текущий [статус платежа](#payment_status)
status.changedDateTime|String|Дата обновления статуса. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh`


Чаще всего для создания платежа вам понадобится пройти дополнительную аутентификацию. Такое может произойти, если требуется 3DS аутентификация.


Если необходима дополнительная аутентификация клиента, в ответе будет присутствовать объект `requirements.threeDS` с параметрами `pareq` и `acsUrl` для перенаправления на страницу подтверждения от эмитента.


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
  }
}
~~~

Отправьте POST-запрос на URL:

`/payin/v1/sites/{siteId}/payments/{paymentId}/complete`

и укажите параметры:

* **{siteId}**: уникальный ID ТСП;
* **{paymentId}**: номер платежа из <a href="#auth_api">Шага 1</a>;
* данные 3DS.

[Подробнее о параметрах запроса](#payment_complete)

**3. Подтвердите платеж**

Используя номер операции (`paymentId`), выполните `capture` - подтверждение операции.

Отправьте PUT-запрос c пустым телом на URL:

`/payin/v1/sites/{siteId}/payments/{paymentId}/captures/{captureId}`

где:

* **{siteId}** - уникальный ID ТСП;
* **{paymentId}** - ID операции, заданный в запросе авторизации на <a href="#auth_api">Шаге 1</a>;
* **{captureId}** - ID подтверждения, уникальный идентификатор на стороне ТСП (ТСП указывает его самостоятельно).

[Подробнее о параметрах запроса](#capture)

> Пример подтверждения

~~~http
PUT /partner/payin/v1/sites/test-01/payments/2820220333/captures/43234 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

~~~

<!--
**4. Произведите возврат**
-->

# Серверные уведомления {#callback}

Уведомление представляет собой входящий POST-запрос с информацией о событии. Тело запроса содержит JSON-сериализованные данные платежа/счета (кодировка UTF-8). Формат уведомления зависит от его типа.

<aside class="success">
Уведомление отправляется только по протоколу HTTPS и только на порт 443.
Сертификат должен быть выдан доверенным центром сертификации (например Comodo, Verisign, Thawte и т.п.)
</aside>

Протокол поддерживает три типа уведомлений о событиях API: `PAYMENT`, `CAPTURE` и `REFUND`. Уведомления отправляются при совершении операций платежа, подтверждения платежа и возврата платежа, соответственно.<!-- Уведомление `BILL` отправляется только при использовании [Checkout](#invoicing), как только выставленный счет будет оплачен.-->

<aside class="notice">
Не установлено единого порядка отправки уведомлений разного типа в ходе выполнения одной и той же операции. Порядок может отличаться для разных операций.
</aside>

<aside class="notice">
Если уведомление об операции не поступило вам в течение 10 минут после совершения операции, необходимо запросить статус операции <a href="#invoice_get">запросом статуса счета</a> или <a href="#payment_get">запросом статуса платежа</a> (в зависимости от того, какой из <a href="#methods">способов взаимодействия</a> вы используете).
</aside>

Адрес сервера для обработки уведомлений указывается в Личном Кабинете на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> в разделе "Настройки". Можно указывать адрес сервера для каждой операции в опциональном параметре `callbackUrl` в запросах к [API](#api_requests).

Для дополнительной уверенности следует принимать уведомления о платежах только с указанных ниже IP-адресов компании QIWI:

* 79.142.16.0/20
* 195.189.100.0/22
* 91.232.230.0/23
* 91.213.51.0/24

Уведомление считается успешно доставленным, если сервер ТСП ответил HTTP кодом состояния `200 OK`.
Таким образом, до тех пор пока не будет ответа сервера с кодом состояния `200 OK`, система будет осуществлять попытки доставки уведомления через увеличивающиеся интервалы времени в течение суток с момента операции.

<aside class="success">
Если по какой-либо причине сервер ТСП успешно обработал уведомление по транзакции, но не вернул <code>200 OK</code>, то при повторной попытке доставки не нужно обрабатывать данную транзакцию как новую.
</aside>

## Авторизация уведомлений {#notifications_auth}

В уведомлении присутствует цифровая подпись запроса, которую ТСП необходимо проверять на своей стороне для исключения возможности подделки уведомления.

<aside class="warning">
Вся ответственность за возможные финансовые потери, случившиеся вследствие отсутствия валидации подписи в нелегитимном уведомлении, лежит полностью на ТСП.
</aside>

Цифровая подпись уведомления помещается в HTTP-заголовок `Signature`.

<!--
Тип уведомлений| HTTP-заголовок с подписью
----|-----
`PAYMENT`, `CAPTURE`, `REFUND` | `Signature`
`BILL` | `X-Api-Signature-SHA256`
-->

Для проверки подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256.

Алгоритм проверки подписи:

1. Объединить значения определенных параметров в одну строку с разделителем "\|". Например:

   `parameters = {payment.paymentId}|{payment.createdDateTime}|{payment.amount.value}`

   где `{*}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки.

   Подпись считается для следующих полей уведомления:

     * тип `PAYMENT`: `payment.paymentId|payment.createdDateTime|payment.amount.value`
     * тип `REFUND`: `refund.refundId|refund.createdDateTime|refund.amount.value`
     * тип `CAPTURE`: `capture.captureId|capture.createdDateTime|capture.amount.value`

2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAС(SHA256, token, parameters)`

   Где:

      * `token` – ключ функции (UTF-8), который можно получить в Личном Кабинете на сайте <a href="https://kassa.qiwi.com/">kassa.qiwi.com</a> в разделе **Настройки**;
      * `parameters` – строка из п.1 (UTF-8);

3. Сравнить значение подписи из  HTTP-заголовка `Signature` уведомления с результатом п.2.


## Уведомления PAYMENT, CAPTURE, REFUND {#payment_callback}

Тип уведомления указан в поле `type`.

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Пример уведомления

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: server.ru

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


Поле уведомления|Описание|Тип
--------|--------|---
paymentId/refundId/captureId|Уникальный идентификатор платежа/возврата/подтверждения в системе ТСП|String(200)
type| Тип уведомления|String(200)
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
paymentCardInfo | Информация о карте. **Приходит только в уведомлениях PAYMENT** | Object
paymentCardInfo.issuingCountry | Код страны эмитента | String(3)
paymentCardInfo.issuingBank | Банк-эмитент | String
paymentCardInfo.paymentSystem | Тип платежной системы | String
paymentCardInfo.fundingSource | Тип карты | String
paymentCardInfo.paymentSystemProduct | Категория карты | String
customer | Информация о пользователе, на которого был выставлен счет| Object
customer.phone |Номер телефона, на который был выставлен счет (если был указан при выставлении счета) |String
customer.email|E-mail пользователя (если был указан при выставлении счета)|String
customer.account| Идентификатор пользователя в системе ТСП (если был указан при выставлении счета)|String
customer.ip| IP адрес пользователя |String
customer.country| Страна адреса пользователя |String
customFields | Поля с произвольной информацией, дополняющей данные по операции | Object
customFields.cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
customFields.cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
customFields.cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
customFields.cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
customFields.cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
tokenData| Объект, содержащий данные о выпущенном [платежном токене](#token_issue)|Object
tokenData.paymentToken| Строка платежного токена для использования при оплате | String
tokenData.expiredDate | Дата окончания срока действия платежного токена. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh:mm` | String
flags| Дополнительные команды для API| Массив. Возможные значения - `SALE`/`REVERSAL`
version | Версия уведомлений | String




Сервис отправки уведомлений перекладывает неуспешные уведомления по очередям:

* 1 попытка с отложенным временем 5 секунд
* 1 попытка с отложенным временем 1 минута
* 3 попытки с отложенным временем по 5 минут

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

<!--
## Уведомления BILL {#bill_callback}

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

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
X-Api-Signature-SHA256: J4WNfNZd***V5mv2w=
Host: server.ru

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

Поле уведомления|Описание|Тип
---------|--------|---
billId|Уникальный идентификатор платежа в системе ТСП|String(200)
siteId|Идентификатор сайта ТСП в QIWI Кассе| Number
amount|Информация о сумме счета|Object
amount.value | Сумма счета, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
amount.currency | Идентификатор валюты счета (Alpha-3 ISO 4217 код) | String(3)
status | Информация о статусе счета | Object
status.value |Строковое значение статуса | String
status.changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
tokenData | Информация о платежном токене (если был запрошен выпуск платежного токена) | Object
tokenData.paymentToken | Строка платежного токена | String
tokenData.expiredDate | Срок действия платежного токена |  URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ` 
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

-->
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

Чек передается в необязательном JSON объекте `cheque` в операциях [выставления счета ](#invoice_put) и [платежа](#payments).


<aside class="notice">
Для активации сервиса приема фискальных данных необходимо сообщить вашему сопровождающему менеджеру номер ИНН, с которым ваша организация зарегистрирована в сервисе по аренде онлайн касс.
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


## Описание данных

~~~json
{
 .. 
"cheque":{
  "sellerId": 3123011520,
  "customerContact": "Test customer contact",
  "chequeType": "COLLECT",
  "taxSystem": "OSN",
  "positions": [
    {
      "quantity": 1,
      "price": {
        "value": 7.82,
        "currency": "RUB"
      },
      "tax": "NDS_0",
      "paymentSubject": "PAYMENT",
      "paymentMethod": "FULL_PAYMENT",
      "description": "Test description"
    }
  ]
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

# Сплитование платежей {#payments_split}

Сплитование платежей - решение, разработанное специально для маркетплейсов. Сплитование платежей позволяет рассчитываться с несколькими поставщиками услуг, производя одно списание с карты покупателя.

<aside class="notice">По умолчанию сплитование платежей отключено. Если вам необходимо подключить данную функцию, обратитесь к вашему сопровождающему менеджеру.</aside>

## Как использовать {#use-split}

В тело [платежного запроса](#payments) добавьте JSON-массив `paymentSplits`, используемый для передачи данных о поставщиках.

### Описание данных

~~~json
{
 "paymentMethod": "...",
 "customer": "....",
 "deviceData": "...",
 "paymentSplits": [
   {
     "type":"MERCHANT_DETAILS",
     "siteUid":"shop_mst-01",
     "splitAmount": {
       "value":300.00,
       "currency":"RUB"
     },
     "orderId":"dasdad444ll4ll",
     "comment":"flowers for my girlfriend"
  },
  {
    "type":"MERCHANT_DETAILS",
    "siteUid":"shop_mst-02",
    "splitAmount": {
      "value":200.00,
      "currency":"RUB"
      },
      "orderId":"sdadada887sdDDDDd",
      "comment":"watermelon"
    }
  ]
}
~~~

Описание массива `paymentSplits`:

Название | Тип | Описание
----|------|-------
paymentSplits | Array | Массив данных о поставщиках
-----|------|------
type | String | Тип передаваемых данных. Доступные значения - `MERCHANT_DETAILS` (данные поставщика)
siteUid | String | [ID поставщика](#start)
splitAmount | Object | Возмещение поставщику
----|----|-----
value | Number | Сумма возмещения
currency | String(3) | Буквенный код валюты возмещения по ISO. Доступен только `RUB`
-----|-----|-----
orderId | String | Номер заказа (необязательный)
comment | String | Комментарий к заказу (необязательный)

<aside class="warning">Сумма возмещений поставщикам должна равняться сумме списания с карты плательщика.</aside>

> Пример ответа

~~~json
{
  "paymentId": "23",
  "billId": "autogenerated-2a8fcfab-45cb-43b9-81bd-edf65e0ef874",
  "createdDateTime": "2020-10-12T15:29:12+03:00",
  "amount": {
    "currency": "RUB",
    "value": "100.00"
  },
  "capturedAmount": {
    "currency": "RUB",
    "value": "100.00"
  },
  "refundedAmount": {
    "currency": "RUB",
    "value": "0.00"
  },
  "paymentMethod": {
    "type": "CARD",
    "maskedPan": "40000******1111",
    "rrn": "001281453012",
    "authCode": "4EC4W7",
    "type": "CARD"
  },
  "status": {
    "value": "COMPLETED",
    "changedDateTime": "2020-10-12T15:29:14+03:00"
  },
  "paymentCardInfo": {
    "issuingCountry": "643",
    "issuingBank": "Альфа-банк",
    "paymentSystem": "MASTERCARD",
    "fundingSource": "PREPAID",
    "paymentSystemProduct": "Debit"
  },
  "paymentSplits": [
    {
      "type": "MERCHANT_DETAILS",
      "siteUid": "shop_mst-01",
      "splitAmount": {
        "currency": "RUB",
        "value": "30.00",
      },
      "splitCommissions": {
        "merchantCms": {
          "currency": "RUB",
          "value": "10.00"
        }
      },
      "orderId": "313fh1f23j13k1k",
      "comment": "i want to buy some goods"
    },
    {
      "type": "MERCHANT_DETAILS",
      "siteUid": "shop_mst-02",
      "splitAmount": {
        "currency": "RUB",
        "value": "20.00"
      },
      "splitCommissions": {
        "merchantCms": {
          "currency": "RUB",
          "value": "10.00"
        }
      },
      "orderId": "sdadada887sdDDDDd",
      "comment": "watermelon"
    },
    {
      "type": "MERCHANT_DETAILS",
      "siteUid": "shop_mst-03",
      "splitAmount": {
        "currency": "RUB",
        "value": "50.00"
      },
      "splitCommissions": {
        "merchantCms": {
          "currency": "RUB",
          "value": "10.00"
        }
      },
      "orderId": "dasdad444ll4ll",
      "comment": "flowers for my girlfriend"
    }
  ]
 }
~~~

В ответе содержатся данные о принятых платежах и комиссиях:

Поле ответа | Тип | Описание
----|------|-------
paymentSplits | Array | Массив с данными о принятых платежах
-----|------|------
type | String | Тип передаваемых данных. Всегда возвращается строка `MERCHANT_DETAILS`
siteUid | String | [ID поставщика](#start)
splitAmount | Object | Данные о возмещении поставщику
----|----|-----
value | String | Сумма возмещения
currency | String(3) | Буквенный код валюты возмещения по ISO
----|----|-----
splitCommissions | Object | Данные о комиссии (необязательный)
-----|----|-----
merchantCms | Object | Данные о комиссии с поставщика
----|-----|----
value | String | Сумма комиссии
currency | String(3) |Буквенный код валюты комиссии по ISO
-----|-----|-----
orderId | String | Номер заказа
comment | String | Комментарий к заказу

## Возвраты по сплитованным платежам {#split-refund}

После успешной авторизации денежных средств можно сделать отмену операции сплитованного платежа. Укажите общую сумму отмены и сумму отмены для каждого поставщика. Поддерживаются как частичная, так и полная отмена операции. 

<aside class="warning">Сумма отмененных возмещений поставщикам должна равняться сумме возврата.</aside>

### Формат запроса {#refunds-split-api}

> Пример запроса

~~~http
PUT /partner/payin/v1/sites/test-01/payments/23/refunds/1 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "refundSplits": [
  {
    "type": "MERCHANT_DETAILS",
    "siteUid": "shop_mst-01",
    "splitAmount": {
      "value": 30.00,
      "currency": "RUB"
    },
    "orderId": "sdadada887sdDDDDd",
    "comment": "watermelon"
  },
  {
    "type": "MERCHANT_DETAILS",
    "siteUid": "shop_mst-02",
    "splitAmount": {
      "value": 20.00,
      "currency": "RUB"
    },
   
    "orderId": "313fh1f23j13k1k",
    "comment": "i want to buy some goods"
  },
  {
    "type": "MERCHANT_DETAILS",
    "siteUid": "shop_mst-03",
    "splitAmount": {
      "value": 50.00,
      "currency": "RUB"
    },
    "orderId": "dasdad444ll4ll",
    "comment": "flowers for my girlfriend"
  }
  ]
}
~~~

> Пример ответа

~~~json
{
    "refundId": "1",
    "createdDateTime": "2020-10-12T15:32:29+03:00",
    "amount": {
        "currency": "RUB",
        "value": "100.00"
    },
    "status": {
        "value": "COMPLETED",
        "changedDateTime": "2020-10-12T15:32:30+03:00"
    },
    "refundSplits": [
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "shop_mst-02",
            "splitAmount": {
                "currency": "RUB",
                "value": "20.00"
            },
            "splitCommissions": {
                "merchantCms": {
                    "currency": "RUB",
                    "value": "10.00"
                }
            },
            "orderId": "sdadada887sdDDDDd",
            "comment": "watermelon"
        },
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "shop_mst-01",
            "splitAmount": {
                "currency": "RUB",
                "value": "30.00"
            },
            "splitCommissions": {
                "merchantCms": {
                    "currency": "RUB",
                    "value": "10.00"
                }
            },
            "orderId": "313fh1f23j13k1k",
            "comment": "i want to buy some goods"
        },
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "shop_mst-03",
            "splitAmount": {
                "currency": "RUB",
                "value": "50.00"
            },
            "splitCommissions": {
                "merchantCms": {
                    "currency": "RUB",
                    "value": "10.00"
                }
            },
            "orderId": "dasdad444ll4ll",
            "comment": "flowers for my girlfriend"
        }
    ]
}
~~~

В тело [запроса на создание возврата](#refund-api) добавьте JSON-массив `refundSplits`, используемый для передачи данных о возвратах поставщикам.

Описание массива `refundSplits`:

Название | Тип | Описание
----|------|-------
refundSplits | Array | Массив данных о возвратах поставщикам
-----|------|------
type | String | Тип передаваемых данных. Доступные значения - `MERCHANT_DETAILS` (данные поставщика)
siteUid | String | [ID поставщика](#start)
splitAmount | Object | Данные об отмене возмещения поставщику
----|----|-----
value | Number | Сумма отмены возмещения
currency | String(3) | Буквенный код валюты отмены возмещения по ISO. Доступен только `RUB`
-----|-----|-----
orderId | String | Номер заказа (необязательный)
comment | String | Комментарий к заказу (необязательный)

В ответе в JSON-массиве `refundSplits` содержатся данные о принятых возвратах:

Поле ответа | Тип | Описание
----|------|-------
refundSplits | Array | Массив данных о возвратах поставщикам
-----|------|------
type | String | Тип передаваемых данных. Всегда возвращается строка `MERCHANT_DETAILS`
siteUid | String | [ID поставщика](#start)
splitAmount | Object | Данные об отмене возмещения поставщику
----|----|-----
value | String | Сумма отмены возмещения
currency | String(3) | Буквенный код валюты отмены возмещения по ISO
----|----|-----
splitCommissions | Object | Данные о комиссии (необязательный)
-----|----|-----
merchantCms | Object | Данные о комиссии с поставщика
----|-----|----
value | String | Сумма комиссии
currency | String(3) | Буквенный код валюты комиссии по ISO
-----|-----|-----
orderId | String | Номер заказа
comment | String | Комментарий к заказу

# Apple Pay {#apple}

Apple Pay позволяет покупателям оплачивать покупки на сайте в одно касание, без ввода данных карты. Технология работает в мобильных приложениях и браузере Safari на iPhone, iPad, Apple Watch и MacBook. Для включения метода оплаты Apple Pay необходимо обратиться к вашему сопровождающему менеджеру.

Вам понадобится самостоятельно выполнить интеграцию с Apple. Это позволит верифицировать сайт ТСП и получать платежные данные пользователя. Требования к ТСП для интеграции Apple Pay на веб-странице:

* Аккаунт разработчика в Apple Developer Program.
* Использование HTTPS на странице с Apple Pay, поддержка TLS 1.2, действительный SSL сертификат.
*  Соблюдение [правил Apple](https://developer.apple.com/apple-pay/acceptable-use-guidelines-for-websites/) по использованию Apple Pay на сайтах.
* Использование фреймворка [Apple Pay JS API](https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api).

Более подробно об интеграции можно узнать на [сайте Apple](https://developer.apple.com/apple-pay/implementation/).

## Как отправлять платеж {#payin_apple}

> Пример объекта external3dSecData

~~~json
"external3dSecData": {
  "cavv": "AOLqt9wP++/WAzN+is7YAoABFA==",
  "eci": "05"
}
~~~

> Пример платежного запроса с передачей криптограммы Apple

~~~json
{
  "paymentMethod": {
    "type": "CARD",
    "pan": "4444443616621049",
    "expiryDate": "12/19",
    "holderName": "Apple pay",
    "external3dSecData": {
      "cavv": "AOLqt9wP++YAoABFA==",
      "eci": "05"
    }
  },
  "amount": {
    "value": "5900.00",
    "currency": "RUB"
  },
  "flags": [
    "SALE"
  ],
  "customer": {
    "account": "79111111111",
    "email": "test@qiwi.com",
    "phone": "79111111111"
  }
}
~~~

Процесс выполнения платежа Apple Pay в API состоит из четырех этапов:

1. Создание платежной сессии Apple Pay и валидация ТСП в Apple Pay.
2. Получение зашифрованных платежных данных из Apple Pay.
3. Расшифровка платежных данных на стороне ТСП.
4. Отправка [запроса на списание средств](#payments) с добавленной криптограммой Apple в QIWI.

Для отправки платежных данных в QIWI необходимо в [платежном запросе](#payments) передать в объекте `paymentMethod` тела запроса дополнительный JSON-объект `external3dSecData`, содержащий платежные данные от Apple:

* `cavv` - содержимое поля `onlinePaymentCryptogram` из расшифрованного [платежного токена Apple](https://developer.apple.com/library/archive/documentation/PassKit/Reference/PaymentTokenJSON/PaymentTokenJSON.html) 
* `eci` - ECI индикатор. Необходимо передавать, если поле `eciIndicator` получено в [платежном токене Apple](https://developer.apple.com/library/archive/documentation/PassKit/Reference/PaymentTokenJSON/PaymentTokenJSON.html). В противном случае параметр не передавать.

# Оплата через СБП {#sbp}

**Протокол онлайн платежей** поддерживает платежи через Систему быстрых платежей (СБП). Система быстрых платежей (СБП) - сервис, созданный Центральным банком РФ (Банком России), позволяющий производить платежи в пользу юридических лиц, например, за товары и услуги, в том числе с использованием QR-кодов.

<aside class="warning">
По умолчанию возможность приема оплаты с помощью Системы Быстрых Платежей отключена. Если вам необходимо подключить данную функцию, обратитесь к вашему сопровождающему менеджеру.
</aside>

## Получение QR-кода {#qr-sbp}

> Пример тела платежного запроса для платежа через СБП

~~~json
{
 "amount" : {
   "value" : "4.05",
   "currency" : "RUB"
},
 "paymentMethod" : {
    "type" : "SBP"
},
 "comment": "test"
}
~~~

> Пример ответа

~~~json
{
  "paymentId": "sbp-test-18",
  "billId": "autogenerated-ef2eccd6-37ce-471d-b0fd-6d2af7085e2e",
  "createdDateTime": "2020-12-11T11:09:48+03:00",
  "amount": {
    "currency": "RUB",
    "value": "4.05"
  },
  "capturedAmount": {
    "currency": "RUB",
    "value": "4.05"
  },
  "refundedAmount": {
    "currency": "RUB",
    "value": "0.00"
  },
  "paymentMethod": {
    "type": "SBP"
  },
  "requirements": {
    "sbp": {
      "qrcId": "AD10006BTTAGFLUT1HEMP1",
      "image": {
        "mediaType": "image/png",
        "content": "iVBORw0KgAAA5fY51AAAR+.."
      },
      "payload": "https://qr.nspk.ru/AD10006B89A9QD8FLUT1HEMP1?type=02&sum=405&cur=RUB&crc=5C01D"
    }
  },
  "status": {
    "value": "WAITING",
    "changedDateTime": "2020-12-11T11:09:49+03:00"
  }
}
~~~

Для того, чтобы пользователь смог произвести оплату с помощью СБП, необходимо выпустить QR-код. Для этого в поле `paymentMethod` указывается платежный метод `SBP`:

`"paymentMethod" : { "type" : "SBP" }`

Чтобы добавить описание платежа, используйте поле `comment`. Если поле в запросе останется пустым, на платежной форме отобразится название магазина.

В ответе на запрос в объекте `requirements.sbp` содержатся данные QR-кода, а также URL-based QR, позволяющий напрямую перенаправить пользователя в приложение банка.

В поле `content` находится base64-encoded QR-код. После расшифровки вы получите изображение, которое можно отобразить пользователю. 

QR-код активен в течение 72 часов, по истечению срока деактивируется.

## Статус платежа {#sbp-status}

> Пример ответа на запрос статуса платежа через СБП

~~~json
{
    "paymentId": "sbp-test-18",
    "billId": "autogenerated-ef2ec7ce-471d-b0fd-6d2af7085e2e",
    "createdDateTime": "2020-12-11T11:09:48+03:00",
    "amount": {
        "currency": "RUB",
        "value": "4.05"
    },
    "capturedAmount": {
        "currency": "RUB",
        "value": "4.05"
    },
    "refundedAmount": {
        "currency": "RUB",
        "value": "0.00"
    },
    "paymentMethod": {
        "type": "SBP"
    },
    "requirements": {
        "sbp": {
            "qrcId": "AD10006BTTAGF4789A9QD8FLUT1HEMP1",
            "payload": "https://qr.nspk.ru/AD10006BTTAGF4789A9QD8FLUT1HEMP1?type=02&sum=405&cur=RUB&crc=5C1D"
        }
    },
    "status": {
        "value": "WAITING",
        "changedDateTime": "2020-12-11T11:09:49+03:00"
    }
}
~~~

После оплаты платеж автоматически перейдет в финальный статус и будет отправлено уведомление. Тем не менее, статус платежа можно узнать самостоятельно, отправив [стандартный запрос статуса платежа](#payment_get). В объекте `requirements.sbp` ответа на запрос статуса платежа помещаются полученные данные о QR-коде.

# Оплата по токену карты {#token}

**Протокол онлайн платежей** поддерживает механизм использования платежных токенов карт пользователей. Выпуск платежного токена карты позволяет сохранить карточные данные в зашифрованной строке платежного токена и в дальнейшем использовать его для безакцептных платежей.

По умолчанию выпуск платежных токенов карт отключен. Если вам необходимо подключить данную функцию, обратитесь к вашему сопровождающему менеджеру.

Для использования платежных токенов вам необходимо два [идентификатора](#test_mode) `siteId` и токена API - один для [выпуска платежных токенов](#token_issue) (как правило, с поддержкой 3DS), второй для [оплаты платежным токеном](#token_pay).

## Выпуск платежного токена {#token_issue}

Чтобы выпустить платежный токен, используйте идентификаторы сайта (`siteId`, токен API), зарегистрированного для выпуска платежных токенов.

<aside class="warning">В случае прохождения 3DS-аутентификации клиентом, платежный токен выпускается только после успешной авторизации платежа банком-эмитентом.</aside>


### Если вы используете [Платежную форму](#invoicing) {#token-issue-payform}

>Пример запроса выставления счета 

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 10.00
   },
   "expirationDateTime": "2021-04-13T14:30:00+03:00",
    "customer": { 
      "account":"token32" 
   }, 
   "customFields": {}, 
   "flags":["BIND_PAYMENT_TOKEN"] 
} 
~~~

>Пример уведомления с платежным токеном

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

Укажите в запросе [выставления счета](#invoicing) дополнительные параметры:

* `flags: ["BIND_PAYMENT_TOKEN"]` - команда, указывающая системе на необходимость выпуска платежного токена
* `customer.account` - уникальный идентификатор Покупателя в системе ТСП. **Не указывайте один и тот же параметр <code>account</code> для всех ваших пользователей. В результате пользователи смогут произвести оплату чужими картами.**

После оплаты счета в [уведомлении](#payment_callback) `PAYMENT` вы получите данные платежного токена:

Поле уведомления|Тип данных|Описание
--------|-------|----------
tokenData|Object| Объект, содержащий данные о выпущенном платежном токене
tokenData.paymentToken|String| Строка платежного токена для использования при оплате
tokenData.expiredDate|String| Дата окончания срока действия платежного токена. Формат даты:<br>`YYYY-MM-DDThh:mm:ss±hh:mm`

### Если вы используете API {#token-issue-api}


>Пример платежного запроса

~~~http
PUT /partner/payin/v1/sites/test-01/payments/1811 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

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


> Пример тела ответа с платежным токеном

~~~json
{
    "paymentId": "test-022",
    "billId": "autogenerated-c4479bb1-c916-4fba-8445-802592fa8d51",
    "createdDateTime": "2020-03-26T12:22:12+03:00",
    "amount": {
        "currency": "RUB",
        "value": "10.00"
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
        "maskedPan": "411111******1111",
        "rrn": "123",
        "authCode": "181218",
        "type": "CARD"
    },
    "createdToken": {
        "token": "27e61f2f-19e1-4fd7-a3c8-fd84508d21ab",
        "name": "411111******1111"
    },
    "customer": {
        "account": "1",
        "phone": "79022222222"
    },
    "status": {
        "value": "COMPLETED",
        "changedDateTime": "2020-03-26T12:22:12+03:00"
    },
    "customFields": {
        "customer_account": "1",
        "customer_phone": "79022222222"
    },
    "flags": [
        "TEST"
    ]
}
~~~

Укажите в запросе на [создание платежа](#payments) дополнительные параметры:

* `flags: ["BIND_PAYMENT_TOKEN"]` - команда, указывающая системе на необходимость выпуска платежного токена
* `customer.account` - уникальный идентификатор Покупателя в системе ТСП. **Не указывайте один и тот же параметр <code>account</code> для всех ваших пользователей. В результате пользователи смогут произвести оплату чужими картами.**

Данные платежного токена возвращаются:

1. В ответе на запрос платежа, если не потребовалась дополнительная аутентификация клиента.
2. После переадресации на страницу подтверждения от эмитента в ответе на запрос [завершения аутентификации](#payment_complete), если потребовалась дополнительная аутентификация клиента.

Структура данных:

Поле ответа|Тип данных|Описание
--------|-------|----------
createdToken|Object| Данные платежного токена
createdToken.token|String| Строка платежного токена для использования при оплате
createdToken.name|String| Маскированный номер карты, для которой выпущен платежный токен

Также в обоих случаях данные платежного токена придут в [уведомлении](#payment_callback) `PAYMENT` (см. выше).

## Процесс оплаты токеном {#token_pay}

>Пример использования платежного токена в запросе

~~~http
PUT /partner/payin/v1/sites/test-02/payments/1815 HTTP/1.1
Accept: application/json
Authorization: Bearer 7uc4b25xx93xxx5d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com


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

Произвести оплату с помощью выпущенного платежного токена можно только через [API](#payments).

Для этого в [платежном запросе](#payment):

* используйте идентификаторы сайта (`siteId`, токен API), зарегистрированного для оплаты платежными токенами
* обязательно укажите параметры платежного токена в объекте `paymentMethod` вместо карточных данных и идентификатор покупателя:

Параметр|Тип|Описание
--------|---|--------
paymentMethod.type|String|Тип операции. Необходимо указать `TOKEN`
paymentMethod.paymentToken|String| Строка платежного токена
customer.account|String|Уникальный идентификатор Покупателя в системе ТСП, для которого выпущен платежный токен. **Без данного параметра оплата с помощью платежного токена невозможна.**

# Оплата с баланса КИВИ Кошелька {#qiwi-wallet-payment}

Создание платежа с баланса КИВИ Кошелька возможно только с помощью [платежной формы КИВИ](#payform).

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 1.00
   },
   "expirationDateTime": "2020-10-16T14:30:00+03:00"
}
~~~

<aside class="notice">
По умолчанию возможность приема оплаты с баланса КИВИ Кошелька отключена. Если вам необходимо подключить данную функцию, обратитесь к вашему сопровождающему менеджеру.
</aside>

Для приема платежей с КИВИ Кошелька используйте метод [Создание счета](#invoice_put). Отправьте PUT-запрос на URL:

`/payin/v1/sites/{siteId}/bills/{billId}`

> Ответ сервера

~~~json
{
    "siteId": "site-01",
    "billId": "89379",
    "amount": {
        "currency": "RUB",
        "value": "1.00"
    },
    "status": {
        "value": "WAITING",
        "changedDateTime": "2020-10-12T12:46:29.452+03:00"
    },
    "creationDateTime": "2020-10-12T12:46:29.452+03:00",
    "expirationDateTime": "2020-10-16T14:30:00+03:00",
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=d875277b-6f0f-445d-8a83-f62c7c07be77"
}
~~~

В ответе придет ссылка на платежную форму (см. поле `payUrl`). На платежной форме пользователю будет доступен способ оплаты с баланса КИВИ Кошелька.

<aside class="notice">Если у вас подключена оплата с карт на платежной форме, пользователю будут доступны оба способа оплаты.</aside>

Процесс оплаты выглядит следующим образом:

1. Пользователь открывает форму.
     
    ![Wallet Invoice](/images/wallet_invoice.jpg)
2. Пользователь авторизуется в КИВИ Кошельке.
    
    ![Wallet Auth](/images/wallet_auth.jpg)
3. Пользователь оплачивает счет.
    
    ![Wallet Pay](/images/wallet_pay.jpg)

<!-- После успешной оплаты вам придет уведомление [BILL](#bill_callback). -->

> Уведомление REFUND

~~~json
{
  "refund":
  {
    "refundId":"1",
    "type":"REFUND",
    "createdDateTime":"2020-10-12T13:08:13+03:00",
    "status":{
      "value":"SUCCESS",
      "changedDateTime":"2020-10-12T13:08:14+03:00"
    },
    "amount":{
      "value":1.00,
      "currency":"RUB"
    },
    "paymentMethod":{
      "type":"QIWI_WALLET",
      "phone":"79022672222"
    },
    "customer":{
      "phone":"0"
    },
    "gatewayData":{
      "type":"QW",
      "walletTxnId":"19990706195"
    },
    "billId":"89379",
    "flags":[]
  },
  "type":"REFUND",
  "version":"1"
}
~~~

Возвраты выполняются с помощью метода API [Операция возврата](#refund-api). В случае успешного возврата вам придет уведомление [REFUND](#payment_callback).


# Возмещение {#reimburse}

По умолчанию, возмещение по проведенным операциям производится раз в 2 дня и  минимальным порогом 10.000 рублей. Если вам необходимо особое расписание, обратитесь к вашему сопровождающему менеджеру.

КИВИ взимает комиссию за каждую подтвержденную операцию. Если отмена операции была произведена до подтверждения, комиссия не взимается. Если была произведена частичная отмена до подтверждения операции, комиссия будет пересчитана.


# Тестовые данные {#test_data}

Для тестирования операций оплаты используются [основные URL](#test_mode) сервисов оплаты. Для каждого нового ТСП в системе создаются `siteId` и по умолчанию включаются в режим тестирования. Также можно запросить переключение в режим тестирования любого своего `siteId`, либо добавление нового `siteId` в режиме тестирования через вашего сопровождающего менеджера.

## Оплата картой {#test_data_card}

В режиме тестирования можно использовать любой номер карты, удовлетворяющий алгоритму Luhn.

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

В тестовой среде установлено ограничение на сумму и количество тестовых операций. По умолчанию максимальная сумма тестовых транзакций - 10 рублей. Максимум - 100 транзакций в сутки (учитываются операции за текущие сутки, время по МСК). Учитываются операции с суммой не более установленного лимита.

Для проведения операции с 3DS необходимо использовать строку `unknown name` в имени держателя карты. Прохождение 3DS в режиме тестирования можно проверить только при вводе номера реальной карты.

## Оплата через СБП {#test_data_sbp}

Для тестирования различных вариантов оплаты и ответов необходимо использовать различные суммы оплаты (поле `amount`):

* `100` - операция сразу пройдет успешно. Будет отправлено уведомление. При запросе статуса вы получите `"SUCCESS"`;
* `200` - операция пройдет успешно с задержкой. При первом запросе статуса вы получите `"WAITING"`. После второго запроса статуса - `"SUCCESS"`.
* При всех остальных суммах платеж пройдет неуспешно.

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


## Причины отклонения {#reasons}

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

## Коды ошибок {#errors}

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
500 | Internal Server Error -- Внутренняя ошибка сервиса. Если тело ответа пустое, повторите запрос с теми же параметрами. Если тело ответа не пустое, выполните [запрос статуса платежа](#payment_get)/[статуса счета](#invoice_get).
502 | Bad Gateway - Нет связи с сервисом
503 | Service Unavailable -- Сервер временно недоступен по техническим причинам, попробуйте позже.

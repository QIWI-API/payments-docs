# Серверные уведомления {#callback}

> Пример уведомления

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: example.com

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
         "maskedPan":"220024******5036",
         "rrn":"124",
         "authCode":"182211"
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

Уведомление от QIWI — входящий POST-запрос с информацией о событии. Тело запроса содержит JSON-сериализованные данные платежа/счета (кодировка UTF-8).

Протокол поддерживает следующие типы уведомлений о событиях API:

* [`PAYMENT`](#payment-callback) — отправляются при совершении операций платежа;
* [`CAPTURE`](#capture-callback) — отправляются при совершении операций подтверждения платежа;
* [`REFUND`](#refund-callback) — отправляются при совершении операций возврата платежа;
* [`CHECK_CARD`](#check-card-callback) — отправляются при совершении операций проверки карты.

<aside class="notice">
Не установлено единого порядка отправки уведомлений разного типа в ходе выполнения одной и той же операции. Порядок может отличаться для разных операций.
</aside>

Адрес вашего сервера для обработки уведомлений указывается в [Личном кабинете](https://kassa.qiwi.com/service/core/merchants?) в разделе **Настройки**.

Чтобы указать URL сервера обработки уведомлений для отдельной операции, используйте:

* параметр `callbackUrl` — в запросах API [Платеж](#payments), [Подтверждение платежа](#capture), [Операция возврата](#refund-api);
* параметр `customFields.invoice_callback_url` — в запросе API [Создание счета](#invoice_put).

URL для уведомлений должен начинаться с https, так как уведомления отправляются по протоколу HTTPS на порт 443.
Сертификат сайта должен быть выпущен доверенным центром сертификации (например Comodo, Verisign, Thawte и т.п.).

<aside class="notice">
Если уведомление об операции не поступило вам в течение 10 минут после совершения операции, необходимо запросить статус операции <a href="#invoice-details">запросом статуса счета</a> или <a href="#payment_get">запросом статуса платежа</a> (в зависимости от того, какой из <a href="#methods">способов взаимодействия</a> вы используете).
</aside>

Для дополнительной уверенности следует принимать уведомления о платежах только с указанных ниже IP-адресов компании QIWI:

* 79.142.16.0/20
* 195.189.100.0/22
* 91.232.230.0/23
* 91.213.51.0/24

Уведомление считается успешно доставленным, если ваш сервер ответил HTTP кодом состояния `200 OK`.
До этого момента система будет пытаться доставить уведомление через увеличивающиеся интервалы времени в течение суток с момента операции.

<aside class="success">
Если по какой-либо причине ваш сервер успешно обработал уведомление по транзакции, но не вернул <code>200 OK</code>, то при повторной попытке доставки не нужно обрабатывать данную транзакцию как новую.
</aside>

## Авторизация уведомлений {#notifications-auth}

В уведомлении присутствует цифровая подпись запроса, которую необходимо проверять на вашей стороне для исключения возможности подделки уведомления.

<aside class="warning">
Вся ответственность за возможные финансовые потери, случившиеся вследствие отсутствия валидации подписи в нелегитимном уведомлении, лежит полностью на мерчанте.
</aside>

Цифровая подпись уведомления помещается в HTTP-заголовок `Signature`.

Для проверки подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256 и ключом, указанным в разделе **Настройки** Личного кабинета мерчанта.

Алгоритм проверки подписи:

1. Объединить значения определенных параметров в одну строку с разделителем "\|". Например:

   `parameters = {payment.paymentId}|{payment.createdDateTime}|{payment.amount.value}`

   где `{*}` – значение параметра уведомления. Все значения при проверке подписи должны трактоваться как строки.

   Подпись считается для следующих полей уведомления:

     * тип `PAYMENT`: `payment.paymentId|payment.createdDateTime|payment.amount.value`
     * тип `REFUND`: `refund.refundId|refund.createdDateTime|refund.amount.value`
     * тип `CAPTURE`: `capture.captureId|capture.createdDateTime|capture.amount.value`
     * тип `CHECK_CARD`: `checkPaymentMethod.requestUid|checkPaymentMethod.checkOperationDate`

2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAС(SHA256, secret, parameters)`

   Где:

      * `secret` — ключ хеширования (UTF-8). Совпадает с ключом серверных уведомлений, указанным в разделе **Настройки** Личного кабинета мерчанта.
      * `parameters` — строка из п.1 (UTF-8).

3. Сравнить значение подписи из  HTTP-заголовка `Signature` уведомления с результатом п.2.

## Частота отправки уведомлений {#notification-rate}

Сервис отправки уведомлений распределяет неуспешные уведомления по очередям:

* 1 попытка с отложенным временем 5 секунд
* 1 попытка с отложенным временем 1 минута
* 3 попытки с отложенным временем по 5 минут

Время повторной отправки может сдвигаться в бОльшую сторону.

## Формат уведомления PAYMENT {#payment-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|------
payment | Описание платежа. | Object | Всегда
----|------|-------|--------
type| [Тип операции](#operation-types) |String(200)| Всегда
paymentId|Уникальный идентификатор платежа в системе ТСП.|String(200)|Всегда
createdDatetime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
amount | Информация о сумме операции |Object|Всегда
--|--|----|---
value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)|Всегда
currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)|Всегда
---|---|---|----
billId| ID счета, соответствующего операции| String(200)|Всегда
status | Информация о статусе операции | Object|Всегда
---|---|---|------
value |[Строковое значение статуса](#operation-statuses) | String|Всегда
changedDatetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
reasonCode| [Код причины отклонения](#reason-codes)| String(200)|Всегда
reasonMessage| Описание причины отклонения| String(200)|Всегда
errorCode| Код ошибки| Number|Всегда
---|---|---|------
paymentMethod| Информация о средстве платежа| Object|Всегда
---|---|---|------
type| Тип метода оплаты| String|Всегда
paymentToken| Платежный токен карты.| String | При оплате платежным токеном
maskedPan| Маскированный PAN карты| String|При оплате платежным токеном или картой
rrn| RRN платежа (по ISO 8583)| Number|При оплате платежным токеном или картой
authCode| Auth-code платежа| Number|При оплате платежным токеном или картой
---|---|---|-------
paymentCardInfo | Информация о карте.| Object |  Всегда
---|---|---|-------
issuingCountry | Код страны эмитента | String(3)|Всегда
issuingBank | Банк-эмитент | String|Всегда
paymentSystem | Тип платежной системы | String|Всегда
fundingSource | Тип карты | String |Всегда
paymentSystemProduct | Категория карты | String|Всегда
---|---|---|-----
customer | Информация о покупателе| Object|Всегда
---|---|---|------
phone |Номер телефона покупателя |String|Всегда
email|E-mail покупателя|String|Всегда
account| Идентификатор покупателя в системе ТСП|String|Всегда
ip| IP адрес покупателя |String|Всегда
country| Страна адреса покупателя |String|Всегда
---|---|---|----
customFields | Поля с произвольной информацией, дополняющей данные по операции | Object|Всегда
---|---|---|-----
cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
`FROM_MERCHANT_CONTRACT_ID`| Номер договора |String(256)|Всегда
`FROM_MERCHANT_BOOKING_NUMBER` | Номер бронирования  |String(256)|Всегда
`FROM_MERCHANT_PHONE` | Мобильный телефон покупателя | String(256)|Всегда
`FROM_MERCHANT_FULL_NAME` | ФИО покупателя |String(256)|Всегда
---|---|---|------|-----
flags| Дополнительные команды, переданные в API| Массив. Возможные элементы - `SALE`/`REVERSAL`|Всегда
tokenData| Объект с информацией о выпущенном [платежном токене](#token_issue)|Object|Если в платеже был запрошен выпуск платежного токена
---|---|---|-----
paymentToken| Строка платежного токена | String|Если в платеже был запрошен выпуск платежного токена
expiredDate | Дата окончания срока действия платежного токена. Формат даты соответствует стандарту ISO-8601:<br>`YYYY-MM-DDThh:mm:ss±hh:mm` | String|Если в платеже был запрошен выпуск платежного токена
---|---|---|-----
paymentSplits | Описание сплитованных платежей. | Array(Objects) | Для [сплитованных платежей](#payments_split)
-----|------|------|-----
type | Тип передаваемых данных. Всегда строка `MERCHANT_DETAILS` | String|Для [сплитованных платежей](#payments_split)
siteUid | [ID поставщика](#split-boarding) | String|Для [сплитованных платежей](#payments_split)
splitAmount | Данные о возмещении поставщику | Object|Для [сплитованных платежей](#payments_split)
----|----|-----|-----
value | Сумма возмещения | Number|Для [сплитованных платежей](#payments_split)
currency | Буквенный код валюты возмещения по ISO | String(3)|Для [сплитованных платежей](#payments_split)
----|----|-----|-----
splitCommissions | Данные о комиссии | Object|Для [сплитованных платежей](#payments_split)
-----|----|-----|-----
merchantCms | Данные о комиссии с поставщика | Object|Для [сплитованных платежей](#payments_split)
----|-----|----|-----
value | Сумма комиссии | Number|Для [сплитованных платежей](#payments_split)
currency |Буквенный код валюты комиссии по ISO | String(3)|Для [сплитованных платежей](#payments_split)
-----|-----|-----|-----
orderId | Номер заказа | String|Для [сплитованных платежей](#payments_split)
comment | Комментарий к заказу | String|Для [сплитованных платежей](#payments_split)
-----|-----|-----|-----
type| Тип уведомления|String(200)|Всегда
version | Версия уведомлений | String|Всегда

## Формат уведомления CAPTURE {#capture-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Поле|Описание|Тип
--------|--------|---
capture | Описание подтверждения. | Object 
----|------|-------
type| [Тип операции](#operation-types) |String(200)
captureId|Уникальный идентификатор подтверждения в системе ТСП.|String(200)
createdDatetime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
amount | Информация о сумме операции |Object
--|--|----
value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)
---|---|---
billId| ID счета, соответствующего операции| String(200)
status | Информация о статусе операции | Object
---|---|---
value |Строковое значение статуса | String
changedDatetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
reasonCode| [Код причины отклонения](#reason-codes)| String(200)
reasonMessage| Описание причины отклонения| String(200)
errorCode| Код ошибки| Number
---|---|---
paymentMethod| Информация о средстве платежа| Object
---|---|---
type| Тип метода оплаты| String
maskedPan| Маскированный PAN карты| String
rrn| RRN платежа (по ISO 8583)| Number
authCode| Auth-code платежа| Number
---|---|---
customer | Информация о покупателе| Object
---|---|---
phone |Номер телефона покупателя|String
email|E-mail покупателя|String
account| Идентификатор покупателя в системе ТСП|String
ip| IP адрес покупателя |String
country| Страна адреса покупателя |String
---|---|---
customFields | Поля с произвольной информацией, дополняющей данные по операции | Object
---|---|---
cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
`FROM_MERCHANT_CONTRACT_ID`| Номер договора |String(256)
`FROM_MERCHANT_BOOKING_NUMBER` | Номер бронирования  |String(256)
`FROM_MERCHANT_PHONE` | Мобильный телефон покупателя | String(256)
`FROM_MERCHANT_FULL_NAME` | ФИО покупателя |String(256)
---|---|---
flags| Дополнительные команды, переданные в API| Массив. Возможные элементы - `SALE`/`REVERSAL`
type| Тип уведомления|String(200)
version | Версия уведомлений | String

## Формат уведомления REFUND {#refund-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|------
refund | Описание возврата. | Object | Всегда
----|------|-------|--------
type| [Тип операции](#operation-types) |String(200)| Всегда
refundId|Уникальный идентификатор возврата в системе ТСП.|String(200)|Всегда
createdDatetime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
amount | Информация о сумме операции |Object|Всегда
--|--|----|---
value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)|Всегда
currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)|Всегда
---|---|---|----
billId| ID счета, соответствующего операции| String(200)|Всегда
status | Информация о статусе операции | Object|Всегда
---|---|---|------
value |Строковое значение статуса | String|Всегда
changedDatetime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
reasonCode| [Код причины отклонения](#reason-codes)| String(200)|Всегда
reasonMessage| Описание причины отклонения| String(200)|Всегда
errorCode| Код ошибки| Number|Всегда
---|---|---|------
paymentMethod| Информация о средстве платежа| Object|Всегда
---|---|---|------
type| Тип метода оплаты| String|Всегда
maskedPan| Маскированный PAN карты| String|Всегда
rrn| RRN платежа (по ISO 8583)| Number|Всегда
authCode| Auth-code платежа| Number|Всегда
---|---|---|-----
customer | Информация о покупателе| Object|Всегда
---|---|---|------
phone |Номер телефона покупателя |String|Всегда
email|E-mail покупателя |String|Всегда
account| Идентификатор покупателя в системе ТСП |String|Всегда
ip| IP адрес покупателя |String|Всегда
country| Страна адреса покупателя |String|Всегда
---|---|---|----
customFields | Поля с произвольной информацией, дополняющей данные по операции | Object|Всегда
---|---|---|-----
cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегд
cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
`FROM_MERCHANT_CONTRACT_ID`| Номер договора |String(256)|Всегда
`FROM_MERCHANT_BOOKING_NUMBER` | Номер бронирования  |String(256)|Всегда
`FROM_MERCHANT_PHONE` | Мобильный телефон покупателя | String(256)|Всегда
`FROM_MERCHANT_FULL_NAME` | ФИО покупателя |String(256)|Всегда
---|---|---|------|-----
flags| Дополнительные команды, переданные в API| Массив. Возможные элементы - `SALE`/`REVERSAL`|Всегда
refundSplits | Описание возвратов по сплитованным платежам. | Array(Objects) | При [возвратах сплитованных платежей](#split-refund)
-----|------|------|-----
type | Тип передаваемых данных. Всегда строка `MERCHANT_DETAILS` | String|При [возвратах сплитованных платежей](#split-refund)
siteUid | [ID поставщика](#split-boarding) | String|При [возвратах сплитованных платежей](#split-refund)
splitAmount | Данные об отмене возмещения поставщику | Object|При [возвратах сплитованных платежей](#split-refund)
----|----|-----|-----
value | Сумма отмены возмещения | Number|При [возвратах сплитованных платежей](#split-refund)
currency | Буквенный код валюты отмены возмещения по ISO | String(3)|При [возвратах сплитованных платежей](#split-refund)
----|----|-----|-----
splitCommissions | Данные о комиссии | Object|При [возвратах сплитованных платежей](#split-refund)
-----|----|-----|-----
merchantCms | Данные о комиссии с поставщика | Object|При [возвратах сплитованных платежей](#split-refund)
----|-----|----|-----
value | Сумма комиссии | Number|При [возвратах сплитованных платежей](#split-refund)
currency |Буквенный код валюты комиссии по ISO | String(3)|При [возвратах сплитованных платежей](#split-refund)
-----|-----|-----|-----
orderId | Номер заказа | String|При [возвратах сплитованных платежей](#split-refund)
comment | Комментарий к заказу | String|При [возвратах сплитованных платежей](#split-refund)
-----|-----|-----|-----
type| Тип уведомления|String(200)|Всегда
version | Версия уведомлений | String|Всегда

## Формат уведомления CHECK_CARD {#check-card-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li>Signature: XXX</li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>


Поле|Описание|Тип
--------|--------|---
checkPaymentMethod | Описание результата проверки карты | Object
----|------|-------
checkOperationDate | Дата проверки карты | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
requestUid | Идентификатор [операции проверки карты](#how-to-check-card) | String
status | Информация о статусе проверки карты | String
isValidCard | Признак валидности карты для платежей | Bool
threeDsStatus | Информация о статусе дополнительной аутентификации при проверке карты. Возможные значения —  `PASSED` (3-D Secure пройден), `NOT_PASSED` (3-D Secure не пройден), `WITHOUT` (3-D Secure не требовалось) | String
paymentMethod| Информация о средстве платежа| Object
---|---|---
type| Тип метода оплаты| String
maskedPan| Маскированный PAN карты| String
cardExpireDate | Срок действия карты | String
cardHolder | Имя держателя карты | String
---|---|---
cardInfo | Информация о карте.| Object
---|---|---
issuingCountry | Код страны эмитента | String(3)
issuingBank | Банк-эмитент | String
paymentSystem | Тип платежной системы | String
fundingSource | Тип карты | String 
paymentSystemProduct | Категория карты | String
---|---|---
createdToken| Объект с информацией о выпущенном вместе с проверкой карты [платежном токене](#token_issue)|Object
---|---|---
token| Строка платежного токена | String
name |Маскированный PAN карты, для которой выпущен платежный токен| String
expiredDate | Дата окончания срока действия платежного токена. Формат даты соответствует стандарту ISO-8601:<br>`YYYY-MM-DDThh:mm:ss±hh:mm` | String
account| Идентификатор покупателя, указанный при выпуске платежного токена|String
---|---|---
type| Тип уведомления|String(200)
version | Версия уведомлений | String

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

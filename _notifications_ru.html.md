# Серверные уведомления {#callback}

Уведомление от QIWI — это входящий POST-запрос с информацией о событии. Тело запроса содержит JSON-сериализованные данные платежа/счета (кодировка UTF-8).

Протокол поддерживает следующие типы уведомлений о событиях API:

* [PAYMENT](#payment-callback) — отправляются при совершении операций платежа;
* [CAPTURE](#capture-callback) — отправляются при совершении операций подтверждения платежа;
* [REFUND](#refund-callback) — отправляются при совершении операций возврата платежа;
* [CHECK_CARD](#check-card-callback) — отправляются при совершении операций проверки карты;
* [TOKEN](#token-callback) — отправляются при выпуске токена СБП и совершении платежных операций с его использованием;
* [PAYOUT](#payout-callback) — отправляются при совершении операций выплаты.

<aside class="notice">
Не установлено единого порядка отправки уведомлений разного типа в ходе выполнения одной и той же операции. Порядок может отличаться для разных операций.
</aside>

Адрес вашего сервера для обработки уведомлений указывается в [Личном кабинете](https://kassa.qiwi.com/service/core/merchants?) в разделе **Настройки**.

Чтобы указать URL сервера обработки уведомлений для отдельной операции, используйте:

* параметр `callbackUrl` — в запросах API [Платеж](#payments), [Подтверждение платежа](#capture), [Операция возврата](#refund-api) и [Выплата](#payout);
* параметр `customFields.invoice_callback_url` — в запросе API [Создание счета](#invoice_put).

URL для уведомлений должен начинаться с https, так как уведомления отправляются по протоколу HTTPS на порт 443. **URL должен быть доступен из Интернета**.

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
Если по какой-либо причине ваш сервер успешно обработал уведомление по транзакции, но не вернул <code>200 OK</code>, то при повторной попытке доставки не нужно обрабатывать такую транзакцию как новую.
</aside>

## Авторизация уведомлений {#notifications-auth}

В уведомлении присутствует цифровая подпись запроса, которую необходимо проверять на вашей стороне для исключения возможности подделки уведомления.

<aside class="warning">
Вся ответственность за возможные финансовые потери, случившиеся вследствие отсутствия валидации подписи в нелегитимном уведомлении, лежит полностью на компании мерчанта.
</aside>

Цифровая подпись уведомления помещается в HTTP-заголовок `Signature`.

Для проверки подписи используется механизм проверки целостности HMAC с хэш-функцией SHA256 и ключом, указанным в разделе **Настройки** Личного кабинета мерчанта.

Алгоритм проверки подписи:

1. Объединить значения определенных параметров в одну строку с разделителем "\|". Например:

   `parameters = {payment.paymentId}|{payment.createdDateTime}|{payment.amount.value}`

   где `{*}` – значение параметра уведомления. Все значения предварительно приводятся к строковому представлению (UTF-8).

   Набор полей уведомления для проверки подписи зависит от типа уведомления:

    * тип `PAYMENT`: `payment.paymentId|payment.createdDateTime|payment.amount.value`
    * тип `REFUND`: `refund.refundId|refund.createdDateTime|refund.amount.value`
    * тип `CAPTURE`: `capture.captureId|capture.createdDateTime|capture.amount.value`
    * тип `CHECK_CARD`: `checkPaymentMethod.requestUid|checkPaymentMethod.checkOperationDate`
    * тип `TOKEN`: `token.merchantSiteUid|token.account|token.status.value|token.status.changedDateTime`
    * тип `PAYOUT`: `payout.payoutId|payout.createdDateTime|payout.amount.value`

2. Вычислить HMAC-хэш c алгоритмом хэширования SHA256:

   `hash = HMAC(SHA256, secret, parameters)`

   Где:

      * `secret` — ключ хеширования (UTF-8). Совпадает с ключом серверных уведомлений, указанным в разделе **Настройки** Личного кабинета мерчанта.
      * `parameters` — строка из п.1.

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
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Пример уведомления PAYMENT

~~~http
POST /qiwi-notify.php HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: example.com
~~~

~~~json
{
  "payment": {
    "paymentId": "A22170834426031500000733E625FCB3",
    "customFields": {},
    "type": "PAYMENT",
    "createdDateTime": "2022-08-05T11:34:42+03:00",
    "status": {
      "value": "SUCCESS",
      "changedDateTime": "2022-08-05T11:34:44+03:00"
    },
    "amount": {
      "value": 5,
      "currency": "RUB"
    },
    "paymentMethod": {
      "type": "SBP",
      "phone": "79111112233"
    },
    "customer": {
      "phone": "0",
      "bankAccountNumber": "4081710809561219555",
      "bic": "044525974",
      "lastName": "ИВАНОВ",
      "firstName": "ИВАН",
      "middleName": "ИВАНОВИЧ",
      "simpleAddress": ""
    },
    "billId": "autogenerated-6cd20922-b1d0-4e67-ba61-e2b7310c4006",
    "flags": [
      "SALE"
    ],
    "qrCodeUid": "acfd9"
  },
  "type": "PAYMENT",
  "version": "1"
}
~~~

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|------
payment | Описание платежа. | Object | Всегда
----|------|-------|--------
payment.<br>type| [Тип операции](#operation-types) — только `PAYMENT` |String(200)| Всегда
payment.<br>paymentId|Уникальный идентификатор платежа в системе ТСП.|String(200)|Всегда
payment.<br>createdDateTime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
payment.<br>billId| Идентификатор счета, соответствующего операции| String(200)|Всегда
payment.<br>qrCodeUid| Идентификатор операции выпуска QR-кода в системе ТСП|String|Если операция была выполнена через СБП
payment.<br>amount | Информация о сумме операции |Object|Всегда
--|--|----|---
payment.<br>amount.<br>value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)|Всегда
payment.<br>amount.<br>currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)|Всегда
---|---|---|----
payment.<br>status | Информация о статусе операции | Object|Всегда
---|---|---|------
payment.<br>status.<br>value |[Строковое значение статуса](#notification-statuses) | String|Всегда
payment.<br>status.<br>changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
payment.<br>status.<br>reasonCode| [Код причины отклонения](#reason-codes)| String(200)|В случае отклонения операции
payment.<br>status.<br>reasonMessage| Описание причины отклонения| String(200)|В случае отклонения операции
payment.<br>status.<br>errorCode| Код ошибки| Number|В случае ошибки
payment.<br>status.<br>psErrorCode| [Оригинальный код ошибки, полученный от платежной системы](#ps-error-codes)|String|В случае отклонения операции
---|---|---|------
payment.<br>paymentMethod| Информация о средстве платежа| Object|Всегда
---|---|---|------
payment.<br>paymentMethod.<br>type| Тип метода оплаты: `CARD` — банковская карта, `TOKEN` — платежный токен, `SBP` — Система Быстрых Платежей, `QIWI_WALLET` — баланс QIWI Кошелька| String|Всегда
payment.<br>paymentMethod.<br>paymentToken| Платежный токен карты.| String | При оплате платежным токеном
payment.<br>paymentMethod.<br>maskedPan| Маскированный PAN карты| String|При оплате платежным токеном или картой
payment.<br>paymentMethod.<br>rrn| RRN платежа (по ISO 8583)| Number|При оплате платежным токеном или картой
payment.<br>paymentMethod.<br>authCode| Auth-code платежа| Number|При оплате платежным токеном или картой
payment.<br>paymentMethod.<br>phone| Телефон, с которого выполнялась оплата через СБП, или номер QIWI Кошелька |String|При оплате через СБП или с баланса QIWI Кошелька
---|---|---|-------
payment.<br>paymentCardInfo | Информация о карте.| Object |  Всегда
---|---|---|-------
payment.<br>paymentCardInfo.<br>issuingCountry | Код страны эмитента | String(3)|Всегда
payment.<br>paymentCardInfo.<br>issuingBank | Банк-эмитент | String|Всегда
payment.<br>paymentCardInfo.<br>paymentSystem | Тип платежной системы | String|Всегда
payment.<br>paymentCardInfo.<br>fundingSource | Тип карты | String |Всегда
payment.<br>paymentCardInfo.<br>paymentSystemProduct | Категория карты | String|Всегда
---|---|---|-----
payment.<br>customer | Информация о покупателе| Object|Всегда
---|---|---|------
payment.<br>customer.<br>phone |Номер телефона покупателя |String|Всегда
payment.<br>customer.<br>email|E-mail покупателя|String|Всегда
payment.<br>customer.<br>account| Идентификатор покупателя в системе ТСП|String|Всегда
payment.<br>customer.<br>ip| IP адрес покупателя |String|Всегда
payment.<br>customer.<br>country| Страна адреса покупателя |String|Всегда
payment.<br>customer.<br>bankAccountNumber|Номер счета плательщика|String| Только для платежей через СБП
payment.<br>customer.<br>bic|БИК банка, выпустившего карту|String| Только для платежей через СБП
payment.<br>customer.<br>lastName|Фамилия покупателя|String| Только для платежей через СБП
payment.<br>customer.<br>firstName|Имя покупателя|String| Только для платежей через СБП
payment.<br>customer.<br>middleName|Отчество покупателя|String| Только для платежей через СБП
payment.<br>customer.<br>simpleAddress|Адрес покупателя|String| Только для платежей через СБП
payment.<br>customer.<br>inn| ИНН покупателя |String| Только для платежей через СБП
---|---|---|----
payment.<br>customFields | Поля с произвольной информацией, дополняющей данные по операции | Object|Всегда
---|---|---|-----
payment.<br>customFields.<br>cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
payment.<br>customFields.<br>cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
payment.<br>customFields.<br>cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
payment.<br>customFields.<br>cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
payment.<br>customFields.<br>cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
---|---|---|------|-----
payment.<br>flags| Дополнительные команды, переданные в API| Массив. Возможные элементы - `SALE`/`REVERSAL`|Всегда
payment.<br>tokenData| Объект с информацией о выпущенном [платежном токене](#token_issue)|Object|Если в платеже был запрошен выпуск платежного токена
---|---|---|-----
payment.<br>tokenData.<br>paymentToken| Строка платежного токена | String|Если в платеже был запрошен выпуск платежного токена
payment.<br>tokenData.<br>expiredDate | Дата окончания срока действия платежного токена. Формат даты соответствует стандарту ISO-8601:<br>`ГГГГ-ММ-ДДTчч:мм:сс±чч:мм` | String|Если в платеже был запрошен выпуск платежного токена
---|---|---|-----
payment.<br>chequeData | Описание фискального чека | [ChequeData](#receipt-data) | Если в операции были отправлены [данные для формирования фискального чека](#cheque)
payment.<br>paymentSplits | Описание сплитованных платежей. | Array(Objects) | Для [сплитованных платежей](#payments_split)
-----|------|------|-----
payment.<br>paymentSplits.<br>type | Тип передаваемых данных. Всегда строка `MERCHANT_DETAILS` | String|Для [сплитованных платежей](#payments_split)
payment.<br>paymentSplits.<br>siteUid | [ID поставщика](#split-boarding) | String|Для [сплитованных платежей](#payments_split)
payment.<br>paymentSplits.<br>splitAmount | Информация о возмещении поставщику | Object|Для [сплитованных платежей](#payments_split)
----|----|-----|-----
payment.<br>paymentSplits.<br>splitAmount.<br>value | Сумма возмещения | Number|Для [сплитованных платежей](#payments_split)
payment.<br>paymentSplits.<br>splitAmount.<br>currency | Буквенный код валюты возмещения по ISO | String(3)|Для [сплитованных платежей](#payments_split)
----|----|-----|-----
payment.<br>paymentSplits.<br>splitCommissions | Информация о комиссии | Object|Для [сплитованных платежей](#payments_split)
-----|----|-----|-----
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms | Информация о комиссии с поставщика | Object|Для [сплитованных платежей](#payments_split)
----|-----|----|-----
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms.<br>value | Сумма комиссии | Number|Для [сплитованных платежей](#payments_split)
payment.<br>paymentSplits.<br>splitCommissions.<br>merchantCms.<br>currency |Буквенный код валюты комиссии по ISO | String(3)|Для [сплитованных платежей](#payments_split)
-----|-----|-----|-----
payment.<br>paymentSplits.<br>orderId | Номер заказа | String|Для [сплитованных платежей](#payments_split)
payment.<br>paymentSplits.<br>comment | Комментарий к заказу | String|Для [сплитованных платежей](#payments_split)
-----|-----|-----|-----
type| Тип уведомления — только `PAYMENT`|String|Всегда
version | Версия уведомлений | String|Всегда

## Формат уведомления CAPTURE {#capture-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Поле|Описание|Тип
--------|--------|---
capture | Описание операции подтверждения. | Object
----|------|-------
capture.<br>type| [Тип операции](#operation-types) — только `CAPTURE` |String(200)
capture.<br>captureId|Уникальный идентификатор подтверждения в системе ТСП.|String(200)
capture.<br>createdDateTime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
capture.<br>amount | Информация о сумме операции |Object
--|--|----
capture.<br>amount.<br>value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)
capture.<br>amount.<br>currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)
---|---|---
capture.<br>billId| ID счета, соответствующего операции| String(200)
capture.<br>status | Информация о статусе операции | Object
---|---|---
capture.<br>status.<br>value |[Строковое значение статуса](#notification-statuses) | String
capture.<br>status.<br>changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
capture.<br>status.<br>reasonCode| [Код причины отклонения](#reason-codes)| String(200)
capture.<br>status.<br>reasonMessage| Описание причины отклонения| String(200)
capture.<br>status.<br>errorCode| Код ошибки| Number
---|---|---
capture.<br>paymentMethod| Информация о средстве платежа| Object
---|---|---
capture.<br>paymentMethod.<br>type| Тип метода оплаты| String
capture.<br>paymentMethod.<br>maskedPan| Маскированный PAN карты| String
capture.<br>paymentMethod.<br>rrn| RRN платежа (по ISO 8583)| Number
capture.<br>paymentMethod.<br>authCode| Auth-code платежа| Number
---|---|---
capture.<br>customer | Информация о покупателе| Object
---|---|---
capture.<br>customer.<br>phone |Номер телефона покупателя|String
capture.<br>customer.<br>email|E-mail покупателя|String
capture.<br>customer.<br>account| Идентификатор покупателя в системе ТСП|String
capture.<br>customer.<br>ip| IP адрес покупателя |String
capture.<br>customer.<br>country| Страна адреса покупателя |String
---|---|---
capture.<br>customFields | Поля с произвольной информацией, дополняющей данные по операции | Object
---|---|---
capture.<br>customFields.<br>cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
capture.<br>customFields.<br>cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
capture.<br>customFields.<br>cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
capture.<br>customFields.<br>cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
capture.<br>customFields.<br>cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)
---|---|---
capture.<br>flags| Дополнительные команды, переданные в API| Array(Strings). Возможные элементы: `SALE`, `REVERSAL`
type| Тип уведомления — только `CAPTURE`|String
version | Версия уведомлений | String

## Формат уведомления REFUND {#refund-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|------
refund | Описание возврата. | Object | Всегда
----|------|-------|--------
refund.<br>type| [Тип операции](#operation-types) — только `REFUND` |String(200)| Всегда
refund.<br>refundId|Уникальный идентификатор возврата в системе ТСП.|String(200)|Всегда
refund.<br>createdDateTime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
refund.<br>amount | Информация о сумме операции |Object|Всегда
--|--|----|---
refund.<br>amount.<br>value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)|Всегда
refund.<br>amount.<br>currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)|Всегда
---|---|---|----
refund.<br>billId| ID счета, соответствующего операции| String(200)|Всегда
refund.<br>status | Информация о статусе операции | Object|Всегда
---|---|---|------
refund.<br>status.<br>value |[Строковое значение статуса](#notification-statuses) | String|Всегда
refund.<br>status.<br>changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
refund.<br>status.<br>reasonCode| [Код причины отклонения](#reason-codes)| String(200)|В случае отклонения операции
refund.<br>status.<br>reasonMessage| Описание причины отклонения| String(200)|В случае отклонения операции
refund.<br>status.<br>errorCode| Код ошибки| Number|В случае ошибки
---|---|---|------
refund.<br>paymentMethod| Информация о средстве платежа| Object|Всегда
---|---|---|------
refund.<br>paymentMethod.<br>type| Тип метода оплаты| String|Всегда
refund.<br>paymentMethod.<br>maskedPan| Маскированный PAN карты| String|Всегда
refund.<br>paymentMethod.<br>rrn| RRN платежа (по ISO 8583)| Number|Всегда
refund.<br>paymentMethod.<br>authCode| Auth-code платежа| Number|Всегда
---|---|---|-----
refund.<br>customer | Информация о покупателе| Object|Всегда
---|---|---|------
refund.<br>customer.<br>phone |Номер телефона покупателя |String|Всегда
refund.<br>customer.<br>email|E-mail покупателя |String|Всегда
refund.<br>customer.<br>account| Идентификатор покупателя в системе ТСП |String|Всегда
refund.<br>customer.<br>ip| IP адрес покупателя |String|Всегда
refund.<br>customer.<br>country| Страна адреса покупателя |String|Всегда
---|---|---|----
refund.<br>customFields | Поля с произвольной информацией, дополняющей данные по операции | Object|Всегда
---|---|---|-----
refund.<br>customFields.<br>cf1 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
refund.<br>customFields.<br>cf2 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
refund.<br>customFields.<br>cf3 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
refund.<br>customFields.<br>cf4 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
refund.<br>customFields.<br>cf5 | Поле с произвольной информацией, дополняющей данные по операции| String(256)|Всегда
---|---|---|------|-----
refund.<br>flags| Дополнительные команды, переданные в API| Array(Strings). Возможные элементы: `SALE`, `REVERSAL`|Всегда
refund.<br>chequeData | Описание фискального чека | [ChequeData](#receipt-data) | Если в операции был отправлены [данные для формирования фискального чека](#cheque)
refund.<br>refundSplits | Описание возвратов по сплитованным платежам. | Array(Objects) | При [возвратах сплитованных платежей](#split-refund)
-----|------|------|-----
refund.<br>refundSplits.<br>type | Тип передаваемых данных. Всегда строка `MERCHANT_DETAILS` | String|При [возвратах сплитованных платежей](#split-refund)
refund.<br>refundSplits.<br>siteUid | [ID поставщика](#split-boarding) | String|При [возвратах сплитованных платежей](#split-refund)
refund.<br>refundSplits.<br>splitAmount | Информация об отмене возмещения поставщику | Object|При [возвратах сплитованных платежей](#split-refund)
----|----|-----|-----
refund.<br>refundSplits.<br>splitAmount.<br>value | Сумма отмены возмещения | Number|При [возвратах сплитованных платежей](#split-refund)
refund.<br>refundSplits.<br>splitAmount.<br>currency | Буквенный код валюты отмены возмещения по ISO | String(3)|При [возвратах сплитованных платежей](#split-refund)
----|----|-----|-----
refund.<br>refundSplits.<br>splitCommissions | Информация о комиссии | Object|При [возвратах сплитованных платежей](#split-refund)
-----|----|-----|-----
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms | Информация о комиссии с поставщика | Object|При [возвратах сплитованных платежей](#split-refund)
----|-----|----|-----
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms.<br>value | Сумма комиссии | Number|При [возвратах сплитованных платежей](#split-refund)
refund.<br>refundSplits.<br>splitCommissions.<br>merchantCms.<br>currency |Буквенный код валюты комиссии по ISO | String(3)|При [возвратах сплитованных платежей](#split-refund)
-----|-----|-----|-----
refund.<br>refundSplits.<br>orderId | Номер заказа | String|При [возвратах сплитованных платежей](#split-refund)
refund.<br>refundSplits.<br>comment | Комментарий к заказу | String|При [возвратах сплитованных платежей](#split-refund)
-----|-----|-----|-----
type| Тип уведомления — только `REFUND`|String|Всегда
version | Версия уведомлений | String|Всегда

## Формат уведомления CHECK_CARD {#check-card-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Пример уведомления CHECK_CARD

~~~json
{
    "checkPaymentMethod": {
        "status": "SUCCESS",
        "isValidCard": true,
        "threeDsStatus": "PASSED",
        "cardInfo": {
            "issuingCountry": "RUS",
            "issuingBank": "Альфа-банк",
            "paymentSystem": "MASTERCARD",
            "fundingSource": "PREPAID",
            "paymentSystemProduct": "TNW|TNW|Mastercard® New World—Immediate Debit|TNW|Mastercard New World-Immediate Debit"
        },
        "createdToken": {
            "token": "7653465767c78-a979-5bae621db96f",
            "name": "54**********47",
            "expiredDate": "2022-12-30T00:00:00+03:00",
            "account": "acc1"
        },
        "requestUid": "uuid1-uuid2-uuid3-uuid4",
        "paymentMethod": {
            "type": "CARD",
            "maskedPan": "54************47",
            "cardHolder": null,
            "cardExpireDate": "12/2022"
        },
        "checkOperationDate": "2021-08-16T14:15:07+03:00"
    },
    "type": "CHECK_CARD",
    "version": "1"
}
~~~

Поле|Описание|Тип
--------|--------|---
checkPaymentMethod | Описание результата проверки карты | Object
----|------|-------
checkPaymentMethod.<br>checkOperationDate | Дата проверки карты | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`
checkPaymentMethod.<br>requestUid | Идентификатор [операции проверки карты](#how-to-check-card) | String
checkPaymentMethod.<br>status | [Статус проверки карты](#card-check-statuses) | String
checkPaymentMethod.<br>isValidCard | Признак доступности карты для платежей | Bool
checkPaymentMethod.<br>threeDsStatus | Информация о статусе дополнительной аутентификации при проверке карты. Возможные значения: `PASSED` (3-D Secure пройден), `NOT_PASSED` (3-D Secure не пройден), `WITHOUT` (3-D Secure не требовалось) | String
----|------|-------
checkPaymentMethod.<br>paymentMethod| Информация о средстве платежа| Object
---|---|---
checkPaymentMethod.<br>paymentMethod.<br>type| Тип метода оплаты| String
checkPaymentMethod.<br>paymentMethod.<br>maskedPan| Маскированный PAN карты| String
checkPaymentMethod.<br>paymentMethod.<br>cardExpireDate | Срок действия карты | String
checkPaymentMethod.<br>paymentMethod.<br>cardHolder | Имя держателя карты | String
---|---|---
checkPaymentMethod.<br>cardInfo | Информация о карте.| Object
---|---|---
checkPaymentMethod.<br>cardInfo.<br>issuingCountry | Код страны эмитента | String(3)
checkPaymentMethod.<br>cardInfo.<br>issuingBank | Банк-эмитент | String
checkPaymentMethod.<br>cardInfo.<br>paymentSystem | Тип платежной системы | String
checkPaymentMethod.<br>cardInfo.<br>fundingSource | Тип карты | String
checkPaymentMethod.<br>cardInfo.<br>paymentSystemProduct | Категория карты | String
---|---|---
checkPaymentMethod.<br>createdToken| Объект с информацией о [платежном токене](#token_issue), выпущенном вместе с проверкой карты |Object
---|---|---
checkPaymentMethod.<br>createdToken.<br>token| Строка платежного токена | String
checkPaymentMethod.<br>createdToken.<br>name |Маскированный PAN карты, для которой выпущен платежный токен| String
checkPaymentMethod.<br>createdToken.<br>expiredDate | Дата окончания срока действия платежного токена. Формат даты соответствует стандарту ISO-8601:<br>`ГГГГ-ММ-ДДTчч:мм:сс±чч:мм` | String
checkPaymentMethod.<br>createdToken.<br>account| Идентификатор покупателя, указанный при выпуске платежного токена|String
---|---|---
type| Тип уведомления  — только `CHECK_CARD`|String
version | Версия уведомлений | String

## Формат уведомления TOKEN {#token-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Уведомление об успешной привязке токена СБП

~~~json
{
  "token": {
    "status": {
      "value": "CREATED",
      "changedDateTime": "2023-01-01T10:00:00+03:00"
    },
    "merchantSiteUid": "test-00",
    "account": "test",
    "value": "d28a4ff8-548d-4536-927d-fc01123bebbf",
    "expiredDate": "2029-01-01T10:00:00+03:00",
    "tokenizationSource": {
      "type": "QR_CODE",
      "uid": "100220001"
    }
  },
  "type": "TOKEN",
  "version": "1"
}
~~~

> Уведомление об успешной оплате СБП, но неуспешной привязке токена СБП

~~~json
{
  "token": {
    "status": {
      "value": "REJECTED",
      "changedDateTime": "2023-01-01T10:00:00+03:00"
    },
    "merchantSiteUid": "test-00",
    "account": "test",
    "tokenizationSource": {
      "type": "QR_CODE",
      "uid": "14012000011"
    }
  },
  "type": "TOKEN",
  "version": "1"
}
~~~

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|-------
token | Описание токена | Object | Всегда
----|------|-------|-------
token.<br>status | Информация о статусе операции | Object | Всегда
----|------|-------|-------
token.<br>status.<br>value | Строковое значение статуса | String | Всегда
token.<br>status.<br>changedDateTime|Дата обновления статуса|URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ` | Всегда
token.<br>status.<br>rejectReason|Причина отклонения|String|В случае отклонения операции
----|------|-------
token.<br>merchantSiteUid| ID поставщика| String | Всегда
token.<br>account|Идентификатор покупателя, указанный при выпуске платежного токена|String|Всегда
token.<br>value|Платежный токен|String|В случае успешной операции
token.<br>expiredDate|Дата окончания срока действия платежного токена. Формат даты соответствует стандарту ISO-8601: `ГГГГ-ММ-ДДTчч:мм:сс±чч:мм` |String|В случае успешной операции
----|------|-------|-------
token.<br>tokenizationSource|Информация об источнике токенизации|Object|Всегда
----|------|-------|-------
token.<br>tokenizationSource.<br>type|Тип источника токенизации|String|Всегда
token.<br>tokenizationSource.<br>uid|ID источника токенизации|String|Всегда
----|------|-------|-------
type| Тип уведомления  — только `TOKEN`|String|Всегда
version | Версия уведомлений | String|Всегда

## Формат уведомления PAYOUT {#payout-callback}

<ul class="nestedList header">
    <li><h3>HEADERS</h3>
        <ul>
             <li><a href="#notifications-auth">Signature: XXX</a></li>
             <li>Accept: application/json</li>
             <li>Content-type: application/json</li>
        </ul>
    </li>
</ul>

> Пример уведомления PAYOUT

~~~http
POST <callback-path> HTTP/1.1
Accept: application/json
Content-type: application/json
Signature: J4WNfNZd***V5mv2w=
Host: <callback-url>
~~~

~~~json
{
  "payout": {
    "payoutId":"kxnawm631754",
    "createdDateTime":"2022-12-22T16:20:30+03:00",
    "amount": {
      "value":200.00,
      "currency":"RUB"
    },
    "status":{
      "value":"SUCCESS",
      "changedDateTime":"2022-12-22T16:34:44+03:00"
    },
    "receiverData": {
      "type":"CARD",
      "maskedPan":"400000******0002"
    },
    "flags":["TEST"]
  },
  "type":"PAYOUT",
  "version":"1"
}
~~~

Поле|Описание|Тип|В каких случаях используется
--------|--------|---|------
payout | Описание выплаты | Object | Всегда
----|------|-------|--------
payout.<br>payoutId|Уникальный идентификатор выплаты в системе ТСП|String(200)|Всегда
payout.<br>createdDateTime | Дата создания операции | URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
payout.<br>amount | Информация о сумме операции |Object|Всегда
--|--|----|---
payout.<br>amount.<br>value | Сумма операции, округленная до двух десятичных знаков в меньшую сторону | Number(6.2)|Всегда
payout.<br>amount.<br>currency | Идентификатор валюты операции (Alpha-3 ISO 4217 код) | String(3)|Всегда
---|---|---|----
payout.<br>status | Информация о статусе операции | Object|Всегда
---|---|---|------
payout.<br>status.<br>value |[Строковое значение статуса](#notification-statuses) | String|Всегда
payout.<br>status.<br>changedDateTime|Дата обновления статуса| URL-закодированная строка<br>`ГГГГ-ММ-ДДTЧЧ:ММ:ССZ`|Всегда
payout.<br>status.<br>reasonCode| [Код причины отклонения](#reason-codes)| String(200)|В случае отклонения операции
payout.<br>status.<br>reasonMessage| Описание причины отклонения| String(200)|В случае отклонения операции
payout.<br>status.<br>errorCode| Код ошибки| Number|В случае ошибки
---|---|---|------
payout.<br>receiverData| Информация о получателе| [PayoutReceiverDataCallback](#payout-receiver-data-callback) |Всегда
payout.<br>flags| Дополнительные флаги операции| Array(Strings). Возможные элементы: `TEST`|При необходимости
-----|-----|-----|-----
type| Тип уведомления — только `PAYOUT`|String|Всегда
version | Версия уведомлений | String|Всегда

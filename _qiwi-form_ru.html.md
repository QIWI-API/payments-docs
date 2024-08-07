# Платеж через форму QIWI {#invoicing}

При подключении платежей через форму QIWI покупателю доступен только способ оплаты банковскими картами. Другие способы оплаты включаются по запросу:

* [Платежные токены карт](#qiwi-form-cardtoken).
* Система быстрых платежей.

Чтобы выполнить платеж через форму QIWI, выставите счет покупателю. Воспользуйтесь [выставлением счета через API](#qiwi-form-invoice-api) или перенаправьте покупателя на форму QIWI [по прямой ссылке с параметрами счета](#https-qiwi-form).

## Процесс платежа {#flow-payment-qiwi-form}

<div class="mermaid">
sequenceDiagram
participant customer as Покупатель
participant store as Магазин
participant qb as QIWI (эквайер)
participant ips as Эмитент
customer->>store:Выбор товаров, Старт оплаты
activate store
store->>qb:API: запрос "Создание счета"<br/>Одношаговый платеж — все способы оплаты<br/>Двухшаговый платеж — только карты
activate qb
qb->>store:Ссылка на платежную форму QIWI<br/>(параметр "payUrl")
store->>customer:Переадресация покупателя на payUrl
customer->>qb:Открытие платежной формы, выбор способа оплаты,<br/>указание платежных данных для выбранного способа
qb->>customer:Аутентификация покупателя:<br/>Для карт — 3-D Secure
customer->>qb:Аутентификация
qb->>ips:Запрос списания денежных средств
activate ips
ips->>qb:Статус операции
qb->>store:Уведомление о статусе операции
rect rgb(255, 238, 223)
Note over qb, customer: Параметр "successUrl" указан в ссылке на платежную форму QIWI
qb->>customer: Возврат на сайт мерчанта при успешной операции
end
store->>qb: Проверка статуса операции<br/>API: запрос "Статус платежа"
qb->>store: Статус операции
rect rgb(255, 238, 223)
Note over store, ips:Двухшаговый платеж
store->>qb:Подтверждение операции (capture)
qb->>ips:Подтверждение списания
deactivate ips
qb->>store:Уведомление о подтверждении платежа
store->>qb: Проверка статуса операции<br/>API: запрос "Статус подтверждения"
qb->>store: Статус операции
end
deactivate qb
deactivate store
</div>

## Интеграция c Платежной формой QIWI без использования API {#https-qiwi-form}

Для мерчантов доступны следующие способы интеграция без использования методов платежного API:

* Передайте параметры счёта в ссылке на Платежную форму — см. ниже [примеры и список параметров](#payform_flow). Когда покупатель открывает ссылку, ему автоматически выставляется счет и отображается Платежная форма.
* Вызовите [метод создания счета](#createpopup) JavaScript-библиотеки Popup.

<aside class="notice">
Язык формы по умолчанию (русский) можно заменить на английский через курирующего менеджера.
</aside>

При оплате счета, выставленного таким способом, аутентификация покупателя и ее завершение выполняются автоматически (без участия мерчанта). Так как используется двухшаговая схема (авторизация платежа и подтверждение), то платеж необходимо подтвердить через [Личный кабинет](https://business.qiwi.com/service/core/merchants?). Сервис QIWI ожидает подтверждения платежа в течение 72 часов. По истечении срока выполняется автоматическое подтверждение платежа.

<aside class="notice">
Чтобы уменьшить период ожидания подтверждения платежа или настроить автоотмену платежа обратитесь в Службу поддержки.
</aside>

<h3 class="request method" id="payform_flow">GET → </h3>

> Ссылка на форму с передачей суммы платежа

~~~shell
https://payment.qiwi.com/create?publicKey=5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3ceEvMvCq55ToeErzhvK6rVkQLaCrYUQcYF5QkS8nCrjnPsLQgsLxqrpQgJ7hg2ZHmEHXFjaG8qjvgcep&extras[cf1]=Order_123&extras[cf3]=winnie@pooh.ru&readonly=cf1&comment=some%20comment&amount=100.00
~~~

> Ссылка на форму без указания суммы платежа (сумму заполняет покупатель)

~~~shell
https://payment.qiwi.com/create?publicKey=5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3ceEvMvCq55ToeErzhvK6rVkQLaCrYUQcYF5QkS8nCrjnPsLQgsLxqrpQgJ7hg2ZHmEHXFjaG8qjvgcep&extras[cf1]=Order_123&extras[cf3]=winnie@pooh.ru&readonly=cf1
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://payment.qiwi.com/create?publicKey={key}&{parameter}={value}</span></h3></li>
</ul>

<aside class="notice">
При переходе на форму в webview на смартфонах Android обязательно включайте <code>settings.setDomStorageEnabled(true).</code>
</aside>

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В URL query указываются параметры счета.</span></li>
</ul>

Параметр ссылки              | Описание                                                                                                                                                                                                                                        | Тип
-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------
publicKey                    | **Обязательный параметр**. Ключ идентификации мерчанта, уникальный для каждого `siteId`. Ключ можно [получить](/ru/payments-lk-guide/#settings) в [Личном кабинете](https://business.qiwi.com/service/core/merchants?) в разделе **Настройки**. | String
billId                       | Уникальный идентификатор счета в системе мерчанта. Генерируется на стороне ТСП любым способом как уникальная последовательность букв, цифр и символов `_`, `-`. Если не указан, то при каждом переходе по ссылке создается новый счет.        | URL-закодированная строка String(200)
amount                       | Сумма покупки, округленная в меньшую сторону до 2 десятичных знаков (всегда в рублях)                                                                                                                                                           | Number(6.2)
currency                     | Код валюты покупки. Возможные значения: `RUB`, `EUR`, `USD`. По умолчанию `RUB`                                                                                                                                                                 | String(3)
phone                        | Номер телефона покупателя (в международном формате)                                                                                                                                                                                             | URL-закодированная строка
email                        | E-mail покупателя                                                                                                                                                                                                                               | URL-закодированная строка
comment                      | Комментарий к счету                                                                                                                                                                                                                             | URL-закодированная строка String(255)
successUrl                   | URL для возврата на сайт мерчанта в случае успешной оплаты. Ссылку необходимо указывать в кодировке UTF-8.                                                                                                                                      | URL-закодированная строка
paymentMethod                | Платежный метод, предлагаемый покупателю по умолчанию на платежной форме. Возможные значения: `CARD`, `SBP`. Если указанный метод недоступен мерчанту, отображается другой доступный. По умолчанию — `CARD`.                                    | String
extras[cf1]                  | Дополнительное поле с произвольной информацией, дополняющей данные счета                                                                                                                                                                        | URL-закодированная строка
extras[cf2]                  | Дополнительное поле с произвольной информацией, дополняющей данные счета                                                                                                                                                                        | URL-закодированная строка
extras[cf3]                  | Дополнительное поле с произвольной информацией, дополняющей данные счета                                                                                                                                                                        | URL-закодированная строка
extras[cf4]                  | Дополнительное поле с произвольной информацией, дополняющей данные счета                                                                                                                                                                        | URL-закодированная строка
extras[cf5]                  | Дополнительное поле с произвольной информацией, дополняющей данные счета                                                                                                                                                                        | URL-закодированная строка
extras[themeCode]            | Дополнительное поле с [кодом стиля Платежной формы](#custom-form)                                                                                                                                                                               | URL-закодированная строка
extras[contract_id]          | Дополнительное поле с номером договора клиента (для передачи данных МФО)                                                                                                                                                                        | URL-закодированная строка
extras[customer_last_name]   | Дополнительное поле с фамилией заёмщика (для передачи данных МФО)                                                                                                                                                                               | URL-закодированная строка
extras[customer_first_name]  | Дополнительное поле с именем заёмщика (для передачи данных МФО)                                                                                                                                                                                 | URL-закодированная строка
extras[customer_middle_name] | Дополнительное поле с отчеством заёмщика (для передачи данных МФО)                                                                                                                                                                              | URL-закодированная строка
readonly                     | Список дополнительных полей, которые должны быть недоступны для изменения покупателем на платежной форме                                                                                                                                        | Строка, разделитель имен полей `,`. Пример: `cf1,cf3`

## Выставление счета и получение ссылки на оплату через API {#qiwi-form-invoice-api}

<aside class="notice">
На Платежной форме QIWI по умолчанию покупателю доступен только способ оплаты банковской картой. Для подключения других способов оплаты обратитесь к курирующему менеджеру.
</aside>

Протокол приема платежей поддерживает выставление счетов с оплатой как [двухшаговым платежом](#qiwi-capture) с холдированием средств на карте покупателя, так и [одношаговым платежом](#one_step) без авторизации покупателя.

### Двухшаговый платеж {#qiwi-capture}

> Выставление счета с оплатой через холдирование (двухшаговый платеж)

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": "42.24"
   },
   "billPaymentMethodsType": [
      "SBP"
   ],
   "comment": "Spasibo",
   "expirationDateTime": "2019-09-13T14:30:00+03:00",
   "customFields": {}
}
~~~

>Подтверждение платежа

~~~http
PUT /partner/payin/v1/sites/23044/payments/804900/captures/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com
~~~

> Уведомление об оплате счета

~~~json
{
  "payment": {
    "type": "PAYMENT",
    "paymentId": "824c7744-1650-4836-abaa-842ca7ca8a74",  <==paymentId, необходимый для подтверждения
    "createdDateTime": "2022-07-27T12:43:35+03:00",
    "status": {
        "value": "SUCCESS",
        "changedDateTime": "2022-07-27T12:43:47+03:00"
    },
    "amount": {
        "value": 1.00,
        "currency": "RUB"
    },
    "paymentMethod": {
        "type": "CARD",
        "maskedPan": "512391******6871",
        "cardHolder": null,
        "cardExpireDate": "3/2030"
    },
    "tokenData": {
        "paymentToken": "cc123da5-2fdd-4685-912e-8671597948a3",
        "expiredDate": "2030-03-01T00:00:00+03:00"
    },
    "customFields": {
        "cf2": "dva",
        "cf1": "1",
        "cf4": "4",
        "cf3": "tri",
        "cf5": "5",
        "full_name": "full_name",
        "phone": "phone",
        "contract_id": "contract_id",
        "comment": "test",
        "booking_number": "booking_number"
    },
    "paymentCardInfo": {
        "issuingCountry": "643",
        "issuingBank": "Тинькофф банк",
        "paymentSystem": "MASTERCARD",
        "fundingSource": "UNKNOWN",
        "paymentSystemProduct": "TNW|TNW|Mastercard® New World—Immediate Debit|TNW|Mastercard New World-Immediate Debit"
    },
    "merchantSiteUid": "test-00",
    "customer": {
        "email": "darta@mail.ru",
        "account": "11235813",
        "phone": "79850223243"
    },
    "gatewayData": {
        "type": "ACQUIRING",
        "eci": "2",
        "authCode": "0123342",
        "rrn": "001239598011"
    },
    "billId": "191616216126154",
    "flags": [
        "AFT"
    ]
  },
  "type": "PAYMENT",
  "version": "1"
}
~~~

1. Передайте в запросе API [Создание счета](#invoice_put):

   * [ключ API](#api-auth);
   * сумму счета в параметре `amount`;
   * дату, до которой необходимо оплатить счет, в параметре `expirationDateTime`;
   * (опционально) другую информацию о счете, в том числе:
     * комментарий в параметре `comment`;
     * информация о покупателе (`customer`, `address`) и получателе платежа (`receiverData`, при необходимости);
     * дополнительные данные по операции в параметре `customFields`.

   Вы можете управлять методами платежа, которые будут отображены покупателю на Платежной форме. Для этого перечислите их в параметре API `billPaymentMethodsType`. Указанные методы должны быть включены через Службу поддержки для `siteId` из запроса.

2. [Перенаправьте покупателя на Платежную форму](#qiwi-redirect) по ссылке из параметра `payUrl` ответа, или [откройте форму во всплывающем окне](#openpopup) с помощью [JavaScript-библиотеки Popup](#popup).
3. Получите идентификатор платежа `paymentId`:
   * из [серверного уведомления](#payment-callback) после успешного холдирования средств;
   * из ответа на запрос API [Получение списка платежей по счету](#invoice-payments).
4. Отправьте запрос API [Подтверждение платежа](#capture) с полученным `paymentId`. [Возмещение](#reimburse) формируется только после подтверждения.
5. Дождитесь завершения платежа: вам поступит [уведомление](#capture-callback), или периодически отправляйте запрос API [Статус подтверждения](#capture_get), чтобы получить информацию о платеже.

<aside class="notice">
По умолчанию, при холдировании сервис QIWI ожидает подтверждения платежа в течение 72 часов. По истечении срока выполняется автоподтверждение платежа. Чтобы увеличить или уменьшить период ожидания, или настроить автоотмену платежа, обратитесь в Службу поддержки. Период ожидания не может длиться более 5 суток.
</aside>

### Одношаговый платеж {#one_step}

> Выставление счета с оплатой без авторизации Покупателя (одношаговый платеж)

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": "100.00"
   },
   "expirationDateTime": "2024-03-13T14:30:00+03:00",
   "flags": [
     "SALE"
    ]
}
~~~

1. Передайте в запросе API [Создание счета](#invoice_put):

   * [ключ API](#api-auth);
   * сумму счета в параметре `amount`;
   * дату, до которой необходимо оплатить счет, в параметре `expirationDateTime`;
   * параметр `"flags":["SALE"]`. **Если не передать его, то будет выполнено безусловное холдирование средств для оплаты счета**;
   * (опционально) другую информацию о счете, в том числе:
     * комментарий в параметре `comment`;
     * информация о покупателе (`customer`, `address`) и получателе платежа (`receiverData`, при необходимости);
     * дополнительные данные по операции в параметре `customFields`.

   Вы можете управлять методами платежа, которые будут отображены покупателю на Платежной форме. Для этого перечислите их в параметре API `billPaymentMethodsType`. Указанные методы должны быть включены через Службу поддержки для `siteId` из запроса.

2. [Перенаправьте покупателя на Платежную форму](#qiwi-redirect) по ссылке из параметра `payUrl` ответа, или [откройте форму во всплывающем окне](#openpopup) с помощью [JavaScript-библиотеки Popup](#popup).
3. Дождитесь завершения платежа: вам поступит [уведомление о платеже](#payment-callback), или периодически отправляйте запрос API [Статус счета](#invoice-details), чтобы получить информацию о платеже.

### Платежный токен {#qiwi-form-cardtoken}

> Выставление счета с оплатой платежным токеном

~~~http
PUT /partner/payin/v1/sites/test-02/bills/1815 HTTP/1.1
Accept: application/json
Authorization: Bearer 7uc4b25xx93xxx5d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2024-03-13T14:30:00+03:00",
   "customer": {
     "account": "token234"
   },
   "customFields": {
    "cf1": "Some data"
   }
   }
}
~~~

Платежные токены используются для списаний с карт без ввода реквизитов карты. Метод оплаты платежным токеном по умолчанию отключен. Чтобы подключить его, обратитесь к курирующему менеджеру.

Подробнее о выпуске платежного токена см. в [этом разделе](#payment-token-issue).

<aside class="warning">
Покупатель может совершать оплату платежным токеном только на том сайте, для которого был выпущен платежный токен.

Чтобы платежный токен действовал на других сайтах, отправьте запрос в Службу поддержки.
</aside>

Чтобы покупатель смог оплатить платежным токеном:

1. Передайте в запросе API [Создание счета](#invoice_put) следующую информацию:
   * [ключ API](#api-auth);
   * сумму счета (`amount`);
   * дату, до которой необходимо оплатить счет (`expirationDateTime`);
   * идентификатор покупателя, для которого был выпущен платежный токен, в параметре `customer.account`. **Без этого параметра оплата платежным токеном невозможна.**
   * (опционально) другую информацию о счете.
2. [Перенаправьте покупателя на Платежную форму](#qiwi-redirect) по ссылке из параметра `payUrl` ответа, или [откройте форму во всплывающем окне](#openpopup) с помощью [JavaScript-библиотеки Popup](#popup).
3. Если для покупателя был выпущен один или несколько платежных токенов, то на Платежной форме отобразится список его привязанных карт.

   ![qiwi-form-tokens](/images/payin/qiwi-form-token.png)

   Для оплаты покупателю достаточно выбрать карту из списка. Указывать карточные данные и проходить проверку 3-D Secure не требуется.

<aside class="warning">Обратитесь к курирующему менеджеру чтобы включить отображение покупателю списка его карт, привязанных через платежные токены. Только в этом случае покупатель сможет воспользоваться привязанными картами на форме. Список карт будет отображаться только для оплаты с сайта мерчанта, где были созданы токены.</aside>

Для списания средств по платежному токену без участия Покупателя воспользуйтесь методом API [Платеж](#payment). См. подробнее [описание использования платежного токена на Платежной форме мерчанта](#merchant-token-pay).

## Перенаправление на форму QIWI {#qiwi-redirect}

>Пример ответа с payUrl

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
    "payUrl": "https://payment.qiwi.com/form/?invoice_uid=78d60ca9-7c99-481f-8e51-0100c9012087"
}
~~~

Чтобы покупатель смог оплатить выставленный счет, перенаправьте его на Платежную форму по ссылке из поля `payUrl` ответа на запрос выставления счета.

По умолчанию, на Платежной форме QIWI 3-D Secure покупателя обязателен.

<aside class="notice">
При открытии ссылки на форму в webview на смартфонах Android обязательно включайте <code>settings.setDomStorageEnabled(true)</code>
</aside>

> Пример ссылки с successUrl

~~~shell
https://payment.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://example.com/payments/#introduction
~~~

К ссылке можно добавить параметры:

| Параметр      | Описание                                                                                                                                                                    | Тип                        |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
| successUrl    | URL для возврата на сайт мерчанта в случае успешной оплаты. Возврат произойдет после успешной 3DS аутентификации. Ссылку необходимо указывать в кодировке UTF-8.            | URL-закодированная строка  |
| lang          | Язык платежной формы. Язык по умолчанию — русский (`ru`).                                                                                                                   | `ru`, `en`                 |
| paymentMethod | Платежный метод, предлагаемый покупателю по умолчанию на платежной форме. Если указанный метод недоступен мерчанту, отображается другой доступный. По умолчанию — `CARD`.   | `CARD`, `SBP`              |

> Пример обработчика событий iframe

~~~javascript
window.addEventListener('message', function (event) {
    switch (event.data) {
        case 'INITIALIZED':
            // Форма загружена
            break;
        case 'PAYMENT_ATTEMPT':
            // Попытка платежа
            break;
        case 'PAYMENT_SUCCEEDED':
            // Платеж прошел успешно
            break;
        case 'PAYMENT_FAILED':
            // Платеж не прошел
            break;
    }
}, false)
~~~

При открытии ссылки в `<iframe>`:

`<iframe src="<ссылка payUrl> ..." />`

вы можете использовать [метод](https://developer.mozilla.org/ru/docs/Web/API/Window/postMessage) `postMessage` для отслеживания состояния формы.

Возможные значения состояния:

* `INITIALIZED` — Форма загружена.
* `PAYMENT_ATTEMPT` — Попытка платежа.
* `PAYMENT_SUCCEEDED` — Платеж прошел успешно.
* `PAYMENT_FAILED` — Платеж не прошел.
* `INITIALIZATION_FAILED` — Ошибка загрузки формы.

## Библиотека Popup {#popup}

Методы библиотеки позволяют открыть Платежную форму оплаты счета как всплывающее окно (popup) поверх сайта ТСП. В библиотеке доступно два метода:

* [Выставление нового счета](#createpopup).
* [Открытие существующего счета](#openpopup).

<button id="pop" class="button-popup">Пример работы popup</button>

Для установки и подключения библиотеки добавьте скрипт в код сайта:

`<script src='https://payment.qiwi.com/popup/v2.js'></script>`

### Выставление нового счета {#createpopup}

>Пример вызова метода выставления счета

~~~javascript
QiwiCheckout.createInvoice({
    publicKey: '5nAq6abtyCz4tcDj89e5w7Y5i524LAFmzrsN6bQTQ3c******',
    amount: 10.00,
    phone: '79123456789',
    email: 'test@example.ru',
    account: 'account1',
    comment: 'Платеж',
    customFields: {
        data: 'data'
    },
    lifetime: '2022-04-04T1540'
})
    .then(data => {
        //     ...
    })
    .catch(error => {
        //     ...
    })
~~~

Чтобы создать счет и открыть форму оплаты, вызовите метод `QiwiCheckout.createInvoice`. Параметры метода:

| Параметр | Описание | Формат |
|--------------|---------------------------------------------|-------------|
| publicKey | **Обязательный параметр**. Ключ идентификации мерчанта, уникальный для каждого `siteId`. Ключ можно [получить](/ru/payments-lk-guide/#settings) в [Личном кабинете](https://business.qiwi.com/service/core/merchants?) в разделе **Настройки**. | String |
| amount | **Обязательный параметр**. Сумма, на которую выставляется счет, округленная в меньшую сторону до 2 десятичных знаков | Number(6.2) |
| phone | Номер телефона пользователя, на который выставляется счет (в международном формате) | String |
| email | E-mail пользователя, куда будет отправлена ссылка для оплаты счета | String |
| account | Идентификатор пользователя в системе мерчанта | String |
| comment | Комментарий к счету | String(255) |
| customFields | Дополнительные данные счета. Список полей см. в описании одноименного параметра в [запросе API выставления счета](#invoice_put) | Object |
| lifetime | Дата, до которой счет будет доступен для оплаты. Если счет не будет оплачен до этой даты, он получит финальный статус и последующая оплата станет невозможна.| `ГГГГ-ММ-ДДTччмм` |

### Открытие существующего счета {#openpopup}

>Пример вызова метода открытия существующего счета

~~~javascript
params = {
    payUrl: '<URL-ссылка на Платежную форму>'
}

QiwiCheckout.openInvoice(params)
    .then(data => {
        // ...
    })
    .catch(error => {
        // ...
    })
~~~

Этот метод используется, когда ссылка на Платежную форму оплаты счета получена при [выставлении счета через API](#qiwi-form-invoice-api).

Чтобы открыть форму оплаты выставленного счета, вызовите метод `QiwiCheckout.openInvoice`.  Параметры метода:

| Параметр | Описание | Формат |
|--------------|------------------------|-------------|
| payUrl | **Обязательный параметр**. URL-ссылка на Платежную форму | String |

## Настройка Платежной формы {#custom-form}

Внешний вид Платежной формы можно настроить в соответствии со стилем ТСП, включая лого, фон и цвет кнопок. Для этого обратитесь в [Службу поддержки](mailto:payin@qiwi.com) и предоставьте следующую информацию:

* уникальный псевдоним стиля, привязанный к идентификатору `siteId` (цифры, латинские буквы и символ дефиса `-`);
* название мерчанта, которое будет отображаться на форме;
* лого в формате PNG или SVG и размера 48x48 или пропорционально больше;
* цвет кнопок в HEX-формате.

Необязательные данные для настройки:

* ссылка на оферту сервиса ТСП.

>Пример передачи параметра стиля при выставлении счета через API

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2024-03-13T14:30:00+03:00",
   "customer": {},
   "customFields": {
     "themeCode":"merchant01-theme01"
   }
}
~~~

Чтобы применить выбранный стиль на Платежной форме:

* Передайте [при выставлении счета через API](#invoice_put) в поле `"themeCode"` JSON-объекта `customFields` псевдоним стиля, который вы указали при настройке. Например:

   `"themeCode":"merchant01-theme01"`.
* Передайте [в прямом вызове Платежной формы](#https-qiwi-form) в параметре `extras[themeCode]` псевдоним стиля, который вы указали при настройке. Например:

   `...&extras[themeCode]=merchant01-theme01`.

Название псевдонима стиля регистрозависимое.

Пример применения настройки к Платежной форме:

![Customer form](/images/payin/qiwi-form-custom.png)

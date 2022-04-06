# Проверка карты покупателя {#check-card}

Мерчант может воспользоваться сервисом проверки реквизитов карты на валидность и доступность для совершения покупок. При этом средства на счете держателя карты не списываются до того, как будут установлены договоренности на рекуррентные списания или будет инициирована транзакция покупки на всю сумму.

Если проверка пройдена успешно, для карты может быть выпущен [платежный токен](#merchant-token-pay).

<aside class="warning">
Платежный токен будет связан с идентификатором сайта и идентификатором Покупателя, которые вы указали в запросе "Проверка карты". Покупатель сможет совершать оплату платежным токеном только на этом сайте.

Чтобы платежный токен действовал на других сайтах одного мерчанта, отправьте запрос в Службу поддержки.
</aside>

Сервис проверки карт по умолчанию отключен. Чтобы подключить его, обратитесь к вашему сопровождающему менеджеру.

## Как использовать сервис через Платежную форму QIWI {#how-to-check-card-qiwi-payform}

>Пример запроса выставления счета с проверкой карты

~~~http
PUT /partner/payin/v1/sites/site-01/bills/892323232111 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd1xxxx356f9
Content-type: application/json
Host: api.qiwi.com

{
    "expirationDateTime": "2023-09-14T14:30:00+03:00",
    "customer": {
        "account":"test-123"
    },
    "flags": ["CHECK_CARD","BIND_PAYMENT_TOKEN"]
}
~~~

>Пример тела успешного ответа

~~~json
{
    "siteId": "site-01",
    "billId": "892323232111",
    "amount": {
      "value": 0.00,
      "currency": "RUB"
    },
    "status": {
      "value": "CREATED",
      "changedDateTime": "2021-08-13T15:43:41"
    },
    "creationDateTime": "2021-08-13T15:43:41",
    "expirationDateTime": "2023-09-14T14:30:00",
    "payUrl": "https://oplata.qiwi.com/validation/card?invoiceUid=fxxxxx-854c-4e56-xxxx-eb49aef2xxxx"
}
~~~

>Пример уведомления с результатом проверки карты

~~~json
{
   "checkPaymentMethod":{
      "status":"SUCCESS",
      "isValidCard":true,
      "threeDsStatus":"WITHOUT",
      "cardInfo":{
         "issuingCountry":"0",
         "issuingBank":"not present",
         "paymentSystem":"VISA",
         "fundingSource":"UNKNOWN",
         "paymentSystemProduct":"UNKNOWN"
      },
      "createdToken":{
         "token":"xxxxxxx-a53a-4de1-8aa4-xxxxxxx",
         "name":"411111******1111",
         "expiredDate":"2021-11-30T00:00:00+03:00",
         "account":"some_account"
      },
      "requestUid":"892323232111",
      "paymentMethod":{
         "type":"CARD",
         "maskedPan":"411111******1111",
         "cardHolder":"",
         "cardExpireDate":"11/2021"
      },
      "checkOperationDate":"2021-08-13T15:44:01+03:00"
   },
   "type":"CHECK_CARD",
   "version":"1"
}
~~~

1. Отправьте [запрос создания счета](#invoice_put) с дополнительным параметром `"flags":["CHECK_CARD", "BIND_PAYMENT_TOKEN"]`. Для генерации платежного токена в запросе должен быть указан параметр `customer.account` — уникальный идентификатор покупателя в системе ТСП. Не указывайте сумму счета. Извлеките из ответа параметр `billId` — он понадобится в п.4.
2. [Перенаправьте покупателя на Платежную форму](#qiwi-redirect) — ссылка на нее находится в параметре `payUrl` ответа.
3. На Платежной форме покупатель указывает реквизиты карты и отправляет их на проверку. На форме выполняется аутентификация покупателя (3-D Secure).
    ![check card](/images/check-card-payin.png)

4. Дождитесь завершения проверки карты: вам придет [уведомление CHECK_CARD](#check-card-callback) с результатом, или [запросите текущий статус проверки](#card-check-info) — в качестве уникального идентификатора проверки карты укажите `billId` из п.1. Результат проверки:

   * Информация о доступности карты для списаний — в атрибуте `isValidCard` (`true` — номер карты валиден).
   * Данные платежного токена — в объекте `createdToken`.

## Как использовать сервис через API {#how-to-check-card}

>Пример запроса проверки карты

~~~http
PUT /partner/payin/v1/sites/site-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9 HTTP/1.1
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

>Пример тела успешного ответа

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

> Пример тела ответа с проверкой 3DS

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "WAITING_3DS",
    "requirements": {
        "pareq": "Some string pareq",
        "acsUrl": "http://xxxxxxx"
    }
}
~~~

>Пример тела ответа с ошибкой проверки

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "ERROR"
}
~~~

>Пример запроса завершения 3DS при проверке карты

~~~http
POST /partner/payin/v1/sites/site-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "pares": "Some string pares"
}
~~~

>Пример тела ответа

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

1. Отправьте [запрос API "Проверка карты"](#card-check-api). В запросе укажите уникальный в рамках вашего сайта идентификатор проверки (`requestUid`). Для генерации платежного токена в запросе должен быть указан параметр `tokenizationData.account` — уникальный идентификатор покупателя в системе ТСП.
2. В ответе информация о доступности карты для списаний содержится в атрибуте `isValidCard` (`true` — номер карты валиден). Данные платежного токена возвращаются в объекте `createdToken`.

Чтобы убедиться, что номер карты ввел именно держатель карты, вы можете использовать дополнительную аутентификацию покупателя 3-D Secure в сервисе проверки карты. Включение/отключение 3DS производится на стороне QIWI через поддержку. Если 3DS включен, то в ответе на запрос проверки карты вы получите объект `"requirements"` с ACS URL для перенаправления покупателя (в поле `status` будет значение `"WAITING_3DS"`).

Сценарий проверки аналогичен [операции покупки](#merchant-threeds):

1. [Перенаправьте покупателя на страницу аутентификации](#merchant-threeds).
2. Завершите 3-D Secure [запросом "Завершение 3DS при проверке карты"](#card-check-complete). В запросе укажите тот же идентификатор проверки, что и в исходном запросе проверки карты.
3. Если проверка 3-D Secure завершилась успешно, в ответе информация о доступности карты для списаний содержится в атрибуте `isValidCard` (`true` — номер карты валиден). Данные платежного токена возвращаются в объекте `createdToken`.

После завершения проверки вам придет [уведомление CHECK_CARD](#check-card-callback) с результатом. Также вы можете всегда [запросить текущий статус проверки](#card-check-info).

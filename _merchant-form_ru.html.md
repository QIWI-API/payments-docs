# Платеж через форму мерчанта {#merchant-api-integration}

<aside class="warning">
При интеграции со своей платежной формой вы отправляете в API запросы с полными номерами карт и кодом CVV/CVV2. Это допускается только при наличии PCI DSS сертификата у вашей организации.

PCI DSS — стандарт информационной безопасности, принятый в индустрии платежных карт Visa и MasterCard. Соблюдать требования стандарта обязаны все компании, которые принимают карты к оплате.
</aside>

При подключении платежей через собственную платежную форму по умолчанию сразу доступен способ оплаты [Банковские карты](#qiwi-form-card). Другие способы оплаты доступны по запросу:

* [Платежные токены карт и QIWI Кошелька](#merchant-token-pay).
* [Yandex Pay](#merchant-form-yandexpay).
* [Mir Pay](#merchant-form-mirpay).
* [Система Быстрых Платежей (СБП)](#merchant-form-sbp).
* [Баланс мобильного телефона](#merchant-form-phone-account)

## Процесс платежа {#flow-payment-merchant-form}

<div class="mermaid">
sequenceDiagram
participant customer as Покупатель
participant store as Магазин
participant qb as QIWI (эквайер)
participant ips as Эмитент
customer->>store:Выбор товаров, Старт оплаты,<br/>ввод платежных данных
activate store
store->>qb:API: запрос "Платеж"<br/>Одношаговый платеж — все способы оплаты<br/>Двухшаговый платеж — только карты
activate qb
qb->>store:Статус операции, данные для 3DS или QR-код СБП
rect rgb(255, 238, 223)
Note over customer, ips:3-D Secure
store->>customer:Переадресация покупателя на acsUrl<br/>или в приложение банка (СБП)
activate ips
ips->>customer:Аутентификация покупателя:<br/>3DS — карты,<br/>СБП — подтверждение операции в интерфейсе эмитента карты
customer->>ips:Аутентификация
ips->>store:Результат аутентификации (PaRes)
store->>qb:API: запрос "Завершение аутентификации клиента"
end
qb->>ips:Запрос списания денежных средств
activate ips
ips->>qb:Статус операции
qb->>store:Уведомление о статусе операции
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

Чтобы создать платеж, передайте в запросе API [Платеж](#payments):

* ключ API;
* сумму платежа;
* метод платежа;
* другую информацию для создания платежа.

## Банковская карта {#merchant-form-card}

Протокол приема платежей поддерживает как двухшаговый платеж с холдированием средств на карте покупателя, так и одношаговый платеж без авторизации покупателя.

### Создание платежа {#merchant-form-hold}

>Пример платежа с холдированием (двухшаговый платеж)

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

>Пример платежа с немедленной оплатой (одношаговый платеж)

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
  },
  "flags": [ "SALE" ]
}
~~~

Чтобы инициировать платеж с предварительным холдированием средств на карте (двухшаговый платеж), передайте в запросе API [Платеж](#payments):

* ключ API;
* сумму платежа;
* метод платежа `CARD` и карточные данные покупателя;
* другая информация для создания платежа.

В двухшаговом платеже [возмещение](#reimburse) формируется только после [подтверждения платежа](#merchant-capture).

<a name="one-step-merchant"></a>

<aside class="notice">
По умолчанию, при холдировании сервис QIWI ожидает <a href="#merchant-capture">подтверждения платежа</a> в течение 72 часов. По истечении срока выполняется автоподтверждение платежа. Чтобы увеличить или уменьшить период ожидания, или настроить автоотмену платежа обратитесь в Службу поддержки. Период ожидания не может длиться более 5 суток.
</aside>

Для одношагового платежа без холдирования укажите в запросе API [Платеж](#payments) параметр `"flags":["SALE"]`. Если не передать этот параметр, то будет выполнено безусловное холдирование средств для выполнения платежа и сервис будет ожидать <a href="#merchant-capture">подтверждения платежа</a>.

### Ожидание аутентификации покупателя (3-D Secure) {#merchant-threeds}

>Пример ответа с требованием аутентификации покупателя

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

> Перенаправление для аутентификации 3-D Secure

~~~html
<form name="form" action="{ACSUrl}" method="post" >
        <input type="hidden" name="TermUrl" value="{TermUrl}" >
        <input type="hidden" name="MD" value="{MD}" >
        <input type="hidden" name="PaReq" value="{PaReq}" >
</form>
~~~

> Завершение аутентификации покупателя

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

Если требуется 3-D Secure аутентификация покупателя, в ответе на запрос платежа добавляется объект `requirements.threeDS` с полями:

- `acsUrl` — URL сервера аутентификации 3-D Secure, для перенаправления на страницу подтверждения от эмитента;
- `pareq` — зашифрованный запрос на аутентификацию 3-D Secure.

Для дополнительной проверки покупателя у эмитента выполните POST-запрос на URL сервера аутентификации 3-D Secure с параметрами:

- `TermUrl` — URL перенаправления покупателя после успешной аутентификации 3-D Secure;
- `MD` — уникальный идентификатор транзакции;
- `PaReq` — значение параметра `pareq` из ответа на платежный запрос.

Чтобы сохранять обратную совместимость, использование протокола 3-D Secure 1.0 или 3-D Secure 2.0 не влияет на вашу интеграцию с API.

Далее информация о покупателе передаётся в платежную систему карты. Банк-эмитент либо предоставляет разрешение на списание средств без аутентификации (frictionless flow), либо принимает решение о необходимости аутентификации с помощью одноразового пароля (challenge flow). После прохождения проверки покупатель перенаправляется по адресу `TermUrl` с зашифрованным результатом проверки в параметре `PaRes`.

Чтобы завершить аутентификацию покупателя, передайте в запросе API [Завершение аутентификации клиента](#payment_complete):

* уникальный ID мерчанта;
* номер платежа (параметр `paymentId`) из ответа на запрос платежа;
* результат 3-D Secure (значение параметра `PaRes`).

### Подтверждение платежа {#merchant-capture}

>Пример уведомления

~~~json
{
  "payment":{
    "paymentId":"804900",  <==paymentId, необходимый для подтверждения
    "type":"PAYMENT",
    "createdDateTime":"2020-11-28T12:58:49+03:00",
    "status":{
        "value":"SUCCESS",
        "changedDateTime":"2020-11-28T12:58:53+03:00"
    },
    "amount":{
      "value":100.00,
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

>Подтверждение платежа

~~~http
PUT /partner/payin/v1/sites/{siteId}/payments/804900/capture/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

**Это действие требуется только для двухшагового платежа с холдированием**.

Чтобы подтвердить платеж:

* Получите `paymentId` платежа:
  * Из [серверного уведомления](#payment-callback) после успешного холдирования средств.
  * Из ответа на запрос [Статус платежа](#payment_get).
* Отправьте запрос API [Подтверждение платежа](#capture) с полученным `paymentId`.

## Платежный токен {#merchant-token-pay}

>Использование платежного токена в запросе платежа

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
    "paymentToken" : "66aebf5f-098e-4e36-922a-a4107b349a96"
  },
  "customer": {
        "account": "token324"
  }
}
~~~

Платежные токены используются для списаний с карт или QIWI кошельков без ввода реквизитов карты или номера кошелька. Метод оплаты платежным токеном по умолчанию отключен. Чтобы подключить его, обратитесь к вашему сопровождающему менеджеру.

О выпуске платежного токена см. подробнее в [этом разделе](#payment-token-issue).

<aside class="warning">
Покупатель может совершать оплату только на том сайте, для которого был выпущен платежный токен.

Чтобы платежный токен действовал на других сайтах, отправьте запрос в Службу поддержки.
</aside>

Чтобы оплатить покупку платежным токеном, передайте в запросе API [Платеж](#payment):

* платежный токен в объекте `paymentMethod`,
* идентификатор покупателя, для которого был выпущен платежный токен, в параметре `customer.account`.

Параметр|Тип|Описание
--------|---|--------
paymentMethod.type|String|Тип операции. Только `TOKEN`
paymentMethod.paymentToken|String| Строка платежного токена
customer.account|String|Уникальный идентификатор Покупателя в системе ТСП, для которого выпущен платежный токен. **Без этого параметра оплата платежным токеном невозможна.**

Покупатель не будет указывать свои карточные данные и проходить проверку 3-D Secure.

## Yandex Pay {#merchant-form-yandexpay}

Оплата покупок с Yandex Pay происходит без ввода данных карты.

Для включения способа оплаты Yandex Pay обратитесь к вашему сопровождающему менеджеру.

### Как отправлять платеж {#merchant-form-yandexpay-payment}

> Пример платежа с данными расшифрованного платежного токена Yandex Pay (метод CLOUD_TOKEN)

~~~json
{
  "paymentMethod": {
    "type": "CARD",
    "pan": "4444443616621049",
    "expiryDate": "12/19",
    "holderName": "Yandex Pay",
    "external3dSec": {
      "type": "YANDEX_PAY",
      "cryptogram": "AOLqt9wP++YAoABFA==",
      "eciIndicator": "05"
    }
  },
  "amount": {
    "value": 5900.00,
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

> Пример платежа с данными расшифрованного платежного токена Yandex Pay (метод PAN_ONLY)

~~~json
{
  "paymentMethod": {
    "type": "CARD",
    "pan": "4444443616621049",
    "expiryDate": "12/19",
    "holderName": "Yandex Pay",
    "external3dSec": {
      "type": "YANDEX_PAY"
    }
  },
  "amount": {
    "value": 760.00,
    "currency": "RUB"
  },
  "flags": [
    "SALE"
  ],
  "customer": {
    "account": "11111",
    "email": "test@qiwi.com",
    "phone": "79111111111"
  }
}
~~~

Формат платежных данных зависит от способа аутентификации, указанного в поле `authMethod` расшифрованного платежного токена Yandex Pay:

* `CLOUD_TOKEN`. Без дополнительной аутентификации покупателя.
  
  Передайте в запросе API [Платеж](#payments) объект `paymentMethod` с параметрами:

  * `type` — всегда `CARD`;
  * данные из расшифрованного платежного токена Yandex Pay:
     * PAN карты в поле `"pan"`;
     * срок действия в формате `MM/YY` в поле `"expiryDate"`;
     * объект `external3dSec` с элементами:
       * `type` — всегда `YANDEX_PAY`;
       * `cryptogram` — содержимое поля `cryptogram` платежного токена Yandex Pay (Base64-закодированная строка);
       * `eciIndicator` — ECI-индикатор. Необходимо передавать, если поле `eciIndicator` получено в платежном токене Yandex Pay. В противном случае параметр не передавать.

* `PAN_ONLY`. С дополнительной аутентификацией покупателя (3-D Secure).
  
  Передайте в запросе API [Платеж](#payments) объект `paymentMethod` с параметрами:

  * `type` — всегда `CARD`;
  * данные из расшифрованного платежного токена Yandex Pay:
     * PAN карты в поле `"pan"`;
     * срок действия в формате `MM/YY` в поле `"expiryDate"`;
     * объект `external3dSec` с полем `type=YANDEX_PAY`.

## Mir Pay {#merchant-form-mirpay}

Клиент оплачивает покупку с Mir Pay без указания данных карты, через приложение Mir Pay.

Для включения способа оплаты Mir Pay обратитесь к вашему сопровождающему менеджеру.

Сценарий оплаты:

1. Клиент выбирает способ оплаты Mir Pay.
2. Платёжная форма зашифровывает информацию о покупке (сумма, валюта и т. д.) по технологии Deep Link или Universal Link согласно спецификации НСПК для In-Application операций и перенаправляет клиента в приложение Mir Pay.
3. Клиент подтверждает оплату в приложении.
4. Приложение передает криптограмму платежа (JWT-токен) в [формате JWE](https://datatracker.ietf.org/doc/html/rfc7519) мерчанту.
5. Мерчант отправляет JWT-токен в [платёжном запросе к API](#payments). Токен может быть отправлен как в [зашифрованном](#merchant-form-mirpay-payment), так и в [расшифрованном](#merchant-form-mirpay-decrypted) виде. В последнем случае мерчант должен быть подключен как агрегатор.

<aside class="warning">
Значения параметров <code>sum</code> и <code>cur</code>, использованные при формировании Deep Link или Universal Link с данными In-Application операции, должны совпадать с <code>amount.value</code> и <code>amount.currency</code> в платёжном запросе API. В ином случае платёж будет отклонён с ошибкой.
</aside>

### Как отправлять платеж с расшифрованными данными {#merchant-form-mirpay-decrypted}

> Пример оформленного платежа

~~~json
{
  "paymentMethod": {
    "type": "CARD",
    "pan": "4444443616621049",
    "expiryDate": "12/19",
    "external3dSec": {
        "type": "MIR_PAY",
        "cav": "3D6FC826A08C82B89780029F69670FDDCF299B",
        "transId": "5ab52487-177f-464b-b695-2954ffc44a13",
        "mid": "000000000000001",
        "media": "DL",
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

Укажите в  объекте `paymentMethod` запроса API [Платёж](#payments) объект `paymentMethod` с параметрами

* `type` — всегда `CARD`;
* данные из расшифрованного платежного токена Mir Pay:
  * токенизированный номер карты в поле `"pan"`;
  * срок действия (в формате `MM/YY`) в поле `"expiryDate"`;
  * объект `external3dSec` с элементами:
    * `type` — всегда `MIR_PAY`;
    * `cav` — In-Application криптограмма (в HEX-формате длиной 38 символов);
    * `transId` — идентификатор In-Application операции (в формате UUID);
    * `mid` — идентификатор ТСП (MerchantId) в системе агрегатора;
    * `media` — тип источника, через который инициирована In-Application операция. Допустимые значения:
      * `ISDK` (In-Application SDK),
      * `DL` (Deep link),
      * `UL` (Universal link),
      * `TR` (In-Application Mir HCE SDK).

### Как отправлять платеж с зашифрованными данными {#merchant-form-mirpay-payment}

> Пример оформленного платежа

~~~json
{
  "paymentMethod": {
    "type" : "MIR_PAY_TOKEN",
    "cryptogram" : "eyJhbGciOiJSUzI1NiIsImN0eSI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwiaWF0IjoxNjAzMzc2MDExfQ.Ce16subvpzUm-xKTdInJ4Nxr75mzpDG2sFrjmdi02vvVmPhcYRju7ulVaZ0wLZqnjnM6XeXR6FZ473-cVMPAJ_O2wTr-EP6O2FnYUCzrrhQWCv5_4-Xk6QNSFD5bsHB8xTPYSsWilPvZuD5rhp80HpztJ0y95bZau8RQsJTVRPE"
  },
  "amount": {
    "value": 3300.00,
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

Укажите в  объекте `paymentMethod` запроса API [Платёж](#payments) объект `paymentMethod` с параметрами

* `type` — всегда `MIR_PAY_TOKEN`;
* `cryptogram` — [JWE-токен](https://www.rfc-editor.org/rfc/rfc7516) с зашифрованными данными In-Application операции, полученный от приложения Mir Pay после подтверждения платежа клиентом.

## Оплата через СБП {#merchant-form-sbp}

**Протокол приема платежей** поддерживает списание средств с покупателя через [Систему быстрых платежей](https://sbp.nspk.ru/) (СБП). Через СБП можно выполнять платежи в пользу юридических лиц, в том числе с использованием QR-кодов.

По умолчанию прием оплаты через СБП отключен. Чтобы подключить этот способ оплаты, обратитесь к вашему сопровождающему менеджеру.

### Получение QR-кода {#qr-sbp}

> Пример тела запроса для платежа через СБП

~~~json
{
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
    "image": {
      "mediaType": "image/png",
      "width": 300,
      "height": 300
    }
  },
  "paymentPurpose": "Flower for my girlfriend",
  "redirectUrl": "http://someurl.com"
}
~~~

> Пример ответа c QR-кодом

~~~json
{
  "qrCodeUid": "Test12",
  "amount": {
    "currency": "RUB",
    "value": "100.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
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

При оплате через СБП покупатель сканирует QR-код и получает ссылку на платеж, которую можно открыть в приложении своего банка.

Для выпуска QR-кода СБП отправьте запрос API [Получение QR-кода СБП](#qr-code-sbp). В запросе укажите:

* Уникальный идентификатор запроса.
* Объект `qrCode` с характеристиками запрашиваемого QR-кода:
   * Тип QR-кода в параметре `qrCode.type`:
     * `DYNAMIC` — динамический QR-код, индивидуальный для каждой оплаты.
   * Срок действия кода в минутах в параметре `qrCode.ttl`. По истечении срока QR-код деактивируется. По умолчанию срок действия 72 часа.
   * Тип и размер изображения QR-кода в блоке `qrCode.image`.
* Сумму платежа в блоке `amount`.
* Параметр `paymentPurpose` с описанием платежа. Если не указан, в приложении банка покупателя отобразится название вашего магазина.

В ответе на запрос в объекте `qrCode` содержатся данные QR-кода:

* `image.content` — base64-encoded QR-код. После расшифровки вы получите изображение для отображения покупателю.
* `payload` — URL-based QR для перенаправления покупателя в приложение банка.

### Статус платежа через СБП {#sbp-status}

После перехода платежа в финальный статус вы получите [уведомление](#payment-callback) с указанным в исходном запросе идентификатором выпуска QR-кода в поле `qrCodeUid`. Актуальный статус платежа по идентификатору платежа `paymentId` из уведомления можно [получить через API](#payment_get).

### Статус QR-кода {#qr-code-status}

> Пример ответа на запрос статуса QR-кода

~~~json
{
  "qrCodeUid": "Test",
  "amount": {
    "currency": "RUB",
    "value": "1.00"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 999,
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

Используйте запрос [Статус QR-кода СБП](#qr-code-sbp-get). В ответе возвращается информация о QR-коде, в том числе его текущий статус. Так вы можете определить действует ли QR-код.

### Оплата токеном через СБП {#token-sbp-payment}

> Пример тела запроса оплаты токеном СБП

~~~json
{
  "tokenizationAccount": "customer123",
  "token": "c5ba4a05-21c9-4a36-af7a-b709b4caa4d6"
}
~~~

О выпуске платежного токена читайте подробнее в [этом разделе](#merchant-form-token-issue-sbp).

<aside class="warning">
Покупатель может совершать оплату только на том сайте, для которого был выпущен платежный токен.

Чтобы платежный токен действовал на других сайтах, отправьте запрос в Службу поддержки.
</aside>

Воспользуйтесь методом API [Платеж токеном СБП](#payment-sbp-token) и передайте в запросе:

* платежный токен в параметре `token`,
* идентификатор покупателя, для которого был выпущен платежный токен, в параметре `tokenizationAccount`.

### Тестирование оплаты СБП {#test-sbp}

См. информацию в [этом разделе](#test_data_sbp).

## Оплата со счета мобильного телефона {#merchant-form-phone-account}

Оплата покупок со счета мобильного телефона происходит без ввода данных карты. Сразу после инициирования платежа покупатель получает SMS-сообщение от своего мобильного оператора с информацией о платеже и подтверждает или отклоняет оплату ответным SMS.

Для включения этого способа оплаты обратитесь к вашему сопровождающему менеджеру.

### Как отправлять платеж {#merchant-form-mobile-payment}

> Пример платежа

~~~json
{
  "paymentMethod": {
    "type": "MOBILE_COMMERCE",
    "phone": "+79111111111"
  },
  "amount": {
    "value": 5900.00,
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

При отправке платежа укажите в блоке `paymentMethod` в запросе API [Платеж](#payments) параметры:

* `type` — всегда `MOBILE_COMMERCE`.
* `phone` — номер телефона, с баланса которого выполняется оплата. Номер указывается в международном формате со знаком `+`.

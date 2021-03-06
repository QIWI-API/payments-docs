# Платеж через форму QIWI {#invoicing}

При подключении платежей через форму QIWI по умолчанию доступен способ оплаты [Банковские карты](#qiwi-form-card). Следующие способы оплаты подключаются по запросу:

* [Платежные токены карт](#qiwi-form-cardtoken).
* [Apple Pay/Google Pay](#qiwi-form-applepay).
* [QIWI Кошелек](#qiwi-form-wallet).

<aside class="notice">
На Платежной форме QIWI способы оплаты Apple Pay/Google Pay и QIWI Кошелек для одного и того же идентификатора <code>siteId</code> не могут быть использованы одновременно. Создайте два отдельных идентификатора для Apple Pay/Google Pay и QIWI Кошелька.
</aside>

## Процесс платежа {#flow-payment-qiwi-form}

<div class="mermaid">
sequenceDiagram
participant customer as Покупатель
participant store as Магазин
participant qb as QIWI (эквайер)
participant ips as Эмитент
customer->>store:Выбор товаров, Старт оплаты
activate store
store->>qb:Выставление счета<br/>Одношаговый платеж — все способы оплаты<br/>Двухшаговый платеж — только карты и КИВИ Кошелек
activate qb
qb->>store:Ссылка на платежную форму QIWI (payUrl)
store->>customer:Переадресация покупателя на payUrl
customer->>qb:Открытие платежной формы,<br/>выбор способа оплаты,<br/>указание платежных данных для выбранного способа
qb->>customer:Аутентификация покупателя:<br/>Для карт — 3DS, для QW — авторизация в КИВИ кошельке
customer->>qb:Аутентификация
qb->>ips:Запрос списания денежных средств
activate ips
ips->>qb:Статус операции
qb->>store:Уведомление о статусе операции
qb->>customer: Переадресация на страницу статуса платежа (successUrl)
rect rgb(237, 243, 255)
Note over store, ips:Двухшаговый платеж
store->>qb:Подтверждение операции (capture)
qb->>ips:Подтверждение списания
deactivate ips
qb->>store:Уведомление о подтверждении платежа
end
deactivate qb
deactivate store
</div>

Чтобы создать счет для платежа, передайте в запросе API [Создание счета](#invoice_put):

* ключ API;
* сумму счета;
* другие данные для выставления счета;

либо перенаправьте покупателя [по прямой ссылке](https-qiwi-form) на форму QIWI.

## Интеграция c Платежной формой QIWI без использования API {#https-qiwi-form}

<aside class="warning">
При использовании этого способа нельзя гарантировать, что все счета выставлены мерчантом, в отличие от выставления по API.
</aside>

Это простой способ интеграции с Платежной формой QIWI. При открытии формы покупателю автоматически выставляется счет. Параметры счета передаются в открытом виде в ссылке. Покупателю отображается платежная форма с выбором способов оплаты.

Для интеграции обратитесь в Службу поддержки для выпуска ключа `PUBLIC_KEY` и указывайте его во всех ссылках. Для каждого `siteId` выпускается уникальный ключ.

При оплате счета, выставленного таким способом, платеж автоматически авторизуется без участия мерчанта. Подключите автоматическое подтверждение платежа в Службе поддержки, чтобы не было необходимости выполнять запрос [Завершение аутентификации](#capture).

<aside class="notice">
По умолчанию, при оплате счета, выставленного по ссылке, сервис QIWI ожидает подтверждения платежа в течение 72 часов. Вы можете подтвердить такой платеж через Личный кабинет. По истечении срока выполняется автоподтверждение платежа. Чтобы уменьшить период ожидания, или настроить автоотмену платежа обратитесь в службу поддержки.
</aside>

<h3 class="request method" id="payform_flow">REDIRECT → </h3>

~~~shell
https://oplata.qiwi.com/create?publicKey=e1ZuGvjEoxxGHUz3raU1hZm2LyrSqxt37XGRtQhMWZrkw2VT8truUtaEBpY833Wxand5eX3MUFUcEq5T3eae1GdQQbQBgJwtVciAVG3btyZbLr4KABGrny2&comment=some%20comment&extras[cf1]=custom%20field%201&amount=14.88
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/create</span></h3></li>
</ul>


<aside class="notice">
При открытии ссылки на форму в webview на смартфонах Android обязательно включайте <code>settings.setDomStorageEnabled(true).</code>
</aside>

<ul class="nestedList params">
    <li><h3>Параметры</h3><span>В URL указываются параметры счета.</span></li>
</ul>

Параметр|Описание|Тип|Обяз.
---------|--------|---|---------|---|----
publicKey | Ключ идентификации мерчанта|String|+
billId|Уникальный идентификатор счета в системе мерчанта. Он должен генерироваться на вашей стороне любым способом. Идентификатором может быть любая уникальная последовательность букв или цифр. Также разрешено использование символа подчеркивания (`_`) и дефиса (`-`). Если не указан, то при каждом переходе по ссылке будет создаваться новый счет.|URL-закодированная строка String(200)|-
amount| Сумма покупки, округленная в меньшую сторону до 2 десятичных знаков (всегда в рублях) | Number(6.2)|-
phone | Номер телефона покупателя (в международном формате) | URL-закодированная строка|-
email | E-mail покупателя | URL-закодированная строка|-
comment | Комментарий к счету|URL-закодированная строка String(255)|-
successUrl|URL для переадресации в случае успешной оплаты. Ссылка должна вести на сайт мерчанта.|URL-закодированная строка|-
extras[cf1] | Поле с произвольной информацией, дополняющей данные счета |URL-закодированная строка| -
extras[cf2] | Поле с произвольной информацией, дополняющей данные счета|URL-закодированная строка | -
extras[cf3] | Поле с произвольной информацией, дополняющей данные счета|URL-закодированная строка | -
extras[cf4] | Поле с произвольной информацией, дополняющей данные счета|URL-закодированная строка | -
extras[cf5] | Поле с произвольной информацией, дополняющей данные счета|URL-закодированная строка | -

По умолчанию, после оплаты счета автоматически выполняется аутентификация покупателя. Завершение аутентификации также происходит автоматически.

## Банковская карта {#qiwi-form-card}

Протокол приема платежей поддерживает как двухшаговый платеж с холдированием средств на карте покупателя, так и одношаговый платеж без авторизации покупателя.

### Создание счета {#hold}

>Пример выставления счета с оплатой через холдирование (двухшаговый платеж)

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
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

>Пример выставления счета с оплатой без авторизации Покупателя (одношаговый платеж)

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

Чтобы выставить счет с оплатой через холдирование средств на карте (двухшаговый платеж), передайте в запросе API [Создание счета](#invoice_put):

* ключ API;
* сумму счета;
* дату, до которой необходимо оплатить счет;
* (опционально) другие данные для выставления счета:
  * данные о покупателе;
  * комментарий к счету;
  * другую информацию.

<a name="one_step">

Для оплаты счета без авторизации покупателя (одношаговый платеж) укажите в запросе API [Создание счета](#invoice_put) параметр `"flags":["SALE"]`. Если не передать этот параметр, то будет выполнено безусловное холдирование средств для оплаты счета.

После выставления счета [перенаправьте покупателя на Платежную форму](#qiwi-redirect) (ссылка на нее придет в параметре `payUrl` ответа).

Дождитесь успешного завершения платежа: подождите, когда придет [уведомление](#payment_callback), или периодически отправляйте запросы [Статус платежа](#invoice_get), чтобы получить информацию о платеже.

В двухшаговом сценарии платежа [возмещение](#reimburse) формируется только после [подтверждения покупки](#qiwi-capture).

<aside class="notice">
По умолчанию, при холдировании сервис QIWI ожидает <a href="#qiwi-capture">подтверждения платежа</a> в течение 72 часов. По истечении срока выполняется автоподтверждение платежа. Чтобы увеличить или уменьшить период ожидания, или настроить автоотмену платежа обратитесь в службу поддержки. Период ожидания не может длиться более 5 суток. 
</aside>

### Подтверждение платежа {#qiwi-capture}

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
    "paymentMethod":"..",
    "customer":"..",
    "gatewayData":"..",
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

Это действие требуется для двухшагового сценария (платеж с холдированием средств).

<aside class="notice">
По умолчанию, при холдировании сервис QIWI ожидает подтверждения платежа в течение 72 часов. По истечении срока выполняется автоподтверждение платежа. Чтобы увеличить или уменьшить период ожидания, или настроить автоотмену платежа обратитесь в службу поддержки. Период ожидания не может длиться более 5 суток. 
</aside>

Чтобы подтвердить платеж:

* Получите `paymentId` платежа:
     * Из уведомления. После успешного холдирования средств вам придет [серверное уведомление](#payment_callback).
     * Из отета на запрос [Статус счета](#invoice_get).
* Отправьте запрос API [Подтверждение платежа](#capture) с полученным `paymentId`.

## Платежный токен {#qiwi-form-cardtoken}

> Выставление счета с оплатой платежным токеном

~~~http
PUT /partner/payin/v1/sites/test-02/bills/1815 HTTP/1.1
Accept: application/json
Authorization: Bearer 7uc4b25xx93xxx5d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
   "amount": {  
     "currency": "RUB",  
     "value": 100.00
   },
   "comment": "Text comment",
   "expirationDateTime": "2018-04-13T14:30:00+03:00",
   "customer": {
     "account": "token234"
   },
   "customFields": {
    "cf1": "Some data",
    "FROM_MERCHANT_CONTRACT_ID": "1234"
   }  
   }
}
~~~

Платежные токены используются для списаний с карт или QIWI кошельков без ввода реквизитов карты или номера кошелька. Метод оплаты платежным токеном по умолчанию отключен. Чтобы подключить его, обратитесь к вашему сопровождающему менеджеру. 

О выпуске платежного токена читайте [в разделе по ссылке](#payment-token-issue).

<aside class="warning">
Покупатель может совершать оплату платежным токеном только на том сайте, для которого был выпущен платежный токен.

Чтобы платежный токен действовал на других сайтах, отправьте запрос в Службу поддержки.
</aside>

Чтобы выставить счет с оплатой платежным токеном, передайте в запросе API [Создание счета](#invoice_put) следующие данные:

* Ключ API.
* Сумму счета;
* Дату, до которой необходимо оплатить счет;
* Идентификатор покупателя, для которого был выпущен платежный токен, в параметре `customer.account`. **Без данного параметра оплата платежным токеном невозможна.**
* Другую информацию о счете.

После выставления счета [перенаправьте покупателя на Платежную форму](#qiwi-redirect) (ссылка на нее придет в параметре `payUrl` ответа).

Если для покупателя был выпущен один или несколько платежных токенов, то на Платежной форме отобразятся его привязанные карты. 

![qiwi-form-tokens](/images/qiwi-form-token.png)

Чтобы воспользоваться платежным токеном, покупателю достаточно выбрать одну из своих привязанных карт. При этом не понадобится указывать карточные данные и проходить проверку 3-D Secure.

Для списания средств по платежному токену без участия Покупателя воспользуйтесь методом API [Платеж](#payment) — см. подробнее [описание использования платежного токена на Платежной форме мерчанта](#merchant-token-pay).

## Apple Pay/Google Pay {#qiwi-form-applepay}

Оплата покупок с Apple Pay происходит без ввода данных карты. Технология работает в мобильных приложениях и браузере Safari на iPhone, iPad, Apple Watch и MacBook.

В способе оплаты Google Pay™ поддерживаются платежи с карт Visa и MasterCard без ввода данных карты.

Чтобы добавить методы оплаты Apple Pay/Google Pay на Платежную форму, обратитесь к вашему сопровождающему менеджеру.

Чтобы выставить счет с оплатой Apple Pay/Google Pay, передайте в запросе API [Создание счета](#invoice_put):

* ключ API;
* сумму счета;
* дату, до которой необходимо оплатить счет;
* другую информацию о счете.

[Перенаправьте покупателя на Платежную форму](#qiwi-redirect) (ссылка на нее придет в параметре `payUrl` ответа).

[Уведомление о платеже](#payment_callback) для способов оплаты Apple Pay/Google Pay аналогично [оплате с банковской карты](#qiwi-form-card).

## QIWI Кошелек {#qiwi-form-wallet}

> Выставление счета для оплаты с КИВИ Кошелька

~~~http
PUT /partner/bill/v1/bills/893794793973 HTTP/1.1
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

По умолчанию прием оплаты с баланса КИВИ Кошелька отключен. Чтобы подключить этот способ оплаты, обратитесь к вашему сопровождающему менеджеру.

<aside class="notice">
Если у вас подключены способы оплаты платежным токеном или Apple Pay/Google Pay, то создайте отдельный идентификатор <code>siteId</code> для приема оплаты только с баланса КИВИ Кошелька.
</aside>

Чтобы выставить счет с оплатой КИВИ Кошельком, передайте в запросе API [Создание счета для оплаты с QIWI Кошелька](#invoice_qw_put):

* ключ API;
* сумму счета;
* дату, до которой необходимо оплатить счет;
* другую информацию о счете.

[Перенаправьте покупателя на Платежную форму](#qiwi-redirect) (ссылка на нее придет в параметре `payUrl` ответа). На Платежной форме покупателю будет доступен способ оплаты с баланса КИВИ Кошелька.

Процесс оплаты для покупателя выглядит следующим образом:

1. Покупатель открывает форму.
     
    ![Wallet Invoice](/images/wallet_invoice.jpg)
2. Покупатель авторизуется в КИВИ Кошельке.
    
    ![Wallet Auth](/images/wallet_auth.jpg)
3. Покупатель оплачивает счет.
    
    ![Wallet Pay](/images/wallet_pay.jpg)

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
    "payUrl": "https://oplata.qiwi.com/form/?invoice_uid=78d60ca9-7c99-481f-8e51-0100c9012087"
}
~~~

Чтобы покупатель смог оплатить выставленный счет, перенаправьте его на Платежную форму (ссылка на нее придет в поле `payUrl` ответа на запрос выставления счета).

<aside class="notice">
При открытии ссылки на форму в webview на смартфонах Android обязательно включайте <code>settings.setDomStorageEnabled(true)</code>
</aside>

> Пример ссылки с successUrl

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://merchant.ru/payments/#introduction
~~~

К ссылке можно добавить следующий параметр:

| Параметр | Описание | Тип |
|--------------|--------|-------------|
| successUrl | URL для переадресации в случае успешной оплаты. Переадресация произойдет после успешной 3DS аутентификации. Ссылку необходимо зашифровать в utf-8 формат. | URL-закодированная строка |

По умолчанию, на Платежной форме QIWI 3-D Secure обязателен.

## Настройка Платежной формы {#custom-form}

Внешний вид Платежной формы можно настроить в соответствии с вашим стилем, включая лого, фон и цвет кнопок. Для этого обратитесь в Службу поддержки <a href='mailto:payin@qiwi.com'>payin@qiwi.com</a> и предоставьте следующие данные:

* уникальный псевдоним стиля, привязанный к идентификатору `siteId` (цифры, латинские буквы и символ дефиса `-`);
* название мерчанта, которое будет отображаться на форме;
* краткое описание деятельности (не более 120 символов);
* лого в формате PNG или SVG и размера 310x36 или пропорционально больше;
* цвет фона и цвет кнопок в HEX-формате.

Необязательные данные для настройки:

* картинка для фона в формате PNG или SVG и размера 382x560 или пропорционально больше;
* варианты предвыбранных сумм платежа (не более трех);
* контактный e-mail для отображения на странице;
* URL страницы успешного платежа;
* номер счетчика Яндекс.Метрики;
* ссылка на оферту вашего сервиса.

>Пример передачи параметра стиля при выставлении счета

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
   "customFields": {
     "themeCode":"merchant01-theme01"
   }
}
~~~

Чтобы применить ваш стиль на Платежной форме, передайте [при выставлении счета](#invoice_put) в поле `"themeCode"` JSON-объекта `customFields` псевдоним стиля, который вы указали при настройке. Например: 

`"themeCode":"merchant01-theme01"`.

Название псевдонима стиля регистрозависимое.

![Customer form](/images/Custom.png)

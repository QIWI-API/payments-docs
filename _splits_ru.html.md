# Сплитование платежей {#payments_split}

Сплитование платежей — решение, разработанное специально для маркетплейсов. Сплитование платежей позволяет рассчитываться с несколькими поставщиками услуг, производя одно списание с карты покупателя.

Чтобы подключить сплитование платежей:

1. Обратитесь к вашему сопровождающему менеджеру и запросите подключение решения.
2. Выполните интеграцию согласно описанию в разделе [Регистрация мерчанта](#split-boarding).

## Интеграция с Платежной формой QIWI {#use-splits-qiwi-form}

Чтобы отправить платеж со сплитованием, передайте в запросе API [Создание счета](#invoice_put) массив<br/>`splits` c данными поставщиков.

### Описание данных

> Пример выставления счета со сплитованием

~~~shell
curl --location --request PUT 'https://api.qiwi.com/partner/payin/v1/sites/Obuc-02/bills/eqwptt' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer token \
--data-raw '{
    "amount": {
        "value": 3.00,
        "currency": "RUB"
    },
    "expirationDateTime": "2021-12-31T23:59:59+03:00",
    "comment": "сomment",
    "splits": [
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "Obuc-00",
            "splitAmount": {
                "value": 2.00,
                "currency": "RUB"
            },
            "orderId": "dressesforwhite",
            "comment": "платьица для Тани"
        },
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "Obuc-01",
            "splitAmount": {
                "value": 1.00,
                "currency": "RUB"
            },
            "orderId": "shoesforvalya",
            "comment": "туфельки для Вали"
        }
    ]
}
'
~~~

> Пример ответа при выставлении счета со сплитованием

~~~json
{
    "billId": "eqwptt",
    "invoiceUid": "44b2ef2a-edc6-4aed-87d3-01cf37ed2732",
    "amount": {
        "currency": "RUB",
        "value": "3.00"
    },
    "expirationDateTime": "2021-12-31T23:59:59+03:00",
    "status": {
        "value": "CREATED",
        "changedDateTime": "2021-02-05T10:21:17+03:00"
    },
    "comment": "сomment",
    "flags": [
        "TEST"
    ],
    "payUrl": "https://oplata.qiwi.com/form?invoiceUid=44b2ef2a-edc6-4aed-87d3-01cf37ed2732",
    "splits": [
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "Obuc-00",
            "splitAmount": {
                "currency": "RUB",
                "value": "2.00"
            },
            "orderId": "dressesforwhite",
            "comment": "платьица для Тани"
        },
        {
            "type": "MERCHANT_DETAILS",
            "siteUid": "Obuc-01",
            "splitAmount": {
                "currency": "RUB",
                "value": "1.00"
            },
            "orderId": "shoesforvalya",
            "comment": "туфельки для Вали"
        }
    ]
}
~~~

Описание массива `splits`:

Название | Тип | Описание
----|------|-------
splits | Array | Массив данных о поставщиках
-----|------|------
type | String | Тип передаваемых данных. Доступные значения: `MERCHANT_DETAILS` (данные поставщика)
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
splitAmount | Object | Возмещение поставщику
----|----|-----
value | Number | Сумма возмещения
currency | String(3) | Буквенный код валюты возмещения по ISO. Доступен только `RUB`
-----|-----|-----
orderId | String | Номер заказа (необязательный)
comment | String | Комментарий к заказу (необязательный)

<aside class="warning">Сумма возмещений поставщикам должна равняться сумме списания с карты плательщика.</aside>

В объекте `splits` ответа содержатся данные о сплитованных платежах и комиссиях:

Поле ответа | Тип | Описание
----|------|-------
splits | Array | Массив с данными о сплитованных платежах
-----|------|------
type | String | Тип передаваемых данных. Всегда возвращается строка `MERCHANT_DETAILS`
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
splitAmount | Object | Данные о возмещении поставщику
----|----|-----
value | String | Сумма возмещения
currency | String(3) | Буквенный код валюты возмещения по ISO
-----|-----|-----
orderId | String | Номер заказа
comment | String | Комментарий к заказу

## Интеграция с Платежной формой мерчанта {#use-splits-merchant-form}

Чтобы отправить платеж со сплитованием, передайте в запросе API [Платеж](#payments) JSON-массив `paymentSplits` с данными поставщиков.

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
   "paymentMethod": "..",
   "status": "..",
   "paymentCardInfo": "..",
   "paymentSplits": [
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

Описание массива `paymentSplits`:

Название | Тип | Описание
----|------|-------
paymentSplits | Array | Массив данных о поставщиках
-----|------|------
type | String | Тип передаваемых данных. Доступные значения: `MERCHANT_DETAILS` (данные поставщика)
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
splitAmount | Object | Возмещение поставщику
----|----|-----
value | Number | Сумма возмещения
currency | String(3) | Буквенный код валюты возмещения по ISO. Доступен только `RUB`
-----|-----|-----
orderId | String | Номер заказа (необязательный)
comment | String | Комментарий к заказу (необязательный)

<aside class="warning">Сумма возмещений поставщикам должна равняться сумме списания с карты плательщика.</aside>

В объекте `paymentSplits` ответа содержатся данные о принятых платежах и комиссиях:

Поле ответа | Тип | Описание
----|------|-------
paymentSplits | Array | Массив с данными о принятых платежах
-----|------|------
type | String | Тип передаваемых данных. Всегда возвращается строка `MERCHANT_DETAILS`
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
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

После успешной авторизации списания денежных средств доступен возврат средств по операции сплитованного платежа.

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

В запросе API [Операция возврата](#refund-api) передайте JSON-массив `refundSplits` с данными о возвратах поставщикам. Укажите общую сумму возврата и сумму возврата для каждого поставщика. Поддерживается как частичный, так и полный возврат. 

<aside class="warning">Сумма отмененных возмещений поставщикам должна равняться общей сумме возврата.</aside>

Описание массива `refundSplits`:

Название | Тип | Описание
----|------|-------
refundSplits | Array | Массив данных о возвратах поставщикам
-----|------|------
type | String | Тип передаваемых данных. Доступные значения: `MERCHANT_DETAILS` (данные поставщика)
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
splitAmount | Object | Данные об отмене возмещения поставщику
----|----|-----
value | Number | Сумма отмены возмещения
currency | String(3) | Буквенный код валюты отмены возмещения по ISO. Доступен только `RUB`
-----|-----|-----
orderId | String | Номер заказа (необязательный)
comment | String | Комментарий к заказу (необязательный)

В JSON-массиве `refundSplits` ответа содержатся данные о принятых возвратах:

Поле ответа | Тип | Описание
----|------|-------
refundSplits | Array | Массив данных о возвратах поставщикам
-----|------|------
type | String | Тип передаваемых данных. Всегда возвращается строка `MERCHANT_DETAILS`
siteUid | String | [Зарегистрированный ID поставщика](#split-boarding)
splitAmount | Object | Данные об отмене возмещения поставщику
----|----|-----
value | String | Сумма отмены возмещения
currency | String(3) | Буквенный код валюты 	 по ISO
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

## Уведомления по сплитованным операциям {#split-operations-notification}

> Пример уведомления по сплитованным платежам

~~~json
{
    "payment": {
        "paymentId": "134d707d-fec4-4a84-93f3-781b4f8c24ac",
        "customFields": {
            "comment": "сomment"
        },
        "paymentCardInfo": {
            "issuingCountry": "643",
            "issuingBank": "Unknown",
            "paymentSystem": "VISA",
            "fundingSource": "UNKNOWN",
            "paymentSystemProduct": "Unknown"
        },
        "type": "PAYMENT",
        "createdDateTime": "2021-02-05T11:29:38+03:00",
        "status": {
            "value": "SUCCESS",
            "changedDateTime": "2021-02-05T11:29:39+03:00"
        },
        "amount": {
            "value": 3,
            "currency": "RUB"
        },
        "paymentMethod": {
            "type": "TOKEN",
            "paymentToken": "1620161e-d498-431b-b006-c52bb78c6bf2",
            "maskedPan": "425600******0003",
            "cardHolder": "CARD HOLDER",
            "cardExpireDate": "11/2022"
        },
        "customer": {
            "email": "glmgmmxr@qiwi123.com",
            "account": "sbderxuftsrt",
            "phone": "13387571067",
            "country": "yccsnnfjgthu",
            "city": "sqdvseezbpzo",
            "region": "shbvyjgspjvu"
        },
        "gatewayData": {
            "type": "ACQUIRING",
            "authCode": "181218",
            "rrn": "123"
        },
        "billId": "autogenerated-19cf2596-62a8-47f2-8721-b8791e9598d0",
        "flags": [],
        "paymentSplits": [
            {
                "type": "MERCHANT_DETAILS",
                "siteUid": "Obuc-00",
                "splitAmount": {
                    "value": 2,
                    "currency": "RUB"
                },
                "splitCommissions": {
                    "merchantCms": {
                        "value": 0.2,
                        "currency": "RUB"
                    },
                    "userCms": null
                },
                "orderId": "dressesforwhite",
                "comment": "платьица для Тани"
            },
            {
                "type": "MERCHANT_DETAILS",
                "siteUid": "Obuc-01",
                "splitAmount": {
                    "value": 1,
                    "currency": "RUB"
                },
                "splitCommissions": {
                    "merchantCms": {
                        "value": 0.02,
                        "currency": "RUB"
                    },
                    "userCms": null
                },
                "orderId": "shoesforvalya",
                "comment": "туфельки для Вали"
            }
        ]
    },
    "type": "PAYMENT",
    "version": "1"
}
~~~

> Пример уведомления по возвратам сплитованных платежей

~~~json
{
    "refund": {
        "refundId": "42f5ca91-965e-4cd0-bb30-3b64d9284048",
        "type": "REFUND",
        "createdDateTime": "2021-02-05T11:31:40+03:00",
        "status": {
            "value": "SUCCESS",
            "changedDateTime": "2021-02-05T11:31:40+03:00"
        },
        "amount": {
            "value": 3,
            "currency": "RUB"
        },
        "paymentMethod": {
            "type": "TOKEN",
            "paymentToken": "1620161e-d498-431b-b006-c52bb78c6bf2",
            "maskedPan": null,
            "cardHolder": null,
            "cardExpireDate": null
        },
        "customer": {
            "email": "glmgmmxr@qiwi123.com",
            "account": "sbderxuftsrt",
            "phone": "13387571067",
            "country": "yccsnnfjgthu",
            "city": "sqdvseezbpzo",
            "region": "shbvyjgspjvu"
        },
        "gatewayData": {
            "type": "ACQUIRING",
            "authCode": "181218",
            "rrn": "123"
        },
        "billId": "autogenerated-19cf2596-62a8-47f2-8721-b8791e9598d0",
        "flags": [
            "REVERSAL"
        ],
        "refundSplits": [
            {
                "type": "MERCHANT_DETAILS",
                "siteUid": "Obuc-00",
                "splitAmount": {
                    "value": 2,
                    "currency": "RUB"
                },
                "splitCommissions": {
                    "merchantCms": {
                        "value": 0,
                        "currency": "RUB"
                    },
                    "userCms": null
                },
                "orderId": "dressesforwhite",
                "comment": ""
            },
            {
                "type": "MERCHANT_DETAILS",
                "siteUid": "Obuc-01",
                "splitAmount": {
                    "value": 1,
                    "currency": "RUB"
                },
                "splitCommissions": {
                    "merchantCms": {
                        "value": 0.02,
                        "currency": "RUB"
                    },
                    "userCms": null
                },
                "orderId": "shoesforvalya",
                "comment": ""
            }
        ]
    },
    "type": "REFUND",
    "version": "1"
}
~~~

Уведомления по сплитованным платежам и по возвратам сплитованных платежей формируются аналогично описанным выше ответам на запросы API:

* В тело [уведомления](#payment-callback) с типом `PAYMENT` добавляется JSON-массив `paymentSplits`, используемый для передачи данных о платежах поставщиков. Формат массива см. [выше](#use-splits-merchant-form). 
* В тело [уведомления](#refund-callback) с типом `REFUND` добавляется JSON-массив `refundSplits`, используемый для передачи данных о возвратах поставщикам. Формат массива см. [выше](#split-refund). 

## Регистрация мерчанта {#split-boarding}

Чтобы подключить сплитование платежей, выполните регистрацию мерчантов в API для маркетплейсов.

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

### Передача данных для регистрации мерчанта {#split-register-merchant}

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


Передайте набор данных по мерчанту. **Набор данных не полный и будет расширяться.**

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
surname | string |Фамилия
name | string |Имя
patronymic | string |Отчество
phone  |number |Телефон
----|----|-----
CONFIDANT_NEW	| object	| Данные о доверенном лице (если есть)
----|----|-----
surname |string |Фамилия
name |string|Имя
patronymic |string|Отчество
phone |number|Телефон

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

### Передача документов {#docs-provision}

Далее необходимо передать пакет документов по подключаемому мерчанту. Каждый документ отправляется отдельным запросом.

Отправьте POST-запрос на URL:

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
fileValueObject | formData | object | Объект, содержащий описание файла, принимает параметры:<br/>request_uid — uid заявки из предыдущего запроса<br/>file_code — код передаваемого документа (см. описание ниже).
multipartFile | formData | file | Передаваемый документ

Возможные значения кода документа:

* `SPLIT_STATEMENT` — Заявление о присоединении к оферте, подписанное  конечным поставщиком и маркетплейсом;
* `CLIENT_QUESTIONNAIRE` — Анкета конечного поставщика (формат анкеты описан в [Приложении №2 документа](https://static.qiwi.com/ru/doc/ishop/public-offer-ishop-new.pdf))

Набор передаваемых кодов также будет расширяться.

> Пример ответа

~~~shell
"861d0207-54b3-4e3a-bcfb-6f379f329328"
~~~

В ответе вы получите uid добавленной экстры.

### Получение информации о ходе подключения мерчанта {#info-merchant-register}

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

Чтобы узнать статус подключения конечного поставщика, отправьте GET-запрос на URL:

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
value | string | Переданное значение экстры
-----|-----|------
request_flow | object	| Служебные данные 
request_uid | string	| Uid созданной заявки заявки на подключение
request_updated_dtime | number	| Unix-time даты последнего обновления данных по заявке и ее статуса
merchant_site_external_ids | object	| Объект, содержащий данные о созданных процессинговых `MerchantSiteId` для конечного поставщика. Данный ID приходит не сразу, необходимо повторять данный запрос до получения ID, либо статуса REJECTED.
merchant_uids | object	| Служебные данные.
check_failures | object	| Не используется

Для отслеживания статуса заявки проверьте значение поля `request_status.request_status_code` в ответе:

* `REJECTED` — регистрация мерчанта отклонена. По номеру `request_id` вы можете обратиться в поддержку за уточнением подробностей.
* `ACCEPTED` — регистрация мерчанта прошла успешно, он переведен в производственный режим. 
* `MANAGER_WORK` — идет регистрация мерчанта, процессинговый идентификатор поставщика находится в тестовом режиме.

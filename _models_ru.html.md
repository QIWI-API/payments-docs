# Модели данных {#data-models}

## PayoutReceiverDataRequest {#payout-receiver-data-request}

Информация о получателе. Доступные типы: `CARD` и `SBP`.

> Тип CARD

~~~json
  "receiverData" : {
    "type" : "CARD",
    "pan" : "86003300000000000",
    "receiverFirstName" : "Ivan",
    "receiverLastName" : "Ivanov"
  }
~~~

Тип метода выплаты `CARD`:

Поле                 | Тип или константа | Описание
---------------------|-------------------|-----------------------
type<br>**required** | `CARD`            | Тип метода выплаты
pan<br>**required**  | string(19)        | Номер банковской карты
receiverFirstName    | string(64)        | Имя получателя
receiverLastName     | string(64)        | Фамилия получателя

> Тип SBP

~~~json
  "receiverData": {
    "type": "SBP",
    "phone" : "79998887766",
    "bankMemberId" : "100000000111"
  }
~~~

Тип метода выплаты `SBP`:

Поле                         | Тип или константа | Описание
-----------------------------|-------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
type<br>**required**         | `SBP`             | Тип метода выплаты
phone<br>**required**        | number(11..13)    | Номер телефона
bankMemberId<br>**required** | string(12)        | Идентификатор банка получателя в СБП. См. список банков, доступных для выплаты на СБП, в формате xlsx ([скачать](/downloads/sbp_members_b2c_receiver.xlsx)) или csv ([скачать](/downloads/sbp_members_b2c_receiver.csv)).

## PayoutReceiverDataResponse {#payout-receiver-data-response}

Информация о получателе. Доступные типы: `CARD` и `SBP`.

> Тип CARD

~~~json
  "receiverData" : {
    "type" : "CARD",
    "maskedPan": "860033*******0000",
    "receiverFirstName" : "Ivan",
    "receiverLastName" : "Ivanov"
  }
~~~

Тип метода выплаты `CARD`:

Поле                      | Тип или константа | Описание
--------------------------|-------------------|-------------------------------------
type<br>**required**      | `CARD`            | Тип метода выплаты
maskedPan<br>**required** | string(19)        | Маскированный номер банковской карты
receiverFirstName         | string(64)        | Имя получателя
receiverLastName          | string(64)        | Фамилия получателя

> Тип SBP

~~~json
  "receiverData": {
    "type": "SBP",
    "phone" : "79998887766",
    "bankMemberId" : "100000000111"
  }
~~~

Тип метода выплаты `SBP`:

Поле                         | Тип или константа | Описание
-----------------------------|-------------------|-------------------------------------
type<br>**required**         | `SBP`             | Тип метода выплаты
phone<br>**required**        | number(11..13)    | Номер телефона
bankMemberId<br>**required** | string(12)        | Идентификатор банка получателя в СБП

## PayoutReceiverDataCallback {#payout-receiver-data-callback}

Информация о получателе. Доступные типы: `CARD` и `SBP`.

> Тип CARD

~~~json
  "receiverData" : {
    "type" : "CARD",
    "maskedPan": "860033*******0000"
  }
~~~

Тип метода выплаты `CARD`:

Поле                      | Тип или константа | Описание
--------------------------|-------------------|-------------------------------------
type<br>**required**      | `CARD`            | Тип метода выплаты
maskedPan<br>**required** | string(19)        | Маскированный номер банковской карты

> Тип SBP

~~~json
  "receiverData": {
    "type": "SBP",
    "phone" : "79998887766",
    "bankMemberId" : "100000000111"
  }
~~~

Тип метода выплаты `SBP`:

Поле                         | Тип или константа | Описание
-----------------------------|-------------------|-------------------------------------
type<br>**required**         | `SBP`             | Тип метода выплаты: "SBP" — СБП
phone<br>**required**        | string            | Номер телефона
bankMemberId<br>**required** | string(12)        | Идентификатор банка получателя в СБП

## ChequeData {#receipt-data}

Информация о [фискальном чеке](#cheque) по операции.

~~~json
  "chequeData": {
    "id":5849136,
    "url":"https://cheques.qiwi.com/cheques/receipt/4a02b590-ae81-479c-8f70-85e4f425e05f"
  }
~~~

Имя | Описание                       | Тип
----|--------------------------------|-------
id  | Идентификатор чека             | String
url | Информация о чеке (URL-ссылка) | String

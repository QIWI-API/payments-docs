# Статусы и типы операций, коды ошибок  {#statuses}

## Коды ошибок {#http-errors}

Протокол приема платежей использует для запросов API следующие HTTP-коды ошибок:

Код ошибки | Описание
---------- | -------
400 | Bad Request — Ваш запрос некорректен (ошибка в данных или в формате запроса).
401 | Unauthorized — Неправильный ключ доступа к API.
403 | Forbidden — Доступ к API запрещен.
404 | Not Found — Указанный ресурс не найден.
405 | Method Not Allowed — Для создания платежа использовался неправильный метод.
406 | Not Acceptable — Формат данных отличается от JSON.
410 | Gone — Запрашиваемый ресурс удален.
429 | Too Many Requests — Слишком много запросов.
500 | Internal Server Error — Внутренняя ошибка сервиса. Если тело ответа пустое, повторите запрос с теми же параметрами. Если тело ответа не пустое, выполните [запрос статуса платежа](#payment_get) или [статуса счета](#invoice_get).
502 | Bad Gateway — Нет связи с сервисом
503 | Service Unavailable  — Сервер временно недоступен по техническим причинам, попробуйте позже.

## Типы операций {#operation-types}

Тип операции возвращается в поле `{operation}.type` [уведомления](#callback).

Тип операции | Описание
---|----
PAYMENT | Платеж. В уведомлении может присутствовать поле `flag: [ "SALE" ]` (обычный платеж) или `flag: [ "AUTH" ]` (платеж с холдированием средств).
CAPTURE | Операция подтверждения. 
REFUND | Операция возврата. В уведомлении может присутствовать поле `flag: [ "REVERSAL" ]`. Это значит, что финансовой операции (списания средств со счета покупателя) не было, комиссия по операции удержана не будет.

## Статусы операций {#operation-statuses}

Статус операции отражает ее текущее состояние. 

API возвращает синхронный статус операции в поле `status.value`. 

В уведомлениях статус помещается в поле `{operation}.status.value`. 

В таблице перечислены возможные статусы и [типы операций](#operation-types), в которых эти статусы используются.

Статус операции | Тип операции | Описание статуса | Где возвращается
---|----|-----|------
WAITING | PAYMENT | Ожидание 3DS авторизации|Ответы API
DECLINED | PAYMENT | Запрос авторизации отклонен|Уведомления, Ответы API
DECLINE | REFUND | Запрос возврата отклонен|Уведомления, Ответы API
DECLINE | CAPTURE | Запрос подтверждения отклонен|Уведомления, Ответы API
SUCCESS | PAYMENT | Запрос авторизации успешно обработан|Уведомления
COMPLETED | PAYMENT | Запрос авторизации успешно обработан|Ответы API
SUCCESS | REFUND | Запрос возврата успешно обработан|Уведомления
COMPLETED | REFUND | Запрос возврата успешно обработан|Ответы API
SUCCESS | CAPTURE | Запрос подтверждения успешно обработан|Уведомления
COMPLETED | CAPTURE | Запрос подтверждения успешно обработан|Ответы API

<aside class="notice">Для счетов используется только статус CREATED.</aside>

## Справочник ошибок API {#reason-codes}

Ошибки API описывают причину отклонения операции и передаются: 

* в ответах на запросы — в поле `status.reason`;
* в уведомлениях — в поле `status.reasonCode`.

Ошибка API|Описание
------------------|--------
INVALID_STATE| Некорректный статус транзакции
INVALID_AMOUNT| Некорректная сумма
INVALID_RECEIVER_DATA | Ошибка при передаче данных о получателе
DECLINED_BY_MPI | Отклонено MPI
DECLINED_BY_FRAUD| Отклонено fraud-мониторингом
REATTEMPT_NOT_PERMITTED| Повторный запрос авторизации запрещен на основании полученного ответа от Платежной системы
GATEWAY_INTEGRATION_ERROR| Ошибка взаимодействия с банком
GATEWAY_TECHNICAL_ERROR| Техническая ошибка на стороне банка
ACQUIRING_MPI_TECH_ERROR| Техническая ошибка при проведении 3DS аутентификации
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
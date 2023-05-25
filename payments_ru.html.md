---
title: Протокол приема платежей

metatitle: Протокол приема платежей

metadescription: Протокол приема платежей позволяет начать приём безопасных платежей с банковских карт от клиентов.

category: acquiring

language_tabs:
  - json: Примеры

toc_footers:
  - <a href='/ru/payments-release-notes/index.html'>Список изменений</a>
  - <a href='/ru/payments-lk-guide/index.html'>Справка по интерфейсу Личного кабинета</a>
  - <a href='/'>На главную</a>
  - <a href='mailto:bss@qiwi.com'>Обратная связь</a>

includes:
  - payments/intro_ru
  - payments/qiwi-form_ru
  - payments/merchant-form_ru
  - payments/notifications_ru
  - payments/refunds-capture-reversal_ru
  - payments/splits_ru
  - payments/payment-token_ru
  - payments/card-check_ru
  - payments/safe-deal_ru
  - payments/reports_ru
  - payments/reimburse_ru
  - payments/statuses_ru
  - payments/api-methods_ru
  - payments/models_ru
  - payments/54fz-data_ru

search: true
---

 *[Ключ API]: Символьная строка для аутентификации мерчанта в API по стандарту OAuth 2.0 RFC 6749, RFC 6750.
 *[Платежный токен]: Символьная строка, созданная по данным карты для списаний без ввода реквизитов карт.
 *[3DS]: 3-D Secure — протокол защиты, используемый для аутентификации держателя банковской карты во время совершения платежной операции посредством сети Интернет.
 *[ТСП]: Торгово-сервисное предприятие
 *[Мерчант]: Торгово-сервисное предприятие
 *[MPI]: Merchant Plug-In — модуль системы, выполняющий 3DS аутентификацию.
 *[PCI DSS]: Payment Card Industry Data Security Standard — стандарт безопасности данных индустрии платёжных карт, учреждённым международными платёжными системами Visa, MasterCard, American Express, JCB и Discover.
 *[REST]: Representational State Transfer — архитектурный стиль взаимодействия компонентов распределённого приложения в сети.
 *[JSON]: JavaScript Object Notation — текстовый формат обмена данными, основанный на JavaScript.
 *[Luhn]: Алгоритм Луна — алгоритм вычисления контрольной цифры номера пластиковой карты, предназначен для выявления ошибок, вызванных непреднамеренным искажением данных.
 *[HTTPS]: Расширение протокола HTTP для поддержки шифрования в целях повышения безопасности. Данные в протоколе HTTPS передаются поверх криптографических протоколов SSL или TLS. В отличие от HTTP с TCP-портом 80, для HTTPS по умолчанию используется TCP-порт 443.

# Обзор протокола {#get-started}

###### [Версия 1.23](/ru/payments-release-notes/index.html) | [Редактировать на GitHub](https://github.com/QIWI-API/payments-docs/)

Протокол приема платежей предоставляет быстрые и безопасные решения для приема и отправки платежей в интернете. Протокол дает вашим покупателям возможность использовать разнообразные методы платежей, включая:

* Банковские карты Visa, Mastercard, МИР.
* Yandex Pay.
* Mir Pay.
* QIWI Кошелек.
* Система Быстрых Платежей (СБП).
* Баланс мобильного телефона.

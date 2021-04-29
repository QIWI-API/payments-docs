---
title: Протокол приема платежей

metatitle: Протокол приема платежей

metadescription: Протокол приема платежей позволяет начать приём безопасных платежей с банковских карт от клиентов.

category: acquiring

language_tabs:
  - json: Примеры

toc_footers:
  - <a href='/'>На главную</a>
  - <a href='mailto:bss@qiwi.com'>Обратная связь</a>

includes:
  - payments/intro_ru
  - payments/qiwi-form_ru
  - payments/merchant-form_ru
  - payments/notifications_ru
  - payments/refunds-capture-reversal_ru
  - payments/splits_ru
  - payments/reports_ru
  - payments/reimburse_ru
  - payments/statuses_ru
  - payments/api-methods_ru
  - payments/54fz-data_ru

search: true
---

 *[Ключ API]: Символьная строка для аутентификации мерчанта в API по стандарту OAuth 2.0 RFC 6749, RFC 6750.
 *[Платежный токен]: Символьная строка, созданная по данным карты для безакцептных платежей
 *[ключ API]: Символьная строка для аутентификации мерчанта в API по стандарту OAuth 2.0 RFC 6749, RFC 6750.
 *[платежный токен]: Символьная строка, созданная по данным карты для безакцептных платежей
 *[3DS]: 3-D Secure - протокол защиты, используемый для аутентификации держателя банковской карты во время совершения платежной операции посредством сети Интернет.
 *[ТСП]: Торгово-сервисное предприятие
 *[Мерчант]: Торгово-сервисное предприятие
 *[MPI]: Merchant Plug-In - модуль системы, выполняющий 3DS аутентификацию.
 *[PCI DSS]: Payment Card Industry Data Security Standard -  стандарт безопасности данных индустрии платёжных карт, учреждённым международными платёжными системами Visa, MasterCard, American Express, JCB и Discover.
 *[REST]: Representational State Transfer -  архитектурный стиль взаимодействия компонентов распределённого приложения в сети.
 *[JSON]: JavaScript Object Notation - текстовый формат обмена данными, основанный на JavaScript.
 *[Luhn]: Алгоритм Луна - алгоритм вычисления контрольной цифры номера пластиковой карты, предназначен для выявления ошибок, вызванных непреднамеренным искажением данных.
 *[HTTPS]: Расширение протокола HTTP для поддержки шифрования в целях повышения безопасности. Данные в протоколе HTTPS передаются поверх криптографических протоколов SSL или TLS. В отличие от HTTP с TCP-портом 80, для HTTPS по умолчанию используется TCP-порт 443.


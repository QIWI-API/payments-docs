---
title: Payment protocol

metatitle: Payment protocol

metadescription: Payment protocol allows RSP to start accepting fast and secure payments from their customers' credit cards.

category: acquiring

language_tabs:
  - json: Examples

toc_footers:
  - <a href='/'>To main page</a>
  - <a href='mailto:bss@qiwi.com'>Feedback</a>

includes:
  - payments/intro_en
  - payments/qiwi-form_en
  - payments/merchant-form_en
  - payments/notifications_en
  - payments/refunds-reversal_en
  - payments/splits_en
  - payments/payment-token_en
  - payments/card-check_en
  - payments/reports_en
  - payments/reimburse_en
  - payments/statuses_en
  - payments/api-methods_en
  - payments/54fz-data_en

search: true
---

 *[API Key]: String for merchant authentication in API according to OAuth 2.0 standard, RFC 6749, RFC 6750.
 *[Payment token]: String linked to the card data for payments without entering card details.
*[3DS]: 3-D Secure — a protection protocol used to authenticate card holder while making a payment transaction over the Internet.
*[RSP]: Retail Service Provider.
*[Merchant]: Retail Service Provider.
*[PCI DSS]: Payment Card Industry Data Security Standard — The Payment Card Industry Data Security Standard – a proprietary information security standard for storing, processing and transmitting credit card data established by Visa, MasterCard, American Express, JCB, and Discover.
*[REST]: Representational State Transfer — a software architectural pattern for Network Interaction between distributed application components.
*[JSON]: JavaScript Object Notation — a lightweight data-interchange format based on JavaScript.
*[Luhn]: Luhn Algorithm — a checksum formula used for verifying and validating identification numbers against accidental errors.
*[HTTPS]: HTTP protocol extension to encrypt and enforce security. In HTTPS protocol, data transfer over SSL and TLS cryptographic protocols. In contrast with HTTP on port 80, the TCP 443 port is used by default for HTTPS.

# Overview {#get-start}

The Payments protocol provides a payment processing solutions that helps you accept payments in fast and secure way. The protocol gives your customers flexibility to pay the way they want, including:

* Debit or credit cards, like Visa, Mastercard, and MIR.
* Apple Pay / Google Pay.
* QIWI Wallet.
* Faster Payments Systems (SBP).

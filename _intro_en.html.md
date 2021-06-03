# Terms and Abbreviations {#articles}

###### Last update: 2021-04-21 | [Edit on GitHub](https://github.com/QIWI-API/payments-docs/blob/master/includes/_intro_en.html.md)

**API Key** — String for merchant authentication in API according to OAuth 2.0 standard [RFC 6749](https://tools.ietf.org/html/rfc6749) [RFC 6750](https://tools.ietf.org/html/rfc6750).

**Payment token** — String linked to the card data for payments without entering card details.

**API**: Application Programming Interface — a set of ready-made methods provided by the application (system) for use in external software products.

**REST**: Representational State Transfer — a software architectural pattern for Network Interaction between distributed application components.

**JSON**: JavaScript Object Notation — a lightweight data-interchange format based on JavaScript [RFC 7159](https://tools.ietf.org/html/rfc7159).

**3DS**: 3-D Secure — protection protocol used to authenticate card holder while making a payment transaction over the Internet. QIWI supports both 3DS 1.0 version and 3DS 2.0 version of the protocol.

**RSP**, **Merchant** — Retail Service Provider.

**MPI**: Merchant Plug-In — programming module performing 3DS authentication.

**PCI DSS**: Payment Card Industry Data Security Standard – a proprietary information security standard for storing, processing and transmitting credit card data established by Visa, MasterCard, American Express, JCB, and Discover.

# How to Get Started {#start}

To start using Protocol, you need to complete the following steps.

**Step 1. Leave request to integrate on [b2b.qiwi.com](https://b2b.qiwi.com)**

After processing the requests, our personnel contact you to discuss possible ways of integration, collect the necessary documents and start integration process.

<a name="auth"></a>

**Step 2. Get access to your Account**

Upon connecting to the Payment Protocol, we provide you the unique site identifier (`siteId`) and access to your [Account](https://kassa.qiwi.com/service/core/merchants?) in our system. We send the Account credentials to your e-mail address specified on registration.

**Step 3. Issue API access key for the integration**

API access key is used for interaction with API. Issue API access key in **Settings** section of your [Account](https://kassa.qiwi.com/service/core/merchants?).

**Step 4. Test the interaction**

During integration process, your `siteId` identifier is in test mode. You can proceed test operations without debiting credit card. See [Test and Production Mode](#test_mode) for details.

When integration on your side is completed, we turn your `siteId` to production mode. In the production mode cards are debited.

## Ways of Integration {#integration}

Payment protocol provides several ways of integration:

* [Payment through QIWI Form](#invoicing).
* [Payment through merchant form](#merchant-api-integration).

<aside class="warning">
"Payment through merchant form" method is available for PCI DSS-certified merchants only, as RSP performs accepting and processing customers' card data.
</aside>

### Available Payment Methods {#payment-ways}

Method|Payment through QIWI Form|Payment through merchant form
-----|---------|--------------
Credit/debit card|✓|✓
Payment by payment token |✓|✓
Apple Pay | ✓ | ✓
Google Pay | ✓ | ✓
[Faster Payments System](http://www.cbr.ru/eng/psystem/sfp/) | × | ✓
QIWI Wallet Balance |✓|✓*

`*` — by issuing a payment token for QIWI wallet

## Operation Types {#operations}

The following operations are available in the protocol:

* An Invoice — an electronic document that the merchant has issued to the customer. Contains information about the amount and number of the customer order. It is not a financial entity and has a limited lifespan. Billing is required to obtain a URL to the QIWI payment form.
* Payment — a cash write-off transaction from the customer in favour of the merchant. The actual write-off occurs only after confirmation (see Capture). When working through the QIWI payment form, Payment is an attempt to pay the bill (see Invoice).
* Complete — the completion of 3DS verification of the customer. It is used when working through the Merchant Payment Form.
* Confirmation (Capture) — confirmation of authorization (charging) of the funds.
* Refund — refund to the customer on a successful payment. Financial operation of debiting money from the merchant in favor of the customer. If there was no confirmation for Payment operation, you will receive the Reversal flag in the response to Refund operation request and the money from the customer's account will not be transferred to the Merchant's account (the acquiring fee is also not withheld).

## Payment processing and settlements scheme {#principial-scheme}


<div class="mermaid">
sequenceDiagram
participant customer as Customer
participant store as Merchant
participant ipsstore as Bank of <br/> Merchant
participant qb as QIWI
participant ips as Payment system
participant ipscust as Bank of <br/> Customer <br/> Issuer or bank-sender
customer->>store:Payment start
store->>qb:Payment operation
qb->>ips:Payment authorization
ips->>ipscust:Payment authorization
rect rgb(237, 243, 255)
Note over ipsstore, ipscust:Settlements
ipscust->>ips:₽₽₽
ips->>qb:₽₽₽
qb->>ipsstore:₽₽₽
end
</div>

## Interaction format {#api-format}

API of the Payment protocol implements REST principles.

API uses HTTPS-requests for main application protocol.

API endpoint:

`https://api.qiwi.com/partner/`

API requests' parameters are transferred in the request body JSON data. Parameters in HTTP GET-requests are placed in URL. 

API always responds in JSON format.

### Authentication {#api-auth}

> Request with authentication

~~~shell
curl -X PUT https://api.qiwi.com/partner/v1/sites/{site_id}/payments/{payment_id} \
     --oauth2-bearer <API Key>
~~~

> Authentication header

~~~shell
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
~~~

For the requests authentication OAuth 2.0 standard is used in accordance with [RFC 6750](https://tools.ietf.org/html/rfc6750). Always put API access key value into `Authorization` HTTP-header as

`Bearer <API Key>`

## Test and Production Mode {#test_mode}

During [integration](#start), your `siteId` identifier is in test mode. You can proceed operations without debiting credit card. You can also request a switch to test mode for any of your `siteId`, or add a new `siteId` to test mode through your support manager.

For testing purposes, basic [protocol URLs](api-format) are used.

**Test mode is not supported for QIWI Wallet balance payments.**

When integration on your side is completed, we turn your ID to production mode. In the production mode cards are debited.

**You don't need to re-release the API access key when you go into production mode. However, if you change the permanent URL for notifications from a test notification (such as a https://your-shop-test.ru/callbacks) to a production one (such as https://your-shop-prod.ru/callbacks), you need to release a new API access key.**

<aside class="notice">
The API access key, linked to a certain <code>siteId</code>, works for all payment methods enabled for this ID.
</aside>

### Card payment in test mode {#test_data_card}

To make tests for payment operations, you may use any card number complied with Luhn algorithm.

<h4>Test card numbers</h4>
<h4 id="visa">
</h4>
<h4 id="mc">
</h4>
<button class="button-popup" id="generate">Get more cards</button>

In testing mode, you may use only Russian ruble (`643` code) for the currency.

CVV in testing mode may be arbitrary (any 3 digits).

To test various payment methods and responses, use different expiry dates:

* If month of expiry date is `02`, then operation is treated as unsuccessful.
* If month of expiry date is `03`, then operation will process successfully with 3 seconds timeout.
* If month of expiry date is `04`, then operation will process unsuccessfully with 3 seconds timeout.
* In all other cases, operation is treated as successful.

Test environment has restrictions on the total amount and number of operations. By default, maximum amount of a test transaction is 10 rubles. Maximum number of test transactions is 100 rubles per day (MSK time zone). Only test transactions within allowed amount are taken into account.

To process 3DS operation, use `unknown name` as card holder name. 3DS in test mode may be properly tested on real card number only.

### Payment through Faster Payments System in test mode {#test_data_sbp}

To test various payment methods and responses, use different amounts (`amount` field):

* `100` — operation is immediately successful. Notification will be sent and you will receive `"SUCCESS"` status in [payment status request](#payment_get);
* `200` — operation is successful with some delay. On the first [payment status request](#payment_get) you will receive `"WAITING"` payment status, on the second request you will get `"SUCCESS"` payment status.
* For any other amounts the payment would be unsuccessful.

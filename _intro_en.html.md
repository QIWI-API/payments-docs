# Terms and Abbreviations {#articles}

**API Key**: String for merchant authorization in API according to OAuth 2.0 standard [RFC 6749](https://tools.ietf.org/html/rfc6749) [RFC 6750](https://tools.ietf.org/html/rfc6750).

**Merchant**: see **RSP**.

**Payment token**: String linked to the card data for payments without entering card details.

**3DS**: 3-D Secure — protection protocol to authenticate card holder while making a payment transaction over the Internet. QIWI supports both 3DS 1.0 version and 3DS 2.0 version of the protocol.

**API**: Application Programming Interface — a set of ready-made methods provided by the application (system) for use in external software products.

**JSON**: JavaScript Object Notation — a lightweight data-interchange format based on JavaScript [RFC 7159](https://tools.ietf.org/html/rfc7159).

**MPI**: Merchant Plug-In — programming module performing 3DS customer authentication.

**PCI DSS**: Payment Card Industry Data Security Standard – a proprietary information security standard for storing, processing and transmitting credit card data established by Visa, Mastercard, American Express, JCB, and Discover.

**REST**: Representational State Transfer — a software architectural pattern for Network Interaction between distributed application components.

**RSP**: Retail Service Provider.

# How to Get Started {#start}

To start using Protocol, you need to complete the following steps.

**Step 1. Leave request to integrate on [b2b.qiwi.com](https://b2b.qiwi.com)**

After processing the requests, our personnel contact you to discuss possible ways of integration, collect the necessary documents and start integration process.

<a name="auth"></a>

**Step 2. Get access to RSP Account**

Upon connecting to the Payment Protocol, we provide you the unique site identifier (`siteId`) and access to RSP [Account](https://business.qiwi.com/service/core/merchants?) in our system. We send the Account credentials to RSP e-mail address specified on registration.

**Step 3. Issue API access key for the integration**

API access key is used for interaction with API. Issue API access key in **Settings** section of RSP [Account](https://business.qiwi.com/service/core/merchants?).

**Step 4. Test the interaction**

During integration process, RSP `siteId` identifier is in test mode where test operations can be done without debiting credit card. See [Test and Production Mode](#test_mode) for details.

When integration on RSP's side is completed, QIWI Support turns the corresponding `siteId` to production mode. In the production mode cards are debited.

## Ways of Integration {#integration}

Payment protocol provides several ways of integration:

* [Payment through QIWI Form](#invoicing).
* [Payment through merchant form](#merchant-api-integration).

<aside class="warning">
"Payment through merchant form" method is available for PCI DSS-certified merchants only, as RSP performs accepting and processing customers' card data.
</aside>

### Available Payment Methods {#payment-ways}

Method|[Payment through QIWI Form](#invoicing)|[Payment through merchant form](#merchant-api-integration)
-----|---------|--------------
Credit/debit card*|✓|✓**
Payment by payment token |✓|✓
[Faster Payments System](http://www.cbr.ru/eng/psystem/sfp/) | ✓ | ✓

`*` — default payment method, other methods are available upon request.

`**` — PCI DSS certification is required.

## Operation Types {#operations}

The following operations are available in the protocol:

* An Invoice — an electronic document that the merchant has issued to the customer. Contains information about the amount and number of the customer order. It is not a financial entity and has a limited lifespan. Billing is required to obtain a URL to the QIWI payment form.
* Payment — a cash write-off transaction from the customer in favour of the merchant. The actual write-off occurs only after confirmation (see Capture). When working through the QIWI payment form, Payment is an attempt to pay the bill (see Invoice).
* Complete — the completion of 3DS verification of the customer. It is used when working through the Merchant Payment Form.
* Confirmation (Capture) — confirmation of authorization (charging) of the funds. Only single successful payment confirmation is possible.
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
rect rgb(255, 238, 223)
Note over ipsstore, ipscust:Settlements
ipscust->>ips:₽₽₽
ips->>qb:₽₽₽
qb->>ipsstore:₽₽₽
end
</div>

## Interaction format {#api-format}

The Payment protocol API is built on the [REST architecture](https://restfulapi.net/), where data and methods are considered as resources accessed via calling Uniform Resource Identifiers (URIs).

API uses HTTPS-requests for calling its methods. API endpoint has URL:

`https://b2b-api.qiwi.com/partner/`

API requests' parameters are transferred as the request body JSON data. Parameters in HTTP GET-requests are placed in URL query.

It is necessary to put `Accept: application/json` header into the request headers as API always responds in JSON format.

The API methods provide logical idempotence, i. e. repeating a method multiple times is equivalent to doing it once. However, the response may change (for example, the state of an invoice may change between requests).

### Authorization {#api-auth}

> Request with authorization

~~~shell
curl -X PUT \
  https://b2b-api.qiwi.com/partner/v1/sites/{site_id}/payments/{payment_id} \
  --oauth2-bearer <API Key>
~~~

> Authorization header

~~~shell
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
~~~

For the requests authorization OAuth 2.0 standard is used in accordance with [RFC 6750](https://tools.ietf.org/html/rfc6750). Always put API access key value into `Authorization` HTTP-header as

`Bearer <API Key>`

# How to Test Operations {#test_mode}

During [integration](#start), RSP `siteId` identifier is in test mode where operations without debiting credit card can be done. You can also request your manager in QIWI Support to switch any of RSP `siteId` to test mode, or add a new `siteId` in test mode.

For testing purposes, the same [protocol URLs](api-format) are used.

<aside class="notice">
When integration on RSP side is completed, QIWI Support turns RSP site ID to production mode. In the production mode, real debits of funds from cards are performed.
</aside>

**You don't need to re-release the API access key when you go into the production mode**.

If necessary, change the permanent URL for notifications from a test notification (such as `https://your-shop-test.ru/callbacks`) to a production one (such as `https://your-shop-prod.ru/callbacks`) in RSP [Account Profile](https://business.qiwi.com/service/core/merchants?).

<aside class="notice">
The API access key, linked to a certain <code>siteId</code>, works for all payment methods enabled for this ID.
</aside>

## Card payment {#test_data_card}

To test various payment methods and responses, use different expiry dates:

Month of card expiry date | Result
----|------
`02`| Operation is treated as unsuccessful
`03`| Operation is processed successfully with 3 seconds timeout
`04`| Operation is processed unsuccessfully with 3 seconds timeout
All other values | Operation is treated as successful

CVV in testing mode may be arbitrary (any 3 digits).

You can also check payment token issue. Issue scheme in the test mode is the same as in production mode. It is described in [Card payment token issue](#merchant-form-token-issue) section.

### Test card numbers {#test-card-numbers}

* **Mir**: `2200000000000004`, `2200000000000012`, `2200000000000020`, `2200000000000038`, `2200000000000046`, `2200000000000053`, `2200000000000061`, `2200000000000079`, `2200000000000087`, `2200000000000095`, `2200000000000103`, `2200000000000111`
* **Visa**: `4256000000000003`, `4256000000000011`, `4256000000000029`, `4256000000000037`, `4256000000000045`, `4256000000000052`, `4256000000000060`, `4256000000000078`, `4256000000000086`, `4256000000000094`, `4256000000000102`, `4256000000000110`
* **Mastercard**: `5236000000000005`, `5236000000000013`, `5236000000000021`, `5236000000000039`, `5236000000000047`, `5236000000000054`, `5236000000000062`, `5236000000000088`, `5236000000000096`, `5236000000000104`, `5236000000000112`, `5236000000000120`
* **UnionPay**: `6056000000000000`, `6056000000000018`, `6056000000000026`, `6056000000000034`, `6056000000000042`, `6056000000000059`, `6056000000000067`, `6056000000000075`, `6056000000000083`, `6056000000000091`, `6056000000000109`, `6056000000000117`

### Test 3-D Secure operations {#test-3ds}

1. Create a payment link [via API](#qiwi-form-invoice-api), or [without it](#https-qiwi-form).
2. Use any card from [Test Card Numbers](#test-card-numbers) list.
3. For 3-D Secure in test mode CVV should be `849` or use a cardholder name that contains the string `3ds`.
4. Confirm or reject the transaction on the form.
![test-3ds](/images/payin/test-3ds.png)

### Test limits {#test_limit}

* You may use only Russian ruble (`643` code) for the currency (`amount.currency`).
* Restrictions on the amount and number of operations:

  * Maximum allowed amount of a single transaction is 10 rubles.
  * Maximum number of transactions is 100 per day. All transactions within the current day (by Moscow timezone) with each transaction' amount not more than 10 rubles are taken into account.

## Payment through Faster Payments System {#test_data_sbp}

In test mode, you can use only [QR-code issue](#qr-code-sbp) and its [status request](#qr-code-sbp-get). To test various responses, use different payment amounts (`amount.value` field):

* `200` — QR-code is successfully created.
* For any other amounts the payment would be unsuccessful with `DECLINED` status.

When requesting [FPS payment status](#qr-code-sbp-get) in the test mode the following statuses are returned:

* `CREATED` — Payment created.
* `DECLINED` — Payment declined.
* `EXPIRED` — Payment QR-code lifetime is expired.

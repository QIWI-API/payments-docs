# Payment Through QIWI Form {#invoicing}

When you integrate payments through the QIWI form, the default payment method is [Bank Cards](#qiwi-form-card). The following payment methods are connected on demand:

* [Card payment token](#qiwi-token-pay).
* [Apple Pay/Google Pay](#qiwi-form-applepay).
* [QIWI Wallet](#qiwi-form-wallet).

<aside class="notice">
On QIWI Payment Form, Apple Pay/Google Pay and QIWI Wallet for the same RSP ID <code>siteId</code> cannot be used at the same time. Create two separate identifers for Apple Pay/Google Pay and QIWI Wallet.
</aside>

## Payment process {#flow-payment-qiwi-form}

<div class="mermaid">
sequenceDiagram
participant customer as Customer
participant store as Merchant
participant qb as QIWI (acquirer)
participant ips as Issuer
customer->>store:Choose goods, Start payment
activate store
store->>qb:Issue invoice<br/>One-step payment — all payment methods<br/>Two-step payment — only cards and QIWI Wallet (QW)
activate qb
qb->>store:URL to QIWI Payment form (payUrl)
store->>customer:Redirecting customer to payUrl
customer->>qb:Payment form opens,<br/>choose payment method,<br/>enter details for chosen method
qb->>customer:Customer authentication:<br/>3DS for cards, QIWI Wallet authorization for QW
customer->>qb:Authentication
qb->>ips:Request for funds charging
activate ips
ips->>qb:Operation status
qb->>store:Notification with operation status
qb->>customer: Redirecting to payment status page (successUrl)
rect rgb(237, 243, 255)
Note over store, ips:Two-step payment
store->>qb:Payment confirmation (capture)
qb->>ips:Confirming card charging
deactivate ips
qb->>store:Notification on payment confirmation
end
deactivate qb
deactivate store
</div>

To create invoice for a payment, send in API request [Invoice](#invoice_put):

* API access key;
* invoice amount;
* other data for invoice issue;

or simply redirect customer by the [direct link](https-qiwi-form) to the QIWI Form.

## QIWI Payment Form integration without API {#https-qiwi-form}

<aside class="warning">
Using this method, you cannot guarantee that all invoices are issued by a merchant, as opposed to an API invoicing.
</aside>

It's an easy way to integrate with the QIWI payment form. When the form is opened, the customer is automatically billed for the order. Parameters of the invoice are sent unprotected in the link. A payment form with a choice of payment methods is shown to the customer.

To integrate, contact Support to obtain the `PUBLIC_KEY` key and use it in all links to the form. A unique key is produced for each `siteId`.

When paying an invoice in this way, the payment is authorized without the participation of the merchant. Turn on automatic payment confirmation in Support, so you don't have to comply with the [Payment confirmation](#capture) request.

<aside class="notice">
By default, when customer pay the invoice with the QIWI Payment form link, the service waits for the payment to be confirmed within 72 hours. You can confirm this payment through your Account. At the end of the term, the payment is self-confirmed. To reduce the waiting period, or to set up a payment auto-refund, contact support.
</aside>

<h3 class="request method" id="payform_flow">REDIRECT → </h3>

~~~shell
https://oplata.qiwi.com/create?publicKey=e1ZuGvjEoxxGHUz3raU1hZm2LyrSqxt37XGRtQhMWZrkw2VT8truUtaEBpY833Wxand5eX3MUFUcEq5T3eae1GdQQbQBgJwtVciAVG3btyZbLr4KABGrny2&comment=some%20comment&extras[cf1]=custom%20field%201&amount=14.88
~~~

<ul class="nestedList url">
    <li><h3>URL <span>https://oplata.qiwi.com/create</span></h3></li>
</ul>


<aside class="notice">
When opening Payment Form in Webview on Android, you should enable <code>settings.setDomStorageEnabled(true)</code> option.
</aside>

<ul class="nestedList params">
    <li><h3>Parameters</h3><span>Invoice parameters put to the URL.</span></li>
</ul>

Parameter|Description|Type|Required
---------|--------|---|---------|---|----
publicKey | Merchant identification key|String|+
billId|Unique invoice identifier in merchant's system. It must be generated on your side with any means. It could be any sequence of digits and letters. Also you might use underscore `_` and dash `-`. If not used, for each URL opening a new invoice is created.|URL-encoded string String(200)|-
amount| Amount of customer order rounded down to 2 digits (always in rubles) | Number(6.2)|-
phone | Customer phone number (international format) | URL-encoded string|-
email | Customer e-mail | URL-encoded string|-
comment | Comment to the invoice|URL-encoded string String(255)|-
successUrl|URL for redirect from the QIWI form in case of successful payment. URL should be within the merchant's site.|URL-encoded string|-
extras[cf1] | Extra field to add any information to invoice data |URL-encoded string| -
extras[cf2] | Extra field to add any information to invoice data|URL-encoded string | -
extras[cf3] | Extra field to add any information to invoice data|URL-encoded string | -
extras[cf4] | Extra field to add any information to invoice data|URL-encoded string | -
extras[cf5] | Extra field to add any information to invoice data|URL-encoded string | -


By default, the customer is automatically authenticated after the invoice is paid. Authentication is also automatically completed.

## Bank card {#qiwi-form-card}

The payment protocol supports both a two-step payment with holding funds on the customer's card, and a one-step payment without the authentication of the customer.

### Invoice creation {#hold}

>Example of invoice creation with payment on hold (two-step payment)

~~~http
PUT /partner/payin/v1/sites/{siteId}/bills/893794793973 HTTP/1.1
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

>Example of invoice creation with payment without customer authentication (one-step payment)

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

To create an invoice with payment through holding funds on the card (two-step scenario), pass the API request [Invoice](#invoice_put):

* The API access key.
* The amount of the invoice.
* The date before which the invoice must be paid.
* (optional) Other invoice data:
  * Customer data.
  * Comment on the imvoice.
  * Other information.

For the two-step payment, the [reimbursement](#reimburse) is formed only after the [order confirmation](#qiwi-capture).

<a name="one_step">

<aside class="notice">
By default, when holding funds, the service expects <a href="#qiwi-capture">confirmation of payment</a> within 72 hours. At the end of the term, the payment is self-confirmed. To increase or reduce the waiting period, or to set up a payment autoreversal, contact Support. The waiting period may not last more than 5 days. 
</aside>

To pay the invoice without the customer's authentication (one-step scenario), include in the API request [Invoice](#invoice_put) the `"flags":["SALE"]` parameter. If you do not pass this parameter, the unconditional holding of funds to pay the invoice will be made.


### Payment confirmation {#qiwi-capture}

> Notification example

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

> Payment confirmation

~~~http
PUT /partner/payin/v1/sites/{siteId}/payments/804900/capture/bxwd8096 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com
~~~

This action is required for two-step scenario (payment with holding funds).

<aside class="notice">
By default, when holding funds, the service expects <a href="#qiwi-capture">confirmation of payment</a> within 72 hours. At the end of the term, the payment is self-confirmed. To increase or reduce the waiting period, or to set up a payment autoreversal, contact Support. The waiting period may not last more than 5 days. 
</aside>


To confirm payment:

* Get `paymentId` identifier of the payment:
     * From [server notification](#payment_callback) after successful holding of funds.
     * From the response to the [Invoice status](#invoice_get) request.
* Send API request [Payment confirmation](#capture) with received `paymentId` identifier.

## Payment token {#qiwi-token-pay}

> Invoice payable with payment token

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

The payment tokens are used for charging a customer balance without entering card details or QIWI Wallet number. By default, the use of payment tokens is disabled. Contact your Support manager to enable that.

You can read about the issue of a payment token [in this section](#payment-token-issue).

<aside class="warning">
Customer will be able to make payment by payment token only on the site where payment token was issued.

To make the payment token valid on other sites, send a request to Support.
</aside>

To create an invoice payable with payment token, send in API request [Invoice](#invoice_put) the following data:

* API access key.
* Amount of the invoice.
* Last date of payment for the invoice.
* Customer identifier for which the payment token was issued, in `customer.account` parameter. **Payment by payment token is not possible without this parameter.**
* Other information about the invoice.

If one or more payment tokens have been issued for the customer, the Payment form will display their linked cards. 

![qiwi-form-tokens](/images/qiwi-form-token.png)

To use the payment token, it is enough for the customer to choose one of their linked cards. Customer doesn't need to provide card data or 3-D Secure authentication.

To charge funds on a payment token without the customer's participation, use the API method [Payment](#payment). See details in section [Using payment token](#merchant-token-pay) for the mercant's payment form.

## Apple Pay/Google Pay {#qiwi-form-applepay}

Apple Pay purchases are made without card data entering. The technology works in mobile apps and the Safari browser on iPhone, iPad, Apple Watch and MacBook.

For the Google Pay™ method, Visa and MasterCard payments are supported without entering the card data.

To add Apple Pay/Google Pay payment methods to the QIWI Payment Form, contact your Support manager.

Invoicing and notification of payment for Apple Pay/Google Pay payment methods are similar to [bank card](#qiwi-form-card) payment.

## QIWI Wallet {#qiwi-form-wallet}

> Invoice payable with QIWI Wallet

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

By default, the payments from QIWI Wallet balance are turned off. To enable this payment method, contact your Support manager.

<aside class="notice">
If you have enabled payment token or Apple Pay/Google Pay methods, create a separate <code>siteId ID</code> to accept payments only from the QIWI Wallet balance.
</aside>

To create an invoice payable with QIWI Wallet, send in the API request [Invoice for QIWI Wallet payment](#invoice_qw_put) the following data:

* API access key.
* The amount of the invoice.
* The date before which the bill must be paid.
* Other invoice information.

[Redirect customer to the Payment form link](#qiwi-redirect), which will come in response in the `payUrl` field. On the Payment form, the customer will have an option to pay from the balance of the QIWI Wallet.

The payment process for the customer is as follows:

1. The customer opens the form.
     
    ![Wallet Invoice](/images/wallet_invoice.jpg)
2. The customer authenticates in QIWI Wallet.
    
    ![Wallet Auth](/images/wallet_auth.jpg)
3. The customer pays for the invoice.
    
    ![Wallet Pay](/images/wallet_pay.jpg)

## Redirect to QIWI Form {#qiwi-redirect}

>Response with payUrl parameter

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

After invoice creation, the link to QIWI Form is in `payUrl` field of the response.

<aside class="notice">
When opening Payment Form in Webview on Android, you should enable <code>settings.setDomStorageEnabled(true)</code> option.
</aside>

> Example of the link with successUrl

~~~shell
https://oplata.qiwi.com/form?invoiceUid=606a5f75-4f8e-4ce2-b400-967179502275&successUrl=https://merchant.ru/payments/#introduction
~~~

You can add the following parameter to the link:

| Parameter | Descripton | Type |
|--------------|--------|-------------|
| successUrl | URL for customer redirection in case of successful payment. Redirect proceeds after the successful 3DS authentication. URL should be UTF-8 encoded. | URL-encoded string |

By default, 3-D Secure is required on the QIWI Form.

## Customization of QIWI Payment Form  {#custom}

>Example of calling Custom Payment form

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
     "themeCode":"merchant01-style01"
    }
}
~~~

Add your style, customizable logo, background, and color of the buttons to the Payment form on the QIWI side. To do so, contact QIWI Support on <a href='mailto:payin@qiwi.com'>payin@qiwi.com</a> and provide the following information:

* unique alias for the Payment form (latin letters, digits, and `-` dash symbol);
* merchant name to be displayed on the Form;
* short description of your service (no more than 120 symbols);
* logo in PNG or SVG format with 310x36 size or proportionally larger;
* color of background and buttons, in HEX.

Some optional data are also used:

* Background image in PNG or SVG format with 382x560 size or proportionally larger;
* A list of three preloaded (the most often) amount of payment ;
* Contact e-mail to display on the Form;
* Success payment redirection URL;
* Yandex.Metrika id;
* Offer reference.

To use your Custom Payment form, send the alias for the Payment form in `"themeCode"` field in `customFields` object of API request [Invoice](#invoice_put). URL received in `payUrl` field of the response would display your Custom Payment form.

![Customer form](/images/Custom.png)

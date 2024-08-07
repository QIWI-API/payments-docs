# Payment Token {#payment-token-issue}

In **Payment protocol** card payment tokens generation is supported. The tokens can be used for debiting cards without entering card details. On issuing payment token, card details are stored securely on QIWI side.

## Features {#payment-token-special}

By default, the issue of payment tokens is disabled. Contact your manager in QIWI Support to enable that.

<aside class="warning">
If merchant's service uses QIWI Payment Form, contact your manager in QIWI Support to enable displaying list of cards linked to the payment tokens. Otherwise a customer may not use linked cards on the Form. A list of cards is displayed for payments from the same site for which the tokens are issued.
</aside>

## Card payment token issue {#merchant-form-token-issue}

>Example of request with invoice and payment token issue

~~~http
PUT /partner/payin/v1/sites/23044/bills/893794793973 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 10.00
   },
   "expirationDateTime": "2021-04-13T14:30:00+03:00",
    "customer": {
      "account":"token32"
   },
   "customFields": {},
   "flags":["BIND_PAYMENT_TOKEN"]
}
~~~

>Example of payment request with payment token issue

~~~http
PUT /partner/payin/v1/sites/test-01/payments/test-022 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: b2b-api.qiwi.com

{
   "amount": {
     "currency": "RUB",
     "value": 2211.24
   },
   "customer": {
   	"account": "token324",
    "phone": "79022222222"
   },
   "flags":["BIND_PAYMENT_TOKEN"]
}
~~~

>Example of response with payment token

~~~json
{
    "paymentId": "test-022",
    "billId": "autogenerated-c4479bb1-c916-4fba-8445-802592fa8d51",
    "createdDateTime": "2020-03-26T12:22:12+03:00",
    "amount": {
        "currency": "RUB",
        "value": 2211.24
    },
    "capturedAmount": "..",
    "refundedAmount": "..",
    "paymentMethod": "..",
    "createdToken": {
        "token": "66aebf5f-098e-4e36-922a-a4107b349a96",<= payment token
        "name": "411111******1111"
    },
    "customer": {
        "account": "token324",
        "phone": "79022222222"
    },
    "status": ".."
}
~~~

<aside class="warning">Card payment token is created only after the successful payment authorization by the issuer bank.</aside>

Include additional options in the API request [Payment](#payments) or [Invoice](#invoice_put), depending on which API you use:

* `"flags": ["BIND_PAYMENT_TOKEN"]` is a flag for issuing a payment token.
* `customer.account` is a unique customer ID in the RSP system.

**To secure card data, use different `account` values for different clients.**

You will receive the card payment token details:

* In the synchronous API response to the [payment request](#payments) or [payment authorization completion](#payment_complete) request in the `createdToken` field.
* In the [PAYMENT notification](#payment-callback) after payment is completed successfully, in the `tokenData` field.

<aside class="warning">
The payment token is linked with the site ID and the customer ID that you have specified in the original API request. The customer will be able to make payment by payment token only on this site.

To make the payment token valid on all sites of the same merchant, send a request to <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

## FPS payment token issue {#merchant-form-token-issue-sbp}

> Request body for the FPS payment token issue

~~~json
{
  "qrCodeUid": "Test123",
  "qrCode": {
    "type": "TOKEN",
    "image": {
        "mediaType": "image/png",
        "width": 300,
        "height": 300
      }
  },
  "tokenizationPurpose": "Описание с деталями привязки счета",
  "tokenizationAccount": "3e2322",
  "flags": ["CREATE_TOKEN"]
}
~~~

> Response body for the FPS payment token issue

~~~json
{
  "qrCodeUid": "Test123",
  "qrCode": {
    "type": "TOKEN",
    "ttl": 10,
    "image": {
        "mediaType": "image/png",
        "width": 300,
        "height": 300,
        "content": "iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAYAA"
    },
    "payload": "https://qr.nspk.ru/AD10006M8KH234K782OQM0L13JI31LQDб",
    "status": "CREATED"
  },
  "tokenizationPurpose": "Описание с деталями привязки счета",
  "flags": ["CREATE_TOKEN"],
  "token": {
      "status": "CREATED",
      "value": "a4a312345-6789-1234-a567-89a1234567a0",
      "expiredDate": "2023-08-11T10:10:32+03:00"
  },
  "createdOn": "2022-08-11T20:10:32+03:00"
}
~~~

> Request body for FPS QR code issue with token creation

~~~json
{
  "qrCodeUid": "Test123",
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "image": {
        "mediaType": "image/png",
        "width": 300,
        "height": 300
      }
  },
  "tokenizationPurpose": "Описание с деталями привязки счета",
  "tokenizationAccount": "3e2322",
  "redirectUrl": "http://someurl.com"
  "flags": ["CREATE_TOKEN"]
}
~~~

> Response body for FPS QR code issue with token creation

~~~json
{
  "qrCodeUid": "Test123",
  "amount": {
    "value": 100.00,
    "currency": "RUB"
  },
  "qrCode": {
    "type": "DYNAMIC",
    "ttl": 10,
    "image": {
        "mediaType": "image/png",
        "width": 300,
        "height": 300,
        "content": "iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAYAA"
    },
    "payload": "https://qr.nspk.ru/AD10006M8KH234K782OQM0L13JI31LQDб",
    "status": "CREATED"
  },
  "redirectUrl": "http://someurl.com",
  "tokenizationPurpose": "Описание с деталями привязки счета",
  "flags": ["CREATE_TOKEN"],
  "token": {
    "status": "CREATED",
    "value": "a4a312345-6789-1234-a567-89a1234567a0",
    "expiredDate": "2023-08-11T10:10:32+03:00"
  },
  "createdOn": "2022-08-11T20:10:32+03:00"
}
~~~

To create a Faster Payment System (FPS) payment token, use one of the following options:

1. Without payment.

   Use [Faster Payment System QR Code](#qr-code-sbp) API request with data:

   * `qrCode` object with QR code parameters:
     * `qrCode.type` — `TOKEN`.
     * `qrCode.image` — type and size of the QR code image.
   * `tokenizationAccount` — unique client identifier in merchant's system.
   * `"flags":["CREATE_TOKEN"]`.
   *`tokenizationPurpose` — token description.

2. Payment with account binding.

   <aside class="warning">FPS payment token is issued only after successful payment authorization by the Issuer.</aside>

   Use [Faster Payment System QR Code](#qr-code-sbp) API request with data:

   * `qrCode` object with QR code parameters:
     * `qrCode.type` — `DYNAMIC`.
     * `qrCode.image` — type and size of the QR code image.
   * `amount` — payment amount.
   * `tokenizationAccount` — unique client identifier in merchant's system.
   * `"flags":["CREATE_TOKEN"]`.
   * `tokenizationPurpose` — token description.

**To secure card data, use different `tokenizationAccount` parameters for different clients.**

FPS payment token is received in `token` parameter of the response and in the [TOKEN notification](#token-callback).

<aside class="notice">
The payment token is linked with the site ID and the customer ID that you have specified in the original API request. The customer will be able to make payment by payment token only on this site.
</aside>

See details on how to make payments by the FPS token in [this section](#token-sbp-payment).

## Disabling the payment token {#delete-payment-token}

To disable a payment token, make a [Payment token deletion](#delete-token) API request. In the JSON-body of the request specify the parameters:

* `customerAccountId` is a unique customer ID in merchant's system linked to a payment token.
* `token` is a payment token to stop.

A successful response means that the payment token for the specified customer is no longer valid.

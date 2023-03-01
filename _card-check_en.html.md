# Customer Card Verification {#check-card}

Merchant can use a verification service to check for customer's card details, its validity and availability for purchases. Funds on cardholder's account are not debited until card recurring is confirmed or payment transaction is initiated.

If card verification is passed successfully, [payment token](#merchant-token-pay) can be issued for the card.

<aside class="warning">
The payment token is linked with the site ID and the customer ID that you have specified in the starting request. The customer will be able to make payment by payment token only on this site.

To make the payment token valid on other sites of the same merchant, send a request to <a href="mailto:payin@qiwi.com">QIWI Support</a>.
</aside>

By default, access to the verification service is disabled. Contact your manager in QIWI Support to enable that.

## How to use service with QIWI Payment Form {#how-to-check-card-qiwi-payform}

>Request to issue invoice with card verification

~~~http
PUT /partner/payin/v1/sites/site-01/bills/892323232111 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd1xxxx356f9
Content-type: application/json
Host: api.qiwi.com

{
    "expirationDateTime": "2023-09-14T14:30:00+03:00",
    "customer": {
        "account":"test-123"
    },
    "flags": ["CHECK_CARD","BIND_PAYMENT_TOKEN"]
}
~~~

>Successful response body

~~~json
{
    "siteId": "site-01",
    "billId": "892323232111",
    "amount": {
      "value": "0.00",
      "currency": "RUB"
    },
    "status": {
      "value": "CREATED",
      "changedDateTime": "2021-08-13T15:43:41"
    },
    "creationDateTime": "2021-08-13T15:43:41",
    "expirationDateTime": "2023-09-14T14:30:00",
    "payUrl": "https://oplata.qiwi.com/validation/card?invoiceUid=fxxxxx-854c-4e56-xxxx-eb49aef2xxxx"
}
~~~

>Notification with card verification result

~~~json
{
   "checkPaymentMethod":{
      "status":"SUCCESS",
      "isValidCard":true,
      "threeDsStatus":"WITHOUT",
      "cardInfo":{
         "issuingCountry":"0",
         "issuingBank":"not present",
         "paymentSystem":"VISA",
         "fundingSource":"UNKNOWN",
         "paymentSystemProduct":"UNKNOWN"
      },
      "createdToken":{
         "token":"xxxxxxx-a53a-4de1-8aa4-xxxxxxx",
         "name":"411111******1111",
         "expiredDate":"2021-11-30T00:00:00+03:00",
         "account":"some_account"
      },
      "requestUid":"uuuuuu-84f6-4044-9dd1-ddddddddd",
      "paymentMethod":{
         "type":"CARD",
         "maskedPan":"411111******1111",
         "cardHolder":"",
         "cardExpireDate":"11/2021"
      },
      "checkOperationDate":"2021-08-13T15:44:01+03:00"
   },
   "type":"CHECK_CARD",
   "version":"1"
}
~~~

1. Send [invoice request](#invoice_put) with additional parameter `"flags":["CHECK_CARD", "BIND_PAYMENT_TOKEN"]`. For payment token generation, set `customer.account` parameter with unique customer identifier in the merchant's system. **Do not specify invoice amount in the request (`amount` parameter)**.
2. Extract `billId` parameter from the response — it is used on step 4. [Redirect customer to the Payment Form](#qiwi-redirect) — reference URL taken from `payUrl` field of the response.
3. On the Payment Form, customer provides card details and submits it for verification. On the form, 3-D Secure authentication performs for customer.
    ![check card](/images/check-card-payin.png)
4. When card verification finishes, you get [CHECK_CARD notification](#checkcard-callback) with the result, or you can [request current status of the verification](#card-check-info) — put there `billId` identifier from step 2 as a unique identifier of the card verification. Result includes:

   * information about whether card is accessible for purchases in `isValidCard` field (`true` — card number is valid and card can be purchased);
   * payment token data in `createdToken` field.

## How to use service with API {#how-to-check-card}

>Request with card verification

~~~http
PUT /partner/payin/v1/sites/site-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9 HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "cardData": {
        "pan": "1111222233334444",
        "expiryDate": "12/34",
        "cvv2": "123",
        "holderName": "Super Man"
    },
    "tokenizationData": {
        "account": "cat_girl"
    }
}
~~~

>Successful response body

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "SUCCESS",
    "isValidCard": true,
    "threeDsStatus": "WITHOUT",
    "checkOperationDate": "2021-07-29T16:30:00+03:00",
    "cardInfo": {
        "issuingCountry": "RUS",
        "issuingBank": "Qiwi bank",
        "paymentSystem": "VISA",
        "fundingSource": "DEBIT",
        "paymentSystemProduct": "Platinum..."
    },
    "createdToken": {
        "token": "1a77343a-dd8a-11eb-ba80-0242ac130004",
        "name": "111122******4444",
        "expiredDate": "2034-12-01T00:00:00+03:00",
        "account": "cat_girl"
    }
}
~~~

> Response with 3DS authentication

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "WAITING_3DS",
    "requirements": {
        "pareq": "Some string pareq",
        "acsUrl": "http://xxxxxxx"
    }
}
~~~

>Response with verification error

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "ERROR"
}
~~~

>Request to finish 3DS in card verification

~~~http
POST /partner/payin/v1/sites/site-01/validation/card/requests/acd7bf20-22e2-4cbf-a218-38d90e9f29b9/complete HTTP/1.1
Accept: application/json
Authorization: Bearer 5c4b25xx93aa435d9cb8cd17480356f9
Content-type: application/json
Host: api.qiwi.com

{
    "pares": "Some string pares"
}
~~~

>Response

~~~json
{
    "requestUid": "acd7bf20-22e2-4cbf-a218-38d90e9f29b9",
    "status": "SUCCESS",
    "isValidCard": true,
    "threeDsStatus": "PASSED",
    "checkOperationDate": "2021-07-29T16:30:00+03:00",
    "cardInfo": {
        "issuingCountry": "RUS",
        "issuingBank": "Qiwi bank",
        "paymentSystem": "VISA",
        "fundingSource": "DEBIT",
        "paymentSystemProduct": "Platinum..."
    },
    "createdToken": {
        "token": "1a77343a-dd8a-11eb-ba80-0242ac130004",
        "name": "111122******4444",
        "expiredDate": "2034-12-01T00:00:00+03:00",
        "account": "cat_girl"
    }
}
~~~

1. Send ["Check card" API request](#card-check-api). Put in the request:
   * Unique verification identifier (`requestUid` field in the request URL). 
   * Card data (`cardData` in the request body). Required parameters — PAN, card expiration date, and CVV2.

   <aside class="notice">If necessary, card CVV may be configured as an optional parameter. Contact your manager in QIWI Support.
   </aside>

   For payment token generation, set `tokenizationData.account` parameter to unique customer's identifier in the merchant's system.
2. In the response, card verification result returns in `isValidCard` field (`true` means card is valid for purchases). Payment token data return in `createdToken` JSON object.

To make sure that the cardholder themselves entered card number, you can use 3-D Secure additional authentication in the card verification service. Contact [QIWI Support](mailto:payin@qiwi.com) to enable or disable 3-D Secure (3DS) procedure. If 3DS is enabled, you receive `"requirements"` object with ACS URL for customer redirect (in this case, `status` field has `"WAITING_3DS"` value).

Verification scenario is similar to [payment operation](#merchant-threeds):

1. [Redirect customer to authentication page](#merchant-threeds).
2. Finish 3-D Secure by ["Complete 3DS on card verification" request](#card-check-complete). Specify the same `requestUid` from the initial card verification request.
3. If 3-D Secure check is finished successfully, `isValidCard` field contains information about card validity (`true` means card is valid for purchases). Payment token data return in `createdToken` JSON object.

When verification finishes, you get [CHECK_CARD notification](#checkcard-callback) with the result, or you can always [get current card verification status by API request](#card-check-info).

## Card verification statuses {#card-check-statuses}

| Status      | Description                                                          |
|-------------|----------------------------------------------------------------------|
| INIT        | Reference URL to card verification form awaiting for customer action |
| SUCCESS     | Card verification is successful                                      |
| ERROR       | Card erification error                                               |
| WAITING_3DS | Awaiting 3-D Secure authentication completion                        |

# Statuses, Types of the Operations, and Error Codes {#statuses}

## Error codes {#http-errors}

The Payment Protocol uses the following HTTP-codes of errors for API requests:

Error Code | Meaning
---------- | -------
400 | Bad Request — Client request is invalid (error in request' data or format).
401 | Unauthorized — Client API access key is wrong.
403 | Forbidden — Access to API is forbidden.
404 | Not Found — The specified resource could not be found.
405 | Method Not Allowed — You tried to create a payment with an invalid method.
406 | Not Acceptable — Client request' data format isn't JSON.
410 | Gone — The resource requested has been removed from our servers.
429 | Too Many Requests — You are sending requests too frequently.
500 | Internal Server Error — We had a problem with our server. Try again later. If response body is empty, repeat the request with the same parameters. If the body is non-empty, make [payment status request](#payment_status)/[invoice status request](#invoice-details).
502 | Bad Gateway — No connection to service
503 | Service Unavailable — We're temporarily offline for maintenance. Please try again later.

## Operation types {#operation-types}

Operation type is returned in `{operation}.type` field of the [notification](#callback).

Operation type | Description
---|----
PAYMENT | The payment. There can be field `flag: [ "SALE" ]` (one-step payment) or `flag: [ "AUTH" ]` (two-step payment with holding funds) in the notification.
CAPTURE | Operation of the payment confirmation.
REFUND | Operation of the refund. There can be field `flag: [ "REVERSAL" ]` in the notification. It means that there was no financial operation (charging from customer's account) and commission fee would not be hold.

## Operation statuses {#operation-statuses}

Operation status reflects its current state.

### API responses {#api-statuses}

API returns synchronous status of the operation in the field `status.value` of the response. 

Status in notifications is in the field `{operation}.status.value`.

In the following table all possible statuses and corresponding [operation types](#operation-types), where each status is used.

 Operation status |Operation type | Status description
------------------|---------------|----------------------------------------------------------------------------------------------------------------
 PAYMENT          |WAITING        | Awaiting for 3DS authentication
 PAYMENT          |DECLINED       | Request for authorization is rejected (in synchronous responses)
 PAYMENT          |DECLINE        | Request for authorization is rejected (in asynchronous responses)
 REFUND           |DECLINE        | Request for refund is rejected
 CAPTURE          |DECLINE        | Request for payment confirmation is rejected
 CAPTURE          |DECLINED       | Request for payment confirmation is rejected (in [API response to the status request](#capture_status))
 PAYMENT          |COMPLETED      | Request for authorization is successfully processed
 REFUND           |COMPLETED      | Request for refund is successfully processed
 CAPTURE          |COMPLETED      | Request for payment confirmation is successfully processed

<aside class="notice">For invoice operations only one status is used: <code>CREATED</code>.</aside>

### Notifications {#notification-statuses}

Status in notifications is in the field `{operation}.status.value`.

In the following table all possible statuses and corresponding [operation types](#operation-types), where each status is used.

 Operation type |Operation status | Status description
----------------|-----------------|-----------------------------------------------------------
 PAYMENT        |DECLINE          | Request for authorization is rejected
 REFUND         |DECLINE          | Request for refund is rejected
 CAPTURE        |DECLINE          | Request for payment confirmation is rejected
 PAYMENT        |SUCCESS          | Request for authorization is successfully processed
 REFUND         |SUCCESS          | Request for refund is successfully processed
 CAPTURE        |SUCCESS          | Request for payment confirmation is successfully processed

## API errors reference {#reason-codes}

API errors describe a reason for rejection of the operation. API errors are present:

* in API responses — field `status.reason`;
* in notifications — field `status.reasonCode`.

Some API errors are accompanied by [details with recommended troubleshooting](#ps-error-codes) in the `status.psErrorCode` field.

<aside class="notice">
API errors list can be expanded.
</aside>

API error| Description
------------------|--------
INVALID_STATE| Incorrect transaction status
INVALID_AMOUNT| Incorrect payment amount
INVALID_RECEIVER_DATA | Error on transmitting receiver data
DECLINED_BY_MPI | Rejected by MPI
DECLINED_BY_FRAUD| Rejected by fraud monitoring
REATTEMPT_NOT_PERMITTED| Re-attempting authorization request is forbidden due to a Payment system rules
REATTEMPT_NOT_PERMITTED_BY_PS| Operation rejected by Payment system. Error details with recommended troubleshooting are in the `status.psErrorCode` [field](#ps-error-codes). Re-attempting the operation is not possible for the card
GATEWAY_INTEGRATION_ERROR| Acquirer integration error
GATEWAY_TECHNICAL_ERROR| Technical error on acquirer side
ACQUIRING_MPI_TECH_ERROR| Technical error on 3DS authentication
ACQUIRING_GATEWAY_TECH_ERROR| Technical error
ACQUIRING_ACQUIRER_ERROR| Technical error
ACQUIRING_AUTH_TECHNICAL_ERROR| Error on funds authorization
ACQUIRING_ISSUER_NOT_AVAILABLE| Issuer error. Issuer is not available at the moment
ACQUIRING_SUSPECTED_FRAUD| Issuer error. Fraud suspicion
ACQUIRING_LIMIT_EXCEEDED| Issuer error. Some limit exceeded
ACQUIRING_NOT_PERMITTED| Issuer error. Operation not allowed
ACQUIRING_INCORRECT_CVV| Issuer error. Incorrect CVV
ACQUIRING_EXPIRED_CARD| Issuer error. Incorrect card expiration date
ACQUIRING_INVALID_CARD| Issuer error. Verify card data
ACQUIRING_INSUFFICIENT_FUNDS| Issuer error. Not enough funds
ACQUIRING_UNKNOWN| Unknown error
BILL_ALREADY_PAID| Invoice is already paid
PAYIN_PROCESSING_ERROR| Payment processing error
PAYMENT_EXPIRED_3DS | 3-D Secure authentication not passed
TRY_AGAIN_LATER | Repeat the request later

## Error detail codes {#ps-error-codes}

Error detail code with recommended action from a Payment system is returned in the `status.psErrorCode` field of the response.

| Code | Corresponding [API error](#reason-codes) | Error details and recommended troubleshooting                                                                                                                  |
|------|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 03   | REATTEMPT_NOT_PERMITTED_BY_PS            | Operation with this MCC forbidden by the card issuer                                                                                                           |
| 04   | REATTEMPT_NOT_PERMITTED_BY_PS            | Card blocked                                                                                                                                                   |
| 05   | ACQUIRING_NOT_PERMITTED                  | The transaction was rejected due to other reasons                                                                                                              |
| 12   | REATTEMPT_NOT_PERMITTED_BY_PS            | Operation of that type forbidden by Rules and Standards of a Payment system                                                                                    |
| 13   | ACQUIRING_NOT_PERMITTED                  | Incorrect amount. Repeat the operation with a different amount                                                                                                 |
| 14   | ACQUIRING_NOT_PERMITTED                  | Incorrect card number. Enter correct number or use another card                                                                                                |
| 15   | REATTEMPT_NOT_PERMITTED_BY_PS            | There is no issuer for the card                                                                                                                                |
| 30   | ACQUIRING_NOT_PERMITTED                  | Operation rejected. Contact QIWI support to get additional information                                                                                         |
| 33   | REATTEMPT_NOT_PERMITTED_BY_PS            | Card is not available for use                                                                                                                                  |
| 41   | REATTEMPT_NOT_PERMITTED_BY_PS            | Card is not available for use                                                                                                                                  |
| 43   | REATTEMPT_NOT_PERMITTED_BY_PS            | Card is not available for use                                                                                                                                  |
| 51   | ACQUIRING_INSUFFICIENT_FUNDS             | The Client might be recommended to repeat the operation after account replenishment                                                                            |
| 54   | ACQUIRING_EXPIRED_CARD                   | The expiration date of the card is missing or transmitted incorrectly                                                                                          |
| 57   | REATTEMPT_NOT_PERMITTED_BY_PS            | This type of operation is not available for the card                                                                                                           |
| 58   | REATTEMPT_NOT_PERMITTED_BY_PS            | This type of operation is not available for the acquirer                                                                                                       |
| 61   | ACQUIRING_LIMIT_EXCEEDED                 | The Client may be recommended to try the transaction again on another day — after the Issuer resets the limit on the total amount of transactions of this type |
| 62   | REATTEMPT_NOT_PERMITTED_BY_PS            | The transaction is not available due to restrictions on the card or account of the Cardholder                                                                  |
| 63   | ACQUIRING_NOT_PERMITTED                  | The transaction was rejected, contact Qiwi support for more information                                                                                        |
| 65   | ACQUIRING_LIMIT_EXCEEDED                 | The Client may be recommended to retry the transaction on another day — after the Issuer resets the limit on the total number of transactions of this type     |
| 75   | ACQUIRING_INCORRECT_CVV                  | The transaction was rejected due to incorrect PIN code entered earlier                                                                                         |
| 76   | REATTEMPT_NOT_PERMITTED_BY_PS            | Rejecting the cancellation of a request due to the absence of the original request                                                                             |
| 78   | REATTEMPT_NOT_PERMITTED_BY_PS            | Denying a request due to an attempt to use an inactive card                                                                                                    |
| 86   | ACQUIRING_INCORRECT_CVV                  | The transaction was rejected due to incorrect PIN code entered earlier                                                                                         |
| 88   | ACQUIRING_AUTH_TECHNICAL_ERROR           | The transaction was rejected due to cryptography error, can occur due to incorrect CVV2/CVC2                                                                   |
| 91   | ACQUIRING_ISSUER_NOT_AVAILABLE           | The Client may be advised to retry the transaction at another time – after the Issuer recovers the functioning                                                 |
| 92   | REATTEMPT_NOT_PERMITTED_BY_PS            | Rejection by the Payment System due to the impossibility of carrying out the transaction                                                                       |
| 93   | REATTEMPT_NOT_PERMITTED_BY_PS            | Denial of a request due to violation of legal requirements                                                                                                     |
| 94   | REATTEMPT_NOT_PERMITTED_BY_PS            | Rejecting a duplicate request                                                                                                                                  |
| 96   | ACQUIRING_NOT_PERMITTED                  | The Client may be advised to retry the transaction at another time – after the Issuer ot the Platform recovers the functioning                                 |
| TS   | REATTEMPT_NOT_PERMITTED_BY_PS            | Rejection of the request due to cancellation of the Cardholder's long-term order                                                                               |
| CB   | ACQUIRING_ACQUIRER_ERROR                 | The transaction was rejected due to incorrect date of birth of the Cardholder                                                                                  |
| CD   | REATTEMPT_NOT_PERMITTED_BY_PS            | The transaction was rejected due to death of the Cardholder                                                                                                    |

### Rules for working with detailed information {#ps-error-codes-rules}
There are two groups of error codes:

* Group 1: `03`, `04`, `12`, `15`, `33`, `41`, `43`, `57`, `58`, `62`, `76`, `78`, `92`, `93`, `94`, `CD`, `TS`, `05`.
* Group 2: `13`, `14`, `30`, `54`, `51`, `61`, `63`, `65`, `75`, `86`, `88`, `91`, `96`, `CB`.

According to the NSPC rules, the following conditions for repeating transactions with non-3DS authorization on MIR cards are applied

* After a response from group 1, no more transactions can be performed during the day.
* After the response from group 2, another attempt to perform transactions is allowed during the day.

Restrictions apply to the final recipient, if we have received the corresponding response codes.

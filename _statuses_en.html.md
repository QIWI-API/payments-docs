# Statuses, Types of the Operations, and Error Codes {#statuses}

## Error codes {#http-errors}

The Payment Protocol uses the following HTTP-codes of errors for API requests:

Error Code | Meaning
---------- | -------
400 | Bad Request — Your request is invalid (error in request' data or format).
401 | Unauthorized — Your API access key is wrong.
403 | Forbidden — Access to API is forbidden.
404 | Not Found — The specified resource could not be found.
405 | Method Not Allowed — You tried to create a payment with an invalid method.
406 | Not Acceptable — Your request' data format isn't JSON.
410 | Gone — The resource requested has been removed from our servers.
429 | Too Many Requests — You are sending requests too frequently.
500 | Internal Server Error — We had a problem with our server. Try again later. If response body is empty, repeat the request with the same parameters. If the body is non-empty, make [payment status request](#payment_status)/[invoice status request](#invoice_get).
502 | Bad Gateway — No connection to service
503 | Service Unavailable — We're temporarily offline for maintenance. Please try again later.

## Operation types {#operation-types}

Operation type is returned in `{operation}.type` field of the [notification](#callback).

Operation type | Description
---|----
PAYMENT | The payment. There can be field `flag: [ "SALE" ]` (one-step payment) or `flag: [ "AUTH" ]` (two-step payment with holding funds) in the notification.
CAPTURE | Operation of the confirmation. 
REFUND | Operation of the refund. There can be field `flag: [ "REVERSAL" ]` in the notification. It means that there was no financial operation (charging from customer's account) and commission fee would not be hold.

## Operation statuses {#operation-statuses}

Operation status reflects its current state. 

API returns synchronous status of the operation in the field `status.value` of the response. 

Status in notifications is in the field `{operation}.status.value`. 

In the following table all possible statuses and corresponding [operation types](#operation-types), where each status is used.

Operation status | Operation type | Status description | Where it is returned
---|----|-----|------
WAITING | PAYMENT | Awaiting for 3DS authentication| API responses
DECLINE | PAYMENT | Request for authentication is rejected | Notifications, API responses
DECLINE | REFUND | Request for refund is rejected| Notifications, API responses
DECLINE | CAPTURE | Request for payment confirmation is rejected| Notifications, API responses
SUCCESS | PAYMENT | Request for authentication is successfully processed| Notifications
COMPLETED | PAYMENT |Request for authentication is successfully processed|API responses
SUCCESS | REFUND | Request for refund is successfully processed|Notifications
COMPLETED | REFUND | Request for refund is successfully processed|API responses
SUCCESS | CAPTURE | Request for payment confirmation is successfully processed|Notifications
COMPLETED | CAPTURE | Request for payment confirmation is successfully processed|API responses

<aside class="notice">For invoices only one status is used: <code>CREATED</code>.</aside>

## API errors reference {#reason-codes}

API errors describe a reason for rejection of the operation. API errors are present: 

- in API responses — field `status.reason`;
- in notifications — field `status.reasonCode`.

API error| Description
------------------|--------
INVALID_STATE| Incorrect transaction status
INVALID_AMOUNT| Incorrect payment amount
DECLINED_BY_MPI | Rejected by MPI
DECLINED_BY_FRAUD| Rejected by fraud monitoring
GATEWAY_INTEGRATION_ERROR| Acquirer integration error
GATEWAY_TECHNICAL_ERROR| Technical error on acquirer side
ACQUIRING_MPI_TECH_ERROR| Technical error on 3DS authentication
ACQUIRING_GATEWAY_TECH_ERROR| Technical error
ACQUIRING_ACQUIRER_ERROR| Technical error
ACQUIRING_AUTH_TECHNICAL_ERROR| Error on funds authentication
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

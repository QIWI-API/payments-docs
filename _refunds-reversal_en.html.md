# Refunds and Reversals {#operations-refund-etc}

Refund and reversal operations are available in the following conditions:

* Refunds are only available for successfully completed payment transactions.
* Reversal of the transaction is possible for [two-step payment operations](#merchant-form-hold) and only for those without received [confirm (CAPTURE)](#merchant-capture).

When the payment is refunded, the QIWI commission fee for the payment is not refunded. An exception is if the reversal operation is performed when the payment is refunded. In this case, there is no financial transaction (charging funds from the customer's account) and there is no commission fee.

## Refunds on paid invoices {#refunds-invoices}

To make a refund on the paid invoice, use the API request [Refund](#refund-api).

## Refunds on processed payments {#refunds-payments}

A refund on the payment is only possible for a successful payment. The refund can be both partial and complete. In the first case, the entire amount of the accepted payment is refunded. In the second case, only a part of the amount of payment is refunded. Before you request a refund for the payment, check that the payment has been successfully completed and is in `SUCCESS` status.

To make a refund on a payment, use the API request [Refund](#refund-api).

<aside class="notice">
There is a possibility to decline (remove) incorrect refunds on card payments (see <a href="#refund_decline">the corresponding API request</a>). Contact your account manager and request transfer to postponed refunds scheme.
</aside>

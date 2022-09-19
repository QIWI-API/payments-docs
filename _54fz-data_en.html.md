# Electronic Receipt Transfer (by Fed.Rule 54) {#cheque}

To work on 54-FZ Rule, the Payment Protocol provides a tool to interact with your online cash register. Information to form a fiscal check is passed on to the JSON-object `cheque` of [invoice](#invoice_put) and [payment](#payments) operation API requests.

To activate the fiscal check service, contact your manager in QIWI Support with the TIN number with which your organization is registered in the online cash rental service.

For ATOL service, provide the following data also:

* RSP's email for receipt printing
* Full title of the organization
* Fiscal data operator details (name, TIN, URL)
* Login / password for security token generation
* Commodity group code

## JSON-object description

~~~ json
{
 ...
 "cheque" : {
  "sellerId" : 3123011520,
	"customerContact" : "Test customer contact",
  "chequeType" : "COLLECT",
	"taxSystem" : "OSN",
	"positions" : [
    {
      "quantity" : 1,
      "price" : {
        "value" : 7.82,
        "currency" : "RUB"
      },
      "tax" : "NDS_0",
      "paymentSubject" : "PAYMENT",
      "paymentMethod" : "FULL_PAYMENT",
      "description" : "Test description"
    }
	]
 }
}
~~~

Parameter|Req.|Type|Description
--------|-------|----------|--------
sellerId|Y|Number|Organization's TIN
chequeType|Y|Number|Cash operation (1054 fiscal tag):<br> `COLLECT` – Incoming cash <br> `COLLECT_RETURN` - Cash return <br> `CONSUME` - Outgoing cash <br> `CONSUME_RETURN` - Outgoing cash return
customerContact|Y|String(64)|Customer's phone or e-mail  (fiscal tag 1008)
taxSystem|Y|Number|Tax system (fiscal tag 1055):<br> `OSN` - General, "ОСН" <br>`USN` – Simplified income, "УСН доход" <br>`USN_MINUS_CONSUM`  – Simplified income minus expense, "УСН доход - расход" <br>`ENVD` – United tax to conditional income, "ЕНВД" <br>`ESN` - United agriculture tax, "ЕСН" <br>`PATENT` – Patent tax system, "Патент"
positions|Y|array|Commodities list
--------|-------|----------|--------
quantity|Y|Number|Quantity (fiscal tag 1023)
price|Y|Number|One item price, with discounts and extra charges (fiscal tag 1079)
tax|Y|Number|VAT rate (fiscal tag 1199):<br>`NDS_CALC_18_118` - VAT rate 18% (18/118) <br>`NDS_CALC_10_110` – VAT rate 10% (10/110) <br>`NDS_0` – VAT rate 0% <br>`NO_NDS` – VAT not applicable<br>`NDS_CALC_20_120` – VAT rate 20% (20/120) (20/120)
description|Y|string(128)|Name
paymentMethod|Y|Number|Cash type (fiscal tag 1214):<br>`ADVANCED_FULL_PAYMENT` – payment in advance 100%. Full payment in advance before commodity provision<br>`PARTIAL_ADVANCE_PAYMENT`  – payment in advance. Partial payment before commodity provision<br>`ADVANCE` – prepayment<br>`FULL_PAYMENT` – full payment, taking into account prepayment, with commodity provision<br>`PARTIAL_PAYMENT` – partial payment and credit payment. Partial payment for the commodity at the moment of delivery, with future credit payment.<br>`CREDIT`  – credit payment. Commodity is delivered with no payment at the moment and future credit payment is expected.<br>`CREDIT_PAYMENT` – payment for the credit. Commodity payment after its delivery with future credit payment.
paymentSubject|Y|Number|Payment subject (fiscal tag 1212):<br>`COMMODITY` – commodity except excise commodities (name and other properties describing the commodity).<br>`EXCISE_COMMODITY` – excise commodity (name and other properties describing the commodity).<br>`WORK` – work (name and other properties describing the work). .<br>`SERVICE` – service (name and other properties describing the service).<br>`GAMBLING_RATE` – gambling rate (in any gambling activities).<br>`GAMBLING_PRIZE` – gambling prize payment (in any gambling activities)в.<br>`LOTTERY_TICKET` – lottery ticket payment (in accepting payments for lottery tickets, including electronic ones, lottery stakes in any lottery activities).<br>`LOTTERY_PRIZE` – lottery prize payment (n any lottery activities). <br>`GRANTING_RESULTS_OF_INTELLECTUAL_ACTIVITY` – provision of rights to use intellectual activity results.<br>`PAYMENT` – payment (advance, pre-payment, deposit, partial payment, credit, fine, bonus, reward, or any similar payment subject).<br>`AGENCY_FEE` – agent's commission (in any fee to payment agent, bank payment agent, commissioner or other agent service).<br>`COMPAUND_PAYMENT_SUBJECT` – multiple payment subject (in any payment constituent subject to any of the above).<br>`OTHER_PAYMENT_SUBJECT` – other payment subject not related to any of the above.

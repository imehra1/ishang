# ishang

select
be.id as [Billing Event ID]
, be.eventType as [Billing Type]
, atr.invoiceNumber as [Invoice Number]
, atr.invoiceStatus as [Invoice Status]
, atr.transactionDate as [Transaction Date]
, atr.transactionEffectiveDate as [Transaction Effective Date]
, atr.dueDate as [Due Date]
, atr.DTYPE AS [Accounting Transaction Type]
, p.startDt as [Period Start Date]
, p.endDt as [Period End Date]
, ba.accountNumber as [Billing Account Number]
, Inv.currentDue as [Amount]
, Inv.currentDue as [Current Due]
, Inv.priorDue as [Prior Due]
, Inv.totalDue as [Total Due]
, Inv.priorPeriodAdjustments as [Prior Period Adjustments]
, CASE WHEN FIN.FirstInvoiceNumber = atr.invoiceNumber THEN 1 ELSE 0 END AS [First Invoice Number]
from BillingAccount ba
inner join BillingEvent be on ba.id = be.[account_id ]
inner join AccountingTransaction atr on atr.billingEvent_id = be.id
INNER JOIN dbo.Period p ON atr.period_id = p.id 
inner JOIN dbo.InvoiceSnapshot Inv on Inv.id = atr.snapshot_id
left join
	(select ba.accountNumber, MIN(atr.invoiceNumber) as FirstInvoiceNumber from
		BillingAccount ba
		inner join BillingEvent be on ba.id = be.[account_id ]
		inner join AccountingTransaction atr on atr.billingEvent_id = be.id 
		where atr.invoiceStatus in ('PAID_IN_FULL', 'ISSUED')
		group by ba.accountNumber
	)FIN on ba.accountNumber = FIN.accountNumber


where atr.DTYPE = 'BenefitsInvoice'

# CHG-001 — Add Debtor Ageing Categories to Finance Reporting

## Change Request

Finance required outstanding customer balances to be grouped into standard debtor-ageing categories for collections and management reporting.

## Business Reason

The original finance reporting showed outstanding balances but did not clearly distinguish current debt from increasingly overdue balances.

Ageing categories improve collections prioritisation and help Finance identify higher-risk customer balances.

## Requested Categories

The reporting model supports:

- Current
- 1–30 Days
- 31–60 Days
- 61–90 Days
- 90+ Days

## Implementation

The Gold debtor-ageing model uses the reporting date and invoice due date to calculate:

`days_overdue`

Outstanding invoices are then assigned to the appropriate ageing bucket.

A reporting sort column was also added:

| Ageing Bucket | Sort Order |
|---|---:|
| Current | 1 |
| 1–30 Days | 2 |
| 31–60 Days | 3 |
| 61–90 Days | 4 |
| 90+ Days | 5 |

This ensures Power BI and other reporting tools display ageing categories in the correct business order rather than alphabetical order.

## Current Results

Reporting date:

`2026-08-31`

Current populated ageing categories:

| Ageing Bucket | Invoice Count | Outstanding Value |
|---|---:|---:|
| Current | 23 | R736,140.85 |
| 1–30 Days | 56 | R2,108,531.41 |
| 31–60 Days | 7 | R336,752.39 |

The `61–90 Days` and `90+ Days` rules are implemented but contain no invoices in the current dataset.

## Validation

Final validation confirmed:

- Debtor ageing rows: **86**
- Total outstanding: **R3,181,424.65**
- Invalid ageing sort rows: **0**

The total outstanding balance continues to reconcile exactly to the invoice/payment reconciliation Gold model.

## Engineering Concepts Demonstrated

- change-request implementation
- debtor ageing
- finance reporting
- PySpark transformations
- Gold-layer modelling
- reporting sort logic
- reconciliation validation
- Power BI-ready data design

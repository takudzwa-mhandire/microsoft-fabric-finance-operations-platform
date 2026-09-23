# INC-001 — Invoice and Payment Reconciliation Variance

## Incident Summary

Finance reported that cash collections in the reporting layer were higher than the payment source extract.

The incident was investigated across the payment source, Bronze, Silver and reporting-processing layers.

A controlled duplicate-payment scenario was used to reproduce and investigate the failure safely without modifying trusted production-style Silver or Gold tables.

## Business Impact

The reporting layer showed cash collections greater than the trusted payment source.

This would prevent Finance from relying on collection reporting until the variance was identified and resolved.

## Known-Good Baseline

Before reproducing the incident, source and reporting totals were reconciled.

- Cleared source payments: **54**
- Source cash collected: **R1,921,072.59**
- Reporting cash collected: **R1,921,072.59**
- Variance: **R0.00**
- Status: **RECONCILED**

## Incident Reproduction

Payment:

`PAY300002`

Invoice:

`INV-2026-200002`

Payment amount:

**R78,726.93**

was deliberately duplicated in an in-memory reporting dataset.

The trusted Silver payment table was not modified.

### Incident Result

- Source cash collected: **R1,921,072.59**
- Reporting cash collected: **R1,999,799.52**
- Variance: **R78,726.93**
- Status: **VARIANCE DETECTED**

The variance exactly matched the duplicated payment amount.

## Investigation

Duplicate payment IDs were analysed in the reporting dataset.

The investigation identified:

- Duplicate payment ID: `PAY300002`
- Reporting occurrences: **2**
- Combined payment value: **R157,453.86**

The payment was then traced through the trusted data layers.

### Trace Results

| Layer | PAY300002 Record Count |
|---|---:|
| Bronze | 1 |
| Silver | 1 |
| Incident reporting dataset | 2 |

This confirmed that the source data was not duplicated.

The Silver transformation layer was also clean.

## Root Cause

The duplicate was introduced during downstream reporting processing.

The same payment business record was processed twice, causing cash collections to be overstated by:

**R78,726.93**

The source extract and trusted Silver data were correct.

## Remediation

The reporting dataset was deduplicated using `payment_id` as the business key.

For duplicate IDs, only one valid occurrence was retained.

### Remediation Result

- Source cash collected: **R1,921,072.59**
- Corrected reporting cash collected: **R1,921,072.59**
- Variance after remediation: **R0.00**
- Status: **RECONCILED**

## Preventive Controls

The following controls reduce the risk of recurrence:

- enforce payment business-key uniqueness before finance aggregation
- validate duplicate `payment_id` values during data-quality processing
- use watermark-controlled and idempotent batch processing
- aggregate payments before joining to invoice-level reporting models
- compare source and reporting collection totals after processing
- prevent already-processed batches from being loaded twice
- retain audit evidence for reconciliation and troubleshooting

The project already demonstrates several of these controls through the DQ and incremental-processing framework.

## Investigation Pattern

The incident followed a structured troubleshooting process:

```text
Establish baseline
      ↓
Reproduce variance
      ↓
Quantify variance
      ↓
Identify duplicate business keys
      ↓
Trace record through Bronze and Silver
      ↓
Isolate processing layer
      ↓
Apply remediation
      ↓
Reconcile totals
      ↓
Document preventive controls

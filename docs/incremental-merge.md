# Delta MERGE and Incremental Processing

## Overview

This project implements watermark-controlled incremental processing using Microsoft Fabric, PySpark and Delta Lake MERGE.

The objective is to process only new source batches, update changed business records, insert new records and prevent duplicate processing when a batch is rerun.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_incremental_merge`

## Controlled MERGE Test

A dedicated test target was created:

`silver.customers_merge_test`

The target was initialised using Batch 1 customer data only.

Initial target state:

- Batch 1 customers: 100
- Watermark: `2026-08-01`

Batch 2 contained:

- 5 source rows
- 2 existing customers requiring updates
- 3 new customers requiring inserts

## Delta MERGE Logic

The MERGE uses `customer_id` as the business key.

### Update Rule

Existing customer records are updated only when the incoming record has a newer `last_modified_at` value.

### Insert Rule

Records with customer IDs not already present in the target are inserted.

## MERGE Result

Batch 2 produced:

- 2 updates
- 3 inserts
- 103 total current customers

Example updated customer:

`CUST0007`

The Batch 2 record replaced the older current-state values with:

- Customer segment: Corporate
- City: Pretoria
- Province: Gauteng

Example inserted customer:

`CUST0101`

This customer did not exist in Batch 1 and was inserted during the MERGE.

## Idempotency

The same Batch 2 MERGE was rerun after successful processing.

Result:

- Rows after rerun: 103
- Unique customer IDs: 103
- Duplicate customer IDs: 0

This confirmed that rerunning the same source batch did not create duplicate business records.

## Watermark Control

A dedicated control entry was used:

`silver_customers_merge_test`

Initial watermark:

`2026-08-01`

After successful Batch 2 MERGE:

`2026-08-02`

The watermark is updated only after the MERGE completes successfully.

If the MERGE fails, the watermark is not advanced, allowing the same batch to be retried safely.

## End-to-End Processing Flow

```text
Read current watermark
        ↓
Compare incoming batch date
        ↓
If not newer → SKIP
        ↓
If newer → read incremental source batch
        ↓
Delta MERGE
        ↓
MERGE succeeds
        ↓
Advance watermark

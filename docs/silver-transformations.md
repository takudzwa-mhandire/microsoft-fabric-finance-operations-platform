# Silver Transformations and Validation

## Overview

The Silver layer transforms raw Bronze data into clean, typed and trusted current-state datasets for downstream business modelling and analytics.

The Silver transformation process is implemented in Microsoft Fabric using PySpark.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_silver_transformations`

## Silver Tables

The following Silver Delta tables are maintained:

- `silver.customers`
- `silver.products`
- `silver.locations`
- `silver.sales_orders`
- `silver.sales_order_lines`
- `silver.invoices`
- `silver.payments`
- `silver.inventory`

## Current-State Processing

Bronze preserves records from all ingestion batches.

Where the same business key appears in multiple batches, Silver selects the latest available version.

Examples include:

- customer attribute changes
- product price changes
- inventory quantity changes
- future order-status changes

PySpark window functions are used to rank records by business key and processing date.

For tables containing `last_modified_at`, the latest source modification timestamp is prioritised.

For daily snapshot data such as locations, `_batch_date` is used to select the latest version.

## Example — Customer Change

Customer `CUST0007` appeared in two Bronze batches.

### Batch 1

- Customer segment: Education
- City: East London
- Province: Eastern Cape

### Batch 2

- Customer segment: Corporate
- City: Pretoria
- Province: Gauteng

Silver correctly selected the Batch 2 version as the current customer record.

Historical Bronze records remain unchanged.

## Example — Product Price Change

Product `PRD0003` was updated between batches.

- Batch 1 selling price: R2,199
- Batch 2 selling price: R2,299

The Silver current-state product record contains the latest value of R2,299.

## Example — Inventory Change

Inventory record `STK000006` changed from:

- Batch 1 quantity: 64
- Batch 2 quantity: 62

Silver correctly retains quantity 62 as the current stock value while Bronze preserves both versions.

## Data Type Standardisation

Silver converts raw string values into appropriate data types.

Examples include:

- dates → `date`
- modification timestamps → `timestamp`
- quantities → `integer`
- monetary values → `decimal(18,2)`
- VAT rates → decimal

Text attributes are trimmed and standardised before use in downstream models.

## Silver Row Counts

| Table | Current Rows |
|---|---:|
| customers | 103 |
| products | 41 |
| locations | 5 |
| sales_orders | 158 |
| sales_order_lines | 399 |
| invoices | 111 |
| payments | 54 |
| inventory | 200 |

## Primary-Key Validation

All Silver tables were validated for:

- null business keys
- blank business keys
- duplicate business keys

All 8 Silver tables passed these checks.

## Referential Integrity Validation

The following relationships were validated:

- sales orders → customers
- sales orders → locations
- sales order lines → sales orders
- sales order lines → products
- invoices → sales orders
- invoices → customers
- payments → invoices
- inventory → products
- inventory → locations

All 9 referential-integrity checks passed with zero invalid records.

Validation results are persisted in:

`audit.silver_validation_results`

## Quarantine Candidate Assessment

Silver transformation logic includes checks that identify records requiring quarantine.

Current rules include:

- invalid sales quantities
- negative financial values
- invalid invoice amounts
- invalid payment amounts
- negative stock quantities or stock levels

The current clean dataset produced zero quarantine candidates.

The full reusable quarantine framework, including rejection reasons, failed-record storage and batch-level handling, is implemented separately under `DQ-001`.

## Engineering Concepts Demonstrated

- PySpark transformations
- Delta Lake
- current-state modelling
- window functions
- latest-record selection
- data-type standardisation
- primary-key validation
- referential integrity
- audit logging
- quarantine candidate identification
- Silver-layer operational validation

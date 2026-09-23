# Gold Finance and Operations Business Models

## Overview

The Gold layer provides curated business-ready data models for Finance, Sales, Inventory and Operations reporting.

The models are built from trusted Silver Delta tables using PySpark in Microsoft Fabric.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_gold_business_models`

## Gold Tables

| Gold Table | Rows | Purpose |
|---|---:|---|
| `gold.invoice_payment_reconciliation` | 111 | Invoice, payment and outstanding-balance reconciliation |
| `gold.debtor_ageing` | 86 | Outstanding invoice ageing |
| `gold.customer_finance_summary` | 72 | Customer-level finance and collections performance |
| `gold.sales_transaction_detail` | 274 | Completed sales transaction detail |
| `gold.daily_sales_summary` | 83 | Daily and location sales performance |
| `gold.product_sales_summary` | 40 | Product sales and profitability |
| `gold.inventory_status` | 200 | Inventory status and replenishment exceptions |

## Invoice and Payment Reconciliation

The reconciliation model combines invoices with cleared customer payments.

Payments are aggregated by invoice before joining to prevent multiple payment records from duplicating invoice values.

Each invoice is classified as:

- Unpaid
- Partially Paid
- Fully Paid
- Overpaid

### Finance Results

- Total invoiced: **R5,102,497.24**
- Total collected: **R1,921,072.59**
- Total outstanding: **R3,181,424.65**

Invoice status results:

- Fully Paid: 25
- Partially Paid: 27
- Unpaid: 59

All 111 invoices passed the reconciliation rule:

`Invoice Amount = Payments Received + Outstanding Balance`

## Debtor Ageing

Outstanding invoices are aged relative to the fixed reporting date:

`2026-08-31`

Ageing categories:

- Current
- 1–30 Days
- 31–60 Days
- 61–90 Days
- 90+ Days

### Ageing Results

| Ageing Bucket | Invoice Count | Outstanding Value |
|---|---:|---:|
| Current | 23 | R736,140.85 |
| 1–30 Days | 56 | R2,108,531.41 |
| 31–60 Days | 7 | R336,752.39 |

Total outstanding:

**R3,181,424.65**

The ageing model therefore reconciles exactly to the finance reconciliation model.

## Customer Finance Summary

Finance data is aggregated to one row per customer with invoice activity.

Metrics include:

- invoice count
- total invoiced
- total collected
- total outstanding
- fully paid invoice count
- partially paid invoice count
- unpaid invoice count
- overdue balance
- overdue invoice count
- maximum days overdue
- collection rate

The model contains **72 customers with invoice activity**.

Total overdue balance:

**R2,445,283.80**

## Completed Sales Transaction Detail

Only orders with status:

`Completed`

are included in recognised sales.

Pending and cancelled orders remain operational records but do not contribute to sales revenue.

### Sales Results

- Completed orders: **111**
- Completed transaction lines: **274**
- Units sold: **1,685**
- Gross sales before discount: **R4,576,045.00**
- Discounts: **R139,090.85**
- Net sales excluding VAT: **R4,436,954.15**

The model uses one row per completed sales-order line.

## Daily Sales Summary

Completed sales are aggregated by:

- order date
- business location

Metrics include:

- completed orders
- units sold
- net sales excluding VAT
- cost of sales
- gross profit
- gross margin percentage

### Profitability Results

- Net sales excluding VAT: **R4,436,954.15**
- Cost of sales: **R2,987,780.00**
- Gross profit: **R1,449,174.15**

The summary contains **83 date/location combinations**.

## Product Sales Summary

Completed sales are aggregated to one row per product.

Metrics include:

- completed orders
- units sold
- net sales
- cost of sales
- gross profit
- gross margin percentage

The model contains **40 products with completed sales activity**.

Product-level totals reconcile exactly to the completed-sales transaction model.

## Inventory Status

The inventory model combines current Silver inventory with product and location information.

Inventory records are classified as:

`Reorder Required`

when:

`quantity_on_hand <= reorder_level`

Otherwise:

`Stock OK`

Reorder quantity is calculated as:

`target_stock_level - quantity_on_hand`

with a minimum of zero.

### Inventory Results

- Inventory records: **200**
- Reorder Required: **37**
- Stock OK: **163**
- Units required to restore target stock: **2,335**
- Current inventory value: **R11,549,140.00**

## Gold Validation

The completed Gold layer was validated across Finance, Sales and Inventory.

### Finance

- Total invoiced: R5,102,497.24
- Total collected: R1,921,072.59
- Total outstanding: R3,181,424.65

### Sales

- Completed orders: 111
- Units sold: 1,685
- Net sales excluding VAT: R4,436,954.15

### Inventory

- Reorder Required: 37
- Stock OK: 163
- Total reorder units: 2,335
- Inventory value: R11,549,140.00

The aggregated Gold models reconcile back to their underlying detailed datasets.

## Engineering Concepts Demonstrated

- Microsoft Fabric
- PySpark
- Delta Lake
- Gold-layer modelling
- business-ready aggregations
- invoice/payment reconciliation
- debtor ageing
- finance analytics
- sales analytics
- profitability modelling
- inventory exception reporting
- dimensional joins
- aggregation validation
- operational KPI modelling

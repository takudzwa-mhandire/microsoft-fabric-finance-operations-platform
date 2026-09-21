# SCD Type 2 Customer History

## Overview

This project implements Slowly Changing Dimension Type 2 processing for customer records in Microsoft Fabric.

The objective is to preserve historical customer states when tracked customer attributes change instead of overwriting previous values.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_scd2_customer_history`

**Target Table:** `silver.customer_history_scd2`

## Source History

Customer history is derived from Bronze customer records across multiple ingestion batches.

The source contained:

- 105 Bronze customer records
- 103 unique customers
- 2 customers with historical changes

Customers with multiple versions:

- `CUST0007`
- `CUST0032`

## Tracked Attributes

Changes to the following customer attributes can create a new historical version:

- customer name
- customer segment
- city
- province
- country
- payment terms
- credit limit
- account status

A change hash is generated from the tracked attributes so repeated records with no actual business change do not create unnecessary historical versions.

## Example — CUST0007

### Version 1

- Segment: Education
- City: East London
- Province: Eastern Cape
- Effective from: 2026-08-01 07:30:00
- Effective to: 2026-08-02 09:10:00
- Current: False

### Version 2

- Segment: Corporate
- City: Pretoria
- Province: Gauteng
- Effective from: 2026-08-02 09:10:00
- Effective to: NULL
- Current: True

## Example — CUST0032

The customer remained in the same segment and location but payment terms changed:

- Previous payment terms: 15 days
- Current payment terms: 45 days

The original record was expired and a new current version was created.

## SCD Type 2 Columns

The historical customer table includes:

- `customer_version_key`
- `version_number`
- `effective_from`
- `effective_to`
- `is_current`

`customer_id` identifies the customer across all versions.

`customer_version_key` uniquely identifies a specific historical version.

## Validation Results

The persisted SCD Type 2 table contains:

| Validation | Result |
|---|---:|
| Total historical rows | 105 |
| Current versions | 103 |
| Expired versions | 2 |
| Unique customer version keys | 105 |
| Customers with multiple current versions | 0 |

This confirms that each customer has exactly one current version.

## Point-in-Time Reporting

The SCD Type 2 table supports historical reporting using the effective-date range.

For `CUST0007`:

### As of 2026-08-01 12:00

The historical state returned:

- Education
- East London
- Eastern Cape

### As of 2026-08-02 12:00

The current state returned:

- Corporate
- Pretoria
- Gauteng

This demonstrates the ability to answer:

> What did this customer look like at a specific point in time?

## Engineering Concepts Demonstrated

- Microsoft Fabric
- PySpark
- Delta Lake
- Slowly Changing Dimension Type 2
- historical dimension modelling
- window functions
- change detection using hashes
- effective dating
- current/expired record management
- surrogate version keys
- point-in-time reporting

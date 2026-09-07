# Bronze Incremental Ingestion

## Overview

The Bronze ingestion layer loads raw daily source extracts from the Vela Distribution Group landing zone into Delta tables in Microsoft Fabric.

The Bronze layer preserves source data without applying business transformations while adding operational metadata for lineage, auditability and incremental processing.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_bronze_ingestion`

## Source Structure

Daily source extracts are stored in the Lakehouse landing zone using date-based folders.

```text
Files/
└── landing/
    ├── 2026-08-01/
    └── 2026-08-02/

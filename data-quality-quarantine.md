# Data Quality and Quarantine Framework

## Overview

This project implements a reusable data-quality and quarantine framework in Microsoft Fabric.

The framework allows valid records to continue processing while invalid or suspicious records are isolated for investigation rather than causing the entire pipeline to fail.

## Fabric Components

**Workspace:** `Vela-Finance-Operations-Dev`

**Lakehouse:** `lh_vela_finops`

**Notebook:** `nb_data_quality`

## Data Quality Architecture

```text
Source / Silver Data
        ↓
Data Quality Rules
        ↓
 ┌───────────────┬─────────────────┐
 │ Valid Records │ Invalid Records │
 │ Continue      │ Quarantine      │
 └───────────────┴─────────────────┘
                         ↓
                  Rejection Reason
                         ↓
                  Audit Logging

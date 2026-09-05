# Microsoft Fabric Finance & Operations Data Platform

## Project Overview

This project is a production-style Microsoft Fabric data engineering simulation for **Vela Distribution Group (Pty) Ltd**, a fictional South African B2B distribution company.

The solution is designed to support Finance, Sales, Inventory and Operations reporting, with an initial focus on accounts receivable, cash collection and financial reconciliation.

The project simulates a real data-engineering environment using incremental data loads, engineering tickets, data-quality exceptions, operational incidents, change requests, monitoring and release documentation.

> **Note:** Vela Distribution Group is fictional. The project uses synthetic and transformed public sample data for learning and portfolio purposes.

## Business Priorities

### Phase 1 — Finance & Cash Collection
- Customer invoices
- Payments received
- Outstanding balances
- Debtor ageing
- Overdue accounts
- Invoice/payment reconciliation
- Collection performance

### Phase 2 — Sales & Inventory
- Sales performance
- Product performance
- Customer analysis
- Inventory levels
- Reorder exceptions

### Phase 3 — Operations
- Order status
- Delivery performance
- Turnaround times
- Operational exceptions

## Solution Architecture

```text
Source Data
    ↓
Landing Zone
    ↓
Bronze
    ↓
Silver
    ↓
Gold
    ↓
SQL Analytics / Semantic Model
    ↓
Power BI

Supporting layers:
Control | Audit | Data Quality | Monitoring

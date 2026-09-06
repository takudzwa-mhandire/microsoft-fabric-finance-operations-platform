Vela Distribution Group — Source Data Design

Purpose

This source package supports the Microsoft Fabric Finance & Operations Data Platform project for the fictional Vela Distribution Group (Pty) Ltd.

The source model is inspired by Microsoft’s public WideWorldImporters sample database and data-warehouse structures, particularly the Customer, Order, Sale/Invoice, Transaction and Stock Holding domains. Values, names, dates, currencies and business attributes in this package are synthetic and transformed for a fictional South African B2B distribution scenario.

Public references:

https://learn.microsoft.com/en-us/sql/samples/wide-world-importers-dw-database-catalog

https://github.com/microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers

Batch

Initial batch date: 2026-08-01

This package represents the initial Vela landing-zone extract. Later project tickets will introduce daily incremental batches, changed records, late-arriving transactions and controlled data-quality incidents.

Source Datasets

customers.csv

Customer master data used for finance, sales analysis and later SCD Type 2 processing.

Key fields:

customer_id

customer_name

customer_segment

city / province / country

payment_terms_days

credit_limit

account_status

registration_date

last_modified_at

products.csv

Product master data used for sales, margin and inventory reporting.

Key fields:

product_id

sku

product_name

category

unit_cost

selling_price

vat_rate

product_status

last_modified_at

locations.csv

Vela operating locations and distribution points.

sales_orders.csv

Sales-order headers containing customer, location, order dates, delivery dates, order status and source-system modification timestamp.

sales_order_lines.csv

Order-line detail containing products, quantities, unit prices, discounts and line values excluding VAT.

invoices.csv

Customer invoices with invoice date, due date, VAT, invoice value and current payment status.

payments.csv

Customer cash receipts with payment method, amount, invoice reference and bank reference.

inventory.csv

Product/location stock holdings with on-hand quantity, reorder level, target stock level and cost.

Relationships

customers.customer_id → sales_orders.customer_id

customers.customer_id → invoices.customer_id

customers.customer_id → payments.customer_id

locations.location_id → sales_orders.location_id

locations.location_id → inventory.location_id

sales_orders.order_id → sales_order_lines.order_id

sales_orders.order_id → invoices.order_id

products.product_id → sales_order_lines.product_id

products.product_id → inventory.product_id

invoices.invoice_id → payments.invoice_id

Data Engineering Notes

last_modified_at will be used as an incremental watermark candidate.

Source records are intentionally stored as CSV extracts before Bronze ingestion.

Business transformations must not be applied in the landing or Bronze layers.

VAT is modelled at 15% for standard-rated items.

Future batches will be supplied separately so troubleshooting scenarios are not visible in advance.

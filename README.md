# SQL Process Automation & Integration (Virtual Retail Database)

## 📌 Project Overview
This repository contains the production-grade SQL scripts, triggers, stored procedures, scheduled jobs, architectural flowcharts, and technical documentation for **Week 5: Automation and Integration of SQL Processes**.

The objective of this final project is to build an automated, self-healing database environment for an enterprise virtual retail platform, streamlining real-time inventory management, stock alerting, historical data archiving, and daily executive analytics rollups.

---

## ⚙️ Core Automated Workflows

### 1. Real-Time Transaction Processing (`sp_ProcessRetailOrder`)
- **Type:** Stored Procedure with Explicit Transaction Control (`TRY...CATCH`, `COMMIT`, `ROLLBACK`).
- **Function:** Validates stock, inserts order header & items, updates customer spend totals, and deducts inventory atomically.

### 2. Low-Stock Alert Generation (`trg_AfterInventoryUpdate_StockAlert`)
- **Type:** `AFTER UPDATE` Database Trigger.
- **Function:** Monitors inventory updates; when stock drops below threshold, automatically inserts an alert into the `stock_alerts` supplier queue without duplicate spamming.

### 3. Historical Data Archiving & Purging (`sp_ArchiveHistoricalOrders`)
- **Type:** Scheduled Batch Procedure & SQL Agent / Cron Job.
- **Function:** Moves completed orders older than 2 years into `orders_archive` and `order_items_archive` in micro-batches to optimize OLTP index performance.

### 4. Daily Executive Sales Analytics Rollup (`sp_GenerateDailySalesRollup`)
- **Type:** Scheduled Nightly Batch Procedure.
- **Function:** Aggregates daily total revenue, order counts, customer metrics, and discounts into `daily_sales_summary`.

---

## 🔄 System Architecture Flowchart
```text
+-------------------------------------------------------------------------+
|                        TRANSACTIONAL INPUT                              |
|                    (Customer / E-Commerce API)                          |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                    STORED PROCEDURE: sp_ProcessRetailOrder              |
|        [ Stock Check ] --> [ Deduct Inventory ] --> [ Create Order ]     |
+-------------------------------------------------------------------------+
                                    |
                                    v (Inventory Updated)
+-------------------------------------------------------------------------+
|              TRIGGER: trg_AfterInventoryUpdate_StockAlert               |
|            IF Quantity < Threshold --> QUEUE Supplier Alert             |
+-------------------------------------------------------------------------+

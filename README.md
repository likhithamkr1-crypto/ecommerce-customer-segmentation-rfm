# 🛍️ E-Commerce Customer Segmentation & RFM Analysis

> An end-to-end data analytics pipeline leveraging SQL, DuckDB, and Python to segment 100,00+ e-commerce customers using Recency, Frequency, and Monetary (RFM) analysis for targeted marketing campaigns.

[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/)
[![SQL / DuckDB](https://img.shields.io/badge/SQL-DuckDB-orange.svg)](https://duckdb.org/)
[![Pandas](https://img.shields.io/badge/Pandas-Data%20Wrangling-brightgreen.svg)](https://pandas.pydata.org/)

---

## 📊 Executive Summary & Business Objective
E-commerce marketing teams often treat all registered users equally, leading to high customer acquisition costs and low conversion rates on promotional campaigns. 
* **The Goal:** Analyze historical transactional telemetry to segment customers into actionable behavioral tiers (*Champions, Loyalists, At-Risk, Lost*).
* **The Impact:** Enables marketing teams to stop wasting budget on blanket ad campaigns and instead deploy personalized retention strategies for high-value cohorts.

---

## 🏗️ Technical Architecture & Workflow
1. **Data Extraction & SQL Aggregation:** Loaded raw order logs and executed complex SQL queries via `DuckDB` within Python to compute raw Recency, Frequency, and Monetary metrics.
2. **Quantile Scoring & Segmentation:** Applied Pandas `qcut` functions to assign 1–4 quintile scores and generate combined RFM segment codes.
3. **Business Rule Mapping:** Categorized users into distinct human-readable marketing segments based on behavior scores.

---

## 💻 Code Snippet: RFM SQL Aggregation
```sql
WITH ValidOrders AS (
    SELECT customer_id, order_id, order_date, order_amount
    FROM df_orders
    WHERE order_status = 'Completed'
),
SnapshotReference AS (
    SELECT MAX(order_date) AS max_date FROM ValidOrders
),
CustomerAggregates AS (
    SELECT 
        v.customer_id,
        DATE_DIFF('day', MAX(v.order_date), (SELECT max_date FROM SnapshotReference)) AS recency_days,
        COUNT(DISTINCT v.order_id) AS frequency,
        ROUND(SUM(v.order_amount), 2) AS monetary
    FROM ValidOrders v
    GROUP BY v.customer_id
)
SELECT * FROM CustomerAggregates
ORDER BY monetary DESC;
## 📂 Repository Structure
```text
ecommerce-customer-segmentation-rfm/
├── notebooks/
│   └── ecommerce_rfm_analysis.ipynb   # Main analysis notebook
└── README.md                          # Project documentation

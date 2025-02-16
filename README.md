# Project Scope:
As a Freelance BI Developer, this project aims to design and develop a Business Intelligence (BI) solution that integrates data from various sources into Snowflake, performs ETL operations, and provides insights via Power BI dashboards to support data-driven decision-making.


# 📌 Step 1: Business Requirements

Identify data sources (CSV files)

Understand key business questions (e.g., revenue trends, customer segmentation).

Define KPIs and metrics for reports and dashboards.

Document requirements in the docs/ folder.


# 📌 Step 2: Set Up Snowflake Warehouse

Create a Snowflake Account (if not already available).

Set Up Database & Schemas:

```sql
CREATE DATABASE BI_ANALYTICS;
CREATE SCHEMA RAW;
CREATE SCHEMA STAGING;
CREATE SCHEMA ANALYTICS;
```

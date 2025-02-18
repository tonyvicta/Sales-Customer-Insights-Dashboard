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

# 📌 Step 3: Set Up Load CSV Data into Snowflake 

Step 3.1: Prepare the CSV Files
Ensure the CSV files (customers_data.csv and transactions_data.csv) are available on your local machine.
Check that they are formatted correctly (comma-separated, proper headers, and clean data).

Step 3.2: Manually Upload CSV Files to Snowflake
To load the CSV files into Snowflake manually, follow these steps:

1️⃣ Log into Snowflake
Open your web browser and log in to your Snowflake account.
2️⃣ Create a Table to Store Data
Before uploading, create tables in Snowflake to store the CSV data.


For Custoemrs Data 

```sql
CREATE OR REPLACE TABLE STAGING.CUSTOMERS (
    Customer_ID INT,
    Name STRING,
    Country STRING,
    Spend INT,
    Segment STRING
);
```

For Transactons Data: 

```sql
CREATE OR REPLACE TABLE STAGING.TRANSACTIONS (
    Transaction_ID INT,
    Customer_ID INT,
    Date DATE,
    Product_Category STRING,
    Amount INT
);
```

3️⃣ Upload the CSV Files Manually

Step 3.3: Verify Data Load
Once the data is uploaded, validate it with SQL queries.

1. Check Record Count:

```sql

SELECT COUNT(*) FROM STAGING.CUSTOMERS;
SELECT COUNT(*) FROM STAGING.TRANSACTIONS;

```

2. Preview Data:

```sql
SELECT * FROM STAGING.CUSTOMERS LIMIT 10;
SELECT * FROM STAGING.TRANSACTIONS LIMIT 10;

```

Step 3.4: Automate Data Refresh

Since the files are uploaded manually, Set up a Snowflake Task to refresh the data at regular intervals.

Schedule a refresh task every hour:

```sql
CREATE OR REPLACE TASK REFRESH_CUSTOMERS
WAREHOUSE = COMPUTE_WH
SCHEDULE = '1 HOUR'
AS
DELETE FROM STAGING.CUSTOMERS;
```



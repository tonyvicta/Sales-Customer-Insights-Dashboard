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

# 📌 Step 4: Transform & Optimize Data in Snowflake
Before visualizing data, clean, standardize, and optimize it for analytics.

1️⃣ Create Analytical Tables (Fact & Dimension Models)
Create fact and dimension tables to improve query performance and support Power BI reporting.

Create a Dimension Table for Customers

```sql
CREATE OR REPLACE TABLE ANALYTICS.DIM_CUSTOMERS AS
SELECT 
    Customer_ID, 
    Name, 
    Country, 
    Segment
FROM STAGING.CUSTOMERS;
```

Create a Fact Table for Transactions

```sql

CREATE OR REPLACE TABLE ANALYTICS.FACT_TRANSACTIONS AS
SELECT 
    T.Transaction_ID,
    T.Customer_ID,
    C.Country,
    C.Segment,
    T.Date,
    T.Product_Category,
    T.Amount
FROM STAGING.TRANSACTIONS T
LEFT JOIN STAGING.CUSTOMERS C
ON T.Customer_ID = C.Customer_ID;

```

2️⃣ Optimize Query Performance in Snowflake
Since Power BI will be querying the data frequently, we optimize performance by:

Creating Clustering Keys

```sql
ALTER TABLE ANALYTICS.FACT_TRANSACTIONS
CLUSTER BY (Date, Product_Category);
```

Using Materialized Views for Aggregated Reports

```sql
CREATE MATERIALIZED VIEW ANALYTICS.MONTHLY_SALES AS
SELECT 
    DATE_TRUNC('month', Date) AS Month,
    Product_Category,
    SUM(Amount) AS Total_Sales
FROM ANALYTICS.FACT_TRANSACTIONS
GROUP BY 1, 2;
```


# 📌 Step 5: Perform Business Analysis Using SQL Queries
Now that the data is structured properly, let's run business queries to generate insights.

1️⃣ Total Sales by Month

SELECT DATE_TRUNC('month', Date) AS Month, 
       SUM(Amount) AS Total_Sales
FROM ANALYTICS.FACT_TRANSACTIONS
GROUP BY 1
ORDER BY 1;

2️⃣ Top 5 Countries by Revenue

```sql
SELECT Country, 
       SUM(Amount) AS Revenue
FROM ANALYTICS.FACT_TRANSACTIONS
GROUP BY Country
ORDER BY Revenue DESC
LIMIT 5;

```
3️⃣ Customer Segmentation Analysis

```sql

SELECT Segment, 
       COUNT(DISTINCT Customer_ID) AS Customer_Count, 
       SUM(Amount) AS Total_Spend
FROM ANALYTICS.FACT_TRANSACTIONS
GROUP BY Segment
ORDER BY Total_Spend DESC;

```

These queries can be used to validate data before connecting to Power BI.


# 📌 Step 6: Connect Power BI to Snowflake and Build Dashboards

## 📊 This report provides real-time sales analysis, customer segmentation, and interactive visualizations.


## Key Visuals in the Dashboard

| **Visualization** | **Type** | **Purpose** |
|------------------|---------|-------------|
| **Total Sales**  | KPI Card | Displays total revenue generated |
| **Total Customers** | KPI Card | Shows distinct customers in the dataset |
| **Average Transaction Value** | KPI Card | Calculates the average amount per transaction |
| **Monthly Revenue Trend** | Line Chart | Tracks revenue over time |
| **Top Product Categories** | Bar Chart | Shows best-performing product categories |
| **Sales by Country** | Map | Displays revenue distribution by country |
| **Customer Spend Analysis** | Table | Lists top customers and their spending |

---


## Data Source: Snowflake
The dashboard pulls data from **Snowflake Data Warehouse**, where it has been processed and optimized for Power BI.


---

## 📌 Step-by-Step Guide to Creating the Dashboard

### **1️⃣ Load Data into Power BI**
1. Open **Power BI Desktop**.
2. Click **"Get Data" → "Snowflake"**.
3. Enter Snowflake **Server & Warehouse details**.
4. Load **FACT_TRANSACTIONS** and **DIM_CUSTOMERS** tables.

---

### **2️⃣ Create Key KPI Cards**
1. **Insert Card Visuals**:
   - Drag **Amount** into a Card → Rename as **Total Sales**.
   - Drag **Customer_ID** → Set Aggregation to **Distinct Count** → Rename as **Total Customers**.
   - Drag **Amount** → Set Aggregation to **Average** → Rename as **Avg Transaction Value**.
2. **Format KPI Cards**:
   - Increase **font size**.
   - Enable **shadow & background color**.

---

### **3️⃣ Create Revenue Trend Line Chart**
1. Insert **Line Chart**.
2. Set **X-axis**: `Date (FACT_TRANSACTIONS)`.
3. Set **Y-axis**: `SUM(Amount)`.
4. Format:
   - Set Date **Aggregation: Monthly**.
   - Enable **Data Labels**.

---

### **4️⃣ Create Product Sales Bar Chart**
1. Insert **Bar Chart**.
2. Set **X-axis**: `Product_Category`.
3. Set **Y-axis**: `SUM(Amount)`.
4. Sort by **Total Sales** in **Descending Order**.

---

### **5️⃣ Add Map for Sales by Country**
1. Insert **Map Visual**.
2. Set **Location**: `Country (DIM_CUSTOMERS)`.
3. Set **Size**: `SUM(Amount)`.
4. Ensure **Data Category** is set to **"Country/Region"**.

---

### **6️⃣ Create Customer Spend Analysis Table**
1. Insert **Table Visual**.
2. Add:
   - `Customer_ID`
   - `Name`
   - `Segment`
   - `SUM(Amount)`

---

### **7️⃣ Add Interactive Slicers**
| **Slicer** | **Field** | **Function** |
|------------|---------|-------------|
| Date Range | `Date` | Filters transactions by selected dates |
| Country | `Country` | Filters sales by location |
| Customer Segment | `Segment` | Filters based on customer value |

**Sync Slicers Across Pages**:
1. Click on a slicer.
2. Go to **View → Sync Slicers**.
3. Enable **Sync across all pages**.

---

## 📌 Enhancements & Optimizations
### **✅ Apply a Custom Theme**
1. Go to **View → Themes → Customize Current Theme**.
2. Set:
   - **Background color**
   - **Font style**
   - **Visual borders & shadows**

---

### **✅ Publish & Share the Report**
1. Click **"File" → "Publish" → "Power BI Service"**.
2. Open the report in **Power BI Web**.
3. Click **"Share"** → Copy the link.
4. Set up **Scheduled Refresh** for live data updates.

---


## 📌 Next Steps
- 🚀 **Add Advanced DAX Measures** (e.g., YoY Growth, Customer Retention)
- 📈 **Optimize Queries for Performance**
- 📤 **Embed Report into SharePoint or Website**

---



![](./reports/Sales-Customer-Insights-Dashbaord.png)


## 🔗 View the Live Power BI Dashboard 


[Click here to view the report](https://app.powerbi.com/links/5QSb4wM7WK?ctid=4be1cff7-4be7-4e71-8005-72e894e81e2f&pbi_source=linkShare)


### **🎯 Author**
👨‍💻 **Tony Tawakali** – Power BI Developer  
📧 **Contact:** tony@datasphered.com

🚀 **Happy Dashboarding!**




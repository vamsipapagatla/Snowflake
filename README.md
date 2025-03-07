# **Snowflake Data Pipeline - PacificRetail**

## **Project Overview**
This repository contains the **end-to-end Snowflake data pipeline** for **PacificRetail**, which ingests, transforms, and loads data into structured layers for analytics and reporting.

### **Architecture Overview**
The pipeline follows a **Bronze → Silver → Gold** structure:
- **Bronze Layer:** Raw data ingestion from **Azure Data Lake Storage (ADLS)**.
- **Silver Layer:** Cleansed, transformed, and deduplicated data.
- **Gold Layer:** Aggregated, optimized data for reporting and analytics.

### **Key Components**
- **Data Ingestion:** `COPY INTO` loads data from ADLS into Snowflake.
- **Change Capture:** Streams track `INSERTS`, `UPDATES`, and `DELETES`.
- **Transformation:** `MERGE` statements process data from **Bronze → Silver**.
- **Task Automation:** Snowflake **tasks** schedule merges to maintain freshness.
- **Error Handling:** Logs capture failures for debugging.

---

## **Project Structure**
```
PacificRetail-Snowflake
 ┣ scripts
 ┃ ┣ Create_DB_Bronze_Schema.sql
 ┃ ┣ External_Stage_Creation.sql
 ┃ ┣ Orders_Load.sql
 ┃ ┣ Product_Load.sql
 ┃ ┣ Customer_Load.sql
 ┃ ┣ Stream_Creation.sql
 ┃ ┣ Orders_Transform.sql
 ┃ ┣ Product_Transform.sql
 ┃ ┣ Customer_Transform.sql
 ┃ ┣ Silver_Data_Load.sql
 ┃ ┃ Gold_Layer.sql
 ┃ ┣ GoldLayer_Views.sql
 ┃ ┗ Debug_Scripts.sql
 ┣ data
 ┃ ┣ transaction.snappy.parquet
 ┃ ┣ products.json
 ┃ ┗ customer.csv
 ┣ README.md  <-- This file
```

---

## **Workflow**
1. **Raw data is staged in ADLS** → `COPY INTO` Snowflake Bronze tables.
2. **Streams track changes** → New inserts and updates go to `_CHANGES_STREAM`.
3. **Merge tasks process data** → Silver tables receive deduplicated, cleaned data.
4. **Gold layer aggregates data** → Optimized for reporting and analytics.
5. **Scheduled tasks keep data fresh** → Runs automatically using **Snowflake Tasks**.

---

## **Setup & Deployment**
### **1. Create the Snowflake Schema**
```sql
RUN scripts/Create_DB_Bronze_Schema.sql;
```
### **2. Configure External Stages for ADLS**
```sql
RUN scripts/External_Stage_Creation.sql;
```
### **3. Load Data into Bronze**
```sql
RUN scripts/Orders_Load.sql;
RUN scripts/Product_Load.sql;
RUN scripts/Customer_Load.sql;
```
### **4. Set Up Streams for Change Tracking**
```sql
RUN scripts/Stream_Creation.sql;
```
### **5. Process Silver & Gold Layers**
```sql
RUN scripts/Silver_Data_Load.sql;
RUN scripts/Gold_Layer.sql;
```

---

## **Troubleshooting & Lessons Learned**
### **Issue: Data missing in Silver tables after processing**
- **Fix:** Stream name mismatch (`ORDER_CHANGES_STREAM` vs `ORDERS_CHANGES_STREAM`).

### **Issue: Streams not tracking existing records**
- **Fix:** Reinserted data (`INSERT INTO raw_table SELECT * FROM raw_table`).

### **Issue: Merge task `SUSPENDED_DUE_TO_ERRORS`**
- **Fix:** Checked `TASK_HISTORY()` logs and fixed incorrect object references.

### **Issue: Duplicate records in Bronze**
- **Fix:** Ensured `COPY INTO` was not reloading the same Parquet file multiple times.

---

## **Future Enhancements**
- **Data Quality Checks:** Add validation rules before merging.
- **Alerting System:** Set up error monitoring for failed tasks.
- **Performance Tuning:** Optimize warehouse scaling and query execution.

---

## **Author**
- **Project Owner:** Vamsidhar
- **Tech Stack:** Snowflake | Azure Data Lake | SQL | Python

**Status:** Deployed & Operational

---

This repository serves as the **documentation & deployment reference** for the PacificRetail Snowflake pipeline.

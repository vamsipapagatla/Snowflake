# **Data Folder - PacificRetail Snowflake Pipeline**

## **Overview**
This folder contains the source data files used for ingestion into the **PacificRetail Snowflake Data Pipeline**. These files are staged in **Azure Data Lake Storage (ADLS)** before being loaded into the **Bronze Layer** of Snowflake.

## **Contents**
| File Name                     | Format  | Description |
|--------------------------------|---------|-------------|
| `transaction.snappy.parquet`  | Parquet | Contains raw transactional order data. |
| `products.json`               | JSON    | Product catalog with categories, brands, and pricing details. |
| `customer.csv`                | CSV     | Customer master file with demographic information. |

## **Data Ingestion Workflow**
1. **Files are uploaded to ADLS** → Staged in `@adls_stage/`
2. **Snowflake `COPY INTO` loads data into Bronze Layer:**
   ```sql
   COPY INTO pacificretail_db.bronze.raw_order FROM @adls_stage/Orders/
   FILE_FORMAT = (FORMAT_NAME = 'parquet_file_format') ON_ERROR = 'CONTINUE';
   ```
3. **Streams track new inserts and changes for processing into Silver Layer.**
4. **Scheduled Snowflake tasks process data into structured tables.**

## **File Formats & Schema**
### **1️⃣ transaction.snappy.parquet (Orders Data)**
- `transaction_id` (STRING) - Unique ID for the transaction
- `customer_id` (INT) - Associated customer ID
- `product_id` (INT) - Purchased product ID
- `quantity` (INT) - Quantity of items bought
- `store_type` (STRING) - Online or Retail purchase
- `total_amount` (FLOAT) - Total purchase amount
- `transaction_date` (DATE) - Date of purchase
- `payment_method` (STRING) - Payment method used

### **2️⃣ products.json (Product Catalog)**
- `product_id` (INT) - Unique product ID
- `name` (STRING) - Product name
- `category` (STRING) - Product category
- `brand` (STRING) - Manufacturer brand
- `price` (FLOAT) - Product price
- `stock_quantity` (INT) - Available stock count
- `rating` (FLOAT) - Customer rating (0-5 scale)
- `is_active` (BOOLEAN) - Whether the product is currently active

### **3️⃣ customer.csv (Customer Data)**
- `customer_id` (INT) - Unique customer ID
- `name` (STRING) - Full name
- `email` (STRING) - Contact email
- `country` (STRING) - Customer country
- `customer_type` (STRING) - Regular or VIP customer
- `registration_date` (DATE) - Date of registration
- `age` (INT) - Customer age
- `gender` (STRING) - Male, Female, or Other
- `total_purchases` (INT) - Number of purchases made

## **Usage Notes**
- Ensure **file formats match the Snowflake schema** before ingestion.
- If new data files are added, update the **COPY INTO commands** accordingly.
- Check **data quality** before moving from Bronze to Silver layers.

This `data` folder serves as the **staging area for all source files** used in the PacificRetail Snowflake pipeline.

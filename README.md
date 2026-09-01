# 🛠️ Product Data ETL Pipeline

## 📌 Overview
This repository contains a **Python ETL pipeline** that automates the process of handling product data.  
The pipeline:
- Captures new product files in CSV format.  
- Loads them into **SQL Server**.  
- Updates the `products` table with new or changed product details.  
- Maintains historical records in an **audit table** (`products_audit_scd2`) using **Slowly Changing Dimension (SCD Type 2)** logic.  

This ensures the database stays up to date while preserving a full history of product changes.

---

## 📂 Dataset
- Input: Daily CSV file (`Products.txt`) received at a fixed time.  
- Format: Pipe‑separated (`|`).  
- Content: Full product file (includes new products, updated products, and unchanged products).  

---

## 🛠️ Tools & Technologies
- **Python** (Pandas, SQLAlchemy, Dateutil) → ETL scripting and automation.  
- **SQL Server** → Data storage and audit tracking.

---

## 🗄️ Database Requirements
You must have the following tables in your SQL Server database:

### 1. `products` table
Stores the latest product details.  
Columns:
- `product_id` (unique identifier)  
- `product_name`  
- `price`

### 2. `products_audit_scd2` table
Tracks historical changes using SCD Type 2.  
Columns:
- `product_key` (surrogate key, auto‑increment by 1)  
- `product_id`  
- `product_name`  
- `price`  
- `effective_date`  
- `expire_date`  
- `active_flag` (1 = active, 0 = expired)

---

## 🔄 ETL Steps

### 1. **Extract**
- Read the daily CSV file (`Products.txt`).  
- Fetch existing product data from SQL Server.  

### 2. **Transform**
- Identify **new products** → insert into `products` and `products_audit_scd2`.  
- Identify **updated products** → expire old records in `products_audit_scd2` and insert new active rows.  
- Maintain **unchanged products** without duplication.  

### 3. **Load**
- Insert new products into `products`.  
- Insert new/updated records into `products_audit_scd2`.  
- Update expired records (`active_flag = 0`, set `expire_date`).  
- Sync changes back to `products` table.  

---

## 📈 Results
- **Products table** always reflects the latest product details.  
- **Audit table** preserves full history of changes with effective/expire dates.  
- Ensures **data integrity** and **traceability** for business analysis.  

---

## ▶️ How to Run
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/product-etl-pipeline.git
   cd product-etl-pipeline
   
2. Place your daily product file in the data/ folder:
   ```bash
   data/Products.txt
   
4. Update the database connection string in the script:
   ```bash
   engine = sql.create_engine('mssql+pyodbc://<SERVER>/<DB>?driver=ODBC+Driver+17+for+SQL+Server')
   
6. Run the ETL script:
   ```bash
   python etl_pipeline.py

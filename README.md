# 🚕 NYC Green Taxi End-to-End Data Engineering Pipeline

## 📌 Project Overview
This project demonstrates an **end-to-end data engineering pipeline** built using **Azure Data Factory, Azure Data Lake Storage Gen2, and Azure Databricks**.

The pipeline ingests **NYC Green Taxi trip data** from a public data source, processes it using **PySpark transformations**, and stores the data following the **Medallion Architecture (Bronze, Silver, Gold)**.

The final serving layer is implemented using **Delta Lake**, enabling features such as:

- ACID transactions
- Data versioning
- Time travel
- Schema enforcement

This project simulates a **real-world batch data pipeline used in Lakehouse architectures**.

---

# 🏗️ Architecture

The project follows a **Lakehouse architecture with Medallion layers**.

Source (NYC Taxi Dataset)  
↓  
Azure Data Factory (Data Ingestion)  
↓  
Azure Data Lake Storage Gen2  
↓  
Bronze Layer (Raw Data - Parquet)  
↓  
Azure Databricks (PySpark Transformations)  
↓  
Silver Layer (Clean Data - Parquet)  
↓  
Gold Layer (Serving Layer - Delta Tables)  
↓  
Analytics / BI Tools

---

# 🛠️ Tech Stack

| Technology | Purpose |
|------------|--------|
| Azure Data Factory | Data ingestion pipeline |
| Azure Data Lake Storage Gen2 | Data storage |
| Azure Databricks | Data processing |
| PySpark | Data transformation |
| Delta Lake | Gold serving layer |
| Parquet | Storage format |
| Azure Active Directory | Secure authentication |

---

# 📂 Dataset

Dataset: **NYC Green Taxi Trip Data**

Source:  
https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

The dataset includes:

- Pickup and drop-off locations
- Trip distance
- Fare amount
- Payment type
- Trip type

This project processes **one year of historical taxi trip data** to simulate a real-world batch data pipeline.

---

# 🧱 Medallion Architecture

## 🥉 Bronze Layer (Raw Data)

The Bronze layer stores **raw ingested data directly from the source**.

Characteristics:

- Raw ingestion from NYC dataset
- Stored in **Parquet format**
- Maintains original schema
- Used as the source of truth

Example structure:

bronze/
   trips2025data/
   trip_zone/
   trip_type/

---

## 🥈 Silver Layer (Clean Data)

The Silver layer contains **cleaned and transformed datasets**.

Transformations performed:

- Schema enforcement
- Column selection
- Feature engineering
- Data normalization
- Splitting zone fields
- Extracting trip date, year, and month

Example transformation:

df_trip = df_trip.withColumn("trip_date", to_date("lpep_pickup_datetime")) \
.withColumn("trip_year", year("lpep_pickup_datetime")) \
.withColumn("trip_month", month("lpep_pickup_datetime"))

---

## 🥇 Gold Layer (Serving Layer)

The Gold layer contains **analytics-ready tables stored using Delta Lake**.

Tables created:

gold.trip_zone  
gold.trip_type  
gold.trip_trip  

Benefits of Delta Lake:

- ACID transactions
- Version history
- Time travel
- Schema enforcement

---
# 🔄 Data Pipeline

## Data Ingestion (Azure Data Factory)

The ingestion pipeline is built using **Azure Data Factory (ADF)** and is designed to dynamically pull NYC Green Taxi trip data for an entire year.

The pipeline uses:

- **HTTP Linked Service** as the source (NYC Taxi dataset website)
- **Azure Data Lake Storage Gen2** as the destination
- **Parameterized datasets**
- **ForEach activity for dynamic iteration**

The pipeline dynamically processes **12 months of taxi data** using the following logic:

1. A **ForEach activity** iterates through months using the expression:

@range(1,12)

2. A pipeline parameter called **p_month** dynamically updates the dataset URL.

3. Based on the month value, the pipeline constructs the correct dataset file name.

Example dataset naming pattern:

green_tripdata_2025_01.parquet  
green_tripdata_2025_02.parquet  
...  
green_tripdata_2025_12.parquet

4. Each iteration triggers a **Copy Activity** that downloads the dataset and stores it in the **Bronze layer of Azure Data Lake Storage**.

Pipeline flow:

ForEach (Months 1–12)  
↓  
Copy Activity  
↓  
Load raw data into Bronze Layer

This approach eliminates manual ingestion and allows the pipeline to **automatically ingest an entire year of data in a scalable and reusable way**.


# ⚙️ Data Processing (Azure Databricks)

Azure Databricks performs data transformation tasks.

Processing steps:

1. Connect to ADLS Gen2 using **Service Principal authentication**
2. Read raw data from Bronze layer
3. Perform transformations using **PySpark**
4. Write cleaned datasets to Silver layer
5. Create Delta tables in the Gold layer

---

# 🧪 Delta Lake Features Demonstrated

### Version History

DESCRIBE HISTORY gold.trip_zone

Tracks table operations and version history.

---

### Update Operation

UPDATE gold.trip_zone  
SET Borough = 'EWR'  
WHERE LocationID = 1

---

### Delete Operation

DELETE FROM gold.trip_zone  
WHERE LocationID = 1

---

### Time Travel

RESTORE gold.trip_zone TO VERSION AS OF 0

This allows recovery of previous table versions.

---

# 🔐 Security

Secure data access is implemented using **Azure Active Directory Service Principal authentication**.

Configuration includes:

- Client ID
- Client Secret
- Directory ID

Spark OAuth configuration is used to authenticate Databricks with ADLS Gen2.

---

# 📁 Project Structure

nyc-taxi-data-engineering/

│  
├── notebooks/  
│   ├── bronze_ingestion  
│   ├── silver_transformation  
│   └── gold_layer  
│  
├── architecture/  
│   └── architecture_diagram.png  
│  
├── pipelines/  
│   └── adf_pipeline_screenshots  
│  
└── README.md  

---

# 📚 Key Learnings

This project demonstrates:

- End-to-end data pipeline design
- Medallion architecture implementation
- Azure Data Factory pipeline automation
- PySpark data transformations
- Secure cloud data access
- Delta Lake advanced features

---

# 🚀 Future Improvements

Potential improvements include:

- Incremental data ingestion
- Data quality validation checks
- Partitioning strategies
- Pipeline monitoring and alerting
- Integration with Power BI dashboards

---

# 👨‍💻 Author

Sriram Mahesh Babu  

Aspiring Data Engineer passionate about building scalable cloud data pipelines.

GitHub  
https://github.com/Mahesh200457

LinkedIn  
https://www.linkedin.com/in/sriram-mahesh-babu

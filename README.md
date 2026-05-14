# project-de-transportation-goodcabs
End-to-end Data Engineering pipeline on Databricks — Bronze, Silver, Gold layers

# 🚕 GoodCabs — Transportation Data Engineering Pipeline

> End-to-end data engineering project built on **Databricks** using a **Medallion Architecture** (Bronze → Silver → Gold) to process and analyze cab trip data across Indian cities.

---

## 📐 Architecture Overview

```
Trips (App) ──► Application Server ──► Relational DB
                                              │
                                    Data Fetch Service (Simulated via S3 upload)
                                              │
                                              ▼
                                    ┌─────────────────────────────────────┐
                                    │         Databricks Workspace         │
                                    │                                     │
                                    │   Bronze ──► Silver ──► Gold        │
                                    │                                     │
                                    │   Unity Catalog  |  Lakeflow DLT    │
                                    │   Lakehouse      |  Genie / Dashboards │
                                    └─────────────────────────────────────┘
```

Raw trip data is ingested from **S3** into the Databricks Lakehouse, processed through three quality layers, and surfaced as city-level analytics dashboards.

---

## 📁 Project Structure

```
project-de-transportation-goodcabs/
│
├── 1. data/
│   ├── city/
│   │   └── city.csv                    # City master data (city_id, city_name)
│   └── trips/
│       ├── Full Load/                  # Initial historical load
│       └── Incremental Load/           # Delta updates
│
├── 2. codes/
│   ├── project_setup.ipynb             # Catalog & schema setup (Bronze/Silver/Gold)
│   │
│   ├── bronze/
│   │   ├── city.py                     # Ingest city CSV → bronze.city (Materialized View)
│   │   └── trips.py                    # Stream trips CSV → bronze.trips (Auto Loader)
│   │
│   ├── silver/
│   │   ├── calendar.py                 # Date dimension with Indian holidays
│   │   ├── city.py                     # Cleaned city dimension → silver.city
│   │   └── trips.py                    # Validated & CDC trips → silver.trips
│   │
│   └── gold/
│       ├── trips_gold.sql              # Aggregated trips fact table
│       ├── trips_chandigarh.sql
│       ├── trips_coimbatore.sql
│       ├── trips_indore.sql
│       ├── trips_jaipur.sql
│       ├── trips_kochi.sql
│       ├── trips_lucknow.sql
│       ├── trips_mysore.sql
│       ├── trips_surat.sql
│       ├── trips_vadodara.sql
│       └── trips_visakhapatnam.sql
│
└── 3. other_files/
    └── architecture.png
```

---

## 🏗️ Medallion Architecture

### 🟤 Bronze Layer — Raw Ingestion
| Table | Method | Source |
|-------|--------|--------|
| `transportation.bronze.city` | Materialized View (batch CSV read) | `s3://goodcabs/data-store/city` |
| `transportation.bronze.trips` | Streaming Table (Auto Loader) | `s3://goodcabs/data-store/trips` |

- Preserves raw data with `_metadata.file_path` and `ingest_datetime`
- Schema evolution enabled via `cloudFiles.schemaEvolutionMode = rescue`
- Change Data Feed enabled on all tables

---

### ⚪ Silver Layer — Cleaned & Validated
| Table | Type | Key Transformations |
|-------|------|-------------------|
| `transportation.silver.city` | Materialized View | Column standardization, timestamp tracking |
| `transportation.silver.trips` | Streaming Table (CDC/SCD1) | Type casting, column renaming, data quality checks |
| `transportation.silver.calendar` | Materialized View | Full date dimension with Indian public holidays |

**Data Quality Expectations on `trips`:**
- `valid_date` — `year(business_date) >= 2020`
- `valid_driver_rating` — `driver_rating BETWEEN 1 AND 10`
- `valid_passenger_rating` — `passenger_rating BETWEEN 1 AND 10`

---

### 🥇 Gold Layer — Analytics-Ready
City-level aggregated trip metrics for 10 Indian cities:

| Cities Covered |
|---|
| Jaipur · Lucknow · Surat · Kochi · Chandigarh · Coimbatore · Indore · Mysore · Vadodara · Visakhapatnam |

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **Databricks** | Cloud data platform & pipeline orchestration |
| **Apache Spark / PySpark** | Distributed data processing |
| **Lakeflow Declarative Pipelines** | Pipeline definition (DLT) |
| **Unity Catalog** | Data governance & access control |
| **Delta Lake** | ACID-compliant table storage |
| **Auto Loader** | Incremental S3 file ingestion |
| **AWS S3** | Cloud object storage for raw data |
| **SQL** | Gold layer analytics queries |
| **Python** | Bronze & Silver transformation logic |

---

## 🚀 Getting Started

### Prerequisites
- Databricks workspace with Unity Catalog enabled
- AWS S3 bucket: `s3://goodcabs/`
- Databricks cluster with access to the S3 bucket

### Step 1 — Catalog & Schema Setup
Run `project_setup.ipynb` in Databricks:
```python
# Creates the transportation catalog with 3 schemas
spark.sql("CREATE CATALOG IF NOT EXISTS transportation")
spark.sql("CREATE SCHEMA IF NOT EXISTS transportation.bronze")
spark.sql("CREATE SCHEMA IF NOT EXISTS transportation.silver")
spark.sql("CREATE SCHEMA IF NOT EXISTS transportation.gold")
```

### Step 2 — Upload Data to S3
```
s3://goodcabs/data-store/city/     ← Upload city.csv
s3://goodcabs/data-store/trips/    ← Upload trips CSVs (Full Load first, then Incremental)
```

### Step 3 — Deploy Lakeflow Pipelines
Upload the pipeline files to Databricks and run in order:

```
1. bronze/city.py       → transportation.bronze.city
2. bronze/trips.py      → transportation.bronze.trips
3. silver/calendar.py   → transportation.silver.calendar
4. silver/city.py       → transportation.silver.city
5. silver/trips.py      → transportation.silver.trips
6. gold/*.sql           → transportation.gold.*
```

---

## 📊 Data Schema

### City (`city.csv`)
| Column | Type | Description |
|--------|------|-------------|
| `city_id` | String | Unique city code (e.g., `RJ01`) |
| `city_name` | String | City name (e.g., `Jaipur`) |

### Trips (Silver)
| Column | Type | Description |
|--------|------|-------------|
| `id` | String | Unique trip ID |
| `business_date` | Date | Trip date |
| `city_id` | String | City reference |
| `passenger_category` | String | `new` or `repeated` |
| `distance_kms` | Double | Distance travelled |
| `sales_amt` | Double | Fare amount |
| `passenger_rating` | Double | Rating 1–10 |
| `driver_rating` | Double | Rating 1–10 |

---

## 🔑 Key Design Decisions

- **Auto Loader** for trips streaming ensures exactly-once ingestion and handles schema evolution automatically
- **SCD Type 1** CDC upsert on `silver.trips` using `trip_id` as the key
- **Change Data Feed** enabled across all layers for downstream change tracking
- **Materialized Views** used for static dimensions (city, calendar) to avoid redundant recomputation
- City-level **Gold tables** allow targeted partitioned queries per city for dashboard performance

---

## 👤 Author

**Nikitha K** — Data Analyst | Analytics Engineer  
📧 nikithak207@gmail.com  
---

## 📄 License

This project is for portfolio and educational purposes.

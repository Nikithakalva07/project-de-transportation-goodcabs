🚖 Good Cabs — End-to-End Data Engineering Project
Built with Databricks LakeFlow Spark Declarative Pipelines (SDP)
________________________________________
📌 Project Overview
Good Cabs is a rapidly growing cab service company facing critical data challenges at scale. Regional managers were receiving delayed, generic insights, and the existing procedural Spark pipelines were rigid, tightly coupled, and slow to adapt to business needs.
This project demonstrates a proof-of-concept migration to LakeFlow Spark Declarative Pipelines on Databricks — resulting in faster pipelines, reduced code complexity, and BI-ready regional dashboards.
________________________________________
🧩 Problem Statement
Pain Point	Description
Delayed Insights	Regional managers not receiving data on time
Inefficient Dashboards	Generic dashboards forcing manual data rework
Outdated Architecture	Procedural Spark pipelines with manual orchestration
________________________________________
🏗️ Technical Architecture
Amazon S3 (CSV files)
        ↓
  [ Bronze Layer ]   ← Raw ingestion with metadata columns
        ↓
  [ Silver Layer ]   ← Cleaned, transformed, validated data
        ↓
  [ Gold Layer ]     ← BI-ready, city-specific views
Tech Stack:
•	Databricks (Free Edition)
•	LakeFlow Spark Declarative Pipelines (SDP)
•	Amazon S3
•	Unity Catalog (governance & RBAC)
•	Auto Loader (incremental ingestion)
________________________________________
📂 Project Structure
/bronze
  ├── city_bronze.py         # Raw city data ingestion
  └── trips_bronze.py        # Raw trips data ingestion

/silver
  ├── city_silver.py         # City data cleaning & transformation
  ├── calendar_silver.py     # Calendar dimension table
  └── trips_silver.py        # Trips data with data quality checks

/gold
  └── gold_views.py          # City-specific BI-ready views

/data
  └── schema/                # Sample schema definitions

README.md
________________________________________
🔄 Pipeline Layers
🥉 Bronze Layer
•	Ingests raw CSV data from S3 using Auto Loader
•	Adds metadata columns (_ingestion_time, _source_file)
•	Uses permissive mode to handle corrupt records without pipeline failure
•	Bad records captured in _corrupt_record column
🥈 Silver Layer
•	Applies transformation and cleaning logic
•	Uses Expectation Annotations for data quality (e.g., valid driver rating ranges)
•	Handles unexpected schema changes via rescue columns
•	Code reduction achieved: 135 lines → 50 lines vs. traditional approach
🥇 Gold Layer
•	Generates city-specific views for regional managers
•	Enables role-based access via Unity Catalog (RBAC)
•	Delivers BI-ready data without manual export or rework
________________________________________
⚙️ Key Features Implemented
•	Declarative vs Imperative: Shifted from manual orchestration to SDP's automatic dependency management
•	Incremental Load: Continuous pipeline mode processes only new data dropped in S3
•	Data Quality: expect annotations flag bad records without stopping pipelines
•	Dry Run Validation: Pre-execution validation catches logical and syntax errors early
•	Access Management: Unity Catalog restricts regional managers to their city-specific data only
________________________________________
📊 Outcomes & Results
Metric	Result
Code Reduction	135 lines → 50 lines (Silver layer)
Pipeline Stability	Incremental updates with no full dataset reprocessing
Data Delivery	Timely, region-specific insights for managers
Stakeholder Approval	Proof-of-concept accepted for roadmap inclusion
________________________________________
🚀 How to Run
1.	Sign up for Databricks Free Edition
2.	Create a Unity Catalog with the required schemas (bronze, silver, gold)
3.	Connect your S3 bucket to Databricks
4.	Import notebooks from this repo into Databricks
5.	Create a new Lakeflow Declarative Pipeline and add the notebooks
6.	Run the pipeline — dependencies are managed automatically!
________________________________________
📚 Resources
•	Codebasics Course Resources
•	Databricks Free Edition Signup
________________________________________
🙋 About
Built as a hands-on learning project by following the Databricks LakeFlow Spark Declarative Pipelines micro-course by Codebasics.
Domain: Transportation / Cab Services
All code and documentation written independently while working through the tutorial.


LinkedIn post:

🚖 Just built an end-to-end Data Engineering project using Databricks LakeFlow Spark Declarative Pipelines — and it's live on GitHub!

Here's the story behind it 👇

Good Cabs, a fast-growing cab company, had a serious problem:
❌ Regional managers weren't getting data on time
❌ Dashboards were too generic — teams were manually reworking exports
❌ Existing Spark pipelines were rigid and hard to maintain

The solution? Migrate to LakeFlow Spark Declarative Pipelines (SDP) on Databricks.

🏗️ What I built:
→ Bronze Layer — Raw ingestion from S3 using Auto Loader, with corrupt record handling
→ Silver Layer — Data cleaning, quality checks via Expectation annotations, schema rescue
→ Gold Layer — City-specific, BI-ready views with role-based access (Unity Catalog)

📉 One highlight: The Silver layer transformation went from 135 lines of imperative code → just 50 lines declaratively. That's the power of SDP.

⚡ Other wins:
✅ Incremental load — only new S3 data is processed, no full reruns
✅ Dry run validation catches errors before they hit production
✅ RBAC via Unity Catalog — each regional manager sees only their city's data
✅ Automatic dependency management — no manual orchestration needed

🛠️ Tech Stack: Databricks | LakeFlow SDP | Amazon S3 | Unity Catalog | Auto Loader | PySpark

Full project + README on GitHub 👉 [your GitHub link here]

#DataEngineering #Databricks #LakeFlow #SparkDeclarativePipelines #ApacheSpark #DataPipeline #OpenToWork #DataEngineer

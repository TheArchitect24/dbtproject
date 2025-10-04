# 🧠 DbtEngineer — NYC Parking Violations Medallion Architecture Project

---

## 📘 Overview

This **dbt (Data Build Tool)** project demonstrates a full end-to-end data transformation workflow using the **Medallion Architecture** (Bronze → Silver → Gold) applied to the **New York City Parking Violations dataset**.  

The project showcases how to:
- Set up and configure a **dbt project**.
- Connect dbt to a **DuckDB database**.
- Build layered, maintainable, and testable data models using **SQL transformations**.
- Implement **data quality testing**, **documentation**, and **version control** best practices.

This project is ideal for learners and professionals aiming to understand how dbt operationalizes ELT (Extract, Load, Transform) workflows with modern data stack tools.

---

## 🏗️ Project Structure

Below is the folder and file structure defined in the `dbt_project.yml` configuration:

```
DbtEngineer/
├── analyses/ # Ad-hoc SQL analysis files
├── macros/ # Custom dbt macros for code reusability
├── models/ # Main transformation models organized by medallion layer
│ ├── bronze/ # Raw staging layer (data ingestion)
│ ├── silver/ # Cleansed and conformed models
│ └── gold/ # Aggregated business-ready data models
├── seeds/ # Static CSV datasets for ingestion
├── snapshots/ # Snapshot models for tracking slowly changing data
├── tests/ # Custom and generic dbt tests
├── dbt_project.yml # Core project configuration file
└── profiles.yml # Database connection and environment configurations
```

---

## ⚙️ Configuration Details

### **dbt_project.yml**

Defines project-wide settings such as file paths, model materializations, and naming conventions.

```yaml
name: 'DbtEngineer'
version: '1.0.0'
config-version: 2
profile: 'DbtEngineer'

model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

clean-targets:
  - "target"
  - "dbt_packages"
```
Model Configuration

Model materializations define how dbt persists each transformation layer.
Each layer represents a stage in the Medallion Architecture:

```
Layer --Directory ------  Materialization	---- Description
Bronze	models/bronze/	  view	               Raw ingestion layer — holds untransformed source data.
Silver	models/silver/	  view / ephemeral	   Cleansed and validated data — standardizes schema and joins.
Gold	  models/gold/	    table	               Aggregated and analytical-ready data models.
```

Example excerpt from the configuration:
```
models:
  DbtEngineer:
    bronze:
      +materialized: view
    silver:
      silver_parking_violation_codes:
        +materialized: ephemeral
      silver_parking_violations:
        +materialized: ephemeral
      silver_violation_tickets:
        +materialized: view
      silver_violation_vehicles:
        +materialized: view      
    gold:
      +materialized: table 
tests:
  +store_failures: true
```

🗄️ Database Connection (profiles.yml)

The project uses DuckDB, a lightweight, file-based analytical database — perfect for local dbt development.
```
DbtEngineer:
  outputs:
    dev:
      type: duckdb
      path: '../data/nyc_parking_violations.db'   
    prod:
      type: duckdb
      path: '../data/prod_nyc_parking_violations.db'  
  target: dev
```

Explanation:

type: Specifies the database type (duckdb).
path: Points to the database file location.
target: Determines the default environment (dev or prod).
Switch environments using:
dbt run --target prod

🧱 Medallion Architecture Layers
🥉 Bronze Layer
Acts as the raw data ingestion layer.
Models are materialized as views.
Stores unaltered NYC parking ticket datasets directly from the source.

🥈 Silver Layer
Cleans and enriches data by standardizing formats, resolving inconsistencies, and joining related tables.
Some models use ephemeral materialization, meaning they’re computed on the fly for efficiency.
Example models:
silver_parking_violation_codes
silver_parking_violations
silver_violation_tickets
silver_violation_vehicles

🥇 Gold Layer
Produces aggregated, business-ready tables for reporting and analytics.
Materialized as tables for better query performance.
Typically includes metrics like number of tickets per borough, most common violations, or fine distributions.

🧪 Testing
dbt supports both generic and custom tests.
Failures are stored due to:

tests:
  +store_failures: true
  
To run all tests:
dbt test

🧭 Common Commands
Command	Description
dbt debug	Test your connection and setup.
dbt compile	Compile models without executing SQL.
dbt run	Execute transformations and build models.
dbt test	Run data quality tests.
dbt clean	Remove target/ and dbt_packages/ directories.
dbt docs generate	Generate documentation for all models.
dbt docs serve	Serve the documentation site locally.


🧰 Environment Setup
1️⃣ Install Dependencies
```
pip install dbt-duckdb
```
2️⃣ Initialize dbt
```
dbt init DbtEngineer
```
3️⃣ Run the Pipeline
```
dbt run
```
4️⃣ View Lineage Graph

After generating docs:
dbt docs serve
Then open the provided URL to view the model lineage and documentation.

🧑‍💻 Learning Outcomes
Through this project, you’ll learn how to:
Structure a dbt project using best practices.
Implement Medallion Architecture.
Use DuckDB as a lightweight analytical database.
Develop modular and maintainable transformation models.
Apply testing, version control, and documentation in dbt.

🪶 Author Notes
"The best way to learn dbt is by building."
This project was a hands-on exercise in setting up a dbt project, connecting it to a database, and implementing a multi-layer transformation pipeline for real-world data — specifically the NYC Parking Violations dataset.


# Automated ETL Pipeline for E-commerce Data

An automated ETL pipeline built using **Python, Apache Airflow, Pandas, and Snowflake** to extract Amazon product sales data from CSV files, perform data cleaning and transformation, and load the processed data into a Snowflake data warehouse.

## 1. Project Overview

This project implements an automated ETL workflow to process Amazon e-commerce product data and make it ready for analytical use.

The pipeline automates three major stages:

- **Extraction:** Identifies new CSV files from the input directory and tracks previously processed files.
- **Transformation:** Uses Pandas to clean, validate, and transform product data into a structured format.
- **Loading:** Uploads transformed CSV files to a Snowflake internal stage and loads the data into Snowflake tables using `COPY INTO`.

Apache Airflow orchestrates the complete workflow, providing scheduled execution, task dependencies, logging, and retry configuration.

## 2. Architecture

![ETL Pipeline Architecture](Automate-etl-pipeline.jpg)

## 3. Technology Stack

| Component | Technology | Purpose |
|---|---|---|
| Programming Language | Python | ETL implementation |
| Data Processing | Pandas | Data cleaning and transformation |
| Workflow Orchestration | Apache Airflow | Scheduling and task management |
| Data Warehouse | Snowflake | Analytical data storage |
| File Format | CSV | Source and intermediate data |
| Data Transfer | Snowflake PUT | Upload files to internal stage |
| Data Loading | COPY INTO | Load staged files into Snowflake |

## 4. ETL Workflow

### 4.1 Extraction

- Reads CSV files from the configured input directory.
- Identifies previously processed files using a tracking file.
- Filters for new files requiring processing.
- Passes extracted file paths between Airflow tasks using XCom.

### 4.2 Transformation

Uses Pandas to perform the following operations:

- Inspects missing values and duplicate records.
- Removes records containing missing values.
- Cleans currency symbols and percentage characters.
- Converts price, discount, rating, and rating-count columns into appropriate numeric data types.
- Saves the transformed datasets as CSV files for downstream loading.

### 4.3 Loading

Uses the Snowflake Python connector through Airflow's SnowflakeHook.

1. Establishes a connection to Snowflake using an Airflow connection.
2. Uploads transformed CSV files to an internal Snowflake stage using the `PUT` command.
3. Loads data from the stage into the target table using `COPY INTO`.
4. Commits the transaction after successful loading.
5. Updates the processed-file tracking information.

## 5. Airflow Orchestration

The ETL workflow is implemented as an Apache Airflow DAG named `amazon_data_etl`.

**Execution sequence:**

`Extract → Transform → Load`

Key orchestration configurations:

- **Schedule:** Daily (`@daily`)
- **Task execution:** PythonOperator
- **Inter-task communication:** XCom
- **Retry configuration:** 3 retries
- **Retry delay:** 5 minutes
- **Logging:** Python logging and Airflow task logs

## 6. Project Structure

```text
Automated_ETL_Pipeline_for_Ecommerce_data/
│
├── Data/
│   └── Source CSV files
│
├── ETL.py
│
├── Automate-etl-pipeline.jpg
│
└── README.md
```

## 7. Prerequisites

- Python
- Apache Airflow
- Pandas
- Snowflake account
- Snowflake Airflow provider

## 8. Configuration

Configure the following Airflow Variables:

| Variable | Description |
|---|---|
| `data_directory` | Directory containing source CSV files |
| `Processed_directory` | Directory containing processed-file tracking information |
| `transformed_directory` | Directory for transformed CSV files |
| `snowflake_conn_id` | Airflow Snowflake connection identifier |

Configure the Snowflake connection in Airflow with the required account credentials and connection details.

The Snowflake internal stage and target table must also be available before executing the loading task.

## 9. Dataset

The project uses the [Amazon Sales Dataset](https://www.kaggle.com/datasets/ahmedsayed564/amazon-sales-dataset) available on Kaggle.

The dataset contains e-commerce product information, including product names, prices, discounts, ratings, and rating counts.

## 10. Future Enhancements

- Implement data quality checks and validation rules before loading.
- Introduce incremental loading and stronger idempotency controls.
- Improve transformation scalability for larger datasets.
- Implement automated monitoring and failure notifications.
- Introduce Snowflake data models for downstream analytics.

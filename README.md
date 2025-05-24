# E-commerce Data Engineering Project with Microsoft Fabric (Medallion Architecture)


![](https://github.com/abigailmwanza/SQL_DataWareHouse/blob/main/What%20is%20a%20Data%20Warehouse%20%E2%80%93%20Definition%2C%20Example%20%26%20Benefits.jpg)
Project Overview

This project implements a data engineering pipeline using Microsoft Fabric, following the Medallion Architecture (Bronze, Silver, Gold layers). The pipeline ingests e-commerce data from Kaggle, process it using parameterized ingestion, lands raw data in a Bronze Lakehouse, and performs transformations in the Silver layer to prepare cleaned and structured data.Then load data in Gold layer

## Objectives
Ingest raw e-commerce data from a public GitHub URL.
Use parameterized pipelines with ForEach and Copy activities in Microsoft Fabric for Bronze layer ingestion.
Transform data in the Silver layer (data type conversions, null handling) using a notebook.
Land the transformed data in the Silver Lakehouse for further processing.

## Data Source
The data is sourced from a public GitHub repository URL containing e-commerce datasets. The datasets include:
store_dim: Information about stores.
customer_dim: Customer details.
trans_dim: Transaction metadata.
fact_table: Transactional facts linking stores, customers, and transactions.

Source URL: [https://github.com/abigailmwanza/DataEngineeringFabric/tree/main/Dataset]

## Pipeline Architecture

The pipeline is built in Microsoft Fabric using the Medallion Architecture, with the Bronze layer for raw data ingestion and the Silver layer for data transformation.

### Bronze Layer
Data Source Connection:
The pipeline connects to the GitHub URL using HTTP connectors in Microsoft Fabric.
Each table's data is accessed via its respective file path in the GitHub repository (e.g., / instagram.com/p/C_4oX4Zx6Z6/ data/store_dim.csv).

#### Parameterized Ingestion:
A parameter (table_name) is used to dynamically specify the table to ingest. store_dim, customer_dim, trans_dim, fact_table.
The parameter is passed to the pipeline to construct file paths and target table names.

Pipeline Activities:
ForEach Activity:
Iterates over a list of tables (store_dim, customer_dim, trans_dim, fact_table).
Dynamically passes the table_name parameter to the Copy activity.


Copy Activity:
Source:Reads data from the GitHub URL for the specified table.
Destination: Writes the raw data to the Bronze Lakehouse in Delta format.
Maintains schema and data integrity without transformations (raw ingestion).



Bronze Lakehouse:
The raw data is landed in the Bronze Lakehouse as Delta tables.
Each table (store_dim, customer_dim, trans_dim, fact_table) is stored in its own Delta table.
Path: lakehouse/bronze/{table_name}.

### Silver Layer
Data Source:
The Silver layer pipeline reads data from the Bronze Lakehouse (lakehouse/bronze/{table_name}).



Data Transformations:
A Microsoft Fabric notebook using PySpark perform transformations on the Bronze layer data.
1. Data Type Conversion: Ensured appropriate data types for each column (e.g., converting string IDs to integers, dates to proper date formats).
2. Null Handling: Replaced null values with 'NA' for string columns or appropriate defaults for numeric/date columns.
3. Removed duplicates, standardized string formats (e.g., trimming whitespace, consistent casing).

Each table (store_dim, customer_dim, trans_dim, fact_table) is processed individually to maintain schema integrity.





# COVID-19-Data Engineering Analytics Pipeline

## End-to-End Azure Solution — Ingestion → Transformation → Analytics → ML → Reporting

### 📌 Project Overview
COVID-19 created a global need for accurate, near–real-time reporting of cases, mortality numbers, vaccination trends, and population-normalized metrics. Many organizations struggled with fragmented data sources, inconsistent reporting formats, and lacked the ability to generate timely dashboards for public health stakeholders.

This project builds a real-world enterprise-grade analytics pipeline  using Microsoft Azure to ingest, transform, store, model, and visualize global COVID-19 statistics.

----

### ❗ Business Problem
Public health agencies and analysts require a single source of truth to answer key questions:

- How fast are cases rising across countries/regions?

- Which countries have the highest death rates relative to population?

- What are the weekly/monthly pandemic trends?

- How can machine learning help detect future surges?

- However, the challenges include:

- Multiple external data sources (API, CSV, public datasets).

- Data updated daily requiring automated ingestion.

- Complex transformations (date alignment, aggregation, normalization).

- Need for scalable analytics + centralized data store.

- Need for dashboards accessible by leadership in real-time.
---

### 💡 Solution Summary

This project delivers a fully automated Azure data pipeline that:

1. Ingests COVID-19 case and population data from APIs & Blob Storage.

2. Stores raw datasets in Azure Data Lake Storage Gen2.

3. Transforms & cleans data using Azure Data Factory, Azure Synapse Notebook / Databricks, and SQL transformations.

4. Loads analytics-ready data into Azure SQL Database.

5. Generates ML predictions for case surges (optional).

6. Publishes dashboards to Power BI for public health reporting.
---
### 🏗️ Solution Architecture (Explained)

<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/project_architeture.png">

## 1. Data Sources
- ECDC COVID-19 API → Daily case & death data (https://www.ecdc.europa.eu/en/covid-19)
- Population Data From Azure Blob Storage (eurostat_data)

2. Ingestion Layer

Azure Data Factory pipeline pulls:

- HTTP API data → Raw zone

- Blob data → Raw zone

- Data stored in Azure Data Lake Gen2 (/raw/covid/)

### 3. Transformation Layer

Azure Data Factory + Synapse/Databricks used for:

- Cleaning & reformatting

- Standardizing date fields

- Aggregating case numbers

- Joining with population datasets

- Creating surrogate keys

- Generating fact & dimension tables

## 4. Storage Layer

- Azure Data Lake (Raw → Curated Zones)

Azure SQL Database:

- dim_country

- fact_covid_daily

- fact_covid_weekly

- fact_population

## 5. Machine Learning Layer (Optional)

- Store features in ADLS

Train ML models (Azure ML):

- Time-series forecasting (ARIMA/Prophet)

- Hotspot detection

- Predict next 7–14 days of cases

## 6. Reporting Layer

- Power BI dashboards connected to Azure SQL Database

- Auto-refresh daily

### Destination: 📥📍
- Azure Data Lake Gen2 Storage.

## Tools ⚙

### Data Integration/Ingestion

- ADF Data Flows within the Data Factory

### Transformation

- Data Flows within the Data Factory
- DataBricks

### Data Warehouse Solution

- Azure SQL Database

### Visualization

- Power BI Desktop
- Power BI Service

### Approach

### Environment Setup
- Azure Subscription
- Data Factory
- Azure Blob Storage Account
- Data Lake Storage Gen2
- Azure SQL Database
- Azure Databricks Cluster
- HD Insight Cluster

### DATA EXTRACTION/ INGESTION
Four different datasets were ingested from both the ECDC website and azure blob storage into Datalake Gen2. They are - 

- Cases and Deaths Data
- Hospital Admissions Data
- Population Data
- Test Conducted Data

We used various components of ADF Pipeline activities to ingest the data from both HTTP Data Source and Azure Storage Account to Azure DataLake. Some of those activities are:

- Validation Activity
- Get Metadata Activity
- Copy Activity

### Population Data : Load into Storage Account and move it to Destination Data Lake
Ingest "population by age" data for all EU Countries into the Data Lake to support the machine learning models with the data to predict an increase in Covid-19 mortality rate.

### Solution Flow
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/copy_activity.jpeg">

### Steps:
1. Create a Linked Service To Azure Blob Storage
2. Create a Source Data Set
3. Create a Linked Service To Azure Data Lake storage (GEN2)
4. Create a Sink Data set
5. Create a Pipeline:
- Execute Copy activity when the file becomes available
- Check metadata counts before loading the data using the IF Condition
- Finally Load Data into our destination
6. ScheduleTrigger


### Pipeline Design :
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/pipeline_diagram.png">

### ECDC Data from Web to Destination Data Lake

### ECDC Data Content - Four files of CSV :
1. Case & Deaths Data.csv
2. Hospital Admission Data.csv
3. testing.csv
4. country_response.csv


### Solution Flow
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/solution_flow.png">

Steps:
1. Create a Linked Service using an HTTP connector
2. Create a Source Data Set
3. Create a Linked Service To Azure Data Lake storage (GEN2)
4. Create a Sink Data set
5. Create a Pipeline With Parameters & Variables
6. Lookup to get all the parameters from json file, then pass it to ForEach ECDC DATA as shown below
7. Schedule Trigger
8. 
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/look_up.png">

### Pipeline Design :

<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/pipeline_design.png">

# 2. DATA TRANSFORMATION

The Cases and Deaths data together with the Hospital admissions data was transformed using ADF Data flows. The Data Flows transformation used on both dataset include

- Select transformation
- Lookup transformation
- Filter transformation
- Join transformation
- Sort transformation
- Conditional split transformation
- Derived columns transformation
- Sink transformation

# Data Flows (1) Cases & Deaths Data:

### Solution Flow
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/transform_cases_death_data.png">

### Steps:
1. Cases And Deaths Source (Azure Data Lake Storage Gen2 )
2. Filter Europe-Only Data
3. Select only the required columns
4. PivotCounts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
5. Lookup Country to get country_code_2_digit,country_code_3_digit columns
6. Select Only the required columns for the Sink
7. Create a Sink dataset (Azure Data Lake Storage Gen2)
8. Used Schedule Trigger

pipeline_design
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/solution_flow1.png">


# Data Flows (2) Hospital Admissions Data:

### Solution Flow
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/hospital_admin.png">

### Steps:
1. Hospital Admissions Source (Azure Data Lake Storage Gen2 )
2. Select only the required columns
3. Lookup Country to get country_code_2_digit,country_code_3_digit columns
4. Select only the required columns
5. Condition Split Weekly, Daily Split condition
  - indicator=='Weekly new hospital admissions per 100k' || indicator=='Weekly new ICU admissions per 100k'
  - indicator== "Daily hospital occupancy" || indicator=="Daily ICU occupancy"
6. For Weekly Path
- Join with Date to get ecdc_Year_week, week_start_date, week_End_date
- Piovt Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
- Sort data using reported_year_week ASC and Country DESC
- Select only required columns for sink
- Create a sink dataset (Azure Data Lake Storage Gen2)
- Schedule Trigger
7. For Daily Path
- Pivot Counts using indicator Columns(confirmed cases, deaths) and get the sum of daily cases count
- Sort Data using reported_year_week ASC and Country DESC
- Select only required columns for sink
- Create a sink dataset (Azure Data Lake Storage Gen2)
- Used Schedule Trigger

<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/trigger.png">


# Databricks Activity (3) -- Population File:

<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/databricks_activity.png">

# 3. Copy Data to Azure SQL
1- Copy Cases and Deaths
2- Copy hospital admissions data
3- Copy testing data

<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/copy_data_to_SQL.png">


<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/copy_data_to_SQL2.png">

# 4. Reporting via Power BI

1. Create a Connection from Azure SQL to Power Bi and load the data
2. Analyze the data to get the total confirmed cases and deaths count
3. Identify the trends in data based on reporting date
4. Publish the report to Power BI Server
5. Publish to web

### Covid-19 Trend in the EU/EEA & UK 2020 by Cases, Deaths, Hospital Occupancy, and ICU Occupancy
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/EU-EEA  UK 2020 by Cases.png">


### Covid-19 Cases and Death breakdown by population in the UK, France, and Germany
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/Covid-19 Cases.png">



### Total Number of covid tests carried out vs Confirmed Cases
<img src = "https://github.com/nelson-analytics/Azure-Data-Factory-End-to-End-Pipeline-on-COVID-19-Data/blob/main/project-snapshots/no_of_test.png">


### Used Technologies
- Azure DataFactory
- Azure HDinsight (Hive)
- Azure Databricks (Pyspark, SparkSql)
- Azure Storage Account
- Azure Data Lake Gen2
- Azure SQL Database
































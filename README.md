# Healthcare DLT Pipeline Project

## Overview

This project demonstrates a comprehensive **Delta Live Tables (DLT)** pipeline for healthcare data processing, implementing the **Bronze-Silver-Gold** medallion architecture. The pipeline processes patient admission data with real-time streaming capabilities, data quality enforcement, and multi-dimensional analytics.

## Architecture

### Medallion Architecture Implementation

```
Raw Data Sources → Bronze Layer → Silver Layer → Gold Layer
     ↓                  ↓              ↓              ↓
Diagnosis Mapping   Raw Ingestion  Data Enrichment  Analytics
Patient Admissions   + DQ Checks      + Joins      + Aggregations
```

### Data Flow

1. **Bronze Layer**: Raw data ingestion with data quality constraints
2. **Silver Layer**: Enriched data with business logic and joins
3. **Gold Layer**: Aggregated analytics for business intelligence

## Project Structure

```
Healthcare_DLT_Pipeline_Project/
├── notebooks/
│   ├── dlt_processing.ipynb                # Main DLT pipeline definition
│   └── feed_raw_tables.ipynb               # Data ingestion utility
├── data/                                   # Sample data files
│   ├── diagnosis_mapping.csv
│   └── patients_daily_file_*.csv
└── README.md                               
```

## Data Sources

### 1. Diagnosis Mapping (Reference Data)
- **File**: `diagnosis_mapping.csv`
- **Purpose**: Maps diagnosis codes to human-readable descriptions
- **Schema**: `diagnosis_code`, `diagnosis_description`

### 2. Daily Patient Data (Streaming Data)
- **Files**: `patients_daily_file_*.csv`
- **Purpose**: Daily patient admission records
- **Schema**: `patient_id`, `name`, `age`, `gender`, `address`, `contact_number`, `admission_date`, `diagnosis_code`

## DLT Pipeline Components

### Bronze Layer Tables

#### 1. `diagnosis_mapping`
- **Type**: Live Table (Batch)
- **Purpose**: Reference data for diagnosis codes
- **Data Quality**:
  - `diagnosis_code_not_null`: Ensures diagnosis codes are not null
  - `diagnosis_desc_not_null`: Ensures diagnosis descriptions are not null
- **Violation Handling**: Drops rows with null values

#### 2. `daily_patients`
- **Type**: Streaming Table
- **Purpose**: Real-time ingestion of patient admission data
- **Data Quality**:
  - `patient_id_not_null`: Ensures patient_id (primary_key) is present
  - `required_fields`: Ensures all essential patient fields are populated
- **Violation Handling**: Drops rows with missing critical data

### Silver Layer Tables

#### 1. `processed_patients_data`
- **Type**: Streaming Table
- **Purpose**: Enriched patient data with diagnosis descriptions
- **Business Logic**:
  - LEFT JOIN with diagnosis mapping
- **Data Quality**:
  - `has_diagnosis`: Ensures diagnosis description is available
- **Violation Handling**: Drops rows without diagnosis descriptions

### Gold Layer Tables

#### 1. `patient_stats_by_admission_date`
- **Type**: Live Table
- **Purpose**: Daily operational metrics
- **Metrics**: Patient counts, average age by date and diagnosis
- **Use Case**: Hospital capacity planning, trend analysis

#### 2. `patient_stats_by_diagnosis`
- **Type**: Live Table
- **Purpose**: Medical insights by diagnosis
- **Metrics**: Patient counts, age statistics, gender distribution
- **Use Case**: Medical research, diagnosis prevalence analysis

#### 3. `patient_stats_by_gender`
- **Type**: Live Table
- **Purpose**: Demographic analysis by gender
- **Metrics**: Patient counts, age statistics, diagnosis diversity
- **Use Case**: Population health studies, demographic insights

## Data Quality Framework

### Constraint Types
- **EXPECT**: Validates data quality rules
- **ON VIOLATION DROP ROW**: Removes invalid records
- **ON VIOLATION SET**: Sets default values for invalid records

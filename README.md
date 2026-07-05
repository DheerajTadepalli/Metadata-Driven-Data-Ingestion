# Metadata-Driven-Data-Ingestion
# 🚀 Metadata-Driven Data Ingestion Framework using Azure Synapse

## 📌 Project Overview

This project demonstrates a **Metadata-Driven Data Ingestion Framework** developed using **Azure Synapse Pipelines**.

The solution dynamically ingests data from multiple source systems using a **single parent pipeline** and dedicated **child pipelines**.

### Supported Source Systems

- Azure SQL Database
- Azure Blob Storage (CSV Files)
- REST API

### Target System

- Azure Data Lake Storage Gen2 (ADLS Gen2)

---

# 📖 Project Objective

Instead of creating individual pipelines for every source object, this framework uses a metadata table to dynamically determine:

- Which source system to connect to
- Which table/file/API endpoint to process
- Where the data should be stored in ADLS Gen2

This approach minimizes development effort and makes onboarding new source objects simple by updating only the metadata table.

---

# 🏗 Architecture

> **Replace the image below with your architecture diagram**

```
                     Metadata Table
                  (Azure SQL Database)
                          │
                          ▼
                   Lookup Activity
                          │
                          ▼
               Filter Active Records
                          │
                          ▼
                     ForEach Activity
                          │
                          ▼
                Switch(Source_System)
        ┌─────────────┼──────────────┐
        ▼             ▼              ▼
 Azure SQL       REST API        Blob Storage
 Child Pipeline  Child Pipeline  Child Pipeline
        │             │              │
        └─────────────┼──────────────┘
                      ▼
            Azure Data Lake Storage Gen2
```

---

# ⚙ Technologies Used

| Technology | Purpose |
|------------|---------|
| Azure Synapse Pipelines | Data Orchestration |
| Azure SQL Database | Metadata Table & Source Database |
| Azure Blob Storage | CSV Source Files |
| REST API | JSON Data Source |
| Azure Data Lake Storage Gen2 | Target Storage |
| GitHub | Version Control |

---

# 📂 Source Systems

## Azure SQL Database

Tables

- Employees
- Customers

---

## Azure Blob Storage

Files

- Products.csv
- Sales.csv

---

## REST API

Endpoints

- /users
- /posts

---

# 📋 Metadata Table

| ObjectName | SchemaName | Source_System | FolderPath | TargetName | IsActive |
|------------|------------|--------------|------------|------------|----------|
| Employees | dbo | Data_Base | Database/Employees | Employees | Y |
| Customers | dbo | Data_Base | Database/Customers | Customers | Y |
| users | NULL | Rest_Api | Rest_Api/users | users | Y |
| posts | NULL | Rest_Api | Rest_Api/posts | posts | Y |
| Products | NULL | blob | Blob/Products | Products | Y |
| Sales | NULL | blob | Blob/Sales | Sales | Y |

The metadata table controls the complete ingestion process.

To onboard a new source object, simply insert a new row into the metadata table.

No pipeline modifications are required.

---

# 🔄 Parent Pipeline

Pipeline Name

```
pl_main_05
```

### Workflow

```
Lookup
   │
   ▼
Filter
   │
   ▼
ForEach
   │
   ▼
Switch(Source_System)
   │
   ├── Execute SQL Child Pipeline
   ├── Execute REST Child Pipeline
   └── Execute Blob Child Pipeline
```

---

## Lookup Activity

Reads all records from the metadata table.

---

## Filter Activity

Processes only active records.

Expression

```text
@equals(item().is_active,'Y')
```

---

## ForEach Activity

Loops through every active metadata record.

---

## Switch Activity

Determines which child pipeline should execute.

Expression

```text
@item().Source_System
```

Supported values

```
Data_Base
Rest_Api
blob
```

---

# 🔹 Azure SQL Child Pipeline

Pipeline

```
pl_child_azure_sql_05
```

Purpose

- Reads SQL tables dynamically
- Uses parameterized datasets
- Stores data in ADLS Gen2

---

# 🔹 Blob Child Pipeline

Pipeline

```
pl_child_blob_05
```

Purpose

- Reads CSV files dynamically
- Stores files in ADLS Gen2

---

# 🔹 REST API Child Pipeline

Pipeline

```
pl_child_rest_05
```

Purpose

- Calls REST endpoints
- Stores JSON response into ADLS Gen2

---

# 📤 Dynamic Parameter Passing

The parent pipeline sends metadata values to child pipelines.

Example

| Parameter | Value |
|-----------|-------|
| ObjectName | Employees |
| SchemaName | dbo |
| FolderPath | Database/Employees |

Child pipelines use these parameters dynamically inside datasets.

This allows the same pipeline to process multiple tables/files without any hardcoding.

---

# 📁 ADLS Gen2 Folder Structure

```
Raw
│
├── Database
│   ├── Employees
│   └── Customers
│
├── Blob
│   ├── Products
│   └── Sales
│
└── Rest_Api
    ├── users
    └── posts
```

---

# 📂 Repository Structure

```
Metadata-Driven-Data-Ingestion
│
├── Pipelines
│   ├── Parent Pipeline
│   ├── Azure SQL Child Pipeline
│   ├── Blob Child Pipeline
│   └── REST API Child Pipeline
│
├── Datasets
│
├── LinkedServices
│
├── SQL
│   ├── Source Tables.sql
│   └── Metadata Table.sql
│
├── SampleData
│   ├── Products.csv
│   └── Sales.csv
│
├── Screenshots
│
├── Architecture
│
└── README.md
```

---

# 🔄 End-to-End Pipeline Flow

1. Read metadata from Azure SQL Database
2. Filter active records
3. Iterate through metadata using ForEach
4. Identify source system using Switch activity
5. Execute corresponding child pipeline
6. Read data from source
7. Write data into Azure Data Lake Storage Gen2

---

# 📸 Screenshots

> Add screenshots here after completing the project.

Suggested screenshots

- Parent Pipeline
- Azure SQL Child Pipeline
- Blob Child Pipeline
- REST API Child Pipeline
- Metadata Table
- Monitor Output
- ADLS Folder Structure

---

# ⚡ Features

- Metadata-Driven Framework
- Parent-Child Pipeline Design
- Dynamic Source Selection
- Parameterized Pipelines
- Dynamic Datasets
- Multiple Source Systems
- Dynamic Folder Creation
- Full Load Ingestion
- Scalable Design
- Easy Source Onboarding

---

# 🛠 Challenges Faced

During development the following challenges were encountered:

- Switch activity failed because of an incorrect metadata column reference.
- Blob storage path configuration returned HTTP 404.
- Dynamic parameter mapping between parent and child pipelines.
- Designing reusable parameterized datasets.

Each issue was resolved by validating metadata, dataset configuration and pipeline parameter mappings.

---

# 🚀 Future Enhancements

- Incremental Load
- Watermark Table
- Error Logging
- Retry Policy
- Email Notifications
- Azure Key Vault Integration
- Logging Framework
- Monitoring Dashboard
- Parquet Output Format
- Data Validation
- Audit Table

---

# 👨‍💻 Author

**Dheeraj**

Azure Synapse | Azure Data Factory | Azure SQL Database | ADLS Gen2

---

## ⭐ If you found this project useful, consider giving it a star.

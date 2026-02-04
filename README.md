# dsai3202-lab2


# Data Ingestion Pipeline on Azure: Amazon Electronics Dataset

## Project Overview

This lab demonstrates the implementation of a modern data engineering pipeline using **Azure Cloud Services**. The goal is to ingest raw Amazon Electronics review and product metadata, apply necessary transformations, and store the output in an optimized, partitioned format for downstream analytics.

## Architecture

* 
**Data Lake:** Azure Blob Storage (Gen2 with Hierarchical Namespaces). 


* 
**Orchestration:** Azure Data Factory (ADF). 


* 
**Compute:** Azure ML Compute Instance (Ubuntu VM) for data streaming and pre-processing. 


* 
**Format:** JSON (Raw) to Parquet (Processed). 



---

## Workflow

### 1. Storage Environment Setup

We established an **Azure Data Lake Storage Gen2** account to serve as the central repository. To maintain data governance, we created a three-tier container structure: 

* 
**`raw`**: Stores immutable, original data. 


* 
**`processed`**: Stores data after initial cleaning and format conversion. 


* 
**`curated`**: Reserved for high-quality, analytics-ready data. 



### 2. Data Ingestion & Pre-processing

Two different ingestion methods were used to simulate real-world scenarios:

* 
**Product Metadata (Manual Ingestion):** Uploaded `meta_Electronics.json` via the Azure Portal UI. 


* **Electronics Reviews (Programmatic Ingestion):**
* Provisioned an **Azure ML Compute Instance** (`Standard_DS3_v2`). 


* Streamed the dataset directly from a public URL using `wget`. 


* Generated a **Shared Access Signature (SAS) Token** for secure terminal-based authentication. 


* Used `azcopy` to transfer the unzipped JSON data to the `raw` container. 





### 3. Data Quality Correction

The original metadata file was formatted as Python dictionaries (single quotes), making it unreadable for standard JSON parsers.  We executed a Python script within the VM to:

1. Parse the file using `ast.literal_eval`. 


2. Export it as valid, line-delimited JSON. 


3. Upload the corrected file (`meta_Electronics_fixed.json`) back to the storage account. 



### 4. Automated ETL Pipeline (ADF)

Using **Azure Data Factory**, we built a Mapping Data Flow to automate the conversion of raw JSON reviews into partitioned Parquet files: 

* 
**Source:** Connected to the `raw` container via a Linked Service using Account Key authentication. 


* **Transformation (Derived Column):** Converted the `unixReviewTime` timestamp into a readable `review_year` using the expression:






* 
**Sink (Parquet):** Configured the output to write to the `processed` container. 


* 
**Optimization:** Enabled **Key Partitioning** on the `review_year` column to improve query performance. 



### 5. Automation & Monitoring

* 
**Pipeline:** Orchestrated the data flow within a pipeline named `pl_reviews_ingestion_parquet_partitioned`. 


* 
**Trigger:** Scheduled the pipeline to run on a daily recurrence, demonstrating how the system would handle new data arrivals in a production environment. 


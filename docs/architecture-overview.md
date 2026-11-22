# Technical Architecture & Data Governance Specification

## 1. Ingestion Strategy (Bronze Layer)
* **Source Endpoint:** City of Ottawa ArcGIS REST API (Spatial Infrastructure Layer).
* **Pipeline Object:** `Ingest_OC_Transit_Data.DataPipeline`[cite: 1]
* **Pagination Control:** Uses an `Until` loop in Fabric Data Factory driven by a dynamic offset counter (`resultRecordOffset += batchSize`). The loop terminates automatically when `exceededTransferLimit` evaluates to `false`.
* **Idempotency:** Raw payloads are landed into `Bronze_Lakehouse.Lakehouse` with UTC ingestion timestamps to allow full historical replay without re-querying external municipal servers[cite: 1].

## 2. Transformation & Data Quality Rules (Silver Layer)
* **Notebook Object:** `NB_Transform_Transit_Bronze_to_Silver.Notebook`[cite: 1]
* **Schema Enforcement:** Explicit PySpark `StructType` definitions prevent silent schema drift if upstream API fields change.
* **Deduplication:** Window functions partition records by unique asset ID and retain the latest valid ingestion timestamp.
* **Standardization:** Trims whitespace, standardizes coordinate reference attributes, and quarantines malformed records for audit review.

## 3. Reporting & Semantic Layer (Gold Layer)
* **Semantic & Report Objects:** `Sem_Transit_Stops_Ottawa.SemanticModel` and `RPT_Ottawa_Transit_Analytics.Report`[cite: 1]
* **Dimensional Design:** Facts and dimensions are modeled in a strict Star Schema to eliminate bidirectional filtering and optimize DAX query execution.
* **Access Governance:** Designed for Row-Level Security (RLS) enforcement across operational zones.

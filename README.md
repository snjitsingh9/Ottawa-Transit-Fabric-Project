# Ottawa Transit Analytics — Microsoft Fabric Medallion Architecture

An automated, end-to-end data engineering and business intelligence pipeline that extracts municipal transit infrastructure data from the City of Ottawa ArcGIS REST API, processes it through a multi-layer Medallion architecture in Microsoft Fabric, and delivers governed Power BI self-serve analytics.

---

## Business & Engineering Problem
Municipal spatial APIs (ArcGIS) enforce strict per-request record limits and return deeply nested JSON structures unsuitable for direct BI reporting. Without automated pagination, schema enforcement, and environment isolation, operational reporting relies on manual exports prone to schema drift and stale metrics.

**Solution:** Built a fault-tolerant, metadata-driven ingestion and transformation framework in Microsoft Fabric with full Application Lifecycle Management (ALM) and automated Power BI semantic model refreshes.

---

## System Architecture

```text
[City of Ottawa ArcGIS REST API]
               │
               ▼
(Data Factory Pipeline + Dynamic Offset Pagination)
[Bronze Layer: Lakehouse] ──► Immutable Raw JSON / CSV Landing Zone
               │
               ▼
(PySpark Notebooks: Schema Enforcement & Spatial Parsing)
[Silver Layer: Delta Tables] ──► Cleansed, Deduplicated Infrastructure Entities
               │
               ▼
(Star Schema Semantic Model + Governed DAX)
[Gold Layer: Power BI] ──► Self-Serve Operational & Coverage Dashboards

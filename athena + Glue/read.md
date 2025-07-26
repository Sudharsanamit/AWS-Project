# AWS Server Log Analytics Pipeline (CSV + JSON) with Athena & S3

This project demonstrates an end-to-end **data engineering pipeline** using AWS services to analyze server logs. It handles both structured (CSV access logs) and semi-structured (NDJSON error logs) data formats, simulating a real-world web server monitoring scenario.

## 📌 Objective

- Ingest web server logs (CSV and NDJSON) into Amazon S3.
- Use AWS Glue Crawlers to classify and catalog the data.
- Query ingested logs with Amazon Athena using SQL.
- *(Optional)* Extend the pipeline for data visualization via Amazon QuickSight.

## ⚙️ AWS Services Used

| AWS Service   | Purpose                                        |
|---------------|------------------------------------------------|
| S3            | Storage for both access and error logs         |
| AWS Glue      | Crawlers for log discovery & table cataloging  |
| Athena        | SQL querying on structured & semi-structured data |

## 🪵 Sample Log Files

### Access Logs (CSV)
**File:** `sample_access_logs.csv`
```csv
timestamp,ip,status_code,url
2025-07-01T12:00:00Z,192.168.1.1,200,/home
2025-07-01T12:01:00Z,192.168.1.2,404,/about
2025-07-01T12:02:00Z,192.168.1.3,500,/contact
```

### Error Logs (NDJSON)
**File:** `error_log.ndjson`
```
{"timestamp": "2025-07-01T12:00:00Z", "level": "ERROR", "message": "NullPointer exception"}
{"timestamp": "2025-07-01T12:02:00Z", "level": "WARNING", "message": "Disk space low"}
```

## 🚀 End-to-End Pipeline Steps

### 1. S3 Bucket Setup
- **Bucket Name:** `server-log-bucket-2025`
- **Folders:**
  - `/access_logs/`
  - `/error_logs/`

### 2. Upload Log Files
- Upload `sample_access_logs.csv` to `/access_logs/`
- Upload `error_log.ndjson` to `/error_logs/`

### 3. AWS Glue Crawler Configuration
- **Create two crawlers:**
  - Access Log Crawler (for CSV): points to `/access_logs/`
  - Error Log Crawler (for NDJSON): points to `/error_logs/`
- **Tables Created in Glue Data Catalog:**
  - `access_logs_table`
  - `error_logs_table`

### 4. Athena SQL Queries

Set Athena query result location (e.g., `/athena-results/` in S3).

**Example Queries:**

Request count by status code:
```sql
SELECT status_code, COUNT(*) AS total
FROM access_logs_table
GROUP BY status_code;
```

Count of error levels:
```sql
SELECT level, COUNT(*) AS count
FROM error_logs_table
GROUP BY level;
```

Find top IPs by hit count:
```sql
SELECT ip, COUNT(*) as hits
FROM access_logs_table
GROUP BY ip
ORDER BY hits DESC;
```

## ✅ Outcomes

- Logs **structured and analyzed** directly in S3 using Glue and Athena (serverless, scalable, no infrastructure to manage).
- Ready for automation (trigger-based ingestion/processing via Lambda, EventBridge, etc).
- Realistic simulation of a production-grade log analytics pipeline.

## 🛰️ Optional Extensions

- Connect Athena tables to Amazon QuickSight for real-time dashboards.
- Automate ingestion via AWS Lambda or Step Functions.

Feel free to clone and adapt this project as a template for your own AWS analytics pipelines!

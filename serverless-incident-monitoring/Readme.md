# 🚀 Serverless Incident Monitoring System – AWS

## 📌 Overview

This project implements a **serverless, event-driven incident monitoring system** on AWS. It automatically detects application failures from structured logs, processes them in real time, stores incidents, triggers alerts, and enables SQL-based analytics.

The system is designed using **loosely coupled, event-driven architecture**, ensuring scalability, reliability, and fault tolerance.

---

## 🧠 Architecture

```
Application → S3 → SNS → SQS → Lambda → DynamoDB + S3 → CloudWatch → SNS Alerts → Athena
```

![Architecture Diagram](https://github.com/Sudharsanamit/AWS-Project/blob/main/serverless-incident-monitoring/architecture%20diagram.png)

### 🔍 Key Flow

1. Application uploads logs to **S3**
2. S3 triggers **SNS notification**
3. SNS sends messages to **SQS queue**
4. **Lambda** processes messages from SQS
5. Incidents are stored in:

   * **DynamoDB** (structured storage)
   * **S3 (processed bucket)** (analytics-ready data)
6. **CloudWatch** monitors system health
7. Alerts are sent via **SNS (Email)**
8. **Athena** enables SQL-based analysis on incident data

---

## 🛠️ AWS Services Used

| Service        | Purpose                    |
| -------------- | -------------------------- |
| **S3**         | Store raw & processed logs |
| **SNS**        | Notification & alerting    |
| **SQS**        | Reliable message queue     |
| **Lambda**     | Incident processing logic  |
| **DynamoDB**   | Incident storage           |
| **CloudWatch** | Monitoring & alarms        |
| **Athena**     | SQL-based analytics        |

---

## 📂 Project Structure

```
serverless-incident-monitoring/
│
├── lambda/
│   └── processor.py
│
├── sample-data/
│   ├── incident1.json
│   └── incident2.json
│
└── README.md
```

---

## ⚙️ Implementation Steps

### 🔹 Step 1: Create S3 Bucket (Raw Logs)

* Name:

  ```
  incident-raw-bucket-yourname
  ```
* Region: `ap-south-1`
* Block Public Access: Enabled
* Encryption: Enabled

---

### 🔹 Step 2: Upload Sample Data

```json
{
  "timestamp": "2026-04-08T10:15:30Z",
  "service": "payment-service",
  "level": "ERROR",
  "message": "Database connection timeout",
  "error_code": "DB_CONN_FAIL",
  "region": "ap-south-1"
}
```

---

### 🔹 Step 3: Create SNS Topic

* Name: `incident-topic`
* Add Email subscription
* Confirm via email

---

### 🔹 Step 4: Create SQS Queue + DLQ

**Dead Letter Queue**

```
incident-dlq
```

**Main Queue**

```
incident-queue
```

* Visibility Timeout: 60 seconds
* Max Receive Count: 3

---

### 🔹 Step 5: Connect SNS → SQS

Update SQS policy:

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "SQS:SendMessage",
  "Resource": "YOUR-SQS-ARN",
  "Condition": {
    "ArnEquals": {
      "aws:SourceArn": "YOUR-SNS-TOPIC-ARN"
    }
  }
}
```

---

### 🔹 Step 6: Connect S3 → SNS

* Go to S3 → Properties → Event Notifications
* Event: `ObjectCreated`
* Destination: SNS topic

---

### 🔹 Step 7: Create DynamoDB Table

* Table Name:

  ```
  incident-table
  ```
* Partition Key:

  ```
  incident_id (String)
  ```
* Capacity Mode: On-demand

---

### 🔹 Step 8: Create Lambda Function

* Name:

  ```
  incident-processor-fn
  ```
* Runtime: Python 3.12
* Trigger: SQS (`incident-queue`)

---

### 🔐 Required IAM Permissions

Attach policies:

* AmazonS3FullAccess
* AmazonSQSFullAccess
* AmazonDynamoDBFullAccess
* CloudWatchLogsFullAccess

---

## 💻 Lambda Function Code

```python
import json
import boto3
from datetime import datetime

s3 = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('incident-table')

def lambda_handler(event, context):
    for record in event['Records']:
        
        sqs_body = json.loads(record['body'])
        sns_message = json.loads(sqs_body['Message'])
        
        if 'Records' in sns_message:
            for s3_record in sns_message['Records']:
                
                bucket = s3_record['s3']['bucket']['name']
                key = s3_record['s3']['object']['key']
                
                print(f"Reading file: {key}")
                
                response = s3.get_object(Bucket=bucket, Key=key)
                content = response['Body'].read().decode('utf-8')
                data = json.loads(content)
                
                level = data.get("level", "INFO")
                
                if level in ["ERROR", "CRITICAL"]:
                    
                    incident_id = f"INC-{int(datetime.utcnow().timestamp())}"
                    
                    item = {
                        "incident_id": incident_id,
                        "service": data.get("service"),
                        "level": level,
                        "message": data.get("message"),
                        "error_code": data.get("error_code"),
                        "status": "OPEN",
                        "created_at": str(datetime.utcnow())
                    }
                    
                    table.put_item(Item=item)
                    
                    s3.put_object(
                        Bucket='incident-processed-bucket-yourname',
                        Key=f"processed/{incident_id}.json",
                        Body=json.dumps(item)
                    )
                    
                    print(f"Incident Created: {incident_id}")
                else:
                    print("Ignored")

    return {"statusCode": 200}
```

---

### 🔹 Step 9: Create Processed S3 Bucket

```
incident-processed-bucket-yourname
```

---

## 📊 Monitoring & Alerts

### CloudWatch Alarms:

* Lambda Errors ≥ 1
* SQS Queue Messages ≥ 5
* DLQ Messages ≥ 1

All alarms → **SNS topic (Email alerts)**

---

## 📈 Athena (SQL Analytics)

### Setup

* Configure result location:

```
athena-results-yourname
```

---

### Create Table

```sql
CREATE EXTERNAL TABLE incidents (
  incident_id string,
  service string,
  level string,
  message string,
  error_code string,
  status string,
  created_at string
)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
LOCATION 's3://incident-processed-bucket-yourname/processed/';
```

---

### 🔎 Sample Queries

**All incidents**

```sql
SELECT * FROM incidents;
```

**Critical incidents**

```sql
SELECT * FROM incidents WHERE level = 'CRITICAL';
```

**Count by service**

```sql
SELECT service, COUNT(*) 
FROM incidents 
GROUP BY service;
```

**Error distribution**

```sql
SELECT level, COUNT(*) 
FROM incidents 
GROUP BY level;
```

---

## 🧪 Testing Checklist

* Upload JSON file to S3
* Verify SNS email received
* Check SQS queue messages
* Confirm Lambda execution (CloudWatch logs)
* Verify DynamoDB entries
* Check processed files in S3
* Run Athena queries

---

## ✨ Key Features

* Event-driven architecture
* Fully serverless design
* Real-time incident detection
* Fault-tolerant processing (SQS + DLQ)
* Automated alerting system
* SQL-based analytics with Athena

---

## 🧠 Interview Summary

> Built a serverless incident monitoring system using AWS services where logs uploaded to S3 trigger an event-driven pipeline using SNS and SQS. Lambda processes incidents, stores them in DynamoDB, sends alerts via SNS, and enables SQL-based analytics using Athena.

---

## 🚀 Future Enhancements

* Add Step Functions for workflow orchestration
* Build dashboard using React + API Gateway
* Integrate with Slack / PagerDuty
* Add ML-based anomaly detection

---

## 📜 License

This project is for educational and demonstration purposes.

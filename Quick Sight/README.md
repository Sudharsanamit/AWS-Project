
# 📊 Visualizing DynamoDB Data with Athena and QuickSight (Serverless BI Pipeline)

## 🛠️ What We Built

This project demonstrates how to visualize NoSQL data stored in Amazon DynamoDB using Amazon QuickSight by building a serverless data analytics pipeline with:

- **Amazon DynamoDB** – NoSQL database for storing task data.
- **AWS Lambda + Athena Connector** – Middleware to enable SQL queries on DynamoDB.
- **AWS Glue** – Metadata catalog for Athena.
- **Amazon S3** – Spill location for large query results.
- **Amazon Athena** – SQL-based querying of DynamoDB via Lambda.
- **Amazon QuickSight** – Business intelligence dashboard for data visualization.

![Architecture Diagram](./path-to-your-diagram.png)

---

## 🎯 Use Case

We track tasks with fields like `TaskID`, `TaskName`, `Category`, `CompletionStatus`, and `CreatedDate`. The goal is to:

- Visualize task completion (done vs not done)
- Categorize tasks by type
- Display an interactive list

---

## 💡 Key Features

- Create and manage DynamoDB table manually or programmatically
- Use pre-built Lambda connector to bridge DynamoDB and Athena
- Configure S3 as a spill location for large Athena queries
- Set up QuickSight to query Athena and build dashboards
- Build 3 visuals in QuickSight:
  - Donut chart for completion status
  - Bar chart for tasks by category
  - Data table showing all tasks

---

## 🧰 Services and Setup

### 1. **DynamoDB**
- Table: `my_tasks`
- Partition Key: `task_id` (String)
- Sort Key: `category` (String)

### 2. **Lambda Athena Connector**
- Use AWS pre-built connector
- Enables Athena to run SQL queries on DynamoDB data

### 3. **Glue**
- Auto-generated metadata catalog when creating data source in Athena

### 4. **S3 Bucket**
- Acts as **spill location** (e.g., `athena-spill-ttt`)
- Required due to Lambda’s 6MB max response limit

### 5. **Athena**
- Create data source using Lambda connector
- Run queries like:
  ```sql
  SELECT * FROM "my_tasks";
  ```

### 6. **QuickSight**
- Connect to Athena
- Grant QuickSight permissions to:
  - Invoke Lambda
  - Access S3 spill bucket
- Create dataset → visualizations → analysis

---

## 🔐 IAM Policies Required

Attach the following **inline policies** to your QuickSight IAM role:

### 🧩 Policy 1: Invoke Lambda Function

> Replace `<region>` and `<aws_account_number>` with your actual AWS region and account ID.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lambda:InvokeFunction",
            "Resource": "arn:aws:lambda:<region>:<aws_account_number>:function:athenafederatedcatalog_mydynamodb"
        }
    ]
}
```

---

### 🪣 Policy 2: Access S3 Spill Bucket

> Replace `<bucket_name>` with the name of your S3 bucket used for spill location.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<bucket_name>",
                "arn:aws:s3:::<bucket_name>/*"
            ]
        }
    ]
}
```

---

## 📈 Visualizations

1. **Donut Chart** – Tasks grouped by `completion_status`
2. **Horizontal Bar Chart** – Tasks grouped by `category`
3. **Data Table** – Task list with `task_name`, `created_date`, `category`, `completion_status`

---

## 💰 Cost Breakdown

- ✅ Free Tier Eligible: DynamoDB, S3, Lambda
- 💸 Minimal Cost: Athena ($5/TB scanned), QuickSight (30-day trial)

Estimated cost: **Less than $1**

---

## 🧹 Clean-up (Important!)

Be sure to delete:
- Athena data source
- S3 bucket
- Lambda connector
- DynamoDB table
- Glue connection
- QuickSight subscription (if not using)


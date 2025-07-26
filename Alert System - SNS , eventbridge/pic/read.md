# 📧 Email Alert System for Simulated Sensor Data using AWS

## 🔍 Overview
This project simulates a real-world IoT use case: *monitoring temperature sensor data* and sending **instant email alerts** if readings exceed a threshold. Everything runs on the AWS Free Tier—no hardware required. The system reads simulated sensor data from a JSON file in S3, processes it with an AWS Lambda function every minute, and triggers an SNS email alert if needed.

## 🎯 Features

- **Simulated real-time data feed** (100 fake sensor readings)
- **Scheduled Lambda execution** (every minute) using EventBridge
- **Instant email notifications** via Amazon SNS
- **Sensor record progress tracking** with DynamoDB
- **Fully serverless** using AWS Lambda
- **100% Free Tier compatible**

## 🧱 AWS Services Used

| Service     | Purpose                                        |
| ----------- | ---------------------------------------------- |
| S3          | Store JSON file containing 100 sensor records  |
| Lambda      | Core logic: read, process, alert               |
| DynamoDB    | Track the last processed sensor record         |
| SNS         | Send email notifications for high temperature  |
| EventBridge | Schedule Lambda to run every 1 min             |
| CloudWatch  | Monitor and view Lambda logs                   |


## ⚙️ How It Works

1. **S3** stores a `sensor_data.json` file with 100 simulated sensor readings.
2. **Lambda** is triggered every 1 minute (EventBridge schedule).
3. Lambda reads the *next* record (progress tracked in DynamoDB).
4. If the temperature exceeds the threshold (e.g., 70°C), an email alert is sent via SNS.
5. The processing index is updated in DynamoDB to pick up the next record on the next run.

## 📦 Sample Sensor Data (`sensor_data.json`)

```json
[
  {"sensorId": "sensor-001", "temperature": 45, "timestamp": "2025-07-01T00:00:00Z"},
  {"sensorId": "sensor-002", "temperature": 78, "timestamp": "2025-07-01T00:01:00Z"},
  // ... more records
]
```

## 🧠 Lambda Function (Python 3.12)

```python
import json
import boto3

s3 = boto3.client('s3')
dynamodb = boto3.resource('dynamodb')
sns = boto3.client('sns')

BUCKET_NAME = 'sensor-data-bucket'
FILE_NAME = 'sensor_data.json'
DDB_TABLE = 'SensorDataIndex'
SNS_TOPIC_ARN = 'arn:aws:sns:region:account-id:high-temp-alert'
TEMP_THRESHOLD = 70

def lambda_handler(event, context):
    table = dynamodb.Table(DDB_TABLE)
    response = table.get_item(Key={'id': 'currentIndex'})
    index = response.get('Item', {}).get('index', 0)

    obj = s3.get_object(Bucket=BUCKET_NAME, Key=FILE_NAME)
    data = json.loads(obj['Body'].read())

    if index >= len(data):
        print("All data processed.")
        return {'statusCode': 200, 'body': 'Completed'}

    record = data[index]
    temp = record['temperature']
    sensor_id = record['sensorId']

    if temp > TEMP_THRESHOLD:
        message = f"⚠️ ALERT: {sensor_id} reported {temp}°C"
        sns.publish(TopicArn=SNS_TOPIC_ARN, Message=message, Subject='Temperature Alert')

    table.put_item(Item={'id': 'currentIndex', 'index': index + 1})
    return {
        'statusCode': 200,
        'body': f"Processed record {index}: {record}"
    }
```

## ⏰ EventBridge Scheduling

- Rule Type: **Schedule**
- Expression: `rate(1 minute)`
- Target: Your Lambda function

## 📧 Email Alerts via SNS

1. Create an SNS Topic.
2. Subscribe your email.
3. Confirm the subscription via your inbox.
4. Example alert:

    **Subject:** Temperature Alert  
    **Message:** ⚠️ ALERT: sensor-002 reported 78°C

## 📊 DynamoDB Table Design

| Table Name  | Partition Key      | Sample Attributes                     |
| ----------- | -----------------  | ------------------------------------- |
| SensorDataIndex | id (String)    | {"id": "currentIndex", "index": 37}   |

- *Tracks which record should be processed next.*

## 📈 Monitoring

- Go to **CloudWatch** → Logs → *Your Lambda Function*
- View logs for processed entries and alerts sent

## 💸 Cost & Free Tier Compliance

| Service     | Usage                        | Free Tier? |
| ----------- | ---------------------------- | ---------- |
| Lambda      | ~44,000 runs/month           | ✅         |
| S3          | Single JSON file  **Interested in code, deployment scripts, or a ready-to-go ZIP? Let me know in the issues or discussions!**

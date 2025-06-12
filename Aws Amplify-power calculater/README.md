# ⚡ Power Calculator – Serverless Application on AWS

A fully serverless web application built on AWS that performs power calculations using the formula `P = V × I`. This project demonstrates a real-world use of core AWS services including **Amplify**, **Lambda**, **API Gateway**, **IAM**, and **DynamoDB**.

---

## 🧾 Project Summary

This project allows users to input voltage and current values into a web interface. These inputs are processed using a Python Lambda function, and the result (calculated power) is stored in DynamoDB. The entire application is hosted serverlessly using AWS Amplify.

---

## 🛠️ Services Used

| Service                | Description                                                          |
| ---------------------- | -------------------------------------------------------------------- |
| **AWS Amplify**        | Hosts static web applications and manages front-end deployment.      |
| **AWS Lambda**         | Runs the backend code (Python) to perform power calculations.        |
| **Amazon API Gateway** | Creates and manages the RESTful API used to invoke Lambda functions. |
| **Amazon DynamoDB**    | NoSQL database used to store calculated results.                     |
| **AWS IAM**            | Manages access control and permissions between services.             |

---

## 🔧 How It Works

1. The application is accessed through a static `index.html` page hosted on **AWS Amplify**.
2. The user inputs voltage and current values through the web interface.
3. On submission, the data is sent via a **REST API** created with **Amazon API Gateway**.
4. **API Gateway** invokes a **Python-based AWS Lambda function**, which calculates the power.
5. The Lambda function stores the calculation result in **DynamoDB**.
6. The response is sent back and displayed on the web page.

---

## 🚀 How to Deploy

### 1. **Create and Host the Frontend with Amplify**

* Create a simple `index.html` with form inputs for voltage and current.
* Go to [AWS Amplify](https://console.aws.amazon.com/amplify/) and set up a new app.
* Drag and drop or connect your frontend code (can use GitHub or manual upload).
* Amplify will automatically build and host your site.

### 2. **Create the Lambda Function**

* Go to [AWS Lambda](https://console.aws.amazon.com/lambda/), create a new Python function.
* Add logic to receive `voltage` and `current`, compute `power`, and write it to DynamoDB.

### 3. **Create the API Gateway**

* Go to [Amazon API Gateway](https://console.aws.amazon.com/apigateway/), create a new REST API.
* Create a resource (e.g., `/calculate`) and method (e.g., `POST`).
* Integrate the method with your Lambda function.
* Enable CORS if calling from Amplify.

### 4. **Set Up DynamoDB**

* Go to [Amazon DynamoDB](https://console.aws.amazon.com/dynamodb/), create a new table.
* Use a meaningful primary key (e.g., a timestamp or UUID).
* Add attributes to store `voltage`, `current`, `power`, and `timestamp`.

### 5. **Configure IAM Permissions**

* Create or modify IAM roles to:

  * Allow **API Gateway** to invoke **Lambda**.
  * Allow **Lambda** to write to **DynamoDB**.

### 6. **Connect Frontend to API**

* In `index.html`, use JavaScript `fetch()` or `axios` to send user inputs to the API Gateway endpoint.
* Display the returned power value on the webpage.

---


## ✅ Final Output

* A hosted web application where users can enter values and get the power calculated instantly.
* Serverless backend handling compute and storage without managing any infrastructure.
* Clean architecture suitable for learning or showcasing AWS skills in portfolios.


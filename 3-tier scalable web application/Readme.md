# 🚀 CloudOps Deployment Platform (AWS) – Full Project README

---

## 📌 Project Overview

This project demonstrates a **production-grade cloud architecture** built on AWS, implementing a **3-tier scalable web application** with:

* Frontend (React) → S3 + CloudFront
* Backend (Node.js) → EC2 + Auto Scaling
* Database (MySQL) → RDS
* Networking → VPC (Public + Private Subnets)
* Security → Security Groups, SSL (ACM)
* Monitoring → CloudWatch
* Domain → Route 53

---

## 🧠 Architecture Flow Diagram

```
User (Browser)
     ↓
CloudFront (CDN + HTTPS)
     ↓
S3 Bucket (React Frontend)
     ↓
API Calls → https://api.domain.com
     ↓
Route 53 (DNS Routing)
     ↓
Application Load Balancer (HTTPS)
     ↓
Auto Scaling Group (EC2 Instances)
     ↓
Node.js Backend (PM2 running)
     ↓
RDS (MySQL Database)
```

---

## 🧱 Technologies Used

* AWS EC2
* AWS RDS (MySQL)
* AWS S3
* AWS CloudFront
* AWS Route 53
* AWS VPC
* AWS Auto Scaling
* AWS Load Balancer (ALB)
* AWS CloudWatch
* AWS ACM (SSL)
* Node.js (Express)
* React.js
* MySQL
* PM2 (Process Manager)
* GitHub

---

## 📁 Project Structure

```
cloudops-project/
│
├── backend/
│   ├── server.js
│   ├── package.json
│
├── frontend/
│   ├── src/App.js
│   ├── package.json
│
└── README.md
```

---

## ⚙️ Backend Code (Node.js)

### 📄 server.js

```javascript
const express = require("express");
const mysql = require("mysql2");
const cors = require("cors");

const app = express();
app.use(cors({ origin: "*", methods: ["GET", "POST"] }));
app.use(express.json());

const db = mysql.createConnection({
    host: "RDS-ENDPOINT",
    user: "admin",
    password: "password",
    database: "feedback_app",
    port: 3306
});

db.connect(err => {
    if (err) console.log(err);
    else console.log("DB Connected");
});

app.post("/feedback", (req, res) => {
    const { name, message } = req.body;
    db.query(
        "INSERT INTO feedback (name, message) VALUES (?, ?)",
        [name, message],
        () => res.send("Inserted")
    );
});

app.get("/feedback", (req, res) => {
    db.query("SELECT * FROM feedback", (err, result) => {
        res.json(result);
    });
});

app.listen(5000, () => console.log("Server running"));
```

---

## 🎨 Frontend Code (React)

### 📄 App.js

```javascript
const API = "https://api.yourdomain.com";

fetch(`${API}/feedback`)
```

---

## 🗄️ Database Setup (MySQL)

```sql
CREATE DATABASE feedback_app;

USE feedback_app;

CREATE TABLE feedback (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## 🚀 Deployment Steps (Commands Included)

---

### 🔹 Backend Local Setup

```bash
npm init -y
npm install express mysql2 cors
node server.js
```

---

### 🔹 Frontend Setup

```bash
npx create-react-app frontend
npm start
npm run build
```

---

### 🔹 EC2 Setup

```bash
sudo apt update -y
sudo apt install -y nodejs npm git
sudo npm install -g pm2
```

---

### 🔹 Deploy Backend to EC2

```bash
git clone https://github.com/your-repo/backend
cd backend
npm install
pm2 start server.js
pm2 save
```

---

### 🔹 Auto Scaling User Data Script

```bash
#!/bin/bash

sudo apt update -y
sudo apt install -y nodejs npm git

sudo npm install -g pm2

cd /home/ubuntu

git clone https://github.com/your-repo/backend || true

cd backend

npm install

pm2 start server.js
pm2 save

sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u ubuntu --hp /home/ubuntu
```

---

## 🌐 Networking Setup

* VPC: `10.0.0.0/16`
* Public Subnets → ALB
* Private Subnets → EC2 + RDS
* Internet Gateway → Public access
* NAT Gateway → Private subnet internet

---

## 🔐 Security Groups

| Service | Port   | Source    |
| ------- | ------ | --------- |
| ALB     | 80/443 | 0.0.0.0/0 |
| EC2     | 5000   | ALB SG    |
| EC2     | 22     | My IP     |
| RDS     | 3306   | EC2 SG    |

---

## ⚖️ Load Balancer

* Listener: HTTP → Redirect to HTTPS
* HTTPS → Forward to Target Group
* Health Check Path: `/feedback`

---

## 🔁 Auto Scaling

* Min: 1
* Desired: 2
* Max: 3
* Metric: CPU Utilization

---

## 🪣 S3 + CloudFront

* S3 → Static hosting (React build)
* CloudFront → CDN + HTTPS
* API calls routed to backend domain

---

## 🌍 Domain Setup

* Route 53 Hosted Zone
* Records:

  * `www.domain.com → CloudFront`
  * `api.domain.com → ALB`

---

## 🔐 SSL Setup

* ACM Certificate:

  * `domain.com`
  * `*.domain.com`

---

## 📊 Monitoring (CloudWatch)

* EC2 CPU Monitoring
* ALB Request Count
* ALB 5XX Errors
* RDS CPU + Connections
* Alerts via SNS Email

---

## 🧪 Testing

```bash
curl https://api.domain.com/feedback
```

---

## 🎯 Final Outcome

* Scalable architecture
* Secure HTTPS communication
* Auto-healing infrastructure
* Global CDN delivery
* Real-time monitoring

---

## 🧠 Key Learnings

* VPC networking design
* Load balancing & scaling
* Backend automation (PM2 + User Data)
* CDN + domain integration
* Debugging real cloud issues

---

## 🏁 Conclusion

This project demonstrates a **complete end-to-end AWS cloud deployment pipeline**, covering:

✔ Infrastructure
✔ Deployment
✔ Security
✔ Scaling
✔ Monitoring

---

🚀 Ready for **DevOps / Cloud Engineer roles**

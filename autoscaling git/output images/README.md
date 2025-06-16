# 🚀 AWS Auto Scaling Lab Project

## 🧠 Overview

This project demonstrates an AWS hands-on lab for setting up Auto Scaling Groups with Application Load Balancers, custom VPC subnets, and health checks. It automates recovery by launching new EC2 instances in the event of failure. This setup follows cloud best practices for resilience, scalability, and security.

---

## 🧱 AWS Services Used & Definitions

### ✅ Amazon EC2 (Elastic Compute Cloud)

Amazon EC2 provides secure, resizable compute capacity in the cloud. It allows you to launch virtual servers (instances) to host applications.

### ✅ Auto Scaling Group (ASG)

ASGs help you automatically scale the number of EC2 instances up or down according to defined conditions, maintaining availability.

### ✅ Application Load Balancer (ALB)

An ALB automatically distributes incoming application traffic across multiple targets (EC2 instances) and Availability Zones.

### ✅ VPC (Virtual Private Cloud)

Amazon VPC allows you to provision a logically isolated section of the AWS cloud for launching AWS resources in a defined network.

### ✅ Subnet

A subnet is a range of IP addresses in your VPC. Public subnets have access to the internet via an internet gateway.

### ✅ Target Group

A target group routes requests to registered targets, such as EC2 instances, based on health checks and load balancing algorithms.

### ✅ Security Group

A virtual firewall for controlling inbound and outbound traffic to AWS resources such as EC2 instances or ALBs.

---

## 📝 Step-by-Step Implementation

### 1. **Review Lab Objectives**

* Understand the scope by reviewing objectives in the Concept section.
* Start the lab and open the AWS Console.

---

### 2. **Access EC2 Console**

* Search `ec2` in the top navigation bar.
* Click **EC2** under Services.

---

### 3. **Auto Scaling Group Verification**

* Navigate to **Auto Scaling Groups**.
* Select the group `TravelAgencyWebServers`.
* Verify it’s using a single subnet and one Availability Zone.
* Check capacity and instance count under the **Details** and **Instance management** tabs.

---

### 4. **Load Balancer Configuration**

* Navigate to **Integrations** tab → **Edit** under Load balancing.
* Click **Add a new load balancer**.
* Choose:

  * Load Balancer Type: *Application Load Balancer*
  * Scheme: *Internet-facing*

#### Configure Load Balancer:

* Select **all Availability Zones** and associated **public subnets**.
* For Listeners and Routing:

  * Choose **Create a target group**
  * Keep default name
* Click **Update** to create the ALB.

---

### 5. **Create Security Group – Load Balancer**

* Navigate to **Security Groups** → **Create security group**.
* Name: `TravelAgencyLoadBalancer`
* Description: "Allow access to the travel agency load balancer from the internet"
* Choose VPC: *lab/TravelAgencyVpc*

#### Inbound Rules:

* Type: HTTP
* Source: `0.0.0.0/0`

#### Outbound Rules:

* Type: HTTP
* Destination: `TravelAgencyWebServer` security group
* Remove default `0.0.0.0/0`
* Click **Create security group**

---

### 6. **Update Web Server Security Group**

* Edit **Inbound Rules** of `TravelAgencyWebServer` security group
* Remove existing rule
* Add Rule:

  * Type: HTTP
  * Source: `TravelAgencyLoadBalancer` security group

---

### 7. **Attach Security Group to Load Balancer**

* Navigate to **Load Balancers**
* Select `TravelAgencyWebServers-1`
* Go to **Security tab** → **Edit**
* Remove `TravelAgencyWebServer` SG
* Add `TravelAgencyLoadBalancer` SG
* Click **Save changes**

---

### 8. **Access Web App**

* Copy ALB DNS Name
* Open in browser with prefix `http://`
* Page should load → confirms app is behind ALB

---

### 9. **Health Check Validation**

* In browser, visit:

  ```
  http://<ALB_DNS>/health
  ```
* Confirm message shows instance is healthy

---

### 10. **Configure Health Checks in Target Group**

* Go to **Target Groups** → select `TravelAgencyWebServers-1`
* Edit Health check path to `/health`
* Advanced settings:

  * Unhealthy threshold: `2`
  * Timeout: `2`
  * Interval: `5`
* Click **Save changes**

---

### 11. **Modify Auto Scaling Group Subnet**

* Navigate to **Auto Scaling Groups** → select `TravelAgencyWebServers`
* Edit Network configuration
* Ensure subnet: `PublicSubnet1` is selected
* Click **Update**

---

### 12. **Trigger Scaling by Terminating Instance**

* Go to **Instances**
* Select `TravelAgencyWebServers` instance → Terminate it
* Auto Scaling will launch new instance

---

### 13. **Verify New Instance Launch**

* Refresh Instances page
* Confirm new instance appears
* Note the Instance ID
* Confirm subnet = `PublicSubnet1`

---

### 14. **Check Activity History**

* Go back to **Auto Scaling Group** → **Activity tab**
* Confirm old instance terminated, new instance launched

---

### 15. **Check Health Again**

* Refresh `/health` page in browser
* Confirm message with new Instance ID

---

### 16. **Add Additional Subnet for High Availability**

* Edit ASG Network settings → select `PublicSubnet2` in addition to `PublicSubnet1`

---

### 17. **Scale ASG to 2 Instances**

* Edit ASG Capacity settings:

  * Desired capacity: `2`
  * Max capacity: `2`
* Confirm second instance is launched in another AZ

---

### 18. **Final Health Validation**

* Confirm in `/health` page
* Refresh until instance ID updates
* Ensure both instances are healthy



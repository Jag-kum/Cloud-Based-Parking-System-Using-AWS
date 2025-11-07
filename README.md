# ☁️ Cloud-Based Parking System Using AWS
## 🅿️ SmartPark — Serverless Smart Parking Management System on AWS

SmartPark is a **real-time, fully serverless parking management system** designed to automate vehicle parking operations in environments such as malls, universities, and tech parks.  
It provides **intelligent slot allocation**, **automated billing**, and **revenue analytics** through a cloud-native architecture powered by **AWS services**.

The project leverages **AWS Lambda**, **API Gateway**, **DynamoDB**, **S3**, **CloudFront**, and additional AWS services like **SQS (Dead Letter Queue)** and **DynamoDB Streams** for reliability and real-time updates.  
The system eliminates manual intervention, enhances scalability, and operates with minimal infrastructure overhead.

---

## 🧠 Overview

Urban parking spaces often face challenges like inefficient slot utilization, long search times, and lack of centralized management.  
SmartPark addresses these issues by providing:
- Automated slot management via serverless compute.
- Dynamic revenue calculation and data visualization.
- Instant availability and fault tolerance with AWS-managed services.

The system integrates a **web-based frontend**, a **REST API backend**, and **NoSQL data persistence** using **AWS DynamoDB**, offering end-to-end automation from vehicle entry to billing.

---

## ✨ Key Features

✅ **Smart Slot Management**
- Automatic assignment of nearest available slot based on vehicle type and level.
- Real-time updates to slot availability and status.

✅ **Automated Entry/Exit Workflow**
- API-based entry/exit logging.
- Dynamic pricing model (hourly or day-pass).
- Auto billing calculation with timestamps.

✅ **Revenue Dashboard**
- Displays daily, hourly, and total revenue metrics.
- Visualized using bar and pie charts.
- Fetches real-time data from DynamoDB via AWS Lambda.

✅ **Data Consistency & Fault Tolerance**
- **SQS Dead Letter Queue (DLQ)** ensures no data loss during failed Lambda invocations.
- **DynamoDB Streams** enable asynchronous event-driven updates for analytics.

✅ **Monitoring & Notifications**
- Integrated with **CloudWatch** for logs, alarms, and metrics.
- Optional **SNS alerts** for billing thresholds and system status.

✅ **Scalable & Cost-Effective**
- Built entirely on AWS free-tier eligible services.
- No servers, no maintenance — pay only per execution.

---

## 🧩 System Architecture

```text
   ┌──────────────────────────┐
   │        Frontend          │
   │ (S3 Static Hosting +     │
   │  CloudFront CDN)         │
   └──────────┬───────────────┘
              │
              ▼
   ┌──────────────────────────┐
   │      API Gateway         │
   │     (REST Endpoints)     │
   └──────────┬───────────────┘
              │
              ▼
   ┌──────────────────────────┐
   │        AWS Lambda        │
   │ (Entry / Exit / Slots /  │
   │   Revenue Functions)     │
   └──────────┬───────────────┘
              │
              ▼
   ┌──────────────────────────┐
   │        DynamoDB          │
   │ Slots & Sessions Tables  │
   └──────────┬───────────────┘
              │
     ┌────────┴────────┐
     ▼                 ▼
SQS Dead Letter Queue   DynamoDB Streams  
(Handles failures)      (Triggers async updates)
```

---

## 🚀 Deployment Steps

### 1️⃣ Frontend Deployment (S3 + CloudFront)

**Build the Next.js app:**

```bash
npm install
npm run build

```
Upload the out/ folder to an S3 bucket with Static Website Hosting enabled.
Set index.html and error.html as defaults.

Create a CloudFront distribution pointing to your S3 bucket.
Access your app using the CloudFront URL.

## 2️⃣ Backend Deployment (Lambda + API Gateway)

Create DynamoDB Tables:
```
Slots (Partition Key: slotId)
Sessions (Partition Key: sessionId)
```

Deploy Lambda Functions:
```
/entry   → Handles vehicle entry logic  
/exit    → Computes billing and frees slot  
/slots   → Lists and updates parking slots  
/revenue → Aggregates total/daily revenue
```

Connect to API Gateway:

Create methods: POST /entry, POST /exit, GET /slots, PATCH /slots, GET /revenue

Enable CORS for all methods.

Deploy to stage prod.

## 3️⃣ Additional AWS Setup

Create SQS Dead Letter Queue (DLQ) for failed event handling.

Enable DynamoDB Streams on the Sessions table for real-time updates.

Use CloudWatch for monitoring and metrics.

## 4️⃣ Testing

Use Postman or cURL to verify endpoints:
```
curl -X POST "<API_URL>/entry" \
     -H "Content-Type: application/json" \
     -d '{"numberPlate": "TN07CV7077", "vehicleType": "Car"}'

curl "<API_URL>/revenue"
```

Then open your CloudFront URL and view the live dashboard.

🧰 AWS Services Summary
Service	Purpose
S3	Hosts the static website frontend.
CloudFront	Delivers the frontend globally with caching.
API Gateway	Manages RESTful API endpoints.
AWS Lambda	Executes backend logic serverlessly.
DynamoDB	Stores slot and session data.
DynamoDB Streams	Triggers Lambdas for real-time updates.
SQS (DLQ)	Captures failed events for reliability.
SNS	Sends alerts for billing and monitoring.
CloudWatch	Tracks logs, metrics, and usage.
IAM	Manages secure service permissions.

## 🧾 Example Use Case

A car enters the parking area and is detected via API /entry.

The system assigns the nearest available slot and logs the session in DynamoDB.

When the car exits via /exit, the system calculates billing (hourly/day-pass).

The total revenue updates automatically and is visualized on the dashboard.

SQS handles any failed invocations, while DynamoDB Streams update analytics.

## 📊 Live Dashboard Output

The dashboard displays real-time metrics including:

Total Revenue

Daily Revenue (Bar Chart)

Hourly vs Day-Pass Split (Pie Chart)

Completed Sessions Table



## 🏁 Conclusion

SmartPark demonstrates a robust, fully serverless architecture that automates parking management end-to-end using AWS services.
The system’s design achieves:

High availability via AWS-managed scaling.

Low maintenance through event-driven automation.

Real-time updates using DynamoDB Streams and Lambda.

Fault tolerance through SQS DLQs and monitoring via CloudWatch.

This project effectively highlights how AWS can power scalable, real-time smart infrastructure solutions for urban mobility and smart cities.

# Cloud-Based-Parking-System-Using-AWS
# 🅿️ SmartPark — Serverless Smart Parking Management System on AWS

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

```plaintext
   ┌──────────────────────────┐
   │        Frontend          │
   │ (S3 Static Hosting +     │
   │  CloudFront CDN)         │
   └──────────┬───────────────┘
              │
              ▼
   ┌──────────────────────────┐
   │      API Gateway         │
   │ (REST Endpoints)         │
   └──────────┬───────────────┘
              │
              ▼
   ┌──────────────────────────┐
   │        AWS Lambda        │
   │ (Entry / Exit / Slots /  │
   │  Revenue Functions)      │
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

## 🚀 Deployment Steps
1. **Frontend**
   - Build the Next.js app: `npm install && npm run build`
   - Upload the `out/` folder to an S3 bucket with static hosting enabled.
   - Set up a CloudFront distribution linked to the bucket.

2. **Backend**
   - Create DynamoDB tables: `Slots` and `Sessions`.
   - Deploy Lambda functions for `/entry`, `/exit`, `/slots`, `/revenue`.
   - Connect them via API Gateway (enable CORS).

3. **Additional AWS Setup**
   - Create SQS DLQ for Lambda failures.
   - Enable DynamoDB Streams for real-time updates.
   - Monitor logs and metrics in CloudWatch.

4. **Test**
   - Open your CloudFront URL → interact with the live parking dashboard.


## 🧰 AWS Services Summary

| Service | Purpose |
|----------|----------|
| **S3** | Hosts the static website frontend. |
| **CloudFront** | Delivers the frontend globally with caching. |
| **API Gateway** | Manages RESTful API endpoints. |
| **AWS Lambda** | Executes backend logic serverlessly. |
| **DynamoDB** | Stores slot and session data. |
| **DynamoDB Streams** | Triggers Lambdas for real-time updates. |
| **SQS (DLQ)** | Captures failed events for reliability. |
| **SNS** | Sends alerts for billing and monitoring. |
| **CloudWatch** | Tracks logs, metrics, and usage. |
| **IAM** | Manages secure service permissions. |


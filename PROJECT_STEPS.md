# 🧩 SmartPark — Complete Project Implementation Guide

## 📖 Project Description

SmartPark is a cloud-native, **serverless parking management system** built on AWS.  
It automates slot management, vehicle tracking, and billing operations while providing real-time revenue analytics.  
The system uses **AWS Lambda** as the core compute layer and integrates **API Gateway**, **DynamoDB**, **S3**, and **CloudFront** for a completely managed, cost-efficient, and scalable parking solution.

SmartPark is ideal for smart city infrastructure, malls, universities, and corporate parking facilities that require **real-time slot monitoring**, **dynamic billing**, and **centralized management** — all without maintaining any servers.

---

## 🧱 System Components and Workflow

1. **Frontend (React / Next.js App)**  
   - Hosted on **Amazon S3** using Static Website Hosting.  
   - Delivered globally through **Amazon CloudFront CDN**.  
   - Connects to backend via **API Gateway endpoints**.

2. **Backend (AWS Lambda + API Gateway)**  
   - Stateless compute functions built in Python or Node.js.  
   - Handles all business logic:
     - `/entry` → Register vehicle entry and allocate a slot.  
     - `/exit` → Free slot and calculate revenue.  
     - `/slots` → Fetch and update available slot data.  
     - `/revenue` → Summarize daily and total earnings.

3. **Database Layer (DynamoDB)**  
   - Two tables:
     - `Slots` → Stores level, type, and occupancy data.  
     - `Sessions` → Tracks vehicle ID, entry/exit time, and calculated revenue.
   - Integrated with **DynamoDB Streams** for real-time data synchronization.

4. **Asynchronous Reliability (SQS Dead Letter Queue)**  
   - Used as a fail-safe mechanism to capture failed Lambda events.  
   - Ensures data is never lost due to execution errors.

5. **Monitoring and Notifications**  
   - **CloudWatch** for logs, alarms, and dashboards.  
   - **SNS** (optional) for sending billing or usage alerts.  
   - **IAM** manages roles and least-privilege permissions.

---


---

## ⚙️ Step-by-Step Setup Guide

### 🔹 1. Frontend Setup (S3 + CloudFront)

#### a. Build the Web Application
```bash
npm install
npm run build
This will create a production build (usually in the out/ or build/ directory).
```

b. Configure S3
Create a new S3 bucket (e.g., smartpark-frontend).

Enable Static Website Hosting in bucket settings.

Upload all files from the build folder.

Set index.html and error.html as default documents.

Make the bucket public or use CloudFront OAI (Origin Access Identity) for secure access.

c. Add CloudFront
Open CloudFront and create a new distribution.

Set the S3 bucket as the origin.

Enable Caching, HTTPS, and configure custom domain (optional).

Copy the CloudFront URL (this will serve your app globally).

### 🔹 2. Backend Setup (API Gateway + Lambda)
a. Create Lambda Functions
You will need four core Lambda functions:

Function Name	Purpose
entry	Allocates slot and records vehicle entry.
exit	Frees slot and computes billing.
slots	Fetches and updates slot details.
revenue	Calculates total/daily earnings and analytics.

Each function connects to DynamoDB and returns JSON responses via API Gateway.

Example Python handler:

python
```
import json, boto3, os
dynamodb = boto3.resource('dynamodb')
TABLE = dynamodb.Table(os.environ['TABLE_SESSIONS'])

def lambda_handler(event, _):
    response = TABLE.scan()
    total = sum(int(i.get("revenue", 0)) for i in response["Items"])
    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"totalRevenue": total})
    }
```
b. Configure Environment Variables
Each function needs:

```
REGION = us-east-1
TABLE_SLOTS = Slots
TABLE_SESSIONS = Sessions
```
c. Add Permissions
Attach IAM Role:

DynamoDB (Read/Write)

CloudWatch Logs

SQS (SendMessage)

Lambda Basic Execution Role

### 🔹 3. Create DynamoDB Tables
a. Slots Table
Attribute	Type	Description
slotId	String	Unique slot identifier
level	Number	Parking level
vehicleType	String	Type of vehicle
status	String	"available" / "occupied"

b. Sessions Table
Attribute	Type	Description
sessionId	String	Unique session ID
vehicleNumber	String	Vehicle license plate
entryTime	String	ISO timestamp
exitTime	String	ISO timestamp
revenue	Number	Calculated billing

c. Enable DynamoDB Streams
Go to the Sessions table → Exports and streams.

Enable a new stream (for Lambda event triggers).

Configure it to invoke a Lambda function when items update.

### 🔹 4. Configure API Gateway
Create a new REST API in API Gateway.

Add resources and methods:

Endpoint	Method	Linked Lambda
/entry	POST	smartpark-entry
/exit	POST	smartpark-exit
/slots	GET/PATCH	smartpark-slots
/revenue	GET	smartpark-revenue

Enable CORS for all methods.

Deploy the API to stage prod.

Copy your API invoke URL — it connects your frontend to AWS.

### 🔹 5. Setup SQS Dead Letter Queue (DLQ)
Open Amazon SQS → Create a queue named smartpark-lambda-dlq.

In each Lambda function, add the queue as a dead-letter destination.

Update the Lambda execution role to include:

```
sqs:SendMessage
sqs:GetQueueAttributes
```
This ensures any failed events are captured for later review.

### 🔹 6. CloudWatch & Monitoring
Use Amazon CloudWatch Logs to monitor function execution.

Create Billing Alarms to notify when free-tier limits are near.

Optional: Set up SNS Notifications for email alerts on function failures or threshold events.

### 🔹 7. Integration Summary
Component	Service	Purpose
Frontend	S3 + CloudFront	Host and distribute the app globally
Backend	API Gateway + Lambda	Process API requests and logic
Database	DynamoDB	Store sessions and slot data
Stream Processing	DynamoDB Streams	Trigger event updates
Reliability	SQS DLQ	Capture failed function invocations
Monitoring	CloudWatch + SNS	Observe and alert system status

🔍 Testing and Validation
Test API

Use Postman or cURL with your deployed API endpoints.

Example:

```
curl -X POST "<API_URL>/entry" \
-H "Content-Type: application/json" \
-d '{"numberPlate":"TN07CV7077","vehicleType":"Car"}'
```
Check DynamoDB

Verify that new items appear in the Sessions table.

Observe revenue and exitTime updates on vehicle exit.

Open Frontend

Access the CloudFront URL.

Interact with the UI — verify live slot and revenue updates.

View Logs

Navigate to CloudWatch → Logs → Log groups.

Confirm successful executions and inspect errors if any.

## 🧾 Deliverables and Output
Frontend Dashboard Includes:

Total revenue summary

Daily revenue (bar chart)

Hourly vs Day-Pass split (pie chart)

Completed sessions table

Backend Services Deliver:

Real-time API responses

Scalable, event-driven data flow

Fault-tolerant queue-based reliability

## 🧑‍💻 Project Credits
Developers: Jagadeesh Kumar B , Raghunandeeshwar S , Sujan SJ
Institution: VIT Vellore
Project Title: Cloud-Based Parking System Using AWS
AWS Services Used: Lambda, API Gateway, DynamoDB, S3, CloudFront, SQS, DynamoDB Streams, SNS, CloudWatch, IAM

## 🏁 Conclusion
SmartPark successfully demonstrates how modern serverless infrastructure on AWS can deliver automated, reliable, and scalable parking management.
By integrating event-driven workflows (DynamoDB Streams + Lambda) and fault-tolerant queues (SQS DLQ), the system ensures real-time accuracy and resilience.
Its architecture is modular, cost-effective, and easily extensible — making it an ideal reference for smart-city IoT and cloud-native applications.

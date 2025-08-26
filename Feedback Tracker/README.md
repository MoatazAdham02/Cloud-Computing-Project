# 📙 Feedback Tracker – Serverless AWS Project

## 📌 Overview
The *Feedback Tracker* is a serverless application built on AWS that collects and stores user feedback via a REST API. It uses *API Gateway, **AWS Lambda, and **DynamoDB* for a fully managed, scalable, and cost-effective architecture.  

---

## 🎯 Features
- Accepts *user feedback* through an HTTP POST request
- Stores feedback in *DynamoDB*
- Optional: Connects to *RDS* for extended user profile management
- Handles *rating scores* and feedback categories
- Automatically timestamps each feedback submission
-  *sentiment analysis* on feedback
-  *email/SNS notifications* on new submissions

---

## 🛠 Architecture
1. *API Gateway* – Handles HTTP requests  
2. *AWS Lambda* – Processes, analyzes, and stores feedback  
3. *Amazon Comprehend* (optional) – Performs sentiment analysis  
4. *DynamoDB* – Stores all feedback records  
5. *SNS / SES* (optional) – Sends notifications when new feedback is submitted  

---

### 🔧 API Gateway Setup

Method: POST /feedback
Integration: Lambda function
CORS: Enabled
### 🧪 Testing the API

curl -X POST https://<your-api-id>.execute-api.<region>.amazonaws.com/feedback \
-H "Content-Type: application/json" \
-d '{"userId":"u001","category":"Course","feedback":"Loved the AWS module!","rating":5}'

📈 Querying in DynamoDB

Open DynamoDB in AWS Console
Navigate to FeedbackTable → Explore Table Items
Filter with:

`userId = "u001"

🔁 End-to-End Flow

- User submits feedback via POST request
- API Gateway forwards request to Lambda
- Lambda function:
     - Validates and extracts userId, category, feedback, rating
     - Adds timestamp if date not provided
     - Runs sentiment analysis on feedback (optional)
     - Stores the feedback in DynamoDB
     - Sends email/SNS notifications (optional)
     - DynamoDB stores the feedback with userId and date as keys
     - Lambda returns a success response

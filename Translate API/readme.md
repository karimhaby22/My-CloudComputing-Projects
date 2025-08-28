🌍 API Translator Service

A serverless translation API that uses Amazon Translate to translate text between languages, with logs stored in DynamoDB. The API is exposed via Amazon API Gateway and powered by AWS Lambda.

🚀 Features

Translate text between supported languages using Amazon Translate

Store request/response logs in DynamoDB

REST API endpoints via API Gateway

Pay-per-use, fully serverless, auto-scaling⚙ Architecture [Client / cURL / App]
        ↓
  [API Gateway REST API]
        ↓
     [Lambda Function]
        ↓
  ┌───────────────┐
  │ Amazon Translate │  (translation service)
  └───────────────┘
        ↓
  [DynamoDB Table]  (store request + result)
🧰 Tech Stack

Amazon API Gateway – Expose REST API

AWS Lambda (Python 3.12) – Core logic

Amazon Translate – Language translation

Amazon DynamoDB – Store logs

CloudWatch – Monitoring and logs

🛠 Setup
1. Create DynamoDB Table

Table name: Translations

Partition key: request_id (String)

2. Create Lambda Function

Runtime: Python 3.12

Name: TranslatorLambda

IAM Role with permissions:{
  "Effect": "Allow",
  "Action": [
    "translate:TranslateText",
    "dynamodb:PutItem",
    "logs:*"
  ],
  "Resource": "*"
}
3. Add Lambda Code4. Deploy API Gateway

Create a REST API in API Gateway.

Create a POST /translate endpoint.

Integrate with Lambda.

Enable CORS for frontend access.


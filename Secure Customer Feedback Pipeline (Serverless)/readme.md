# Secure Customer Feedback Pipeline (Serverless)

## Project Overview
This project implements a *fully serverless pipeline* for collecting, processing, and storing customer feedback securely using AWS managed services. It demonstrates how to integrate email ingestion, processing, encryption, storage, and notifications in a secure and scalable workflow.

---

## Architecture

[Customer Email]
|
SES
|
EventBridge
|
Lambda
|
S3 <-- Encrypt with KMS
|
SQS
|
Lambda
|
DynamoDB
|
SNS (Notifications)
|
Team 
---

## Services Used

- *AWS SES*: Receive and store emails
- *EventBridge*: Trigger Lambda functions from SES events
- *Lambda*: Process emails and perform sentiment analysis
- *S3*: Store raw and encrypted emails
- *SQS*: Queue metadata for batch processing
- *DynamoDB*: Store processed feedback
- *SNS*: Notify internal teams
- *KMS*: Encrypt sensitive data

---

## Features

- Secure storage of emails using *S3 + KMS encryption*
- Automated *sentiment analysis* using Python
- *Real-time notifications* for negative feedback via SNS
- Asynchronous processing using *SQS*
- Fully serverless, scalable architecture

---

## Setup Instructions

### 1. SES - Receive Emails
1. Verify your email/domain in [AWS SES Console](https://console.aws.amazon.com/ses/home).
2. Create a *Receipt Rule*:
   - Store emails in S3.
   - Trigger EventBridge events.

### 2. EventBridge - Trigger Lambda
1. Create a rule for *SES Email Received* events.
2. Set the Lambda function as the target.

### 3. Lambda - Process Emails
1. Create a Lambda function (Python 3.9+).
2. Assign permissions: S3, DynamoDB, SNS, KMS.
3. Set environment variables: S3_BUCKET, SNS_TOPIC_ARN, DDB_TABLE, KMS_KEY_ID.
4. Implement email parsing, sentiment analysis, encryption, storage, and notifications.

### 4. S3 - Store Emails
- Create a bucket with *Server-Side Encryption (KMS)*.
- Enable versioning if needed.

### 5. SQS - Batch Processing
- Create an SQS queue for email metadata.
- Optional: Batch Lambda processing for analytics.

### 6. DynamoDB - Store Processed Feedback
- Table: FeedbackTable
- Primary Key: EmailID
- Store sentiment and encrypted content.

### 7. SNS - Notify Team
- Create an SNS topic.
- Subscribe team emails or endpoints.
- Publish alerts for negative feedback.

### 8. KMS - Encrypt Sensitive Data
- Create a KMS key.
- Use in Lambda to encrypt email content before storage.

---

## Lambda Example Code

```python
import boto3
import email
from textblob import TextBlob
import base64

s3 = boto3.client('s3')
dynamodb = boto3.client('dynamodb')
sns = boto3.client('sns')
kms = boto3.client('kms')

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(Bucket=bucket, Key=key)
    raw_email = response['Body'].read().decode('utf-8')
    msg = email.message_from_string(raw_email)
    body = msg.get_payload(decode=True).decode('utf-8')

    sentiment_score = TextBlob(body).sentiment.polarity
    sentiment = 'Positive' if sentiment_score > 0 else 'Negative' if sentiment_score < 0 else 'Neutral'

    encrypted_body = kms.encrypt(KeyId='YOUR_KMS_KEY_ID', Plaintext=body)['CiphertextBlob']
    encrypted_body_b64 = base64.b64encode(encrypted_body).decode('utf-8')

    dynamodb.put_item(
        TableName='FeedbackTable',
        Item={
            'EmailID': {'S': key},
            'Sentiment': {'S': sentiment},
            'Content': {'S': encrypted_body_b64}
        }
    )

    if sentiment == 'Negative':
        sns.publish(
            TopicArn='YOUR_SNS_TOPIC_ARN',
            Message=f'Negative feedback received: {key}'
        )

    return {'status': 'success'}
###Learning Outcomes

Integrate multiple AWS services in a serverless workflow

Secure data storage with S3 + KMS

Asynchronous processing using SQS

Real-time notifications using SNS

Process and analyze email content using Lambda + DynamoDB

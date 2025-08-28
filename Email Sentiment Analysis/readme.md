# Email Sentiment Analyzer on AWS

A serverless solution to analyze the sentiment of incoming emails using *AWS SES, **SNS, **Lambda, and **S3*. This project automatically detects the sentiment of emails and stores results for further processing or notifications.

---

## Features

- Receive emails via *AWS SES*.
- Trigger *AWS Lambda* functions when a new email arrives.
- Analyze sentiment using a sentiment analysis library or AWS Comprehend.
- Store email content and sentiment results in *S3*.
- Send notifications or alerts via *SNS*.

---

## Architecture

1. *SES (Simple Email Service)*  
   Receives incoming emails and stores them in an S3 bucket.

2. *S3 (Simple Storage Service)*  
   Stores raw email content and processed sentiment results.

3. *Lambda*  
   Processes the email:
   - Reads email from S3
   - Performs sentiment analysis
   - Saves results back to S3
   - Publishes notification to SNS

4. *SNS (Simple Notification Service)*  
   Sends notifications about the sentiment analysis results (optional).
SES → S3 → Lambda → S3 (results) → SNS (notifications)
---

## Prerequisites

- AWS Account
- IAM Role with permissions for:
  - SES
  - Lambda
  - S3
  - SNS
- Python 3.9+ (if using Python Lambda)
- AWS CLI configured

---

## Deployment

1. *Setup SES*
   - Verify your domain/email in SES.
   - Configure SES to deliver incoming emails to S3.

2. *Setup S3*
   - Create an S3 bucket to store incoming emails.
   - Create a separate S3 bucket (optional) for sentiment results.

3. *Create Lambda Function*
  4.	Setup SNS
	•	Create a topic for notifications.
	•	Subscribe email or endpoints for alerts.

⸻

Usage
	1.	Send an email to your verified SES email address.
	2.	Lambda is triggered automatically when SES delivers the email to S3.
	3.	Sentiment analysis results are saved in S3 and optionally notified via SNS.

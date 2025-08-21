# AWS Event-Driven Projects

This repository contains *two example projects* demonstrating event-driven architectures using *Amazon S3, EventBridge, Lambda, SNS, and SQS*.

---

## Project 1: File Upload Trigger with EventBridge + S3

### Scenario

Automatically react to files uploaded to an S3 bucket (logs, images, reports) using *Amazon EventBridge*. Avoid polling; trigger downstream actions directly.

### Architecture

1. *Amazon S3 Bucket* – User uploads a file.  
2. *EventBridge Rule* – Detects Object Created events.  
3. *Target Service* – Routes event to:
   - *SNS Topic* for notifications.
   - Optionally, a *Lambda function* for processing.

*Workflow:*  
📂 *S3 File Upload* → 🔔 *EventBridge Rule* → 📩 *SNS Notification*

### Setup Steps

1. *Create an S3 Bucket*  
   Example: student-upload-bucket.

2. *Create EventBridge Rule*  
   - Event Source: *S3*  
   - Event Type: *AWS API Call via CloudTrail → PutObject*  
   - Target: *SNS Topic* (FileUploadNotifications)

3. *Create an SNS Topic*  
   - Name: FileUploadNotifications  
   - Subscribe your email.

4. *Test*  
   - Upload a file to S3 → EventBridge triggers → SNS sends email.

*Reference:* [AWS EventBridge Docs](https://docs.aws.amazon.com/eventbridge/latest/userguide/event-types.html)

---

## Project 2: SNS → SQS Fan-Out with Email Notifications

### Scenario

Send messages to multiple targets using SNS → SQS integration. Enables email notifications and queue processing simultaneously.

### Architecture

📥 *SNS Topic* → 📧 *Email Notification*  
📥 *SNS Topic* → 📥 *SQS Queue*

### Setup Steps

#### Step 1: Create SQS Queue
- Console: *SQS → Create Queue*  
- Name: MyTaskQueue  

#### Step 2: Create SNS Topic
- Console: *SNS → Create Topic*  
- Name: MyQueueNotifications  

#### Step 3: Subscribe Email to SNS
- Protocol: Email  
- Endpoint: Your email  
- Confirm subscription via email.

#### Step 4: Connect SQS to SNS
- In SNS topic → *Create subscription*  
- Protocol: Amazon SQS  
- Target: MyTaskQueue  

#### Step 5: Permissions (SQS Policy)

json
{
  "Version": "2012-10-17",
  "Id": "SQSPolicyForSNS",
  "Statement": [
    {
      "Sid": "AllowSNSPublish",
      "Effect": "Allow",
      "Principal": { "Service": "sns.amazonaws.com" },
      "Action": "SQS:SendMessage",
      "Resource": "arn:aws:sqs:us-east-1:123456789012:MyTaskQueue",
      "Condition": { "ArnEquals": { "aws:SourceArn": "arn:aws:sns:us-east-1:123456789012:MyQueueNotifications" } }
    }
  ]
}
#### Step 6: Test Flow
- Publish a message via SNS → delivered to **both SQS and email**.  
- Verify by checking **email inbox** and **SQS console**.

**Reference:** [AWS SNS to SQS Fan-Out](https://docs.aws.amazon.com/sns/latest/dg/sns-send-message-to-sqs.html)

---

### Workflow Diagram (Simplified)

#### Step 6: Test Flow
- Publish a message via SNS → delivered to **both SQS and email**.  
- Verify by checking **email inbox** and **SQS console**.

---

### Workflow Diagram S3 Upload → EventBridge → SNS → Email````
↘→ SQS → Lambda (optional)   
---

### Notes

- Messages sent *directly to SQS* do *not trigger SNS email notifications*.  
- EventBridge for S3 requires *CloudTrail integration* for object-level events.  
- SNS → SQS allows multiple subscriptions (fan-out pattern).

`markdown
# Resume Ranker with AWS Comprehend, S3, Lambda, and DynamoDB

This project demonstrates a serverless pipeline for **resume keyword ranking** using:

- **Amazon S3** for resume storage
- **AWS Lambda** for processing resumes
- **Amazon Comprehend** for key phrase extraction
- **Amazon DynamoDB** for storing resume scores

---

## 🚀 Steps to Deploy

### Step 1: Create an S3 Bucket

- Bucket name: `resume-upload-bucket-[yourname]`
- Enable versioning (optional)
- Disable public access
- Will configure **S3 event trigger** for Lambda later

---

### Step 2: Add a Sample Resume

Save the following text into `resume1.txt` and upload it to your S3 bucket:

text
John Doe
Experienced cloud engineer with skills in AWS, Lambda, S3, DynamoDB.
Certified Solutions Architect. Worked on CI/CD pipelines using Jenkins.
`

---

### Step 3: IAM Role for Lambda

Create an IAM role named: `lambda-comprehend-role`

Attach the following policy:

json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "comprehend:DetectEntities",
        "comprehend:DetectKeyPhrases",
        "s3:GetObject",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}


✅ For production: use **least privilege** and restrict specific ARNs.

---

### Step 4: Create Lambda Function

* **Runtime**: Python 3.12
* **Role**: `lambda-comprehend-role`

Paste the following code:

python
import boto3
import json

comprehend = boto3.client('comprehend')
s3 = boto3.client('s3')
dynamo = boto3.resource('dynamodb')
table = dynamo.Table('ResumeScores')

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key    = event['Records'][0]['s3']['object']['key']
    
    obj = s3.get_object(Bucket=bucket, Key=key)
    text = obj['Body'].read().decode('utf-8')

    # Extract key phrases
    response = comprehend.detect_key_phrases(Text=text, LanguageCode='en')
    phrases = [phrase['Text'].lower() for phrase in response['KeyPhrases']]

    # Keywords to match
    target_keywords = ['aws', 'lambda', 'dynamodb', 'jenkins']
    score = sum([1 for keyword in target_keywords if keyword in phrases])

    print(f"Resume: {key} | Score: {score} / {len(target_keywords)}")

    # Save to DynamoDB
    table.put_item(Item={
        'resumeId': key,
        'score': score,
        'phrases': phrases
    })
    
    return {
        'statusCode': 200,
        'body': f'Resume {key} scored {score}'
    }


---

### Step 5: Add S3 Trigger to Lambda

* Event type: **PUT**
* Bucket: `resume-upload-bucket-[yourname]`
* (Optional) Prefix: `resumes/`

---

### Step 6 (Optional): Create DynamoDB Table

Table: `ResumeScores`

* Partition key: `resumeId` (String)

This table stores the score and extracted phrases for each resume.

---

## ✅ Testing

1. Upload `resume1.txt` to your S3 bucket
2. Lambda will trigger automatically
3. Check **CloudWatch Logs** to see scoring output
4. Query **DynamoDB** (`ResumeScores`) to view stored results

---


```

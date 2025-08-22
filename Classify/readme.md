📂 AWS S3 Document Classifier

A serverless pipeline using Amazon S3 + AWS Lambda + Amazon Comprehend to automatically classify uploaded text files into categories (finance, healthcare, education, uncategorized) based on detected keywords and phrases.

When you upload a text file into your S3 bucket, the Lambda function triggers automatically, analyzes the content with Amazon Comprehend, and moves the file into the correct category folder inside the same bucket.

🚀 Features

Triggered automatically on S3 file upload

Detects document language (using Comprehend)

Extracts key phrases

Classifies into categories:

finance/ → contains words like invoice, payment

healthcare/ → contains words like patient, treatment

education/ → contains words like student, teacher

uncategorized/ → fallback for everything else

Moves file into the correct folder (copy + delete)

🏗️ Architecture

Amazon S3 – Upload triggers Lambda via event notification

AWS Lambda – Reads file, analyzes text, classifies, and moves file

Amazon Comprehend – Detects language + extracts key phrases

Amazon S3 – Stores the classified file into category subfolders

📦 Setup Instructions
1. IAM Setup

Create an IAM Role for Lambda with permissions:

AmazonS3FullAccess

ComprehendFullAccess

CloudWatchLogsFullAccess

2. S3 Setup

Create an S3 bucket (e.g., document-classifier-bucket)

Add a folder uploads/ for incoming files

Configure an event notification on the bucket:

Event type: PUT (object created)

Prefix: uploads/

Destination: Lambda function

3. Lambda Function

Deploy the following code:

import boto3

s3 = boto3.client('s3')
comprehend = boto3.client('comprehend')


def move_object(bucket, source_key, destination_key):
    """Move an object within the same bucket (copy + delete)."""
    copy_source = {'Bucket': bucket, 'Key': source_key}
    s3.copy_object(Bucket=bucket, CopySource=copy_source, Key=destination_key)
    s3.delete_object(Bucket=bucket, Key=source_key)


def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key    = event['Records'][0]['s3']['object']['key']
    
    obj = s3.get_object(Bucket=bucket, Key=key)
    text = obj['Body'].read().decode('utf-8')

    # Detect language
    lang_response = comprehend.detect_dominant_language(Text=text)
    lang = lang_response['Languages'][0]['LanguageCode']
    
    # Extract key phrases
    topic_response = comprehend.detect_key_phrases(Text=text, LanguageCode=lang)
    keywords = [kp['Text'].lower() for kp in topic_response['KeyPhrases']]

    # Keyword-based classifier
    if 'invoice' in keywords or 'payment' in keywords:
        category = 'finance'
    elif 'patient' in keywords or 'treatment' in keywords:
        category = 'healthcare'
    elif 'student' in keywords or 'teacher' in keywords:
        category = 'education'
    else:
        category = 'uncategorized'

    # Build new key under category folder
    new_key = f"{category}/{key.split('/')}"

    # Move file
    move_object(bucket, key, new_key)
    
    return {
        'statusCode': 200,
        'body': f'File moved to {new_key}'
    }

🔥 Usage Example
Upload Test Files

Upload some .txt files into your uploads/ folder in S3:

invoice.txt

This invoice confirms the payment for your subscription.


➡️ Moved to finance/invoice.txt

patient.txt

The patient has started a new treatment plan.


➡️ Moved to healthcare/patient.txt

student.txt

The teacher assigned homework to the student.


➡️ Moved to education/student.txt

random.txt

The weather is nice today.


➡️ Moved to uncategorized/random.txt

🛠️ Technologies

AWS Lambda

Amazon S3

Amazon Comprehend

📌 Future Improvements

Expand keyword rules into a configurable JSON (dynamic categories)

Add DynamoDB logging for classified files

Use Comprehend custom classification model for better accuracy

✨ Now your S3 bucket can classify files in real time with just Lambda + Comprehend.

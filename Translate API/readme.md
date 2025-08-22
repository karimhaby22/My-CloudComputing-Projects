🌐 Real-Time Translator API (AWS)

A serverless translation API built with AWS Lambda, API Gateway, Amazon Translate, and Amazon Comprehend.
This API translates text into English (or another target language) and performs sentiment analysis on the result.

🚀 Features

Translate any text into your chosen target language using Amazon Translate

Detect sentiment (Positive, Negative, Neutral, Mixed) with Amazon Comprehend

Exposed via REST API Gateway endpoint

Serverless (no servers to manage)

Optional logging to Amazon S3

🏗️ Architecture

API Gateway → Handles HTTP requests

Lambda → Core logic for translation + sentiment

Amazon Translate → Language translation

Amazon Comprehend → Sentiment analysis

S3 (Optional) → Store logs / history

📦 Setup Instructions
1. IAM Setup

Create a Lambda execution role with permissions:

AmazonTranslateFullAccess

ComprehendFullAccess

AmazonS3FullAccess (optional)

CloudWatchLogsFullAccess

2. Lambda Function

Create a Lambda function and paste the following code:
import json
import boto3
translate = boto3.client('translate')

def lambda_handler(event, context):

    try:

        # Print for debugging
        print("Received event:", event)

        # Safely parse input
        body = json.loads(event.get('body', '{}'))

        text = body.get('text', '')
        target_lang = body.get('target', 'es')  # default to Spanish

        if not text:
            return {
                'statusCode': 400,
                'body': json.dumps({'error': 'Missing "text" in request'})
            }

        result = translate.translate_text(
            Text=text,
            SourceLanguageCode='auto',
            TargetLanguageCode=target_lang
        )

        return {
            'statusCode': 200,
            'body': json.dumps({'translatedText': result['TranslatedText']})
        }

    except Exception as e:
        print("Error:", str(e))
        return {
            'statusCode': 500,
            'body': json.dumps({'error': str(e)})
        }
3. API Gateway

Create a POST endpoint (e.g., /translate)

Integrate with the Lambda function

Enable CORS if needed

Deploy the API
Request
curl -X POST "https://q0rs1d8j06.execute-api.us-east-1.amazonaws.com/karimhabytranslate" -H "Content-Type: application/json" -d "{\"text\": \"Guten Tag Welt\", \"target\": \"en\"}"
Response
{"translatedText": "Good day world"}
🛠️ Technologies

AWS Lambda

Amazon API Gateway

Amazon Translate

Amazon Comprehend

Amazon S3 (optional)

📌 Future Improvements

Add support for batch translations

Store translation history in DynamoDB

Add authentication via IAM / Cognito

✨ Built with AWS Serverless Services for real-time translation and sentiment analysis.

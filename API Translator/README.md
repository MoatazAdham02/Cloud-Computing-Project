🌍 AWS API Translator

An **API Gateway + Lambda + Amazon Translate** project that accepts text in any language and returns the English translation.  

---

📌 Overview
This project uses:
- **Amazon API Gateway** → to expose an HTTP API endpoint.  
- **AWS Lambda** → to handle requests and connect with Amazon Translate.  
- **Amazon Translate** → to automatically detect the source language and translate it into English.  

---

⚙️ Architecture

Client → API Gateway → Lambda → Amazon Translate → Response

yaml
Copy
Edit

---

🚀 Setup Instructions

1. IAM Permissions
Make sure your IAM user/role has these policies:
- `AmazonAPIGatewayAdministrator`
- `AWSLambda_FullAccess`
- `TranslateFullAccess`
- `CloudWatchLogsFullAccess` (for debugging)

---

2. Create Lambda Function

1. Go to **AWS Console → Lambda → Create function**  
   - Runtime: Python 3.12  
   - Name: `TranslatorLambda`  

2. Use this code:

```python 3.12(runtime)
import json
import boto3

translate = boto3.client("translate")

def lambda_handler(event, context):
    try:
        body = json.loads(event["body"])
        text_to_translate = body.get("text", "")

        if not text_to_translate:
            return {
                "statusCode": 400,
                "body": json.dumps({"error": "No text provided"})
            }

        result = translate.translate_text(
            Text=text_to_translate,
            SourceLanguageCode="auto",
            TargetLanguageCode="en"
        )

        return {
            "statusCode": 200,
            "headers": {"Content-Type": "application/json"},
            "body": json.dumps({
                "original": text_to_translate,
                "translated": result["TranslatedText"],
                "sourceLang": result["SourceLanguageCode"]
            })
        }

    except Exception as e:
        return {
            "statusCode": 500,
            "body": json.dumps({"error": str(e)})
        }
```
Then, deploy the function.

3. Create API Gateway

Go to API Gateway → Create API → HTTP API
Add a POST route: /translate
Integration: select Lambda
Deploy to stage dev

4. Test the API
Use curl or Postman:

bash
Copy
Edit
curl -X POST "https://8xbrum3qnc.execute-api.us-east-1.amazonaws.com/translateprojectnada" -H "Content-Type: application/json" -d "{\"text\": \"Bonjour\", \"target\": \"en\"}"
✅ Example Response:

json
Copy
Edit
{
  "original": "Bonjour",
  "translated": "Hello",
  "sourceLang": "fr"
}

🔐 Optional Enhancements:

CORS → Enable if calling from a frontend app
API Key → Require authentication for clients
Usage Plans → Limit API calls per client

📖 Notes
Amazon Translate supports 75+ languages.

SourceLanguageCode="auto" automatically detects the input language.

You can change TargetLanguageCode from "en" to another supported language.

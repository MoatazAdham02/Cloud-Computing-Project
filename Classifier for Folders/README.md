Here are the steps to create a Classifier for Folders:

1. Create a S3 bucket
2. Define the role for the Lambda function
3. Create the Lambda function and add the trigger.

```python 3.12 code:
import boto3

s3 = boto3.client("s3")

def lambda_handler(event, context):
    # Get the uploaded file details from S3 event
    bucket = event["Records"][0]["s3"]["bucket"]["name"]
    key = event["Records"][0]["s3"]["object"]["key"]

    # Download file content
    obj = s3.get_object(Bucket=bucket, Key=key)
    content = obj["Body"].read().decode("utf-8").lower()

    # Detect category
    if "invoice" in content or "payment" in content:
        category = "finance"
    elif "patient" in content or "treatment" in content:
        category = "healthcare"
    elif "student" in content or "teacher" in content:
        category = "education"
    else:
        category = "uncategorized"

    # New key inside the category folder
    new_key = f"{category}/{key}"

    # Copy file into category folder
    s3.copy_object(
        Bucket=bucket,
        CopySource={"Bucket": bucket, "Key": key},
        Key=new_key
    )

    # Keep the original file (do not delete it)
    return {
        "statusCode": 200,
        "body": f"File '{key}' categorized into '{category}'"
    }
```

4. Go to your bucket and add the text files
5. The lambda function will automatically create the folders and add the files in those folders.

N.B The lambda function created those folders based on the if condition in the Python code

📸 Project: Image Auto-Tagging with Amazon Rekognition

README

---

✅ What It Does

This serverless project automatically tags images uploaded to an *S3 bucket* using *Amazon Rekognition*.

When an image is uploaded:

1. An *AWS Lambda* function is triggered  
2. The image is analyzed using *Amazon Rekognition* to detect labels (e.g., "Person", "Car", "Outdoor")  
3. Detected labels are stored in a *DynamoDB* table for future reference or search  

Perfect for:

- Smart media libraries  
- Content filtering  
- Metadata enrichment  


🔧 Step-by-Step Setup Instructions

Step 1: Create the S3 Bucket*

1. Open the **[Amazon S3 Console](https://console.aws.amazon.com/s3/)**  
2. Click *Create bucket*  
3. Choose a unique name (e.g., image-tagging-bucket-yourname)  
4. ⚠ For testing only: Uncheck *"Block all public access"* if you need public access (not recommended for production)  
5. (Optional) Enable versioning for resilience  
6. Click *Create bucket*  

> This bucket will hold your uploaded images and trigger the Lambda function.

---

Step 2: Create the DynamoDB Table*

1. Go to the **[DynamoDB Console](https://console.aws.amazon.com/dynamodb/)**  
2. Click *Create table*  
3. *Table name:* ImageLabels  
4. *Partition key:* imageName (Type: String)  
5. Leave other settings as default and click *Create*  

> This table will store image names and their detected labels.

---

Step 3: Create the Lambda Function*

1. Navigate to the **[AWS Lambda Console](https://console.aws.amazon.com/lambda/)**  
2. Click *Create function*  
3. Select *Author from scratch*  
4. *Function name:* ImageTaggingFunction  
5. *Runtime:* Python 3.12  
6. *Permissions:* Create a new role with basic Lambda permissions  

> This function will process S3 upload events and analyze images.

---

Step 4: Add Lambda Code*
(Add your Lambda function code here.)

---

Step 5: Set Up S3 Event Trigger*

1. Go to your *S3 bucket* → *Properties* tab  
2. Scroll to *Event notifications* → Click *Create event notification*  
3. Configure:  
   - *Name:* LambdaTrigger  
   - *Event types:* Put Object  
   - *Send to:* Lambda function  
   - *Lambda function:* Select ImageTaggingFunction  
4. Save  

> Now, every image upload will invoke your Lambda function.

---

Step 6: Configure IAM Permissions*

Ensure your Lambda execution role has the required permissions:

1. Go to **[IAM Console](https://console.aws.amazon.com/iam/)** → *Roles*  
2. Find the role attached to ImageTaggingFunction  
3. Attach the following *managed policies*:  
   - AmazonRekognitionFullAccess  
   - AmazonDynamoDBFullAccess  
   - AmazonS3ReadOnlyAccess  

> These permissions allow Lambda to access S3, call Rekognition, and write to DynamoDB.

---

✅ Testing the System

1. Upload an image (e.g., cat.jpg) to your S3 bucket  
2. Wait a few seconds for processing  
3. Go to *DynamoDB Console* → ImageLabels → *Explore table*  
4. Look for an entry with your image name and a list of detected labels (e.g., ["Cat", "Animal", "Pet"])

---

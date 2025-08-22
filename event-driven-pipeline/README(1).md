# Face Alert System with AWS Rekognition

## 📌 Project Overview
This project implements an **automated face recognition and alert system** using **AWS services**.  
When a new image is uploaded to a designated S3 bucket, the system:

- Detects faces in the image.  
- Compares them to a reference collection of known faces.  
- Sends an **SNS alert** if any unknown face is detected.  
- Indexes unknown faces for future tracking.  

---

## 🏗 Architecture

Reference Images Bucket (collectionalert-bucket)
│
▼
Rekognition Reference Collection (collectionalert)
│
New Uploads Bucket (facealert)
│
▼
AWS Lambda Function (Face Alert Logic)
│
┌──────────┴───────────┐
▼ ▼
SNS Alert Rekognition UnknownFaces Collection

yaml
Copy
Edit

### 🔄 Flow
1. User uploads a new image to **facealert S3 bucket**.  
2. **S3 ObjectCreated event** triggers the Lambda.  
3. Lambda uses **AWS Rekognition** to detect faces.  
4. Each face is compared against the **reference collection** (`collectionalert`).  
5. If a face does **not match**:
   - An **SNS alert** is sent to notify admins.  
   - The face is indexed into the **UnknownFaces collection**.  
6. Known faces are logged, but no alert is sent.  

---

## ☁️ AWS Services Used

| Service              | Role in Project |
|----------------------|-----------------|
| **Amazon S3**        | Stores reference and uploaded images |
| **AWS Lambda**       | Core processing logic for face detection and alerts |
| **Amazon Rekognition** | Detects and compares faces |
| **Amazon SNS**       | Sends email/SMS alerts for unknown faces |
| **CloudWatch Logs**  | Monitors Lambda execution details |

---

## ⚙️ Setup Instructions

### 1️⃣ Create S3 Buckets
- **Reference Images Bucket** → `collectionalert-bucket`  
  Stores known face images.  
- **Upload Bucket** → `facealert`  
  Stores new images to be checked.  

---

### 2️⃣ Create Rekognition Collections
- **Reference Collection** → `collectionalert`  
- **Unknown Faces Collection** → `UnknownFaces`  

> You can create them via AWS Console or programmatically in Lambda.

---

### 3️⃣ Index Reference Faces
Upload known images to `collectionalert-bucket` and index them:

``python
rek.index_faces(
    CollectionId='collectionalert',
    Image={'S3Object': {'Bucket': 'collectionalert-bucket', 'Name': 'known.jpg'}},
    ExternalImageId='known-person',
    DetectionAttributes=['DEFAULT']
)

Repeat for all known images.

4️⃣ Create SNS Topic:

Create an SNS topic → e.g., FaceAlertTopic
Subscribe an email or SMS endpoint.

Set SNS topic ARN as Lambda environment variable:

ruby
Copy
Edit
SNS_TOPIC_ARN = arn:aws:sns:<region>:<account-id>:FaceAlertTopic

5️⃣ Configure Lambda Function
Runtime: Python 3.12
Handler: lambda_function.lambda_handler
Environment Variables:
SNS_TOPIC_ARN → ARN of SNS topic
SIMILARITY_THRESHOLD → (optional, default 80)

IAM Role Permissions:
rekognition:CreateCollection
rekognition:IndexFaces
rekognition:SearchFacesByImage
rekognition:DetectFaces
s3:GetObject (for both buckets)
sns:Publish

S3 Trigger:

Bucket → facealert
Event → All object create events

6️⃣ Testing
Upload Test
Upload an image to facealert bucket.

Check Logs
Review CloudWatch Logs for Lambda execution details.

SNS Alert
If face is unknown, SNS notification will be triggered.

7️⃣ Notes
Similarity Threshold → Lower to ~70 if recognition fails for known faces.

Image Quality → Clear, frontal faces work best.

Unknown Face Tracking → All unknown faces are indexed in UnknownFaces collection.

✅ System ready for real-time face monitoring and alerts using AWS Rekognition.

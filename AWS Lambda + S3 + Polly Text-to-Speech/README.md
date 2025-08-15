This project converts text files stored in Amazon S3 into audio files using Amazon Polly.  
No Comprehend is used — only Lambda, S3, and Polly.

---

📂 Project Structure

We use two S3 folders (prefixes):

- **input/** — Place .txt files here for processing.
- **audio/** — Generated .mp3 files are saved here.

---

⚙ How It Works

1. Upload a .txt file to the input/ folder in S3.
2. An *S3 event trigger* invokes an AWS Lambda function.
3. Lambda:
   - Reads the text from the file.
   - Sends the text to *Amazon Polly*.
   - Receives the audio output (MP3 format).
   - Saves the MP3 file to the audio/ folder in the same S3 bucket.
4. The audio file can then be downloaded or streamed.

🛠 Setup Steps

1. Create an S3 Bucket
- Create a new bucket (e.g., my-tts-bucket).
- Inside the bucket, create two folders:  
  - input/  
  - audio/

2. Create the Lambda Function
- Runtime: *Python 3.x* (or Node.js if preferred).
- Attach the following IAM permissions:
  - AmazonS3ReadOnlyAccess (read input files)
  - AmazonS3FullAccess (to write audio files — or restrict to audio/ prefix)
  - AmazonPollyFullAccess (to synthesize speech)
- Add an *S3 trigger* for the input/ folder (.txt files only).

3. Paste Lambda Code 
📷 Example Workflow
	1.	Upload: input/example.txt
	2.	Lambda → Polly → Save
	3.	Output: audio/example.mp3 in S3

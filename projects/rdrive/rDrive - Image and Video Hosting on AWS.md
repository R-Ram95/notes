---
id: rDrive - Image and Video Hosting on AWS
aliases: []
tags:
  - Project-Notes
---


 [!important]  
 **Goal**  
 Create a frontend with auth that:  
 - Allows only 2 users  
 - Allows uploading and viewing images and videos  
 - UI for downloading images and videos  
 
 Create a backend with endpoints for:  
 - Auth  
 - Get images and videos  
 - Download images and videos  
 - Upload images and videos  
 
 Proposed backend architecture:  
 - CloudFront + S3 for frontend  
 - API Gateway + Lambda + S3 + DynamoDB for backend  
 
 Alternative backend:  
 - ECS + S3 + DynamoDB

 [!important]  
 **Preliminary Info**  
 - Size of files: 4.1 GB (budget for 10 GB)  
 - Metadata store on DynamoDB: ~1 GB  
   - Image name  
   - Folder path  
   - Timestamp  
   - Size

 [!important]  
 **Architecture**  
 ![[/image 2.png|image 2.png]]  
 
 **Flow Summary**  
 1. The user accesses the web app and requests resources via API Gateway.  
 2. API Gateway routes requests to Lambda functions.  
 3. These functions interact with S3 (file storage) and DynamoDB (metadata storage).  
 4. Lambda Authorizer ensures only authenticated users interact with backend services.  
 
 **Architecture Explanation**  
 
 - **Frontend Layer**  
   - Users can: log in, upload, view, and download images  
 - **CloudFront**  
   - Delivers frontend artifacts (JS Bundle) to the user  
 - **S3**  
   - Stores frontend artifacts  
 
 **Backend Layer**  
 - **API Gateway**  
   - Routes client API requests to appropriate Lambda functions  
 - **Lambda**  
   - Contains logic for backend routes  
   - Image route: get, upload, rename, move images  
   - Video route: get (streaming??), upload, rename, move videos  
   - Auth route: generates JWT based on username + password  
 - **DynamoDB**  
   - Stores metadata about images and videos  
   - Stores username, hashed password, session tokens  
 - **S3**  
   - Stores the actual images and videos  
 
 **Cost**  
 
 **S3**  
 - Storage: $0.023/GB for 10 GB ⇒ $0.23/month  
 - Retrieval: $0.0004 per 1,000 GET requests for 10,000 requests ⇒ $4.00/month  
 - Data Transfer: $0.09/GB for 5 GB ⇒ $0.45/month  
 
 **CloudFront**  
 - Data Transfer: $0.085/GB for 5 GB ⇒ $0.425/month  
 - Requests: $0.0075 per 10,000 HTTP/HTTPS requests for 10,000 requests ⇒ $0.075/month  
 
 **API Gateway**  
 - REST API Calls: $3.50 per million requests for 100,000 requests ⇒ $0.35/month  
 
 **Lambda**  
 - Free Tier (100,000 executions free)  
 - Compute Time: well within the free tier  
 
 **DynamoDB**  
 - Storage: $0.25/GB for 1 GB ⇒ $0.25/month  
 - Writes/Reads: negligible costs  
 
 **Route 53**  
 - Hosted Zone: $0.50/month  
 
 **ACM**  
 - Free for CloudFront/API Gateway  
 
 **Total Monthly Estimate**: $6.28 USD/month  
 
 **Notes**: Clerk's free tier sessions expire every 60 seconds, so we may need to run auth lambda every minute which could become costly. Unsure how to use Clerk with custom domains.

 [!important]  
 **DynamoDB Table Design**  
 Stores metadata attributes of images and videos:  
 - Uploader ID (string)  
 - Date Uploaded (string)  
 - File Type (string)  
 - File Size (number)  
 - Folder Path (Partition Key)  
 - File Name (Sort Key)  
 - Thumbnail URL (string)  
 
 **Considerations**  
 - Create LSI for file type?  
 - Dummy record for empty folders.  
 
 **Access Pattern**  
 1. User requests files in a folder.  
 2. Lambda queries DynamoDB using Folder Path as partition key.  
 3. Lambda generates signed URLs for S3 files.  
 4. Return URLs to the client.  
 
 **Example Flow**  
 - Client requests files in `/spain2024`.  
 - Lambda queries DynamoDB and retrieves metadata.  
 - Lambda generates signed URLs for files.  
 - Client uses URLs to download/view assets.  
 
 **Benefits**: Secure, scalable, efficient.  
 **Considerations**: URL expiration time, concurrent access, Lambda costs.

 [!important]  
 **Resources**  
 - [Supertokens - Email Password Auth](https://supertokens.com/features/email-password-authentication)  
 - [AWS Lambda Authorizer Guide](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html)  


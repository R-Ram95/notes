---
tags:
  - Project-Notes
---
> [!important]  
> TODO List Backend uploading images getting images delete images modify images uploading videos getting videos deleting videos modifying videos Frontend: make login screen make image viewer screen make video player  

  

> [!important]  
> GoalGoal:Create an FE with auth ⇒ only allow access for 2 Usersthat allows me to upload images and videosallows me to view images and videosui that allows me to download images and videosCreate BE with endpointsauthget images and videosdownload images and videosupload images and videosproposed:cloudfront + s3 for feapi gateway + lambda + s3 + dynamoDB for BEAlternative BE: ECS + S3 + dynamoDB  

  

> [!important]  
> Preliminary InfoSize of Files: 4.1 G ⇒ budget for 10GMetadata store on DynamoDB: ~1GBimage namefolder pathtime stampsize  

  

> [!important]  
> Architecture![[/image 2.png|image 2.png]]Flow SummaryThe user accesses the web app and requests specific resources via API Gateway.API Gateway, secured with custom SSL certificates from ACM, routes requests to the appropriate Lambda functions.These functions interact with S3 (for file storage) and DynamoDB (for metadata storage).Lambda Authorizer ensures that only authenticated users can interact with the backend services.Architecture ExplanationArchitecture ExplanationFrontEnd LayerUsers access web app via browser. Users can: login, upload images, view images, download images CloudFrontUsed to deliver FE artifacts (JS Bundle) to the userS3 https://aws.amazon.com/s3/getting-started/?nc=sn&loc=6&dn=1stores FE artifacts (JS bundle)Backend LayerAPI Gateway https://aws.amazon.com/api-gateway/handles incoming API requests from the clientacts as a front door for applications to access data, business logic, or functionality from the BEroutes the requests to the appropriate Lambda functionsLambda (Compute) https://aws.amazon.com/lambda/contains logic for BE/image route + Lambdaget imagesupload imagesrename imagesmove images/videos route + Lambdaget (streaming??) videosupload videosrename vidosmove videos/auth route + Lambdagenerates session token (JWT) based on UName + Passwordused as Authorizer for API Gateway & Authentication for FEDynamoDB (Storage) https://aws.amazon.com/dynamodb/stores metadata about the images and videos stored in S3stores username and hashed password and session tokensS3 (image + video storage) https://aws.amazon.com/s3/getting-started/?nc=sn&loc=6&dn=1name of asset will include the file pathCostCostS3Storage Costs:S3 Standard Storage: $0.023 per GB for the first 50 TBFor 10 GB of images + Videos, this would be:10 GB × $0.023/GB = $0.23/monthData Retrieval:Retrieving data from S3 costs $0.0004 per 1,000 GET requestsAssume 10,000 GET requests in a month:10,000 × $0.0004 = $4.00/monthData Transfer:Data transfer out to the internet costs $0.09 per GB after 1 GB free per monthAssuming you transfer 5 GB out:5 GB × $0.09/GB = $0.45/monthCloudFrontData Transfer to Internet:$0.085 per GB for the first 10 TBAssuming delivery 5 GB of images via CloudFront:5 GB × $0.085/GB = $0.425/monthRequests:$0.0075 per 10,000 HTTP/HTTPS requestsAssume 10,000 requests:10,000 × $0.0075 = $0.075/monthAPI GatewayREST API Calls:$3.50 per million requestsAssume 100,000 API requests per month:100,000 × ($3.50/1,000,000) = $0.35/monthLambdaExecution Costs:First 1 million requests are free, after that $0.20 per million requestsAssume 100,000 Lambda executions - within the free tier:freeCompute Time:400,000 GB-seconds free per monthAssuming each Lambda function runs for 100ms with 128 MB of memory for 100,000 requests:100,000×(128MB×100ms)=1.28GB−seconds This is well within the free tier.DynamoDBStorage Costs:$0.25 per GB per month for storageAssume 1 GB of metadata stored:MetaData Storage = < 1 GB ⇒ 1 GB × $0.25 = $0.25/monthAssumed Metadata Fields:Uploader ID (string, ~36 bytes for a UUID)Date Uploaded (string, ~10 bytes for "YYYY-MM-DD")File Type (string, ~5 bytes for "image" or "video")File Size (number, ~8 bytes for integer)File URL (string, ~100 bytes for the S3 URL)Thumbnail URL (optional, ~100 bytes for a thumbnail URL)Tags/Labels (optional, ~50 bytes if you use tags/labels)Description (optional, varies, but ~200 bytes for a typical user description)Estimated Metadata Size per Record:If we sum the above:Uploader ID: 36 bytesDate Uploaded: 10 bytesFile Type: 5 bytesFile Size: 8 bytesFile URL: 100 bytesThumbnail URL: 100 bytesTags/Labels: 50 bytes (optional)Description: 200 bytes (optional)Total size:Without optional fields: ~259 bytesWith optional fields: ~509 bytesFor 1,000 Images/Videos:Without optional fields: 259 bytes × 1,000 = 259,000 bytes (~0.26 MB)With optional fields: 509 bytes × 1,000 = 509,000 bytes (~0.51 MB)Read/Write Requests:25 WCUs and 25 RCUs are free for on-demand capacity.Write Request Units (WRU): $1.25 per million writesRead Request Units (RRU): $0.25 per million readsAssume 1000 writes and 1000 reads / monthWrite: 1000 requests = 0.001 million writes x $1.25 / 1 million writes = $0.00125 per monthRead: 1000 reads = 0.001 million reads x $0.25 / 1 million reads = $0.00025 per monthRoute 53Hosted Zones:$0.50 per hosted zone per month.Custom domain with SSL:1 hosted zone × $0.50 = $0.50/monthACMACM is free for certificates used with services like CloudFront and API Gateway.Total Monthly Estimate:S3 Storage & Transfer: $4.68CloudFront: $0.50API Gateway: $0.35Lambda: Free (within free tier)DynamoDB: $0.25Route 53: $0.50ACM: FreeTotal = $6.28 USD/ Month = $75.36 USD/ Year  
Notes:in Clerks free tier sessions tokens expire every 60 seconds, this means we would need to run the auth lambda every 60 seconds, when fetching up to 1000 images, this may get costly ⇒ I also don’t really understand how to use clerk on my own custom domain???  
  

  

> [!important]  
> DynamoDB Table Designstores metadata attributes of Image and videoMetaDataUploader ID: stringDate Uploaded : stringFile Type: string File Size: numberFolder Path: string - PARTITION KEYFile name: string - SORT KEYThumbnail URL: stringCreate Global Secondary Index or Local Secondary Index for file type?Folder Path as partition key and File Name as sort key is efficient for querying.Consider an LSI on File Type for more efficient querying within folders by file type.For empty folders, use a dummy record to signify their existence without files.10 RRU and 10 WRUAccess PatternAccess Pattern Overview:User Requests Files (Client-Side):The user navigates to a folder in the web application (e.g., /images, /spain2024).The web application sends a request to your backend (API Gateway + Lambda) to fetch the assets in that folder.Lambda Queries DynamoDB:The Lambda function receives the request and queries DynamoDB using the folder path (partition key) to retrieve all the metadata for files within that folder.The metadata contains the file names and folder path, which correspond to the actual files in the S3 bucket.Lambda Generates Signed URLs:Once the metadata is retrieved, the Lambda function generates pre-signed URLs for the files in S3 using the folder path and file name.The pre-signed URLs allow the client to directly download the files from S3 securely without exposing the S3 bucket to the public.Return Signed URLs to Client:Lambda sends the signed URLs back to the web application through API Gateway.The signed URLs are typically valid for a limited period (e.g., 15 minutes or 1 hour), allowing secure access to the S3 assets without requiring further authentication.Client Uses Signed URLs:The web application receives the signed URLs and uses them to download or display the assets (images, videos) directly from S3.Detailed Example of the Flow:Client Request to Lambda:Web app sends a request to the API Gateway for assets in folder /spain2024.The backend receives the request and triggers a Lambda function to query DynamoDB.DynamoDB Query:Lambda queries DynamoDB for all items with the partition key Folder Path = /spain2024.DynamoDB returns a list of files, each with a corresponding file name and metadata.Generate Signed URLs:For each file, Lambda uses the AWS SDK to create a pre-signed URL using the folder path and file name:javascript  
Copy code  
const url = s3.getSignedUrl('getObject', {  
Bucket: 'your-s3-bucket-name',  
Key: `${folderPath}/${fileName}`,  
Expires: 3600 // URL valid for 1 hour  
});  
  
Lambda generates a pre-signed URL for each file in the folder.Return URLs:Lambda returns a JSON response to the client:json  
Copy code  
{  
"files": [  
{  
"fileName": "image1.jpg",  
"signedUrl": "https://s3.amazonaws.com/your-bucket/spain2024/image1.jpg?AWSAccessKeyId=...&Expires=..."  
},  
{  
"fileName": "video1.mp4",  
"signedUrl": "https://s3.amazonaws.com/your-bucket/spain2024/video1.mp4?AWSAccessKeyId=...&Expires=..."  
}  
]  
}  
  
Client Displays Assets:The web application uses the signed URLs to either:Render images or play videos directly.Download the files if needed.Benefits of This Approach:Security: Pre-signed URLs ensure that files in S3 are not publicly accessible unless authorized. The URL expires after a set period, adding another layer of security.Scalability: DynamoDB is highly scalable for storing and querying metadata, while S3 is optimized for storing large files.Efficiency: The client only interacts with S3 via pre-signed URLs, minimizing backend processing overhead after the signed URLs are generated.Considerations:Expiration Time: You’ll need to decide how long the signed URLs should remain valid, balancing between security and user experience.Concurrent Access: If multiple users access the same asset at the same time, pre-signed URLs may need to be regenerated (or cached) frequently, depending on how quickly they expire.Lambda Costs: Generating signed URLs and querying DynamoDB incurs Lambda execution time, but for a small user base, this should remain low.Summary:DynamoDB stores metadata (folder path, file name, etc.).S3 stores the actual assets (images, videos).Lambda queries DynamoDB for metadata, generates pre-signed URLs for S3 assets, and sends those URLs to the client.The web application then uses the pre-signed URLs to fetch and display the assets directly from S3.This pattern is efficient and secure for serving images and videos from S3, while keeping file metadata in DynamoDB.  

  

  

> [!important]  
> Resourceshttps://supertokens.com/features/email-password-authentication https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.htmlhttps://blog.focusotter.com/the-complete-guide-to-integrating-clerk-with-an-aws-backendhttps://www.talkncloud.com/aws-cognito-the-good-the-bad-the-ugly/https://blog.taskli.st/posts/setting-up-supertokens-with-awshttps://stackoverflow.com/questions/65986897/how-to-provide-a-custom-domain-name-for-a-lambda-based-apigateway-in-cdkhttps://exanubes.com/blog/create-api-gateway-with-custom-domain  
  

  

> [!important]  
> Multiple Imagesconst AWS = require('aws-sdk');  
const s3 = new AWS.S3();  
  
exports.handler = async (event) => {  
const { images } = JSON.parse(event.body);  
const presignedUrls = await Promise.all(images.map(image => {  
const params = {  
Bucket: 'your-bucket-name',  
Key: `uploads/${image.name}`,  
Expires: 60 * 5, // URL valid for 5 minutes  
ContentType: image.type,  
};  
return s3.getSignedUrlPromise('putObject', params);  
}));  
  
return {  
statusCode: 200,  
body: JSON.stringify(presignedUrls),  
};  
};  
exports.handler = async (event) => {  
const { images } = JSON.parse(event.body);  
const presignedUrls = await Promise.all(images.map(image => {  
const params = {  
Bucket: 'your-bucket-name',  
Key: `uploads/${image}`,  
Expires: 60 * 5, // URL valid for 5 minutes  
};  
return s3.getSignedUrlPromise('getObject', params);  
}));  
  
return {  
statusCode: 200,  
body: JSON.stringify(presignedUrls),  
};  
};  
  

  

> [!important]  
> APISRetrieve Images: Get a Single ImageMethod: GETEndpoint: /api/images/{imageId}Description: Retrieves details of a specific image. Get Multiple ImagesMethod: GETEndpoint: /api/imagesQuery Parameters: Optionally, you can add pagination and filtering parameters (e.g., ?page=1&limit=10).Description: Retrieves a list of images, possibly with pagination. Upload a Single ImageMethod: POSTEndpoint: /api/imagesRequest Body: Use multipart/form-data to send the image file.Description: Uploads a single image and returns its metadata. Upload Multiple ImagesMethod: POSTEndpoint: /api/images/batchRequest Body: Use multipart/form-data to send multiple image files.Description: Uploads multiple images and returns metadata for eac Download a Single ImageMethod: GETEndpoint: /api/images/{imageId}/downloadDescription: Returns the image file for download. The Content-Disposition header can be set to prompt download. Download Multiple ImagesMethod: GETEndpoint: /api/images/downloadQuery Parameters: ?ids=imageId1,imageId2,... (comma-separated)Description: Returns multiple images. This could be handled by creating a ZIP file or providing links for each.
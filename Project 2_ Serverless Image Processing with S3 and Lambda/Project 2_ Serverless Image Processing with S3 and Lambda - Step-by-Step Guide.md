# Project 2: Serverless Image Processing with S3 and Lambda - Step-by-Step Guide

This guide provides detailed instructions for creating a serverless image processing application where users upload images to an S3 bucket, triggering an AWS Lambda function that processes and resizes the images before storing them in another S3 bucket.

## Project Overview

**Architecture:** Serverless

### Architecture Diagram

![Project 2 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537172_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QyX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcxNzJfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNReVgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=vOQZUQJe9XGiBGx4ifULeq~3fE8ZTmcsuSV~Tlg4gMgoSPQQvDOogzWS6cGVLfhcMNMFFdU6oV~evm3PRvqA83tEk39OTXj1gAhJSc2joAFuBl-ACQriweZQ-zeaOePR3A~vEJdKexgOla9znGHVtZY0h9NRkNECUzZyfDPvQrxGprorBCAxT0A89HvcNd~eqtf5SgDSJkb874HMr7H4CacL7Z3Ezdpsv~YP1hzv2TyOg0aXCboUPjmqme1WFoqCIsu2KmgIPXkeQULn94xrQfAJ6rQBA~QkE51xsGJSCrx-yE6wT1xIRY7F6B9iHzAjgw9pGF4eMI1EiAigxNhHvA__)



**Key AWS Services Used:**
*   **Amazon S3:** Object storage service for storing original and processed images.
*   **AWS Lambda:** Serverless compute service that executes image processing code in response to S3 events.
*   **Amazon API Gateway (Optional):** Fully managed service for creating, publishing, and managing APIs to expose upload endpoints.
*   **Amazon DynamoDB (Optional):** NoSQL database service for storing metadata about uploaded images.
*   **AWS Step Functions (Optional):** Serverless orchestration service for handling complex workflows.
*   **IAM (Identity and Access Management):** Manages access to AWS services and resources securely.
*   **CloudWatch:** Monitoring and logging service for Lambda functions and API Gateway.

## Step-by-Step Instructions

### Phase 1: Prepare your AWS Environment

1.  **Create an AWS Account:** If you don't have one, sign up for a free tier account at [aws.amazon.com](https://aws.amazon.com/).
2.  **Choose a Region:** Select an AWS region close to your geographical location for lower latency (e.g., `us-east-1` for N. Virginia).
3.  **Create an IAM User (Best Practice):**
    *   Navigate to the IAM dashboard.
    *   Create a new IAM user with programmatic access and AWS Management Console access.
    *   Attach the `AdministratorAccess` policy for simplicity in this project (in a real-world scenario, use least privilege).
    *   Save the Access Key ID and Secret Access Key (for programmatic access) and the login URL.

### Phase 2: Create S3 Buckets

1.  **Create Source S3 Bucket (for original images):**
    *   Navigate to the S3 dashboard.
    *   Click 'Create bucket'.
    *   Bucket name: `my-image-upload-bucket-[unique-suffix]` (S3 bucket names must be globally unique).
    *   Region: Select your preferred region.
    *   Block Public Access settings: Keep default (block all public access for security).
    *   Bucket Versioning: Enable (optional, for backup purposes).
    *   Default encryption: Enable with Amazon S3 managed keys (SSE-S3).
    *   Click 'Create bucket'.

2.  **Create Destination S3 Bucket (for processed images):**
    *   Click 'Create bucket'.
    *   Bucket name: `my-processed-images-bucket-[unique-suffix]`.
    *   Region: Same as the source bucket.
    *   Block Public Access settings: You may want to allow public read access if you plan to serve processed images directly from S3. For this project, keep it blocked for security.
    *   Bucket Versioning: Enable (optional).
    *   Default encryption: Enable with Amazon S3 managed keys (SSE-S3).
    *   Click 'Create bucket'.

### Phase 3: Create IAM Role for Lambda Function

1.  **Create Lambda Execution Role:**
    *   Navigate to the IAM dashboard.
    *   Click 'Roles' in the left sidebar.
    *   Click 'Create role'.
    *   Select 'AWS service' as the trusted entity type.
    *   Choose 'Lambda' as the use case.
    *   Click 'Next: Permissions'.

2.  **Attach Policies:**
    *   Search for and attach the following managed policies:
        *   `AWSLambdaBasicExecutionRole` (for CloudWatch Logs access).
        *   `AmazonS3FullAccess` (for S3 read/write access - in production, use more restrictive policies).
    *   Alternatively, create a custom policy with specific permissions:
        ```json
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": [
                        "logs:CreateLogGroup",
                        "logs:CreateLogStream",
                        "logs:PutLogEvents"
                    ],
                    "Resource": "arn:aws:logs:*:*:*"
                },
                {
                    "Effect": "Allow",
                    "Action": [
                        "s3:GetObject",
                        "s3:PutObject"
                    ],
                    "Resource": [
                        "arn:aws:s3:::my-image-upload-bucket-[unique-suffix]/*",
                        "arn:aws:s3:::my-processed-images-bucket-[unique-suffix]/*"
                    ]
                }
            ]
        }
        ```
    *   Click 'Next: Tags' (optional).
    *   Click 'Next: Review'.
    *   Role name: `lambda-image-processing-role`.
    *   Click 'Create role'.

### Phase 4: Create Lambda Function for Image Processing

1.  **Create Lambda Function:**
    *   Navigate to the Lambda dashboard.
    *   Click 'Create function'.
    *   Choose 'Author from scratch'.
    *   Function name: `image-processing-function`.
    *   Runtime: `Python 3.9` (or latest available).
    *   Architecture: `x86_64`.
    *   Execution role: Choose 'Use an existing role' and select `lambda-image-processing-role`.
    *   Click 'Create function'.

2.  **Write Lambda Function Code:**
    *   In the Lambda function console, replace the default code with the following Python code:

    ```python
    import json
    import boto3
    from PIL import Image
    import io
    import os

    s3_client = boto3.client('s3')

    def lambda_handler(event, context):
        # Get the bucket and object key from the S3 event
        source_bucket = event['Records'][0]['s3']['bucket']['name']
        object_key = event['Records'][0]['s3']['object']['key']
        
        # Define the destination bucket
        destination_bucket = os.environ['DESTINATION_BUCKET']
        
        try:
            # Download the image from S3
            response = s3_client.get_object(Bucket=source_bucket, Key=object_key)
            image_data = response['Body'].read()
            
            # Open the image using PIL
            image = Image.open(io.BytesIO(image_data))
            
            # Resize the image (example: 300x300 pixels)
            resized_image = image.resize((300, 300), Image.LANCZOS)
            
            # Convert the image back to bytes
            output_buffer = io.BytesIO()
            
            # Determine the format based on the original file extension
            file_extension = object_key.split('.')[-1].lower()
            if file_extension in ['jpg', 'jpeg']:
                resized_image.save(output_buffer, format='JPEG')
                content_type = 'image/jpeg'
            elif file_extension == 'png':
                resized_image.save(output_buffer, format='PNG')
                content_type = 'image/png'
            else:
                # Default to JPEG for unsupported formats
                resized_image.save(output_buffer, format='JPEG')
                content_type = 'image/jpeg'
                object_key = object_key.rsplit('.', 1)[0] + '.jpg'
            
            output_buffer.seek(0)
            
            # Upload the processed image to the destination bucket
            processed_key = f"processed-{object_key}"
            s3_client.put_object(
                Bucket=destination_bucket,
                Key=processed_key,
                Body=output_buffer.getvalue(),
                ContentType=content_type
            )
            
            print(f"Successfully processed {object_key} and saved as {processed_key}")
            
            return {
                'statusCode': 200,
                'body': json.dumps({
                    'message': 'Image processed successfully',
                    'processed_image': f"s3://{destination_bucket}/{processed_key}"
                })
            }
            
        except Exception as e:
            print(f"Error processing image {object_key}: {str(e)}")
            return {
                'statusCode': 500,
                'body': json.dumps({
                    'error': str(e)
                })
            }
    ```

3.  **Configure Environment Variables:**
    *   In the Lambda function console, scroll down to 'Environment variables'.
    *   Click 'Edit'.
    *   Add a new environment variable:
        *   Key: `DESTINATION_BUCKET`
        *   Value: `my-processed-images-bucket-[your-unique-suffix]`
    *   Click 'Save'.

4.  **Add PIL (Pillow) Library:**
    *   Since PIL is not included in the default Lambda runtime, you need to add it as a layer or package it with your function.
    *   **Option 1: Use a Lambda Layer (Recommended):**
        *   In the Lambda function console, scroll down to 'Layers'.
        *   Click 'Add a layer'.
        *   Choose 'AWS layers' or 'Custom layers'.
        *   Search for a public Pillow layer or create your own.
        *   For simplicity, you can use a public layer like `arn:aws:lambda:us-east-1:770693421928:layer:Klayers-p39-Pillow:1` (check for the latest version and your region).
    *   **Option 2: Package Dependencies:**
        *   Create a deployment package locally with Pillow included and upload it as a ZIP file.

### Phase 5: Configure S3 Event Trigger

1.  **Set up S3 Event Notification:**
    *   Navigate to the S3 dashboard.
    *   Click on your source bucket (`my-image-upload-bucket-[unique-suffix]`).
    *   Go to the 'Properties' tab.
    *   Scroll down to 'Event notifications'.
    *   Click 'Create event notification'.

2.  **Configure Event Notification:**
    *   Event name: `image-upload-trigger`.
    *   Prefix: Leave empty (to trigger on all objects) or specify a prefix like `uploads/`.
    *   Suffix: `.jpg,.jpeg,.png` (to trigger only on image files).
    *   Event types: Select `s3:ObjectCreated:*` (all object creation events).
    *   Destination: Choose 'Lambda function'.
    *   Lambda function: Select `image-processing-function`.
    *   Click 'Save changes'.

### Phase 6: (Optional) Create API Gateway for Image Upload

1.  **Create REST API:**
    *   Navigate to the API Gateway dashboard.
    *   Click 'Create API'.
    *   Choose 'REST API' and click 'Build'.
    *   API name: `image-upload-api`.
    *   Description: `API for uploading images to S3`.
    *   Endpoint Type: Regional.
    *   Click 'Create API'.

2.  **Create Resource and Method:**
    *   In the API Gateway console, click 'Actions' > 'Create Resource'.
    *   Resource Name: `upload`.
    *   Resource Path: `/upload`.
    *   Click 'Create Resource'.
    *   Select the `/upload` resource and click 'Actions' > 'Create Method'.
    *   Choose 'POST' from the dropdown and click the checkmark.

3.  **Configure Method Integration:**
    *   Integration type: AWS Service.
    *   AWS Region: Your region.
    *   AWS Service: Simple Storage Service (S3).
    *   HTTP method: PUT.
    *   Action Type: Use path override.
    *   Path override: `{bucket}/{key}` (where bucket and key will be provided in the request).
    *   Execution role: Create a new IAM role with S3 PutObject permissions.
    *   Click 'Save'.

4.  **Deploy API:**
    *   Click 'Actions' > 'Deploy API'.
    *   Deployment stage: Create a new stage named `prod`.
    *   Click 'Deploy'.
    *   Note the Invoke URL for testing.

### Phase 7: (Optional) Add DynamoDB for Metadata Storage

1.  **Create DynamoDB Table:**
    *   Navigate to the DynamoDB dashboard.
    *   Click 'Create table'.
    *   Table name: `image-metadata`.
    *   Partition key: `image_id` (String).
    *   Use default settings for other options.
    *   Click 'Create table'.

2.  **Update Lambda Function to Store Metadata:**
    *   Add DynamoDB permissions to the Lambda execution role.
    *   Update the Lambda function code to store metadata:

    ```python
    import json
    import boto3
    from PIL import Image
    import io
    import os
    import uuid
    from datetime import datetime

    s3_client = boto3.client('s3')
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('image-metadata')

    def lambda_handler(event, context):
        # Get the bucket and object key from the S3 event
        source_bucket = event['Records'][0]['s3']['bucket']['name']
        object_key = event['Records'][0]['s3']['object']['key']
        
        # Define the destination bucket
        destination_bucket = os.environ['DESTINATION_BUCKET']
        
        try:
            # Download the image from S3
            response = s3_client.get_object(Bucket=source_bucket, Key=object_key)
            image_data = response['Body'].read()
            
            # Open the image using PIL
            image = Image.open(io.BytesIO(image_data))
            original_size = image.size
            
            # Resize the image (example: 300x300 pixels)
            resized_image = image.resize((300, 300), Image.LANCZOS)
            
            # Convert the image back to bytes
            output_buffer = io.BytesIO()
            
            # Determine the format based on the original file extension
            file_extension = object_key.split('.')[-1].lower()
            if file_extension in ['jpg', 'jpeg']:
                resized_image.save(output_buffer, format='JPEG')
                content_type = 'image/jpeg'
            elif file_extension == 'png':
                resized_image.save(output_buffer, format='PNG')
                content_type = 'image/png'
            else:
                # Default to JPEG for unsupported formats
                resized_image.save(output_buffer, format='JPEG')
                content_type = 'image/jpeg'
                object_key = object_key.rsplit('.', 1)[0] + '.jpg'
            
            output_buffer.seek(0)
            
            # Upload the processed image to the destination bucket
            processed_key = f"processed-{object_key}"
            s3_client.put_object(
                Bucket=destination_bucket,
                Key=processed_key,
                Body=output_buffer.getvalue(),
                ContentType=content_type
            )
            
            # Store metadata in DynamoDB
            image_id = str(uuid.uuid4())
            table.put_item(
                Item={
                    'image_id': image_id,
                    'original_key': object_key,
                    'processed_key': processed_key,
                    'original_size': f"{original_size[0]}x{original_size[1]}",
                    'processed_size': "300x300",
                    'upload_time': datetime.utcnow().isoformat(),
                    'source_bucket': source_bucket,
                    'destination_bucket': destination_bucket
                }
            )
            
            print(f"Successfully processed {object_key} and saved as {processed_key}")
            
            return {
                'statusCode': 200,
                'body': json.dumps({
                    'message': 'Image processed successfully',
                    'image_id': image_id,
                    'processed_image': f"s3://{destination_bucket}/{processed_key}"
                })
            }
            
        except Exception as e:
            print(f"Error processing image {object_key}: {str(e)}")
            return {
                'statusCode': 500,
                'body': json.dumps({
                    'error': str(e)
                })
            }
    ```

### Phase 8: Testing the Application

1.  **Test Image Upload:**
    *   Navigate to your source S3 bucket.
    *   Click 'Upload'.
    *   Select an image file (JPEG or PNG).
    *   Click 'Upload'.

2.  **Verify Processing:**
    *   Check the CloudWatch Logs for your Lambda function to see if it executed successfully.
    *   Navigate to your destination S3 bucket to verify that the processed image was created.
    *   If you implemented DynamoDB, check the table for the metadata entry.

3.  **Test API Gateway (if implemented):**
    *   Use a tool like Postman or curl to test the API endpoint.
    *   Send a POST request to the API Gateway URL with an image file.

### Phase 9: Monitoring and Optimization

1.  **CloudWatch Monitoring:**
    *   Navigate to the CloudWatch dashboard.
    *   Check Lambda function metrics (invocations, duration, errors).
    *   Set up alarms for error rates or execution duration.

2.  **Performance Optimization:**
    *   Adjust Lambda function memory allocation based on processing requirements.
    *   Consider using Lambda layers for common dependencies.
    *   Implement error handling and retry logic.

### Phase 10: Clean Up Resources

1.  **Delete Lambda Function:** Delete the `image-processing-function`.
2.  **Delete S3 Buckets:** Empty and delete both S3 buckets (source and destination).
3.  **Delete DynamoDB Table:** Delete the `image-metadata` table (if created).
4.  **Delete API Gateway:** Delete the `image-upload-api` (if created).
5.  **Delete IAM Role:** Delete the `lambda-image-processing-role`.
6.  **Delete CloudWatch Logs:** Delete the log groups created by Lambda.

This comprehensive guide should help you successfully implement Project 2. The serverless architecture provides automatic scaling, cost efficiency, and high availability without managing servers.


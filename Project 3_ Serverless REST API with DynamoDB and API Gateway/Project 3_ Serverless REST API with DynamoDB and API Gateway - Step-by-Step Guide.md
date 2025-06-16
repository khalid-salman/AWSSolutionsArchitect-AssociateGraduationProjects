# Project 3: Serverless REST API with DynamoDB and API Gateway - Step-by-Step Guide

This guide provides detailed instructions for developing a serverless REST API using Amazon API Gateway, AWS Lambda, and DynamoDB to manage a simple to-do list or customer records. The API allows users to create, read, update, and delete (CRUD) data without managing servers.

## Project Overview

**Architecture:** Serverless

### Architecture Diagram

![Project 3 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537224_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QzX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcyMjRfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNRelgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=INc7wiL3jaWqPjEkt4nLIa2bDXB~kV2IIMg0v9wJ3RvrrM4sacGkWl6Qd5yzvFpl2CQTSsvDKWHI3pSiQSiMhlh8awhk62xDquLnZcyklOTo-9ObLXynjBZqcLdUWvlwfufPZhMDD53s7Rgshvm2XOAj0xouU63R4tzS7DAxILTlp5nz-AIFNQnWViWJPcy8OylR2gpSLvjFF4E8FIE8Ewkm~RwDb8xVaj0pNUASE~Bas8-tn9tQHmL4FsFnV7mAojGmtBiiBRjBaE0zg7HVxMicotCPLMMSHIoH2~ynJs1OP9pdMOcdIrt9vfwHirnErRHMYjGf37NWRQrSfWbFeQ__)



**Key AWS Services Used:**
*   **Amazon API Gateway:** Fully managed service for creating, publishing, maintaining, monitoring, and securing REST APIs at any scale.
*   **AWS Lambda:** Serverless compute service that runs code in response to events and automatically manages the underlying compute resources.
*   **Amazon DynamoDB:** Fast and flexible NoSQL database service for applications that need consistent, single-digit millisecond latency at any scale.
*   **AWS IAM (Identity and Access Management):** Controls access to AWS services and resources securely.
*   **Amazon CloudWatch:** Monitoring and observability service for AWS cloud resources and applications.
*   **Amazon S3:** Object storage service to host the frontend of your application (optional).

## Step-by-Step Instructions

### Phase 1: Prepare your AWS Environment

1.  **Create an AWS Account:** If you don't have one, sign up for a free tier account at [aws.amazon.com](https://aws.amazon.com/).
2.  **Choose a Region:** Select an AWS region close to your geographical location for lower latency (e.g., `us-east-1` for N. Virginia).
3.  **Create an IAM User (Best Practice):**
    *   Navigate to the IAM dashboard.
    *   Create a new IAM user with programmatic access and AWS Management Console access.
    *   Attach the `AdministratorAccess` policy for simplicity in this project (in a real-world scenario, use least privilege).
    *   Save the Access Key ID and Secret Access Key (for programmatic access) and the login URL.

### Phase 2: Create DynamoDB Table

1.  **Navigate to DynamoDB:**
    *   Go to the AWS Management Console.
    *   Search for and select "DynamoDB".

2.  **Create Table:**
    *   Click "Create table".
    *   Table name: `TodoItems` (or `CustomerRecords` if building a customer management system).
    *   Partition key: `id` (String) - This will be the unique identifier for each item.
    *   Sort key: Leave empty for this simple example.
    *   Table settings: Use default settings (On-demand billing mode is recommended for development).
    *   Click "Create table".

3.  **Table Schema for Todo List:**
    ```json
    {
        "id": "unique-uuid",
        "title": "Task title",
        "description": "Task description",
        "completed": false,
        "createdAt": "2023-12-01T10:00:00Z",
        "updatedAt": "2023-12-01T10:00:00Z"
    }
    ```

4.  **Alternative Schema for Customer Records:**
    ```json
    {
        "id": "unique-uuid",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@example.com",
        "phone": "+1234567890",
        "address": "123 Main St, City, State",
        "createdAt": "2023-12-01T10:00:00Z",
        "updatedAt": "2023-12-01T10:00:00Z"
    }
    ```

### Phase 3: Create IAM Role for Lambda Functions

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
        *   `AmazonDynamoDBFullAccess` (for DynamoDB read/write access - in production, use more restrictive policies).
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
                        "dynamodb:GetItem",
                        "dynamodb:PutItem",
                        "dynamodb:UpdateItem",
                        "dynamodb:DeleteItem",
                        "dynamodb:Scan",
                        "dynamodb:Query"
                    ],
                    "Resource": "arn:aws:dynamodb:*:*:table/TodoItems"
                }
            ]
        }
        ```
    *   Click 'Next: Tags' (optional).
    *   Click 'Next: Review'.
    *   Role name: `lambda-dynamodb-role`.
    *   Click 'Create role'.

### Phase 4: Create Lambda Functions for CRUD Operations

You'll create separate Lambda functions for each CRUD operation, or a single function that handles all operations based on the HTTP method.

#### Option 1: Single Lambda Function (Recommended)

1.  **Create Lambda Function:**
    *   Navigate to the Lambda dashboard.
    *   Click 'Create function'.
    *   Choose 'Author from scratch'.
    *   Function name: `todo-api-handler`.
    *   Runtime: `Python 3.9` (or latest available).
    *   Architecture: `x86_64`.
    *   Execution role: Choose 'Use an existing role' and select `lambda-dynamodb-role`.
    *   Click 'Create function'.

2.  **Write Lambda Function Code:**
    *   Replace the default code with the following Python code:

    ```python
    import json
    import boto3
    import uuid
    from datetime import datetime
    from decimal import Decimal

    # Initialize DynamoDB resource
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('TodoItems')

    def lambda_handler(event, context):
        # Get HTTP method and path
        http_method = event['httpMethod']
        path = event['path']
        
        # Handle CORS preflight requests
        if http_method == 'OPTIONS':
            return {
                'statusCode': 200,
                'headers': {
                    'Access-Control-Allow-Origin': '*',
                    'Access-Control-Allow-Headers': 'Content-Type',
                    'Access-Control-Allow-Methods': 'GET,POST,PUT,DELETE,OPTIONS'
                },
                'body': ''
            }
        
        try:
            if http_method == 'GET':
                if path == '/todos':
                    # Get all todos
                    response = table.scan()
                    items = response['Items']
                    
                    # Convert Decimal to float for JSON serialization
                    items = json.loads(json.dumps(items, default=decimal_default))
                    
                    return {
                        'statusCode': 200,
                        'headers': {
                            'Access-Control-Allow-Origin': '*',
                            'Content-Type': 'application/json'
                        },
                        'body': json.dumps({
                            'items': items,
                            'count': len(items)
                        })
                    }
                elif path.startswith('/todos/'):
                    # Get specific todo by ID
                    todo_id = path.split('/')[-1]
                    response = table.get_item(Key={'id': todo_id})
                    
                    if 'Item' in response:
                        item = json.loads(json.dumps(response['Item'], default=decimal_default))
                        return {
                            'statusCode': 200,
                            'headers': {
                                'Access-Control-Allow-Origin': '*',
                                'Content-Type': 'application/json'
                            },
                            'body': json.dumps(item)
                        }
                    else:
                        return {
                            'statusCode': 404,
                            'headers': {'Access-Control-Allow-Origin': '*'},
                            'body': json.dumps({'error': 'Todo not found'})
                        }
            
            elif http_method == 'POST':
                # Create new todo
                body = json.loads(event['body'])
                
                todo_id = str(uuid.uuid4())
                timestamp = datetime.utcnow().isoformat() + 'Z'
                
                item = {
                    'id': todo_id,
                    'title': body.get('title', ''),
                    'description': body.get('description', ''),
                    'completed': body.get('completed', False),
                    'createdAt': timestamp,
                    'updatedAt': timestamp
                }
                
                table.put_item(Item=item)
                
                return {
                    'statusCode': 201,
                    'headers': {
                        'Access-Control-Allow-Origin': '*',
                        'Content-Type': 'application/json'
                    },
                    'body': json.dumps(item)
                }
            
            elif http_method == 'PUT':
                # Update existing todo
                todo_id = path.split('/')[-1]
                body = json.loads(event['body'])
                
                timestamp = datetime.utcnow().isoformat() + 'Z'
                
                # Check if item exists
                response = table.get_item(Key={'id': todo_id})
                if 'Item' not in response:
                    return {
                        'statusCode': 404,
                        'headers': {'Access-Control-Allow-Origin': '*'},
                        'body': json.dumps({'error': 'Todo not found'})
                    }
                
                # Update item
                update_expression = "SET "
                expression_attribute_values = {}
                expression_attribute_names = {}
                
                if 'title' in body:
                    update_expression += "#title = :title, "
                    expression_attribute_names['#title'] = 'title'
                    expression_attribute_values[':title'] = body['title']
                
                if 'description' in body:
                    update_expression += "description = :description, "
                    expression_attribute_values[':description'] = body['description']
                
                if 'completed' in body:
                    update_expression += "completed = :completed, "
                    expression_attribute_values[':completed'] = body['completed']
                
                update_expression += "updatedAt = :updatedAt"
                expression_attribute_values[':updatedAt'] = timestamp
                
                response = table.update_item(
                    Key={'id': todo_id},
                    UpdateExpression=update_expression,
                    ExpressionAttributeValues=expression_attribute_values,
                    ExpressionAttributeNames=expression_attribute_names if expression_attribute_names else None,
                    ReturnValues='ALL_NEW'
                )
                
                updated_item = json.loads(json.dumps(response['Attributes'], default=decimal_default))
                
                return {
                    'statusCode': 200,
                    'headers': {
                        'Access-Control-Allow-Origin': '*',
                        'Content-Type': 'application/json'
                    },
                    'body': json.dumps(updated_item)
                }
            
            elif http_method == 'DELETE':
                # Delete todo
                todo_id = path.split('/')[-1]
                
                # Check if item exists
                response = table.get_item(Key={'id': todo_id})
                if 'Item' not in response:
                    return {
                        'statusCode': 404,
                        'headers': {'Access-Control-Allow-Origin': '*'},
                        'body': json.dumps({'error': 'Todo not found'})
                    }
                
                table.delete_item(Key={'id': todo_id})
                
                return {
                    'statusCode': 200,
                    'headers': {'Access-Control-Allow-Origin': '*'},
                    'body': json.dumps({'message': 'Todo deleted successfully'})
                }
            
            else:
                return {
                    'statusCode': 405,
                    'headers': {'Access-Control-Allow-Origin': '*'},
                    'body': json.dumps({'error': 'Method not allowed'})
                }
        
        except Exception as e:
            print(f"Error: {str(e)}")
            return {
                'statusCode': 500,
                'headers': {'Access-Control-Allow-Origin': '*'},
                'body': json.dumps({'error': 'Internal server error'})
            }

    def decimal_default(obj):
        if isinstance(obj, Decimal):
            return float(obj)
        raise TypeError
    ```

### Phase 5: Create API Gateway

1.  **Create REST API:**
    *   Navigate to the API Gateway dashboard.
    *   Click 'Create API'.
    *   Choose 'REST API' and click 'Build'.
    *   API name: `todo-api`.
    *   Description: `Serverless Todo API`.
    *   Endpoint Type: Regional.
    *   Click 'Create API'.

2.  **Create Resources and Methods:**

    **Create /todos resource:**
    *   In the API Gateway console, select the root resource (/).
    *   Click 'Actions' > 'Create Resource'.
    *   Resource Name: `todos`.
    *   Resource Path: `/todos`.
    *   Enable CORS: Check this option.
    *   Click 'Create Resource'.

    **Create GET method for /todos:**
    *   Select the `/todos` resource.
    *   Click 'Actions' > 'Create Method'.
    *   Choose 'GET' from the dropdown and click the checkmark.
    *   Integration type: Lambda Function.
    *   Lambda Region: Your region.
    *   Lambda Function: `todo-api-handler`.
    *   Click 'Save' and 'OK' to give API Gateway permission to invoke your Lambda function.

    **Create POST method for /todos:**
    *   Select the `/todos` resource.
    *   Click 'Actions' > 'Create Method'.
    *   Choose 'POST' from the dropdown and click the checkmark.
    *   Configure the same way as GET method.

    **Create {id} resource:**
    *   Select the `/todos` resource.
    *   Click 'Actions' > 'Create Resource'.
    *   Resource Name: `{id}`.
    *   Resource Path: `/{id}`.
    *   Enable CORS: Check this option.
    *   Click 'Create Resource'.

    **Create GET, PUT, DELETE methods for /todos/{id}:**
    *   Repeat the method creation process for GET, PUT, and DELETE methods on the `{id}` resource.

3.  **Enable CORS:**
    *   For each resource, select it and click 'Actions' > 'Enable CORS'.
    *   Access-Control-Allow-Origin: `*` (or specify your domain).
    *   Access-Control-Allow-Headers: `Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token`.
    *   Access-Control-Allow-Methods: Select all methods you've created.
    *   Click 'Enable CORS and replace existing CORS headers'.

4.  **Deploy API:**
    *   Click 'Actions' > 'Deploy API'.
    *   Deployment stage: Create a new stage named `prod`.
    *   Stage description: `Production stage`.
    *   Click 'Deploy'.
    *   Note the Invoke URL (e.g., `https://your-api-id.execute-api.region.amazonaws.com/prod`).

### Phase 6: Test the API

1.  **Test with curl or Postman:**

    **Create a new todo:**
    ```bash
    curl -X POST https://your-api-id.execute-api.region.amazonaws.com/prod/todos \
      -H "Content-Type: application/json" \
      -d '{
        "title": "Learn AWS",
        "description": "Complete the serverless API project",
        "completed": false
      }'
    ```

    **Get all todos:**
    ```bash
    curl https://your-api-id.execute-api.region.amazonaws.com/prod/todos
    ```

    **Get a specific todo:**
    ```bash
    curl https://your-api-id.execute-api.region.amazonaws.com/prod/todos/{todo-id}
    ```

    **Update a todo:**
    ```bash
    curl -X PUT https://your-api-id.execute-api.region.amazonaws.com/prod/todos/{todo-id} \
      -H "Content-Type: application/json" \
      -d '{
        "title": "Learn AWS - Updated",
        "completed": true
      }'
    ```

    **Delete a todo:**
    ```bash
    curl -X DELETE https://your-api-id.execute-api.region.amazonaws.com/prod/todos/{todo-id}
    ```

### Phase 7: (Optional) Create a Simple Frontend

1.  **Create HTML Frontend:**
    *   Create a simple HTML file with JavaScript to interact with your API:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Todo App</title>
        <style>
            body { font-family: Arial, sans-serif; max-width: 800px; margin: 0 auto; padding: 20px; }
            .todo-item { border: 1px solid #ddd; margin: 10px 0; padding: 10px; border-radius: 5px; }
            .completed { text-decoration: line-through; opacity: 0.6; }
            input, textarea, button { margin: 5px; padding: 8px; }
            button { background: #007bff; color: white; border: none; border-radius: 3px; cursor: pointer; }
            button:hover { background: #0056b3; }
        </style>
    </head>
    <body>
        <h1>Todo App</h1>
        
        <div>
            <h3>Add New Todo</h3>
            <input type="text" id="title" placeholder="Title" required>
            <textarea id="description" placeholder="Description"></textarea>
            <button onclick="createTodo()">Add Todo</button>
        </div>
        
        <div id="todos"></div>
        
        <script>
            const API_URL = 'https://your-api-id.execute-api.region.amazonaws.com/prod';
            
            async function loadTodos() {
                try {
                    const response = await fetch(`${API_URL}/todos`);
                    const data = await response.json();
                    displayTodos(data.items);
                } catch (error) {
                    console.error('Error loading todos:', error);
                }
            }
            
            function displayTodos(todos) {
                const container = document.getElementById('todos');
                container.innerHTML = '<h3>Your Todos</h3>';
                
                todos.forEach(todo => {
                    const todoDiv = document.createElement('div');
                    todoDiv.className = `todo-item ${todo.completed ? 'completed' : ''}`;
                    todoDiv.innerHTML = `
                        <h4>${todo.title}</h4>
                        <p>${todo.description}</p>
                        <p>Created: ${new Date(todo.createdAt).toLocaleString()}</p>
                        <button onclick="toggleComplete('${todo.id}', ${!todo.completed})">
                            ${todo.completed ? 'Mark Incomplete' : 'Mark Complete'}
                        </button>
                        <button onclick="deleteTodo('${todo.id}')">Delete</button>
                    `;
                    container.appendChild(todoDiv);
                });
            }
            
            async function createTodo() {
                const title = document.getElementById('title').value;
                const description = document.getElementById('description').value;
                
                if (!title) {
                    alert('Title is required');
                    return;
                }
                
                try {
                    const response = await fetch(`${API_URL}/todos`, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ title, description, completed: false })
                    });
                    
                    if (response.ok) {
                        document.getElementById('title').value = '';
                        document.getElementById('description').value = '';
                        loadTodos();
                    }
                } catch (error) {
                    console.error('Error creating todo:', error);
                }
            }
            
            async function toggleComplete(id, completed) {
                try {
                    await fetch(`${API_URL}/todos/${id}`, {
                        method: 'PUT',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({ completed })
                    });
                    loadTodos();
                } catch (error) {
                    console.error('Error updating todo:', error);
                }
            }
            
            async function deleteTodo(id) {
                if (confirm('Are you sure you want to delete this todo?')) {
                    try {
                        await fetch(`${API_URL}/todos/${id}`, { method: 'DELETE' });
                        loadTodos();
                    } catch (error) {
                        console.error('Error deleting todo:', error);
                    }
                }
            }
            
            // Load todos when page loads
            loadTodos();
        </script>
    </body>
    </html>
    ```

2.  **Host Frontend on S3:**
    *   Create an S3 bucket for static website hosting.
    *   Upload the HTML file.
    *   Enable static website hosting on the bucket.
    *   Make the bucket publicly readable.
    *   Access your frontend via the S3 website URL.

### Phase 8: Monitoring and Logging

1.  **CloudWatch Monitoring:**
    *   Navigate to the CloudWatch dashboard.
    *   Check Lambda function metrics (invocations, duration, errors).
    *   Monitor API Gateway metrics (requests, latency, errors).
    *   Check DynamoDB metrics (read/write capacity, throttling).

2.  **Set up Alarms:**
    *   Create CloudWatch alarms for:
        *   Lambda function errors > 5%
        *   API Gateway 4xx/5xx errors
        *   DynamoDB throttling events
        *   High response times

3.  **CloudWatch Logs:**
    *   Review Lambda function logs for debugging.
    *   Enable API Gateway access logging.
    *   Set up log retention policies.

### Phase 9: Security Enhancements

1.  **API Authentication (Optional):**
    *   Add API keys for basic authentication.
    *   Implement AWS Cognito for user authentication.
    *   Use IAM roles for service-to-service authentication.

2.  **Input Validation:**
    *   Add input validation in Lambda functions.
    *   Use API Gateway request validation.
    *   Implement rate limiting.

3.  **Security Best Practices:**
    *   Use least privilege IAM policies.
    *   Enable CloudTrail for API auditing.
    *   Implement HTTPS only.
    *   Add request/response logging.

### Phase 10: Clean Up Resources

1.  **Delete API Gateway:** Delete the `todo-api` REST API.
2.  **Delete Lambda Function:** Delete the `todo-api-handler` function.
3.  **Delete DynamoDB Table:** Delete the `TodoItems` table.
4.  **Delete S3 Bucket:** Empty and delete the frontend hosting bucket (if created).
5.  **Delete IAM Role:** Delete the `lambda-dynamodb-role`.
6.  **Delete CloudWatch Logs:** Delete the log groups created by Lambda and API Gateway.

This comprehensive guide should help you successfully implement Project 3. The serverless REST API provides a scalable, cost-effective solution for building modern web applications without managing servers.


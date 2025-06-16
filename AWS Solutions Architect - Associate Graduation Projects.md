# AWS Solutions Architect - Associate Graduation Projects

This repository contains a collection of three comprehensive projects designed to help aspiring AWS Solutions Architects gain hands-on experience with various AWS services and architectural patterns. Each project includes detailed step-by-step instructions, architecture diagrams, and presentations to guide you through the implementation.

## Table of Contents

1.  [Project 1: Scalable Web Application with ALB and Auto Scaling](#project-1-scalable-web-application-with-alb-and-auto-scaling)
    *   [Overview](#overview-1)
    *   [Architecture Diagram](#architecture-diagram-1)
    *   [Key Services](#key-services-1)
    *   [Implementation Phases](#implementation-phases-1)
2.  [Project 2: Serverless Image Processing with S3 and Lambda](#project-2-serverless-image-processing-with-s3-and-lambda)
    *   [Overview](#overview-2)
    *   [Architecture Diagram](#architecture-diagram-2)
    *   [Key Services](#key-services-2)
    *   [Implementation Phases](#implementation-phases-2)
3.  [Project 3: Serverless REST API with DynamoDB and API Gateway](#project-3-serverless-rest-api-with-dynamodb-and-api-gateway)
    *   [Overview](#overview-3)
    *   [Architecture Diagram](#architecture-diagram-3)
    *   [Key Services](#key-services-3)
    *   [Implementation Phases](#implementation-phases-3)
4.  [Project Summary & Comparison](#project-summary--comparison)
5.  [Implementation Roadmap](#implementation-roadmap)
6.  [Key Skills Developed](#key-skills-developed)
7.  [Best Practices Summary](#best-practices-summary)
8.  [Next Steps](#next-steps)
9.  [Certification Preparation](#certification-preparation)
10. [Support and Resources](#support-and-resources)

## Project 1: Scalable Web Application with ALB and Auto Scaling

### Overview
This project focuses on deploying a highly available and scalable web application on AWS using traditional EC2 instances, an Application Load Balancer (ALB), and Auto Scaling Groups (ASG). It demonstrates how to design and implement an infrastructure that can handle varying loads and remain resilient to failures.

**Architecture Type:** EC2-based Traditional Architecture
**Complexity Level:** Intermediate
**Estimated Time:** 4-6 hours

### Architecture Diagram

![Project 1 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537171_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QxX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcxNzFfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNReFgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=jvo7nEWxL2ZHl8whWmtu73KN50PTFk1IgxhLwzqNilzl7pD7jVZHCCY893iUoOCM5EJGt~AP5rHq9L6nwYPKw7pBx1khp7dTwqoCG8ldG1Y-v-LTj9Rrlv4b9uRfP-9O9wgSQtrRpR7w5BH9ZJQ-K-opwxw0TZLeHcINe4cAItPG0zlry1Pndv1XWIgiM9OGK6CH4PMDpzph~KfbV-JdIFKbrbDuxygrTd85FPWzDI57NTMVT7l~X1EkpxN81WyxmkYlMj5PQagSZK0R-upe9fP5KUf3NfFq7oWbSHu5f5K-C4q2m9ft6ve4pI-vJj6PBxSNvGzz6qLTz2aQPhNgBg__)

### Key Services
*   **Amazon EC2 (Elastic Compute Cloud):** Virtual servers in the cloud.
*   **Application Load Balancer (ALB):** Distributes incoming application traffic across multiple targets, such as EC2 instances.
*   **Auto Scaling Groups (ASG):** Automatically adjusts the number of EC2 instances in response to demand.
*   **Amazon RDS (Relational Database Service):** (Optional) Managed relational database service.
*   **AWS IAM (Identity and Access Management):** Manages access to AWS services and resources.
*   **Amazon CloudWatch & SNS:** Monitoring and notification services.

### Implementation Phases
1.  **Environment Setup:** AWS account, region, IAM user.
2.  **VPC & Subnet Configuration:** Set up a Virtual Private Cloud with public and private subnets.
3.  **EC2 Instance & AMI Creation:** Launch an EC2 instance, install a web server, and create a custom AMI.
4.  **Application Load Balancer Setup:** Configure ALB, target groups, and listeners.
5.  **Auto Scaling Group Configuration:** Create launch templates and ASG for dynamic scaling.
6.  **RDS Database (Optional):** Set up a managed relational database.
7.  **Monitoring & Alerts:** Configure CloudWatch alarms and SNS notifications.
8.  **Testing & Validation:** Verify high availability and auto-scaling functionality.
9.  **Security & Best Practices:** Implement security groups, NACLs, and IAM roles.
10. **Resource Cleanup:** Delete all created resources to avoid charges.

## Project 2: Serverless Image Processing with S3 and Lambda

### Overview
This project demonstrates building a fully serverless image processing application. Users upload images to an S3 bucket, which triggers an AWS Lambda function to process and resize the images before storing them in another S3 bucket. This showcases an event-driven architecture that is highly scalable and cost-effective.

**Architecture Type:** Event-driven Serverless Architecture
**Complexity Level:** Advanced
**Estimated Time:** 6-8 hours

### Architecture Diagram

![Project 2 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537172_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QyX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcxNzJfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNReVgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=vOQZUQJe9XGiBGx4ifULeq~3fE8ZTmcsuSV~Tlg4gMgoSPQQvDOogzWS6cGVLfhcMNMFFdU6oV~evm3PRvqA83tEk39OTXj1gAhJSc2joAFuBl-ACQriweZQ-zeaOePR3A~vEJdKexgOla9znGHVtZY0h9NRkNECUzZyfDPvQrxGprorBCAxT0A89HvcNd~eqtf5SgDSJkb874HMr7H4CacL7Z3Ezdpsv~YP1hzv2TyOg0aXCboUPjmqme1WFoqCIsu2KmgIPXkeQULn94xrQfAJ6rQBA~QkE51xsGJSCrx-yE6wT1xIRY7F6B9iHzAjgw9pGF4eMI1EiAigxNhHvA__)

### Key Services
*   **Amazon S3 (Simple Storage Service):** Object storage for raw and processed images.
*   **AWS Lambda:** Serverless compute service for image processing logic.
*   **Amazon API Gateway:** (Optional) Creates a RESTful API endpoint for image uploads.
*   **Amazon DynamoDB:** (Optional) NoSQL database for storing image metadata.
*   **AWS Step Functions:** (Optional) Orchestrates complex serverless workflows.
*   **Amazon CloudWatch:** Monitors Lambda function execution and S3 events.

### Implementation Phases
1.  **Environment Setup:** AWS account, region, IAM user.
2.  **S3 Buckets Creation:** Create source and destination S3 buckets.
3.  **IAM Role for Lambda:** Create an IAM role with necessary S3 and Lambda permissions.
4.  **Lambda Function Development:** Write Python code for image resizing using PIL/Pillow.
5.  **S3 Event Triggers:** Configure S3 event notifications to trigger Lambda on object creation.
6.  **API Gateway Integration (Optional):** Set up an API endpoint for direct image uploads.
7.  **DynamoDB Integration (Optional):** Store image metadata in a DynamoDB table.
8.  **Testing & Validation:** Upload images and verify processing and storage.
9.  **Monitoring & Optimization:** Configure CloudWatch for performance and error tracking.
10. **Resource Cleanup:** Delete all created resources.

## Project 3: Serverless REST API with DynamoDB and API Gateway

### Overview
This project focuses on developing a serverless REST API using Amazon API Gateway, AWS Lambda, and DynamoDB. It enables users to perform Create, Read, Update, and Delete (CRUD) operations on data (e.g., a to-do list or customer records) without managing any servers. This project emphasizes API design, data modeling, and serverless backend development.

**Architecture Type:** API-first Serverless Architecture
**Complexity Level:** Advanced
**Estimated Time:** 5-7 hours

### Architecture Diagram

![Project 3 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537224_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QzX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcyMjRfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNRelgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=INc7wiL3jaWqPjEkt4nLIa2bDXB~kV2IIMg0v9wJ3RvrrM4sacGkWl6Qd5yzvFpl2CQTSsvDKWHI3pSiQSiMhlh8awhk62xDquLnZcyklOTo-9ObLXynjBZqcLdUWvlwfufPZhMDD53s7Rgshvm2XOAj0xouU63R4tzS7DAxILTlp5nz-AIFNQnWViWJPcy8OylR2gpSLvjFF4E8FIE8Ewkm~RwDb8xVaj0pNUASE~Bas8-tn9tQHmL4FsFnV7mAojGmtBiiBRjBaE0zg7HVxMicotCPLMMSHIoH2~ynJs1OP9pdMOcdIrt9vfwHirnErRHMYjGf37NWRQrSfWbFeQ__)

### Key Services
*   **Amazon API Gateway:** Manages RESTful API endpoints and routes requests to Lambda.
*   **AWS Lambda:** Executes the backend logic for CRUD operations.
*   **Amazon DynamoDB:** Provides a fast and scalable NoSQL database for storing data.
*   **AWS IAM (Identity and Access Management):** Secures access to Lambda and DynamoDB.
*   **Amazon CloudWatch:** Monitors API and Lambda performance and logs.
*   **Amazon S3:** (Optional) Hosts a simple static frontend for the API.

### Implementation Phases
1.  **Environment Setup:** AWS account, region, IAM user.
2.  **DynamoDB Table Creation:** Create a DynamoDB table for your data (e.g., `TodoItems`).
3.  **IAM Role for Lambda:** Create an IAM role with DynamoDB access for Lambda.
4.  **Lambda Function Development:** Write Python Lambda functions for GET, POST, PUT, DELETE operations.
5.  **API Gateway Setup:** Create a REST API, resources (e.g., `/todos`, `/todos/{id}`), and methods.
6.  **CORS Configuration:** Enable Cross-Origin Resource Sharing for your API.
7.  **API Deployment:** Deploy the API to a stage (e.g., `prod`).
8.  **API Testing:** Test all CRUD endpoints using `curl` or Postman.
9.  **Frontend Integration (Optional):** Create a simple HTML/JavaScript frontend and host it on S3.
10. **Monitoring & Security:** Set up CloudWatch alarms and implement API security best practices.
11. **Resource Cleanup:** Delete all created resources.

## Project Summary & Comparison

| Aspect | Project 1 (EC2) | Project 2 (Serverless Image) | Project 3 (Serverless API) |
|---|---|---|---|
| **Compute** | EC2 Instances | Lambda Functions | Lambda Functions |
| **Primary Storage** | EBS + RDS | S3 Buckets | DynamoDB |
| **Scaling** | Auto Scaling Groups | Automatic (event-driven) | Automatic (API Gateway + Lambda) |
| **Cost Model** | Pay for running instances | Pay per execution/storage | Pay per request/storage |
| **Maintenance** | OS, application, patching | Minimal (AWS manages) | Minimal (AWS manages) |
| **Availability** | Multi-AZ deployment | Built-in (S3, Lambda) | Built-in (API Gateway, Lambda, DynamoDB) |
| **Use Case** | Traditional web apps, custom servers | Event-driven data processing | RESTful microservices, backend APIs |

## Implementation Roadmap

To maximize your learning and build a strong foundation, we recommend the following order for tackling these projects:

1.  **Project 1: Scalable Web Application**
    *   **Focus:** Core AWS services, traditional architecture, networking, and foundational concepts.
    *   **Why:** Provides a solid understanding of how traditional applications are hosted and scaled on AWS before diving into serverless.

2.  **Project 3: Serverless REST API**
    *   **Focus:** Serverless backend development, API design, and NoSQL databases.
    *   **Why:** Introduces you to the serverless paradigm and how to build modern, scalable APIs, which are fundamental for many cloud-native applications.

3.  **Project 2: Serverless Image Processing**
    *   **Focus:** Event-driven architectures, advanced serverless patterns, and inter-service communication.
    **Why:** Builds upon your serverless knowledge by demonstrating how services can react to events, a crucial pattern for highly decoupled and scalable systems.

### Prerequisites
*   An active AWS account (Free Tier eligible where applicable).
*   Basic understanding of cloud computing concepts.
*   Familiarity with command-line interfaces (CLI).
*   Basic programming knowledge (Python is used in Lambda examples).

### Time Investment
*   **Total Estimated Time:** 15-21 hours (excluding optional components and extensive debugging).
*   **Recommended Pace:** Dedicate 1-2 weeks per project to allow for thorough understanding and experimentation.

## Key Skills Developed

By completing these projects, you will develop critical skills essential for an AWS Solutions Architect:

### Technical Skills
*   **AWS Service Proficiency:** Hands-on experience with EC2, ALB, ASG, S3, Lambda, API Gateway, DynamoDB, IAM, CloudWatch, and SNS.
*   **Architectural Design:** Ability to design resilient, scalable, and cost-optimized solutions.
*   **Serverless Development:** Building and deploying serverless applications and APIs.
*   **Networking:** Understanding VPCs, subnets, security groups, and network ACLs.
*   **Database Management:** Working with relational (RDS) and NoSQL (DynamoDB) databases.
*   **Automation:** Using AWS CLI and SDKs for resource management.
*   **Monitoring & Logging:** Implementing CloudWatch for operational visibility.

### Professional Skills
*   **Problem Solving:** Troubleshooting common cloud infrastructure and application issues.
*   **Decision Making:** Choosing appropriate AWS services for specific use cases.
*   **Cost Optimization:** Identifying and implementing strategies to reduce AWS costs.
*   **Security Best Practices:** Applying security principles to cloud architectures.
*   **Documentation:** Creating clear and concise architectural diagrams and guides.

## Best Practices Summary

Across all projects, adhere to these fundamental AWS best practices:

### Security
*   **Least Privilege:** Grant only the necessary permissions to IAM users and roles.
*   **Encryption:** Encrypt data at rest (e.g., S3, DynamoDB) and in transit (e.g., HTTPS for API Gateway).
*   **Network Segmentation:** Use VPCs, subnets, security groups, and NACLs to control traffic flow.
*   **Logging & Auditing:** Enable CloudTrail and CloudWatch Logs for comprehensive activity tracking.
*   **Input Validation:** Validate all input at the API and application layers to prevent injection attacks.

### Cost Optimization
*   **Right-Sizing:** Choose appropriate instance types and Lambda memory configurations.
*   **Auto Scaling:** Dynamically adjust resources based on demand to avoid over-provisioning.
*   **Lifecycle Policies:** Implement S3 lifecycle rules to transition or expire old data.
*   **Serverless First:** Leverage serverless services (Lambda, S3, DynamoDB) for pay-per-use cost models.
*   **Billing Alerts:** Set up CloudWatch billing alarms to monitor spending.

### Performance
*   **High Availability:** Design for redundancy across multiple Availability Zones.
*   **Load Balancing:** Distribute traffic efficiently using ALBs.
*   **Caching:** Implement caching (e.g., API Gateway caching) to reduce latency and load.
*   **Optimized Code:** Write efficient Lambda functions and database queries.
*   **Monitoring:** Continuously monitor performance metrics to identify bottlenecks.

### Operational Excellence
*   **Infrastructure as Code (IaC):** Use CloudFormation or Terraform for repeatable deployments.
*   **Monitoring & Alarms:** Set up proactive alerts for critical metrics.
*   **Logging:** Centralize logs for easier analysis and troubleshooting.
*   **Automated Deployments:** Implement CI/CD pipelines for consistent deployments.
*   **Documentation:** Maintain up-to-date architecture diagrams and runbooks.

## Next Steps

Upon successful completion of these projects, consider expanding your AWS knowledge and skills by exploring:

*   **Advanced AWS Services:** Dive into services like Amazon EKS (Kubernetes), AWS Fargate (container orchestration), Amazon ElastiCache (in-memory caching), and AWS Step Functions (workflow orchestration).
*   **DevOps Integration:** Learn about AWS CodePipeline (CI/CD), AWS CloudFormation (IaC), and AWS Systems Manager (operations management).
*   **Advanced Architectures:** Explore microservices patterns, event-driven architectures, multi-region deployments, and hybrid cloud solutions.
*   **Specialized Certifications:** Pursue AWS Specialty Certifications (e.g., Advanced Networking, Security, Database) to deepen your expertise.

## Certification Preparation

These projects are directly aligned with the domains of the AWS Solutions Architect - Associate certification exam:

*   **Domain 1: Design Resilient Architectures** (30% of exam)
*   **Domain 2: Design High-Performing Architectures** (28% of exam)
*   **Domain 3: Design Secure Applications and Architectures** (24% of exam)
*   **Domain 4: Design Cost-Optimized Architectures** (18% of exam)

By implementing these projects, you will gain practical experience that reinforces the theoretical knowledge required for the certification.

## Support and Resources

*   **AWS Documentation:** The official source for all AWS services.
    *   [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
    *   [AWS Architecture Center](https://aws.amazon.com/architecture/)
    *   [AWS Solutions Library](https://aws.amazon.com/solutions/)
*   **Community Forums:** AWS re:Post, Stack Overflow, and various online communities.
*   **GitHub:** Explore more AWS sample projects and open-source tools.

## Conclusion

This project portfolio provides a robust and practical learning path for anyone aspiring to become an AWS Solutions Architect. By building these real-world solutions, you will not only deepen your understanding of AWS services but also develop the critical thinking and problem-solving skills necessary to design and implement effective cloud solutions. Remember to clean up your AWS resources after completing each project to avoid incurring unexpected costs.

Happy Clouding! 🚀



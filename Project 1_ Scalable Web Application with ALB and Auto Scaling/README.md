# Project 1: Scalable Web Application with ALB and Auto Scaling - Step-by-Step Guide

This guide provides detailed instructions for deploying a scalable web application on AWS using EC2 instances, Application Load Balancer (ALB), and Auto Scaling Groups (ASG). This project demonstrates best practices for compute scalability, security, and cost optimization.

## Project Overview

**Architecture:** EC2-based

### Architecture Diagram

![Project 1 Architecture Diagram](https://private-us-east-1.manuscdn.com/sessionFile/VggiyKytuW5UKqJ3qexkSX/sandbox/Ixl70BjIymnJle1WHfK8rV-images_1749990537171_na1fn_L2hvbWUvdWJ1bnR1L3Byb2plY3QxX2FyY2hpdGVjdHVyZV9kaWFncmFt.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvVmdnaXlLeXR1VzVVS3FKM3FleGtTWC9zYW5kYm94L0l4bDcwQmpJeW1uSmxlMVdIZks4clYtaW1hZ2VzXzE3NDk5OTA1MzcxNzFfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzQnliMnBsWTNReFgyRnlZMmhwZEdWamRIVnlaVjlrYVdGbmNtRnQucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzY3MjI1NjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=jvo7nEWxL2ZHl8whWmtu73KN50PTFk1IgxhLwzqNilzl7pD7jVZHCCY893iUoOCM5EJGt~AP5rHq9L6nwYPKw7pBx1khp7dTwqoCG8ldG1Y-v-LTj9Rrlv4b9uRfP-9O9wgSQtrRpR7w5BH9ZJQ-K-opwxw0TZLeHcINe4cAItPG0zlry1Pndv1XWIgiM9OGK6CH4PMDpzph~KfbV-JdIFKbrbDuxygrTd85FPWzDI57NTMVT7l~X1EkpxN81WyxmkYlMj5PQagSZK0R-upe9fP5KUf3NfFq7oWbSHu5f5K-C4q2m9ft6ve4pI-vJj6PBxSNvGzz6qLTz2aQPhNgBg__)



**Key AWS Services Used:**
*   **EC2 (Elastic Compute Cloud):** Virtual servers in the cloud to host the web application.
*   **Application Load Balancer (ALB):** Distributes incoming application traffic across multiple targets, such as EC2 instances, in multiple Availability Zones.
*   **Auto Scaling Group (ASG):** Automatically adjusts the number of EC2 instances in response to demand, ensuring high availability and fault tolerance.
*   **Amazon RDS (Relational Database Service) (Optional):** Managed relational database service (e.g., MySQL/PostgreSQL) for the backend database, configured with Multi-AZ for high availability.
*   **IAM (Identity and Access Management):** Manages access to AWS services and resources securely.
*   **CloudWatch & SNS (Simple Notification Service):** Monitors application performance and sends alerts for critical events.

## Step-by-Step Instructions

### Phase 1: Prepare your AWS Environment

1.  **Create an AWS Account:** If you don't have one, sign up for a free tier account at [aws.amazon.com](https://aws.amazon.com/).
2.  **Choose a Region:** Select an AWS region close to your geographical location for lower latency (e.g., `us-east-1` for N. Virginia).
3.  **Create an IAM User (Best Practice):**
    *   Navigate to the IAM dashboard.
    *   Create a new IAM user with programmatic access and AWS Management Console access.
    *   Attach the `AdministratorAccess` policy for simplicity in this project (in a real-world scenario, use least privilege).
    *   Save the Access Key ID and Secret Access Key (for programmatic access) and the login URL.
4.  **Create a Key Pair:**
    *   Navigate to the EC2 dashboard.
    *   Under 'Network & Security', select 'Key Pairs'.
    *   Click 'Create key pair', give it a name (e.g., `my-web-app-key`), and download the `.pem` file. This will be used to SSH into your EC2 instances.

### Phase 2: Launch a Web Server Instance (Golden AMI)

1.  **Launch an EC2 Instance (Temporary for AMI Creation):**
    *   Go to the EC2 dashboard and click 'Launch instances'.
    *   Choose an Amazon Machine Image (AMI) (e.g., `Amazon Linux 2 AMI` or `Ubuntu Server`).
    *   Choose an instance type (e.g., `t2.micro` for free tier).
    *   Configure instance details:
        *   Network: Create a new VPC or use the default.
        *   Subnet: Select a public subnet.
        *   Auto-assign Public IP: Enable.
    *   Add storage (default is usually fine).
    *   Add tags (e.g., `Name: WebServer-AMI-Builder`).
    *   Configure a security group:
        *   Create a new security group (e.g., `web-server-sg`).
        *   Add inbound rules: SSH (Port 22) from My IP, HTTP (Port 80) from Anywhere.
    *   Review and Launch. Select your created key pair.
2.  **Connect to the Instance and Install Web Server:**
    *   Once the instance is running, connect to it via SSH using your `.pem` file.
    *   Install a web server (e.g., Apache for Amazon Linux, Nginx for Ubuntu):
        *   **Amazon Linux:**
            ```bash
            sudo yum update -y
            sudo yum install -y httpd
            sudo systemctl start httpd
            sudo systemctl enable httpd
            echo "<h1>Hello from Project 1 Web Server!</h1>" | sudo tee /var/www/html/index.html
            ```
        *   **Ubuntu:**
            ```bash
            sudo apt update -y
            sudo apt install -y nginx
            sudo systemctl start nginx
            sudo systemctl enable nginx
            echo "<h1>Hello from Project 1 Web Server!</h1>" | sudo tee /var/www/html/index.html
            ```
    *   Verify the web server is running by accessing the instance's public IP in a web browser.
3.  **Create a Custom AMI:**
    *   In the EC2 dashboard, select your running web server instance.
    *   Actions > Image and templates > Create image.
    *   Give it a name (e.g., `my-web-app-ami`) and a description.
    *   Click 'Create image'. This will create a 'golden AMI' with your web server pre-installed.
4.  **Terminate the Temporary EC2 Instance:** Once the AMI creation is complete, terminate the temporary EC2 instance to avoid incurring unnecessary costs.

### Phase 3: Set up Application Load Balancer (ALB)

1.  **Create a Target Group:**
    *   Navigate to the EC2 dashboard.
    *   Under 'Load Balancing', select 'Target Groups'.
    *   Click 'Create target group'.
    *   Choose 'Instances' as the target type.
    *   Give it a name (e.g., `my-web-app-tg`).
    *   Protocol: HTTP, Port: 80.
    *   VPC: Select your VPC.
    *   Health checks: Configure as needed (e.g., HTTP, Path: `/`).
    *   Click 'Next' and then 'Create target group' (you won't register instances yet).
2.  **Create an Application Load Balancer:**
    *   Under 'Load Balancing', select 'Load Balancers'.
    *   Click 'Create Load Balancer' and choose 'Application Load Balancer'.
    *   Give it a name (e.g., `my-web-app-alb`).
    *   Scheme: Internet-facing.
    *   IP address type: IPv4.
    *   VPC: Select your VPC.
    *   Mappings: Select at least two public subnets in different Availability Zones for high availability.
    *   Security groups: Create a new security group for the ALB (e.g., `alb-sg`).
        *   Add inbound rule: HTTP (Port 80) from Anywhere.
    *   Listeners and routing: Add a listener for HTTP:80 and forward to your `my-web-app-tg` target group.
    *   Review and Create.

### Phase 4: Configure Auto Scaling Group (ASG)

1.  **Create a Launch Template:**
    *   Navigate to the EC2 dashboard.
    *   Under 'Instances', select 'Launch Templates'.
    *   Click 'Create launch template'.
    *   Give it a name (e.g., `my-web-app-lt`).
    *   Launch template contents:
        *   AMI: Select your custom AMI (`my-web-app-ami`).
        *   Instance type: `t2.micro`.
        *   Key pair: Select your key pair.
        *   Network settings: Select your `web-server-sg` security group.
        *   Advanced details (Optional):
            *   IAM instance profile: Create a new IAM role with `AmazonEC2ContainerRegistryReadOnly` (if pulling images from ECR) or other necessary permissions.
            *   User data: (Optional) Add a script to run on instance launch, e.g., to update packages or configure the web server further.
    *   Click 'Create launch template'.
2.  **Create an Auto Scaling Group:**
    *   Navigate to the EC2 dashboard.
    *   Under 'Auto Scaling', select 'Auto Scaling Groups'.
    *   Click 'Create Auto Scaling group'.
    *   Give it a name (e.g., `my-web-app-asg`).
    *   Launch template: Select your `my-web-app-lt`.
    *   Network:
        *   VPC: Select your VPC.
        *   Subnets: Select the same public subnets used for the ALB.
    *   Load balancing: Choose 'Attach to an existing load balancer' and select your `my-web-app-tg` target group.
    *   Group size:
        *   Desired capacity: 2 (start with two instances).
        *   Minimum capacity: 1.
        *   Maximum capacity: 4 (or more, depending on expected load).
    *   Scaling policies:
        *   Configure dynamic scaling policies (e.g., 'Target tracking scaling policy').
        *   Metric: `ASGCPUUtilization`.
        *   Target value: 50 (aim for 50% CPU utilization).
    *   Notifications (Optional): Configure SNS topic for notifications on scale-out/in events.
    *   Tags (Optional): Add tags.
    *   Review and Create.

### Phase 5: (Optional) Integrate with Amazon RDS

1.  **Launch an RDS Instance:**
    *   Navigate to the RDS dashboard.
    *   Click 'Create database'.
    *   Choose a database engine (e.g., MySQL or PostgreSQL).
    *   Select 'Free tier' if applicable.
    *   Specify DB instance identifier, master username, and password.
    *   VPC: Select your VPC.
    *   Subnet group: Create a new DB subnet group with private subnets (best practice).
    *   Public accessibility: No (for security).
    *   VPC security group: Create a new security group for RDS (e.g., `rds-sg`).
        *   Add inbound rule: MySQL/PostgreSQL (Port 3306/5432) from your `web-server-sg` (allowing only your EC2 instances to connect).
    *   Multi-AZ deployment: Yes (for high availability).
    *   Backup, Monitoring, and Maintenance: Configure as needed.
    *   Click 'Create database'.
2.  **Update Web Application:** Modify your web application code to connect to the RDS endpoint instead of a local database.

### Phase 6: Monitoring and Alarms

1.  **CloudWatch Alarms:**
    *   Navigate to the CloudWatch dashboard.
    *   Under 'Alarms', click 'Create alarm'.
    *   Select a metric (e.g., `CPUUtilization` for EC2 instances in your ASG, or `HealthyHostCount` for your ALB target group).
    *   Define the threshold (e.g., if CPU utilization > 80% for 5 minutes).
    *   Configure actions: Send notification to an SNS topic (create a new one if you haven't already).
    *   Give the alarm a name and description.
    *   Create alarm.

### Phase 7: Clean Up Resources

1.  **Terminate Auto Scaling Group:** Delete the ASG. This will terminate all instances launched by the ASG.
2.  **Delete Launch Template:** Delete the launch template.
3.  **Delete Application Load Balancer:** Delete the ALB.
4.  **Delete Target Group:** Delete the target group.
5.  **Delete RDS Instance (if created):** Delete the RDS database instance (take a final snapshot if needed).
6.  **Delete Custom AMI:** Deregister the AMI and delete its associated snapshots.
7.  **Delete Security Groups:** Delete the security groups created for the web server, ALB, and RDS.
8.  **Delete Key Pair:** Delete the key pair.

This comprehensive guide should help you successfully implement Project 1. Remember to monitor your costs and clean up resources once you are done with the project.


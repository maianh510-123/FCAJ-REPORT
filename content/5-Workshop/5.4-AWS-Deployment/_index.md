---
title: "AWS Deployment"
date: 2026-07-31
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
# AWS Deployment with Terraform

## 1. Infrastructure bootstrap

I deployed the bootstrap stack first to create three foundational resources:

- a versioned, AES-256 encrypted S3 bucket for Terraform remote state;
- a DynamoDB table to prevent concurrent state writes;
- the **quickbite-backend** ECR repository with scan-on-push.

<img src="/images/5-Workshop/ecr-repository.png" alt="QuickBite Amazon ECR repository" width="100%" />
<p><em>The quickbite-backend ECR repository stores the FastAPI backend image.</em></p>

After bootstrap, I used the **state_bucket**, **lock_table**, and **ecr_repository_url** outputs to configure the main stack.

## 2. Build and push the backend image

I built the backend from its Dockerfile, tagged it with the ECR repository URL, and pushed it to ECR. The Launch Template does not contain source code; each EC2 instance authenticates through its IAM role, pulls the versioned image, and runs the same configuration.

## 3. Main Terraform stack

The main stack wires the **network**, **data**, and **app** modules. Terraform plan described the VPC, subnets, ALB, ASG, RDS, S3, CloudFront, IAM, Secret, log group, alarms, and SNS before apply. The final apply created 58 resources.

### Network module

- VPC in ap-southeast-1;
- two public subnets for the ALB and NAT Gateways;
- two private application subnets for EC2;
- two isolated database subnets for RDS;
- Security Group chaining: ALB-SG → App-SG:8000 → DB-SG:5432.

### Data module

- encrypted Multi-AZ RDS PostgreSQL db.t3.micro;
- a DB subnet group across two isolated subnets;
- Secrets Manager containing DATABASE_URL and the JWT secret.

### App module

- ALB, Target Group, and **/health** health check;
- Launch Template and Auto Scaling Group with min 2, desired 2, max 4;
- target tracking with a 60% CPU target;
- private web and menu-images S3 buckets;
- two CloudFront distributions;
- IAM role, instance profile, ECR read-only, SSM core, and a scoped inline policy;
- CloudWatch log group, CPU alarm, and SNS email.

<img src="/images/5-Workshop/ec2-two-az.png" alt="Two QuickBite EC2 instances across Availability Zones" width="100%" />
<p><em>The Auto Scaling Group maintains two t3.micro instances in ap-southeast-1a and ap-southeast-1b; both passed all 3/3 status checks.</em></p>

## 4. S3 and CloudFront

Terraform created three buckets:

- **quickbite-web-a64511** for the frontend;
- **quickbite-menu-images-a64511** for menu images;
- **quickbite-tfstate-46a21a** for remote state.

<img src="/images/5-Workshop/s3-buckets.png" alt="QuickBite S3 buckets" width="100%" />
<p><em>The web, menu-images, and Terraform-state buckets in ap-southeast-1.</em></p>

Public access is blocked for both content buckets. CloudFront Origin Access Control is the only principal allowed to read objects.

<img src="/images/5-Workshop/cloudfront-distributions.png" alt="Two QuickBite CloudFront distributions" width="100%" />
<p><em>Two enabled CloudFront distributions: one for the ALB/API and one for web + menu images.</em></p>

## 5. IAM, secrets, and instance management

The EC2 instance profile is declared in the Launch Template. Its role has only the required permissions:

- read the application secret;
- read and write objects in the menu-images bucket;
- deliver logs to the QuickBite log group;
- pull images from ECR;
- connect through Systems Manager Session Manager.

<img src="/images/5-Workshop/iam-roles.png" alt="IAM roles in the AWS account" width="100%" />
<p><em>IAM roles and service-linked roles support Auto Scaling, Elastic Load Balancing, RDS, and the EC2 application instances.</em></p>

I used SSM to load the schema, seed data, and views into RDS from inside the VPC. This kept the database private and avoided opening port 5432 to my laptop.

## 6. Frontend build

After Terraform exported **api_url** and **frontend_bucket**, I built React with the CloudFront API domain as the API base, then synchronized the dist directory to the web S3 bucket. SPA fallback maps 403/404 responses to index.html so routes such as admin, menu, and order tracking still work after refresh.

## 7. Attachment

- [QuickBite source](../../attachments/quickbite-source.zip)
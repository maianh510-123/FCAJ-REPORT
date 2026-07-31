---
title: "Security and cost optimization"
date: 2026-07-31
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---
# Security and cost optimization

## 1. Identity and credentials

I stopped using root access keys after identifying the risk in the early deployment stage. AWS CLI uses a dedicated IAM user, while EC2 stores no access key and receives permissions through an instance profile.

The EC2 role includes the required permissions for:

- Secrets Manager GetSecretValue on the application secret;
- S3 GetObject and PutObject on the menu-images bucket;
- CloudWatch Logs CreateLogStream, PutLogEvents, and DescribeLogStreams;
- ECR read-only access;
- Systems Manager Managed Instance Core.

## 2. Network isolation

| Layer | Control |
|---|---|
| CloudFront | User-facing HTTPS entry point |
| ALB | Forwards requests only to the target group on TCP 8000 |
| EC2 | Runs in private subnets |
| RDS | Runs in isolated database subnets |
| Security Groups | ALB-SG → App-SG:8000 → DB-SG:5432 |
| SSM | Instance management without public SSH |
| NAT Gateway | Outbound access from private subnets |

## 3. S3 and CloudFront OAC

The web and menu-images buckets both block public access. Their policies allow the CloudFront service principal to read objects only when SourceArn matches the web distribution. The backend uploads images through its EC2 IAM role and does not use public ACLs.

## 4. Secret management

DATABASE_URL and the JWT secret are stored in Secrets Manager. All EC2 instances read the same secret, keeping tokens consistent across instances. The secret is not hard-coded in the Launch Template, user data, image, or plain Terraform variables.

## 5. Containers and ECR

The backend image is stored in a private ECR repository with scan-on-push enabled. Instances pull the image through the ECR read-only managed policy. Running the same image on every instance reduces configuration drift.

## 6. Logging and alarms

Container stdout/stderr is sent to a Terraform-managed CloudWatch log group. The log group has finite retention to avoid indefinite storage. CloudWatch monitors Auto Scaling Group CPU and sends Alarm/OK state changes through SNS.

## 7. Cost control

The high-availability architecture costs more than the earlier baseline because it includes:

- two always-running EC2 instances;
- Multi-AZ RDS;
- an Application Load Balancer;
- two NAT Gateways;
- two CloudFront distributions;
- CloudWatch logs and metrics.

I use AWS Budgets and Cost Explorer to monitor cost. Terraform also exposes **multi_az**, **enable_nat**, and **enable_interface_endpoints** so another environment can adjust the balance between reliability and cost.

## 8. Well-Architected review

### Operational Excellence

- Infrastructure as Code;
- remote state and locking;
- modular design;
- health checks;
- CloudWatch, SNS, and operational documentation.

### Security

- private S3 + OAC;
- private EC2 and isolated RDS;
- IAM role;
- Secrets Manager;
- SSM;
- no root access key.

### Reliability

- two Availability Zones;
- ALB;
- ASG min 2;
- Multi-AZ RDS;
- target tracking and health checks.

### Performance Efficiency

- CloudFront caching;
- ALB request distribution;
- Auto Scaling;
- t3.micro sized for the demo.

### Cost Optimization

- Budget;
- Cost Explorer;
- Terraform destroy;
- switches for NAT, endpoints, and Multi-AZ.

### Sustainability

- managed services;
- Auto Scaling capacity;
- log retention;
- an explicit environment lifecycle.
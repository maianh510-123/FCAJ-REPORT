---
title: "Clean-up"
date: 2026-07-31
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
# Clean-up

The QuickBite infrastructure has a complete Terraform lifecycle. At the time this report was finalized, I kept the environment running for the demo and had not yet executed destroy.

## 1. Lifecycle order

The main stack is destroyed first, followed by the bootstrap stack. This keeps remote state and ECR available until Terraform has removed the primary resources.

- **terraform destroy** in the stack directory removes the VPC, ALB, ASG, RDS, CloudFront, content S3 buckets, IAM, CloudWatch, SNS, and Secrets Manager;
- **terraform destroy** in the bootstrap directory removes ECR, the state bucket, and the DynamoDB lock table.

## 2. Destroy-oriented configuration

The Terraform source includes demo-environment lifecycle settings:

- content and state S3 buckets use force_destroy;
- ECR uses force_delete;
- RDS deletion protection is disabled;
- RDS skips the final snapshot for disposable demo data;
- Secrets Manager uses a zero-day recovery window;
- the CloudWatch log group is Terraform-managed.

These settings make complete deletion possible but are not suitable for production data that requires retention or recovery.

## 3. Cost-sensitive components

- NAT Gateways;
- Multi-AZ RDS;
- Application Load Balancer;
- two t3.micro EC2 instances;
- CloudWatch Logs;
- S3 object versions;
- CloudFront traffic.

AWS Budgets and Cost Explorer monitor cost during the demo. When the environment is no longer required, Terraform state remains the authoritative list of project-managed resources.

## 4. Current status

Clean-up is not included in the evidence set because the frontend, API, and dashboard are still being used to present the final result. The report does not claim clean-up evidence at this time.
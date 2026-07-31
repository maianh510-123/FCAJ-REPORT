---
title: "Workshop"
date: 2026-07-31
weight: 5
chapter: false
pre: " <b> 5. </b> "
includeInReport: false
---
# Workshop: Deploying QuickBite on AWS with Terraform

This workshop documents how I moved QuickBite from a local Docker environment to a two-Availability-Zone high-availability architecture in **ap-southeast-1**.

## Workshop structure

1. **Overview:** the problem, business flow, and deployed architecture;
2. **Prerequisites:** local environment, AWS account, and Terraform structure;
3. **Local baseline:** the application state before cloud deployment;
4. **AWS deployment:** bootstrap, ECR, Terraform modules, database, and frontend;
5. **Testing and validation:** console evidence, health checks, business flow, image upload, monitoring, and email;
6. **Security and cost:** IAM, S3 OAC, network isolation, Secrets Manager, SSM, Budgets, and cost trade-offs;
7. **Clean-up:** resource lifecycle and Terraform destroy design.

## Completed scope

- two CloudFront distributions for web/images and API;
- private S3 for the frontend, menu images, and Terraform state;
- an ALB across two public subnets;
- an EC2 Auto Scaling Group in two private subnets, min 2, desired 2, max 4;
- Multi-AZ RDS PostgreSQL in isolated database subnets;
- an ECR repository and backend Docker image;
- Secrets Manager for DATABASE_URL and the JWT secret;
- Systems Manager Session Manager without public SSH;
- CloudWatch Logs, a CPU alarm, target-tracking alarms, and SNS email;
- a least-privilege IAM role;
- AWS Budgets and Cost Explorer;
- Terraform Infrastructure as Code.

QuickBite does not use API Gateway or a custom domain in this deployment. Users access the system through the default CloudFront HTTPS domains.
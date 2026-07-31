---
title: "Prerequisites"
date: 2026-07-31
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
# Prerequisites

## 1. Environment used

- Windows 11 and PowerShell;
- AWS CLI v2;
- Terraform 1.5 or later;
- Docker Desktop;
- Node.js LTS and npm;
- Git;
- Hugo Extended for report validation;
- AWS Region **ap-southeast-1**.

## 2. Initial account security

During the early stage, I discovered that a root-account access key had appeared in a screenshot. I moved to a dedicated IAM user, deleted the exposed root key, and used IAM credentials for AWS CLI. From that point onward, I reviewed screenshots to ensure that Access Keys, Secret Keys, database passwords, and JWT secrets were not exposed.

## 3. Source structure

| Component | Content |
|---|---|
| Backend | FastAPI, SQLAlchemy, Alembic, PostgreSQL schema, tests, and Dockerfile |
| Frontend | React, TypeScript, Vite, Material UI, and production build |
| Terraform bootstrap | S3 remote state, DynamoDB lock, and ECR |
| Terraform stack | network, data, and app modules |
| Report | Bilingual Hugo site, architecture, worklog, and evidence images |

## 4. Terraform structure

| Directory | Responsibility |
|---|---|
| **terraform/bootstrap** | Creates the state bucket, lock table, and ECR first |
| **terraform/stack/modules/network** | VPC, subnets, route tables, NAT, endpoints, and Security Groups |
| **terraform/stack/modules/data** | Multi-AZ RDS and Secrets Manager |
| **terraform/stack/modules/app** | ALB, ASG, IAM, CloudWatch, SNS, S3, and CloudFront |
| **terraform/stack** | Wires the modules and exports web_url, api_url, buckets, and log group |

## 5. Main deployment values

- instance type: **t3.micro**;
- database class: **db.t3.micro**;
- RDS Multi-AZ: **true**;
- Auto Scaling Group: **min 2, desired 2, max 4**;
- CloudWatch CPU threshold: **70% for two 300-second periods**;
- Region: **ap-southeast-1**;
- frontend and image buckets: private;
- EC2: private subnets, managed through SSM;
- RDS: isolated subnets, accepting TCP 5432 only from the App Security Group.

## 6. Project files

- [QuickBite source](../../attachments/quickbite-source.zip)
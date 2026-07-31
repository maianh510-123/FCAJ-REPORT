---
title: "Local baseline"
date: 2026-07-31
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
# Local baseline

## 1. Architecture before deployment

Before creating AWS resources, I completed QuickBite locally with four main containers:

| Component | Technology |
|---|---|
| Frontend | React, TypeScript, and Vite |
| Backend | FastAPI and Uvicorn |
| Database | PostgreSQL |
| Mock email | Mailpit |

Docker Compose allowed me to validate the application in a production-like environment before moving the database to RDS and the backend to EC2.

## 2. Validated flows

- registration, login, and session restoration;
- customer, admin, kitchen, and delivery authorization;
- menu search, filtering, sorting, and cart operations;
- COD and mock e-wallet orders;
- server-side subtotal, delivery fee, tax, and total calculation;
- admin order confirmation and management;
- kitchen preparing and ready transitions;
- delivery completion;
- order tracking with code and token;
- dashboards, CSV reports, and audit logs;
- image-upload validation by type and size;
- mock email through Mailpit;
- health endpoint and automated tests.

## 3. AWS-oriented changes

I mapped the local components to AWS services:

- local PostgreSQL became RDS PostgreSQL;
- the frontend production build moved to S3 and CloudFront;
- the backend Docker image was pushed to ECR;
- the backend ran in an EC2 Auto Scaling Group;
- image files moved to the menu-images S3 bucket;
- local secrets moved to Secrets Manager;
- SSH was replaced by SSM Session Manager;
- container stdout/stderr moved to CloudWatch Logs.

## 4. Why I retained the local baseline

The local baseline helped me separate application defects from infrastructure defects. When a deployment issue appeared, I could compare it with the local result and determine whether the cause was code, environment variables, networking, IAM, CloudFront, the database, or local CLI tooling.
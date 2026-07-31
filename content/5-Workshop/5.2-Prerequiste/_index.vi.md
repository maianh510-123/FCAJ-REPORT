---
title: "Điều kiện tiên quyết"
date: 2026-07-31
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
# Điều kiện tiên quyết

## 1. Môi trường em sử dụng

- Windows 11 và PowerShell;
- AWS CLI v2;
- Terraform phiên bản 1.5 trở lên;
- Docker Desktop;
- Node.js LTS và npm;
- Git;
- Hugo Extended để kiểm tra website báo cáo;
- AWS Region **ap-southeast-1**.

## 2. Tài khoản và bảo mật ban đầu

Trong giai đoạn đầu, em phát hiện access key của root account đã xuất hiện trong screenshot. Em chuyển sang IAM user riêng, xóa key root đã lộ và chỉ dùng credential của IAM identity cho AWS CLI. Từ thời điểm đó, mọi screenshot đều được kiểm tra để tránh lộ Access Key, Secret Key, database password hoặc JWT secret.

## 3. Cấu trúc source

| Thành phần | Nội dung |
|---|---|
| Backend | FastAPI, SQLAlchemy, Alembic, PostgreSQL schema, tests và Dockerfile |
| Frontend | React, TypeScript, Vite, Material UI và production build |
| Terraform bootstrap | S3 remote state, DynamoDB lock và ECR |
| Terraform stack | network, data và app modules |
| Report | Hugo song ngữ, kiến trúc, worklog và ảnh minh chứng |

## 4. Cấu trúc Terraform

| Thư mục | Trách nhiệm |
|---|---|
| **terraform/bootstrap** | Tạo state bucket, lock table và ECR trước |
| **terraform/stack/modules/network** | VPC, subnets, route tables, NAT, endpoints và Security Groups |
| **terraform/stack/modules/data** | RDS Multi-AZ và Secrets Manager |
| **terraform/stack/modules/app** | ALB, ASG, IAM, CloudWatch, SNS, S3 và CloudFront |
| **terraform/stack** | Kết nối ba module và xuất web_url, api_url, bucket và log group |

## 5. Các giá trị triển khai chính

- instance type: **t3.micro**;
- database class: **db.t3.micro**;
- RDS Multi-AZ: **true**;
- Auto Scaling Group: **min 2, desired 2, max 4**;
- CloudWatch CPU threshold: **70% trong hai chu kỳ 300 giây**;
- Region: **ap-southeast-1**;
- frontend và image buckets: private;
- EC2: private subnet, quản trị bằng SSM;
- RDS: isolated subnet, chỉ nhận TCP 5432 từ App Security Group.

## 6. Tệp dự án

- [Mã nguồn QuickBite](../../attachments/quickbite-source.zip)
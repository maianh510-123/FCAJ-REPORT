---
title: "Workshop"
date: 2026-07-31
weight: 5
chapter: false
pre: " <b> 5. </b> "
includeInReport: false
---
# Workshop: Triển khai QuickBite trên AWS bằng Terraform

Workshop trình bày quá trình em đưa QuickBite từ Docker local lên kiến trúc High Availability hai Availability Zone tại Region **ap-southeast-1**.

## Cấu trúc workshop

1. **Tổng quan:** bài toán, business flow và kiến trúc đã triển khai;
2. **Điều kiện tiên quyết:** môi trường local, tài khoản AWS và cấu trúc Terraform;
3. **Baseline local:** trạng thái ứng dụng trước khi lên cloud;
4. **Triển khai AWS:** bootstrap, ECR, Terraform modules, database và frontend;
5. **Kiểm thử và xác thực:** bằng chứng console, health check, business flow, upload ảnh, monitoring và email;
6. **Bảo mật và chi phí:** IAM, S3 OAC, network isolation, Secrets Manager, SSM, Budgets và các đánh đổi chi phí;
7. **Clean-up:** vòng đời tài nguyên và phương án Terraform destroy.

## Phạm vi đã hoàn thành

- hai CloudFront distributions cho web/images và API;
- S3 private cho frontend, menu images và Terraform state;
- ALB tại hai public subnets;
- EC2 Auto Scaling Group trong hai private subnets, min 2, desired 2, max 4;
- RDS PostgreSQL Multi-AZ trong isolated database subnets;
- ECR repository và Docker image backend;
- Secrets Manager cho DATABASE_URL và JWT secret;
- Systems Manager Session Manager, không cần public SSH;
- CloudWatch Logs, CPU alarm, target tracking alarms và SNS email;
- IAM role theo least privilege;
- AWS Budgets và Cost Explorer;
- Infrastructure as Code bằng Terraform.

QuickBite không sử dụng API Gateway hoặc custom domain trong bản triển khai này. Người dùng truy cập bằng CloudFront default domains qua HTTPS.
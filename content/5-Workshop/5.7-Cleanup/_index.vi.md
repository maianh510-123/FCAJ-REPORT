---
title: "Dọn dẹp tài nguyên"
date: 2026-07-31
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
# Clean-up

Hạ tầng QuickBite được thiết kế để có vòng đời đầy đủ bằng Terraform. Tại thời điểm hoàn thiện báo cáo, môi trường vẫn được giữ để demo nên em chưa chạy destroy.

## 1. Thứ tự vòng đời

Main stack được xóa trước, bootstrap stack được xóa sau. Thứ tự này giữ remote state và ECR cho đến khi các resource chính đã được Terraform loại bỏ.

- **terraform destroy** tại thư mục stack xóa VPC, ALB, ASG, RDS, CloudFront, S3 content buckets, IAM, CloudWatch, SNS và Secrets Manager;
- **terraform destroy** tại thư mục bootstrap xóa ECR, state bucket và DynamoDB lock table.

## 2. Cấu hình hỗ trợ destroy

Terraform source có các lựa chọn phục vụ môi trường demo:

- S3 content buckets và tfstate bucket dùng force_destroy;
- ECR dùng force_delete;
- RDS tắt deletion protection;
- RDS dùng skip final snapshot cho dữ liệu demo;
- Secrets Manager dùng recovery window bằng 0;
- CloudWatch log group do Terraform quản lý.

Các cấu hình này giúp môi trường có thể xóa sạch, nhưng không phù hợp cho production data cần retention hoặc recovery.

## 3. Thành phần có chi phí cần chú ý

- NAT Gateway;
- RDS Multi-AZ;
- Application Load Balancer;
- hai EC2 t3.micro;
- CloudWatch Logs;
- S3 object versions;
- CloudFront traffic.

AWS Budgets và Cost Explorer được dùng để theo dõi chi phí trong thời gian demo. Sau khi môi trường không còn cần thiết, Terraform state là nguồn xác định đầy đủ các resource do project quản lý.

## 4. Trạng thái hiện tại

Clean-up chưa được thực hiện trong bộ ảnh minh chứng vì frontend, API và dashboard vẫn đang được dùng để trình bày kết quả. Báo cáo không khẳng định có clean-up evidence ở thời điểm này.
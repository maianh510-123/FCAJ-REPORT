---
title: "Triển khai AWS"
date: 2026-07-31
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
# Triển khai AWS bằng Terraform

## 1. Bootstrap hạ tầng nền

Em triển khai bootstrap stack trước để tạo ba tài nguyên nền:

- S3 bucket có versioning và AES-256 encryption cho Terraform remote state;
- DynamoDB table để tránh hai tiến trình cùng ghi state;
- ECR repository **quickbite-backend** có scan-on-push.

<img src="/images/5-Workshop/ecr-repository.png" alt="QuickBite Amazon ECR repository" width="100%" />
<p><em>ECR repository quickbite-backend lưu Docker image của FastAPI backend.</em></p>

Sau bootstrap, em lấy các output **state_bucket**, **lock_table** và **ecr_repository_url** để cấu hình main stack.

## 2. Build và push backend image

Backend được build từ Dockerfile, gắn tag theo ECR repository URL và push lên ECR. Launch Template không chứa source code; mỗi EC2 đăng nhập ECR bằng IAM role, pull image đã version và chạy cùng một cấu hình.

## 3. Main Terraform stack

Main stack kết nối ba module **network**, **data** và **app**. Terraform plan mô tả toàn bộ VPC, subnets, ALB, ASG, RDS, S3, CloudFront, IAM, Secret, log group, alarms và SNS trước khi apply. Lần apply cuối tạo đủ 58 resources.

### Network module

- VPC tại ap-southeast-1;
- hai public subnets cho ALB và NAT Gateway;
- hai private application subnets cho EC2;
- hai isolated database subnets cho RDS;
- Security Group chaining: ALB-SG → App-SG:8000 → DB-SG:5432.

### Data module

- RDS PostgreSQL db.t3.micro, encrypted và Multi-AZ;
- DB subnet group trên hai isolated subnets;
- Secrets Manager chứa DATABASE_URL và JWT secret.

### App module

- ALB, Target Group và health check **/health**;
- Launch Template và Auto Scaling Group min 2, desired 2, max 4;
- target tracking policy với CPU mục tiêu 60%;
- S3 web và menu-images private;
- hai CloudFront distributions;
- IAM role, instance profile, ECR read-only, SSM core và scoped inline policy;
- CloudWatch log group, CPU alarm và SNS email.

<img src="/images/5-Workshop/ec2-two-az.png" alt="Hai EC2 QuickBite tại hai Availability Zone" width="100%" />
<p><em>Auto Scaling Group duy trì hai instance t3.micro tại ap-southeast-1a và ap-southeast-1b; cả hai vượt qua 3/3 status checks.</em></p>

## 4. S3 và CloudFront

Terraform tạo ba bucket:

- **quickbite-web-a64511** cho frontend;
- **quickbite-menu-images-a64511** cho ảnh món;
- **quickbite-tfstate-46a21a** cho remote state.

<img src="/images/5-Workshop/s3-buckets.png" alt="Các S3 bucket của QuickBite" width="100%" />
<p><em>Ba S3 bucket cho web, menu images và Terraform state tại ap-southeast-1.</em></p>

Web và image buckets đều chặn public access. CloudFront Origin Access Control là principal duy nhất được đọc object.

<img src="/images/5-Workshop/cloudfront-distributions.png" alt="Hai CloudFront distributions của QuickBite" width="100%" />
<p><em>Hai CloudFront distributions được bật: một distribution cho ALB/API và một distribution cho web + menu images.</em></p>

## 5. IAM, Secret và quản trị instance

EC2 instance profile được khai báo trong Launch Template. Role chỉ có các quyền cần thiết:

- đọc đúng secret của ứng dụng;
- đọc và ghi object trong menu-images bucket;
- gửi log vào log group QuickBite;
- pull image từ ECR;
- kết nối Systems Manager Session Manager.

<img src="/images/5-Workshop/iam-roles.png" alt="IAM roles trong tài khoản AWS" width="100%" />
<p><em>IAM roles và service-linked roles hỗ trợ Auto Scaling, Elastic Load Balancing, RDS và EC2 application instances.</em></p>


Em dùng SSM để nạp schema, seed và views vào RDS từ bên trong VPC. Cách này giữ database private và không cần mở port 5432 cho laptop.

## 6. Build frontend

Sau khi Terraform xuất **api_url** và **frontend_bucket**, em build React với API base là CloudFront API domain, sau đó đồng bộ thư mục dist lên S3 web bucket. SPA fallback trả index.html cho 403/404 để các route như admin, menu và order tracking vẫn hoạt động khi refresh.

## 7. Tệp đính kèm

- [Mã nguồn QuickBite](../../attachments/quickbite-source.zip)
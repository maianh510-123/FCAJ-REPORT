---
title: "Bảo mật và tối ưu chi phí"
date: 2026-07-31
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---
# Bảo mật và tối ưu chi phí

## 1. Identity và credential

Em không dùng access key của root sau khi phát hiện rủi ro trong giai đoạn đầu. AWS CLI sử dụng IAM user riêng; EC2 không lưu access key mà nhận quyền qua instance profile.

EC2 role có các quyền cần thiết cho:

- Secrets Manager GetSecretValue trên đúng application secret;
- S3 GetObject và PutObject trên menu-images bucket;
- CloudWatch Logs CreateLogStream, PutLogEvents và DescribeLogStreams;
- ECR read-only;
- Systems Manager Managed Instance Core.

## 2. Network isolation

| Lớp | Kiểm soát |
|---|---|
| CloudFront | Entry point HTTPS cho người dùng |
| ALB | Chỉ chuyển request đến target group TCP 8000 |
| EC2 | Nằm trong private subnets |
| RDS | Nằm trong isolated database subnets |
| Security Groups | ALB-SG → App-SG:8000 → DB-SG:5432 |
| SSM | Quản trị instance không cần public SSH |
| NAT Gateway | Cho outbound từ private subnet |

## 3. S3 và CloudFront OAC

Web bucket và menu-images bucket đều bật Block Public Access. Bucket policy chỉ cho phép CloudFront service principal đọc object khi SourceArn khớp đúng web distribution. Backend upload ảnh bằng EC2 IAM role, không dùng public ACL.

## 4. Secret management

DATABASE_URL và JWT secret được lưu trong Secrets Manager. Tất cả EC2 đọc cùng một secret, giúp token hợp lệ nhất quán giữa nhiều instance. Secret không được hard-code trong Launch Template, user data, image hoặc Terraform variables dạng plain text.

## 5. Container và ECR

Backend image nằm trong ECR private repository. Scan-on-push được bật ở bootstrap. Instance pull image bằng ECR read-only managed policy. Việc dùng cùng một image cho mọi instance giúp giảm configuration drift.

## 6. Logging và alarm

Container stdout/stderr được gửi vào CloudWatch log group do Terraform quản lý. Log group có retention hữu hạn để tránh giữ log vô thời hạn. CloudWatch theo dõi CPU của Auto Scaling Group và gửi trạng thái Alarm/OK qua SNS.

## 7. Kiểm soát chi phí

Kiến trúc HA có chi phí cao hơn baseline cũ vì gồm:

- hai EC2 luôn hoạt động;
- RDS Multi-AZ;
- Application Load Balancer;
- hai NAT Gateway;
- hai CloudFront distributions;
- CloudWatch Logs và metrics.

Em dùng AWS Budgets và Cost Explorer để theo dõi chi phí. Terraform cũng cung cấp các biến **multi_az**, **enable_nat** và **enable_interface_endpoints** để có thể điều chỉnh giữa reliability và cost ở môi trường khác.

## 8. Well-Architected review

### Operational Excellence

- Infrastructure as Code;
- remote state và locking;
- module hóa;
- health check;
- CloudWatch, SNS và tài liệu vận hành.

### Security

- private S3 + OAC;
- private EC2 và isolated RDS;
- IAM role;
- Secrets Manager;
- SSM;
- không dùng root key.

### Reliability

- hai Availability Zone;
- ALB;
- ASG min 2;
- RDS Multi-AZ;
- target tracking và health checks.

### Performance Efficiency

- CloudFront cache;
- ALB distribution;
- Auto Scaling;
- t3.micro phù hợp demo.

### Cost Optimization

- Budget;
- Cost Explorer;
- Terraform destroy;
- biến bật/tắt NAT, endpoints và Multi-AZ.

### Sustainability

- managed services;
- capacity theo Auto Scaling;
- log retention;
- môi trường có vòng đời rõ ràng.
---
title: "Tổng quan"
date: 2026-07-31
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---
# Tổng quan Workshop

## 1. Bài toán QuickBite

QuickBite số hóa luồng đặt món và vận hành giữa khách hàng, quản trị viên, bếp và giao hàng. Backend FastAPI xử lý xác thực, menu, order, payment, report, audit log và upload ảnh. PostgreSQL lưu dữ liệu giao dịch; React cung cấp storefront và các giao diện theo vai trò.

## 2. Luồng nghiệp vụ

1. Customer xem menu và tạo đơn.
2. Backend kiểm tra dữ liệu, tính tổng tiền ở server và ghi order vào PostgreSQL.
3. Admin xác nhận hoặc quản lý đơn.
4. Kitchen chuyển đơn qua các trạng thái chuẩn bị.
5. Delivery nhận đơn sẵn sàng và hoàn tất giao hàng.
6. Customer theo dõi trạng thái bằng tài khoản hoặc mã tra cứu.

## 3. Kiến trúc AWS đã triển khai

<img src="/images/5-Workshop/5.1-Workshop-overview/internship.png" alt="Sơ đồ kiến trúc QuickBite trên AWS" width="100%" />
<p><em>Sơ đồ kiến trúc High Availability hai Availability Zone được triển khai bằng Terraform.</em></p>

Kiến trúc gồm ba lớp chính:

- **Global edge:** CloudFront cung cấp HTTPS và cache cho frontend, ảnh món và API;
- **Application layer:** ALB phân phối request đến EC2 Auto Scaling Group trong hai private subnet;
- **Data layer:** RDS PostgreSQL Multi-AZ nằm trong isolated database subnets.

Các dịch vụ ECR, Secrets Manager, Systems Manager, CloudWatch, SNS, IAM, Budgets và Cost Explorer hỗ trợ triển khai, bảo mật, quan sát và vận hành.

## 4. Hai CloudFront distributions

Em tách CloudFront thành hai distribution:

- **Web distribution:** origin mặc định là S3 frontend và behavior **/menu/** trỏ đến S3 menu images;
- **API distribution:** origin là Application Load Balancer và tắt cache cho request API.

Cách tách này tránh xung đột giữa route React **/menu** và endpoint FastAPI **/menu**, đồng thời giữ HTTPS ở phía người dùng mà không cần mua custom domain.

## 5. High Availability

- ALB được đặt trên hai public subnet tại hai Availability Zone;
- Auto Scaling Group duy trì hai EC2 ở hai private subnet;
- Launch Template giúp instance mới có cùng AMI, IAM profile, user data và Docker configuration;
- RDS Multi-AZ tạo standby đồng bộ và hỗ trợ failover managed;
- ALB health check dùng endpoint **/health** để loại target không khỏe;
- target tracking scaling policy theo dõi CPU trung bình của Auto Scaling Group.

## 6. Kết quả triển khai

Frontend và API hoạt động qua CloudFront. Hai EC2 đều ở trạng thái Running và vượt qua 3/3 status checks. API health trả về service **quickbite-api**, version **1.4.0**. Admin dashboard, menu, upload ảnh, CloudWatch alarms và SNS email đều có bằng chứng thực tế trong phần Validation.
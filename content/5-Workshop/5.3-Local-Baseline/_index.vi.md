---
title: "Baseline local"
date: 2026-07-31
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
# Baseline local

## 1. Kiến trúc trước khi deploy

Trước khi tạo tài nguyên AWS, em hoàn thiện QuickBite ở local với bốn container chính:

| Thành phần | Công nghệ |
|---|---|
| Frontend | React, TypeScript và Vite |
| Backend | FastAPI và Uvicorn |
| Database | PostgreSQL |
| Email mock | Mailpit |

Docker Compose giúp em kiểm tra ứng dụng trong một môi trường gần giống production trước khi chuyển database sang RDS và backend sang EC2.

## 2. Các luồng đã kiểm tra

- đăng ký, đăng nhập và khôi phục phiên;
- phân quyền customer, admin, kitchen và delivery;
- tìm kiếm, lọc, sắp xếp và thêm món vào giỏ;
- tạo đơn COD và mock e-wallet;
- tính subtotal, delivery fee, tax và total ở server;
- admin xác nhận và quản lý đơn;
- kitchen chuyển preparing và ready;
- delivery hoàn tất giao hàng;
- order tracking bằng code và token;
- dashboard, CSV report và audit log;
- upload ảnh có kiểm tra định dạng và dung lượng;
- email mock qua Mailpit;
- health endpoint và automated tests.

## 3. Thay đổi để phù hợp AWS

Từ baseline local, em tách các thành phần theo dịch vụ:

- PostgreSQL local được thay bằng RDS PostgreSQL;
- frontend production build được đưa lên S3 và CloudFront;
- backend Docker image được push lên ECR;
- backend chạy trên EC2 Auto Scaling Group;
- file ảnh chuyển sang S3 menu-images;
- secret chuyển từ file local sang Secrets Manager;
- SSH được thay bằng SSM Session Manager;
- stdout/stderr container được đưa vào CloudWatch Logs.

## 4. Lý do giữ baseline local

Baseline local giúp em phân biệt lỗi ứng dụng và lỗi hạ tầng. Khi một lỗi xuất hiện sau khi deploy, em có thể so sánh với kết quả local để xác định vấn đề nằm ở code, environment variable, network, IAM, CloudFront, database hay công cụ CLI.
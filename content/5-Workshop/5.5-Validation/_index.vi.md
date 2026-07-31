---
title: "Kiểm thử và xác thực"
date: 2026-07-31
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---
# Kiểm thử và xác thực

## 1. Bằng chứng hạ tầng

| Thành phần | Kết quả xác thực |
|---|---|
| CloudFront | Hai distributions ở trạng thái Enabled |
| EC2 / ASG | Hai t3.micro tại ap-southeast-1a và ap-southeast-1b, 3/3 checks passed |
| API | Health endpoint trả status ok, service quickbite-api, version 1.4.0 |
| ECR | Repository quickbite-backend tồn tại, AES-256 |
| S3 | Web, menu-images và tfstate buckets tồn tại |
| IAM | Roles cho Auto Scaling, ELB, RDS và application instances được tạo |
| CloudWatch | CPU alarm và target tracking high/low alarms hoạt động |
| SNS | Email nhận được thông báo khi alarm chuyển trạng thái |
| Application | Customer menu, admin dashboard và image upload hoạt động |

## 2. API health check

<img src="/images/5-Workshop/api-healthcheck.png" alt="QuickBite API health check" width="100%" />
<p><em>CloudFront API domain trả JSON status ok, service quickbite-api và version 1.4.0.</em></p>

Health check xác nhận CloudFront API distribution, ALB, target group, EC2 container và FastAPI đều hoạt động trong cùng một request path.

## 3. Storefront và luồng customer

<img src="/images/5-Workshop/customer-menu.png" alt="QuickBite customer menu" width="100%" />
<p><em>Storefront được phân phối qua CloudFront, hỗ trợ menu, tìm kiếm, lọc, giỏ hàng, tra cứu và đăng nhập.</em></p>

Em kiểm tra menu, filter, sort, cart, login, tạo đơn và order tracking. Frontend gọi API qua CloudFront API domain nên không gặp mixed content.

## 4. Admin dashboard

<img src="/images/5-Workshop/admin-dashboard.png" alt="QuickBite admin dashboard" width="100%" />
<p><em>Dashboard quản trị đọc dữ liệu từ RDS qua FastAPI và hiển thị số đơn, doanh thu, trạng thái và món bán chạy.</em></p>

Dashboard hiển thị tổng đơn, tổng doanh thu, doanh thu trong ngày, pending orders, biểu đồ trạng thái và top món. Điều này xác nhận frontend, backend, database và role-based authorization hoạt động end-to-end.

## 5. Upload ảnh món

<img src="/images/5-Workshop/menu-image-upload.png" alt="Upload ảnh món trên QuickBite" width="100%" />
<p><em>Admin upload ảnh qua FastAPI; object được lưu trong S3 private menu-images và được hiển thị lại qua CloudFront behavior /menu/*.</em></p>

Luồng upload đã được kiểm tra từ giao diện admin. Backend dùng EC2 IAM role để PutObject; URL trả về dùng CloudFront web distribution thay vì public S3 URL.

## 6. CloudWatch và Auto Scaling alarms

<img src="/images/5-Workshop/cloudwatch-alarms.png" alt="CloudWatch alarms của QuickBite" width="100%" />
<p><em>CloudWatch hiển thị CPU alarm và hai target tracking alarms của Auto Scaling Group.</em></p>

Các alarm gồm:

- **quickbite-cpu-high:** CPU trung bình lớn hơn 70% trong 10 phút;
- **TargetTracking AlarmHigh:** hỗ trợ scale out;
- **TargetTracking AlarmLow:** hỗ trợ scale in.

<img src="/images/5-Workshop/cloudwatch-overview.png" alt="QuickBite CloudWatch overview" width="100%" />
<p><em>CloudWatch Overview hiển thị metric CPU và trạng thái alarm của dịch vụ EC2.</em></p>

## 7. SNS email

<img src="/images/5-Workshop/sns-email.png" alt="CloudWatch alarm email delivered through SNS" width="100%" />
<p><em>The email records quickbite-cpu-high changing from INSUFFICIENT_DATA to OK, including its threshold and state-change time.</em></p>

Email chứng minh SNS subscription đã được xác nhận và CloudWatch action có thể gửi thông báo ra ngoài AWS Console.

## 8. Kiểm thử nghiệp vụ

### Customer

- xem menu và ảnh;
- đăng nhập;
- tạo đơn;
- thanh toán COD hoặc mock e-wallet;
- theo dõi trạng thái và tra cứu đơn.

### Admin

- xem dashboard;
- quản lý món và danh mục;
- upload ảnh;
- xác nhận và theo dõi đơn;
- xuất báo cáo và xem audit log.

### Kitchen

- xem đơn đã xác nhận;
- chuyển trạng thái preparing và ready.

### Delivery

- xem đơn ready;
- hoàn tất giao hàng.

## 9. Xác thực bảo mật

- truy cập trực tiếp S3 không được dùng làm đường public chính;
- EC2 không có public workload endpoint; traffic ứng dụng đi qua ALB;
- RDS nằm trong isolated subnets và chỉ nhận kết nối từ App Security Group;
- secret không nằm trong Terraform source hoặc Docker image;
- EC2 dùng IAM role thay vì access key;
- SSM thay cho SSH;
- screenshot không chứa credential đang hoạt động.
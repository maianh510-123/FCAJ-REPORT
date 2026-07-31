---
title: "Bản đề xuất"
date: 2026-07-31
weight: 2
chapter: false
pre: " <b> 2. </b> "
includeInReport: false
---
# Đề xuất dự án QuickBite trên AWS

## 1. Tổng quan dự án

QuickBite là nền tảng đặt món và vận hành căng-tin dành cho bốn nhóm người dùng: khách hàng, quản trị viên, nhân viên bếp và nhân viên giao hàng. Hệ thống hỗ trợ xem menu, tạo và theo dõi đơn hàng, quản lý món, xử lý đơn theo vai trò, báo cáo doanh thu, audit log và upload ảnh món.

Ban đầu, QuickBite được xây dựng và kiểm thử trên máy local với **React/Vite**, **FastAPI**, **PostgreSQL**, **Docker Compose** và **Mailpit**. Sau khi hoàn thiện các luồng nghiệp vụ chính, em chuyển hệ thống lên AWS theo kiến trúc High Availability hai Availability Zone và quản lý hạ tầng bằng Infrastructure as Code với **Terraform**.

Mục tiêu của đề xuất không chỉ là đưa ứng dụng lên cloud, mà còn xây dựng một môi trường có thể triển khai lại, theo dõi được, có kiểm soát chi phí và phù hợp với các nguyên tắc của AWS Well-Architected Framework.

## 2. Bối cảnh và bài toán cần giải quyết

Phiên bản local đáp ứng chức năng nhưng chưa phù hợp với một môi trường cloud có thể truy cập và vận hành ổn định:

1. Frontend, backend và database chưa được tách theo trách nhiệm vận hành.
2. Ứng dụng chưa có lớp phân phối nội dung qua HTTPS.
3. Backend chưa có cân bằng tải, Auto Scaling và cơ chế thay thế instance không khỏe.
4. Database chưa có bản standby đồng bộ giữa hai Availability Zone.
5. Ảnh món chưa được lưu trong object storage riêng.
6. Secret và quyền truy cập chưa được quản lý tập trung.
7. Log, metric, alarm và email thông báo chưa được thu thập tự động.
8. Việc tạo, thay đổi và xóa hạ tầng còn phụ thuộc nhiều vào thao tác thủ công.
9. Chi phí của các tài nguyên như NAT Gateway, ALB và RDS cần được theo dõi từ đầu.
10. Hệ thống cần có bằng chứng triển khai thực tế thay vì chỉ dừng ở sơ đồ kiến trúc.

## 3. Phạm vi dự án

### 3.1. Phạm vi chức năng

QuickBite bao gồm các luồng chính sau:

- Khách hàng xem menu, tìm kiếm món, tạo đơn và theo dõi trạng thái.
- Quản trị viên quản lý món, danh mục, đơn hàng, báo cáo và audit log.
- Nhân viên bếp tiếp nhận và cập nhật trạng thái chế biến.
- Nhân viên giao hàng tiếp nhận và hoàn tất đơn.
- Backend lưu đơn hàng, trạng thái, lịch sử thao tác và dữ liệu liên quan trong PostgreSQL.
- Ảnh món được upload qua backend, lưu tại S3 và phân phối qua CloudFront.

### 3.2. Phạm vi hạ tầng

Phần hạ tầng được triển khai tại **ap-southeast-1** và bao gồm:

- Hai Availability Zone.
- Public subnet, private application subnet và isolated database subnet cho mỗi AZ.
- Application Load Balancer, EC2 Auto Scaling Group và RDS PostgreSQL Multi-AZ.
- Hai CloudFront distributions, S3 private buckets, ECR, Secrets Manager, Systems Manager, CloudWatch và SNS.
- Terraform remote state trên S3 và state locking bằng DynamoDB.

### 3.3. Ngoài phạm vi hiện tại

Các nội dung sau chưa được xem là thành phần chính thức của bản demo:

- Custom domain và Route 53.
- AWS WAF.
- Pipeline CI/CD hoàn chỉnh.
- Forced RDS failover và failure injection có kiểm soát.
- Chiến lược backup liên vùng cho môi trường production.

## 4. Mục tiêu và tiêu chí thành công

### 4.1. Mục tiêu chức năng

- Duy trì đầy đủ luồng customer → admin → kitchen → delivery.
- Cung cấp dashboard, báo cáo, audit log và order tracking.
- Upload ảnh món qua backend và hiển thị ảnh qua CloudFront.
- Cung cấp endpoint health check để ALB và người vận hành kiểm tra trạng thái dịch vụ.
- Đảm bảo frontend có thể gọi API qua HTTPS mà không gặp lỗi CORS hoặc mixed content.

### 4.2. Mục tiêu phi chức năng

- Có ít nhất hai EC2 hoạt động tại hai Availability Zone.
- ALB phân phối request đến các target khỏe.
- RDS PostgreSQL chạy Multi-AZ và được mã hóa.
- EC2 không cần public SSH; quản trị bằng Systems Manager Session Manager.
- S3 frontend và menu images ở trạng thái private, chỉ được phân phối qua CloudFront OAC.
- Log và metric được gửi đến CloudWatch.
- Alarm gửi thông báo qua SNS email.
- Hạ tầng có thể tạo lại và xóa theo vòng đời Terraform.

### 4.3. Tiêu chí nghiệm thu

Dự án được xem là hoàn thành khi:

- Frontend truy cập được bằng HTTPS qua CloudFront.
- Endpoint **/health** trả về trạng thái **ok**.
- Hai EC2 ở trạng thái running và vượt qua status check.
- ALB target group có target healthy.
- RDS hoạt động với cấu hình Multi-AZ.
- Backend pull image từ ECR thành công.
- Người quản trị upload ảnh món và ảnh được lưu, phân phối đúng.
- Dashboard quản trị hiển thị dữ liệu đơn hàng và doanh thu.
- CloudWatch ghi nhận log, metric và alarm.
- SNS gửi được email khi trạng thái alarm thay đổi.
- Sau triển khai, Terraform plan không phát hiện thay đổi ngoài dự kiến.

## 5. Dịch vụ AWS sử dụng

| Dịch vụ | Cấu hình đã triển khai | Vai trò trong QuickBite |
|---|---|---|
| Amazon CloudFront | 2 distributions | Phân phối frontend, ảnh món và API qua HTTPS |
| Amazon S3 | Web bucket, menu-images bucket và tfstate bucket | Lưu static assets, ảnh món và Terraform remote state |
| Application Load Balancer | Internet-facing trên 2 public subnets | Nhận request từ CloudFront API origin và phân phối đến EC2 |
| Amazon EC2 | 2 instance t3.micro tại ap-southeast-1a và ap-southeast-1b | Chạy container FastAPI |
| Auto Scaling Group | min 2, desired 2, max 4 | Duy trì capacity, thay thế instance không khỏe và hỗ trợ scale |
| Amazon RDS for PostgreSQL | db.t3.micro, Multi-AZ, encrypted | Lưu dữ liệu nghiệp vụ và duy trì standby đồng bộ |
| Amazon ECR | Repository quickbite-backend | Lưu Docker image backend theo version |
| AWS Secrets Manager | Secret dùng chung cho ứng dụng | Lưu DATABASE_URL và JWT secret |
| AWS Systems Manager | Session Manager | Quản trị EC2 không cần mở SSH |
| AWS IAM | EC2 instance role và service-linked roles | Cấp quyền theo nguyên tắc least privilege |
| Amazon CloudWatch | Log group, CPU alarm và target-tracking alarms | Thu thập log, metric và theo dõi tình trạng hệ thống |
| Amazon SNS | Email subscription | Gửi thông báo alarm |
| AWS Budgets | Cảnh báo ngân sách | Kiểm soát chi phí trong thời gian triển khai |
| AWS Cost Explorer | Phân tích chi phí | Theo dõi mức sử dụng theo dịch vụ và thời gian |
| Amazon DynamoDB | Terraform state lock table | Tránh nhiều tiến trình cùng cập nhật state |
| Internet Gateway và NAT Gateway | IGW dùng cho public layer, NAT theo mỗi AZ | Cung cấp đường ra phù hợp cho public và private resources |

## 6. Thiết kế kiến trúc và thành phần

<img src="/images/2-Proposal/quickbite-aws-architecture-ha.png" alt="Kiến trúc QuickBite High Availability trên AWSe" width="100%" />
<p><em>Kiến trúc QuickBite được triển khai tại ap-southeast-1 bằng Terraform, sử dụng hai Availability Zone, ALB, EC2 Auto Scaling, RDS Multi-AZ, CloudFront, S3, ECR, Secrets Manager, Systems Manager, CloudWatch và SNS..</em></p>

### 6.1. Global edge và frontend

Người dùng truy cập QuickBite bằng HTTPS qua CloudFront. Frontend React được build thành static assets và lưu trong S3 private. CloudFront sử dụng Origin Access Control để đọc dữ liệu từ S3 mà không cần public bucket.

Một CloudFront distribution phục vụ frontend và ảnh món. Behavior **/menu/** được dùng để phân phối ảnh từ menu-images bucket. Việc tách origin giúp frontend và ảnh vẫn được quản lý riêng nhưng người dùng truy cập qua cùng lớp HTTPS.

### 6.2. API và cân bằng tải

Frontend gọi API qua CloudFront API distribution riêng. Cách tách này tránh xung đột giữa SPA route **/menu** và API route có cùng tiền tố.

CloudFront chuyển request đến Application Load Balancer. ALB hoạt động trên hai public subnet và chuyển request đến các EC2 private qua cổng 8000. Health check giúp ALB chỉ gửi request tới target đang hoạt động bình thường.

### 6.3. Application layer

Backend FastAPI được đóng gói thành Docker image và lưu trong Amazon ECR. Launch Template cung cấp cấu hình khởi tạo cho Auto Scaling Group.

Auto Scaling Group duy trì tối thiểu hai EC2 tại hai Availability Zone, desired capacity bằng hai và có thể mở rộng tối đa bốn instance. EC2 không cần public IP và không mở SSH; việc quản trị được thực hiện qua Systems Manager Session Manager.

### 6.4. Data layer

RDS PostgreSQL được đặt trong isolated database subnets. Primary và standby nằm ở hai Availability Zone khác nhau và sử dụng synchronous replication.

Security Group của RDS chỉ cho phép kết nối TCP 5432 từ Security Group của EC2. Database không nhận kết nối trực tiếp từ Internet hoặc từ ALB.

### 6.5. Storage và image upload

Frontend static assets và menu images được lưu ở hai S3 bucket khác nhau. Khi quản trị viên upload ảnh món:

1. Frontend gửi file đến API.
2. FastAPI kiểm tra quyền và nội dung upload.
3. Backend ghi object vào menu-images bucket.
4. URL CloudFront được lưu cùng dữ liệu món.
5. Người dùng xem ảnh qua CloudFront thay vì truy cập S3 trực tiếp.

### 6.6. Security và secret management

DATABASE_URL và JWT secret được lưu trong Secrets Manager. EC2 nhận quyền đọc secret thông qua instance role.

IAM role cũng giới hạn quyền theo nhu cầu thực tế: pull image từ ECR, đọc secret, ghi ảnh vào S3, gửi log đến CloudWatch và kết nối Systems Manager. Cách này giúp loại bỏ nhu cầu đặt access key dài hạn trong EC2 hoặc source code.

### 6.7. Monitoring và notification

Container stdout/stderr được chuyển đến CloudWatch Logs. CloudWatch theo dõi CPU và các metric liên quan đến Auto Scaling.

Khi alarm chuyển trạng thái, SNS gửi email cho người vận hành. Cơ chế này giúp em kiểm tra hệ thống mà không cần liên tục mở AWS Console.

## 7. Infrastructure as Code với Terraform

### 7.1. Bootstrap stack

Bootstrap stack sử dụng local state để tạo các tài nguyên nền tảng:

- S3 bucket có versioning và encryption cho Terraform remote state.
- DynamoDB table cho state locking.
- ECR repository cho backend image.

Sau khi bootstrap hoàn thành, main stack chuyển sang sử dụng remote state để tăng tính an toàn và khả năng cộng tác.

### 7.2. Main stack

Main stack được chia thành ba module:

- **Network module:** VPC, hai Availability Zone, public subnets, private application subnets, isolated database subnets, Internet Gateway, NAT Gateways, route tables và Security Groups.
- **Data module:** RDS PostgreSQL Multi-AZ, DB subnet group và Secrets Manager.
- **Application module:** S3, CloudFront OAC, hai CloudFront distributions, ALB, Launch Template, Auto Scaling Group, IAM role, CloudWatch, SNS và Systems Manager integration.

### 7.3. Lợi ích khi sử dụng Terraform

- Hạ tầng được mô tả bằng code và có thể review trước khi apply.
- Terraform plan giúp phát hiện thay đổi trước khi triển khai.
- Remote state và state locking giảm rủi ro cập nhật đồng thời.
- Module hóa giúp tách network, data và application.
- Tag mặc định hỗ trợ truy vết tài nguyên và phân tích chi phí.
- Terraform destroy hỗ trợ dọn dẹp toàn bộ môi trường theo đúng vòng đời.

## 8. Triển khai kỹ thuật

### 8.1. Các giai đoạn triển khai

| Giai đoạn | Nội dung chính | Kết quả |
|---|---|---|
| 1. Khảo sát và ổn định local | Rà soát source, hoàn thiện role flow, Docker Compose và PostgreSQL | Baseline local chạy được end-to-end |
| 2. Thiết kế cloud | Xác định VPC, subnet, ALB, ASG, RDS Multi-AZ, S3, CloudFront và monitoring | Hoàn thiện kiến trúc mục tiêu |
| 3. Chuẩn bị IaC | Tạo bootstrap stack, remote state, ECR và Terraform modules | Hạ tầng có thể triển khai lặp lại |
| 4. Triển khai data và application | Apply network/data/app, push Docker image, khởi tạo database | Backend chạy trên EC2 và kết nối RDS |
| 5. Triển khai frontend | Build React, sync lên S3, cấu hình CloudFront | Website hoạt động qua HTTPS |
| 6. Kiểm thử chức năng | Kiểm tra login, menu, order flow, dashboard và upload ảnh | Luồng nghiệp vụ hoạt động trên AWS |
| 7. Monitoring và cost control | CloudWatch Logs, alarms, SNS, Budgets và Cost Explorer | Có observability và cảnh báo chi phí |
| 8. Hoàn thiện báo cáo | Thu thập ảnh console, diagram và kết quả kiểm thử | Có bộ bằng chứng triển khai thực tế |

### 8.2. Yêu cầu kỹ thuật

- Windows 11 và PowerShell cho môi trường thao tác.
- AWS CLI v2 để xác thực và kiểm tra tài nguyên.
- Terraform để quản lý hạ tầng.
- Docker Desktop để build và kiểm tra image backend.
- Node.js và npm để build frontend.
- Quyền IAM đủ để tạo VPC, EC2, ALB, RDS, S3, CloudFront, ECR, IAM, Secrets Manager, SSM, CloudWatch, SNS, DynamoDB và Budgets.
- Region triển khai: **ap-southeast-1**.
- Docker image phải được push lên ECR trước khi Auto Scaling Group khởi tạo application instances.
- Database schema và seed data phải được nạp trước khi kiểm thử nghiệp vụ.

## 9. Lộ trình và mốc triển khai

| Thời gian | Trọng tâm | Mốc đạt được |
|---|---|---|
| Tuần 1 | Khảo sát yêu cầu, source code và AWS fundamentals | Xác định phạm vi QuickBite |
| Tuần 2 | Hoàn thiện luồng nghiệp vụ và PostgreSQL | Local baseline ổn định |
| Tuần 3 | Docker hóa, kiểm thử và rà soát bảo mật | Backend/frontend chạy đồng nhất |
| Tuần 4 | Thiết kế kiến trúc và đánh giá chi phí | Chốt kiến trúc HA hai AZ |
| Tuần 5 | Terraform bootstrap và network module | Có remote state, ECR và VPC |
| Tuần 6 | Data module và application module | Có RDS, ALB, ASG và EC2 |
| Tuần 7 | Deploy frontend, CloudFront và kiểm thử chức năng | Website và API hoạt động qua HTTPS |
| Tuần 8 | Monitoring, alarm, cost control và bằng chứng | Hoàn thiện hệ thống và báo cáo |

## 10. Ước tính ngân sách và kiểm soát chi phí

### 10.1. Mục tiêu ngân sách

Ngân sách triển khai được kiểm soát trong phạm vi AWS credits của chương trình và đặt mục tiêu không vượt quá **200 USD credits** cho toàn bộ giai đoạn workshop.

Chi phí thực tế phụ thuộc vào thời gian tài nguyên hoạt động, lưu lượng, số request và dung lượng log. Vì vậy, em sử dụng AWS Budgets và Cost Explorer để theo dõi chi phí thực tế thay vì khẳng định một con số cố định ngay từ đầu.

### 10.2. Nhóm chi phí chính

| Nhóm tài nguyên | Yếu tố tạo chi phí | Cách kiểm soát |
|---|---|---|
| EC2 Auto Scaling | Số instance và thời gian chạy | Dùng t3.micro, desired capacity bằng 2 và destroy sau demo |
| RDS Multi-AZ | Instance database, storage và standby | Dùng db.t3.micro, giới hạn storage và thời gian vận hành |
| NAT Gateway | Số NAT Gateway, thời gian và dữ liệu xử lý | Theo dõi Cost Explorer; chỉ giữ trong thời gian cần thiết |
| Application Load Balancer | Thời gian chạy và LCU | Chỉ dùng một ALB cho API |
| CloudFront | Request và data transfer | Cache static assets và ảnh món |
| S3 | Dung lượng và request | Tách bucket, xóa object thử nghiệm khi không còn cần |
| ECR | Dung lượng image | Xóa image tag cũ và chỉ giữ version cần thiết |
| CloudWatch | Log ingestion và retention | Giới hạn retention và tránh log debug kéo dài |
| Secrets Manager | Số secret | Dùng một secret ứng dụng có kiểm soát |
| SNS | Số notification | Chỉ gửi alarm cần thiết |

### 10.3. Biện pháp kiểm soát

- Thiết lập AWS Budget và email cảnh báo.
- Dùng tag chung cho toàn bộ tài nguyên.
- Kiểm tra Cost Explorer theo dịch vụ.
- Dùng instance nhỏ phù hợp với tải demo.
- Giới hạn log retention.
- Không triển khai custom domain, WAF hoặc dịch vụ không cần thiết cho phạm vi hiện tại.
- Dùng Terraform destroy khi kết thúc giai đoạn demo.

## 11. Đánh giá rủi ro

| Rủi ro | Ảnh hưởng | Khả năng | Biện pháp giảm thiểu |
|---|---|---|---|
| Vượt ngân sách | Cao | Trung bình | Budgets, Cost Explorer, tag, right-sizing và destroy |
| Lộ credential | Cao | Thấp | IAM role, Secrets Manager, không dùng root key trong ứng dụng |
| Một EC2 hoặc một AZ gặp lỗi | Trung bình | Thấp | ALB, ASG min 2 và triển khai hai AZ |
| Database gặp sự cố | Cao | Thấp | RDS Multi-AZ, standby đồng bộ và Security Group riêng |
| CloudFront route xung đột | Trung bình | Trung bình | Tách web/images và API thành hai distributions |
| Upload ảnh thất bại | Trung bình | Trung bình | IAM policy giới hạn đúng bucket, kiểm tra API và CloudFront behavior |
| EC2 không pull được image | Cao | Trung bình | ECR permission, NAT Gateway và health check |
| Thiếu log khi có lỗi | Trung bình | Trung bình | CloudWatch Logs, metric và alarm |
| Terraform state bị cập nhật đồng thời | Cao | Thấp | Remote state trên S3 và DynamoDB state lock |
| Dữ liệu cần giữ bị xóa khi cleanup | Cao | Thấp | Xác nhận dữ liệu trước khi chạy Terraform destroy |

### Kế hoạch dự phòng

- Nếu một EC2 lỗi, ALB loại target không khỏe và ASG tạo instance thay thế.
- Nếu một Availability Zone lỗi, hệ thống còn application capacity ở AZ còn lại.
- Nếu RDS primary gặp lỗi, Multi-AZ hỗ trợ failover sang standby.
- Nếu phiên bản mới không ổn định, có thể deploy lại Docker image version trước từ ECR.
- Nếu chi phí tăng nhanh, dừng môi trường demo hoặc destroy stack sau khi đã lưu bằng chứng cần thiết.

## 12. Áp dụng AWS Well-Architected Framework

| Trụ cột | Cách em áp dụng |
|---|---|
| Operational Excellence | Terraform, remote state, state locking, module hóa, health check, CloudWatch và tài liệu triển khai |
| Security | S3 private + OAC, EC2 private, RDS isolated, Security Group chaining, IAM role, Secrets Manager và SSM |
| Reliability | Hai Availability Zone, ALB, ASG min 2, health check, RDS Multi-AZ và tự động thay thế instance |
| Performance Efficiency | CloudFront cache, ALB phân phối tải, ASG target tracking và t3.micro phù hợp tải demo |
| Cost Optimization | AWS Budgets, Cost Explorer, right-sizing, log retention và Terraform destroy |
| Sustainability | Managed services, Auto Scaling, kiểm soát retention và dọn môi trường sau demo |

## 13. Kết quả kỳ vọng

### 13.1. Kết quả kỹ thuật

- QuickBite hoạt động công khai qua HTTPS.
- Frontend, backend, database và object storage được tách theo trách nhiệm.
- Backend có hai EC2 tại hai Availability Zone và được bảo vệ sau ALB.
- Database sử dụng RDS PostgreSQL Multi-AZ.
- Ảnh món được lưu tại S3 private và phân phối qua CloudFront.
- Secret được quản lý bằng Secrets Manager.
- Log, metric, alarm và email notification hoạt động.
- Hạ tầng có thể tạo lại bằng Terraform.

### 13.2. Giá trị vận hành

- Giảm phụ thuộc vào thao tác cấu hình tay.
- Có khả năng truy vết thay đổi hạ tầng.
- Có cơ chế kiểm tra sức khỏe và cảnh báo sự cố.
- Có khả năng kiểm soát chi phí theo dịch vụ.
- Có nền tảng để bổ sung CI/CD, WAF, custom domain và backup trong giai đoạn tiếp theo.

### 13.3. Sản phẩm bàn giao

- Source code QuickBite.
- Website QuickBite triển khai trên AWS.
- Kiến trúc AWS High Availability.
- Terraform code cho bootstrap và main stack.
- Bộ ảnh minh chứng AWS Console và demo.
- Báo cáo FCAJ song ngữ Việt–Anh.

## 14. Kết luận

Kết quả kỳ vọng của đề xuất là biến QuickBite từ một ứng dụng local thành một hệ thống cloud có kiến trúc rõ ràng, có tính sẵn sàng, bảo mật, giám sát và khả năng triển khai lặp lại. Việc sử dụng Terraform giúp phần hạ tầng trở thành một thành phần của sản phẩm thay vì chỉ là các thao tác cấu hình rời rạc trên AWS Console.
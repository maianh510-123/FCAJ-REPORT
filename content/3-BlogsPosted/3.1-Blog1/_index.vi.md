---
title: "Blog 1"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Amazon GuardDuty vs Amazon Inspector: Đâu là giải pháp bảo mật phù hợp?

Trong hệ sinh thái AWS, **Amazon GuardDuty** và **Amazon Inspector** là hai dịch vụ bảo mật hàng đầu nhưng lại phục vụ hai mục đích hoàn toàn khác nhau: **Phát hiện mối đe dọa (Threat Detection)** và **Quản lý lỗ hổng (Vulnerability Management)**. Việc kết hợp cả hai công cụ tạo nên chiến lược "Phòng thủ chiều sâu" (Defense-in-Depth) tối ưu cho mọi hệ thống đám mây.

---

### Các điểm chính cần nắm:

* **GuardDuty – "Camera an ninh 24/7" (Real-time Threat Detection):**
  * **Cơ chế:** Phân tích thụ động các luồng dữ liệu log (CloudTrail, VPC Flow Logs, DNS Logs, EKS Audit Logs, S3 Event Logs) bằng Machine Learning và dữ liệu Threat Intelligence.
  * **Mục tiêu:** Phát hiện hành vi bất thường và mã độc ngay tại thời điểm thực thi (Runtime).
  * **Ví dụ:** Cảnh báo lộ IAM Access Key bị gọi từ IP lạ, EC2/EKS bị nhiễm malware đào tiền ảo, hoặc các truy vấn bất thường trên S3/RDS.

* **Inspector – "Bác sĩ khám sức khỏe" (Vulnerability Management):**
  * **Cơ chế:** Chủ động "soi" sâu vào cấu hình, hệ điều hành và gói phần mềm để đối chiếu với cơ sở dữ liệu lỗ hổng bảo mật toàn cầu (CVE).
  * **Mục tiêu:** Rà soát và tìm ra điểm yếu trước khi kẻ xấu kịp khai thác (Scan-time).
  * **Ví dụ:** Quét lỗ hổng thư viện (Log4j, OpenSSL,...) trên EC2/ECR, đánh giá mã nguồn Lambda, cảnh báo mở cổng internet vô tội vạ.

* **Bảng so sánh nhanh:**
  * **Cách tiếp cận:** GuardDuty đọc log thụ động Inspector quét sâu vào tài nguyên.
  * **Phạm vi:** GuardDuty bao phủ toàn tài khoản (IAM, S3, RDS, EKS,...) Inspector tập trung vào EC2 (EBS), ECR Image, Lambda code.
  * **Câu hỏi giải quyết:** GuardDuty trả lời *"Có ai đang tấn công tôi không?"*  Inspector trả lời *"Hệ thống của tôi có lỗ hổng nào dễ bị khai thác không?"*.

---

### Bài học thực chiến & Kiến trúc tối ưu:

1. **Không chọn 1 trong 2:** Nếu chỉ dùng Inspector, bạn vá hết lỗ hổng đã biết nhưng dễ "mù" trước các cuộc tấn công Zero-day hoặc lộ Key. Nếu chỉ dùng GuardDuty, bạn biết mình bị đánh nhưng nhà lại quá nhiều lỗ hổng để hacker ra vào.
2. **Shift-Left Security:** Tích hợp Amazon Inspector ngay vào quy trình **CI/CD Pipeline** khi build Docker Image hoặc Deploy code Lambda để phát hiện lỗi từ sớm, giúp tiết kiệm tối đa chi phí sửa chữa trên Production.
3. **Quản trị tập trung:** Gom toàn bộ cảnh báo (Findings) từ cả hai dịch vụ về **AWS Security Hub** để phân loại độ ưu tiên và tự động hóa thông báo qua Slack/Telegram hoặc kích hoạt tự động vá lỗi (Automated Remediation).

---

## Bằng chứng
![alt text](/images/Post_1.png)
---

### Tài liệu tham khảo & Hướng dẫn triển khai

* 📄 **Trang chủ AWS Threat Detection & Response:** [AWS Detection and Response Solutions](https://aws.amazon.com/vi/products/security/detection-and-response/)
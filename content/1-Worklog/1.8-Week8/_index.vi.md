---
title: "Worklog Tuần 8"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Giai đoạn: Kiểm thử và Báo cáo (Tuần 8)

### Mục tiêu tuần 8:
* Phối hợp kiểm thử AWS Validation & Giám sát hệ thống (Amazon CloudWatch, Alarms).
* Biên soạn bài viết Blog kỹ thuật, chuẩn bị danh mục tài liệu tham khảo (References) và Evidence Checklist nghiệm thu.
* Bàn giao mã nguồn, bảo vệ dự án QuickBite.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Thực hiện kiểm thử toàn diện **AWS Validation**: Phối hợp kiểm thử liên thông End-to-End giữa Frontend CloudFront/S3 $\leftrightarrow$ API Gateway/Lambda $\leftrightarrow$ EC2/RDS <br> - Tích hợp công cụ giám sát lỗi Frontend tự động (Sentry / CloudWatch RUM) | 27/07/2026 | 27/07/2026 | <https://aws.amazon.com/> |
| 3 | - Phối hợp cấu hình **Amazon CloudWatch Dashboard & Alarms**: Cảnh báo độ trễ response API, tỷ lệ lỗi 4xx/5xx và lưu lượng truy cập Frontend | 28/07/2026 | 28/07/2026 | <https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html> |
| 4 | - Biên soạn bài viết **Blog Kỹ thuật dự án**: Tổng kết kiến trúc Frontend Next.js, bài học kinh nghiệm về UI/UX và quy trình Deploy CI/CD lên AWS Cloud | 29/07/2026 | 29/07/2026 | <https://storybook.js.org/> |
| 5 | - **Hoàn thiện báo cáo thực tập cá nhân**: Rà soát, tổng hợp Evidence Checklist (hình ảnh minh họa, log triển khai, link sản phẩm) và chuẩn hóa toàn bộ nội dung báo cáo | 30/07/2026 | 30/07/2026 | <https://github.com/> |
| 6 | - **Nộp báo cáo thực tập cá nhân** cho đơn vị thực tập FCAJ & Nhà trường <br> - Bàn giao toàn bộ mã nguồn chỉn chu trên GitHub kèm file `README.md`| 31/07/2026 | 31/07/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 8:
* Đạt tiêu chí kiểm thử AWS Validation liên thông toàn hệ thống Cloud Serverless.
* Tích hợp thành công công cụ theo dõi lỗi Sentry/CloudWatch RUM và thiết lập Alarms cảnh báo tự động.
* Hoàn thành bài viết Blog Kỹ thuật, danh mục References và bộ Evidence Checklist nghiệm thu đầy đủ.
* Đóng gói, bàn giao mã nguồn sạch sẽ trên GitHub và bảo vệ thành công dự án QuickBite tại FCAJ.
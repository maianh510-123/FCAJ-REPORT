---
title: "Worklog Tuần 7"
date: 2026-07-20
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Giai đoạn: Chuẩn bị triển khai (Tuần 6 - Tuần 7)

### Mục tiêu tuần 7:
* Phối hợp triển khai CI/CD Pipeline (GitHub Actions) tự động deploy Frontend.
* Tối ưu hóa hiệu năng Frontend (Core Web Vitals, Bundle Size) & Tinh chỉnh Responsive Web Design.
* Thực hiện dọn dẹp mã nguồn (Cleanup Code), xóa dữ liệu rác và chuẩn bị cho giai đoạn kiểm thử chính thức.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Cấu hình **GitHub Actions Workflow**: Tự động Linting, Type Check, Build và Sync tập tin tĩnh| 20/07/2026 | 20/07/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |
| 3 | - Phối hợp cấu hình tự động xóa Cache CloudFront mỗi khi có phiên bản Frontend mới deploy | 21/07/2026 | 21/07/2026 | <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |
| 4 | - Tối ưu hóa hiệu năng Frontend: Phân tích dung lượng bằng `@next/bundle-analyzer`, áp dụng Code Splitting & Dynamic Import (`next/dynamic`)| 22/07/2026 | 22/07/2026 |  |
| 5 | - Tinh chỉnh Responsive Web Design mượt mà trên thiết bị Mobile, Tablet, Desktop và tăng cường hiệu ứng Micro-interactions (Framer Motion) | 23/07/2026 | 23/07/2026 | <https://tailwindcss.com/docs/responsive-design> |
| 6 | - Tiến hành **Cleanup mã nguồn**: Xóa các file console.log, comment dư thừa, tối ưu hóa các linh kiện UI và dọn dẹp các tài nguyên thử nghiệm rác | 24/07/2026 | 24/07/2026 | <https://github.com/> |

### Kết quả đạt được tuần 7:
* Thiết lập thành công CI/CD Pipeline tự động deploy ứng dụng Frontend.
* Giao diện đạt độ tương thích mượt mà trên các nền tảng thiết bị.
* Mã nguồn Frontend được dọn dẹp sạch sẽ, chuẩn hóa kiến trúc sẵn sàng cho bước kiểm thử tổng thể.
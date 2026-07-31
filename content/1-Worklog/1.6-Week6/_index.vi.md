---
title: "Worklog Tuần 6"
date: 2026-07-13
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Giai đoạn: Chuẩn bị triển khai (Tuần 6 - Tuần 7)

### Mục tiêu tuần 6:
* Viết script và chuẩn bị file deploy tự động cho Frontend (Build scripts, Nginx config, Dockerfile).
* Cấu hình giải quyết triệt để lỗi giao thoa tên miền (CORS) & Lỗi Mixed Content (HTTP/HTTPS).
* Tích hợp giải pháp Upload ảnh món ăn.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Chuẩn bị script build tĩnh Next.js (`next build && next export` / SSG) và cấu hình file Nginx Server Block phục vụ hosting tập tin Frontend | 13/07/2026 | 13/07/2026 | <https://nextjs.org/docs> |
| 3 | - Xử lý triệt để lỗi **CORS (Cross-Origin Resource Sharing)**: Cấu hình Allowed Origins trên Backend FastAPI và AWS CloudFront Header Policies | 14/07/2026 | 14/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS> |
| 4 | - Xử lý triệt để lỗi **Mixed Content**: Đảm bảo 100% các request API và liên kết hình ảnh gọi qua chứng chỉ mã hóa HTTPS (AWS Certificate Manager - ACM) | 15/07/2026 | 15/07/2026 | |
| 5 | - Tích hợp tính năng upload ảnh món ăn: Gọi API xin Presigned URL từ Backend và tải file ảnh trực tiếp qua HTTP `PUT` request | 16/07/2026 | 16/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html> |
| 6 | - Tối ưu hiển thị hình ảnh món ăn với `next/image` và kiểm thử tải ảnh mượt mà từ S3 Bucket qua CloudFront CDN | 17/07/2026 | 17/07/2026 | <https://nextjs.org/docs/api-reference/next/image> |

### Kết quả đạt được tuần 6:
* Chuẩn bị hoàn tất các file deploy Frontend (Script build, Nginx config) sẵn sàng cho hạ tầng Cloud.
* Khắc phục hoàn toàn lỗi CORS giữa Frontend domain và Backend API domain.
* Loại bỏ triệt để lỗi Mixed Content, đảm bảo toàn bộ luồng giao tiếp chạy qua HTTPS an toàn.
* Tích hợp thành công tính năng Upload ảnh trực tiếp qua Presigned URL giúp giảm tải băng thông server.
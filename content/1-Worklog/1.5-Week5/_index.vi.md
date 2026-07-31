---
title: "Worklog Tuần 5"
date: 2026-07-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Giai đoạn: Thiết kế Cloud (Tuần 4 - Tuần 5)

### Mục tiêu tuần 5:
* Lập trình tính năng Real-time Tracking trên Frontend kết nối WebSockets / AWS API Gateway WebSocket.
* Xây dựng Canteen Live Dashboard nhận tín hiệu thông báo đơn hàng từ AWS Lambda.
* Hoàn thiện thiết kế tích hợp các dịch vụ Cloud cốt lõi: EC2, S3, CloudFront CDN và AWS RDS.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Khởi tạo kết nối WebSocket phía Client kết nối đến AWS API Gateway WebSocket <br> - Viết Custom Hook `useWebSocket` xử lý cơ chế tự động kết nối lại (Auto-reconnect with Exponential Backoff) khi rớt mạng | 06/07/2026 | 06/07/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-websocket-api.html> |
| 3 | - Lập trình màn hình Order Tracking Real-time: Progress Bar 4 bước (`PENDING` $\rightarrow$ `PREPARING` $\rightarrow$ `READY` $\rightarrow$ `COMPLETED` khớp với `models/order.py`) | 07/07/2026 | 07/07/2026 | <https://nextjs.org/docs> |
| 4 | - Xây dựng **Canteen Live Dashboard**: Lắng nghe sự kiện đẩy từ **AWS Lambda**, tự động cập nhật Card đơn hàng mới tức thì không cần reload trang | 08/07/2026 | 08/07/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/welcome.html> |
| 5 | - Tích hợp chuông báo âm thanh (Audio Alert) và Web Notification API phát Push Notification khi có đơn mới | 09/07/2026 | 09/07/2026 | <https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API> |
| 6 | - Phối hợp chốt sơ đồ thiết kế tích hợp tổng thể Cloud | 10/07/2026 | 10/07/2026 | <https://aws.amazon.com/cloudfront/> |

### Kết quả đạt được tuần 5:
* Màn hình Order Tracking và Canteen Live Dashboard phản hồi tức thì sự kiện chuyển trạng thái đơn từ AWS Lambda.
* Tích hợp thành công chuông báo âm thanh (Audio Alert) và Browser Push Notification cho Canteen.
* Thống nhất mô hình phân phối ứng dụng Frontend.
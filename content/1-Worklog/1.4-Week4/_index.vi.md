---
title: "Worklog Tuần 4"
date: 2026-06-29
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Giai đoạn: Thiết kế Cloud (Tuần 4 - Tuần 5)

### Mục tiêu tuần 4:
* Đóng góp xây dựng tài liệu Proposal & Kiến trúc Cloud theo tiêu chuẩn AWS Well-Architected Framework.
* Xây dựng State Management cho Giỏ hàng (Zustand Cart Store) & Form Checkout kết nối API Đặt hàng (`routers/orders.py`).
* Thiết kế mô hình hạ tầng mạng VPC và chuẩn bị tích hợp lưu trữ cơ sở dữ liệu AWS RDS (PostgreSQL).

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đóng góp nội dung Frontend & User Experience vào bản **Proposal Kiến trúc Cloud** của nhóm <br> - Nghiên cứu 6 trụ cột của AWS Well-Architected Framework (Operational Excellence, Security, Reliability, Performance, Cost, Sustainability) | 29/06/2026 | 29/06/2026 | <https://aws.amazon.com/architecture/well-architected/> |
| 3 | - Xây dựng **Zustand Cart Store**: Viết logic Thêm/Sửa/Xóa món, tự động tính tổng tiền khớp với `OrderItemSchema` (`schemas/order.py`) <br> - Lưu trữ giỏ hàng bền vững vào `localStorage` qua Zustand persist middleware | 30/06/2026 | 30/06/2026 | <https://zustand-demo.pmnd.rs/> |
| 4 | - Lập trình giao diện Trang Checkout: Form nhập vị trí nhận hàng & Tích hợp chuyển khoản QR tự động (VietQR API) <br> - Validate dữ liệu bằng React Hook Form + Zod Schema khớp với `OrderCreateSchema` | 01/07/2026 | 01/07/2026 ||
| 5 | - Kết nối RESTful API Đặt hàng (`POST /orders/` từ `routers/orders.py`) gửi dữ liệu về cơ sở dữ liệu AWS RDS PostgreSQL <br> - Lập trình giao diện Lịch sử & Chi tiết Đơn hàng (`GET /orders/`) bằng TanStack Query | 02/07/2026 | 02/07/2026 | <https://tanstack.com/query/latest> |
| 6 | - Phối hợp rà sơ đồ thiết kế VPC (Public/Private Subnets, NAT Gateway) đảm bảo các kết nối API từ Frontend đến Backend và RDS được bảo mật | 03/07/2026 | 03/07/2026 ||

### Kết quả đạt được tuần 4:
* Hoàn thiện phần đóng góp Frontend trong bản Proposal kiến trúc Cloud theo AWS Well-Architected Framework.
* Lập trình hoàn chỉnh Zustand Cart Store quản lý trạng thái giỏ hàng toàn cục, lưu trữ `localStorage` ổn định.
* Tích hợp thành công API Đặt hàng (`POST /orders/`) và API Lịch sử đơn hàng (`GET /orders/`)lưu trữ tại AWS RDS.
* Tích hợp VietQR API thanh toán tự động và React Hook Form + Zod schema validation chuẩn xác.
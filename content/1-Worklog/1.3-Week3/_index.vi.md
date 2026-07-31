---
title: "Worklog Tuần 3"
date: 2026-06-22
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Giai đoạn: Ổn định ứng dụng (Tuần 3)

### Mục tiêu tuần 3:
* Phối hợp đóng gói môi trường phát triển Frontend/Backend bằng Docker & Docker Compose.
* Lập trình các màn hình cốt lõi, kết nối RESTful API (`routers/auth.py`, `routers/menu.py`)sử dụng cơ sở dữ liệu PostgreSQL.
* Kiểm thử phân quyền người dùng (Role Flow: Customer vs Canteen Admin) và chạy thử nghiệm.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Viết `Dockerfile` tối ưu Multi-stage build cho dự án Frontend Next.js <br> - Phối hợp cấu hình `docker-compose.yml` chạy đồng thời Frontend, Backend FastAPI và PostgreSQL Database ở máy Local | 22/06/2026 | 22/06/2026 | <https://docs.docker.com/> |
| 3 | - Lập trình giao diện Login/Register, kết nối API Auth (`POST /auth/login`, `POST /auth/register` từ `routers/auth.py`)<br> - Xử lý lưu JWT Token an toàn và cấu hình Axios Interceptor tự động gán đính kèm Header | 23/06/2026 | 23/06/2026 | <https://nextjs.org/docs> |
| 4 | - Lập trình Trang chủ & Menu món ăn, kết nối API Menu (`GET /menu/items` từ `routers/menu.py`) <br> - Tích hợp thanh tìm kiếm Debounce Search Input giúp tìm món ăn mượt mà không bị spam API | 24/06/2026 | 24/06/2026 | <https://react.dev/> |
| 5 | - Lập trình trang Quản lý Menu dành cho Canteen Admin: Kết nối các API CRUD món ăn (`POST/PUT/DELETE /menu/items`) <br> - Kiểm thử **Role Flow**: Đảm bảo điều hướng chính xác giữa vai trò Khách hàng (Customer) và Quản lý Canteen (Admin) | 25/06/2026 | 25/06/2026 | <https://nextjs.org/docs> |
| 6 | - Thực hiện kiểm thử: Chạy toàn bộ luồng ứng dụng qua Docker Compose | 26/06/2026 | 26/06/2026 ||

### Kết quả đạt được tuần 3:
* Đóng gói thành công ứng dụng Frontend bằng Docker và phối hợp chạy trơn tru cùng Backend & PostgreSQL qua Docker Compose.
* Tích hợp thành công API Authentication (`routers/auth.py`), xử lý xác thực JWT Token và lưu trữ an toàn.
* Tích hợp trơn tru API Menu (`routers/menu.py`), hiển thị danh sách món ăn sinh động có hỗ trợ Debounce Search.
* Hoàn thiện màn hình Quản lý Menu Canteen và kiểm thử phân quyền người dùng (Role Flow) chính xác.
* Kiểm thử thành công.
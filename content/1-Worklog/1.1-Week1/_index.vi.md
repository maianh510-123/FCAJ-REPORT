---
title: "Worklog Tuần 1"
date: 2026-06-05
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Giai đoạn: Khảo sát và Scope (Tuần 1 - Tuần 2)

### Mục tiêu tuần 1:
* Onboarding, tiếp nhận các yêu cầu nghiệp vụ từ đơn vị thực tập First Cloud AI Journey (FCAJ).
* Review tổng quan cấu trúc mã nguồn Backend dự án QuickBite (`models/`, `routers/`, `schemas/`) và thiết lập Git Flow.
* Quản lý IAM User/Roles và áp dụng các nguyên tắc bảo mật đám mây cơ bản.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 5 | - Tham gia buổi Onboarding, tìm hiểu các yêu cầu nghiệp vụ của FCAJ đối với dự án QuickBite <br> - Đọc và cam kết tuân thủ quy định bảo mật, quy trình quản lý mã nguồn Git Flow (Commit convention, PR review) | 05/06/2026 | 05/06/2026 | |
| 2 | - Review chi tiết mã nguồn Backend QuickBite: Phân tích các router (`auth.py`, `menu.py`, `orders.py`) và mô hình Pydantic Schemas (`user.py`, `item.py`, `order.py`) <br> - Đánh giá luồng dữ liệu cần tích hợp cho giao diện người dùng (UI Client) | 08/06/2026 | 08/06/2026 | <https://github.com/> |
| 3 | - Đăng ký/Kích hoạt tài khoản AWS Free Tier và thiết lập phân quyền bảo mật IAM User/Roles | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - Tìm hiểu các tiêu chuẩn bảo mật IAM cơ bản (Principle of Least Privilege, MFA, IAM Access Keys Rotation) | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Khởi tạo máy chủ EC2 Ubuntu Server chạy thử nghiệm, mở Security Groups| 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - Tổng kết yêu cầu scope dự án Frontend, lập danh sách màn hình cần thiết kế UI/UX và tài liệu hóa cấu trúc API specs | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 1:
* Hoàn thành thủ tục Onboarding, nắm rõ yêu cầu nghiệp vụ từ FCAJ và quy chuẩn Git Flow của nhóm.
* Đọc hiểu toàn bộ cấu trúc Backend QuickBite (FastAPI, SQLAlchemy, Pydantic Schemas), xác định rõ các Endpoint API cần tích hợp.
* Tạo máy chủ EC2 Ubuntu Server thử nghiệm, cấu hình Security Group và kết nối SSH an toàn qua Key Pair.
* Chốt scope danh sách các màn hình giao diện Client/Admin cho dự án QuickBite.
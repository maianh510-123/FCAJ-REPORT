---
title: "Blog 2 - Disaster Recovery trên AWS"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---
# Disaster Recovery trên AWS: Không phải đợi hệ thống sập mới nghĩ đến phục hồi

**Trạng thái nội dung:** Hoàn thành  

Khi mới học deploy ứng dụng, mình thường chỉ tập trung vào câu hỏi: “Làm sao để hệ thống chạy được?” Nhưng sau khi tìm hiểu sâu hơn về cloud, mình nhận ra một câu hỏi khác quan trọng không kém: “Nếu hệ thống gặp sự cố thì phục hồi như thế nào?”

Đó là lúc **Disaster Recovery (DR)** trở nên rất thực tế.

Disaster Recovery là kế hoạch giúp workload phục hồi sau các sự cố nghiêm trọng như database hỏng, lỗi triển khai, mất dữ liệu, Availability Zone gặp vấn đề hoặc một thành phần chính không còn hoạt động. Với hệ thống thật, “chạy được” là chưa đủ. Hệ thống cần biết dữ liệu nào phải bảo vệ, có thể ngừng bao lâu và phục hồi theo các bước nào.

## Hai khái niệm quan trọng: RTO và RPO

### Recovery Time Objective (RTO)

RTO là khoảng thời gian tối đa chấp nhận được để đưa hệ thống trở lại hoạt động sau sự cố.

Ví dụ, nếu QuickBite có RTO là 4 giờ, đội vận hành cần có runbook và tài nguyên để phục hồi frontend, backend và database trong tối đa 4 giờ.

### Recovery Point Objective (RPO)

RPO là lượng dữ liệu tối đa có thể mất, thường được diễn tả bằng thời gian.

Nếu RPO là 24 giờ, hệ thống có thể chấp nhận mất dữ liệu phát sinh từ lần backup gần nhất trong vòng một ngày. Nếu RPO chỉ vài phút, cơ chế sao lưu hoặc replication phải thường xuyên hơn và chi phí cũng cao hơn.

RTO/RPO không nên được chọn theo cảm tính. Chúng cần phản ánh mức độ quan trọng của workload và khả năng tài chính.

## Bốn chiến lược DR phổ biến trên AWS

### 1. Backup and Restore

Đây là cách đơn giản và tiết kiệm nhất. Dữ liệu được sao lưu định kỳ; khi có sự cố, hệ thống được dựng lại và restore từ backup.

Ví dụ với QuickBite production:

- snapshot RDS tự động;
- S3 Versioning cho bucket ảnh;
- source và deployment runbook nằm trong repository;
- export cấu hình hoặc Infrastructure as Code;
- kiểm thử restore định kỳ.

Ưu điểm là chi phí thấp. Nhược điểm là RTO thường dài hơn vì cần tạo lại tài nguyên.

### 2. Pilot Light

Dữ liệu cốt lõi và một phần hạ tầng quan trọng được duy trì ở môi trường dự phòng, còn compute đầy đủ chưa chạy. Khi có sự cố, hệ thống bật thêm tài nguyên để phục vụ traffic.

Pilot Light có RTO tốt hơn Backup and Restore nhưng cần quản lý đồng bộ dữ liệu và hạ tầng dự phòng.

### 3. Warm Standby

Một phiên bản thu nhỏ của workload chạy sẵn ở site hoặc Region dự phòng. Khi primary gặp sự cố, môi trường standby được scale lên.

Cách này phục hồi nhanh hơn Pilot Light, nhưng tốn chi phí vì luôn có tài nguyên đang chạy.

### 4. Multi-site Active/Active

Nhiều site/Region cùng phục vụ traffic. Nếu một nơi gặp sự cố, nơi còn lại tiếp tục hoạt động.

Đây là hướng có RTO rất thấp nhưng phức tạp và đắt nhất. Với một project thực tập như QuickBite, lựa chọn này không hợp lý cho demo.

## Áp dụng cho QuickBite

### Kiến trúc hiện tại

QuickBite đã được nâng từ phương án Single-AZ ban đầu lên kiến trúc High Availability trong một Region:

- hai EC2 t3.micro trong Auto Scaling Group tại hai Availability Zone;
- Application Load Balancer;
- RDS PostgreSQL db.t3.micro Multi-AZ;
- S3 private cho frontend, ảnh và Terraform state;
- hai CloudFront distributions;
- ECR, Secrets Manager và Systems Manager;
- CloudWatch Logs/Alarm, SNS, Budgets và Cost Explorer;
- Terraform Infrastructure as Code.

Kiến trúc này giảm single point of failure trong Region, nhưng chưa phải Disaster Recovery đa Region. Project chưa triển khai cross-region snapshot, secondary Region hoặc Active/Active.

Việc ghi rõ giới hạn này là quan trọng. High Availability và Disaster Recovery liên quan nhưng không đồng nghĩa.

### Hướng DR phù hợp

Với quy mô QuickBite, **Backup and Restore** là bước đầu hợp lý:

1. bật automated backup cho RDS trong môi trường cần giữ dữ liệu;
2. tạo manual snapshot trước thay đổi lớn;
3. bật S3 Versioning cho bucket ảnh quan trọng;
4. giữ Dockerfile, Compose, SQL và runbook trong repository;
5. ghi lại biến cấu hình cần phục hồi, nhưng không lưu secret thật;
6. định kỳ restore thử sang một database mới;
7. test ứng dụng kết nối database vừa restore;
8. đo thời gian thực tế để kiểm tra RTO.

QuickBite hiện đã dùng RDS Multi-AZ và Terraform. Bước tiếp theo phù hợp là bổ sung backup policy, restore drill và bản sao ngoài Region. Multi-AZ giúp failover trong Region nhưng không thay thế chiến lược DR.

### Cleanup demo không phải Disaster Recovery

Trong hướng dẫn demo, RDS có thể được xóa bằng `--skip-final-snapshot` để tránh chi phí khi dữ liệu chỉ là seed/test và không cần giữ lại.

Điều đó **không** nên áp dụng cho production.

Cần phân biệt:

| Tình huống | Cách xử lý |
|---|---|
| Môi trường demo có dữ liệu giả | có thể xóa và skip final snapshot |
| Môi trường test cần tái hiện lỗi | tạo snapshot trước khi xóa |
| Môi trường production | backup policy, final snapshot và restore test bắt buộc |

Cleanup là hoạt động kiểm soát chi phí. DR là khả năng phục hồi dữ liệu và dịch vụ. Hai mục tiêu này liên quan nhưng không giống nhau.

### Backup chỉ có giá trị khi restore được

Một bài học quan trọng là: backup chưa từng restore thử thì chưa thể coi là phương án phục hồi đã được kiểm chứng.

Một restore drill đơn giản cho QuickBite có thể gồm:

1. tạo snapshot RDS;
2. restore thành instance mới;
3. dùng EC2 hoặc một test client có network phù hợp để kết nối;
4. chạy `\dt` và kiểm tra sample orders;
5. trỏ backend test sang endpoint mới;
6. chạy `/health`, login, đọc menu và tạo một đơn test;
7. ghi lại thời gian restore;
8. xóa tài nguyên test sau khi hoàn thành.

Quá trình này giúp phát hiện password/config sai, thiếu permission, schema không đủ hoặc runbook không còn chính xác.

### Bài học rút ra

Disaster Recovery không phải chỉ dành cho ngân hàng hoặc hệ thống rất lớn. Ngay cả một project nhỏ cũng nên trả lời được:

- dữ liệu quan trọng nằm ở đâu;
- có backup hay không;
- ai chịu trách nhiệm restore;
- RTO/RPO là bao nhiêu;
- runbook có được test không;
- chi phí của mức bảo vệ đó có phù hợp không.

Với QuickBite, kiến trúc hiện tại đã có hai Availability Zone, Auto Scaling và RDS Multi-AZ, nhưng chưa có DR đa Region. Bước phát triển tiếp theo là Backup and Restore có kiểm thử, RDS snapshots, S3 Versioning và restore drill định kỳ.

## Bằng chứng

![alt text](/images/Post_2.png)

## Nguồn tham khảo

- [Disaster recovery options in the cloud](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html)
- [Business continuity plan: RTO and RPO](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/business-continuity-plan-bcp.html)
- [Disaster Recovery architecture on AWS: Pilot Light and Warm Standby](https://aws.amazon.com/blogs/architecture/disaster-recovery-dr-architecture-on-aws-part-iii-pilot-light-and-warm-standby/)
- [Designing sustainable disaster recovery strategies](https://aws.amazon.com/blogs/storage/designing-sustainable-disaster-recovery-strategies/)
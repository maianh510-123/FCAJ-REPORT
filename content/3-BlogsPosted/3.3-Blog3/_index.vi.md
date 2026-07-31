---
title: "Blog 3 - Least Privilege trên AWS"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---
# Least Privilege trên AWS: Cấp quyền vừa đủ là một kỹ năng rất quan trọng

**Trạng thái nội dung:** Hoàn thành  

Khi mới làm việc với AWS, mình từng có một suy nghĩ khá đơn giản: nếu chương trình gặp lỗi quyền, cứ cấp thêm quyền là xong.

- Lambda không đọc được S3? Cấp thêm quyền.
- EC2 không upload được file? Gắn policy rộng hơn.
- User thao tác không được? Dùng quyền administrator cho nhanh.

Cách này đúng là giúp việc thử nghiệm diễn ra nhanh hơn, nhưng càng học AWS, mình càng thấy đây là một thói quen nguy hiểm. Trong cloud, quyền truy cập không chỉ quyết định chương trình có chạy hay không. Nó còn quyết định phạm vi thiệt hại nếu credential bị lộ, service bị khai thác hoặc cấu hình sai.

Đó là lý do nguyên tắc **Least Privilege** rất quan trọng.

## Least Privilege là gì?

Least Privilege nghĩa là mỗi user, role hoặc workload chỉ được cấp đúng những quyền cần thiết để hoàn thành nhiệm vụ, không nhiều hơn.

Nguyên tắc này không có nghĩa là “cấp quyền càng ít càng tốt” một cách máy móc. Một policy quá hẹp làm workload không chạy. Mục tiêu là hiểu hành vi thật của ứng dụng, sau đó giới hạn:

- action nào được phép;
- resource nào được truy cập;
- prefix hoặc object nào cần dùng;
- điều kiện nào phải thỏa mãn;
- quyền có cần tồn tại lâu dài hay không.

## Ví dụ trực tiếp từ QuickBite

Backend QuickBite chạy trên EC2 cần hai nhóm quyền chính:

1. upload/đọc ảnh món trong bucket `quickbite-menu-images-<env>`, chủ yếu dưới prefix `menu/*`;
2. ghi container log vào CloudWatch Logs.

Backend **không** cần:

- quản trị toàn bộ S3;
- xóa mọi bucket trong account;
- đọc bucket không liên quan;
- tạo IAM user;
- dùng một access key cố định được lưu trong `.env`.

### Policy quá rộng

Một policy như sau có thể giúp code “chạy ngay”, nhưng phạm vi quyền quá lớn:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "*"
    }
  ]
}
```

Nếu EC2 bị khai thác, attacker có thể thao tác trên mọi bucket mà policy cho phép.

### Policy hẹp hơn cho QuickBite

Ví dụ dưới đây giới hạn backend vào đúng bucket và prefix ảnh:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListMenuImagePrefix",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::quickbite-menu-images-<env>",
      "Condition": {
        "StringLike": {
          "s3:prefix": [
            "menu/*"
          ]
        }
      }
    },
    {
      "Sid": "ReadWriteMenuImages",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::quickbite-menu-images-<env>/menu/*"
    },
    {
      "Sid": "WriteQuickBiteLogs",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogStream",
        "logs:DescribeLogStreams",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:ap-southeast-1:<account-id>:log-group:quickbite/backend:*"
    }
  ]
}
```

Nếu code không cần đọc ảnh trực tiếp thì `s3:GetObject` có thể tiếp tục được cân nhắc loại bỏ. Nếu cần xóa ảnh cũ, chỉ thêm `s3:DeleteObject` sau khi xác nhận use case thật sự.

## Vì sao nên dùng IAM role cho EC2?

QuickBite dùng **EC2 instance profile/IAM role** thay cho access key dài hạn.

Khi role được gắn vào EC2:

- AWS SDK có thể nhận temporary credentials tự động;
- không cần lưu `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` trong `.env`;
- credential được xoay vòng;
- khi xóa role hoặc thu hồi policy, workload mất quyền mà không cần đổi key trong source.

Đây là cách phù hợp hơn so với tạo IAM user chỉ để ứng dụng chạy trên EC2.

## Security Group cũng là Least Privilege

Least Privilege không chỉ áp dụng cho IAM.

Trong QuickBite:

- SSH port 22 chỉ nên mở từ IP quản trị;
- RDS không public;
- RDS port 5432 chỉ nhận traffic từ `quickbite-ec2-sg`;
- không mở PostgreSQL cho `0.0.0.0/0`;
- bucket frontend dùng CloudFront OAC thay vì public access trực tiếp;
- CORS chỉ cho phép domain frontend thật.

Mỗi lớp đều giới hạn phạm vi truy cập.

## Không hard-code secret

Một policy tốt vẫn chưa đủ nếu secret bị commit vào Git.

Các thói quen cần áp dụng:

- thêm `.env` vào `.gitignore`;
- chỉ commit `.env.example` với placeholder;
- tạo `SECRET_KEY` trực tiếp trên EC2;
- không chụp screenshot có password RDS;
- không đưa key pair `.pem` vào repository;
- ưu tiên IAM role và temporary credentials;
- với production, cân nhắc Systems Manager Parameter Store hoặc Secrets Manager.

Trong demo hiện tại, database password và application secret vẫn được truyền qua `.env` trên EC2. Báo cáo phải ghi rõ đây là giới hạn hiện tại; Secrets Manager là future improvement chứ chưa được vẽ như đã triển khai.

## IAM Access Analyzer hỗ trợ gì?

AWS IAM Access Analyzer có thể hỗ trợ:

- phát hiện resource được public hoặc chia sẻ cross-account;
- review external access;
- kiểm tra policy bằng policy validation;
- hỗ trợ tạo policy dựa trên activity trong một số workflow;
- giúp phát hiện quyền không còn cần thiết.

Tuy nhiên, công cụ không thay thế việc hiểu ứng dụng. Developer vẫn cần biết `/uploads/image` gọi action nào và Docker `awslogs` cần quyền nào.

## Một quy trình thực tế để giảm quyền

Mình thấy cách làm sau phù hợp hơn việc gắn AdministratorAccess:

1. liệt kê action thật sự của workload;
2. giới hạn resource ARN;
3. dùng IAM role;
4. chạy chức năng chính;
5. xem lỗi `AccessDenied` trong log;
6. chỉ thêm action cần thiết;
7. dùng policy validation/Access Analyzer;
8. review lại sau khi tính năng thay đổi;
9. xóa policy, role hoặc permission không còn dùng.

Quy trình này chậm hơn một chút lúc đầu nhưng làm kiến trúc dễ giải thích và an toàn hơn.

## Bài học rút ra

Bảo mật AWS không chỉ là “đừng để lộ key”. Ngay cả khi credential không bị lộ, một policy quá rộng vẫn làm tăng blast radius.

Least Privilege buộc mình phải hiểu workload:

- EC2 thật sự cần truy cập bucket nào?
- backend cần đọc, ghi hay xóa object?
- RDS nên nhận traffic từ đâu?
- log group nào cần được ghi?
- quyền nào chỉ phục vụ test và phải xóa sau đó?

Với QuickBite, việc dùng EC2 IAM role, giới hạn S3 vào `menu/*`, giới hạn CloudWatch log group và chỉ mở RDS từ EC2 security group là những cách áp dụng trực tiếp, có thể kiểm chứng.

Least Privilege không làm hệ thống an toàn tuyệt đối, nhưng nó làm giảm phạm vi thiệt hại khi có sự cố. Trong cloud, giảm blast radius là một phần quan trọng của thiết kế tốt.

## Bằng chứng
![alt text](/images/Post_3.png)

## Nguồn tham khảo

- [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [What is IAM Access Analyzer?](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html)
- [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html)
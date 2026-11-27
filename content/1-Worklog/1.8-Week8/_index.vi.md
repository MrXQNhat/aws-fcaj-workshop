---
title: "Worklog Tuần 8"
date: 2026-11-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---


### Mục tiêu tuần 8:

* Nắm vững kiến trúc và quy trình triển khai ứng dụng hướng sự kiện sử dụng Amazon SQS và Amazon SNS.
* Thành thạo việc triển khai ứng dụng đóng gói dưới dạng container trên AWS sử dụng Docker, EC2, RDS và ECR.
* Khám phá cơ chế xác thực đa trang của Amazon Cognito, quản lý User Pool và Identity Pool cho các ứng dụng web và di động an toàn.
### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc thực hiện                                                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Xử lý đơn hàng với Amazon SQS và SNS <br>&emsp; + Tạo hàng đợi tin nhắn và chủ đề SNS <br>&emsp; + Xây dựng hàm Lambda quản lý, thanh toán và xử lý đơn hàng                                         | 02/11/2026 | 02/11/2026      | <https://000083.awsfcaj.com/>             |
| 3   | - Triển khai ứng dụng trên Docker <br>&emsp; + Cấu hình VPC, Security Group và IAM role cho ECR <br>&emsp; + Khởi tạo và cấu hình cơ sở dữ liệu RDS                                                   | 03/11/2026 | 03/11/2026      | <https://000015.awsfcaj.com/>             |
| 4   | - Triển khai Container và Kiểm thử <br>&emsp; + Triển khai ứng dụng sử dụng Docker Image và Docker Compose <br>&emsp; + Đẩy và quản lý image container lên Amazon ECR                                    | 04/11/2026 | 04/11/2026      | <https://000015.awsfcaj.com/>             |
| 5   | - Triển khai AWS Cognito Cross Sites <br>&emsp; + Tìm hiểu các khái niệm cốt lõi về Amazon Cognito <br>&emsp; + Cấu hình User Pool quản lý thư mục người dùng và token                                 | 05/11/2026 | 05/11/2026      | <https://000141.awsfcaj.com/>             |
| 6   | - Identity Pool và Phân quyền Tài nguyên <br>&emsp; + Cấu hình Identity Pool cấp quyền truy cập an toàn vào tài nguyên AWS <br>&emsp; + Kiểm thử xác thực đa trang và dọn dẹp tài nguyên                  | 06/11/2026 | 06/11/2026      | <https://000141.awsfcaj.com/>             |

### Kết quả đạt được tuần 8:

* Xây dựng thành công hệ thống xử lý đơn hàng tận dụng Amazon SQS để tách rời hàng đợi tin nhắn và Amazon SNS để thông báo tức thì cho quản trị viên.
* Triển khai thành công ứng dụng nhiều tầng dạng container trên AWS sử dụng EC2, RDS và Docker Compose tuân thủ các nguyên tắc bảo mật mạng.
* Đẩy và quản lý hiệu quả các Docker image container sử dụng Amazon Elastic Container Registry (ECR).
* Nắm vững kiến trúc Amazon Cognito, phân biệt và triển khai thành công User Pool cùng Identity Pool.
* Cấu hình thành công hệ thống xác thực, phân quyền và quản lý danh tính liên kết bảo mật cho ứng dụng web.

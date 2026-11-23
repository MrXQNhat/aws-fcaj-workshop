---
title: "Worklog Tuần 7"
date: 2026-10-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---



### Mục tiêu tuần 7:

* Nắm vững kiến trúc và quy trình triển khai Hệ thống Quản lý Tài liệu (Document Management System - DMS) theo mô hình Serverless trên AWS.
* Thành thạo việc tạo bảng DynamoDB, viết hàm Lambda, cấu hình API Gateway, tích hợp giao diện Frontend và triển khai ứng dụng sử dụng AWS SAM.
### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc thực hiện                                                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu kiến trúc Hệ thống Quản lý Tài liệu (DMS) <br> - Tạo bảng trong DynamoDB để lưu trữ thông tin file                                                                                          | 19/10 | 19/10      | <https://000133.awsfcaj.com/>             |
| 3   | - Viết và triển khai các hàm Lambda quản lý tài liệu <br>&emsp; + Tạo hàm liệt kê (listing function) <br>&emsp; + Tạo hàm upload file (upload function) <br>&emsp; + Tạo hàm xóa file (delete function)    | 20/10 | 20/10      | <https://000133.awsfcaj.com/>             |
| 4   | - Cấu hình Amazon API Gateway <br>&emsp; + Tạo các phương thức API (Methods) <br>&emsp; + Cài đặt và bật CORS <br>&emsp; + Kiểm thử API bằng Postman                                                    | 21/10 | 21/10      | <https://000079.awsfcaj.com/>             |
| 5   | - Xây dựng và triển khai Front-end gọi API Gateway <br>&emsp; + Triển khai giao diện React Front-end <br>&emsp; + Kiểm thử tích hợp API với Front-end                                                  | 22/10 | 22/10      | <https://000079.awsfcaj.com/>             |
| 6   | - Triển khai ứng dụng với AWS SAM <br>&emsp; + Tổng quan về AWS Serverless Application Model <br>&emsp; + Đóng gói và triển khai stack với SAM CLI <br>&emsp; + Dọn dẹp tài nguyên (Cleanup)               | 23/10 | 23/10      | <https://000136.awsfcaj.com/>             |

### Kết quả đạt được tuần 7:

* Nắm vững kiến trúc toàn diện của Hệ thống Quản lý Tài liệu Serverless sử dụng AWS Lambda, DynamoDB và S3.
* Tạo thành công các bảng DynamoDB và triển khai các hàm Lambda xử lý việc liệt kê, tải lên và xóa tài liệu.
* Cấu hình thành công các endpoint của API Gateway, bật tính năng CORS và kiểm thử API hoàn chỉnh bằng Postman.
* Tích hợp ứng dụng giao diện React Front-end để giao tiếp mượt mà với các API backend.
* Sử dụng thành thạo AWS SAM (Serverless Application Model) để mô tả, đóng gói và triển khai toàn bộ ứng dụng serverless trong một stack duy nhất qua cú pháp YAML.

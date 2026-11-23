---
title: "Worklog Tuần 10"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Khám phá các rủi ro bảo mật trong kiến trúc serverless, tập trung vào các lỗ hổng AWS Lambda và Giả mạo yêu cầu phía máy chủ (SSRF).
* Phân tích bảo mật lưu trữ đám mây, các bucket S3 không được xác thực và khai thác dịch vụ metadata.

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                     | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Đánh giá bảo mật lưu trữ đám mây (S3) <br>&emsp; + Kiểm toán quyền public bucket và ACL <br>&emsp; + Khai thác chính sách bucket cấu hình sai và lộ lọt object                                         | 09/11 | 09/11      | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html> |
| 3   | - Khai thác Dịch vụ Metadata của Instance (IMDS) <br>&emsp; + Hiểu rủi ro dịch vụ metadata EC2 (IMDSv1 vs IMDSv2) <br>&emsp; + Trích xuất credential tạm thời qua lỗ hổng SSRF                         | 10/11 | 10/11      | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html> |
| 4   | - Lỗ hổng Serverless (AWS Lambda) <br>&emsp; + Phân tích lỗi injection và giải mã không an toàn trong hàm Lambda <br>&emsp; + Kiểm toán bảo mật event source mapping                                     | 11/11 | 11/11      | <https://owasp.org/www-project-serverless-top-10/> |
| 5   | - Cấu hình sai API Gateway & Lambda <br>&emsp; + Khai thác thiếu phân quyền trên endpoint API Gateway <br>&emsp; + Thực hiện tấn công tham chiếu đối tượng trực tiếp không an toàn (IDOR) trong serverless | 12/11 | 12/11      | <https://cloudsecurityalliance.org/>      |
| 6   | - Khắc phục và Phát triển an toàn <br>&emsp; + Bắt buộc sử dụng IMDSv2 và vô hiệu hóa token hopping <br>&emsp; + Triển khai kiểm tra đầu vào và phân quyền chặt chẽ cho API Lambda                      | 13/11 | 13/11      | <https://docs.aws.amazon.com/lambda/latest/dg/security-iam.html> |

### Kết quả đạt được tuần 10:

* Thành thạo việc nhận diện và khai thác các cloud storage bucket bị cấu hình lộ thông tin và quyền hạn đối tượng không an toàn.
* Khai thác thành công Dịch vụ Metadata của EC2 (IMDSv1) thông qua lỗ hổng SSRF để đánh cắp IAM credential tạm thời, minh họa vectơ tấn công di chuyển ngang.
* Khám phá các lỗ hổng OWASP Top 10 đặc thù cho kiến trúc serverless, bao gồm cấu hình hàm không an toàn và event injection.
* Phân tích các điểm yếu phân quyền của API Gateway và bảo vệ các endpoint serverless chống lại truy cập dữ liệu trái phép.
* Triển khai các biện pháp phòng thủ chiều sâu như bắt buộc dùng IMDSv2 và kiểm thực dữ liệu đầu vào nghiêm ngặt trên toàn bộ các thành phần serverless.
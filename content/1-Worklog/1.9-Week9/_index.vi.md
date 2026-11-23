---
title: "Worklog Tuần 9"
date: 2026-11-10
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Mục tiêu tuần 9:

* Biết các nguyên tắc cốt lõi về bảo mật đám mây, Mô hình Trách nhiệm Chia sẻ và các vector đe dọa phổ biến.
* Nhận diện và khai thác các lỗi cấu hình sai trong Quản lý Định danh và Truy cập (IAM).

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                     | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Nghiên cứu Mô hình Trách nhiệm Chia sẻ bảo mật Cloud <br> - Phân tích các lỗi cấu hình sai phổ biến và đường đi leo thang đặc quyền                                                                  | 02/11 | 02/11      | <https://aws.amazon.com/compliance/shared-responsibility-model/> |
| 3   | - Kiểm toán bảo mật AWS IAM <br>&emsp; + Phân tích các chính sách IAM cấp quyền quá mức <br>&emsp; + Nhận diện quyền wildcard nguy hiểm (`*`) <br>&emsp; + Sử dụng công cụ tự động đánh giá IAM          | 03/11 | 03/11      | <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html> |
| 4   | - Mô phỏng Lab Leo thang Đặc quyền (Privilege Escalation) <br>&emsp; + Khai thác quyền `iam:CreateAccessKey` và `iam:PutUserPolicy` <br>&emsp; + Mô phỏng giả lập tấn công chỉnh sửa policy và assume-role | 04/11 | 04/11      | <https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/> |
| 5   | - Trinh sát & Khảo sát Hạ tầng Cloud (Reconnaissance) <br>&emsp; + Liệt kê tài nguyên cloud sử dụng Pacu và ScoutSuite <br>&emsp; + Lập bản đồ bề mặt tấn công và nhận diện role quản trị bị lộ             | 05/11 | 05/11      | <https://github.com/RhinoSecurityLabs/pacu> |
| 6   | - Khắc phục và Tăng cường bảo mật (Remediation) <br>&emsp; + Áp dụng nguyên tắc Đặc quyền tối thiểu (Least Privilege) vào chính sách IAM <br>&emsp; + Cấu hình AWS Organizations và Service Control Policies (SCPs) | 06/11 | 06/11      | <https://aws.amazon.com/architecture/well-architected/> |

### Kết quả đạt được tuần 9:

* Hiểu sâu sắc về bức tranh đe dọa trên đám mây và ranh giới trách nhiệm bảo mật giữa nhà cung cấp dịch vụ và khách hàng.
* Nhận diện và phân tích thành công các lỗi cấu hình sai chính sách IAM nguy hiểm có thể dẫn đến chiếm đoạt toàn bộ tài khoản.
* Thực hiện thành công các cuộc tấn công leo thang đặc quyền trong môi trường lab, minh họa cách kẻ tấn công vượt qua ranh giới phân quyền yếu kém.
* Sử dụng thành thạo các công cụ kiểm thử xâm nhập đám mây chuyên dụng như Pacu và ScoutSuite để trinh sát tài nguyên tự động.
* Áp dụng các chiến lược khắc phục thi hành nguyên tắc Đặc quyền tối thiểu và triển khai Chính sách Kiểm soát Dịch vụ bảo vệ.
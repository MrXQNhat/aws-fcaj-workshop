---
title: "Worklog Tuần 1"
date: 2026-09-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---
{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}


### Mục tiêu tuần 1:

* Kết nối, làm quen với các thành viên trong First Cloud AI Journey.
* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo AWS Free Tier account<br>- Tìm hiểu AWS Console & AWS CLI<br>- Thực hành:<br>&msp; + Tạo AWS account<br>&msp; + Cài AWS CLI & cấu hình<br>&msp; + Cách sử dụng AWS CLI | 14/09 | 14/09 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Phân biệt Root User & IAM User, tạo IAM User/Group/Role<br>- Bật MFA, tạo Access Key / AWS CLI | 15/09 | 15/09 | <https://www.youtube.com/watch?v=RVDwsmyFA-Q&list=PL4NoNM0L1m72rjn5s7VzKnaqEK7UTpeGa><br><https://000002.awsstudygroup.com/><br><https://000048.awsstudygroup.com/> |
| 4 | - Viết & quản lý IAM Policies (JSON, Least Privilege)<br>- Cấu hình Permission Boundary, SCP, IAM Policy Simulator | 16/09 | 16/09 | <https://000002.awsstudygroup.com/vi/><br><https://000048.awsstudygroup.com/vi/> |
| 5 | - Làm quen với CloudShell trên AWS<br>- Khởi tạo môi trường: Tạo CloudShell / Cloud9 instance<br>- Thao tác tính năng cơ bản: Sử dụng lệnh Linux cơ bản (pwd, ls, cat), tạo và chỉnh sửa file bản tin bằng vi, thực hiện Upload/Download file qua IDE/Terminal<br>- Tương tác qua AWS CLI: Kiểm tra phiên bản CLI, truy vấn danh sách S3 bucket (aws s3 ls) và EC2 instance (aws ec2 describe-instances) | 17/09 | 17/09 | <https://000049.awsstudygroup.com/vi/> |
| 6 | - Tìm hiểu các gói AWS Support Plans:<br>&msp; - Nghiên cứu & so sánh 4 gói hỗ trợ của AWS: Basic, Developer, Business, và Enterprise<br>&msp; - Phân tích sự khác biệt về chi phí, thời gian phản hồi (Response time), quyền truy cập AWS Trusted Advisor và kênh hỗ trợ 24/7<br>&msp; - Tối ưu hóa việc lựa chọn gói Support Plan phù hợp cho từng quy mô dự án và ngân sách doanh nghiệp | 18/09 | 18/09 | <https://000009.awsstudygroup.com/> |

### Kết quả đạt được tuần 1:

* **Khởi tạo & Cấu hình môi trường:**
  * Khởi tạo thành công tài khoản AWS Free Tier.
  * Làm quen giao diện AWS Management Console và hoàn thành cài đặt, cấu hình AWS CLI trên máy cá nhân để tương tác với tài nguyên AWS.
* **Quản trị người dùng & Bảo mật (IAM Identity Core):**
  * Nắm rõ sự khác biệt giữa Root User và IAM User, áp dụng Best Practice không sử dụng Root User cho công việc hàng ngày.
  * Khởi tạo thành công các IAM User, Group và Role.
  * Bật xác thực 2 lớp (MFA) cho tài khoản và tạo Access Key để kết nối an toàn qua AWS CLI.
* **Quản lý chính sách & Kiểm soát phân quyền (IAM Policies & Governance):**
  * Hiểu và viết được các IAM Policy (JSON) theo nguyên tắc Least Privilege (Quyền tối thiểu).
  * Tìm hiểu và cấu hình các cơ chế phân quyền nâng cao: Permission Boundary và Service Control Policies (SCP).
  * Sử dụng thành thạo IAM Policy Simulator để kiểm thử và giả lập luồng phân quyền.



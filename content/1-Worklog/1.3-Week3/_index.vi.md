---
title: "Worklog Tuần 3"
date: 2026-10-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---


### Mục tiêu tuần 3:

* Làm quen với các dịch vụ và công cụ cơ bản trên AWS.
* Triển khai hạ tầng mạng và môi trường Hybrid DNS.
* Thực hành sử dụng AWS CLI để quản lý tài nguyên AWS.
* Nắm được kiến thức cơ bản về VPC, IAM, S3, SNS và EC2.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu tổng quan về bài lab Set up Hybrid DNS with Route 53 Resolver.<br>- Tìm hiểu mô hình kết nối giữa hệ thống DNS trên AWS và DNS on-premises.<br>- Tìm hiểu vai trò của Amazon Route 53 Resolver trong kiến trúc Hybrid DNS, bao gồm Outbound Endpoint, Inbound Endpoint và Resolver Rules.<br>- Tìm hiểu các dịch vụ AWS được sử dụng trong bài lab như Amazon VPC, EC2, AWS CloudFormation và AWS Directory Service.<br>- Chuẩn bị môi trường thực hành và tạo EC2 Key Pair để phục vụ việc kết nối đến các EC2 instance sau khi triển khai. | 28/09 | 29/09 | <https://000010.awsstudygroup.com/vi/> |
| 3 | - Tải CloudFormation template từ GitHub và tìm hiểu cách sử dụng template để triển khai hạ tầng AWS.<br>- Tạo CloudFormation Stack với tên HybridDNS.<br>- Cấu hình các thành phần mạng như VPC, Public Subnet và Private Subnet tại các Availability Zone được yêu cầu.<br>- Cấu hình EC2 Remote Desktop Gateway và lựa chọn Key Pair đã tạo trước đó.<br>- Kiểm tra và triển khai CloudFormation Stack để AWS tự động tạo các tài nguyên cần thiết.<br>- Cấu hình Security Group cho Remote Desktop Gateway, loại bỏ các port không sử dụng và giới hạn quyền truy cập RDP/ICMP theo IP cần thiết. | 29/09 | 29/09 | <https://000010.awsstudygroup.com/vi/> |
| 4 | - Cài đặt AWS CLI v2 trên môi trường Linux/Windows.<br>- Kiểm tra phiên bản AWS CLI sau khi cài đặt.<br>- Sử dụng aws configure để cấu hình AWS CLI với Access Key, Secret Access Key, Region và Output Format.<br>- Tìm hiểu cách tạo và sử dụng nhiều profile trong AWS CLI thông qua --profile.<br>- Kiểm tra thông tin cấu hình, region và danh sách profile.<br>- Sử dụng AWS CLI để kiểm tra các tài nguyên đang tồn tại trên AWS như EC2 instance và S3 bucket. | 30/09 | 30/09 | <https://000011.awsstudygroup.com/> |
| 5 | - Thực hành sử dụng AWS CLI với Amazon S3.<br>- Tạo S3 bucket bằng AWS CLI và kiểm tra danh sách bucket.<br>- Tạo file và upload object lên S3 bucket.<br>- Kiểm tra các object đang có trong bucket.<br>- Thực hiện xóa object và bucket sau khi hoàn thành bài thực hành.<br>- Tìm hiểu Amazon SNS và sử dụng AWS CLI để tạo SNS Topic.<br>- Đăng ký email subscriber vào Topic, xác nhận subscription và kiểm tra trạng thái.<br>- Gửi thử message thông qua SNS và kiểm tra việc nhận thông báo qua email. | 1/10 | 1/10 | <https://000011.awsstudygroup.com/> |
| 6 | - Thực hành quản lý IAM bằng AWS CLI: tạo IAM Group, IAM User và thêm User vào Group.<br>- Kiểm tra thông tin Group/User và tạo Access Key cho User; tìm hiểu việc quản lý và xóa Access Key khi không còn sử dụng.<br>- Thực hành xây dựng mạng VPC bằng AWS CLI gồm VPC, Subnet, Internet Gateway và Route Table.<br>- Cấu hình route mặc định 0.0.0.0/0, attach Internet Gateway và associate Route Table với Subnet.<br>- Tạo EC2 instance bằng AWS CLI, tạo Key Pair và Security Group, mở quyền SSH, kiểm tra trạng thái EC2 và kết nối SSH vào instance.<br>- Tìm hiểu cách xử lý các lỗi thường gặp khi thực hành AWS CLI và thực hiện dọn dẹp các tài nguyên đã tạo sau khi hoàn thành lab. | 2/10 | 2/10 | <https://000011.awsstudygroup.com/> |

### Kết quả đạt được tuần 3:

* Hiểu được kiến trúc Hybrid DNS và các thành phần cơ bản của Route 53 Resolver.
* Biết triển khai hạ tầng AWS bằng CloudFormation và cấu hình Security Group.
* Cài đặt, cấu hình và sử dụng được AWS CLI.
* Thực hành được các thao tác cơ bản với S3, SNS, IAM, VPC và EC2.
* Có nền tảng để tiếp tục thực hành quản trị và triển khai hệ thống trên AWS.

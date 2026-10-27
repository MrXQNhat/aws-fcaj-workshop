---
title: "Worklog Tuần 2"
date: 2026-09-27
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---



### Mục tiêu tuần 2:

* Nắm vững kiến ​​thức cốt lõi về mạng AWS và kết nối đám mây lai (VPC, Subnet, Site-to-Site VPN).
* Tự động hóa quy trình quản lý EC2 và tối ưu hóa chi phí bằng cách sử dụng AWS Lambda và IAM.
* Triển khai ứng dụng và các khối lượng công việc dạng container trên Amazon Lightsail.
* Thiết lập khả năng quan sát hệ thống, theo dõi các chỉ số (metrics) và cấu hình cảnh báo tự động bằng AWS CloudWatch.
* Đảm bảo không phát sinh chi phí hạ tầng trong suốt quá trình thực hành tại workshop.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Thực hành Workshop: Amazon VPC & AWS Site-to-Site VPN<br>- Khởi tạo hạ tầng VPC cơ bản: Tạo VPC, Subnet (Public/Private), Internet Gateway, Route Table và Security Group<br>- Triển khai máy chủ & Kết nối: Tạo EC2 Server, cấu hình NAT Gateway, kiểm tra kết nối qua Reachability Analyzer và Session Manager<br>- Thấu hiểu & Cấu hình VPN: Thiết lập Site-to-Site VPN (Virtual Private Gateway, Customer Gateway, IPsec Tunnel/Strongswan)<br>- Kiểm thử & Dọn dẹp: Kiểm tra lưu lượng mạng, giám sát qua CloudWatch và thực hiện Clean up tài nguyên | 21/09 | 21/09 | <https://000003.awsstudygroup.com/vi/> |
| 3 | - Thực hành Workshop: Tối ưu chi phí EC2 với Lambda<br>- Chuẩn bị hạ tầng: Tạo VPC, Security Group, EC2 instance và cài đặt Incoming Webhook Slack<br>- Phân quyền: Tạo Tag cho Instance, tạo IAM Role cho Lambda<br>- Tự động hóa: Viết Lambda Function tự động Stop/Start EC2 instance<br>- Kiểm thử & Dọn dẹp: Kiểm tra kết quả qua Slack và tiến hành Clean up toàn bộ tài nguyên | 22/09 | 22/09 | <https://000022.awsstudygroup.com/vi/> |
| 4 | - Thực hành Workshop: Triển khai ứng dụng trên Amazon Lightsail<br>- Khởi tạo Instance: Tạo và cấu hình Amazon Lightsail Instance (WordPress/Linux)<br>- Cấu hình Mạng & Domain: Cấu hình Static IP, đính kèm DNS record, thiết lập Firewall/Networking rules<br>- Tối ưu & Quản trị: Cấu hình SSL/TLS certificate (Let's Encrypt), quản lý cơ sở dữ liệu tích hợp và sao lưu (Snapshots)<br>- Kiểm thử & Dọn dẹp: Kiểm tra truy cập trang Web qua Domain/Static IP và thực hiện Clean up tài nguyên | 23/09 | 23/09 | <https://000045.awsstudygroup.com/> |
| 5 | - Thực hành Amazon Lightsail Container: Khởi tạo dịch vụ Lightsail Container Service và thực hiện triển khai ứng dụng từ Public Docker Image.<br>- Tự đóng gói & triển khai Custom Image: Cài đặt Docker trên môi trường Ubuntu (Lightsail Instance), build container image riêng, push lên registry và cập nhật deployment mới thành công.<br>- Kiến thức thu nhận: Nắm vững quy trình đóng gói và vận hành ứng dụng container hóa (containerized application) đơn giản trên môi trường AWS Lightsail mà không cần quản trị hạ tầng phức tạp. Tiến hành dọn dẹp tài nguyên (Clean up) sau khi hoàn thành. | 24/09 | 24/09 | <https://000046.awsstudygroup.com/> |
| 6 | - Thực hành AWS CloudWatch Workshop: Chuẩn bị môi trường và cấu hình các thành phần giám sát hệ thống trên Amazon CloudWatch.<br>- Thu thập & Quản lý dữ liệu: Cấu hình theo dõi chỉ số hiệu năng (Metrics) và tập trung dữ liệu nhật ký hệ thống (Logs).<br>- Cảnh báo & Trực quan hóa: Khởi tạo CloudWatch Alarms để gửi cảnh báo tự động khi có sự cố và xây dựng CloudWatch Dashboards để trực quan hóa trạng thái hệ thống.<br>- Kiến thức thu nhận: Hiểu rõ cơ chế giám sát (Observe), theo dõi (Monitor) và phát hiện sự cố (Detect faults) giúp tối ưu vận hành ứng dụng trên AWS Cloud. Tiến hành dọn dẹp tài nguyên (Clean up) sau bài lab. | 25/09 | 25/09 | <https://000008.awsstudygroup.com/> |

### Kết quả đạt được tuần 2:

* **Cloud Networking & Security Mastery:** Hiểu sâu và làm chủ hạ tầng VPC, phân chia Subnet Public/Private, cấu hình Routing, NAT Gateway và kết nối VPN bảo mật.
* **Serverless Automation & Cost Optimization:** Tự động hóa quản lý tài nguyên EC2 bằng Lambda Function, giúp giảm chi phí hạ tầng không cần thiết và gửi thông báo qua Slack.
* **PaaS Deployment & Containerization:** Nắm vững quy trình đóng gói ứng dụng Docker, đẩy image lên registry và vận hành ứng dụng container hóa trên Amazon Lightsail.
* **System Observability & Monitoring:** Xây dựng hệ thống giám sát toàn diện với CloudWatch (Metrics, Logs, Alarms, Dashboards) để phát hiện và xử lý sự cố chủ động.


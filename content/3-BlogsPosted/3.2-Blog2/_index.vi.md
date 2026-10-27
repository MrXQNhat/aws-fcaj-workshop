---
title: "Blog 2"
date: 2026-09-25
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# QUẢN LÝ TẬP TRUNG HÀNG TRĂM AWS ACCOUNT AN TOÀN VỚI ORGANIZATIONS & SERVICE CONTROL POLICIES (SCPs)

Việc quản lý hệ thống trên mô hình một AWS Account duy nhất tiềm ẩn rủi ro lớn về mất an toàn thông tin và gián đoạn dịch vụ khi quy mô phình to hoặc khi áp dụng các công cụ tự động hóa như AI Agent. Để khắc phục triệt để bài toán này, AWS khuyến nghị triển khai kiến trúc Multi-Account kết hợp với **AWS Organizations** và chính sách kiểm soát dịch vụ **Service Control Policies (SCPs)**.

### Các điểm chính cần nắm:

* **Rủi ro mô hình Single-Account & thách thức từ AI Agent:** Việc gom chung các môi trường (Dev, Staging, Prod) vào một tài khoản duy nhất dễ dẫn đến sập toàn bộ hệ thống khi xảy ra sự cố. Ngoài ra, các AI Agent vận hành tự động nếu không có rào chắn cấp tổ chức có thể bị thao túng (Prompt Injection) gây lãng phí chi phí hoặc xóa nhầm tài nguyên quan trọng.
* **Kiến trúc Multi-Account chuẩn AWS:** Phân chia rõ ràng giữa Management Account (tài khoản gốc chỉ dùng quản lý/hóa đơn), các Organizational Units - OUs (như Security OU, Workload OU) và áp đặt rào chắn kiểm soát tập trung.
* **Vai trò của Service Control Policies (SCPs):** SCPs thiết lập mức giới hạn quyền hạn tối đa phủ lên từng OU hoặc tài khoản thành viên. SCPs giúp bảo vệ dữ liệu audit (chặn xóa/tắt CloudTrail), khoanh vùng địa lý (giới hạn Region được khởi tạo tài nguyên), khóa chặt các tài nguyên nhạy cảm (S3, RDS) và ngăn tài khoản con tự rời khỏi tổ chức.
* **Giám sát tập trung:** Kết hợp tự động hóa thu thập log qua AWS CloudTrail Organizational Trails, quản lý cấu hình bằng AWS Config Aggregator và tự động bật AWS GuardDuty cho toàn bộ tài khoản mới.
* **Lộ trình dịch chuyển 3 bước:** (1) Khởi tạo AWS Organizations & đăng nhập tập trung SSO → (2) Quy hoạch cấu trúc OUs & tách bạch môi trường → (3) Siết chặt SCPs & tự động hóa cấp phát tài khoản bằng AWS Control Tower.

### Nguồn tham khảo:
[1] AWS Documentation - Service Control Policies (SCPs) (https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)
[2] AWS Documentation - Example Service Control Policies (https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_examples.html)
[3] AWS Builder Center - Protecting AWS Organization with SCPs (https://builder.aws.com/content/3ItkGyU0Nn5rfYhYQOkbvFiUQ2O/introduction-to-aws-organizations-and-service-control-policies-scps)

![](b2.jpg)

Link bài viết: https://www.facebook.com/groups/660548818043427/user/100084179052828/

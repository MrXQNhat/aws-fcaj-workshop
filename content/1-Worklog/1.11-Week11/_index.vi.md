---
title: "Worklog Tuần 11"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Mục tiêu tuần 11:

* Làm quen phương pháp luận pháp y số trên đám mây, các đường ống phân tích log (CloudTrail, VPC Flow Logs) và kỹ thuật săn lùng mối đe dọa.
* Thành thạo kỹ thuật phòng thủ đám mây, triển khai Ứng phó sự cố tự động (Incident Response) và kiểm tra bảo mật Hạ tầng dưới dạng Mã nguồn (IaC).

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Logging & Giám sát Đám mây <br>&emsp; + Cấu hình AWS CloudTrail, CloudWatch Logs và VPC Flow Logs <br>&emsp; + Tập trung hóa tổng hợp log phục vụ săn lùng mối đe dọa                                  | 23/11/2026 | 23/11/2026      | <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html> |
| 3   | - Săn lùng Mối đe dọa & Pháp y Cloud (Threat Hunting & Forensics) <br>&emsp; + Phân tích các lệnh gọi API độc hại và hành vi bất thường trong CloudTrail <br>&emsp; + Thực hiện triage pháp y trên instance bị chiếm quyền | 24/11/2026 | 24/11/2026      | <https://aws.amazon.com/blogs/security/>  |
| 4   | - Bảo mật Hạ tầng dưới dạng Mã nguồn (IaC Security) <br>&emsp; + Quét mẫu Terraform và CloudFormation tìm lỗi bảo mật bằng Checkov/TfSec <br>&emsp; + Tích hợp bảo mật shift-left vào đường ống CI/CD    | 25/11/2026 | 25/11/2026      | <https://www.checkov.io/>                 |
| 5   | - Ứng phó Sự cố Tự động hóa (Automated IR) <br>&emsp; + Xây dựng luồng khắc phục tự động sử dụng EventBridge và Lambda <br>&emsp; + Tự động thu hồi credential IAM bị lộ và cô lập instance bị tấn công   | 26/11/2026 | 26/11/2026      | <https://aws.amazon.com/solutions/security-automation/> |
| 6   | - Đánh giá Dự án Cuối kỳ & Tăng cường Bảo mật <br>&emsp; + Thực hiện đánh giá bảo mật toàn diện kiến trúc lab serverless <br>&emsp; + Hoàn thiện danh mục đầu tư và tài liệu bảo mật cloud                 | 27/11/2026 | 27/11/2026      | <https://www.nist.gov/cyberframework>     |

### Kết quả đạt được tuần 11:

* Thiết lập các đường ống giám sát tập trung trên đám mây bằng cách cấu hình và kiểm toán AWS CloudTrail cùng VPC Flow Logs.
* Phát triển kỹ năng săn lùng mối đe dọa trên cloud để phát hiện hoạt động API bất thường, hành vi đánh cắp credential và truy cập trái phép.
* Tích hợp các công cụ bảo mật "Shift-Left" (Checkov, TfSec) vào quy trình IaC nhằm phát hiện cấu hình sai ngay từ giai đoạn viết mã trước khi triển khai.
* Thiết kế và kiểm thử các kịch bản ứng dụng phản ứng sự cố tự động (playbook IR) sử dụng EventBridge và Lambda nhằm cô lập tức thời tài nguyên cloud bị xâm phạm.
* Tổng kết chương trình bảo mật bằng việc tăng cường cấu hình toàn bộ kiến trúc lab và biên soạn báo cáo thực thi bảo mật cloud chuyên nghiệp.
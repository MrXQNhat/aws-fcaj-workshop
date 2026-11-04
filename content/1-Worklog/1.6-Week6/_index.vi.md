---
title: "Worklog Tuần 6"
date: 2026-10-25
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Mục tiêu tuần 6:

* Thiết kế, chuẩn hóa và hoàn thiện toàn bộ hệ thống sơ đồ kiến trúc và luồng dữ liệu cho dự án SecureLearn.
* Chi tiết hóa hạ tầng serverless lai (hybrid serverless) giữa AWS và Cloudflare, đảm bảo tính minh bạch trong xử lý media, kiểm soát truy cập và phát hiện bất thường bằng AI.

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc thực hiện                                                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Nghiên cứu và thiết kế sơ đồ Kiến trúc Tổng thể (General Architecture) <br> - Xác định ranh giới tích hợp giữa lớp biên Cloudflare Edge Compute và đám mây AWS                         | 19/10/2026 | 19/10/2026      |                                           |
| 3   | - Thiết kế Luồng Tải lên & Xử lý Media (`datatransfer.png`) <br>&emsp; + Người dùng upload qua Cloudflare Pages <br>&emsp; + API Gateway & Lambda Upload Handler <br>&emsp; + R2 storage & chuyển mã MediaConvert | 20/10/2026 | 20/10/2026      |                                           |
| 4   | - Thiết kế Luồng Phát lại & Kiểm soát truy cập (`playback.png`) <br>&emsp; + Xác thực Cognito & kiểm tra JWT <br>&emsp; + Kiểm tra quyền hạn trong DynamoDB <br>&emsp; + Tạo Signed Token bằng Cloudflare Workers | 21/10/2026 | 21/10/2026      |                                           |
| 5   | - Thiết kế Nhánh Phát hiện Bất thường bằng AI (`AI.png`) <br>&emsp; + Trigger định kỳ qua EventBridge <br>&emsp; + Phân tích log bằng Isolation Forest Lambda <br>&emsp; + Cảnh báo tự động qua Amazon SNS    | 22/10/2026 | 22/10/2026      |                                           |
| 6   | - **Kiểm tra & Hoàn thiện:** <br>&emsp; + Chuẩn hóa toàn bộ sơ đồ kiến trúc (`general.jpg`, `datatransfer.jpg`, `playback.jpg`, `AI.png`) <br>&emsp; + Tích hợp sơ đồ vào tài liệu dự án             | 23/10/2026 | 23/10/2026      |                                           |

### Kết quả đạt được tuần 6:

* Thiết kế và chuẩn hóa thành công toàn bộ hệ thống sơ đồ kiến trúc serverless lai cho dự án SecureLearn, kết nối mượt mà giữa khái niệm bảo mật cấp cao và luồng thực thi thực tế.
* Xây dựng chi tiết luồng tiếp nhận video từ đầu đến cuối, mô tả quá trình tải lên từ Cloudflare Pages thông qua API Gateway, Lambda, kho lưu trữ R2 riêng tư và tự động hóa chuyển mã qua MediaConvert.
* Hoàn thiện cấu trúc luồng phát video an toàn và cấp phép, tích hợp xác thực Amazon Cognito, kiểm tra JWT, xác thực quyền hạn trong DynamoDB và sinh Signed URL từ Cloudflare Workers.
* Thiết lập sơ đồ đường ống giám sát AI tự động, mô tả cách EventBridge kích hoạt hàm Lambda chạy mô hình Isolation Forest để phân tích log CloudWatch và phát cảnh báo qua Amazon SNS.
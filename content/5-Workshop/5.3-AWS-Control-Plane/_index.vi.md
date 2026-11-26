---
title: "Triển khai Control Plane trên AWS"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

Trong phần này, bạn sẽ cấu hình xác thực Cognito và backend Spring Boot chạy trên ECS, bảo vệ thông tin bí mật bằng KMS và kết nối cảnh báo phát hiện bất thường bằng AI. Backend xử lý định tuyến API, phân quyền và cấp playback token; Lambda riêng chỉ dùng cho phát hiện bất thường bằng AI.

### Các phần thực hành
1. [5.3.1 Tạo Amazon Cognito User Pool](5.3.1-cognito/)
2. [5.3.2 Cấu hình Lambda phát hiện bất thường bằng AI](5.3.2-lambda/)
3. [5.3.3 Triển khai Backend Spring Boot trên ECS](5.3.3-api/)
4. [5.3.4 Cấu hình AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
5. [5.3.5 Cấu hình cảnh báo SNS & EventBridge](5.3.5-sns-eventbridge/)
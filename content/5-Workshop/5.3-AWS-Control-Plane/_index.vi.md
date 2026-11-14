---
title: "Triển khai Control Plane trên AWS"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

Trong phần này, bạn sẽ triển khai hệ thống backend điều khiển cốt lõi trên AWS để xử lý xác thực người dùng, kiểm tra quyền truy cập, cấp short-lived playback token và mã hóa khóa bí mật.

### Các phần thực hành
1. [5.3.1 Tạo Amazon Cognito User Pool](5.3.1-cognito/)
2. [5.3.2 Cấu hình Lambda License Service](5.3.2-lambda/)
3. [5.3.3 Tạo API Gateway](5.3.3-api/)
4. [5.3.4 Cấu hình AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
5. [5.3.5 Tạo SNS & Eventbridge](5.3.5-sns-eventbridge/)
---
title: "Workshop"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# SecureLearn: Hệ Thống Bảo Vệ Nội Dung Số Dành Cho Nền Tảng Giáo Dục

Trong workshop này, bạn sẽ xây dựng **SecureLearn**, một giải pháp bảo vệ nội dung video với chi phí hợp lý dành cho các trung tâm giáo dục vừa và nhỏ (SMEs). Toàn bộ hệ thống chạy trên **AWS**:

* Backend Spring Boot chạy trên ECS Fargate phía sau Application Load Balancer. Backend xử lý xác thực thông qua Cognito, cấp token phát video, đồng thời chuyển mã và mã hóa video HLS AES-128 bằng FFmpeg.
* Dữ liệu được lưu trữ trong RDS for PostgreSQL, DynamoDB và S3.
* Frontend ReactJS được phân phối qua CloudFront và bảo vệ bằng AWS WAF.
* Hệ thống phát hiện bất thường bằng AI chạy trên Lambda (container image trên ECR), được kích hoạt bởi DynamoDB Stream và EventBridge.

### Nội dung Workshop

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Các bước chuẩn bị (Prerequisites)](5.2-Prerequiste/)
3. [Triển khai Control Plane trên AWS](5.3-AWS-Control-Plane/)
   - [Amazon Cognito User Pool](5.3.1-cognito/)
   - [Lambda phát hiện bất thường bằng AI](5.3.2-lambda/)
   - [Backend Spring Boot trên ECS](5.3.3-api/)
   - [AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
   - [Cảnh báo qua SNS & EventBridge](5.3.5-sns-eventbridge/)
4. [Triển khai AWS Media Plane](5.4-AWS-Media-Plane/)
   - [S3 storage](5.4.1-s3-storage/)
   - [CloudFront](5.4.2-cloudfront/)
   - [WAF](5.4.3-waf/)
   - [AWS Elemental MediaConvert](5.4.4-mediaconvert/)
5. [Hệ thống giám sát và cảnh báo bằng AI](5.5-AI-Monitoring/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)

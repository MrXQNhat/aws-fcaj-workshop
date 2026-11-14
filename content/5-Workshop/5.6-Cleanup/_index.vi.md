---
title: "Dọn dẹp Tài nguyên"
date: 2026-03-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Sau khi hoàn thành bài thực hành và quay video demo báo cáo, hãy tiến hành xóa toàn bộ tài nguyên đã khởi tạo để tránh phát sinh chi phí ngoài ý muốn.

### 1. Dọn dẹp Tài nguyên trên AWS
1. **Amazon EventBridge:** Xóa lịch chạy tự động `SecureLearn-AI-Scan-Schedule`.
2. **AWS Lambda:** Xóa các hàm `SecureLearn-LicenseService`, `SecureLearn-AnomalyDetection` và các Lambda Layer đi kèm.
3. **Amazon API Gateway:** Xóa `SecureLearn-API`.
4. **Amazon Cognito:** Xóa User Pool `SecureLearnUserPool`.
5. **Amazon DynamoDB & SNS:** Xóa các bảng dữ liệu trên DynamoDB và SNS topic `SecureLearn-AnomalyAlerts`.
6. **AWS Secrets Manager & KMS:** Xóa secret `SecureLearn/CloudflareCredentials` và đặt lịch xóa khóa `SecureLearn-Key` (thời gian chờ tối thiểu 7 ngày).
7. **Amazon CloudWatch:** Xóa thủ công các Log Group còn sót lại do Lambda và API Gateway tạo ra.

### 2. Dọn dẹp Tài nguyên trên Cloudflare
1. **Cloudflare R2:** Xóa toàn bộ video gốc, các phân đoạn HLS/DASH đã chuyển mã và file model `.pkl` trong bucket, sau đó xóa các bucket.
2. **Cloudflare Workers & Queues:** Xóa các Worker `SecureLearn-EdgeAuth`, `SecureLearn-UploadConsumer` và hàng đợi `securelearn-video-uploads`.
3. **Cloudflare Zero Trust:** Xóa ứng dụng và chính sách truy cập `SecureLearn Admin Portal`.

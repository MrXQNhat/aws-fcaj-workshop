---
title: "Các bước chuẩn bị"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Trước khi bắt đầu triển khai workshop, hãy đảm bảo bạn đã chuẩn bị đầy đủ các tài khoản, công cụ và mã nguồn sau:

### 1. Tài khoản Đám mây
* **Tài khoản AWS:** Có quyền IAM để khởi tạo các dịch vụ Cognito, API Gateway, Lambda, DynamoDB, MediaConvert, KMS, Secrets Manager, EventBridge, SNS và CloudWatch.
* **Tài khoản Cloudflare:** Đã kích hoạt **Cloudflare R2**, **Workers & Pages**, **Queues**, và **Cloudflare Zero Trust** (gói Free hỗ trợ tối đa 50 người dùng).

### 2. Môi trường Phát triển Local
* **Backend:** Java 17 (Spring Boot) hoặc Python 3.12 / Node.js 20.x cho các hàm AWS Lambda.
* **Frontend:** Node.js 18+ cùng mã nguồn ReactJS và thư viện `hls.js`.
* **Huấn luyện AI:** Môi trường Python (Jupyter Notebook) cài đặt `scikit-learn`, `numpy`, `scipy` để đóng gói mô hình Isolation Forest (`.pkl`).
* **Công cụ CLI:** Đã cài đặt và cấu hình AWS CLI (`aws configure`) và Cloudflare Wrangler CLI (`npm install -g wrangler`).

### 3. Hạ tầng Cơ sở Sẵn có
* Bucket lưu video gốc và bucket lưu file HLS đầu ra trên **Cloudflare R2**.
* Bảng **Amazon DynamoDB** lưu trữ quyền truy cập khóa học, metadata video và nhật ký truy cập.

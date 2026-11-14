---
title: "Tổng quan Workshop"
date: 2026-11-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Bối cảnh Bài toán & Giải pháp
Các trung tâm đào tạo vừa và nhỏ (SMEs) đối mặt với nguy cơ thất thoát tài sản trí tuệ nghiêm trọng do video bài giảng bị tải xuống, quay màn hình và phát tán trái phép. Các giải pháp DRM thương mại (Widevine, PlayReady) có chi phí bản quyền quá cao và tích hợp phức tạp.

**SecureLearn** giải quyết bài toán bằng kiến trúc AWS–Cloudflare Hybrid Serverless:
* **Lớp Upload & Xử lý (Media Pipeline):** Video tải lên Cloudflare R2 kích hoạt Cloudflare Queues và Consumer Worker điều phối chuyển mã qua **AWS Elemental MediaConvert** sang định dạng HLS/DASH.
* **Lớp Phân phối & Kiểm soát truy cập:** Học viên đăng nhập qua **Amazon Cognito** nhận JWT. **AWS Lambda** kiểm tra quyền học trong **Amazon DynamoDB** và cấp short-lived playback token (HMAC-SHA256, thời hạn 1 giờ) được **Cloudflare Workers** xác thực tại biên.
* **Lớp Bảo mật & Quản lý khóa:** **AWS KMS** và **AWS Secrets Manager** bảo vệ thông tin xác thực API, **AWS CloudTrail** ghi lại toàn bộ nhật ký truy cập.
* **Lớp AI & Truy vết:** **AWS EventBridge** kích hoạt Lambda chạy mô hình **Isolation Forest** mỗi 5 phút để phát hiện hành vi bất thường và gửi cảnh báo qua **Amazon SNS**, kết hợp watermark động hiển thị User ID và IP của học viên.

### 2. Sơ đồ Kiến trúc Hệ thống

![Kiến trúc tổng thể](/content/2-Proposal/general.png)




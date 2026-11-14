---
title: "Workshop"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# SecureLearn: Hệ Thống Bảo Vệ Nội Dung Số Dành Cho Nền Tảng Giáo Dục

Trong bài thực hành (workshop) này, bạn sẽ xây dựng **SecureLearn**, giải pháp bảo vệ nội dung video chi phí thấp dành cho các trung tâm đào tạo vừa và nhỏ (SMEs). Kiến trúc sử dụng mô hình **Hybrid Serverless** kết hợp giữa **AWS** (đảm nhiệm xác thực, phân quyền, chuyển mã video, phát hiện bất thường bằng AI và logic nghiệp vụ) với **Cloudflare** (đảm nhiệm lưu trữ giao diện, lưu trữ object, xác thực token tại biên và phân phối CDN miễn phí băng thông egress).

### Nội dung Workshop

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Các bước chuẩn bị (Prerequisites)](5.2-Prerequiste/)
3. [Triển khai Control Plane trên AWS](5.3-AWS-Control-Plane/)
   - [Amazon Cognito User Pool](5.3.1-cognito/)
   - [Lambda License Service & API Gateway](5.3.2-api-lambda/)
   - [AWS KMS & Secrets Manager](5.3.3-kms-secrets/)
4. [Triển khai Media Plane & Bảo mật biên trên Cloudflare](5.4-Cloudflare-Media-Plane/)
   - [Cloudflare R2 & Hàng đợi sự kiện Queues](5.4.1-r2-queues/)
   - [Cloudflare Worker xác thực Playback tại biên](5.4.2-edge-worker/)
   - [Bảo vệ cổng quản trị với Cloudflare Zero Trust](5.4.3-zero-trust/)
5. [Hệ thống Giám sát AI & Cảnh báo](5.5-AI-Monitoring/)
6. [Dọn dẹp tài nguyên (Clean Up)](5.6-Cleanup/)

---
title: "Hệ thống Giám sát AI & Cảnh báo"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Bước 1: Đóng gói Lambda Layer cho Thư viện AI
1. Huấn luyện mô hình **Isolation Forest** trên môi trường local với dữ liệu log truy cập và xuất ra file `model.pkl`. Tải file `model.pkl` lên thư mục riêng (`models/`) trên Cloudflare R2.
2. Đóng gói các thư viện `scikit-learn`, `numpy`, và `scipy` thành một **Lambda Layer** (đảm bảo dung lượng giải nén dưới 250 MB) để tối ưu thời gian khởi động lạnh (cold start).

### Bước 2: Tạo Hàm Lambda Phát hiện Bất thường
1. Tạo hàm Lambda mới tên `SecureLearn-AnomalyDetection` với runtime **Python 3.12** và gắn Lambda Layer ở Bước 1.
2. Cài đặt logic suy luận:
   * Tải `model.pkl` từ Cloudflare R2 thông qua AWS SDK (tương thích S3 API).
   * Đọc log truy cập và cấp token gần nhất từ **Amazon DynamoDB** hoặc **CloudWatch Logs** (phân tích số lượng IP, tần suất request, phiên đăng nhập đồng thời).
   * Chạy `model.predict()` để phát hiện tài khoản có dấu hiệu chia sẻ hoặc tải lậu hàng loạt.
   * Nếu phát hiện bất thường, gọi `sns.publish()` gửi thông báo tới **Amazon SNS**.

### Bước 3: Tạo SNS Topic Gửi Cảnh báo
1. Truy cập **Amazon SNS** > **Topics** > **Create topic** > **Standard**.
2. **Name:** `SecureLearn-AnomalyAlerts`.
3. Tạo một **Subscription** với giao thức **Email**, nhập địa chỉ email của quản trị viên và nhấn xác nhận trong hộp thư đến.

### Bước 4: Lên lịch Quét Tự động qua EventBridge
1. Truy cập **Amazon EventBridge** > **Schedules** > **Create schedule**.
2. **Schedule name:** `SecureLearn-AI-Scan-Schedule`.
3. **Schedule pattern:** Chạy định kỳ theo chu kỳ `rate(5 minutes)`.
4. **Target:** Chọn hàm Lambda `SecureLearn-AnomalyDetection` và nhấn **Create schedule**.

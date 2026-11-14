---
title: "Cloudflare R2 & Hàng đợi sự kiện Queues"
date: 2026-03-01
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

### Bước 1: Tạo Cloudflare Queue
1. Truy cập **Cloudflare Dashboard** > **Queues** > **Create queue**.
2. **Queue name:** `securelearn-video-uploads`.
3. Nhấn **Create**.

### Bước 2: Bật R2 Event Notifications
1. Truy cập **R2** và chọn bucket lưu trữ video gốc.
2. Vào **Settings** > **Event notifications** > **Add notification**.
3. **Event types:** Chọn `Object created`.
4. **Destination:** Chọn hàng đợi `securelearn-video-uploads`.
5. Nhấn **Save**.

### Bước 3: Triển khai Consumer Worker
1. Tạo Worker mới đặt tên là `SecureLearn-UploadConsumer`.
2. Liên kết hàng đợi `securelearn-video-uploads` làm trigger cho Worker này.
3. Viết logic lắng nghe sự kiện upload từ queue và kích hoạt job chuyển mã trên **AWS Elemental MediaConvert** (hoặc gọi API Gateway để cập nhật trạng thái video).

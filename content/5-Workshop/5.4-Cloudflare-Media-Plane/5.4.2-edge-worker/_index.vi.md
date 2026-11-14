---
title: "Cloudflare Worker xác thực Playback tại biên"
date: 2026-03-01
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Bước 1: Khởi tạo Edge Authorization Worker
1. Truy cập **Cloudflare Dashboard** > **Workers & Pages** > **Create application** > **Create Worker**.
2. **Name:** `SecureLearn-EdgeAuth`.
3. Gắn binding cho **R2 bucket** chứa các file HLS/DASH private (ví dụ đặt tên biến `VIDEO_BUCKET`) và thêm biến bí mật `PLAYBACK_SIGNING_SECRET`.

### Bước 2: Cài đặt Logic Xác thực Token
1. Chặn bắt các yêu cầu HTTP truy xuất file manifest `.m3u8` và các phân đoạn `.ts` / `.m4s`.
2. Trích xuất **short-lived token** cùng thời gian hết hạn (`exp`) từ query string hoặc header.
3. Tính toán lại chữ ký HMAC-SHA256 và kiểm tra:
   * Chữ ký khớp với `PLAYBACK_SIGNING_SECRET`.
   * Thời gian hiện tại chưa vượt quá thời điểm hết hạn (`exp`).
   * Đường dẫn video yêu cầu khớp với Video ID được cấp phép trong token.
4. Nếu hợp lệ, đọc object trực tiếp từ `env.VIDEO_BUCKET` và trả về client với chi phí băng thông egress bằng 0; nếu không hợp lệ, trả về lỗi `HTTP 403 Forbidden`.

### Bước 3: Cấu hình Route Tên miền
1. Mở tab **Triggers** của Worker > **Add route**.
2. Gắn route `video.yourdomain.com/*` vào Worker `SecureLearn-EdgeAuth`.

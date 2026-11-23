---
title: "Amazon S3 lưu trữ video"
date: 2026-11-01
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

### Bước 1: Tạo bucket lưu video
1. Truy cập **Amazon S3** > **Create bucket**.
2. **Bucket type:** General purpose > **Bucket name:** `securelearn-media-<account-id>`.
3. **Block all public access:** Giữ **bật**. Không còn URL public kiểu `pub-*.r2.dev`; video chỉ truy cập được qua presigned URL do backend ký.
4. **Default encryption:** Chọn **SSE-KMS** > chọn khóa `SecureLearn-Key` (mục 5.3.4) > **Bucket Key:** Enable.
5. Nhấn **Create bucket**.

Bucket dùng các thư mục (prefix) sau, backend và Lambda tự tạo khi ghi file:
* `videos/{courseId}/`: video gốc giảng viên upload, bị backend xóa sau khi xử lý xong.
* `hls/{courseId}/{tên-bài}/`: `master.m3u8`, `content.key` (khóa AES-128 16 byte) và các `segment_XXX.ts` đã mã hóa của từng bài học.
* `models/`: file `model.pkl` của Lambda AI.

### Bước 2: Cấu hình CORS
Trình duyệt PUT video gốc (presigned URL 15 phút) và tải segment sau khi backend trả `302` (presigned URL 60 giây) trực tiếp từ S3, nên bucket phải cho phép CORS.
1. Vào tab **Permissions** > **Cross-origin resource sharing (CORS)** > **Edit**, dán:
```json
[
  {
    "AllowedOrigins": ["https://learn.yourdomain.com", "http://localhost:3000"],
    "AllowedMethods": ["GET", "HEAD", "PUT"],
    "AllowedHeaders": ["*"],
    "ExposeHeaders": ["ETag"],
    "MaxAgeSeconds": 3000
  }
]
```
2. Nhấn **Save changes**.

### Bước 3: Tạo Lifecycle rule (khuyến nghị)
1. Vào tab **Management** > **Create lifecycle rule** > **Name:** `cleanup-source-videos`.
2. **Prefix:** `videos/` > tích **Expire current versions of objects** > **Days:** `1`.
3. Nhấn **Create rule** (dọn video gốc còn sót khi xử lý bị lỗi).

### Bước 4: Chuyển dữ liệu cũ từ Cloudflare R2 (nếu có)
Giữ nguyên cấu trúc key (`hls/...`) để không phải sửa cột `url` của bảng `Lesson` trong database:
```bash
# Profile riêng chứa access key R2 cũ
aws configure --profile r2

aws s3 sync s3://securelearnvideo/hls ./r2-backup/hls --profile r2 \
  --endpoint-url https://<r2-account-id>.r2.cloudflarestorage.com
aws s3 sync ./r2-backup/hls s3://securelearn-media-<account-id>/hls --region ap-southeast-1
```
Bài học cũ còn lưu URL public `https://pub-xxxx.r2.dev/...` sẽ được backend (`PlaybackService.normalizeLocation`) tự đổi về object key ở lần phát đầu tiên, miễn là biến `LEGACY_PUBLIC_BASE_URL` của task definition đặt đúng URL cũ. Khi đã chuyển xong, xóa biến này và xóa bucket R2.

### Bước 5: Kiểm tra pipeline
Sau khi hoàn thành mục 5.4.2, đăng nhập bằng tài khoản `INSTRUCTOR`, upload một video ngắn (dưới 1 phút), rồi kiểm tra:
```bash
aws s3 ls s3://securelearn-media-<account-id>/hls/ --recursive | head
aws s3 cp s3://securelearn-media-<account-id>/hls/<courseId>/<tên-bài>/master.m3u8 - | head -8
```
Playlist phải có dòng `#EXT-X-KEY:METHOD=AES-128,URI="securelearn-key"`, và thư mục `videos/` không còn video gốc. Mở URL S3 của một segment mà không có chữ ký sẽ nhận `403 AccessDenied`.

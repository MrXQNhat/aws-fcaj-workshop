---
title: "Frontend & phân phối qua Amazon CloudFront"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Bước 1: Build & tải Frontend lên S3
1. Tạo bucket `securelearn-web-<account-id>` , giữ **Block all public access** bật, **không** bật Static website hosting.
2. Trong thư mục `client/`, tạo file `.env.production` với nội dung `VITE_API_URL=/api/v1` (frontend và API dùng chung tên miền nên không cần CORS).
3. Build và đồng bộ lên bucket:
```bash
npm ci
npm run build
aws s3 sync dist/ s3://securelearn-web-<account-id>/ --delete
```
> `vercel.json`, `nginx.conf` và `client/Dockerfile` không còn dùng khi deploy lên AWS.

### Bước 2: Tạo CloudFront Function cho SPA
1. Truy cập **CloudFront** > **Functions** > **Create function** > **Name:** `securelearn-spa-rewrite`.
2. Dán code sau > **Save changes** > tab **Publish** > **Publish function**:
```js
function handler(event) {
    var request = event.request;
    // Đường dẫn không phải file tĩnh (vd /vi/student/courses) => trả về index.html cho React Router
    if (!request.uri.includes('.')) {
        request.uri = '/index.html';
    }
    return request;
}
```
> Không dùng **Custom error responses** (403/404 → `index.html`) vì cấu hình đó áp dụng cho cả `/api/*` và sẽ biến lỗi JSON của backend thành trang HTML.

### Bước 3: Tạo CloudFront Distribution
1. **Certificate Manager** ở region **us-east-1** > **Request** > domain `learn.yourdomain.com` > DNS validation (CloudFront chỉ dùng chứng chỉ tạo ở us-east-1).
2. **CloudFront** > **Distributions** > **Create distribution**.
3. **Origin domain:** chọn bucket `securelearn-web-<account-id>` > **Origin access:** Origin access control settings > **Create new OAC**.
4. **Default cache behavior:** Redirect HTTP to HTTPS > **Cache policy:** `CachingOptimized` > **Function associations (Viewer request):** `securelearn-spa-rewrite`.
5. **WAF:** chọn Do not enable (gắn ở mục 5.4.3).
6. **Alternate domain name:** `learn.yourdomain.com` > **Custom SSL certificate:** chứng chỉ ở bước 1 > **Default root object:** `index.html`.
7. Nhấn **Create distribution** > **Copy policy** và dán vào **Bucket policy** của bucket `securelearn-web-<account-id>`.

### Bước 4: Thêm Origin & Behavior cho API
1. Tab **Origins** > **Create origin**:
   * **Origin domain:** `origin.yourdomain.com` > **Protocol:** HTTPS only.
   * **Add custom header:** `X-Origin-Verify` = chuỗi bí mật đã tạo ở mục 5.3.3 (Bước 6).
   * **Response timeout:** `60` giây > **Create origin**.
2. Tab **Behaviors** > **Create behavior**:
   * **Path pattern:** `/api/*` > **Origin:** `origin.yourdomain.com`.
   * **Allowed HTTP methods:** GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE.
   * **Cache policy:** `CachingDisabled` > **Origin request policy:** `AllViewer` > **Create behavior**.

> Phải dùng `AllViewer` (không dùng `AllViewerExceptHostHeader`). Backend sinh URL manifest/khóa/segment từ header Host; nếu Host là `origin.yourdomain.com`, trình phát sẽ gọi thẳng ALB mà không có header `X-Origin-Verify` và bị chặn `403`. Với Host = `learn.yourdomain.com` cùng `server.forward-headers-strategy=framework`, URL được sinh đúng là `https://learn.yourdomain.com/api/v1/lesson/...`.

### Bước 5: Trỏ tên miền & kiểm tra
1. **Route 53** > tạo bản ghi `learn.yourdomain.com` (A, Alias) trỏ tới distribution vừa tạo.
2. Gọi thẳng ALB `https://origin.yourdomain.com/api/v1/allcourses` phải nhận `403`; gọi qua `https://learn.yourdomain.com/api/v1/allcourses` nhận `200`.
3. Mở `https://learn.yourdomain.com`, đăng nhập và phát thử một video. Trong DevTools > Network sẽ thấy:
   * `/playback` trả `playbackUrl` trên tên miền `learn.yourdomain.com`.
   * `/manifest` trả playlist có `#EXT-X-KEY:METHOD=AES-128`.
   * `/key` trả 16 byte.
   * Các `/segment/{n}` trả `302`, và file `.ts` được tải từ S3 với `X-Amz-Expires=60`.
4. Vào DynamoDB > `AccessLog` > **Explore items** để kiểm tra log PLAYBACK / MANIFEST_REQUEST / KEY_REQUEST / SEGMENT_REQUEST đã được ghi, với `ipAddress` là IP thật của người dùng.
5. Mỗi lần cập nhật frontend, chạy lại lệnh `aws s3 sync` ở Bước 1 và `aws cloudfront create-invalidation --distribution-id <id> --paths "/*"`.

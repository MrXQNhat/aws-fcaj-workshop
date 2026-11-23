---
title: "Frontend & Delivery with Amazon CloudFront"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Step 1: Build & Upload the Frontend to S3
1. Create bucket `securelearn-web-<account-id>` , keep **Block all public access** on, and do **not** enable Static website hosting.
2. In `client/`, create `.env.production` containing `VITE_API_URL=/api/v1` (frontend and API share one domain, so no CORS is needed).
3. Build and sync to the bucket:
```bash
npm ci
npm run build
aws s3 sync dist/ s3://securelearn-web-<account-id>/ --delete
```
> `vercel.json`, `nginx.conf` and `client/Dockerfile` are not used for the AWS deployment.

### Step 2: Create a CloudFront Function for the SPA
1. Go to **CloudFront** > **Functions** > **Create function** > **Name:** `securelearn-spa-rewrite`.
2. Paste the following code > **Save changes** > **Publish** tab > **Publish function**:
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
> Do not use **Custom error responses** (403/404 → `index.html`): they apply to `/api/*` too and would turn the backend's JSON errors into an HTML page.

### Step 3: Create the CloudFront Distribution
1. **Certificate Manager** in region **us-east-1** > **Request** > domain `learn.yourdomain.com` > DNS validation (CloudFront only uses certificates from us-east-1).
2. **CloudFront** > **Distributions** > **Create distribution**.
3. **Origin domain:** the `securelearn-web-<account-id>` bucket > **Origin access:** Origin access control settings > **Create new OAC**.
4. **Default cache behavior:** Redirect HTTP to HTTPS > **Cache policy:** `CachingOptimized` > **Function associations (Viewer request):** `securelearn-spa-rewrite`.
5. **WAF:** Do not enable (attached in section 5.4.3).
6. **Alternate domain name:** `learn.yourdomain.com` > **Custom SSL certificate:** the certificate from step 1 > **Default root object:** `index.html`.
7. Click **Create distribution** > **Copy policy** and paste it into the **Bucket policy** of `securelearn-web-<account-id>`.

### Step 4: Add the API Origin & Behavior
1. **Origins** tab > **Create origin**:
   * **Origin domain:** `origin.yourdomain.com` > **Protocol:** HTTPS only.
   * **Add custom header:** `X-Origin-Verify` = the secret created in section 5.3.3 (Step 6).
   * **Response timeout:** `60` seconds > **Create origin**.
2. **Behaviors** tab > **Create behavior**:
   * **Path pattern:** `/api/*` > **Origin:** `origin.yourdomain.com`.
   * **Allowed HTTP methods:** GET, HEAD, OPTIONS, PUT, POST, PATCH, DELETE.
   * **Cache policy:** `CachingDisabled` > **Origin request policy:** `AllViewer` > **Create behavior**.

> Use `AllViewer` (not `AllViewerExceptHostHeader`). The backend builds manifest/key/segment URLs from the Host header; if Host is `origin.yourdomain.com`, the player calls the ALB directly without the `X-Origin-Verify` header and gets `403`. With Host = `learn.yourdomain.com` plus `server.forward-headers-strategy=framework`, the URLs are correctly built as `https://learn.yourdomain.com/api/v1/lesson/...`.

### Step 5: Point the Domain & Test
1. **Route 53** > create record `learn.yourdomain.com` (A, Alias) pointing to the new distribution.
2. Calling the ALB directly at `https://origin.yourdomain.com/api/v1/allcourses` must return `403`; through `https://learn.yourdomain.com/api/v1/allcourses` it returns `200`.
3. Open `https://learn.yourdomain.com`, sign in and play a video. In DevTools > Network you should see:
   * `/playback` returns a `playbackUrl` on the `learn.yourdomain.com` domain.
   * `/manifest` returns a playlist with `#EXT-X-KEY:METHOD=AES-128`.
   * `/key` returns 16 bytes.
   * Each `/segment/{n}` returns `302`, and the `.ts` file is downloaded from S3 with `X-Amz-Expires=60`.
4. Go to DynamoDB > `AccessLog` > **Explore items** to check that PLAYBACK / MANIFEST_REQUEST / KEY_REQUEST / SEGMENT_REQUEST logs were written, with `ipAddress` being the user's real IP.
5. Each time the frontend changes, rerun the `aws s3 sync` command from Step 1 and `aws cloudfront create-invalidation --distribution-id <id> --paths "/*"`.

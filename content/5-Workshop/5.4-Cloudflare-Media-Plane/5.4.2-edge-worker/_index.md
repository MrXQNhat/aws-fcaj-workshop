---
title: "Edge Playback Authorization Worker"
date: 2026-03-01
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

### Step 1: Create Edge Authorization Worker
1. Go to **Cloudflare Dashboard** > **Workers & Pages** > **Create application** > **Create Worker**.
2. **Name:** `SecureLearn-EdgeAuth`.
3. Bind your private HLS/DASH **R2 bucket** to the Worker (e.g., variable name `VIDEO_BUCKET`) and add `PLAYBACK_SIGNING_SECRET` as an encrypted Worker secret.

### Step 2: Implement Token Verification Logic
1. Intercept incoming HTTP requests for `.m3u8` manifests and `.ts` / `.m4s` video segments.
2. Extract the **short-lived token** and expiration timestamp from query parameters or custom headers.
3. Recompute the HMAC-SHA256 signature and verify:
   * Signature matches `PLAYBACK_SIGNING_SECRET`.
   * Current timestamp is strictly less than the token's expiration (`exp`).
   * Requested video path matches the authorized video ID in the token payload.
4. If valid, fetch the object directly from `env.VIDEO_BUCKET` and stream it with zero egress fees; otherwise, return `HTTP 403 Forbidden`.

### Step 3: Configure Custom Route
1. Open the Worker's **Triggers** tab > **Add route**.
2. Map the route `video.yourdomain.com/*` to `SecureLearn-EdgeAuth`.

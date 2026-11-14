---
title: "Cloudflare R2 & Event Queues"
date: 2026-03-01
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

### Step 1: Create Cloudflare Queue
1. Log in to the **Cloudflare Dashboard** > **Queues** > **Create queue**.
2. **Queue name:** `securelearn-video-uploads`.
3. Click **Create**.

### Step 2: Enable R2 Event Notifications
1. Navigate to **R2** and select your source video bucket.
2. Open **Settings** > **Event notifications** > **Add notification**.
3. **Event types:** Select `Object created`.
4. **Destination:** Select queue `securelearn-video-uploads`.
5. Click **Save**.

### Step 3: Deploy Consumer Worker
1. Create a new Worker named `SecureLearn-UploadConsumer`.
2. Bind the `securelearn-video-uploads` queue as a consumer trigger.
3. Implement logic to read upload event messages and trigger **AWS Elemental MediaConvert** (or notify your backend API Gateway) to transcode the raw video into HLS/DASH segments.

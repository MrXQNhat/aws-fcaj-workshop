---
title: "AI Anomaly Detection & Alerting"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Step 1: Package Lambda Layer for Isolation Forest Dependencies
1. Train the **Isolation Forest** anomaly detection model offline on simulated access logs and serialize it to `model.pkl`. Upload `model.pkl` to a dedicated prefix (`models/`) on Cloudflare R2.
2. Package `scikit-learn`, `numpy`, and `scipy` into a **Lambda Layer** (keeping uncompressed size under 250 MB) to optimize cold start latency.

### Step 2: Create AI Anomaly Detection Lambda
1. Create a new Lambda function named `SecureLearn-AnomalyDetection` using **Python 3.12** runtime and attach the Lambda Layer from Step 1.
2. Implement inference logic:
   * Download `model.pkl` from Cloudflare R2 via the S3-compatible SDK.
   * Query recent playback/token logs from **Amazon DynamoDB** or **CloudWatch Logs** (checking IP diversity, request frequency, and concurrent sessions).
   * Run `model.predict()` to flag anomalous accounts.
   * If anomalies are detected, publish an alert payload to **Amazon SNS**.

### Step 3: Create Amazon SNS Alert Topic
1. Open **Amazon SNS** > **Topics** > **Create topic** > **Standard**.
2. **Name:** `SecureLearn-AnomalyAlerts`.
3. Create an **Email** subscription with the administrator's email address and confirm the subscription link sent to the inbox.

### Step 4: Schedule Periodic Scans via EventBridge
1. Go to **Amazon EventBridge** > **Schedules** > **Create schedule**.
2. **Schedule name:** `SecureLearn-AI-Scan-Schedule`.
3. **Schedule pattern:** Regular rate of `rate(5 minutes)`.
4. **Target:** Select Lambda function `SecureLearn-AnomalyDetection` and click **Create schedule**.

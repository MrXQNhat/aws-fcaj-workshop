---
title: "Clean Up Resources"
date: 2026-03-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

After completing the workshop and recording your demonstration, clean up all provisioned resources to avoid unexpected charges.

### 1. Clean Up AWS Resources
1. **Amazon EventBridge:** Delete the `SecureLearn-AI-Scan-Schedule` rule/schedule.
2. **AWS Lambda:** Delete `SecureLearn-LicenseService`, `SecureLearn-AnomalyDetection`, and any associated Lambda Layers.
3. **Amazon API Gateway:** Delete `SecureLearn-API`.
4. **Amazon Cognito:** Delete the `SecureLearnUserPool` user pool.
5. **Amazon DynamoDB & SNS:** Delete the entitlement/log tables and the `SecureLearn-AnomalyAlerts` SNS topic.
6. **AWS Secrets Manager & KMS:** Delete the `SecureLearn/CloudflareCredentials` secret and schedule deletion for `SecureLearn-Key` (minimum 7-day waiting period).
7. **Amazon CloudWatch:** Manually delete remaining CloudWatch Log Groups created by Lambda and API Gateway.

### 2. Clean Up Cloudflare Resources
1. **Cloudflare R2:** Delete all uploaded source videos, transcoded HLS/DASH segments, and the `.pkl` model file inside your R2 buckets, then delete the buckets.
2. **Cloudflare Workers & Queues:** Delete `SecureLearn-EdgeAuth`, `SecureLearn-UploadConsumer`, and the `securelearn-video-uploads` queue.
3. **Cloudflare Zero Trust:** Remove the `SecureLearn Admin Portal` Access application and policy.

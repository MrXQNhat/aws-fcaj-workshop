---
title: "Workshop Overview"
date: 2026-11-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Problem Statement & Solution Overview
Small and medium-sized educational centers (SMEs) face severe intellectual property leakage when lecture videos are downloaded, screen-recorded, and redistributed illegally. Commercial DRM solutions (Widevine, PlayReady) carry prohibitive licensing fees and integration complexity.

**SecureLearn** is an LMS with a ReactJS frontend and a Spring Boot backend that protects videos in several layers:
* **Media Pipeline:** Instructors upload source videos directly to Amazon S3 using presigned URLs. The Spring Boot backend on Amazon ECS Fargate uses MediaConvert to convert them to HLS (6-second segments) and encrypts every segment with AES-128 using a per-lesson key.
* **Delivery & Access Control:** Students authenticate via Amazon Cognito (JWT). The backend issues a playback token (HMAC-SHA256, 2-hour TTL), only returns the decryption key to the token holder, and serves each segment through a 60-second S3 presigned URL. The ReactJS frontend is delivered through Amazon CloudFront (filtered by AWS WAF), and business data lives in Amazon RDS for PostgreSQL.
* **Security & Key Management:** **AWS KMS** and **AWS Secrets Manager** protect API credentials, while **AWS CloudTrail** logs all audit events.
* **AI & Leak Tracing:** **AWS EventBridge** triggers an **Isolation Forest** Lambda function every 5 minutes to detect abnormal access patterns and send alerts via **Amazon SNS**, paired with a JavaScript dynamic watermark displaying the student's User ID and IP.

### 2. Architecture Diagrams

![General Architecture](general.png)


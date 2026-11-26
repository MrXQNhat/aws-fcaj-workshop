---
title: "AWS Control Plane Setup"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

In this section, you will configure Cognito authentication and the ECS-hosted Spring Boot backend, protect application secrets with KMS, and connect AI anomaly alerts. The backend handles API routing, authorization, and playback-token generation; the separate Lambda is for AI anomaly detection.

### Sub-sections
1. [5.3.1 Create Amazon Cognito User Pool](5.3.1-cognito/)
2. [5.3.2 Configure the AI Anomaly Detection Lambda](5.3.2-lambda/)
3. [5.3.3 Deploy the Spring Boot Backend on ECS](5.3.3-api/)
4. [5.3.4 Configure AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
5. [5.3.5 Configure SNS & EventBridge Alerts](5.3.5-sns-eventbridge/)
---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# SecureLearn: A Digital Content Protection System for Educational Platforms

In this workshop, you will build **SecureLearn**, a cost-effective video content protection solution designed for small and medium-sized educational centers (SMEs). The architecture utilizes a **Hybrid Serverless** model, combining **AWS**—which handles authentication, authorization, video transcoding, AI-based anomaly detection, and business logic—with **Cloudflare**, which manages interface hosting, object storage, edge-based token authentication, and CDN distribution (with no egress bandwidth costs).

### Workshop Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Deploying the Control Plane on AWS](5.3-AWS-Control-Plane/)
- [Amazon Cognito User Pool](5.3.1-cognito/)
- [Lambda License Service & API Gateway](5.3.2-api-lambda/)
- [AWS KMS & Secrets Manager](5.3.3-kms-secrets/)
4. [Deploying the AWS-Media-Plane](5.4-AWS-Media-Plane/)
- [S3 storage ](5.4.1-s3-storage/)
- [Cloudfont](5.4.2-cloudfont/)
- [WAF](5.4.3-waf/)
5. [AI Monitoring & Alerting System](5.5-AI-Monitoring/)
6. [Clean Up](5.6-Cleanup/)

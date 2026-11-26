---
title: "Workshop"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# SecureLearn: A Digital Content Protection System for Educational Platforms

In this workshop, you will build **SecureLearn**, a cost-effective video content protection solution designed for small and medium-sized educational centers (SMEs). The whole system run on **AWS:**
* A Spring Boot backend runs on ECS Fargate behind an Application Load Balancer. It handles authentication through Cognito, issues playback tokens, and transcodes + encrypts AES-128 HLS with FFmpeg.
* Data lives in RDS for PostgreSQL, DynamoDB and S3.
* The ReactJS frontend is delivered through CloudFront, protected by AWS WAF.
* An AI anomaly detection system runs on Lambda (container image on ECR), triggered by a DynamoDB Stream and EventBridge.


### Workshop Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Deploying the Control Plane on AWS](5.3-AWS-Control-Plane/)
   - [Amazon Cognito User Pool](5.3.1-cognito/)
   - [AI Anomaly Detection Lambda](5.3.2-lambda/)
   - [Spring Boot Backend on ECS](5.3.3-api/)
   - [AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
   - [SNS & EventBridge Alerts](5.3.5-sns-eventbridge/)
4. [Deploying the AWS-Media-Plane](5.4-AWS-Media-Plane/)
   - [S3 storage](5.4.1-s3-storage/)
   - [CloudFront](5.4.2-cloudfront/)
   - [WAF](5.4.3-waf/)
   - [AWS Elemental MediaConvert](5.4.4-mediaconvert/)
5. [AI Monitoring & Alerting System](5.5-AI-Monitoring/)
6. [Clean Up](5.6-Cleanup/)

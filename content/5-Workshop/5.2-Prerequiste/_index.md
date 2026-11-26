---
title: "Prerequisites"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Before starting the workshop, ensure you have the following accounts, tools, and source code ready:

### 1. Cloud Accounts
* **AWS Account:** Active account with IAM permissions to create Cognito, API Gateway, Lambda, DynamoDB, MediaConvert, KMS, Secrets Manager, EventBridge, SNS, and CloudWatch resources.
* **Primary Region:** us-east-1 (N. Virginia). The **ACM certificate for CloudFront** and the **AWS WAF web ACL** (CloudFront scope) must be created in `us-east-1`.
* A **domain** managed in Route 53 (e.g. `securelearn.com`), used for:
  * `learn.securelearn.com` → CloudFront (what users open).
  * `origin.securelearn.com` → ALB (CloudFront calls the backend over HTTPS).

### 2. Local Development Environment
* **Backend:** Java 17 (Spring Boot) or Python 3.12 / Node.js 20.x for AWS Lambda functions.
* **Frontend:** Node.js 18+ with ReactJS and `hls.js` player library.
* **AI Model Training:** Python environment (Jupyter Notebook) with `scikit-learn`, `numpy`, and `scipy` to serialize the Isolation Forest model (`.pkl`).
* **CLI Tools:** * **CLI Tools:** AWS CLI configured (`aws configure`), Docker Desktop, and Python 3 with `boto3` for the AI Lambda test-log script.


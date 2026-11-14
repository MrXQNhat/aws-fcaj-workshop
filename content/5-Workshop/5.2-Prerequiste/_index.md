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
* **Cloudflare Account:** Active account with access to **Cloudflare R2**, **Workers & Pages**, **Queues**, and **Cloudflare Zero Trust** (Free Plan supports up to 50 users).

### 2. Local Development Environment
* **Backend:** Java 17 (Spring Boot) or Python 3.12 / Node.js 20.x for AWS Lambda functions.
* **Frontend:** Node.js 18+ with ReactJS and `hls.js` player library.
* **AI Model Training:** Python environment (Jupyter Notebook) with `scikit-learn`, `numpy`, and `scipy` to serialize the Isolation Forest model (`.pkl`).
* **CLI Tools:** AWS CLI configured (`aws configure`) and Cloudflare Wrangler CLI (`npm install -g wrangler`).

### 3. Existing Base Components
* Source video bucket and HLS output bucket created on **Cloudflare R2**.
* **Amazon DynamoDB** table created for storing course entitlements, video metadata, and access logs.

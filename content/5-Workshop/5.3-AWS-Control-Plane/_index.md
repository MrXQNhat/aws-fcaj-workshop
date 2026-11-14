---
title: "AWS Control Plane Setup"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

In this section, you will deploy the core backend control plane on AWS to handle user authentication, license verification, short-lived playback token generation, and secret key encryption.

### Sub-sections
1. [5.3.1 Create Amazon Cognito User Pool](5.3.1-cognito/)
2. [5.3.2 Configure Lambda License Service](5.3.2-lambda/)
3. [5.3.3 Create API Gateway](5.3.3-api/)
4. [5.3.4 Configure AWS KMS & Secrets Manager](5.3.4-kms-secrets/)
5. [5.3.5 Create SNS & Eventbridge](5.3.5-sns-eventbridge/)
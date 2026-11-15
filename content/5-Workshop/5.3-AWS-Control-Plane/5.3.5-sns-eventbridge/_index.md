---
title: "SNS & EVENTBRIDGE"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.3.5. </b> "
---

### Configure SNS & EventBridge (Scheduler)
1. **Create SNS:** Go to Amazon SNS > Topics > Create topic.

2. Select Standard. **Name:** SecureLearn-Alerts > Create.
![](image.png)

3. Create Subscription: Protocol select Email, Endpoint fill in your email. Confirm via email.
![](image-1.png)

(Copy the RNA of this SNS Topic, paste it into the SNS_TOPIC_ARN environment variable of Lambda AI Anomaly Detection.)

### Create EventBridge Scheduler
1. Go to Amazon EventBridge > Scheduler > Create schedule.

2. Name: AI-Scan.

3. Schedule pattern: Select Recurring schedule > Rate-based schedule > Fill in 5 minutes.
![](image-2.png)

4. Target: Select AWS Lambda > Select SecureLearn-AnomalyDetection.
![](image-3.png)

5. Click Create schedule.
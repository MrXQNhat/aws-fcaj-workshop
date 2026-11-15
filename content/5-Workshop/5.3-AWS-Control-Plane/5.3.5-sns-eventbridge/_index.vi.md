---
title: "SNS & EVENTBRIDGE"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.3.5. </b> "
---

### Cấu hình SNS & EventBridge (Scheduler)
1. **Tạo SNS:** Truy cập Amazon SNS > Topics > Create topic.

2. Chọn Standard. **Name:** SecureLearn-Alerts > Create.
![](image.png)

3. Tạo Subscription: Protocol chọn Email, Endpoint điền email của bạn. Xác nhận qua email.
![](image-1.png)

(Copy ARN của SNS Topic này, dán vào biến môi trường SNS_TOPIC_ARN của Lambda AI Anomaly Detection.)

### Tạo EventBridge Scheduler 
1. Truy cập Amazon EventBridge > Scheduler > Create schedule.

2. Name: AI-Scan.

3. Schedule pattern: Chọn Recurring schedule > Rate-based schedule > Điền 5 minutes.
![](image-2.png)

4. Target: Chọn AWS Lambda > Chọn SecureLearn-AnomalyDetection.
![](image-3.png)

5. Nhấn Create schedule.
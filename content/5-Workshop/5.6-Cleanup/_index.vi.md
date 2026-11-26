---
title: "Dọn dẹp Tài nguyên"
date: 2026-03-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Sau khi hoàn thành bài thực hành và quay video demo báo cáo, hãy tiến hành xóa toàn bộ tài nguyên đã khởi tạo để tránh phát sinh chi phí ngoài ý muốn.

### 1. Dọn dẹp Tài nguyên trên AWS
1. AWS WAF & CloudFront (us-east-1 / Global): Bỏ liên kết và xóa Web ACL securelearn-waf, IP set securelearn-admin-allowlist; Disable rồi Delete distribution, xóa CloudFront Function securelearn-spa-rewrite.
2. Amazon ECS & ECR: Đặt desired count của securelearn-backend-svc về 0, xóa service, cluster securelearn-cluster, deregister task definition securelearn-backend và xóa repository `securelearn-backend`.
3. Elastic Load Balancing & ACM: Xóa securelearn-alb, target group securelearn-backend-tg, sau đó xóa chứng chỉ ACM ở cả ap-southeast-1 (ALB) và us-east-1 (CloudFront).
4. Amazon RDS: Xóa securelearn-db (bỏ chọn final snapshot nếu không cần giữ dữ liệu) và subnet group securelearn-db-subnets.
5. Amazon DynamoDB: Xóa bảng `AnomalyAlert`; chỉ xóa bảng `AccessLog` sau khi đã dừng backend và không còn cần dữ liệu.
6. Amazon S3: Làm rỗng và xóa các bucket `securelearn-media-<account-id>`, `securelearn-web-<account-id>` và `securelearn-models-<account-id>`.
7. AWS Secrets Manager & KMS: Xóa secret securelearn/backend và đặt lịch xóa khóa SecureLearn-Key (thời gian chờ tối thiểu 7 ngày), chỉ sau khi đã xóa bucket video.
8. Amazon Cognito: Xóa User Pool SecureLearn.
9. Route 53: Xóa bản ghi learn, origin và các bản ghi xác thực ACM.
10. Amazon VPC: Xóa securelearn-vpc (kèm subnet, Security Group và S3 Gateway endpoint).
11. AWS Lambda, EventBridge, SNS & ECR: Xóa event-source mapping và Lambda function, rule EventBridge `securelearn-anomaly-every-5min` cùng target, SNS topic `securelearn-anomaly-alerts` và repository ECR `securelearn-anomaly`.
12. IAM: Xóa các role `securelearn-ecs-execution-role`, `securelearn-ecs-task-role` và `securelearn-anomaly-lambda-role`.
13. Amazon CloudWatch & CloudTrail: Xóa thủ công các log group ECS và Lambda còn sót lại; xóa trail `securelearn-trail` nếu đã tạo.

```powershell
aws events remove-targets --rule securelearn-anomaly-every-5min --ids 1 --region $REGION
aws events delete-rule --name securelearn-anomaly-every-5min --region $REGION
$ESM = aws lambda list-event-source-mappings --function-name $FUNC --region $REGION --query "EventSourceMappings[0].UUID" --output text
aws lambda delete-event-source-mapping --uuid $ESM --region $REGION
aws lambda delete-function --function-name $FUNC --region $REGION
aws ecr delete-repository --repository-name $REPO --force --region $REGION
aws iam delete-role-policy --role-name $ROLE --policy-name securelearn-anomaly-access
aws iam detach-role-policy --role-name $ROLE --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
aws iam delete-role --role-name $ROLE
$TOPIC = aws sns list-topics --region $REGION --query "Topics[?ends_with(TopicArn, ':securelearn-anomaly-alerts')].TopicArn" --output text
aws sns delete-topic --topic-arn $TOPIC --region $REGION
aws s3 rb "s3://$BUCKET" --force
aws dynamodb delete-table --table-name AnomalyAlert --region $REGION
aws logs delete-log-group --log-group-name /aws/lambda/$FUNC --region $REGION
```
Các lệnh trên xóa: rule EventBridge securelearn-anomaly-every-5min, kết nối stream, Lambda securelearn-anomaly-detection, repository ECR securelearn-anomaly, role securelearn-anomaly-lambda-role, SNS topic securelearn-anomaly-alerts, bucket securelearn-models-<account-id>, bảng AnomalyAlert và log group của Lambda. Bảng AccessLog dùng chung với backend.

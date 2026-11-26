---
title: "Clean Up Resources"
date: 2026-03-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

After completing the workshop and recording your demonstration, clean up all provisioned resources to avoid unexpected charges.

### 1. Clean Up AWS Resources
1. AWS WAF & CloudFront (us-east-1 / Global): Disassociate and delete the Web ACL `securelearn-waf` and the IP set `securelearn-admin-allowlist`; disable and then delete the distribution, and remove the CloudFront Function `securelearn-spa-rewrite`.
2. Amazon ECS & ECR: Set the desired count for `securelearn-backend-svc` to 0, delete the service and the cluster `securelearn-cluster`, deregister the task definition `securelearn-backend`, and delete the `securelearn-backend` repository.
3. Elastic Load Balancing & ACM: Delete the ALB `securelearn-alb` and the target group `securelearn-backend-tg`, then delete the ACM certificates in both `ap-southeast-1` (for the ALB) and `us-east-1` (for CloudFront).
4. Amazon RDS: Delete the database `securelearn-db` (deselect the final snapshot option if you do not need to retain the data) and the subnet group `securelearn-db-subnets`.
5. Amazon DynamoDB: Delete `AnomalyAlert`; delete the `AccessLog` table only after the backend has been shut down and the data is no longer needed.
6. Amazon S3: Empty and delete the buckets `securelearn-media-<account-id>`, `securelearn-web-<account-id>`, and `securelearn-models-<account-id>`.
7. AWS Secrets Manager & KMS: Delete the secret `securelearn/backend` and schedule the deletion of the KMS key `SecureLearn-Key` (with a minimum waiting period of 7 days)—perform this only after the video bucket has been deleted.
8. Amazon Cognito: Delete the User Pool `SecureLearn`.
9. Route 53: Delete the `learn` and `origin` records, as well as any ACM validation records.
10. Amazon VPC: Delete the VPC `securelearn-vpc` (including its subnets, security groups, and S3 Gateway endpoint).
11. AWS Lambda, EventBridge, SNS & ECR: Delete the Lambda event-source mapping and function, the `securelearn-anomaly-every-5min` EventBridge rule and its target, the `securelearn-anomaly-alerts` SNS topic, and the `securelearn-anomaly` ECR repository.
12. IAM: Delete the roles `securelearn-ecs-execution-role`, `securelearn-ecs-task-role`, and `securelearn-anomaly-lambda-role`.
13. Amazon CloudWatch & CloudTrail: Manually delete any remaining ECS and Lambda log groups; delete the trail `securelearn-trail` if it was created.
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
The above commands delete: EventBridge rule securelearn-anomaly-every-5min, stream connection, Lambda securelearn-anomaly-detection, ECR repository securelearn-anomaly, role securelearn-anomaly-lambda-role, SNS topic securelearn-anomaly-alerts, bucket securelearn-models-<account-id>, table AnomalyAlert and log group of Lambda. The AccessLog table is shared with the backend.
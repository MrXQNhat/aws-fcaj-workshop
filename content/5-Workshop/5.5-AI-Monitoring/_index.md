---
title: "AI Anomaly Detection & Alerting"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Guide to Deploying Anomaly Lambda on AWS

This document provides instructions for deploying the **AI Detection & Alert** component of SecureLearn from scratch, entirely on AWS:

- **Amazon S3** stores the Isolation Forest model.
- **Amazon ECR** stores the Lambda container image.
- **AWS Lambda** (`securelearn-anomaly-detection`) performs feature engineering, scoring, and alert generation.
- **DynamoDB Stream** (from the `AccessLog` table) triggers the Lambda function whenever a new log entry is added. This serves as the primary, near-real-time processing pipeline.
- **EventBridge** invokes the Lambda function every 5 minutes as a **fallback mechanism** in case of stream delays or errors.
- Detected anomalies are written to the **DynamoDB `AnomalyAlert`** table, and notifications are sent via **Amazon SNS**. Logs are recorded in **CloudWatch**.

Target Region: `ap-southeast-1` (Singapore).

---

## Important Notes Before You Begin

- **You must use PowerShell; do not use Command Prompt (cmd).**
- If your prompt appears as `D:\...>`, you are in cmd. Type `powershell` and press Enter to switch. 
- Once the prompt changes to `PS D:\...>`, you are in PowerShell. 
- Running these commands in cmd will result in the error: `'$REGION' is not recognized as an internal or external command`.
- **Variables persist only within the current window.** If you close and reopen PowerShell, you must re-run the variable-setting block in Step 0. The `clear`/`cls` command only clears the screen; it does not delete variables.
- **No web application is required.** The steps below focus exclusively on AWS infrastructure. The `seed_test_logs.py` script writes simulated logs directly into DynamoDB.
- **Skip creation commands if resources already exist.** If a `create-...` command returns an error such as `already exists`, `ResourceInUseException`, or `EntityAlreadyExists`, it means the resource is already present. Simply skip that command and proceed to the next step. ---

## What is uploaded and where

| Component | Destination |
|---|---|
| `isolation_forest_securelearn_v1.pkl` | **S3** (Step 1). This is the only file uploaded to S3. |
| `handler.py`, `requirements.txt`, `Dockerfile` | Packaged into a **container image on ECR** (Step 5). |
| `lambda-env.json`, `lambda-policy.json`, `trust-policy.json`, `filter-criteria.json` | Used locally when running AWS CLI commands; not uploaded. |
| `seed_test_logs.py`, `README.md` | Used locally only. |

> The model must be `isolation_forest_securelearn_v1.pkl`: a single file containing 12 features, with no scaler used. **Do not use** the `isolation_forest.joblib` + `scaler.joblib` + `feature_cols.json` set from the previous design. The `handler.py` script does not read a scaler; using the old files will result in errors or incorrect scoring.

---

## Step 0: Preparation

You will need:

- **AWS CLI v2**, with `aws configure` executed using an account that has administrator privileges.
- **Docker Desktop** (must be running).
- **Python 3 with boto3** (`pip install boto3`), to run the script for injecting test logs.
- The `anomaly-lambda` directory (extracted from `anomaly-lambda.zip`), with the `isolation_forest_securelearn_v1.pkl` file already placed inside. Open **PowerShell** in the `anomaly-lambda` directory and set the following variables:

```powershell
$REGION  = "ap-southeast-1"
$ACCOUNT = aws sts get-caller-identity --query Account --output text
$REPO    = "securelearn-anomaly"
$IMAGE   = "$ACCOUNT.dkr.ecr.$REGION.amazonaws.com/${REPO}:v2"
$FUNC    = "securelearn-anomaly-detection"
$ROLE    = "securelearn-anomaly-lambda-role"
$BUCKET  = "securelearn-models-$ACCOUNT"
echo $ACCOUNT
```

The last line should output your 12-digit AWS account ID. If it is empty or returns an error, run `aws configure` and then set the variables again.

---

## Step 1: Create an S3 bucket and upload the model

```powershell
aws s3 mb "s3://$BUCKET" --region $REGION

aws s3api put-public-access-block --bucket $BUCKET `
--public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"

aws s3 cp .\isolation_forest_securelearn_v1.pkl "s3://$BUCKET/models/isolation_forest_securelearn_v1.pkl"

aws s3 ls "s3://$BUCKET/models/"
```

- The final command should list the `.pkl` file.
- The path must be `models/...`, as the IAM policy restricts the Lambda function to reading only from this directory.
- This bucket is separate from the HLS video bucket; it blocks all public access and uses SSE-S3 encryption (by default). The Lambda function accesses the model via an IAM role, requiring no access keys.

---

## Step 2: Insert the account ID into the JSON file

No manual editing is required. The following command replaces all occurrences of `ACCOUNT_ID` in `lambda-policy.json` and `lambda-env.json` with the value of `$ACCOUNT`:

```powershell
echo $ACCOUNT   # Ensure a 12-digit value is displayed before running the command below

foreach ($f in "lambda-policy.json", "lambda-env.json") {
(Get-Content $f -Raw) -replace "ACCOUNT_ID", $ACCOUNT | Set-Content $f -NoNewline
}
```

Verification:

```powershell
Select-String -Path lambda-policy.json, lambda-env.json -Pattern "ACCOUNT_ID"
Select-String -Path lambda-env.json -Pattern "MODEL_BUCKET"
```

- If the first command **outputs nothing**, it means all replacements were successful.
- The second command should output `"MODEL_BUCKET": "securelearn-models-<12-digit-value>"`.

> If `$ACCOUNT` is empty when the command is executed, `ACCOUNT_ID` will be replaced by an empty string, resulting in corrupted files. In this case, extract the two files again from the zip archive and rerun the process.

---

## Step 3: Create a DynamoDB table and enable streams

### 3.1. AccessLog table (with streams enabled)
If the SE team has already created the `AccessLog` table (including the `byDate` GSI), skip this command and proceed to 3.2.

```powershell
aws dynamodb create-table --table-name AccessLog `
--attribute-definitions AttributeName=userId,AttributeType=S AttributeName=accessTime,AttributeType=S AttributeName=logDate,AttributeType=S `
--key-schema AttributeName=userId,KeyType=HASH AttributeName=accessTime,KeyType=RANGE `
--global-secondary-indexes "IndexName=byDate,KeySchema=[{AttributeName=logDate,KeyType=HASH},{AttributeName=accessTime,KeyType=RANGE}],Projection={ProjectionType=ALL}" `
--stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE `
--billing-mode PAY_PER_REQUEST --region $REGION
```

### 3.2. Enabling Streams for an Existing `AccessLog` Table

> **Notify the SE team before performing this step:**
> - If they are currently modifying the `AccessLog` table structure (e.g., adding or removing an index), your command will fail, as DynamoDB only allows one table update operation at a time.
> - Enabling streams will not disrupt backend read/write operations.
> - Once enabled, any log written to `AccessLog` (including test logs from the SE team) will trigger the Lambda function. The backend must ensure that logs follow the format specified at the end of this document.

Check whether streams are already enabled:

```powershell
aws dynamodb describe-table --table-name AccessLog --region $REGION --query "Table.StreamSpecification"
```

- If the result is `null`, enable streams:
```powershell
aws dynamodb update-table --table-name AccessLog `
--stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE --region $REGION
Start-Sleep -Seconds 10
```
- If `"StreamEnabled": true` is already present, skip this step. ### 3.3. AnomalyAlert Table (without streams)

```powershell
aws dynamodb create-table --table-name AnomalyAlert `
--attribute-definitions AttributeName=userId,AttributeType=S AttributeName=alertTime,AttributeType=S `
--key-schema AttributeName=userId,KeyType=HASH AttributeName=alertTime,KeyType=RANGE `
--billing-mode PAY_PER_REQUEST --region $REGION
```

> **Do not enable streams on the `AnomalyAlert` table.** Since a Lambda function writes to this table, enabling streams would trigger the Lambda function again, creating an infinite loop.

---

## Step 4: Create an SNS topic for alert emails

```powershell
$TOPIC = aws sns create-topic --name securelearn-anomaly-alerts --region $REGION --query TopicArn --output text
aws sns subscribe --topic-arn $TOPIC --protocol email --notification-endpoint "email-admin@gmail.com" --region $REGION
```

- Replace `email-admin@gmail.com` with your administrator email address.
- Open your email inbox and click **Confirm subscription**. If you do not confirm, you will not receive any alert emails.
- It is safe to run `create-topic` multiple times; if the topic already exists, the command will simply return the existing ARN.

---

## Step 5: Build the image and push it to ECR

```powershell
aws ecr create-repository --repository-name $REPO --region $REGION   # skip if it already exists

aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin "$ACCOUNT.dkr.ecr.$REGION.amazonaws.com"

docker buildx build --platform linux/amd64 --provenance=false --sbom=false -t $IMAGE --push .
```

The flags `--provenance=false` and `--sbom=false` are mandatory. If omitted, Lambda will reject the image with the error: "image manifest is not supported." ---

## Step 6: Create an IAM Role for Lambda

```powershell
aws iam create-role --role-name $ROLE --assume-role-policy-document file://trust-policy.json   # skip if it already exists

aws iam attach-role-policy --role-name $ROLE `
--policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

aws iam put-role-policy --role-name $ROLE --policy-name securelearn-anomaly-access `
--policy-document file://lambda-policy.json

$ROLE_ARN = aws iam get-role --role-name $ROLE --query Role.Arn --output text
Start-Sleep -Seconds 15   # wait for the IAM role to take effect
```

The role is granted only the necessary permissions (following the principle of Least Privilege):

- `s3:GetObject` within the `models/` directory of the model bucket.
- Querying the `AccessLog` table and the `byDate` GSI.
- Reading the stream from the `AccessLog` table.
- Querying and performing `PutItem` operations on the `AnomalyAlert` table.
- `sns:Publish` to the `securelearn-anomaly-alerts` topic.

Using `put-role-policy` with the same name will overwrite any existing policy, making it safe to run the command multiple times. ---

## Step 7: Create a Lambda function from an image

Check if the function already exists:

```powershell
aws lambda get-function --function-name $FUNC --region $REGION --query Configuration.State
```

### Scenario A: The function does not exist (returns `ResourceNotFoundException`)

```powershell
aws lambda create-function --function-name $FUNC `
--package-type Image --code ImageUri=$IMAGE `
--role $ROLE_ARN --architectures x86_64 `
--memory-size 1024 --timeout 120 `
--environment file://lambda-env.json --region $REGION

aws lambda wait function-active-v2 --function-name $FUNC --region $REGION
```

### Scenario B: The function already exists (returns `"Active"`)

Update the code, followed by the environment variables. The `wait` command between the two updates is mandatory; omitting it will cause the subsequent command to fail with a `ResourceConflictException`.

```powershell
aws lambda update-function-code --function-name $FUNC --image-uri $IMAGE --region $REGION
aws lambda wait function-updated-v2 --function-name $FUNC --region $REGION

aws lambda update-function-configuration --function-name $FUNC `
  --environment file://lambda-env.json --region $REGION
aws lambda wait function-updated-v2 --function-name $FUNC --region $REGION
```
### Verification

```powershell
aws lambda get-function --function-name $FUNC --region $REGION --query "{Image:Code.ImageUri, Status:Configuration.LastUpdateStatus, Bucket:Configuration.Environment.Variables.MODEL_BUCKET}"
```

The output should show:

- `Image` ending in `:v2`.
- `Status` as `Successful`.
- `Bucket` as `securelearn-models-<12-digit-number>`.

**If `Bucket` is `null`**, the command to update the environment variables did not complete successfully:

1. Check if the PowerShell variables still exist: `echo $FUNC $REGION $IMAGE`. If they are empty, re-run Step 0.
2. Re-run the `wait` command, followed by `update-function-configuration`, then `wait` again (Scenario B), and re-verify.

---

## Step 8: Test the Lambda function via manual invocation

```powershell
python seed_test_logs.py
aws lambda invoke --function-name $FUNC --region $REGION out.json
Get-Content out.json
```

- The `invoke` command must be executed **within 1 minute** of seeding. The simulated logs cover the most recent 4-minute window, while the Lambda function reads the most recent 5-minute window.
- Expected output:
```
{"mode": "schedule", "logs": 150, "users_scored": 3, "anomalies": 1, "new_alerts": 1}
```
- The first execution may take 5–10 seconds as the Lambda function needs to load the model from S3.
- A `new_alerts` value of `0` is also normal if the user has already been alerted within the last 30 minutes (`DEDUP_MINUTES`).

The seeding script creates 3 simulated users:

| User | Behavior | Expected Result |
|---|---|---|
| `user-normal-001` | Normal viewing pattern, approx. 3.9 seconds/chunk | No alert |
| `user-crawler-002` | Rapid, continuous loading, approx. 50 ms/chunk | **Anomaly detected** |
| `user-sharing-003` | 8 IPs, 6 devices | No alert (model v1 has not yet learned to identify this pattern) |

In case of an error, check the logs:

```powershell
aws logs tail /aws/lambda/$FUNC --since 10m --region $REGION
```

| Error | Resolution |
|---|---|
| `AccessDenied` during `GetObject` | Re-check Step 2 and Step 6 |
| `NoSuchKey` | Verify the `models/...` path from Step 1 |
| `Missing environment variable MODEL_BUCKET` | Redo Case B in Step 7 |
| `AccessDeniedException` when querying `AccessLog` | Re-check Step 2 and Step 6 |
| `No module named 'boto3'` (when running seed) | Run `pip install boto3` | ---

## Step 9: Connect DynamoDB Stream to Lambda

```powershell
$STREAM_ARN = aws dynamodb describe-table --table-name AccessLog `
--query "Table.LatestStreamArn" --output text --region $REGION
echo $STREAM_ARN

aws lambda create-event-source-mapping --function-name $FUNC `
--event-source-arn $STREAM_ARN `
--starting-position LATEST `
--batch-size 100 `
--maximum-batching-window-in-seconds 30 `
--maximum-retry-attempts 2 `
--maximum-record-age-in-seconds 3600 `
--filter-criteria file://filter-criteria.json `
--region $REGION
```

- `filter-criteria.json` ensures that the Lambda function only receives **INSERT** events.
- Lambda processes events in batches, with a maximum of 100 records or a 30-second window.
- When receiving a batch from the stream, the Lambda function queries only the logs from the last 5 minutes for the users included in that batch, rather than scanning the entire table.
- The Lambda function will retry a maximum of 2 times and discard records older than 1 hour; this prevents a failed batch from blocking the entire shard. Any users missed in this process will be picked up again during the EventBridge scan in Step 11.

Wait for approximately 1 minute, then verify:

```powershell
aws lambda list-event-source-mappings --function-name $FUNC --region $REGION --query "EventSourceMappings[].State"
```

The result should be `["Enabled"]`.

---

## Step 10: Test the stream flow

Before testing, wait approximately **30 minutes** from the time of the test in Step 8 to allow the deduplication (anti-duplicate) period to expire. Alternatively, you can delete the `user-crawler-002` record from the `AnomalyAlert` table via the AWS Console. 
```powershell
python seed_test_logs.py
Start-Sleep -Seconds 60
aws logs tail /aws/lambda/$FUNC --since 3m --region $REGION
```

Expected results:

- The logs should contain a line: `[stream] ... users, fetched ... log items`, followed by a score table for each user.
- An email with the subject "[SecureLearn] 1 canh bao bat thuong" (Anomaly Alert) should be received, referencing `user-crawler-002`.
- The `AnomalyAlert` table should contain a new record with `status = OPEN` and all 12 features populated.

---

## Step 11: EventBridge fallback execution every 5 minutes

Check if the rule already exists:

```powershell
aws events list-targets-by-rule --rule securelearn-anomaly-every-5min --region $REGION --query "Targets[].Arn"
```

- If the result already contains the ARN for `securelearn-anomaly-detection`, no further action is required.
- If a `ResourceNotFoundException` occurs or the list is empty, create the rule:

```powershell
$RULE_ARN = aws events put-rule --name securelearn-anomaly-every-5min `
--schedule-expression "rate(5 minutes)" --region $REGION --query RuleArn --output text

aws lambda add-permission --function-name $FUNC --statement-id eventbridge-5min `
--action lambda:InvokeFunction --principal events.amazonaws.com `
--source-arn $RULE_ARN --region $REGION

$FUNC_ARN = aws lambda get-function --function-name $FUNC --region $REGION --query Configuration.FunctionArn --output text
aws events put-targets --rule securelearn-anomaly-every-5min --targets "Id=1,Arn=$FUNC_ARN" --region $REGION
```

- The execution interval is set to 5 minutes—matching `LOOKBACK_MINUTES`—to ensure no logs are missed in the event of a stream failure. - If a user is detected in both a stream and a scheduled run, `DEDUP_MINUTES` (30 minutes) will prevent duplicate emails from being sent.

---

## Updates for New Models or Code

**New Model** (e.g., v2): Upload it to S3, update `MODEL_KEY` in `lambda-env.json`, and then update the configuration:

```powershell
aws s3 cp .\isolation_forest_securelearn_v2.pkl "s3://$BUCKET/models/isolation_forest_securelearn_v2.pkl"
aws lambda update-function-configuration --function-name $FUNC --environment file://lambda-env.json --region $REGION
```

If the new model was trained using a version of scikit-learn other than 1.9.1, update `requirements.txt` accordingly and rebuild the image.

**Modifying `handler.py`**: Update the tag in `$IMAGE` (e.g., to `:v3`), then run:

```powershell
docker buildx build --platform linux/amd64 --provenance=false --sbom=false -t $IMAGE --push .
aws lambda update-function-code --function-name $FUNC --image-uri $IMAGE --region $REGION
```

---

## Log Format for Backend AccessLogs

The Lambda function reads the fields listed below. If the backend renames any of these fields, the Lambda function will calculate the features incorrectly.

| Field | Example | Notes |
|---|---|---|
| `userId` | `user-123` | Partition key |
| `accessTime` | `2026-11-10T08:30:00.123Z` | Sort key; ISO UTC format, including milliseconds |
| `logDate` | `2026-11-10` | Partition key for the `byDate` GSI |
| `action` | `MANIFEST` / `CHUNK` / `ERROR` | |
| `videoId` | `video-demo-01` | |
| `chunkIndex` | `42` | Present only when `action = CHUNK` |
| `totalChunks` | `600` | Total number of video chunks |
| `ip` | `14.161.20.5` | |
| `deviceId` | `dev-a` | |

---

## Clean up after the workshop

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

The `AccessLog` table is shared by the backend, so it cannot be deleted here.
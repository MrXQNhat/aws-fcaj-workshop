---
title: "Hệ thống Giám sát AI & Cảnh báo"
date: 2026-03-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Hướng dẫn triển khai Anomaly Lambda trên AWS

Tài liệu này hướng dẫn triển khai khối **AI Detection & Alert** của SecureLearn từ đầu, toàn bộ trên AWS:

- **Amazon S3** lưu model Isolation Forest.
- **Amazon ECR** lưu container image của Lambda.
- **AWS Lambda** (`securelearn-anomaly-detection`) tính đặc trưng, chấm điểm và ghi cảnh báo.
- **DynamoDB Stream** của bảng `AccessLog` kích hoạt Lambda mỗi khi có log mới. Đây là luồng chính, gần thời gian thực.
- **EventBridge** gọi Lambda mỗi 5 phút để **dự phòng** khi stream trễ hoặc lỗi.
- Kết quả bất thường ghi vào bảng **DynamoDB `AnomalyAlert`** và gửi email qua **Amazon SNS**. Log ghi vào **CloudWatch**.

Region dùng xuyên suốt: `ap-southeast-1` (Singapore).

---

## Lưu ý trước khi bắt đầu

- **Phải dùng PowerShell, không dùng Command Prompt (cmd).**
  - Nếu dấu nhắc là `D:\...>` thì bạn đang ở cmd. Gõ `powershell` rồi Enter để chuyển.
  - Khi dấu nhắc thành `PS D:\...>` là đã ở PowerShell.
  - Nếu chạy trong cmd, sẽ gặp lỗi `'$REGION' is not recognized as an internal or external command`.
- **Biến chỉ tồn tại trong cửa sổ đang mở.** Nếu đóng PowerShell rồi mở lại, phải chạy lại khối đặt biến ở Bước 0. Lệnh `clear`/`cls` chỉ xóa màn hình, không xóa biến.
- **Không cần web chạy.** Các bước dưới chỉ làm việc với AWS. Script `seed_test_logs.py` ghi thẳng log giả lập vào DynamoDB.
- **Tài nguyên đã có sẵn thì bỏ qua lệnh tạo.** Nếu một lệnh `create-...` báo lỗi kiểu `already exists`, `ResourceInUseException` hay `EntityAlreadyExists`, nghĩa là tài nguyên đó đã có. Bỏ qua lệnh đó và làm tiếp.

---

## Những gì được upload và đi đâu

| Thành phần | Đi đâu |
|---|---|
| `isolation_forest_securelearn_v1.pkl` | **S3** (Bước 1). Đây là file duy nhất đưa lên S3 |
| `handler.py`, `requirements.txt`, `Dockerfile` | Đóng gói vào **container image trên ECR** (Bước 5) |
| `lambda-env.json`, `lambda-policy.json`, `trust-policy.json`, `filter-criteria.json` | Chỉ dùng trên máy khi chạy lệnh AWS CLI, không upload |
| `seed_test_logs.py`, `README.md` | Chỉ dùng trên máy |

> Model phải là `isolation_forest_securelearn_v1.pkl`: một file, 12 đặc trưng, không dùng scaler. **Không dùng** bộ `isolation_forest.joblib` + `scaler.joblib` + `feature_cols.json` của thiết kế cũ. `handler.py` không đọc scaler nên sẽ lỗi hoặc chấm điểm sai.

---

## Bước 0: Chuẩn bị

Bạn cần:

- **AWS CLI v2**, đã chạy `aws configure` bằng tài khoản có quyền admin.
- **Docker Desktop** đang chạy.
- **Python 3 kèm boto3** (`pip install boto3`), để chạy script bơm log test.
- Thư mục `anomaly-lambda` (giải nén từ `anomaly-lambda.zip`), có chép sẵn file `isolation_forest_securelearn_v1.pkl` vào.

Mở **PowerShell** trong thư mục `anomaly-lambda` và đặt biến:

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

Dòng cuối phải in ra số tài khoản AWS gồm 12 chữ số. Nếu trống hoặc báo lỗi, chạy `aws configure` rồi đặt biến lại.

---

## Bước 1: Tạo bucket S3 và upload model

```powershell
aws s3 mb "s3://$BUCKET" --region $REGION

aws s3api put-public-access-block --bucket $BUCKET `
  --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"

aws s3 cp .\isolation_forest_securelearn_v1.pkl "s3://$BUCKET/models/isolation_forest_securelearn_v1.pkl"

aws s3 ls "s3://$BUCKET/models/"
```

- Lệnh cuối phải liệt kê được file `.pkl`.
- Đường dẫn bắt buộc là `models/...`, vì IAM policy chỉ cho Lambda đọc trong thư mục này.
- Bucket tách riêng khỏi bucket video HLS, chặn mọi truy cập public và được mã hóa SSE-S3 (mặc định). Lambda đọc model bằng IAM role, không cần access key nào.

---

## Bước 2: Điền account ID vào file JSON

Không cần sửa tay. Lệnh dưới tự thay mọi chữ `ACCOUNT_ID` trong `lambda-policy.json` và `lambda-env.json` bằng giá trị của `$ACCOUNT`:

```powershell
echo $ACCOUNT   # phải in ra 12 chữ số trước khi chạy lệnh dưới

foreach ($f in "lambda-policy.json", "lambda-env.json") {
  (Get-Content $f -Raw) -replace "ACCOUNT_ID", $ACCOUNT | Set-Content $f -NoNewline
}
```

Kiểm tra:

```powershell
Select-String -Path lambda-policy.json, lambda-env.json -Pattern "ACCOUNT_ID"
Select-String -Path lambda-env.json -Pattern "MODEL_BUCKET"
```

- Lệnh đầu **không in ra gì** nghĩa là đã thay hết.
- Lệnh thứ hai phải in ra `"MODEL_BUCKET": "securelearn-models-<12 chữ số>"`.

> Nếu `$ACCOUNT` rỗng lúc chạy, `ACCOUNT_ID` sẽ bị thay bằng chuỗi trống và file bị sai. Khi đó hãy giải nén lại hai file này từ zip rồi chạy lại.

---

## Bước 3: Tạo bảng DynamoDB và bật stream

### 3.1. Bảng AccessLog (có stream)

Nếu team SE đã tạo bảng `AccessLog` (kèm GSI `byDate`), bỏ qua lệnh này và sang 3.2.

```powershell
aws dynamodb create-table --table-name AccessLog `
  --attribute-definitions AttributeName=userId,AttributeType=S AttributeName=accessTime,AttributeType=S AttributeName=logDate,AttributeType=S `
  --key-schema AttributeName=userId,KeyType=HASH AttributeName=accessTime,KeyType=RANGE `
  --global-secondary-indexes "IndexName=byDate,KeySchema=[{AttributeName=logDate,KeyType=HASH},{AttributeName=accessTime,KeyType=RANGE}],Projection={ProjectionType=ALL}" `
  --stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE `
  --billing-mode PAY_PER_REQUEST --region $REGION
```

### 3.2. Bật stream nếu bảng AccessLog đã có sẵn

> **Báo team SE trước khi làm bước này:**
> - Nếu họ đang sửa cấu trúc bảng `AccessLog` (thêm hay xóa index), lệnh của bạn sẽ lỗi, vì DynamoDB chỉ cho cập nhật bảng một việc một lúc.
> - Bật stream không làm gián đoạn việc đọc/ghi của backend.
> - Sau khi bật, mọi log ghi vào `AccessLog` (kể cả log test của SE) đều kích hoạt Lambda. Backend phải giữ đúng định dạng log ở cuối tài liệu này.

Kiểm tra stream đã bật chưa:

```powershell
aws dynamodb describe-table --table-name AccessLog --region $REGION --query "Table.StreamSpecification"
```

- Kết quả `null`: bật stream.
  ```powershell
  aws dynamodb update-table --table-name AccessLog `
    --stream-specification StreamEnabled=true,StreamViewType=NEW_IMAGE --region $REGION
  Start-Sleep -Seconds 10
  ```
- Đã có `"StreamEnabled": true`: bỏ qua.

### 3.3. Bảng AnomalyAlert (không có stream)

```powershell
aws dynamodb create-table --table-name AnomalyAlert `
  --attribute-definitions AttributeName=userId,AttributeType=S AttributeName=alertTime,AttributeType=S `
  --key-schema AttributeName=userId,KeyType=HASH AttributeName=alertTime,KeyType=RANGE `
  --billing-mode PAY_PER_REQUEST --region $REGION
```

> **Không bật stream trên bảng `AnomalyAlert`.** Lambda ghi vào bảng này, nên nếu bảng này cũng kích hoạt Lambda thì sẽ thành vòng lặp.

---

## Bước 4: Tạo SNS topic để gửi email cảnh báo

```powershell
$TOPIC = aws sns create-topic --name securelearn-anomaly-alerts --region $REGION --query TopicArn --output text
aws sns subscribe --topic-arn $TOPIC --protocol email --notification-endpoint "email-admin@gmail.com" --region $REGION
```

- Thay `email-admin@gmail.com` bằng email quản trị viên.
- Mở hộp thư và bấm **Confirm subscription**. Nếu không xác nhận, bạn sẽ không nhận được email cảnh báo nào.
- `create-topic` chạy lại nhiều lần vẫn an toàn: nếu topic đã có, lệnh chỉ trả về ARN cũ.

---

## Bước 5: Build image và đẩy lên ECR

```powershell
aws ecr create-repository --repository-name $REPO --region $REGION   # bỏ qua nếu báo đã tồn tại

aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin "$ACCOUNT.dkr.ecr.$REGION.amazonaws.com"

docker buildx build --platform linux/amd64 --provenance=false --sbom=false -t $IMAGE --push .
```

Bắt buộc phải có `--provenance=false --sbom=false`. Nếu thiếu, Lambda sẽ từ chối image với lỗi "image manifest is not supported".

---

## Bước 6: Tạo IAM Role cho Lambda

```powershell
aws iam create-role --role-name $ROLE --assume-role-policy-document file://trust-policy.json   # bỏ qua nếu đã tồn tại

aws iam attach-role-policy --role-name $ROLE `
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

aws iam put-role-policy --role-name $ROLE --policy-name securelearn-anomaly-access `
  --policy-document file://lambda-policy.json

$ROLE_ARN = aws iam get-role --role-name $ROLE --query Role.Arn --output text
Start-Sleep -Seconds 15   # chờ IAM role có hiệu lực
```

Role chỉ được cấp đúng các quyền cần thiết (Least Privilege):

- `s3:GetObject` trong thư mục `models/` của bucket model.
- Query bảng `AccessLog` và GSI `byDate`.
- Đọc stream của `AccessLog`.
- Query và `PutItem` vào bảng `AnomalyAlert`.
- `sns:Publish` vào topic `securelearn-anomaly-alerts`.

`put-role-policy` cùng tên sẽ ghi đè policy cũ, nên chạy lại nhiều lần vẫn an toàn.

---

## Bước 7: Tạo Lambda từ image

Kiểm tra hàm đã tồn tại chưa:

```powershell
aws lambda get-function --function-name $FUNC --region $REGION --query Configuration.State
```

### Trường hợp A: chưa có hàm (báo `ResourceNotFoundException`)

```powershell
aws lambda create-function --function-name $FUNC `
  --package-type Image --code ImageUri=$IMAGE `
  --role $ROLE_ARN --architectures x86_64 `
  --memory-size 1024 --timeout 120 `
  --environment file://lambda-env.json --region $REGION

aws lambda wait function-active-v2 --function-name $FUNC --region $REGION
```

### Trường hợp B: đã có hàm (trả về `"Active"`)

Cập nhật code, rồi đến biến môi trường. Lệnh `wait` giữa hai lần cập nhật là bắt buộc. Nếu thiếu, lệnh sau sẽ lỗi `ResourceConflictException`.

```powershell
aws lambda update-function-code --function-name $FUNC --image-uri $IMAGE --region $REGION
aws lambda wait function-updated-v2 --function-name $FUNC --region $REGION

aws lambda update-function-configuration --function-name $FUNC `
  --environment file://lambda-env.json --region $REGION
aws lambda wait function-updated-v2 --function-name $FUNC --region $REGION
```

### Kiểm tra

```powershell
aws lambda get-function --function-name $FUNC --region $REGION --query "{Image:Code.ImageUri, Status:Configuration.LastUpdateStatus, Bucket:Configuration.Environment.Variables.MODEL_BUCKET}"
```

Kết quả phải có:

- `Image` kết thúc bằng `:v2`.
- `Status` là `Successful`.
- `Bucket` là `securelearn-models-<12 chữ số>`.

**Nếu `Bucket` là `null`**, lệnh cập nhật biến môi trường chưa chạy thành công:

1. Kiểm tra biến PowerShell còn không: `echo $FUNC $REGION $IMAGE`. Nếu trống, chạy lại Bước 0.
2. Chạy lại `wait`, rồi `update-function-configuration`, rồi `wait` (Trường hợp B), sau đó kiểm tra lại.

---

## Bước 8: Test Lambda bằng cách gọi tay

```powershell
python seed_test_logs.py
aws lambda invoke --function-name $FUNC --region $REGION out.json
Get-Content out.json
```

- Lệnh `invoke` phải chạy **trong vòng 1 phút** sau khi seed. Log giả lập nằm trong 4 phút gần nhất, còn Lambda chỉ đọc 5 phút gần nhất.
- Kết quả mong đợi:
  ```
  {"mode": "schedule", "logs": 150, "users_scored": 3, "anomalies": 1, "new_alerts": 1}
  ```
- Lần chạy đầu mất khoảng 5–10 giây vì Lambda phải tải model từ S3.
- `new_alerts` bằng `0` cũng bình thường, nếu user đó đã được cảnh báo trong 30 phút qua (`DEDUP_MINUTES`).

Script seed tạo 3 user giả lập:

| User | Hành vi | Kết quả mong đợi |
|---|---|---|
| `user-normal-001` | Xem bình thường, khoảng 3,9 giây/chunk | Không báo |
| `user-crawler-002` | Tải dồn dập, khoảng 50 ms/chunk | **Báo bất thường** |
| `user-sharing-003` | 8 IP, 6 thiết bị | Không báo (model v1 chưa học được kiểu này) |

Nếu lỗi, xem log:

```powershell
aws logs tail /aws/lambda/$FUNC --since 10m --region $REGION
```

| Lỗi | Cách xử lý |
|---|---|
| `AccessDenied` khi `GetObject` | Kiểm tra lại Bước 2 và Bước 6 |
| `NoSuchKey` | Kiểm tra lại đường dẫn `models/...` ở Bước 1 |
| `Thiếu biến môi trường MODEL_BUCKET` | Làm lại Trường hợp B ở Bước 7 |
| `AccessDeniedException` khi Query `AccessLog` | Kiểm tra lại Bước 2 và Bước 6 |
| `No module named 'boto3'` (khi chạy seed) | Chạy `pip install boto3` |

---

## Bước 9: Nối DynamoDB Stream với Lambda

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

- `filter-criteria.json` chỉ cho Lambda nhận sự kiện **INSERT**.
- Lambda nhận theo lô, tối đa 100 bản ghi hoặc 30 giây.
- Khi nhận lô từ stream, Lambda chỉ query log 5 phút gần nhất của những user có trong lô, không quét toàn bảng.
- Lambda chỉ thử lại tối đa 2 lần và bỏ qua bản ghi cũ hơn 1 giờ, để một lô lỗi không chặn cả shard. User bị bỏ sót vẫn được EventBridge quét lại ở Bước 11.

Chờ khoảng 1 phút, rồi kiểm tra:

```powershell
aws lambda list-event-source-mappings --function-name $FUNC --region $REGION --query "EventSourceMappings[].State"
```

Kết quả phải là `["Enabled"]`.

---

## Bước 10: Test luồng stream

Trước khi test, đợi khoảng **30 phút** kể từ lần test ở Bước 8 để hết thời gian chống gửi trùng. Nếu không muốn đợi, xóa bản ghi `user-crawler-002` trong bảng `AnomalyAlert` trên Console.

```powershell
python seed_test_logs.py
Start-Sleep -Seconds 60
aws logs tail /aws/lambda/$FUNC --since 3m --region $REGION
```

Kết quả mong đợi:

- Trong log có dòng `[stream] ... users, fetched ... log items`, kèm bảng điểm của từng user.
- Nhận được email "[SecureLearn] 1 canh bao bat thuong" nhắc tới `user-crawler-002`.
- Bảng `AnomalyAlert` có bản ghi mới với `status = OPEN` và đủ 12 đặc trưng.

---

## Bước 11: EventBridge chạy dự phòng mỗi 5 phút

Kiểm tra rule đã có chưa:

```powershell
aws events list-targets-by-rule --rule securelearn-anomaly-every-5min --region $REGION --query "Targets[].Arn"
```

- Nếu kết quả đã chứa ARN của `securelearn-anomaly-detection`: không cần làm gì thêm.
- Nếu báo `ResourceNotFoundException`, hoặc danh sách trống: tạo rule.

```powershell
$RULE_ARN = aws events put-rule --name securelearn-anomaly-every-5min `
  --schedule-expression "rate(5 minutes)" --region $REGION --query RuleArn --output text

aws lambda add-permission --function-name $FUNC --statement-id eventbridge-5min `
  --action lambda:InvokeFunction --principal events.amazonaws.com `
  --source-arn $RULE_ARN --region $REGION

$FUNC_ARN = aws lambda get-function --function-name $FUNC --region $REGION --query Configuration.FunctionArn --output text
aws events put-targets --rule securelearn-anomaly-every-5min --targets "Id=1,Arn=$FUNC_ARN" --region $REGION
```

- Lịch chạy giữ ở 5 phút, bằng `LOOKBACK_MINUTES`, để không bỏ sót log khi stream lỗi.
- Nếu stream và lịch chạy cùng thấy một user, `DEDUP_MINUTES` (30 phút) sẽ chặn gửi trùng email.

---

## Cập nhật khi có model hoặc code mới

**Model mới** (ví dụ v2): upload lên S3, sửa `MODEL_KEY` trong `lambda-env.json`, rồi cập nhật cấu hình:

```powershell
aws s3 cp .\isolation_forest_securelearn_v2.pkl "s3://$BUCKET/models/isolation_forest_securelearn_v2.pkl"
aws lambda update-function-configuration --function-name $FUNC --environment file://lambda-env.json --region $REGION
```

Nếu model mới được train bằng phiên bản scikit-learn khác 1.9.1, sửa cả `requirements.txt` rồi build lại image.

**Sửa `handler.py`**: đổi tag trong `$IMAGE` (ví dụ `:v3`), rồi chạy:

```powershell
docker buildx build --platform linux/amd64 --provenance=false --sbom=false -t $IMAGE --push .
aws lambda update-function-code --function-name $FUNC --image-uri $IMAGE --region $REGION
```

---

## Định dạng log backend cần ghi vào AccessLog

Lambda đọc các trường dưới đây. Nếu backend đổi tên trường, Lambda sẽ tính sai đặc trưng.

| Trường | Ví dụ | Ghi chú |
|---|---|---|
| `userId` | `user-123` | Partition key |
| `accessTime` | `2026-11-10T08:30:00.123Z` | Sort key, ISO UTC, có mili-giây |
| `logDate` | `2026-11-10` | Partition key của GSI `byDate` |
| `action` | `MANIFEST` / `CHUNK` / `ERROR` | |
| `videoId` | `video-demo-01` | |
| `chunkIndex` | `42` | Chỉ có khi `action = CHUNK` |
| `totalChunks` | `600` | Tổng số chunk của video |
| `ip` | `14.161.20.5` | |
| `deviceId` | `dev-a` | |

---

## Dọn dẹp sau workshop

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

Bảng `AccessLog` do backend dùng chung nên không xóa ở đây.

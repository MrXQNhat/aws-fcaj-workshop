---
title: "API Gateway"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.3.3. </b> "
---


Backend Spring Boot cung cấp toàn bộ API `/api/v1/*`, chạy trong ECS Fargate phía sau Application Load Balancer. Spring Boot tự định tuyến, tự kiểm tra JWT của Cognito (`oauth2ResourceServer`) và tự phân quyền theo nhóm, không cần API Gateway.

### Bước 1: Tạo VPC & Security Group
1. Truy cập **VPC** (region `ap-southeast-1`) > **Create VPC** > chọn **VPC and more**.
2. **Name tag:** `securelearn` > **IPv4 CIDR:** `10.0.0.0/16` > **Number of AZs:** `2` > **Public subnets:** `2` > **Private subnets:** `2` > **NAT gateways:** `None` > **VPC endpoints:** `S3 Gateway` > Nhấn **Create VPC**.
3. Vào **Security groups** > **Create security group** (VPC `securelearn-vpc`), tạo 3 nhóm:
   * `securelearn-alb-sg`: Inbound HTTPS 443, nguồn là prefix list `com.amazonaws.global.cloudfront.origin-facing` (chỉ CloudFront được gọi ALB).
   * `securelearn-ecs-sg`: Inbound TCP 8080, nguồn là `securelearn-alb-sg`.
   * `securelearn-rds-sg`: Inbound PostgreSQL 5432, nguồn là `securelearn-ecs-sg`.

> ECS Fargate, ALB và RDS bắt buộc phải nằm trong VPC. Chuỗi security group trên đảm bảo Internet → chỉ CloudFront gọi được ALB → chỉ ALB gọi được container → chỉ container gọi được database. Không tạo NAT Gateway để tiết kiệm chi phí; task ECS chạy ở public subnet có public IP.

### Bước 2: Tạo RDS for PostgreSQL
1. Truy cập **RDS** > **Subnet groups** > **Create DB subnet group** > **Name:** `securelearn-db-subnets` > chọn 2 **private subnet** > **Create**.
2. **Databases** > **Create database** > **Standard create** > **Engine:** PostgreSQL 16 > **Templates:** Free tier.
3. **DB instance identifier:** `securelearn-db` > **Master username:** `securelearn` > **Credentials management:** Self managed, tự đặt mật khẩu (sẽ lưu vào Secrets Manager ở mục 5.3.4).
4. **Instance class:** `db.t4g.micro` > **Storage:** gp3 20 GiB.
5. **Connectivity:** VPC `securelearn-vpc` > subnet group `securelearn-db-subnets` > **Public access:** No > **Security group:** `securelearn-rds-sg`.
6. **Additional configuration** > **Initial database name:** `securelearn` > Nhấn **Create database** và ghi lại **Endpoint**.

> Backend chạy với `spring.jpa.hibernate.ddl-auto=update` nên Hibernate tự tạo bảng `Course`, `Chapter`, `Lesson` ở lần khởi động đầu tiên.


### Bước 3: Build & đẩy image lên Amazon ECR
Kiểm tra `Dockerfile` đã cài `ffmpeg` (image `eclipse-temurin:25-jre`), sau đó tạo repository và đẩy image (chạy trong thư mục `backend/`):
```bash
ACCOUNT_ID=<account-id>
REGION=ap-southeast-1
REGISTRY=$ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com

aws ecr create-repository --repository-name securelearn-backend --region $REGION
aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $REGISTRY

docker build --platform linux/amd64 -t $REGISTRY/securelearn-backend:v1 .
docker push $REGISTRY/securelearn-backend:v1
```

### Bước 4: Tạo IAM Role cho ECS
Vào **IAM** > **Roles** > **Create role** > **AWS service** > **Elastic Container Service Task**, tạo 2 role:
1. **`securelearn-ecs-execution-role`**: gắn policy `AmazonECSTaskExecutionRolePolicy`, thêm inline policy cho phép `secretsmanager:GetSecretValue` trên secret `securelearn/backend` và `kms:Decrypt` trên khóa `SecureLearn-Key` (mục 5.3.4).
2. **`securelearn-ecs-task-role`**: inline policy cho code backend (thay cho access key R2/AWS):
```json
{
  "Version": "2012-10-17",
  "Statement": [
    { "Effect": "Allow", "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::securelearn-media-<account-id>/*" },
    { "Effect": "Allow", "Action": ["kms:Decrypt", "kms:GenerateDataKey"], "Resource": "<kms-key-arn>" },
    { "Effect": "Allow", "Action": ["dynamodb:PutItem", "dynamodb:GetItem", "dynamodb:Query"],
      "Resource": ["arn:aws:dynamodb:ap-southeast-1:<account-id>:table/AccessLog",
                   "arn:aws:dynamodb:ap-southeast-1:<account-id>:table/AccessLog/index/*"] },
    { "Effect": "Allow", "Action": ["cognito-idp:AdminCreateUser", "cognito-idp:AdminDeleteUser",
        "cognito-idp:AdminGetUser", "cognito-idp:AdminAddUserToGroup",
        "cognito-idp:AdminListGroupsForUser", "cognito-idp:AdminSetUserPassword",
        "cognito-idp:ListUsers", "cognito-idp:ListUsersInGroup"],
      "Resource": "arn:aws:cognito-idp:ap-southeast-1:<account-id>:userpool/<user-pool-id>" }
  ]
}
```

### Bước 5: Tạo Application Load Balancer
1. Truy cập **Certificate Manager** (region `ap-southeast-1`) > **Request** > domain `*.yourdomain.com` (wildcard, phủ cả `origin` và `learn` vì CloudFront chuyển tiếp header Host của người dùng) > DNS validation > **Create records in Route 53**.
2. **EC2** > **Target groups** > **Create target group** > **Target type:** IP > **Name:** `securelearn-backend-tg` > HTTP port `8080` > VPC `securelearn-vpc` > **Health check path:** `/api/v1/allcourses` (endpoint công khai, backend không có Actuator) > **Create** (không đăng ký target).
3. **Load balancers** > **Create** > **Application Load Balancer** > **Name:** `securelearn-alb` > Internet-facing > 2 **public subnet** > security group `securelearn-alb-sg` > **Listener:** HTTPS 443 forward tới `securelearn-backend-tg`, chọn chứng chỉ vừa tạo > **Create**.
4. Tab **Attributes** > **Idle timeout:** `300` giây (cho request upload/xử lý video kéo dài).
5. Sinh chuỗi bí mật bằng `openssl rand -hex 32`. Mở listener HTTPS:443:
   * **Default action:** Return fixed response `403`.
   * **Add rule:** điều kiện HTTP header `X-Origin-Verify` = chuỗi bí mật > forward tới `securelearn-backend-tg`.
6. **Route 53** > tạo bản ghi `origin.yourdomain.com` (A, Alias) trỏ tới `securelearn-alb`.

### Bước 6: Tạo Task Definition & ECS Service
> Thực hiện bước này sau khi đã tạo secret ở mục 5.3.4.

1. Truy cập **ECS** > **Task definitions** > **Create new task definition**:
   * **Family:** `securelearn-backend` > **Launch type:** AWS Fargate > **CPU** `2 vCPU` / **Memory** `4 GB` > **Ephemeral storage:** `50` GiB (chỗ cho FFmpeg ghi video gốc và segment tạm).
   * **Task execution role:** `securelearn-ecs-execution-role`; **Task role:** `securelearn-ecs-task-role`.
   * **Container:** name `backend`, image `<account-id>.dkr.ecr.ap-southeast-1.amazonaws.com/securelearn-backend:v1`, port `8080`.
   * **Environment variables** (khớp với `application.properties` ở Bước 3):
     * `AWS_REGION` = `ap-southeast-1`
     * `SPRING_DATASOURCE_URL` = `jdbc:postgresql://<rds-endpoint>:5432/securelearn`
     * `SPRING_DATASOURCE_USERNAME` = `securelearn`
     * `S3_BUCKET_NAME` = `securelearn-media-<account-id>`
     * `COGNITO_POOL_ID`, `COGNITO_CLIENT_ID` = giá trị ở mục 5.3.1
     * `CORS_ALLOWED_ORIGINS` = `https://learn.yourdomain.com`
     * `LEGACY_PUBLIC_BASE_URL` = URL `https://pub-xxxx.r2.dev` cũ (chỉ cần trong giai đoạn chuyển dữ liệu, xem mục 5.4.1)
     * `JAVA_TOOL_OPTIONS` = `-XX:MaxRAMPercentage=60`
   * **Secrets** (ValueFrom): `SPRING_DATASOURCE_PASSWORD` = `<secret-arn>:SPRING_DATASOURCE_PASSWORD::`, `COGNITO_CLIENT_SECRET` = `<secret-arn>:COGNITO_CLIENT_SECRET::`, `PLAYBACK_TOKEN_SECRET` = `<secret-arn>:PLAYBACK_TOKEN_SECRET::`.
   * **Log collection:** Amazon CloudWatch > Nhấn **Create**.
2. **Clusters** > **Create cluster** > **Name:** `securelearn-cluster` > **AWS Fargate** > **Create**.
3. Mở cluster > **Services** > **Create**:
   * **Launch type:** FARGATE > **Task definition:** `securelearn-backend` > **Service name:** `securelearn-backend-svc` > **Desired tasks:** `1`.
   * **Networking:** VPC `securelearn-vpc` > 2 **public subnet** > security group `securelearn-ecs-sg` > **Public IP:** On.
   * **Load balancing:** dùng `securelearn-alb` có sẵn > listener `443:HTTPS` > target group `securelearn-backend-tg` > **Health check grace period:** `180` giây.
   * Nhấn **Create**, chờ task **RUNNING** và target group báo **healthy**. Log `Started SecureLearnApplication` trong CloudWatch cho biết backend đã kết nối được RDS và Cognito.

### Các API chính của backend
* **POST** `/api/v1/auth/register`, `/otp`, `/login`, `/refresh`, `/forgot-password`, `/reset-password`: đăng ký, xác nhận OTP, đăng nhập qua Cognito (`USER_PASSWORD_AUTH` + `SECRET_HASH`).
* **GET** `/api/v1/lesson/{id}/playback`: cấp playback token HMAC (hạn 2 giờ).
* **GET** `.../manifest`, `.../key`, `.../segment/{n}`: trả manifest đã viết lại, khóa AES-128 và redirect 302 tới presigned URL S3 hạn 60 giây.
* **POST** `/api/v1/upload/presign`, `/api/v1/upload/process` (INSTRUCTOR/ADMIN): xin URL upload (hạn 15 phút) và yêu cầu FFmpeg xử lý video.
* **/api/v1/admin/***: quản lý người dùng (chỉ ADMIN sau khi sửa phân quyền, xem mục 5.4.3).

> `/upload/process` hiện chạy đồng bộ, CloudFront chỉ chờ tối đa 60 giây nên video dài sẽ báo timeout dù backend vẫn xử lý xong. Khi demo hãy dùng video ngắn, hoặc sửa code trả `202` và xử lý nền.

---
title: "API Gateway"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.3.3. </b> "
---



The Spring Boot backend serves the whole `/api/v1/*` API and runs on ECS Fargate behind an Application Load Balancer. Spring Boot does its own routing, validates Cognito JWTs itself (`oauth2ResourceServer`) and authorizes by group, so API Gateway is not needed.

### Step 1: Create the VPC & Security Groups
1. Go to **VPC** (region `ap-southeast-1`) > **Create VPC** > select **VPC and more**.
2. **Name tag:** `securelearn` > **IPv4 CIDR:** `10.0.0.0/16` > **Number of AZs:** `2` > **Public subnets:** `2` > **Private subnets:** `2` > **NAT gateways:** `None` > **VPC endpoints:** `S3 Gateway` > Click **Create VPC**.
3. Go to **Security groups** > **Create security group** (VPC `securelearn-vpc`) and create 3 groups:
   * `securelearn-alb-sg`: inbound HTTPS 443 from prefix list `com.amazonaws.global.cloudfront.origin-facing` (only CloudFront can reach the ALB).
   * `securelearn-ecs-sg`: inbound TCP 8080 from `securelearn-alb-sg`.
   * `securelearn-rds-sg`: inbound PostgreSQL 5432 from `securelearn-ecs-sg`.

> ECS Fargate, the ALB and RDS must live in a VPC. The security-group chain above ensures Internet → only CloudFront reaches the ALB → only the ALB reaches the container → only the container reaches the database. No NAT Gateway is created to save cost; the ECS task runs in a public subnet with a public IP.

### Step 2: Create RDS for PostgreSQL
1. Go to **RDS** > **Subnet groups** > **Create DB subnet group** > **Name:** `securelearn-db-subnets` > select the 2 **private subnets** > **Create**.
2. **Databases** > **Create database** > **Standard create** > **Engine:** PostgreSQL 16 > **Templates:** Free tier.
3. **DB instance identifier:** `securelearn-db` > **Master username:** `securelearn` > **Credentials management:** Self managed, set a password (stored in Secrets Manager in section 5.3.4).
4. **Instance class:** `db.t4g.micro` > **Storage:** gp3 20 GiB.
5. **Connectivity:** VPC `securelearn-vpc` > subnet group `securelearn-db-subnets` > **Public access:** No > **Security group:** `securelearn-rds-sg`.
6. **Additional configuration** > **Initial database name:** `securelearn` > Click **Create database** and note the **Endpoint**.

> The backend runs with `spring.jpa.hibernate.ddl-auto=update`, so Hibernate creates the `Course`, `Chapter`, `Lesson` tables on first start.

### Step 3: Build & Push the Image to Amazon ECR
Check that the `Dockerfile` installs `ffmpeg` (image `eclipse-temurin:25-jre`), then create the repository and push the image (run in `backend/`):
```bash
ACCOUNT_ID=<account-id>
REGION=ap-southeast-1
REGISTRY=$ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com

aws ecr create-repository --repository-name securelearn-backend --region $REGION
aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $REGISTRY

docker build --platform linux/amd64 -t $REGISTRY/securelearn-backend:v1 .
docker push $REGISTRY/securelearn-backend:v1
```

### Step 4: Create IAM Roles for ECS
Go to **IAM** > **Roles** > **Create role** > **AWS service** > **Elastic Container Service Task** and create 2 roles:
1. **`securelearn-ecs-execution-role`**: attach `AmazonECSTaskExecutionRolePolicy` and an inline policy allowing `secretsmanager:GetSecretValue` on the `securelearn/backend` secret and `kms:Decrypt` on the `SecureLearn-Key` key (section 5.3.4).
2. **`securelearn-ecs-task-role`**: inline policy for the backend code (replacing the R2/AWS access keys):
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

### Step 5: Create the Application Load Balancer
1. Go to **Certificate Manager** (region `ap-southeast-1`) > **Request** > domain `*.yourdomain.com` (wildcard, covering both `origin` and `learn` because CloudFront forwards the viewer's Host header) > DNS validation > **Create records in Route 53**.
2. **EC2** > **Target groups** > **Create target group** > **Target type:** IP > **Name:** `securelearn-backend-tg` > HTTP port `8080` > VPC `securelearn-vpc` > **Health check path:** `/api/v1/allcourses` (public endpoint; the backend has no Actuator) > **Create** (register no targets).
3. **Load balancers** > **Create** > **Application Load Balancer** > **Name:** `securelearn-alb` > Internet-facing > the 2 **public subnets** > security group `securelearn-alb-sg` > **Listener:** HTTPS 443 forwarding to `securelearn-backend-tg` with the new certificate > **Create**.
4. **Attributes** tab > **Idle timeout:** `300` seconds (for long upload/processing requests).
5. Generate a secret with `openssl rand -hex 32`. Open the HTTPS:443 listener:
   * **Default action:** Return fixed response `403`.
   * **Add rule:** condition HTTP header `X-Origin-Verify` = the secret > forward to `securelearn-backend-tg`.
6. **Route 53** > create record `origin.yourdomain.com` (A, Alias) pointing to `securelearn-alb`.

### Step 6: Create the Task Definition & ECS Service
> Do this step after creating the secret in section 5.3.4.

1. Go to **ECS** > **Task definitions** > **Create new task definition**:
   * **Family:** `securelearn-backend` > **Launch type:** AWS Fargate > **CPU** `2 vCPU` / **Memory** `4 GB` > **Ephemeral storage:** `50` GiB (room for FFmpeg's raw video and temp segments).
   * **Task execution role:** `securelearn-ecs-execution-role`; **Task role:** `securelearn-ecs-task-role`.
   * **Container:** name `backend`, image `<account-id>.dkr.ecr.ap-southeast-1.amazonaws.com/securelearn-backend:v1`, port `8080`.
   * **Environment variables** (matching `application.properties` from Step 3):
     * `AWS_REGION` = `ap-southeast-1`
     * `SPRING_DATASOURCE_URL` = `jdbc:postgresql://<rds-endpoint>:5432/securelearn`
     * `SPRING_DATASOURCE_USERNAME` = `securelearn`
     * `S3_BUCKET_NAME` = `securelearn-media-<account-id>`
     * `COGNITO_POOL_ID`, `COGNITO_CLIENT_ID` = values from section 5.3.1
     * `CORS_ALLOWED_ORIGINS` = `https://learn.yourdomain.com`
     * `LEGACY_PUBLIC_BASE_URL` = the old `https://pub-xxxx.r2.dev` URL (only needed while migrating data, see section 5.4.1)
     * `JAVA_TOOL_OPTIONS` = `-XX:MaxRAMPercentage=60`
   * **Secrets** (ValueFrom): `SPRING_DATASOURCE_PASSWORD` = `<secret-arn>:SPRING_DATASOURCE_PASSWORD::`, `COGNITO_CLIENT_SECRET` = `<secret-arn>:COGNITO_CLIENT_SECRET::`, `PLAYBACK_TOKEN_SECRET` = `<secret-arn>:PLAYBACK_TOKEN_SECRET::`.
   * **Log collection:** Amazon CloudWatch > Click **Create**.
2. **Clusters** > **Create cluster** > **Name:** `securelearn-cluster` > **AWS Fargate** > **Create**.
3. Open the cluster > **Services** > **Create**:
   * **Launch type:** FARGATE > **Task definition:** `securelearn-backend` > **Service name:** `securelearn-backend-svc` > **Desired tasks:** `1`.
   * **Networking:** VPC `securelearn-vpc` > the 2 **public subnets** > security group `securelearn-ecs-sg` > **Public IP:** On.
   * **Load balancing:** use the existing `securelearn-alb` > listener `443:HTTPS` > target group `securelearn-backend-tg` > **Health check grace period:** `180` seconds.
   * Click **Create**, wait for the task to be **RUNNING** and the target group to report **healthy**. The `Started SecureLearnApplication` log line in CloudWatch means the backend reached RDS and Cognito.

### Main Backend APIs
* **POST** `/api/v1/auth/register`, `/otp`, `/login`, `/refresh`, `/forgot-password`, `/reset-password`: sign-up, OTP confirmation, sign-in through Cognito (`USER_PASSWORD_AUTH` + `SECRET_HASH`).
* **GET** `/api/v1/lesson/{id}/playback`: issue an HMAC playback token (2-hour TTL).
* **GET** `.../manifest`, `.../key`, `.../segment/{n}`: return the rewritten manifest, the AES-128 key, and a 302 redirect to a 60-second S3 presigned URL.
* **POST** `/api/v1/upload/presign`, `/api/v1/upload/process` (INSTRUCTOR/ADMIN): request an upload URL (15-minute TTL) and ask FFmpeg to process the video.
* **/api/v1/admin/***: user management (ADMIN only once authorization is fixed, see section 5.4.3).

> `/upload/process` currently runs synchronously, and CloudFront waits at most 60 seconds, so long videos report a timeout even though the backend finishes processing. Use short videos for demos, or change the code to return `202` and process in the background.

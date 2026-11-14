---
title: "API Gateway"
date: 2026-11-01
weight: 2
chapter: false
pre: " <b> 5.3.3. </b> "
---

### Tạo HTTP API Gateway & JWT Authorizer
1. Truy cập **Amazon API Gateway** > **Create API** > **HTTP API** > **Build**.
![](image-5.png)
2. **API name:** `SecureLearn-API` > **IP address type:** `IPv4` > Nhấn **Next**.
![](image-6.png)
3. **Integrations:** Nhấn **add integrations** > Lambda > **AWS Region**: us-east-1 > **Lambda function:** chọn Lambda license mới tạo > **version:** 2 > Nhấn **Next**
![](image-7.png)
4. **Cấu hình Route:**
   * **Tạo Route 1:** Method GET, Path /video/{id}/license, Integration chọn Lambda SecureLearn-LicenseService.
![](image-8.png)

   * **Tạo Route 2:** Method POST, Path /upload/webhook, Integration chọn Lambda SecureLearn-UploadHandler.
![](image.png)
   * **Tạo Route 3:** Method GET, Path /admin/data, Integration chọn Lambda SecureLearn-AdminHandler.
![](image-1.png)


5. Giữ stage `$default` với **Auto-deploy** được bật và nhấn **Create**.
![](image-9.png)

6. **Gắn JWT Authorizer:**
   * Vào **Authorization** > **Manage authorizers** > **Create**.
   ![](image-10.png)
   * **Name:** `CognitoAuthorizer`
   * **Type:** `JWT`
   * **Issuer URL:** `https://us-east-1ngbokcb38.auth.us-east-1.amazoncognito.com`
   * **Audience:** `1ts3buhe230pqn3ldfdv90qj7f`
![](image-11.png)

7. **Gắn Authorizer:** Quay lại Routes, chọn GET /video/{id}/token > Attach authorization > chọn CognitoAuthorizer.
![](image-12.png)

8. Cấu hình CORS: Vào CORS > Configure.

* **Allow-Origin:** http://localhost:3000.

* **Allow-Headers:** authorization,content-type.

* **Allow-Methods:** Chọn GET, POST.

![](image-2.png)
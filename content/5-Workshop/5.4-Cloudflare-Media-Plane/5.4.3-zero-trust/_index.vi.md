---
title: "Bảo vệ cổng quản trị với Cloudflare Zero Trust"
date: 2026-03-01
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

### Bước 1: Tạo Ứng dụng Self-Hosted Access
1. Truy cập **Cloudflare Zero Trust** > **Access** > **Applications** > **Add an application**.
2. Chọn loại **Self-hosted**.
3. **Application name:** `SecureLearn Admin Portal`
4. **Application domain:** `admin.yourdomain.com`
5. **Session Duration:** Chọn `24 hours` và nhấn **Next**.

### Bước 2: Thiết lập Chính sách Truy cập (Access Policy)
1. **Policy name:** `Admin Access`
2. **Action:** Chọn **Allow**.
3. **Configure rules:**
   * **Selector:** Chọn **Emails** (hoặc **Emails ending in** theo tên miền tổ chức).
   * **Value:** Nhập danh sách địa chỉ email của giảng viên/quản trị viên được phép truy cập.
4. Nhấn **Next** và **Add application** để kích hoạt lớp xác thực Zero Trust trước cổng quản trị.

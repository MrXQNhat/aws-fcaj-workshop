---
title: "Media Plane & Bảo mật biên Cloudflare"
date: 2026-03-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Trong phần này, bạn sẽ cấu hình lớp biên (Edge) trên Cloudflare để xử lý bất đồng bộ sự kiện upload video, xác thực short-lived playback token trước khi phân phối các phân đoạn HLS từ R2 private và bảo vệ cổng quản trị bằng Zero Trust Access.

### Các phần thực hành
1. [5.4.1 Cloudflare R2 & Hàng đợi sự kiện Queues](5.4.1-r2-queues/)
2. [5.4.2 Cloudflare Worker xác thực Playback tại biên](5.4.2-edge-worker/)
3. [5.4.3 Bảo vệ cổng quản trị với Cloudflare Zero Trust](5.4.3-zero-trust/)

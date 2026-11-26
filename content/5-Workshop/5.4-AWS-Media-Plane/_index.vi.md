---
title: "Triển khai Media Plane & Bảo mật biên trên AWS"
date: 2026-03-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Trong phần này, bạn sẽ triển khai lớp lưu trữ video, phân phối frontend và bảo mật biên trên AWS, thay cho các thành phần Cloudflare của phiên bản cũ:

| Mục | Thay thế cho |
|---|---|
| 5.4.1 Amazon S3 | Cloudflare R2 (`securelearnvideo`), URL public `pub-*.r2.dev` |
| 5.4.2 Amazon CloudFront | Cloudflare Pages / Vercel, Cloudflare CDN, DNS, SSL |
| 5.4.3 AWS WAF | Cloudflare WAF, Cloudflare Zero Trust |
| 5.4.4 AWS Elemental MediaConvert | Chuyển mã HLS thử nghiệm độc lập; đầu ra tách biệt với pipeline FFmpeg hiện tại |

### Các phần thực hành
1. [5.4.1 Tạo Amazon S3 lưu trữ video](5.4.1-s3-storage/)
2. [5.4.2 Triển khai Frontend & phân phối qua Amazon CloudFront](5.4.2-cloudfront/)
3. [5.4.3 Bảo vệ cổng quản trị với AWS WAF](5.4.3-waf/)
4. [5.4.4 Tạo đầu ra HLS với AWS Elemental MediaConvert](5.4.4-mediaconvert/)

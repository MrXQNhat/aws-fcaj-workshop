---
title: "AWS Media Plane & Edge Security"
date: 2026-03-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

In this section you deploy video storage, frontend delivery and edge security on AWS, replacing the Cloudflare components of the previous version:

| Section | Replaces |
|---|---|
| 5.4.1 Amazon S3 | Cloudflare R2 (`securelearnvideo`), public URL `pub-*.r2.dev` |
| 5.4.2 Amazon CloudFront | Cloudflare Pages / Vercel, Cloudflare CDN, DNS, SSL |
| 5.4.3 AWS WAF | Cloudflare WAF, Cloudflare Zero Trust |
| 5.4.4 AWS Elemental MediaConvert | Optional standalone HLS transcoding; output stays separate from the active FFmpeg pipeline |

### Sub-sections
1. [5.4.1 Create Amazon S3 video storage](5.4.1-s3-storage/)
2. [5.4.2 Deploy the Frontend & deliver through Amazon CloudFront](5.4.2-cloudfront/)
3. [5.4.3 Protect the admin portal with AWS WAF](5.4.3-waf/)
4. [5.4.4 Create an HLS output with AWS Elemental MediaConvert](5.4.4-mediaconvert/)

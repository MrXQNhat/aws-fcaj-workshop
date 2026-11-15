---
title: "Week 10 Worklog"
date: 2026-11-11
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---



### Week 10 Objectives:

* Explore serverless architecture security risks, focusing on AWS Lambda vulnerabilities and Server-Side Request Forgery (SSRF).
* Analyze cloud storage security, unauthenticated S3 buckets, and metadata service exploitation.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Cloud Storage (S3) Security Assessment <br>&emsp; + Audit public bucket permissions and ACLs <br>&emsp; + Exploit misconfigured bucket policies and object exposure                                    | 16/11/2026 | 16/11/2026      | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html> |
| 3   | - Instance Metadata Service (IMDS) Exploitation <br>&emsp; + Understand EC2 metadata service risks (IMDSv1 vs IMDSv2) <br>&emsp; + Extract temporary security credentials via SSRF vulnerabilities     | 17/11/2026 | 17/11/2026      | <https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/configuring-instance-metadata-service.html> |
| 4   | - Serverless Vulnerabilities (AWS Lambda) <br>&emsp; + Analyze injection flaws and insecure deserialization in Lambda functions <br>&emsp; + Audit event source mapping security                         | 18/11/2026 | 18/11/2026      | <https://owasp.org/www-project-serverless-top-10/> |
| 5   | - API Gateway & Lambda Security Misconfigurations <br>&emsp; + Exploit missing authorization on API Gateway endpoints <br>&emsp; + Perform indirect object reference (IDOR) attacks in serverless apps   | 19/11/2026 | 19/11/2026      | <https://cloudsecurityalliance.org/>      |
| 6   | - Mitigation & Secure Development <br>&emsp; + Enforce IMDSv2 and disable token hopping <br>&emsp; + Implement robust input validation and authorization checks for Lambda APIs                     | 20/11/2026 | 20/11/2026      | <https://docs.aws.amazon.com/lambda/latest/dg/security-iam.html> |

### Week 10 Achievements:

* Mastered identifying and exploiting exposed cloud storage buckets and insecure object permissions.
* Successfully exploited EC2 Instance Metadata Service (IMDSv1) via SSRF to steal temporary IAM credentials, demonstrating lateral movement vectors.
* Explored OWASP Top 10 vulnerabilities specific to serverless architectures, including insecure function configurations and event injection.
* Analyzed API Gateway authorization flaws and secured serverless endpoints against unauthorized data access.
* Implemented defense-in-depth measures such as enforcing IMDSv2 and rigorous input validation across all serverless components.


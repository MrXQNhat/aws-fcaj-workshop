---
title: "Week 11 Worklog"
date: 2026-11-10
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Week 11 Objectives:

* Understand cloud forensic methodologies, log analysis pipelines (CloudTrail, VPC Flow Logs), and threat hunting techniques.
* Master cloud defensive engineering, implementing Automated Incident Response (IR) and Infrastructure as Code (IaC) security scanning.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Cloud Logging & Visibility <br>&emsp; + Configure AWS CloudTrail, CloudWatch Logs, and VPC Flow Logs <br>&emsp; + Centralize log aggregation for threat hunting                                      | 16/11 | 16/11      | <https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html> |
| 3   | - Cloud Threat Hunting & Forensics <br>&emsp; + Analyze malicious API calls and anomalous user behavior in CloudTrail <br>&emsp; + Perform forensic triage on compromised cloud instances                    | 17/11 | 17/11      | <https://aws.amazon.com/blogs/security/>  |
| 4   | - Infrastructure as Code (IaC) Security <br>&emsp; + Scan Terraform and CloudFormation templates for security flaws using Checkov/TfSec <br>&emsp; + Shift-left security integration in CI/CD pipelines | 18/11 | 18/11      | <https://www.checkov.io/>                 |
| 5   | - Automated Incident Response <br>&emsp; + Build automated remediation workflows using EventBridge and Lambda <br>&emsp; + Automatically revoke compromised IAM credentials and isolate instances         | 19/11 | 19/11      | <https://aws.amazon.com/solutions/security-automation/> |
| 6   | - Final Project Review & Security Hardening <br>&emsp; + Conduct comprehensive security review of the secure serverless lab architecture <br>&emsp; + Finalize cloud security portfolio and documentation      | 20/11 | 20/11      | <https://www.nist.gov/cyberframework>     |



### Week 11 Achievements:

* Established centralized cloud visibility pipelines by configuring and auditing AWS CloudTrail and VPC Flow Logs.
* Developed cloud threat hunting skills to detect anomalous API activity, credential theft, and unauthorized access patterns.
* Integrated "Shift-Left" security tools (Checkov, TfSec) into Infrastructure as Code workflows to catch misconfigurations before deployment.
* Designed and tested automated incident response playbooks using EventBridge and Lambda to instantly isolate compromised cloud resources.
* Concluded the security curriculum by hardening lab architectures and compiling a robust cloud security implementation portfolio.

---
title: "Week 9 Worklog"
date: 2026-11-15
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Week 9 Objectives:

* Understand core cloud security principles, the Shared Responsibility Model, and common cloud threat vectors.
* Identifying and exploiting Identity and Access Management (IAM) misconfigurations in cloud environments.
### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Study Cloud Security Shared Responsibility Model <br> - Analyze common cloud misconfigurations and privilege escalation paths                                                                         | 09/11/2026 | 09/11/2026      | <https://aws.amazon.com/compliance/shared-responsibility-model/> |
| 3   | - AWS IAM Security Auditing <br>&emsp; + Analyze overly permissive IAM policies <br>&emsp; + Identify dangerous wildcard permissions (`*`) <br>&emsp; + Use automated tools for IAM assessment          | 10/11/2026 | 10/11/2026      | <https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html> |
| 4   | - Privilege Escalation Lab Simulation <br>&emsp; + Exploit `iam:CreateAccessKey` and `iam:PutUserPolicy` <br>&emsp; + Simulate policy tampering and assume-role attacks                                 | 11/11/2026 | 11/11/2026      | <https://rhinosecuritylabs.com/aws/aws-privilege-escalation-methods-mitigation/> |
| 5   | - Cloud Reconnaissance & Enumeration <br>&emsp; + Enumerate cloud assets using Pacu and ScoutSuite <br>&emsp; + Map cloud attack surfaces and identify exposed administrative roles                      | 12/11/2026 | 12/11/2026      | <https://github.com/RhinoSecurityLabs/pacu> |
| 6   | - Remediation and Hardening <br>&emsp; + Implement Least Privilege principle in IAM policies <br>&emsp; + Configure AWS Organizations and Service Control Policies (SCPs)                              | 13/11/2026 | 13/11/2026      | <https://aws.amazon.com/architecture/well-architected/> |

### Week 9 Achievements:

* Gained deep insights into cloud threat landscapes and the division of security responsibilities between cloud providers and customers.
* Successfully identified and analyzed dangerous IAM policy misconfigurations that lead to full account compromise.
* Conducted practical privilege escalation attacks in a lab environment, demonstrating how attackers bypass weak permission boundaries.
* Utilized specialized cloud penetration testing tools like Pacu and ScoutSuite to perform automated asset reconnaissance.
* Applied remediation strategies enforcing the Principle of Least Privilege and deploying protective Service Control Policies.
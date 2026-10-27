---
title: "Week 1 Worklog"
date: 2026-09-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

* Connect and get acquainted with members of First Cloud AI Journey.
* Understand basic AWS services, how to use the console & CLI.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- | --------------- | ----------------------------------------- |
| 2 | - Create an AWS Free Tier account<br>- Explore the AWS Console & AWS CLI<br>- Hands-on practice:<br>&nbsp; + Create an AWS account<br>&nbsp; + Install and configure the AWS CLI<br>&nbsp; + Learn how to use the AWS CLI | 14/09 | 14/09 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - Distinguish between Root User and IAM User; create IAM Users, Groups, and Roles<br>- Enable MFA; create Access Keys / AWS CLI credentials | 15/09 | 15/09 | <https://www.youtube.com/watch?v=RVDwsmyFA-Q&list=PL4NoNM0L1m72rjn5s7VzKnaqEK7UTpeGa><br><https://000002.awsstudygroup.com/><br><https://000048.awsstudygroup.com/> |
| 4 | - Write and manage IAM Policies (JSON format, principle of least privilege)<br>- Configure Permission Boundaries, SCPs, and use the IAM Policy Simulator | 16/09 | 16/09 | <https://000002.awsstudygroup.com/vi/><br><https://000048.awsstudygroup.com/vi/> |
| 5 | - Get familiar with AWS CloudShell<br>- Set up the environment: Create a CloudShell or Cloud9 instance<br>- Perform basic operations: Use standard Linux commands (pwd, ls, cat), create and edit files using `vi`, and upload/download files via the IDE/Terminal<br>- Interact via AWS CLI: Check the CLI version, list S3 buckets (`aws s3 ls`), and query EC2 instances (`aws ec2 describe-instances`) | 17/09 | 17/09 | <https://000049.awsstudygroup.com/vi/> |
| 6 | - Explore AWS Support Plans:<br>&nbsp; - Research and compare the four AWS support tiers: Basic, Developer, Business, and Enterprise<br>&nbsp; - Analyze differences in costs, response times, access to AWS Trusted Advisor, and 24/7 support channels<br>&nbsp; - Optimize the selection of an AWS Support Plan based on project scale and corporate budget | 18/09 | 18/09 | <https://000009.awsstudygroup.com/> |


### Week 1 Achievements:

* **Environment Initialization & Configuration:**
  * Successfully set up an AWS Free Tier account. 
  * Familiarized with the AWS Management Console; installed and configured the AWS CLI on a local machine to interact with AWS resources.
* **User Administration & Security (IAM Identity Core):**
  * Understood the distinction between the Root User and IAM Users; adhered to best practices by avoiding the use of the Root User for daily tasks. 
  * Successfully created IAM Users, Groups, and Roles. 
  * Enabled Multi-Factor Authentication (MFA) for the account and generated Access Keys for secure connectivity via the AWS CLI.
* **Policy Management & Access Control (IAM Policies & Governance):**
  * Learned to write IAM Policies (in JSON format) based on the principle of "Least Privilege." 
  * Explored and configured advanced access control mechanisms, including Permission Boundaries and Service Control Policies (SCPs). 
  * Gained proficiency in using the IAM Policy Simulator to test and emulate permission flows.
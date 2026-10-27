---
title: "Week 2 Worklog"
date: 2026-09-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---



### Week 2 Objectives:

* Master core knowledge of AWS networking and hybrid cloud connectivity (VPC, Subnet, Site-to-Site VPN).
* Automate EC2 management processes and optimize costs using AWS Lambda and IAM.
* Deploy applications and containerized workloads on Amazon Lightsail.
* Establish system observability, track metrics, and configure automated alarms using AWS CloudWatch.
* Ensure no infrastructure costs are incurred during workshop practice.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Resource Material |
| --- | --- | --- | --- | --- |
| Mon | - Workshop Practice: Amazon VPC & AWS Site-to-Site VPN<br>- Initialize basic VPC infrastructure: Create VPC, Subnet (Public/Private), Internet Gateway, Route Table, and Security Group<br>- Deploy servers & Connectivity: Create EC2 Server, configure NAT Gateway, test connectivity via Reachability Analyzer and Session Manager<br>- Understand & Configure VPN: Set up Site-to-Site VPN (Virtual Private Gateway, Customer Gateway, IPsec Tunnel/Strongswan)<br>- Test & Clean up: Check network traffic, monitor via CloudWatch, and perform resource clean up | 21/09 | 21/09 | <https://000003.awsstudygroup.com/vi/> |
| Tue | - Workshop Practice: Optimizing EC2 Costs with Lambda<br>- Prepare infrastructure: Create VPC, Security Group, EC2 instance, and configure Slack Incoming Webhook<br>- Authorization: Create Instance Tag, create IAM Role for Lambda<br>- Automation: Write Lambda Function to automatically Stop/Start EC2 instance<br>- Test & Clean up: Check results via Slack and proceed to clean up all resources | 22/09 | 22/09 | <https://000022.awsstudygroup.com/vi/> |
| Wed | - Workshop Practice: Deploying Applications on Amazon Lightsail<br>- Initialize Instance: Create and configure Amazon Lightsail Instance (WordPress/Linux)<br>- Network & Domain Configuration: Configure Static IP, attach DNS record, set up Firewall/Networking rules<br>- Optimization & Management: Configure SSL/TLS certificate (Let's Encrypt), manage integrated database and backups (Snapshots)<br>- Test & Clean up: Check website access via Domain/Static IP and perform resource clean up | 23/09 | 23/09 | <https://000045.awsstudygroup.com/> |
| Thu | - Amazon Lightsail Container Practice: Initialize Lightsail Container Service and deploy applications from Public Docker Image.<br>- Self-packaging & Custom Image Deployment: Install Docker on Ubuntu environment (Lightsail Instance), build custom container image, push to registry, and successfully update new deployment.<br>- Acquired Knowledge: Master the packaging and operation of simple containerized applications on the AWS Lightsail environment without complex infrastructure management. Perform resource clean up upon completion. | 24/09 | 24/09 | <https://000046.awsstudygroup.com/> |
| Fri | - AWS CloudWatch Workshop Practice: Prepare environment and configure system monitoring components on Amazon CloudWatch.<br>- Data Collection & Management: Configure performance metrics tracking and centralize system log data.<br>- Alarms & Visualization: Initialize CloudWatch Alarms to send automated alerts when incidents occur and build CloudWatch Dashboards to visualize system status.<br>- Acquired Knowledge: Understand mechanisms for observing, monitoring, and detecting faults to optimize application operations on the AWS Cloud. Perform resource clean up after the lab. | 25/09 | 25/09 | <https://000008.awsstudygroup.com/> |


### Week 2 Achievements:

* **Cloud Networking & Security Mastery:** Deeply understand and master VPC infrastructure, Public/Private Subnet partitioning, Routing configuration, NAT Gateway, and secure VPN connectivity.
* **Serverless Automation & Cost Optimization:** Automate EC2 resource management using Lambda Function, reducing unnecessary infrastructure costs and sending notifications via Slack.
* **PaaS Deployment & Containerization:** Master the Docker application packaging process, push images to the registry, and operate containerized applications on Amazon Lightsail.
* **System Observability & Monitoring:** Build a comprehensive monitoring system with CloudWatch (Metrics, Logs, Alarms, Dashboards) to proactively detect and handle incidents.
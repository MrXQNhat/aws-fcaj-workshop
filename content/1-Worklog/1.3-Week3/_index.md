---
title: "Week 3 Worklog"
date: 2026-10-04
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---


### Week 3 Objectives:

* Get acquainted with basic AWS services and tools.
* Deploy network infrastructure and Hybrid DNS environment.
* Practice using AWS CLI to manage AWS resources.
* Grasp basic knowledge of VPC, IAM, S3, SNS, and EC2.
### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Resource Material |
| --- | --- | --- | --- | --- |
| Mon | - Overview of Set up Hybrid DNS with Route 53 Resolver lab.<br>- Understand the connection model between DNS on AWS and on-premises DNS.<br>- Learn the role of Amazon Route 53 Resolver in Hybrid DNS architecture, including Outbound Endpoint, Inbound Endpoint, and Resolver Rules.<br>- Explore AWS services used in the lab such as Amazon VPC, EC2, AWS CloudFormation, and AWS Directory Service.<br>- Prepare practice environment and create EC2 Key Pair to connect to EC2 instances after deployment. | 28/09 | 29/09 | <https://000010.awsstudygroup.com/vi/> |
| Tue | - Download CloudFormation template from GitHub and learn how to use templates to deploy AWS infrastructure.<br>- Create CloudFormation Stack named HybridDNS.<br>- Configure network components such as VPC, Public Subnet, and Private Subnet in required Availability Zones.<br>- Configure EC2 Remote Desktop Gateway and select the previously created Key Pair.<br>- Validate and deploy CloudFormation Stack to let AWS automatically create necessary resources.<br>- Configure Security Group for Remote Desktop Gateway, remove unused ports, and limit RDP/ICMP access by necessary IPs. | 29/09 | 29/09 | <https://000010.awsstudygroup.com/vi/> |
| Wed | - Install AWS CLI v2 on Linux/Windows environment.<br>- Check AWS CLI version after installation.<br>- Use `aws configure` to set up AWS CLI with Access Key, Secret Access Key, Region, and Output Format.<br>- Learn how to create and use multiple profiles in AWS CLI via `--profile`.<br>- Check configuration info, region, and profile list.<br>- Use AWS CLI to check existing AWS resources such as EC2 instances and S3 buckets. | 30/09 | 30/09 | <https://000011.awsstudygroup.com/> |
| Thu | - Practice using AWS CLI with Amazon S3.<br>- Create S3 bucket via AWS CLI and list buckets.<br>- Create files and upload objects to S3 bucket.<br>- Check existing objects in the bucket.<br>- Delete objects and buckets after practice completion.<br>- Learn Amazon SNS and use AWS CLI to create an SNS Topic.<br>- Subscribe email to the Topic, confirm subscription, and check status.<br>- Test sending messages via SNS and verify receiving notifications via email. | 1/10 | 1/10 | <https://000011.awsstudygroup.com/> |
| Fri | - Practice IAM management using AWS CLI: create IAM Group, IAM User, and add User to Group.<br>- Check Group/User info and create Access Key for User; learn management and deletion of Access Keys when no longer needed.<br>- Practice building VPC network via AWS CLI including VPC, Subnet, Internet Gateway, and Route Table.<br>- Configure default route 0.0.0.0/0, attach Internet Gateway, and associate Route Table with Subnet.<br>- Create EC2 instance via AWS CLI, create Key Pair and Security Group, open SSH port, check EC2 status, and SSH into the instance.<br>- Learn how to handle common errors when practicing AWS CLI and clean up created resources after lab completion. | 2/10 | 2/10 | <https://000011.awsstudygroup.com/> |

### Week 3 Achievements:

* Understand Hybrid DNS architecture and basic components of Route 53 Resolver.
* Know how to deploy AWS infrastructure using CloudFormation and configure Security Groups.
* Install, configure, and effectively use AWS CLI.
* Practice basic operations with S3, SNS, IAM, VPC, and EC2.
* Build a solid foundation to continue managing and deploying systems on AWS.
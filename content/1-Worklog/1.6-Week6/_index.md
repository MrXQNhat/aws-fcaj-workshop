---
title: "Week 6 Worklog"
date: 2026-10-25
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Week 6 Objectives:

* Design, standardize, and finalize the complete set of system architecture and data flow diagrams for the SecureLearn project.
* Detail the hybrid serverless infrastructure between AWS and Cloudflare, ensuring clarity across media processing, access control, and AI anomaly detection.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Research and design the high-level General Architecture topology <br> - Map out Cloudflare Edge Compute and AWS Cloud integration boundaries                                                          | 19/10/2026 | 19/10/2026      |                                           |
| 3   | - Design the Data Upload & Media Pipeline Flow (`datatransfer.png`) <br>&emsp; + Client upload via Cloudflare Pages <br>&emsp; + API Gateway & Upload Handler Lambda <br>&emsp; + R2 storage & MediaConvert transcoding <br> | 20/10/2026 | 20/10/2026      |                                           |
| 4   | - Design the Playback & Access Control Flow (`playback.png`) <br>&emsp; + Cognito authentication & JWT verification <br>&emsp; + DynamoDB entitlement checks <br>&emsp; + Cloudflare Workers signed token generation | 21/10/2026 | 21/10/2026      |                                           |
| 5   | - Design the AI Anomaly Detection Branch (`AI.png`) <br>&emsp; + EventBridge periodic triggers <br>&emsp; + Isolation Forest Lambda log analysis <br>&emsp; + Amazon SNS automated alerting                | 22/10/2026 | 22/10/2026      |                                           |
| 6   | - **Review & Finalize:** <br>&emsp; + Standardize all architecture diagrams (`general.jpg`, `datatransfer.jpg`, `playback.jpg`, `AI.png`) <br>&emsp; + Integrate diagrams into project documentation | 23/10/2026 | 23/10/2026      |                                           |

### Week 6 Achievements:

* Successfully designed and standardized the comprehensive hybrid serverless architecture diagrams for the SecureLearn project, bridging high-level security concepts with practical execution.
* Mapped out the end-to-end media upload pipeline, detailing video ingestion from Cloudflare Pages through API Gateway, Lambda, private R2 storage, and automated MediaConvert transcoding.
* Structured the secure playback and licensing workflow, incorporating Amazon Cognito authentication, JWT verification, DynamoDB entitlement validation, and Cloudflare Workers signed URL generation.
* Established the automated AI monitoring pipeline layout, detailing how EventBridge triggers an Isolation Forest Lambda function to analyze CloudWatch logs and dispatch alerts via Amazon SNS.

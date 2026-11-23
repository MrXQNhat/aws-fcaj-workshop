---
title: "Week 11 Worklog"
date: 2026-11-06
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Week 11 Objectives:

* Understand container security fundamentals, Docker container escape techniques, and runtime hardening.
* Explore Kubernetes (K8s) cluster architecture security, RBAC misconfigurations, and pod breakout methods.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Docker Security & Container Escapes <br>&emsp; + Analyze Docker daemon socket exposure risks <br>&emsp; + Simulate container escape via privileged containers                                        | 23/11 | 23/11      | <https://www.cisecurity.org/benchmark/docker> |
| 3   | - Kubernetes Cluster Security Architecture <br>&emsp; + Explore K8s control plane components and API server security <br>&emsp; + Audit Role-Based Access Control (RBAC) permissions                   | 24/11 | 24/11      | <https://kubernetes.io/docs/concepts/security/> |
| 4   | - Kubernetes Misconfiguration Exploitation <br>&emsp; + Exploit overly permissive RBAC roles and service accounts <br>&emsp; + Abuse anonymous authentication and insecure K8s dashboards                | 25/11 | 25/11      | <https://book.hacktricks.xyz/pentesting-cloud/kubernetes-security-pentesting> |
| 5   | - Pod Breakout & Lateral Movement <br>&emsp; + Perform pod breakout techniques (host network/PID namespace abuse) <br>&emsp; + Steal secrets from K8s etcd or secret objects                              | 26/11 | 26/11      | <https://falco.org/>                      |
| 6   | - Kubernetes Hardening & Defense <br>&emsp; + Implement Pod Security Standards (PSS) and Network Policies <br>&emsp; + Setup admission controllers and runtime security monitoring (Falco)                 | 27/11 | 27/11      | <https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/2716980/nsa-cisa-release-kubernetes-hardening-guidance/> |

### Week 11 Achievements:

* Acquired comprehensive knowledge of container isolation boundaries and common Docker misconfigurations that allow container breakouts.
* Evaluated Kubernetes cluster security postures by auditing RBAC configurations and identifying over-privileged service accounts.
* Executed simulated Kubernetes attacks, including exploiting exposed API endpoints and abusing insecure pod specifications.
* Practiced advanced pod breakout techniques such as namespace manipulation and sensitive volume mounting abuse.
* Deployed container hardening solutions including Pod Security Standards, Network Policies, and Falco runtime security monitoring.
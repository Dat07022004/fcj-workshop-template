---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Deploying The VibeMatch Application On AWS

Docker backend on EC2 Auto Scaling, Amazon DocumentDB, API Gateway, Amplify, Route 53, ACM, and WAF.

| Information | Value |
| --- | --- |
| Students | Tran Thanh Hai<br>Nguyen Thanh Dat |
| Student IDs | 2280600824<br>2280600620 |
| Project | VibeMatch - Web dating application |
| Source code | [Dat07022004/webdating](https://github.com/Dat07022004/webdating) |
| Domain | [vibematch.cloud](https://vibematch.cloud) |
| AWS Region | ap-southeast-1 (Singapore) |
| Workshop scope | Deploy backend, database, frontend access, DNS/HTTPS, WAF, and system testing |
| Internship Company | Amazon Web Services Vietnam Company Limited |
| Company Supervisor | Nguyen Gia Hung |
| University Supervisor | M.Sc. Dao Le Thao Nguyen |

> Objective: This document follows an FCJ-style workshop structure: overview, prerequisites, deployment labs, testing, and cleanup.

![Overall VibeMatch architecture on AWS](/images/5-Workshop/vibematch-architecture.jpg)

## Contents

1. [Workshop introduction](5.1-introduction/)

2. [Environment preparation](5.2-prerequisites/)

3. [Create VPC, subnets, and network layer](5.3-network-layer/)

4. [Create Amazon DocumentDB](5.4-documentdb/)

5. [Deploy Docker backend on EC2 Auto Scaling](5.5-backend-autoscaling/)

6. [Create API Gateway for frontend-to-backend access](5.6-api-gateway/)

7. [Deploy frontend on AWS Amplify](5.7-amplify-frontend/)

8. [Configure Route 53, ACM, and HTTPS for Socket.IO](5.8-route53-acm-socket/)

9. [Enable WAF for the frontend](5.9-waf/)

10. [Test the system](5.10-system-testing/)

11. [Cleanup and cost optimization](5.11-cleanup-cost/)

12. [Conclusion](5.12-conclusion/)

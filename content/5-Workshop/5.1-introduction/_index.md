---
title: "Workshop introduction"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

This workshop guides the deployment of the VibeMatch application on AWS using a production-like architecture. The backend is packaged with Docker and runs on private EC2 instances managed by an Auto Scaling Group. Traffic is distributed through an Application Load Balancer. The database uses Amazon DocumentDB in private subnets. The frontend runs on AWS Amplify and accesses the backend through API Gateway and a custom domain.

## AWS Services Used

| Service | Role in the system |
| --- | --- |
| Amazon VPC | Separates public subnets, private application subnets, and private database subnets in the same network. |
| Amazon EC2 Auto Scaling | Runs backend containers with desired capacity 2 and can scale when traffic increases. |
| Application Load Balancer | Receives HTTP/HTTPS requests and distributes traffic to backend instances. |
| Amazon ECR | Stores the backend Docker image so EC2 instances can pull it at startup. |
| AWS Secrets Manager | Stores DATABASE_URL and sensitive variables such as Clerk, Cloudinary, and MoMo credentials. |
| Amazon DocumentDB | Stores application data with one writer and one reader replica for failover. |
| API Gateway HTTP API | Provides an HTTPS endpoint for the frontend to call backend REST APIs. |
| AWS Amplify | Builds and hosts the frontend and manages production environment variables. |
| Route 53 and ACM | Manage domains, DNS records, and HTTPS certificates. |
| AWS WAF | Protects the frontend from malicious requests through Amplify Firewall. |
| Amazon CloudWatch | Collects logs, metrics, and alarms from the backend, EC2 Auto Scaling, ALB, API Gateway, DocumentDB, and WAF for operational monitoring. |

![Frontend, API Gateway, ALB, EC2 Auto Scaling, DocumentDB, and WAF architecture](/images/5-Workshop/5.1-introduction/architecture-overview.jpg)

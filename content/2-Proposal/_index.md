---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# VibeMatch Web Application Deployment on AWS
## Project Proposal

### Project Information

| Item | Details |
| --- | --- |
| Student | Tran Thanh Hai - 2280600824 - 22DTHC5 |
| Major | Information Technology - Software Engineering |
| Internship Unit | Amazon Web Services Vietnam Co., Ltd. |
| Project Scope | Deploy backend, database, frontend, API access, realtime socket, DNS, HTTPS, and WAF protection on AWS. |
| Current Status | Backend and frontend have been deployed and are operating together in the AWS production environment. |

### 1. Executive Summary

The VibeMatch project aims to deploy a modern web dating application on AWS with a scalable, secure, and production-oriented architecture. The project covers backend containerization, private database deployment, frontend hosting, domain management, HTTPS access, API routing, realtime socket communication, and basic web protection.

The backend has been packaged with Docker and deployed on EC2 instances managed by an Auto Scaling Group behind an Application Load Balancer. Application data is stored in Amazon DocumentDB inside private subnets. The frontend has been deployed on AWS Amplify using the custom domain `vibematch.cloud`, with REST API traffic routed through API Gateway and realtime socket traffic routed through a dedicated HTTPS socket domain.

The result is a working cloud deployment that demonstrates practical knowledge of AWS networking, compute, database, security, monitoring, DNS, and application delivery.

### 2. Problem Statement

A web application that runs only on a local development machine cannot satisfy real deployment requirements such as public access, secure database connectivity, domain configuration, HTTPS, scalability, monitoring, and protection from common web threats. The project therefore requires a cloud deployment model that allows the application to run reliably while keeping backend servers and databases protected from direct public exposure.

- Backend must be reachable by frontend users but should not expose EC2 instances directly to the Internet.
- Database must remain private and accessible only from backend instances.
- Frontend must be available through a custom HTTPS domain and connect properly to backend APIs.
- The system must support realtime communication without browser mixed-content errors.
- The architecture must balance production readiness with cost awareness for an internship/demo project.

### 3. Solution Architecture

The proposed architecture separates the system into frontend, access, backend, database, and security layers. Users access `vibematch.cloud` through Route 53 DNS and AWS Amplify. Frontend REST requests are sent to API Gateway, which proxies requests to the backend. Realtime Socket.IO traffic uses `socket.vibematch.cloud` with HTTPS on the Application Load Balancer.

The backend runs inside a VPC across private application subnets. EC2 instances are launched by an Auto Scaling Group and registered to an ALB target group. Amazon DocumentDB is deployed in private database subnets with one writer instance and one reader replica used as a failover target. Security groups restrict traffic so DocumentDB accepts connections only from backend EC2 instances.

![VibeMatch AWS Solution Architecture](/images/2-Proposal/vibematch_architecture.jpg)

**Table 1. High-level AWS solution architecture**

| Layer | AWS Services | Purpose |
| --- | --- | --- |
| Frontend | AWS Amplify, Route 53, ACM, WAF | Host SPA frontend with custom domain, HTTPS, DNS, and web protection. |
| API Access | API Gateway HTTP API | Expose REST API endpoint for frontend while keeping backend routing centralized. |
| Realtime | Route 53, ALB HTTPS, Socket.IO | Provide secure socket domain for realtime chat and notification features. |
| Backend | EC2, Docker, Auto Scaling Group, ALB, ECR | Run backend container with scalable compute and load balancing. |
| Database | Amazon DocumentDB | Store application data in private subnets with TLS connection and failover capability. |
| Operations | CloudWatch, SSM, Secrets Manager | Support logs, private instance access, and secret/environment management. |

### 4. Technical Implementation

Backend implementation focused on packaging and deploying the Node.js backend as a Docker image. The image is stored in Amazon ECR, pulled by EC2 instances during launch, and executed as a container. Environment variables and sensitive values such as database URL and Clerk credentials are managed through AWS Secrets Manager.

Network implementation used one VPC with public subnets for load balancer access, private application subnets for backend EC2 instances, and private database subnets for DocumentDB. Security groups were configured so the ALB can reach backend port 3000, and backend EC2 instances can reach DocumentDB port 27017.

Database implementation used Amazon DocumentDB with TLS enabled. The backend connects through the cluster endpoint instead of an instance endpoint to support failover. The connection string includes TLS, replica set, read preference, retryWrites disabled, authSource, and compatible authentication mechanism.

Frontend implementation used AWS Amplify with the custom domain `vibematch.cloud`. API requests are configured through `VITE_API_URL` pointing to API Gateway. Realtime socket requests are configured through `VITE_SOCKET_URL` pointing to `socket.vibematch.cloud`. Amplify Firewall/AWS WAF is enabled to provide recommended web application protection.

### 5. Timeline & Milestones

**Table 2. Project timeline and milestones**

| Milestone | Period | Deliverables |
| --- | --- | --- |
| Foundation research | 05/05/2026 - 24/05/2026 | AWS account, IAM, VPC, EC2, storage, database, monitoring, DNS, and basic cloud deployment knowledge. |
| Architecture preparation | 25/05/2026 - 14/06/2026 | Serverless/API Gateway research, Docker/ECR basics, CI/CD concepts, Security Hub, network expansion, and project architecture diagram. |
| Advanced AWS practice | 15/06/2026 - 05/07/2026 | Permission boundary, SSM, KMS, cost visualization, CloudFormation, CDK, NoSQL design, and early backend deployment tests. |
| Backend deployment | 06/07/2026 - 12/07/2026 | Dockerized backend deployed on EC2 Auto Scaling Group, ALB routing, DocumentDB connection, health checks, and Postman validation. |
| Frontend deployment | 13/07/2026 - 19/07/2026 | Amplify deployment, `vibematch.cloud` custom domain, Route 53 DNS, HTTPS, API Gateway integration, socket domain, and WAF protection. |

### 6. Budget Estimation

The project budget is mainly affected by always-on infrastructure rather than the VPC itself. The highest-cost components are expected to be DocumentDB instances, NAT Gateways, EC2 instances, Application Load Balancer, VPC interface endpoints, and data transfer. Amplify, Route 53, API Gateway, and WAF are usually lower-cost in demo traffic conditions, but they still contribute to the total bill.

**Table 3. Budget estimation and cost optimization areas**

| Cost Area | Expected Cost Behavior | Optimization Plan |
| --- | --- | --- |
| DocumentDB | High fixed hourly cost because writer and reader instances run continuously. | Stop cluster when not testing; for long pauses, snapshot and delete if acceptable. |
| NAT Gateway | High fixed hourly cost plus data processing cost, especially with two NAT Gateways. | Use only when needed for demo; consider one NAT Gateway in non-production or rely on endpoints where suitable. |
| EC2 Auto Scaling | Cost depends on desired capacity and instance type. | Set desired capacity to 0 outside demo windows; scale to 1-2 instances only when testing. |
| ALB and VPC Endpoints | Hourly charges continue while resources exist. | Keep only required endpoints; delete unused endpoints after deployment validation. |
| Amplify, API Gateway, WAF, Route 53 | Generally traffic/request based plus small fixed DNS/domain costs. | Keep active for frontend demo, monitor request volume and WAF metrics. |

Observed billing during the deployment period showed that costs can rise quickly when production-style resources are left running continuously. Therefore, the recommended operating model for the internship environment is start-on-demand for backend/database resources and always-on only for low-cost frontend/DNS resources.

### 7. Risk Assessment

**Table 4. Project risk assessment**

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Unexpected AWS cost increase | May consume credits quickly and interrupt testing. | Use Cost Explorer, budget alerts, stop ASG/DocumentDB when idle, and review NAT/endpoints. |
| Misconfigured security groups | Could block traffic or expose private resources. | Apply least privilege and verify ALB, EC2, and DocumentDB rules separately. |
| DocumentDB connection failure | Backend cannot start or API cannot access data. | Validate TLS CA bundle, connection string, authSource, authMechanism, and EC2-to-DB network path. |
| CORS/authentication issue | Frontend cannot call protected API routes. | Keep API Gateway CORS, backend `ALLOWED_ORIGINS`, `FRONTEND_URL`, and Clerk domain settings aligned. |
| Socket.IO instability behind ALB | Realtime chat/notifications may fail. | Use HTTPS socket domain, ALB sticky sessions if polling is used, or force websocket transport. |
| Single-region dependency | Regional issue could affect the whole app. | Document backup/restore process and consider multi-region only for future production scale. |

### 8. Expected Outcomes

The expected outcome is a complete AWS deployment proposal and working cloud environment for the VibeMatch project. The system demonstrates how a full-stack web application can be deployed using managed AWS services while applying practical security and scalability principles.

- A production-like backend environment using Docker, EC2 Auto Scaling, ALB, and DocumentDB.
- A hosted frontend on AWS Amplify using `vibematch.cloud` with HTTPS and SPA routing.
- A REST API access layer through API Gateway and a separate HTTPS socket domain for realtime features.
- Basic web protection through AWS WAF/Amplify Firewall.
- Operational experience with AWS CLI, Session Manager, health checks, logs, CORS, Clerk authentication, DNS, certificates, and cost control.
- A reusable architecture and documentation base for future improvements such as CI/CD automation, infrastructure as code, observability, and production cost optimization.

### References

- Amazon Web Services, AWS Documentation: <https://docs.aws.amazon.com/>
- Amazon Web Services, Amazon EC2 Auto Scaling User Guide: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/>
- Amazon Web Services, Amazon DocumentDB Developer Guide: <https://docs.aws.amazon.com/documentdb/>
- Amazon Web Services, AWS Amplify Hosting User Guide: <https://docs.aws.amazon.com/amplify/>
- Amazon Web Services, AWS WAF Developer Guide: <https://docs.aws.amazon.com/waf/>
- Docker Inc., Docker Documentation: <https://docs.docker.com/>

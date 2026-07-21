---
title: "Cleanup and cost optimization"
date: 2024-01-01
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

After the demo or testing phase is complete, hourly billed resources should be paused or removed to avoid exceeding credits. Important cost-related resources include DocumentDB instances, NAT Gateway, VPC interface endpoints, EC2 instances, ALB, and WAF.

| Resource | Cost optimization approach |
| --- | --- |
| EC2 Auto Scaling | Set desired/min capacity to 0 when the backend does not need to run. |
| DocumentDB | Stop the cluster when unused; create a snapshot before deletion if data must be retained. |
| NAT Gateway | Delete NAT Gateway when the workshop is complete because NAT is billed hourly and by data processed. |
| VPC endpoints | Delete unused interface endpoints if the backend will be stopped for a long period. |
| ALB/API Gateway/WAF | Keep them only when a public demo is needed; delete them when the internship environment is finished. |

```powershell
aws autoscaling update-auto-scaling-group --region ap-southeast-1 --auto-scaling-group-name webdating-backend-asg --min-size 0 --desired-capacity 0
aws docdb stop-db-cluster --region ap-southeast-1 --db-cluster-identifier webdating-docdb
```

![Billing and Cost Management summary](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-16.png)

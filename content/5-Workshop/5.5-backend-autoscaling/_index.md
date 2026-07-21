---
title: "Deploy Docker backend on EC2 Auto Scaling"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

> Expected result: The backend container runs on 2 private EC2 instances, initializes automatically through Launch Template user data, and receives traffic through the ALB.

## Prerequisites

* The backend has a working Dockerfile.

* The ECR repository has been created and the image has been pushed to ECR.

* Secrets Manager contains the backend environment variables.

* DocumentDB is available.

## Implementation Steps

1. Build the backend image from the backend directory.

1. Log in to ECR and push the `webdating-backend:latest` image.

1. Create an IAM role/profile for EC2 with permission to pull from ECR, read Secrets Manager, write CloudWatch Logs, and use SSM Session Manager.

1. Write `user-data.sh`: install Docker, download `global-bundle.pem`, read secrets, log in to ECR, pull the image, and run the `webdating-backend` container.

1. Create a Launch Template using Amazon Linux 2023, IAM instance profile, backend security group, and user data.

1. Create a target group on port 3000 with health check path `/api/health`.

1. Create an Application Load Balancer in 2 public subnets.

1. Create an Auto Scaling Group in 2 private application subnets with initial desired/min/max capacity `2/2/4`.

### Reference Commands

```powershell
docker build -t webdating-backend:latest ./backend
aws ecr get-login-password --region
$AWS_REGION |
docker login --username AWS --password-stdin 055259485156.dkr.ecr.ap-southeast-1.amazonaws.com
docker tag webdating-backend:latest 055259485156.dkr.ecr.ap-southeast-1.amazonaws.com/webdating-backend:latest
docker push 055259485156.dkr.ecr.ap-southeast-1.amazonaws.com/webdating-backend:latest
curl.exe "http://webdating-backend-alb-218383004.ap-southeast-1.elb.amazonaws.com/api/health"
curl.exe "http://webdating-backend-alb-218383004.ap-southeast-1.elb.amazonaws.com/api/health/db"
```

## Completion Check

* The target group has 2 healthy targets.

* `GET /api/health` returns `{"message":"OK"}`.

* `GET /api/health/db` returns `Database connection is healthy` and `state=connected`.

* The Docker container `webdating-backend` runs stably without a restart loop.

![ECR repository with latest backend image](/images/5-Workshop/5.5-backend-autoscaling/ecr-repository.png)

![Backend EC2 instance in private subnet AZ A](/images/5-Workshop/5.5-backend-autoscaling/backend-ec2-instance-a.png)

![Backend EC2 instance in private subnet AZ B](/images/5-Workshop/5.5-backend-autoscaling/backend-ec2-instance-b.png)

![Launch Template created for backend](/images/5-Workshop/5.5-backend-autoscaling/launch-template.png)

![Auto Scaling Group desired capacity 2](/images/5-Workshop/5.5-backend-autoscaling/auto-scaling-group.png)

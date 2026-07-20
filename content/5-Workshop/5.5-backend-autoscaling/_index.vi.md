---
title: "Deploy backend Docker lên EC2 Auto Scaling"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

> Kết quả cần đạt: Backend container chạy trên 2 EC2 private instances, tự khởi tạo bằng Launch Template user data và được ALB phân phối traffic.

## Điều kiện trước khi làm

* Backend có Dockerfile hoạt động.

* ECR repository đã tạo và image đã push lên ECR.

* Secrets Manager đã có các biến môi trường backend.

* DocumentDB đã available.

## Các bước thực hiện

1. Build backend image từ thư mục backend.

1. Login ECR và push image `webdating-backend:latest`.

1. Tạo IAM role/profile cho EC2 có quyền pull ECR, đọc Secrets Manager, ghi CloudWatch Logs và dùng SSM Session Manager.

1. Viết `user-data.sh`: cài Docker, tải `global-bundle.pem`, đọc secrets, login ECR, pull image và chạy container `webdating-backend`.

1. Tạo Launch Template dùng Amazon Linux 2023, IAM instance profile, security group backend và user data.

1. Tạo target group port 3000 với health check path `/api/health`.

1. Tạo Application Load Balancer trong 2 public subnets.

1. Tạo Auto Scaling Group trong 2 private app subnets, desired/min/max ban đầu `2/2/4`.

### Lệnh tham khảo

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

## Kiểm tra hoàn tất

* Target group có 2 targets healthy.

* `GET /api/health` trả `{"message":"OK"}`.

* `GET /api/health/db` trả `Database connection is healthy` và `state=connected`.

* Docker container `webdating-backend` chạy ổn định, không restart loop.

![ECR repository có image backend latest](/images/5-Workshop/5.5-backend-autoscaling/ecr-repository.png)

![Backend EC2 instance trong private subnet AZ A](/images/5-Workshop/5.5-backend-autoscaling/backend-ec2-instance-a.png)

![Backend EC2 instance trong private subnet AZ B](/images/5-Workshop/5.5-backend-autoscaling/backend-ec2-instance-b.png)

![Launch Template đã tạo cho backend](/images/5-Workshop/5.5-backend-autoscaling/launch-template.png)

![Auto Scaling Group desired capacity 2](/images/5-Workshop/5.5-backend-autoscaling/auto-scaling-group.png)

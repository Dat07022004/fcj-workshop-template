---
title: "Giới thiệu workshop"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

Workshop này hướng dẫn triển khai ứng dụng VibeMatch lên AWS theo mô hình production-like. Backend được đóng gói bằng Docker và chạy trên EC2 private instances trong Auto Scaling Group, traffic đi qua Application Load Balancer. Database sử dụng Amazon DocumentDB trong private subnet. Frontend chạy trên AWS Amplify, truy cập backend thông qua API Gateway và domain tùy chỉnh.

## Dịch vụ AWS sử dụng

| Dịch vụ | Vai trò trong hệ thống |
| --- | --- |
| Amazon VPC | Tách public subnet, private app subnet và private DB subnet trong cùng một network. |
| Amazon EC2 Auto Scaling | Chạy backend container với desired capacity 2 và có thể mở rộng khi traffic tăng. |
| Application Load Balancer | Nhận request HTTP/HTTPS và phân phối tới backend instances. |
| Amazon ECR | Lưu Docker image backend để EC2 pull image khi khởi tạo. |
| AWS Secrets Manager | Lưu DATABASE_URL và các biến nhạy cảm như Clerk/Cloudinary/MoMo. |
| Amazon DocumentDB | Lưu dữ liệu ứng dụng, gồm 1 writer và 1 reader replica làm failover target. |
| API Gateway HTTP API | Cung cấp endpoint HTTPS cho frontend gọi REST API backend. |
| AWS Amplify | Build và host frontend, cấu hình biến môi trường production. |
| Route 53 và ACM | Quản lý domain, DNS record và certificate HTTPS. |
| AWS WAF | Bảo vệ frontend khỏi request độc hại thông qua Amplify Firewall. |

![Sơ đồ kiến trúc frontend, API Gateway, ALB, EC2 Auto Scaling, DocumentDB và WAF](/images/5-Workshop/5.1-introduction/architecture-overview.jpg)

---
title: "Tạo VPC, subnet và network layer"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

> Kết quả cần đạt: Tạo network tách lớp đúng yêu cầu: ALB nằm public subnet, backend EC2 nằm private app subnet, DocumentDB nằm private DB subnet.

## Điều kiện trước khi làm

* Đã chọn region `ap-southeast-1`.

* Có VPC CIDR, ví dụ `10.20.0.0/16`.

## Các bước thực hiện

1. Tạo VPC `webdating-vpc` và bật DNS support, DNS hostnames.

1. Tạo 2 public subnets ở 2 AZ để đặt internet-facing ALB.

1. Tạo 2 private app subnets ở 2 AZ để Auto Scaling Group chạy EC2 backend.

1. Tạo 2 private DB subnets ở 2 AZ để tạo DocumentDB subnet group.

1. Gắn Internet Gateway cho VPC và tạo route `0.0.0.0/0` cho public route table.

1. Tạo NAT Gateway cho private app subnets nếu cần outbound internet; với mô hình HA có thể dùng 2 NAT Gateway, mỗi AZ một NAT.

1. Tạo VPC endpoints cho ECR API, ECR Docker, S3, CloudWatch Logs, SSM, SSM Messages, Secrets Manager và STS để EC2 private hoạt động ổn định.

1. Tạo security groups: `alb-sg`, `ec2-backend-sg`, `docdb-sg`, `endpoint-sg`.

## Kiểm tra hoàn tất

* Public route table có route ra Internet Gateway.

* Private app route table có route ra NAT Gateway hoặc đủ VPC endpoints cần thiết.

* `alb-sg` cho phép 80/443 từ internet.

* `ec2-backend-sg` chỉ nhận port 3000 từ `alb-sg`.

* `docdb-sg` chỉ nhận port 27017 từ `ec2-backend-sg`.

[CHÈN ẢNH: Ảnh VPC đã tạo và bật DNS support/hostnames]

[CHÈN ẢNH: Ảnh danh sách 6 subnets public/app/db]

[CHÈN ẢNH: Ảnh route tables của public và private app subnets]

[CHÈN ẢNH: Ảnh NAT Gateway/VPC endpoints]

[CHÈN ẢNH: Ảnh security group inbound rules]

---
title: "Chuẩn bị môi trường"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

> Kết quả cần đạt: Có đủ công cụ và thông tin đầu vào để thực hiện các lab triển khai AWS.

## Các bước thực hiện

1. Đăng nhập AWS Console bằng tài khoản có quyền quản trị các dịch vụ VPC, EC2, ECR, DocumentDB, API Gateway, Amplify, Route 53, ACM, WAF và Secrets Manager.

1. Cài AWS CLI và cấu hình profile bằng lệnh `aws configure`.

1. Chuẩn bị source code backend/frontend của dự án VibeMatch từ repository [Dat07022004/webdating](https://github.com/Dat07022004/webdating).

1. Chuẩn bị Docker để build backend image.

1. Chuẩn bị Clerk project để lấy publishable key, secret key và bearer token khi test protected API.

1. Chuẩn bị domain [vibematch.cloud](https://vibematch.cloud) hoặc domain tương ứng nếu dùng custom domain.

### Lệnh tham khảo

```powershell
$AWS_REGION = "ap-southeast-1"
$APP_NAME = "webdating"
git clone https://github.com/Dat07022004/webdating.git
aws sts get-caller-identity
docker --version
```

## Kiểm tra hoàn tất

* Chạy được `aws sts get-caller-identity` và nhìn thấy Account ID.

* Build backend image thành công trên máy local.

* Có quyền truy cập AWS Console và có thể tạo service trong region `ap-southeast-1`.


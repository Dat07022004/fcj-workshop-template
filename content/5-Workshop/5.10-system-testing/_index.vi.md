---
title: "Kiểm thử hệ thống"
date: 2024-01-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

> Kết quả cần đạt: Xác nhận frontend, REST API, backend container và DocumentDB hoạt động đúng sau khi deploy.

## Điều kiện trước khi làm

* Có URL frontend, API Gateway URL và socket domain.

* Có tài khoản Clerk test để lấy bearer token.

## Các bước thực hiện

1. Gọi health API qua API Gateway.

1. Gọi DB health API để xác nhận backend kết nối DocumentDB.

1. Dùng Postman gọi protected route thiếu token để xác nhận 401.

1. Lấy Clerk bearer token từ frontend đang đăng nhập và gọi `/api/users/me`.

1. Gọi onboarding/profile flow để xác nhận write/read DocumentDB.

1. Mở browser DevTools kiểm tra request frontend tới API Gateway và socket tới `socket.vibematch.cloud`.

1. Kiểm tra target group health có 2 instances healthy.

1. Kiểm tra Amazon CloudWatch Logs để xác nhận backend ghi log vào log group `/webdating/backend`.

1. Kiểm tra CloudWatch Metrics cho API Gateway, Application Load Balancer, EC2, DocumentDB, Logs và các dịch vụ liên quan.

1. Kiểm tra CloudWatch Alarms cho các cảnh báo như API Gateway 5xx, ALB target 5xx, unhealthy targets, ASG low in-service instances và DocumentDB high CPU.

### Lệnh tham khảo

```powershell
curl.exe "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health"
curl.exe "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health/db"
curl.exe -H "Authorization: Bearer <clerk-token>" "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/users/me"
```

## Kiểm tra hoàn tất

* `/api/health` trả 200.

* `/api/health/db` trả connected.

* Protected API có token hợp lệ hoạt động.

* Frontend đăng nhập, xem profile, discover và các màn hình chính không lỗi CORS.

* Postman xác nhận API backend và DB hoạt động đúng.

* CloudWatch hiển thị log group `/webdating/backend`, metrics của các dịch vụ chính và danh sách alarms phục vụ giám sát vận hành.

![VibeMatch homepage on custom domain](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-09.png)

![VibeMatch notifications page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-10.png)

![VibeMatch matches page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-11.png)

![VibeMatch match detail test](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-12.png)

![VibeMatch messaging page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-13.png)

![VibeMatch video call test](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-14.png)

![VibeMatch profile page](/images/5-Workshop/5.7-5.11-frontend/workshop-frontend-15.png)

![CloudWatch alarms for VibeMatch monitoring](/images/5-Workshop/5.10-system-testing/cloudwatch-alarms.png)

![CloudWatch backend log group](/images/5-Workshop/5.10-system-testing/cloudwatch-backend-log-group.png)

![CloudWatch metrics for AWS services](/images/5-Workshop/5.10-system-testing/cloudwatch-metrics.png)

# Tổng hợp nội dung CloudWatch đã thêm vào Workshop

File này dùng để review riêng phần CloudWatch trước khi chốt nội dung workshop. Ảnh CloudWatch có thể gửi riêng và gắn vào phần 5.10.

## 1. Mục tiêu bổ sung CloudWatch

CloudWatch được bổ sung vào workshop để thể hiện phần giám sát vận hành của hệ thống VibeMatch sau khi triển khai trên AWS.

CloudWatch trong workshop tập trung vào 3 nhóm chính:

* CloudWatch Logs: theo dõi log backend từ EC2 instances.
* CloudWatch Metrics: xem metrics của API Gateway, ALB, EC2, DocumentDB, Logs và các dịch vụ liên quan.
* CloudWatch Alarms: cấu hình cảnh báo cho các tình huống như API Gateway 5xx, ALB target 5xx, unhealthy targets, ASG low in-service instances và DocumentDB high CPU.

## 2. Các phần workshop đã cập nhật

### 5.1. Giới thiệu workshop

Đã thêm Amazon CloudWatch vào bảng dịch vụ AWS sử dụng.

Nội dung tiếng Việt:

```md
| Amazon CloudWatch | Thu thập logs, metrics và alarms từ backend, EC2 Auto Scaling, ALB, API Gateway, DocumentDB và WAF để hỗ trợ giám sát vận hành. |
```

Nội dung tiếng Anh:

```md
| Amazon CloudWatch | Collects logs, metrics, and alarms from the backend, EC2 Auto Scaling, ALB, API Gateway, DocumentDB, and WAF for operational monitoring. |
```

### 5.5. Deploy backend Docker lên EC2 Auto Scaling

Đã thêm bước cấu hình backend gửi log lên CloudWatch Logs.

Nội dung tiếng Việt:

```md
1. Cấu hình backend gửi application logs lên Amazon CloudWatch Logs thông qua log group `/webdating/backend`.
```

Phần kiểm tra hoàn tất:

```md
* CloudWatch Logs ghi nhận log backend từ các EC2 instances trong log group `/webdating/backend`.
```

Nội dung tiếng Anh:

```md
1. Configure the backend to send application logs to Amazon CloudWatch Logs through the `/webdating/backend` log group.
```

Completion check:

```md
* CloudWatch Logs receives backend logs from the EC2 instances in the `/webdating/backend` log group.
```

### 5.10. Kiểm thử hệ thống

Đây là phần chính để đưa CloudWatch vào workshop vì CloudWatch được dùng để xác nhận trạng thái vận hành sau khi deploy.

Đã thêm vào các bước thực hiện:

```md
1. Kiểm tra Amazon CloudWatch Logs để xác nhận backend ghi log vào log group `/webdating/backend`.

1. Kiểm tra CloudWatch Metrics cho API Gateway, Application Load Balancer, EC2, DocumentDB, Logs và các dịch vụ liên quan.

1. Kiểm tra CloudWatch Alarms cho các cảnh báo như API Gateway 5xx, ALB target 5xx, unhealthy targets, ASG low in-service instances và DocumentDB high CPU.
```

Đã thêm vào kiểm tra hoàn tất:

```md
* CloudWatch hiển thị log group `/webdating/backend`, metrics của các dịch vụ chính và danh sách alarms phục vụ giám sát vận hành.
```

Nội dung tiếng Anh:

```md
1. Check Amazon CloudWatch Logs to confirm that the backend writes logs to the `/webdating/backend` log group.

1. Check CloudWatch Metrics for API Gateway, Application Load Balancer, EC2, DocumentDB, Logs, and related services.

1. Check CloudWatch Alarms for alerts such as API Gateway 5xx, ALB target 5xx, unhealthy targets, ASG low in-service instances, and DocumentDB high CPU.
```

Completion check:

```md
* CloudWatch shows the `/webdating/backend` log group, metrics for the main services, and alarms used for operational monitoring.
```

### 5.12. Kết luận

Đã bổ sung CloudWatch vào đoạn kết luận để thể hiện hệ thống không chỉ deploy được mà còn có giám sát vận hành.

Nội dung tiếng Việt:

```md
Amazon CloudWatch được sử dụng để theo dõi logs, metrics và alarms của backend, ALB, EC2 Auto Scaling, API Gateway, DocumentDB và WAF. Quy trình kiểm thử bằng Postman, browser và CloudWatch xác nhận API, xác thực Clerk, kết nối database và trạng thái vận hành hệ thống hoạt động đúng.
```

Nội dung tiếng Anh:

```md
Amazon CloudWatch was used to monitor logs, metrics, and alarms for the backend, ALB, EC2 Auto Scaling, API Gateway, DocumentDB, and WAF. Testing with Postman, the browser, and CloudWatch confirmed that the API, Clerk authentication, database connection, and operational status work correctly.
```

## 3. Ảnh CloudWatch cần gắn vào 5.10

Bạn có thể gửi ảnh riêng cho các mục sau:

```md
![CloudWatch alarms for VibeMatch monitoring](/images/5-Workshop/5.10-system-testing/cloudwatch-alarms.png)

![CloudWatch backend log group](/images/5-Workshop/5.10-system-testing/cloudwatch-backend-log-group.png)

![CloudWatch metrics for AWS services](/images/5-Workshop/5.10-system-testing/cloudwatch-metrics.png)
```

Ý nghĩa từng ảnh:

* `cloudwatch-alarms.png`: danh sách CloudWatch Alarms của hệ thống VibeMatch.
* `cloudwatch-backend-log-group.png`: log group `/webdating/backend` và các log streams từ backend EC2 instances.
* `cloudwatch-metrics.png`: trang CloudWatch Metrics hiển thị các namespace như ApiGateway, ApplicationELB, EC2, DocDB, Logs.

## 4. Gợi ý đoạn mô tả ngắn để đưa vào báo cáo

```md
Trong quá trình triển khai, Amazon CloudWatch được sử dụng để hỗ trợ quan sát trạng thái vận hành của hệ thống VibeMatch. Backend gửi application logs vào log group `/webdating/backend`, giúp kiểm tra log từ các EC2 instances khi container khởi động và xử lý request. Ngoài ra, CloudWatch Metrics được dùng để theo dõi các dịch vụ chính như API Gateway, Application Load Balancer, EC2, DocumentDB và WAF. Hệ thống cũng có các CloudWatch Alarms để cảnh báo các tình huống bất thường như lỗi 5xx, unhealthy targets, số lượng instance phục vụ thấp hoặc CPU DocumentDB cao.
```

English version:

```md
During deployment, Amazon CloudWatch was used to support operational monitoring for the VibeMatch system. The backend sends application logs to the `/webdating/backend` log group, allowing logs from EC2 instances to be checked when containers start and process requests. CloudWatch Metrics is also used to monitor key services such as API Gateway, Application Load Balancer, EC2, DocumentDB, and WAF. The system includes CloudWatch Alarms for abnormal situations such as 5xx errors, unhealthy targets, low in-service instance count, or high DocumentDB CPU usage.
```

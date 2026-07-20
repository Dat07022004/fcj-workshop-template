---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Triển khai ứng dụng web VibeMatch trên AWS
## Bản đề xuất dự án

### Thông tin dự án

| Mục | Chi tiết |
| --- | --- |
| Sinh viên | Trần Thanh Hải - 2280600824 - 22DTHC5<br>Nguyễn Thành Đạt - 2280600620 - 22DTHC5 |
| Chuyên ngành | Công nghệ thông tin - Kỹ thuật phần mềm |
| Đơn vị thực tập | Công ty TNHH Amazon Web Services Việt Nam |
| Phạm vi dự án | Triển khai backend, cơ sở dữ liệu, frontend, truy cập API, realtime socket, DNS, HTTPS và bảo vệ WAF trên AWS. |
| Trạng thái hiện tại | Backend và frontend đã được triển khai và đang hoạt động cùng nhau trong môi trường AWS production. |

### 1. Tóm tắt điều hành

Dự án VibeMatch hướng đến việc triển khai một ứng dụng hẹn hò web hiện đại trên AWS với kiến trúc có khả năng mở rộng, bảo mật và định hướng production. Dự án bao gồm container hóa backend, triển khai cơ sở dữ liệu riêng tư, lưu trữ frontend, quản lý domain, truy cập HTTPS, định tuyến API, giao tiếp realtime socket và bảo vệ web cơ bản.

Backend đã được đóng gói bằng Docker và triển khai trên các EC2 instance được quản lý bởi Auto Scaling Group phía sau Application Load Balancer. Dữ liệu ứng dụng được lưu trong Amazon DocumentDB bên trong private subnets. Frontend đã được triển khai trên AWS Amplify bằng custom domain `vibematch.cloud`, REST API traffic được định tuyến qua API Gateway và realtime socket traffic được định tuyến qua một HTTPS socket domain riêng.

Kết quả là một môi trường cloud deployment hoạt động được, thể hiện kiến thức thực tế về AWS networking, compute, database, security, monitoring, DNS và application delivery.

### 2. Tuyên bố vấn đề

Một ứng dụng web chỉ chạy trên máy phát triển local không thể đáp ứng các yêu cầu triển khai thực tế như truy cập công khai, kết nối cơ sở dữ liệu an toàn, cấu hình domain, HTTPS, khả năng mở rộng, monitoring và bảo vệ khỏi các mối đe dọa web phổ biến. Vì vậy, dự án cần một mô hình triển khai cloud cho phép ứng dụng vận hành ổn định trong khi vẫn bảo vệ backend server và database khỏi việc bị truy cập trực tiếp từ Internet.

- Backend cần có thể được người dùng frontend truy cập nhưng không nên để lộ EC2 instance trực tiếp ra Internet.
- Database phải nằm riêng tư và chỉ backend instance mới được phép truy cập.
- Frontend phải khả dụng thông qua custom HTTPS domain và kết nối đúng với backend APIs.
- Hệ thống phải hỗ trợ giao tiếp realtime mà không gặp lỗi mixed-content trên trình duyệt.
- Kiến trúc cần cân bằng giữa mức độ sẵn sàng cho production và kiểm soát chi phí cho dự án thực tập/demo.

### 3. Kiến trúc giải pháp

Kiến trúc đề xuất tách hệ thống thành các lớp frontend, access, backend, database và security. Người dùng truy cập `vibematch.cloud` thông qua Route 53 DNS và AWS Amplify. Các request REST từ frontend được gửi đến API Gateway, sau đó proxy đến backend. Socket.IO traffic realtime sử dụng `socket.vibematch.cloud` với HTTPS trên Application Load Balancer.

Backend chạy trong một VPC với các private application subnets. EC2 instances được khởi tạo bởi Auto Scaling Group và đăng ký vào ALB target group. Amazon DocumentDB được triển khai trong private database subnets với một writer instance và một reader replica dùng làm failover target. Security groups giới hạn traffic để DocumentDB chỉ chấp nhận kết nối từ backend EC2 instances.

![Kiến trúc giải pháp AWS của VibeMatch](/images/2-Proposal/vibematch_architecture.jpg)

**Bảng 1. Kiến trúc giải pháp AWS mức tổng quan**

| Lớp | Dịch vụ AWS | Mục đích |
| --- | --- | --- |
| Frontend | AWS Amplify, Route 53, ACM, WAF | Lưu trữ SPA frontend với custom domain, HTTPS, DNS và bảo vệ web. |
| API Access | API Gateway HTTP API | Cung cấp REST API endpoint cho frontend đồng thời tập trung hóa backend routing. |
| Realtime | Route 53, ALB HTTPS, Socket.IO | Cung cấp socket domain bảo mật cho tính năng chat và notification realtime. |
| Backend | EC2, Docker, Auto Scaling Group, ALB, ECR | Chạy backend container với compute có khả năng mở rộng và load balancing. |
| Database | Amazon DocumentDB | Lưu dữ liệu ứng dụng trong private subnets với kết nối TLS và khả năng failover. |
| Operations | CloudWatch, SSM, Secrets Manager | Hỗ trợ logs, truy cập private instance và quản lý secret/environment. |

### 4. Triển khai kỹ thuật

Phần triển khai backend tập trung vào đóng gói và triển khai Node.js backend dưới dạng Docker image. Image được lưu trong Amazon ECR, được EC2 instances pull khi khởi chạy và chạy dưới dạng container. Các biến môi trường và giá trị nhạy cảm như database URL và Clerk credentials được quản lý qua AWS Secrets Manager.

Phần triển khai network sử dụng một VPC với public subnets cho load balancer access, private application subnets cho backend EC2 instances và private database subnets cho DocumentDB. Security groups được cấu hình để ALB có thể truy cập backend port 3000, và backend EC2 instances có thể truy cập DocumentDB port 27017.

Phần triển khai database sử dụng Amazon DocumentDB với TLS được bật. Backend kết nối thông qua cluster endpoint thay vì instance endpoint để hỗ trợ failover. Connection string bao gồm TLS, replica set, read preference, tắt retryWrites, authSource và authentication mechanism tương thích.

Phần triển khai frontend sử dụng AWS Amplify với custom domain `vibematch.cloud`. API requests được cấu hình qua `VITE_API_URL` trỏ đến API Gateway. Realtime socket requests được cấu hình qua `VITE_SOCKET_URL` trỏ đến `socket.vibematch.cloud`. Amplify Firewall/AWS WAF được bật để cung cấp lớp bảo vệ ứng dụng web được khuyến nghị.

### 5. Lộ trình & mốc triển khai

**Bảng 2. Lộ trình và mốc triển khai dự án**

| Mốc | Thời gian | Sản phẩm bàn giao |
| --- | --- | --- |
| Nghiên cứu nền tảng | 05/05/2026 - 24/05/2026 | Kiến thức về AWS account, IAM, VPC, EC2, storage, database, monitoring, DNS và cloud deployment cơ bản. |
| Chuẩn bị kiến trúc | 25/05/2026 - 14/06/2026 | Nghiên cứu Serverless/API Gateway, Docker/ECR cơ bản, khái niệm CI/CD, Security Hub, mở rộng network và sơ đồ kiến trúc dự án. |
| Thực hành AWS nâng cao | 15/06/2026 - 05/07/2026 | Permission boundary, SSM, KMS, trực quan hóa chi phí, CloudFormation, CDK, thiết kế NoSQL và các thử nghiệm backend deployment ban đầu. |
| Triển khai backend | 06/07/2026 - 12/07/2026 | Dockerized backend được triển khai trên EC2 Auto Scaling Group, ALB routing, kết nối DocumentDB, health checks và kiểm thử bằng Postman. |
| Triển khai frontend | 13/07/2026 - 19/07/2026 | Amplify deployment, custom domain `vibematch.cloud`, Route 53 DNS, HTTPS, tích hợp API Gateway, socket domain và bảo vệ WAF. |

### 6. Ước tính ngân sách

Ngân sách dự án chủ yếu bị ảnh hưởng bởi hạ tầng chạy liên tục thay vì bản thân VPC. Các thành phần có chi phí cao nhất dự kiến là DocumentDB instances, NAT Gateways, EC2 instances, Application Load Balancer, VPC interface endpoints và data transfer. Amplify, Route 53, API Gateway và WAF thường có chi phí thấp hơn trong điều kiện demo traffic, nhưng vẫn đóng góp vào tổng chi phí.

**Bảng 3. Ước tính ngân sách và khu vực tối ưu chi phí**

| Hạng mục chi phí | Hành vi chi phí dự kiến | Kế hoạch tối ưu |
| --- | --- | --- |
| DocumentDB | Chi phí cố định theo giờ cao vì writer và reader instances chạy liên tục. | Dừng cluster khi không kiểm thử; với thời gian tạm dừng dài, snapshot và xóa nếu phù hợp. |
| NAT Gateway | Chi phí cố định theo giờ cao cộng với chi phí xử lý dữ liệu, đặc biệt khi dùng hai NAT Gateways. | Chỉ dùng khi cần demo; cân nhắc một NAT Gateway trong môi trường non-production hoặc dựa vào endpoints khi phù hợp. |
| EC2 Auto Scaling | Chi phí phụ thuộc vào desired capacity và instance type. | Đặt desired capacity về 0 ngoài khung demo; scale lên 1-2 instances chỉ khi kiểm thử. |
| ALB và VPC Endpoints | Phí theo giờ tiếp tục phát sinh khi resources còn tồn tại. | Chỉ giữ endpoints cần thiết; xóa endpoints không dùng sau khi xác thực deployment. |
| Amplify, API Gateway, WAF, Route 53 | Thường tính theo traffic/request cộng với một phần chi phí cố định nhỏ cho DNS/domain. | Giữ hoạt động cho frontend demo, theo dõi request volume và WAF metrics. |

Chi phí quan sát được trong giai đoạn deployment cho thấy chi phí có thể tăng nhanh khi các resource theo phong cách production được để chạy liên tục. Vì vậy, mô hình vận hành được khuyến nghị cho môi trường thực tập là bật backend/database khi cần và chỉ duy trì liên tục các resource frontend/DNS có chi phí thấp.

### 7. Đánh giá rủi ro

**Bảng 4. Đánh giá rủi ro dự án**

| Rủi ro | Tác động | Biện pháp giảm thiểu |
| --- | --- | --- |
| Chi phí AWS tăng ngoài dự kiến | Có thể tiêu tốn credit nhanh và làm gián đoạn kiểm thử. | Sử dụng Cost Explorer, budget alerts, dừng ASG/DocumentDB khi idle và rà soát NAT/endpoints. |
| Security groups cấu hình sai | Có thể chặn traffic hoặc làm lộ private resources. | Áp dụng least privilege và kiểm tra riêng từng rule của ALB, EC2 và DocumentDB. |
| Lỗi kết nối DocumentDB | Backend không thể khởi động hoặc API không thể truy cập dữ liệu. | Xác thực TLS CA bundle, connection string, authSource, authMechanism và đường truyền EC2-to-DB. |
| Lỗi CORS/authentication | Frontend không thể gọi các protected API routes. | Đồng bộ API Gateway CORS, backend `ALLOWED_ORIGINS`, `FRONTEND_URL` và Clerk domain settings. |
| Socket.IO không ổn định phía sau ALB | Tính năng chat/notification realtime có thể lỗi. | Sử dụng HTTPS socket domain, ALB sticky sessions nếu dùng polling, hoặc ép websocket transport. |
| Phụ thuộc single-region | Sự cố khu vực có thể ảnh hưởng toàn bộ ứng dụng. | Tài liệu hóa quy trình backup/restore và chỉ cân nhắc multi-region cho quy mô production trong tương lai. |

### 8. Kết quả kỳ vọng

Kết quả kỳ vọng là một bản đề xuất triển khai AWS hoàn chỉnh và một môi trường cloud hoạt động cho dự án VibeMatch. Hệ thống thể hiện cách triển khai một full-stack web application bằng các dịch vụ AWS được quản lý, đồng thời áp dụng các nguyên tắc bảo mật và khả năng mở rộng trong thực tế.

- Môi trường backend gần với production sử dụng Docker, EC2 Auto Scaling, ALB và DocumentDB.
- Frontend được lưu trữ trên AWS Amplify với `vibematch.cloud`, HTTPS và SPA routing.
- Lớp truy cập REST API thông qua API Gateway và một HTTPS socket domain riêng cho tính năng realtime.
- Bảo vệ web cơ bản thông qua AWS WAF/Amplify Firewall.
- Kinh nghiệm vận hành với AWS CLI, Session Manager, health checks, logs, CORS, Clerk authentication, DNS, certificates và kiểm soát chi phí.
- Nền tảng kiến trúc và tài liệu có thể tái sử dụng cho các cải tiến tương lai như CI/CD automation, infrastructure as code, observability và production cost optimization.

### Tài liệu tham khảo

- Amazon Web Services, AWS Documentation: <https://docs.aws.amazon.com/>
- Amazon Web Services, Amazon EC2 Auto Scaling User Guide: <https://docs.aws.amazon.com/autoscaling/ec2/userguide/>
- Amazon Web Services, Amazon DocumentDB Developer Guide: <https://docs.aws.amazon.com/documentdb/>
- Amazon Web Services, AWS Amplify Hosting User Guide: <https://docs.aws.amazon.com/amplify/>
- Amazon Web Services, AWS WAF Developer Guide: <https://docs.aws.amazon.com/waf/>
- Docker Inc., Docker Documentation: <https://docs.docker.com/>

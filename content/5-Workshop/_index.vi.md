---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Triển Khai Ứng Dụng Vibematch Trên Aws

Backend Docker trên EC2 Auto Scaling, Amazon DocumentDB, API Gateway, Amplify, Route 53, ACM và WAF

| Thông tin | Giá trị |
| --- | --- |
| Sinh viên | Trần Thanh Hải<br>Nguyễn Thành Đạt |
| MSSV | 2280600824<br>2280600620 |
| Dự án | VibeMatch - Web dating application |
| AWS Region | ap-southeast-1 (Singapore) |
| Phạm vi workshop | Triển khai backend, database, frontend access, DNS/HTTPS, WAF và kiểm thử |
| Công ty thực tập | Công ty TNHH Amazon Web Services Việt Nam |
| Cán bộ hướng dẫn | Nguyễn Gia Hưng |
| Giảng viên hướng dẫn | ThS. Đào Lê Thảo Nguyên |

> Mục tiêu: Tài liệu này mô phỏng cấu trúc workshop kiểu FCJ: overview, prerequisite, các lab triển khai, kiểm thử và cleanup. Những vị trí cần ảnh màn hình đã được đánh dấu màu đỏ để bổ sung sau.

![Sơ đồ kiến trúc tổng quan VibeMatch trên AWS](/images/5-Workshop/vibematch-architecture.jpg)

## Nội dung

1. [Giới thiệu workshop](5.1-introduction/)

2. [Chuẩn bị môi trường](5.2-prerequisites/)

3. [Tạo VPC, subnet và network layer](5.3-network-layer/)

4. [Tạo Amazon DocumentDB](5.4-documentdb/)

5. [Deploy backend Docker lên EC2 Auto Scaling](5.5-backend-autoscaling/)

6. [Tạo API Gateway cho frontend gọi backend](5.6-api-gateway/)

7. [Deploy frontend trên AWS Amplify](5.7-amplify-frontend/)

8. [Cấu hình Route 53, ACM và HTTPS cho Socket.IO](5.8-route53-acm-socket/)

9. [Bật WAF cho frontend](5.9-waf/)

10. [Kiểm thử hệ thống](5.10-system-testing/)

11. [Cleanup và tối ưu chi phí](5.11-cleanup-cost/)

12. [Kết luận](5.12-conclusion/)

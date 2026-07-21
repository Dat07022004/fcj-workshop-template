---
title: "Worklog Tuần 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Công việc đã làm

* Tạo Route Table và cấu hình đường ra internet thông qua Internet Gateway.
* Gắn thủ công 2 public subnets vào route table bằng subnet association.
* Tìm hiểu cách cô lập private subnets để bảo vệ các tài nguyên không cần truy cập trực tiếp từ internet.
* Nghiên cứu cách chia VPC thành các subnet dải `/24` theo kiến trúc Multi-AZ tại `ap-southeast-1a` và `ap-southeast-1b`.

## Kết quả đạt được

* Hiểu vai trò của Route Table, Internet Gateway và subnet association.
* Biết cách thiết kế public/private subnet trong cùng một VPC.
* Nắm được nguyên tắc chia subnet theo nhiều Availability Zones để tăng tính sẵn sàng.
* Có nền tảng network để triển khai các thành phần backend, load balancer và database về sau.

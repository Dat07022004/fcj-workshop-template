---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

## Công việc đã làm

* Hoàn thiện frontend dự án VibeMatch.
* Triển khai frontend lên AWS Amplify với domain riêng `vibematch.cloud`.
* Cấu hình HTTPS và DNS bằng Route 53.
* Bật AWS WAF để bảo vệ lớp truy cập frontend.
* Kết nối REST API qua API Gateway.
* Cấu hình realtime connection qua socket domain riêng.

## Kết quả đạt được

* Frontend VibeMatch vận hành trên AWS Amplify.
* Domain `vibematch.cloud` hoạt động với HTTPS.
* Frontend có lớp bảo vệ bằng AWS WAF/Amplify Firewall.
* Ứng dụng có thể gọi REST API thông qua API Gateway.
* Realtime socket hoạt động qua domain riêng, phù hợp với frontend chạy HTTPS.

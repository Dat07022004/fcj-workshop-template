---
title: "Kết luận"
date: 2024-01-01
weight: 12
chapter: false
pre: " <b> 5.12. </b> "
---

Sau workshop, hệ thống VibeMatch đã có frontend vận hành trên AWS Amplify, REST API qua API Gateway, backend Docker chạy trên EC2 Auto Scaling sau ALB và dữ liệu lưu trên Amazon DocumentDB trong private subnet. Domain, HTTPS và WAF được cấu hình để cải thiện trải nghiệm truy cập và lớp bảo vệ frontend. Quy trình kiểm thử bằng Postman và browser xác nhận API, xác thực Clerk và kết nối database hoạt động đúng.

* FCJ workshop template: https://github.com/Dat07022004/fcj-workshop-template

* AWS DocumentDB Developer Guide: https://docs.aws.amazon.com/documentdb/latest/developerguide/

* AWS API Gateway HTTP API Guide: https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api.html

* AWS Amplify Hosting Documentation: https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html

* AWS WAF Developer Guide: https://docs.aws.amazon.com/waf/latest/developerguide/

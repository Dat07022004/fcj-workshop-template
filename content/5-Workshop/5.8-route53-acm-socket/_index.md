---
title: "Cấu hình Route 53, ACM và HTTPS cho Socket.IO"
date: 2024-01-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

> Kết quả cần đạt: Tạo domain HTTPS riêng cho Socket.IO vì frontend chạy HTTPS không được gọi socket HTTP.

## Điều kiện trước khi làm

* Domain `vibematch.cloud` đã trỏ nameserver về Route 53.

* ALB backend đã có target group healthy.

## Các bước thực hiện

1. Tạo hosted zone hoặc dùng hosted zone hiện có cho `vibematch.cloud`.

1. Tạo record `socket.vibematch.cloud` kiểu A Alias trỏ tới ALB.

1. Request ACM certificate cho `socket.vibematch.cloud` trong region `ap-southeast-1`.

1. Validate certificate bằng DNS record trong Route 53.

1. Tạo HTTPS listener port 443 trên ALB và gắn ACM certificate.

1. Forward listener 443 về target group backend port 3000.

1. Mở inbound 443 trên ALB security group.

1. Cập nhật frontend `VITE_SOCKET_URL=https://socket.vibematch.cloud` và redeploy Amplify.

## Kiểm tra hoàn tất

* `curl.exe -i https://socket.vibematch.cloud/api/health` trả 200 khi target group healthy.

* Browser không còn lỗi Mixed Content với Socket.IO.

* Socket.IO nên ưu tiên WebSocket transport hoặc bật ALB stickiness nếu vẫn dùng polling.

[CHÈN ẢNH: Ảnh Route 53 record `socket.vibematch.cloud` alias tới ALB]

[CHÈN ẢNH: Ảnh ACM certificate ở trạng thái Issued]

[CHÈN ẢNH: Ảnh ALB listener HTTPS 443 gắn certificate]

[CHÈN ẢNH: Ảnh frontend không còn Mixed Content]

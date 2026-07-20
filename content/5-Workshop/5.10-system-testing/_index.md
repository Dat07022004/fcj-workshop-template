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

[CHÈN ẢNH: Ảnh Postman test `/api/health` và `/api/health/db` thành công]

[CHÈN ẢNH: Ảnh Postman protected route với Bearer token thành công]

[CHÈN ẢNH: Ảnh frontend chạy trên `https://vibematch.cloud`]

[CHÈN ẢNH: Ảnh target group có 2 healthy targets]

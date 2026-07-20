---
title: "Tạo API Gateway cho frontend gọi backend"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

> Kết quả cần đạt: Tạo HTTPS endpoint ổn định để frontend gọi REST API mà không gọi trực tiếp ALB HTTP.

## Điều kiện trước khi làm

* ALB backend đã healthy.

* Biết ALB DNS: `webdating-backend-alb-218383004.ap-southeast-1.elb.amazonaws.com`.

## Các bước thực hiện

1. Tạo HTTP API trong API Gateway ở region `ap-southeast-1`.

1. Tạo HTTP proxy integration tới `http://<alb-dns>/{proxy}`.

1. Tạo route `ANY /{proxy+}` trỏ tới integration.

1. Tạo route `OPTIONS /{proxy+}` để xử lý preflight nếu cần.

1. Cấu hình CORS cho frontend origin `https://vibematch.cloud` và `https://www.vibematch.cloud`.

1. Dùng stage `$default` với auto deploy.

### Lệnh tham khảo

```powershell
$API_GATEWAY_URL = "https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com"
curl.exe "$API_GATEWAY_URL/api/health"
curl.exe "$API_GATEWAY_URL/api/health/db"
curl.exe -i -X OPTIONS "$API_GATEWAY_URL/api/users/me" -H "Origin: https://vibematch.cloud" -H "Access-Control-Request-Method: GET" -H "Access-Control-Request-Headers: Authorization,Content-Type"
```

## Kiểm tra hoàn tất

* `GET https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com/api/health` trả 200.

* Preflight `OPTIONS /api/users/me` trả CORS headers hợp lệ.

* Protected route thiếu token trả 401 nhưng vẫn có `access-control-allow-origin` đúng.

[CHÈN ẢNH: Ảnh API Gateway HTTP API đã tạo]

[CHÈN ẢNH: Ảnh integration tới ALB]

[CHÈN ẢNH: Ảnh routes ANY và OPTIONS]

[CHÈN ẢNH: Ảnh CORS configuration cho `https://vibematch.cloud`]

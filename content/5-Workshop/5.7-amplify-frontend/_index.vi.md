---
title: "Deploy frontend trên AWS Amplify"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

> Kết quả cần đạt: Frontend VibeMatch chạy trên Amplify Hosting, dùng custom domain và gọi backend qua API Gateway.

## Điều kiện trước khi làm

* Frontend build được bằng Vite/React.

* API Gateway REST endpoint đã hoạt động.

* Socket domain HTTPS đã hoặc sẽ được cấu hình ở phần sau.

## Các bước thực hiện

1. Tạo Amplify app và kết nối repository/branch frontend.

1. Cấu hình app root là `frontend` nếu project là monorepo.

1. Thêm environment variables cho frontend.

1. Deploy branch production/feature tương ứng.

1. Cấu hình rewrite cho SPA để các route như `/discover`, `/profile` không bị 404 khi refresh.

1. Gắn custom domain `vibematch.cloud` vào Amplify.

### Lệnh tham khảo

```text
VITE_API_URL=https://zsc1wtu6rc.execute-api.ap-southeast-1.amazonaws.com
VITE_SOCKET_URL=https://socket.vibematch.cloud

SPA rewrite:
Source: /<*>
Target: /index.html
Type: 404 (Rewrite)
```

## Kiểm tra hoàn tất

* Frontend mở được bằng `https://vibematch.cloud`.

* Các route SPA refresh không bị 404.

* Frontend gọi API qua `VITE_API_URL` và không bị CORS.

* Đăng nhập Clerk và gọi được profile/onboarding/discover.

![Route 53 hosted zone for vibematch.cloud](/images/5-Workshop/5.7-amplify-frontend/route53-hosted-zone.png)

![Amplify app deployed](/images/5-Workshop/5.7-amplify-frontend/amplify-app-deployed.png)

![Amplify environment variables](/images/5-Workshop/5.7-amplify-frontend/amplify-environment-variables.png)

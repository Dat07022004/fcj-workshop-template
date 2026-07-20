---
title: "Bật WAF cho frontend"
date: 2024-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

> Kết quả cần đạt: Bảo vệ lớp truy cập frontend bằng Amplify Firewall/AWS WAF.

## Điều kiện trước khi làm

* Frontend đã chạy trên AWS Amplify.

* Custom domain đã hoạt động ổn định.

## Các bước thực hiện

1. Mở Amplify app `webdating`.

1. Vào Hosting -> Firewall.

1. Bật `Enable Amplify-recommended Firewall protection`.

1. Kiểm tra trong WAF & Shield sẽ thấy Web ACL được tạo bởi Amplify.

1. Nếu muốn khóa domain mặc định của Amplify, bật `Restrict access to amplifyapp.com` sau khi custom domain chạy ổn định.

1. Theo dõi sampled requests và CloudWatch metrics để phát hiện request bất thường.

## Kiểm tra hoàn tất

* Web ACL `CreatedByAmplify-...` tồn tại trong AWS WAF.

* Frontend vẫn truy cập bình thường qua `https://vibematch.cloud`.

* Không tạo thêm Web ACL mới cho frontend nếu Amplify Firewall đã quản lý Web ACL này.

[CHÈN ẢNH: Ảnh Amplify Firewall đã bật recommended protection]

[CHÈN ẢNH: Ảnh AWS WAF Web ACL do Amplify tạo]

[CHÈN ẢNH: Ảnh sampled requests hoặc dashboard WAF]

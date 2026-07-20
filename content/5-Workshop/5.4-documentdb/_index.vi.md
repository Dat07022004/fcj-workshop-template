---
title: "Tạo Amazon DocumentDB"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

> Kết quả cần đạt: Tạo DocumentDB cluster riêng tư gồm 1 writer/primary instance và 1 reader replica làm standby/failover target.

## Điều kiện trước khi làm

* Đã có private DB subnets ở 2 AZ.

* Đã có `docdb-sg` cho phép port 27017 từ EC2 backend security group.

## Các bước thực hiện

1. Tạo DB subnet group từ 2 private DB subnets.

1. Tạo cluster parameter group cho DocumentDB 5.0 và giữ TLS enabled.

1. Tạo cluster `webdating-docdb` với master username dạng `webdating_admin`.

1. Tạo writer instance trong một AZ.

1. Tạo reader replica ở AZ khác để làm failover target.

1. Chờ cluster và instances chuyển sang trạng thái `available`.

1. Lấy cluster endpoint và reader endpoint.

1. Tạo secret `/webdating/backend/DATABASE_URL` trong Secrets Manager.

### Lệnh tham khảo

```powershell
$DOCDB_ENDPOINT = "webdating-docdb.cluster-cfu04me6ymt5.ap-southeast-1.docdb.amazonaws.com"
$DATABASE_URL = "mongodb://webdating_admin:<docdb-password>@$DOCDB_ENDPOINT:27017/webdating?tls=true&tlsCAFile=/etc/docdb/global-bundle.pem&replicaSet=rs0&readPreference=secondaryPreferred&retryWrites=false&authSource=admin&authMechanism=SCRAM-SHA-1"
aws secretsmanager create-secret --region
$AWS_REGION --name "/webdating/backend/DATABASE_URL" --secret-string
$DATABASE_URL
```

## Kiểm tra hoàn tất

* DocumentDB cluster có endpoint dạng `webdating-docdb.cluster-...ap-southeast-1.docdb.amazonaws.com`.

* Có 2 instances: 1 writer và 1 reader replica.

* Security group của DocumentDB chỉ mở 27017 từ backend EC2 SG.

* `DATABASE_URL` có `tls=true`, `tlsCAFile`, `replicaSet=rs0`, `retryWrites=false`, `authSource=admin`, `authMechanism=SCRAM-SHA-1`.

[CHÈN ẢNH: Ảnh DocumentDB subnet group]

[CHÈN ẢNH: Ảnh cluster `webdating-docdb` ở trạng thái available]

[CHÈN ẢNH: Ảnh writer và reader replica]

[CHÈN ẢNH: Ảnh secret DATABASE_URL đã tạo trong Secrets Manager]

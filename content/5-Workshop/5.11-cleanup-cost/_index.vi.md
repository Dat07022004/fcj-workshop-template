---
title: "Cleanup và tối ưu chi phí"
date: 2024-01-01
weight: 11
chapter: false
pre: " <b> 5.11. </b> "
---

Sau khi hoàn tất demo hoặc test, cần tạm dừng các tài nguyên tính phí theo giờ để tránh vượt credit. Các tài nguyên đáng chú ý gồm DocumentDB instances, NAT Gateway, VPC interface endpoints, EC2 instances, ALB và WAF.

| Tài nguyên | Cách tiết kiệm chi phí |
| --- | --- |
| EC2 Auto Scaling | Set desired/min capacity về 0 khi không cần chạy backend. |
| DocumentDB | Stop cluster khi không dùng; snapshot trước khi xóa nếu cần giữ dữ liệu. |
| NAT Gateway | Xóa NAT Gateway nếu workshop đã kết thúc; NAT tính phí theo giờ và data processed. |
| VPC endpoints | Xóa interface endpoints không dùng nếu đã dừng backend lâu dài. |
| ALB/API Gateway/WAF | Giữ khi cần demo public; xóa nếu kết thúc môi trường thực tập. |

```powershell
aws autoscaling update-auto-scaling-group --region ap-southeast-1 --auto-scaling-group-name webdating-backend-asg --min-size 0 --desired-capacity 0
aws docdb stop-db-cluster --region ap-southeast-1 --db-cluster-identifier webdating-docdb
```

[CHÈN ẢNH: Ảnh Cost Explorer/Billing sau khi theo dõi chi phí]

[CHÈN ẢNH: Ảnh Auto Scaling Group đã scale về 0 khi tạm dừng]

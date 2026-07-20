---
title: "Blog 1 - Xây dựng AIOps cho ứng dụng trên Amazon Bedrock: Từ giám sát thủ công đến self-driving AI operations"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

Khi các ứng dụng Generative AI bắt đầu được đưa vào môi trường production, bài toán không còn chỉ là chọn foundation model nào, viết prompt ra sao hay tích hợp API như thế nào. Một vấn đề quan trọng hơn bắt đầu xuất hiện: làm sao để vận hành các workload AI ổn định, phát hiện sớm sự cố và phản ứng kịp thời khi usage tăng nhanh?

Trong quá trình tìm hiểu về Amazon Bedrock và vận hành ứng dụng AI trên AWS, mình có nghiên cứu một giải pháp khá thú vị tên là **Amazon Bedrock Ops Alert**. Đây là một kiến trúc AIOps giúp tự động giám sát workload chạy trên Amazon Bedrock, phát hiện lỗi, theo dõi mức sử dụng, nhận diện bất thường và hỗ trợ tạo AWS Support case khi cần.

Điều mình thấy hay ở giải pháp này là nó không chỉ đơn thuần gửi cảnh báo. Nó còn gom nhiều tín hiệu vận hành lại với nhau, phân loại vấn đề và cung cấp ngữ cảnh để đội ngũ SRE xử lý nhanh hơn.

![Amazon Bedrock Ops Alert architecture](/images/3-BlogsPosted/3.1-Blog1/bedrock-aiops-architecture.png)

## Vấn đề cần giải quyết

Khi một ứng dụng Generative AI còn ở giai đoạn thử nghiệm, việc theo dõi lỗi thường khá đơn giản. Developer có thể kiểm tra log, xem response từ model và xử lý thủ công nếu có lỗi phát sinh.

Tuy nhiên, khi workload được mở rộng cho nhiều người dùng, nhiều team hoặc nhiều ứng dụng cùng sử dụng Amazon Bedrock, việc vận hành trở nên phức tạp hơn rất nhiều. Một số vấn đề thường gặp gồm:

* Số lượng request tăng nhanh theo thời gian.
* Token usage tăng do prompt dài hoặc output lớn.
* Latency tăng làm ảnh hưởng trải nghiệm người dùng.
* Throttling xảy ra khi workload tiến gần giới hạn quota.
* Lỗi client hoặc server cần được phát hiện sớm.
* Alarm threshold cần được cập nhật khi quota thay đổi.
* Khi mở support case, SRE phải tự gom metric, quota và lịch sử sử dụng.

Nếu tất cả những việc này đều xử lý thủ công, đội ngũ vận hành rất dễ rơi vào trạng thái reactive: chỉ bắt đầu điều tra khi người dùng đã bị ảnh hưởng.

## Giải pháp

Amazon Bedrock Ops Alert giải quyết bài toán này bằng cách xây dựng một hệ thống giám sát tự động gồm ba lớp chính.

**Layer 1: Critical Error Detection**

Tầng này theo dõi các lỗi quan trọng như client errors, server errors và throttles. Mục tiêu là phát hiện nhanh các sự cố có thể ảnh hưởng trực tiếp đến workload.

**Layer 2: Usage Rate Monitoring**

Tầng này theo dõi mức sử dụng của ứng dụng, bao gồm request rate, token usage và latency. Các ngưỡng cảnh báo có thể được tính toán dựa trên quota hiện tại, ví dụ cảnh báo khi workload tiến gần một tỷ lệ nhất định của hạn mức được cấp.

**Layer 3: Anomaly Detection**

Tầng này sử dụng CloudWatch Anomaly Detection để nhận diện các mẫu bất thường trong invocations, input tokens, output tokens và latency. Thay vì chỉ dựa vào ngưỡng cố định, hệ thống có thể học baseline từ dữ liệu lịch sử và cảnh báo khi có biến động khác thường.

Kiến trúc tổng quan có thể hình dung như sau:

```text
Amazon Bedrock Metrics
-> Amazon CloudWatch Alarms
-> Composite Alarm
-> Amazon SNS
-> AWS Lambda
-> Service Quotas / AWS Support API
-> Email Notification
```

Trong mô hình này, Amazon Bedrock gửi runtime metrics lên Amazon CloudWatch. CloudWatch Alarms theo dõi lỗi, usage và anomaly. Composite Alarm gom các cảnh báo con lại. SNS kích hoạt Lambda xử lý cảnh báo. Lambda kiểm tra quota, lịch sử metric và loại sự cố. Nếu cần, hệ thống tạo hoặc cập nhật AWS Support case. Cuối cùng, email notification được gửi đến nhóm SRE với đầy đủ thông tin ngữ cảnh.

Một điểm đáng chú ý là hệ thống còn có cơ chế tự động cập nhật ngưỡng cảnh báo. Khi quota của Amazon Bedrock thay đổi, Lambda có thể truy vấn Service Quotas, tính lại threshold và cập nhật CloudWatch Alarms. Điều này giúp giảm thao tác thủ công và tránh tình trạng alarm bị lỗi thời.

## Góc nhìn cá nhân

Theo mình, Amazon Bedrock Ops Alert là một ví dụ rất rõ cho xu hướng self-driving operations trên cloud.

Với workload truyền thống, đội ngũ vận hành thường quan tâm nhiều đến CPU, memory, disk hoặc network. Nhưng với Generative AI workload, các chỉ số quan trọng lại khác đi: số lần gọi model, số token đầu vào, số token đầu ra, latency, throttling và quota usage.

Điều này khiến observability cho AI application cần một cách tiếp cận riêng. Không chỉ biết hệ thống có lỗi hay không, mà còn cần biết workload đang tiến gần giới hạn nào, usage có bất thường không, và sự cố đó nên xử lý theo hướng tăng quota hay điều tra lỗi ứng dụng.

Mình đánh giá cao giải pháp này ở ba điểm:

* Giúp chuyển từ reactive monitoring sang proactive operations.
* Giảm công việc thủ công cho AI SRE team.
* Tạo support case có ngữ cảnh thay vì chỉ gửi một cảnh báo rời rạc.

## Một số lưu ý khi triển khai

Dù giải pháp này khá hữu ích, vẫn có một số điểm cần lưu ý:

* Cần cấu hình IAM permission chặt chẽ cho Lambda, Service Quotas và Support API.
* AWS Support API yêu cầu tài khoản có gói hỗ trợ phù hợp.
* CloudWatch Anomaly Detection cần dữ liệu lịch sử để học baseline.
* Ngưỡng cảnh báo nên được tinh chỉnh để tránh false alarm.
* Không nên tự động tạo quá nhiều support case trùng lặp.
* Cần theo dõi chi phí phát sinh từ CloudWatch Alarms, Lambda và SNS.

## Kết luận

Khi Generative AI được triển khai ở quy mô production, việc vận hành hệ thống không thể chỉ dừng lại ở việc gọi model thành công. Doanh nghiệp cần một cơ chế giám sát chủ động hơn để đảm bảo workload ổn định, phát hiện sớm bất thường và phản ứng nhanh khi có sự cố.

Amazon Bedrock Ops Alert cho thấy cách AWS kết hợp Amazon Bedrock, CloudWatch, Lambda, SNS, Service Quotas và AWS Support API để xây dựng một mô hình AIOps thực tế cho ứng dụng AI.

Theo mình, đây là một hướng rất đáng tham khảo cho các team đang xây dựng ứng dụng Generative AI trên AWS. Khi AI workload ngày càng lớn, self-driving operations sẽ không còn là một lựa chọn "nice to have", mà dần trở thành một phần quan trọng trong kiến trúc vận hành production.

Link bài Facebook: [Xem Blog 1 trên AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2177589129672714)

Link bài viết gốc: [How to build self-driving AI operations on Amazon Bedrock at scale](https://aws.amazon.com/blogs/machine-learning/how-to-build-self-driving-ai-operations-on-amazon-bedrock-at-scale/)

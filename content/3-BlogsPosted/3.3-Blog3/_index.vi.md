---
title: "Blog 3 - Amazon S3 Annotations: Khi dữ liệu trong S3 không chỉ được lưu trữ, mà còn có ngữ cảnh
"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Trong quá trình tìm hiểu các cập nhật mới của AWS, mình thấy một tính năng khá thú vị vừa được giới thiệu cho Amazon S3, đó là **Amazon S3 annotations**. Đây là một tính năng cho phép gắn thêm thông tin mô tả trực tiếp vào từng object trong S3, giúp dữ liệu không chỉ được lưu trữ mà còn mang theo ngữ cảnh để phục vụ tìm kiếm, phân tích và các workflow AI.

Điều mình thấy đáng chú ý ở tính năng này là cách AWS mở rộng vai trò của S3. Trước đây, chúng ta thường xem S3 như nơi lưu trữ hình ảnh, video, file log, tài liệu hoặc dataset. Nhưng khi dữ liệu ngày càng lớn, việc chỉ lưu file thôi là chưa đủ. Hệ thống còn cần hiểu được file đó là gì, nội dung bên trong nói về điều gì, thuộc nhóm dữ liệu nào và có thể được sử dụng trong ngữ cảnh nào.

![Amazon S3 annotations](/images/3-BlogsPosted/3.3-Blog3/amazon-s3-annotations.png)

## Vấn đề cần giải quyết

Trong nhiều hệ thống dữ liệu lớn, object thường được lưu trong Amazon S3, nhưng phần metadata chi tiết lại nằm ở một nơi khác. Ví dụ, một file video có thể được lưu trong S3, còn transcript, nội dung tóm tắt, thông tin bản quyền, rating nội dung hoặc thông số kỹ thuật lại nằm trong database, data catalog hoặc hệ thống quản lý tài sản số riêng.

Cách tiếp cận này có thể hoạt động tốt ở quy mô nhỏ, nhưng khi dữ liệu tăng lên hàng triệu hoặc hàng tỷ object, việc đồng bộ metadata trở nên phức tạp hơn. Nếu object được copy, replicate sang region khác hoặc đi qua nhiều pipeline xử lý dữ liệu, phần ngữ cảnh đi kèm cũng cần được cập nhật chính xác.

Điều này đặt ra câu hỏi: liệu có thể gắn phần ngữ cảnh trực tiếp vào object trong S3 để dữ liệu dễ được tìm kiếm, phân tích và sử dụng bởi AI agent hơn hay không?

## Giải pháp

Đó cũng là lý do Amazon S3 annotations trở nên đáng chú ý.

Thay vì phải quản lý toàn bộ metadata chi tiết ở một hệ thống riêng, S3 annotations cho phép gắn trực tiếp các thông tin mô tả vào từng object. Với một file video, annotation có thể là transcript do AI tạo ra, tóm tắt nội dung, ngôn ngữ phụ đề, thông tin bản quyền, rating nội dung hoặc thông số kỹ thuật như codec, độ phân giải và audio track.

Điểm hay là annotation có thể được cập nhật hoặc xóa mà không cần ghi lại object gốc. Điều này rất hữu ích với các object có dung lượng lớn, vì chúng ta không cần upload lại toàn bộ file chỉ để thay đổi phần mô tả hoặc thông tin phân loại.

Kiến trúc có thể hình dung như sau:

```text
S3 Object
-> S3 Annotations
-> S3 Metadata Tables
-> Amazon Athena / Analytics Engine / AI Agent
```

Trong mô hình này, object vẫn được lưu trong Amazon S3, còn annotation đóng vai trò là lớp ngữ cảnh đi kèm. Khi annotations được đưa vào S3 Metadata Tables, các công cụ như Amazon Athena có thể truy vấn metadata này để phục vụ analytics hoặc các hệ thống AI.

Ví dụ, một công ty media có rất nhiều video trong S3. Nếu muốn tìm các video có phụ đề tiếng Tây Ban Nha, rating PG hoặc thuộc một nhóm nội dung cụ thể, hệ thống có thể truy vấn annotation table thay vì phải mở từng video hoặc dò thông tin ở nhiều hệ thống khác nhau.

Điều mình thấy thú vị là S3 annotations giúp đưa dữ liệu và business context lại gần nhau hơn. Trước đây, dữ liệu thường chỉ được lưu trữ. Nhưng với AI, dữ liệu cần phải có thể hiểu được. Một object nếu không có ngữ cảnh thì rất khó để AI agent tìm đúng, phân loại đúng hoặc hành động đúng.

## Kết luận và kiến nghị

Theo mình, Amazon S3 annotations không chỉ là một tính năng metadata mới, mà là một bước tiến giúp S3 phù hợp hơn với các workload hiện đại như data lake, analytics, media platform và AI agent.

Trong tương lai, khi dữ liệu ngày càng lớn và AI agent được sử dụng nhiều hơn trong doanh nghiệp, việc quản lý ngữ cảnh của dữ liệu sẽ trở nên rất quan trọng. Câu hỏi không chỉ còn là dữ liệu được lưu ở đâu, mà là hệ thống có hiểu dữ liệu đó có ý nghĩa gì hay không.

Đối với mình, S3 annotations là một cập nhật đáng theo dõi vì nó giúp object trong S3 không chỉ là một file nằm trong bucket, mà có thể mang theo cả thông tin mô tả, ngữ cảnh nghiệp vụ và dữ liệu hỗ trợ phân tích. Đây có thể là nền tảng hữu ích cho các hệ thống AI và analytics cần xử lý dữ liệu ở quy mô lớn.

Link bài Facebook: [Xem Blog 3 trên AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2215539599211000)

Nguồn tham khảo: [Amazon S3 Annotations: Attach rich, queryable context directly to your objects](https://aws.amazon.com/blogs/aws/amazon-s3-annotations-attach-rich-queryable-context-directly-to-your-objects/?content_source=fb&fb_content_id=Q9-wBQF8BH-Hzw33CxzDRc2ZdH-wywdJpVFct3lbgEsAmg2vq_B2eU3E9IxAo5bsiQ&channel_type=fb)

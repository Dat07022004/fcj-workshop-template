---
title: "Blog 2 - Xử lý đối tượng Amazon S3 ở quy mô lớn với AWS Step Functions Distributed Map"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

Trong các hệ thống dữ liệu hiện đại, Amazon S3 thường đóng vai trò là nơi lưu trữ trung tâm cho rất nhiều loại dữ liệu khác nhau, từ log ứng dụng, dữ liệu khách hàng, dataset phục vụ machine learning cho đến các file báo cáo hoặc dữ liệu sự kiện. Khi khối lượng dữ liệu ngày càng lớn, thách thức không còn nằm ở việc lưu trữ, mà nằm ở cách xử lý hàng nghìn hoặc hàng triệu object một cách ổn định, song song và dễ vận hành.

Trước đây, để xử lý dữ liệu trong S3 theo một prefix cụ thể, các đội kỹ thuật thường phải xây dựng những workflow tương đối phức tạp. Một bước dùng để liệt kê object, một bước khác đọc từng file, sau đó cần thêm logic để parse nội dung và tiếp tục điều phối các tác vụ xử lý phía sau. Cách tiếp cận này có thể giải quyết bài toán, nhưng thường kéo theo nhiều mã tùy chỉnh, nhiều trạng thái trung gian và chi phí vận hành không nhỏ.

AWS Step Functions Distributed Map giúp đơn giản hóa bài toán này bằng cách cho phép chạy nhiều nhánh xử lý song song trên một tập dữ liệu lớn. Điểm đáng chú ý là khả năng lặp theo S3 prefix kết hợp với tham số chuyển đổi **LOAD_AND_FLATTEN**. Nhờ đó, workflow có thể dùng `S3ListObjectsV2` để tìm các object dưới một prefix cụ thể, sau đó đọc và phân rã trực tiếp nội dung file thành từng bản ghi để xử lý trong cùng một Map state. Nói cách khác, Step Functions không chỉ nhìn thấy danh sách file, mà còn có thể đi thẳng vào phần dữ liệu bên trong file.

![AWS Step Functions Distributed Map S3 workflow](/images/3-BlogsPosted/3.2-Blog2/step-functions-distributed-map-s3.png)

## Tình huống minh họa

Bài viết minh họa bằng một tình huống rất thực tế: xử lý và tổng hợp log ứng dụng. Các file log được lưu trong một S3 bucket theo prefix, chẳng hạn như `/logs/daily`. State machine của Step Functions sẽ liệt kê toàn bộ file log trong prefix đó, sau đó sử dụng Distributed Map để xử lý các file song song.

Trong quá trình xử lý, workflow thống kê số lượng log theo các mức `INFO`, `WARNING` và `ERROR`, ghi metric lỗi theo giờ vào Amazon CloudWatch, lưu kết quả thống kê vào Amazon DynamoDB, rồi gọi AWS Lambda để tổng hợp kết quả cuối cùng.

Kết quả đầu ra của workflow là một bản tóm tắt theo ngày, thể hiện tổng số lỗi, tổng số cảnh báo, tổng số record và phân tích chi tiết theo từng giờ. Đây là kiểu đầu ra rất hữu ích cho các hệ thống cần quan sát tình trạng vận hành, phát hiện bất thường hoặc xây dựng dashboard giám sát.

## Giá trị của LOAD_AND_FLATTEN

Giá trị quan trọng của `LOAD_AND_FLATTEN` nằm ở chỗ Distributed Map không chỉ xử lý metadata của object trong S3. Khi được cấu hình, nó có thể đọc nội dung thực tế của từng object, parse dữ liệu theo định dạng đã khai báo và biến từng dòng hoặc từng record thành item riêng để xử lý.

Các định dạng được hỗ trợ gồm:

* CSV
* JSON
* JSONL
* PARQUET

Đây đều là những định dạng rất phổ biến trong data lake, batch processing và pipeline phân tích dữ liệu.

## Lưu ý khi thiết kế prefix

Một điểm cần lưu ý khi thiết kế prefix là nên dùng dấu `/` ở cuối đường dẫn. Nếu prefix là `folder1`, workflow có thể match cả `folder1/file.csv` và `folder10/file.csv`. Trong khi đó, nếu dùng `folder1/`, phạm vi xử lý sẽ chính xác hơn.

Ngoài ra, các object nằm dưới cùng một prefix nên có cùng định dạng dữ liệu. Nếu khai báo `InputType` là `JSONL`, prefix đó không nên chứa lẫn các file CSV hoặc Parquet.

## Góc nhìn kiến trúc

Từ góc nhìn kiến trúc, tính năng này giúp giảm đáng kể độ phức tạp của pipeline. Trước đây, một workflow thường cần tách riêng việc liệt kê object, tạo manifest, đọc file, parse nội dung và điều phối xử lý song song. Với Distributed Map và `LOAD_AND_FLATTEN`, nhiều bước trong số đó có thể được gom lại trong một Map state.

Điều này giúp hệ thống ít mã tùy chỉnh hơn, ít thành phần trung gian hơn và dễ mở rộng hơn khi số lượng file tăng lên.

Cách tiếp cận này đặc biệt phù hợp với các hệ thống serverless và event-driven, nơi dữ liệu liên tục được ghi vào S3. Thay vì phải tạo danh sách file cố định trước mỗi lần chạy, workflow có thể đọc trực tiếp những object hiện có trong prefix tại thời điểm thực thi. Điều đó giúp pipeline linh hoạt hơn, bền vững hơn và dễ thích nghi với các thay đổi trong dữ liệu đầu vào.

## Kết luận

AWS Step Functions Distributed Map với khả năng xử lý theo S3 prefix và `LOAD_AND_FLATTEN` là một cải tiến rất thực tế cho các pipeline dữ liệu trên AWS. Nó giúp kết hợp việc liệt kê object, đọc nội dung và phân rã record vào cùng một workflow, từ đó giảm bớt nhu cầu xây dựng logic điều phối phức tạp bên ngoài.

Giá trị lớn nhất có thể đúc kết là: khi dữ liệu ngày càng lớn, điều quan trọng không chỉ là xử lý được dữ liệu, mà là xử lý theo cách dễ mở rộng, dễ quan sát và dễ vận hành. Tính năng này giúp các đội kỹ thuật tập trung nhiều hơn vào logic nghiệp vụ thay vì mất thời gian duy trì các lớp xử lý trung gian lặp đi lặp lại.

Với các bài toán như phân tích log, batch processing, data lake ingestion, tổng hợp báo cáo hoặc xử lý dữ liệu phục vụ machine learning, Distributed Map kết hợp S3 prefix là một hướng tiếp cận đáng cân nhắc trong kiến trúc AWS hiện đại.

Link bài Facebook: [Xem Blog 2 trên AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj/posts/2206913116740315)

# Kiểm thử hiệu năng API với Apache JMeter

**Sinh viên:** `<Họ tên>` | **MSSV:** `<MSSV>` | **Lớp:** `<Lớp>`
**Công cụ:** Apache JMeter 5.6.3 | **Đối tượng:** https://jsonplaceholder.typicode.com/posts

## 1. Giới thiệu JMeter

Apache JMeter là công cụ mã nguồn mở viết bằng Java, dùng để kiểm thử hiệu năng và kiểm thử tải bằng cách mô phỏng nhiều người dùng ảo truy cập hệ thống cùng lúc. Các thành phần chính: **Thread Group** (số người dùng ảo), **Sampler** (gửi request), **Listener** (hiển thị kết quả).

## 2. Cài đặt

1. Cài JDK (kiểm tra bằng `java -version`).
2. Tải gói **Binaries** của JMeter 5.6.3 tại https://jmeter.apache.org, giải nén đầy đủ.
3. Chạy `bin/jmeter.bat`.

> Nếu gặp lỗi `Unable to access jarfile ApacheJMeter.jar`: do chạy trực tiếp trong file .zip hoặc tải nhầm gói Source. Cần giải nén đầy đủ gói Binaries.

## 3. Kịch bản kiểm thử

**Mục tiêu:** mô phỏng 100 người dùng ảo truy cập API `GET /posts` trong 60 giây, đánh giá độ ổn định và thời gian phản hồi.

**Thread Group**

| Tham số | Giá trị |
|---|---|
| Number of Threads | 100 |
| Ramp-up period | 10 giây |
| Loop Count | Infinite |
| Duration | 60 giây |

![Thread Group](thread-group.png)

**HTTP Request**

| Tham số | Giá trị |
|---|---|
| Protocol | https |
| Server Name | jsonplaceholder.typicode.com |
| Method / Path | GET / `/posts` |
| Encoding | UTF-8 |

![HTTP Request](http-request.png)

**Listener:** View Results Tree, Aggregate Report, Summary Report.

## 4. Kết quả

![Aggregate Report](aggregate-report.png)

| Chỉ số | Giá trị |
|---|---|
| Tổng số request | 83.782 |
| Thất bại (Error %) | 0 (0,00%) |
| Average / Median | 65 ms / 56 ms |
| 90% / 95% / 99% Line | 73 / 99 / 303 ms |
| Min / Max | 25 ms / 8.672 ms |
| Throughput | 1.388,5 request/giây |

## 5. Phân tích

- **Độ ổn định:** 83.782 request không có lỗi nào, tỷ lệ thành công 100%.
- **Thời gian phản hồi:** trung bình 65 ms, 95% request dưới 99 ms. Median gần với Average nên phần lớn request có tốc độ đồng đều.
- **Request chậm:** 99% Line là 303 ms và Max là 8.672 ms, cho thấy một số ít request chậm bất thường (có thể do mạng hoặc máy chủ, chưa đủ dữ liệu để kết luận nguyên nhân).
- **Throughput:** rất cao do kịch bản không có Timer, mỗi user gửi request liên tục, nên đây là mức tải tối đa chứ không phải hành vi người dùng thật.

## 6. Kết luận

Dưới tải 100 người dùng ảo trong 60 giây, API `GET /posts` hoạt động **ổn định và nhanh** (0% lỗi, trung bình 65 ms, ~1.388 req/s). Cần lưu ý một số ít request chậm bất thường. Hướng mở rộng: thử thêm mức tải 50/200 thread, thêm Timer và Assertion.

*Lưu ý: kết quả phụ thuộc vào mạng và máy chạy; đối tượng kiểm thử là API demo công khai.*

## 7. Cấu trúc repo và cách chạy lại

```
├── README.md
├── api-test.jmx
├── thread-group.png
├── http-request.png
└── aggregate-report.png
```

Mở `api-test.jmx` bằng JMeter → **Clear All** → **Start**, hoặc chạy CLI:

```bash
jmeter -n -t api-test.jmx -l result.jtl
```

## Tài liệu tham khảo

- Apache JMeter User Manual: https://jmeter.apache.org/usermanual/index.html
- JSONPlaceholder: https://jsonplaceholder.typicode.com/

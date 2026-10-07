# BÁO CÁO THỰC HÀNH 8.7
## An toàn và Bảo mật trong dự án phần mềm mã nguồn mở

| Thông tin | Chi tiết |
|-----------|----------|
| Sinh viên thực hiện | Nguyễn Như Hồng Hạnh |
| Repository | https://github.com/hanhnguyen230505-code/oss-security-practice |

---

## Phần 1. Nghiên cứu Security Advisory: lỗ hổng CVE-2023-30861 của Flask

### 1.1. Thông tin chung

| Hạng mục | Nội dung |
|----------|----------|
| Dự án | pallets/flask (framework web Python) |
| Mã định danh | CVE-2023-30861 |
| Công bố | Tháng 05/2023 |
| Điểm CVSS | 7.5 – mức High |
| Phiên bản bị ảnh hưởng | Flask 2.2.x đến 2.2.4 và Flask 2.3.x đến 2.3.1 |
| Phiên bản đã vá | Flask 2.2.5 và 2.3.2 |

### 1.2. Bản chất của lỗ hổng

Khi một ứng dụng Flask được đặt sau reverse proxy có bộ nhớ đệm (Nginx, Varnish, Apache Traffic Server hoặc CDN), phản hồi có chứa header `Set-Cookie` có thể bị proxy lưu lại. Nguyên nhân là phản hồi thiếu chỉ thị `Vary: Cookie`, khiến proxy không phân biệt được các người dùng khác nhau và trả cùng một bản phản hồi (kèm cookie session) cho nhiều người.

Hệ quả là cookie session của người dùng A có thể được gửi tới người dùng B.

### 1.3. Mức độ rủi ro

- Kẻ tấn công có thể chiếm phiên đăng nhập (Session Hijacking) của người khác mà không cần biết mật khẩu.
- Có thể dẫn tới giả mạo danh tính và chiếm quyền tài khoản.
- Lỗi chỉ xuất hiện trong một cấu hình triển khai cụ thể (có proxy cache), nên khó phát hiện nếu chỉ kiểm thử ở môi trường phát triển.

### 1.4. Cách dự án khắc phục

1. Thêm header `Vary: Cookie` vào các phản hồi khi session được truy cập hoặc bị thay đổi, để proxy không dùng chung bản lưu đệm giữa các người dùng.
2. Phát hành bản vá trên hai nhánh đang được hỗ trợ là 2.2.5 và 2.3.2.
3. Công bố advisory để người dùng biết cần nâng cấp.

---

## Phần 2. Quy trình xử lý Security Issue đề xuất

Quy trình dưới đây dành cho nhóm maintainer của một thư viện/ứng dụng OSS giả định.

```
Tiếp nhận → Đánh giá → Vá lỗi kín → Công bố
```

### Bước 1. Tiếp nhận báo cáo
- Bật **Private Vulnerability Reporting** trên GitHub để người báo cáo gửi thông tin riêng tư.
- Tạo file `SECURITY.md` ở thư mục gốc, nêu rõ kênh liên hệ và yêu cầu **không** báo lỗi bảo mật qua Issue công khai.

### Bước 2. Xác minh và đánh giá
- Gửi xác nhận đã nhận báo cáo trong vòng 24–48 giờ.
- Dựng môi trường sandbox cô lập để tái hiện lỗi (Proof of Concept).
- Xác định phạm vi phiên bản bị ảnh hưởng và chấm điểm CVSS.

### Bước 3. Vá lỗi trong môi trường kín
- Tạo **Temporary Private Fork** từ GitHub Security Advisory để viết bản vá cùng người báo cáo mà không lộ mã vá ra ngoài.
- Viết unit test và regression test để chắc chắn bản vá sửa đúng lỗi, không làm hỏng chức năng hiện có.

### Bước 4. Công bố có trách nhiệm
- Đề nghị cấp mã CVE thông qua GitHub Advisory.
- Thống nhất thời điểm công bố với người báo cáo, thường trong khoảng 30–90 ngày.
- Phát hành bản mới (release tag) cùng hướng dẫn nâng cấp, đồng thời ghi nhận đóng góp của nhà nghiên cứu (Credit).

---

## Phần 3. Bài học bảo mật rút ra

### 3.1. Các vấn đề thường gặp trong dự án OSS

| Vấn đề | Mô tả |
|--------|-------|
| Rủi ro chuỗi cung ứng | Dùng thư viện bên thứ ba đã có lỗ hổng, hoặc bị tấn công Dependency Confusion, Typosquatting |
| Lộ thông tin nhạy cảm | Vô tình commit chuỗi kết nối CSDL, API token, secret key vào lịch sử commit công khai |
| Báo lỗi sai kênh | Mở issue công khai về lỗ hổng nghiêm trọng khi chưa có bản vá, tạo điều kiện cho khai thác zero-day |

### 3.2. Biện pháp phòng ngừa

- **Quản lý phụ thuộc:** dùng GitHub Dependabot hoặc Snyk để tự động phát hiện và cập nhật gói có lỗ hổng.
- **Chặn lộ bí mật:** bật Secret Scanning và Push Protection để từ chối commit chứa thông tin xác thực.
- **Phân tích mã tĩnh:** tích hợp CodeQL (SAST) vào quy trình CI/CD để rà soát lỗi trước khi merge.

---

## Kết luận

Trường hợp CVE-2023-30861 cho thấy một lỗi nhỏ về header HTTP cũng có thể gây hậu quả lớn khi kết hợp với môi trường triển khai thực tế. Một dự án OSS cần có kênh báo cáo riêng tư, quy trình xử lý rõ ràng và các công cụ tự động hóa để giữ an toàn cho người dùng.
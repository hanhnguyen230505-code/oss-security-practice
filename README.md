# oss-security-practice

Repository lưu bài thực hành **8.7 – An toàn và Bảo mật trong dự án phần mềm mã nguồn mở**.

**Sinh viên:** Nguyễn Như Hồng Hạnh

## Giới thiệu

Repo này tổng hợp kết quả tìm hiểu về cách các dự án mã nguồn mở (OSS) tiếp nhận, xử lý và công bố lỗ hổng bảo mật. Bài thực hành lấy lỗ hổng của framework Flask làm ví dụ thực tế, sau đó xây dựng một quy trình xử lý security issue mẫu.

## Cấu trúc repository

| File | Nội dung |
|------|----------|
| `README.md` | Giới thiệu tổng quan về repo (file này) |
| `REPORT_8_7.md` | Báo cáo chi tiết của bài thực hành |

## Tóm tắt nhanh

- **Case study:** CVE-2023-30861 của `pallets/flask` – cookie session có thể bị proxy cache phát tán cho người dùng khác.
- **Quy trình đề xuất:** 4 bước từ tiếp nhận báo cáo, đánh giá, vá lỗi kín đến công bố có trách nhiệm.
- **Bài học chính:** kiểm soát phụ thuộc, không để lộ bí mật trong mã nguồn, và không báo lỗi bảo mật qua issue công khai.

## Cách đọc

Xem file [`REPORT_8_7.md`](./REPORT_8_7.md) để đọc phân tích đầy đủ.

## Mục đích sử dụng

Nội dung phục vụ mục đích học tập trong học phần về phát triển phần mềm mã nguồn mở.
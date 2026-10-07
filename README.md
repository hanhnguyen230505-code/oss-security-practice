# Báo cáo thực hành 8.7: An toàn và Bảo mật trong dự án phần mềm mã nguồn mở

**Sinh viên thực hiện:** NGUYỄN NHƯ HỒNG HẠNH  
**Repository:** https://github.com/hanhnguyen230505-code/oss-security-practice  

---

## 1. Phân tích Security Advisory của một dự án OSS trên GitHub

- **Tên dự án:** pallets/flask
- **Mã CVE:** CVE-2023-30861
- **Thời điểm công bố:** Tháng 05/2023
- **Mô tả lỗ hổng và phạm vi ảnh hưởng:**
  - Lỗ hổng rò rỉ cookie session khi ứng dụng hoạt động phía sau các reverse caching proxy (như Nginx, Varnish, Apache Traffic Server, CDN). Khi endpoint trả về header `Set-Cookie`, các proxy lưu đệm phản hồi này và phát tán giá trị cookie của phiên hiện tại cho các người dùng khác.
  - Các phiên bản bị ảnh hưởng gồm toàn bộ nhánh Flask `<= 2.2.4` và `<= 2.3.1`.
- **Mức độ nghiêm trọng và rủi ro:**
  - Điểm số: CVSS 7.5 (Mức High).
  - Rủi ro trực tiếp: Cho phép kẻ tấn công thực hiện Session Hijacking, đánh cắp danh tính hoặc chiếm quyền kiểm soát tài khoản người dùng khác trên hệ thống mà không cần mật khẩu.
- **Biện pháp xử lý của dự án:**
  - Dự án bổ sung chỉ thị `Vary: Cookie` vào các phản hồi có session bị thay đổi nhằm ngăn cản bộ nhớ đệm proxy lưu trữ nhầm.
  - Phát hành bản vá khắc phục trên các bản cập nhật Flask 2.2.5 và Flask 2.3.2.

---

## 2. Đề xuất quy trình xử lý Security Issue cho dự án giả định

Quy trình được thiết kế cho vai trò Maintainer quản lý một ứng dụng/thư viện OSS:

1. **Kênh tiếp nhận (Ingestion):**
   - Kích hoạt tính năng Private Vulnerability Reporting trên GitHub để nhận thông báo ẩn danh và bảo mật.
   - Công bố file `SECURITY.md` ở thư mục gốc quy định rõ đầu mối liên hệ thay vì dùng Issue thông thường.
2. **Xác minh và Đánh giá (Triage & Verification):**
   - Phản hồi xác nhận tiếp nhận thông tin trong vòng 24–48 giờ.
   - Tạo môi trường sandbox cô lập để tái hiện mã độc/lỗi (PoC), phân tích độ lan rộng và gán chỉ số CVSS.
3. **Vá lỗi và Thử nghiệm kín (Remediation & Testing):**
   - Mở một Temporary Private Fork từ GitHub Security Advisory để thảo luận và viết mã vá bí mật cùng người báo cáo.
   - Thiết lập bài kiểm thử tự động (Unit Test / Regression Test) nhằm xác nhận bản vá không phá vỡ logic cũ.
4. **Công bố có trách nhiệm (Responsible Disclosure):**
   - Yêu cầu cấp mã CVE định danh thông qua GitHub Advisory.
   - Thống nhất mốc thời gian công bố (thường sau 30 đến 90 ngày kể từ khi ghi nhận).
   - Phát hành đồng loạt mã nguồn mới (Release tag), đính kèm hướng dẫn cập nhật và ghi nhận đóng góp của nhà nghiên cứu bảo mật (Credit).

---

## 3. Bài học bảo mật rút ra từ dự án phần mềm mã nguồn mở

- **Vấn đề bảo mật thường gặp:**
  - Chuỗi cung ứng (Software Supply Chain): Sử dụng các gói thư viện bên ngoài (dependencies) có sẵn lỗ hổng hoặc bị tấn công Dependency Confusion / Typosquatting.
  - Lộ lọt thông tin nhạy cảm: Lập trình viên vô tình commit các chuỗi kết nối cơ sở dữ liệu, API Token hoặc Secret Key lên lịch sử commit công khai.
  - Báo cáo lỗi thiếu an toàn: Người dùng mở issue công khai để hỏi về một lỗi bảo mật nghiêm trọng trước khi có bản vá khiến lỗ hổng biến thành zero-day.
- **Biện pháp phòng ngừa cần áp dụng:**
  - Sử dụng công cụ quét phụ thuộc tự động như GitHub Dependabot hoặc Snyk.
  - Bật tính năng Secret Scanning và push protection để chặn commit chứa thông tin xác thực.
  - Tích hợp công cụ phân tích tĩnh mã nguồn (SAST) như CodeQL vào luồng CI/CD.
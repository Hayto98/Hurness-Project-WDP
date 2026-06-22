# Slide Đề xuất Dự án

## Hệ thống Theo dõi Xu hướng Nghiên cứu Học thuật

- Tổng quan dự án: Trực quan hóa xu hướng nghiên cứu học thuật và xác định vùng trống nghiên cứu
- Mục đích: Hỗ trợ hoạt động nghiên cứu khách quan trong lĩnh vực thông tin quá tải
- Đối tượng: Nền tảng hỗ trợ toàn diện dành cho học sinh, nhà nghiên cứu và giáo viên

> **Ghi chú:** Dự án này cung cấp môi trường hỗ trợ nghiên cứu dựa trên dữ liệu bằng cách trích xuất xu hướng nghiên cứu từ khối lượng dữ liệu học thuật khổng lồ.

---

## Mục lục

- Tổng quan dự án
- Cấu trúc & Kiến trúc hệ thống
- Tính năng chính và Phương pháp phân tích
- Luồng nghiệp vụ và Cấu trúc màn hình
- Tóm tắt và Bước tiếp theo

---

## Tổng quan Dự án

- Bối cảnh: Khó khăn trong việc nắm bắt xu hướng và ra quyết định cảm tính do số lượng luận văn tăng nhanh
- Vấn đề: Các công cụ tìm kiếm truyền thống thiếu tính năng phân tích xu hướng và phát hiện Research Gap
- Giải pháp: Xây dựng "Research Corpus" tích hợp các nguồn dữ liệu học thuật đáng tin cậy (OpenAlex, arXiv, v.v.)
- Giá trị: Nâng cao hiệu quả lựa chọn chủ đề nghiên cứu, phát hiện lĩnh vực chưa khai thác, phân tích khách quan hoạt động nghiên cứu

---

## Tổng quan Cấu trúc Hệ thống


> **Ghi chú:** ※Cần xác nhận: Thiết kế khả dụng chi tiết như load balancer và cấu hình cache dự kiến được xác định trong giai đoạn phát triển

---

## Danh sách Tính năng Chính


---

## Luồng Nghiệp vụ: Phân tích & Hỗ trợ AI


> **Ghi chú:** ※Cung cấp tích hợp ba quy trình: Thu thập & Quản lý dữ liệu, Phân tích & Dashboard, Hỗ trợ AI.

---

## Cấu trúc Màn hình

- Trang chủ (Dashboard): Tổng quan xu hướng
- Tìm kiếm/Chi tiết Luận văn: Tóm tắt AI thông tin chi tiết
- Phân tích Research Gap: Trực quan hóa heatmap vùng trống
- Thư viện Cá nhân: Quản lý collection tài liệu

---

## Tóm tắt & Bước Tiếp theo

- Hoàn thành thiết kế nền tảng toàn bộ dự án
- Tiếp theo là lựa chọn chi tiết stack công nghệ (※Cần xác nhận)
- Bắt đầu kiểm chứng engine phân tích chính bằng prototype
- Xây dựng phương án đối phó với giới hạn API rate của nguồn dữ liệu bên ngoài

> **Ghi chú:** Vì lộ trình phát triển chưa được xác định, khuyến nghị sớm xác định các mốc tiến độ.

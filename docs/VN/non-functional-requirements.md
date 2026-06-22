# Danh sách Yêu cầu Phi chức năng

| id | category | requirement | targetValue | priority |
| --- | --- | --- | --- | --- |
| NFR-001 | Hiệu suất | Thời gian phản hồi tìm kiếm & phân tích | Hiển thị kết quả tìm kiếm và vẽ biểu đồ kết quả phân tích phải trong vòng 3 giây. ※Cần xác nhận: Giới hạn hiệu suất theo quy mô tập dữ liệu | Cao |
| NFR-002 | Khả dụng | Tỷ lệ hoạt động của hệ thống | 99.9% (ngoại trừ thời gian bảo trì) | Trung bình |
| NFR-003 | Bảo mật | Xác thực & Phân quyền | Xây dựng nền tảng xác thực an toàn sử dụng OAuth2.0/OpenID Connect. ※Cần xác nhận: Có hay không tích hợp SSO | Cao |
| NFR-004 | Vận hành | Giám sát xử lý batch thu thập dữ liệu | Gửi cảnh báo thông báo cho quản trị viên khi thu thập dữ liệu từ API bên ngoài thất bại. ※Cần xác nhận: Thời gian trễ thu thập được phép | Trung bình |
| NFR-005 | Di chuyển | Đáp ứng thêm/thay đổi nguồn dữ liệu | Cấu hình sao cho việc thêm cơ sở dữ liệu học thuật mới chỉ cần thay đổi cài đặt. ※Cần xác nhận: Kế hoạch thêm nguồn dữ liệu trong tương lai | Trung bình |
| NFR-006 | Mở rộng | Khả năng mở rộng (Scalability) | Thiết kế có thể mở rộng ngang để đáp ứng tải truy cập đồng thời khi số lượng người dùng nghiên cứu tăng (quy mô hàng nghìn đến hàng chục nghìn người). ※Cần xác nhận: Số lượng người dùng dự kiến | Cao |
| NFR-007 | Hiệu suất | Thời gian hoàn thành xử lý batch | Hoàn thành cập nhật tăng dần tất cả nguồn dữ liệu trong batch ban đêm trong vòng 8 giờ. ※Cần xác nhận: Tần suất cập nhật nguồn dữ liệu | Trung bình |
| NFR-008 | Bảo mật | Mã hóa truyền thông | Áp dụng TLS 1.3 trở lên cho tất cả truyền thông. | Cao |
| NFR-009 | Vận hành | Sao lưu & Phục hồi | Sao lưu cơ sở dữ liệu hàng ngày, mục tiêu phục hồi trong vòng RPO 24 giờ và RTO 4 giờ khi xảy ra sự cố. ※Cần xác nhận: Phạm vi mất dữ liệu được phép | Trung bình |
| NFR-010 | Bảo mật | Bảo vệ thông tin cá nhân khi sử dụng AI | Thực hiện xử lý masking để không bao gồm thông tin cá nhân người dùng hoặc nội dung nghiên cứu chưa công bố khi gửi truy vấn đến mô hình AI. ※Cần xác nhận: Chính sách học dữ liệu của LLM được sử dụng | Cao |

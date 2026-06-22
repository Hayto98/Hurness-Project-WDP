# Danh sách Yêu cầu Chức năng

| id | functionName | description | relatedRequirement | priority |
| --- | --- | --- | --- | --- |
| FR-001 | Xử lý Batch Thu thập Dữ liệu Bên ngoài | Chức năng tự động thực thi định kỳ lấy metadata luận văn từ API như OpenAlex và đưa vào Research Corpus. ※Cần xác nhận: Khoảng cách thực thi | BR-001, BR-004 | Cao |
| FR-002 | Tính năng Chuẩn hóa Dữ liệu Luận văn & Loại bỏ Trùng lặp | Xử lý backend chuyển đổi dữ liệu thu thập sang schema chung và tích hợp trùng lặp dựa trên DOI. | BR-002, BR-003 | Cao |
| FR-003 | Công cụ Tìm kiếm Luận văn | Cung cấp tính năng tìm kiếm theo từ khóa, tác giả, năm xuất bản, v.v. và lọc theo thuộc tính. | BR-009, BR-010 | Cao |
| FR-004 | Màn hình Hiển thị Chi tiết Luận văn | Tính năng màn hình hiển thị metadata luận văn kết quả tìm kiếm, tóm tắt và liên kết đến nguyên văn bên ngoài. | BR-013 | Cao |
| FR-005 | Engine Phân tích Xu hướng | Xử lý backend phân tích tính toán thống kê số lượng luận văn theo năm và tốc độ tăng trưởng. | BR-015, BR-016 | Cao |
| FR-006 | Xử lý Phát hiện Research Gap | Tính năng phân tích xác định và trình bày các vùng có tiềm năng tăng trưởng với ít luận văn. | BR-018 | Cao |
| FR-007 | Dashboard Trực quan hóa | Tính năng frontend hiển thị biểu đồ chuỗi thời gian xu hướng nghiên cứu, heatmap Research Gap, v.v. | BR-022, BR-023 | Cao |
| FR-008 | Tính năng Quản lý Thư viện Cá nhân | Tính năng CRUD để người dùng lưu luận văn, tạo thư mục và quản lý. | BR-027, BR-028 | Cao |
| FR-009 | Tính năng Tóm tắt & Hỗ trợ Luận văn bằng AI | Tính năng AI tự động tóm tắt luận văn, trình bày tài liệu liên quan và giải thích thuật ngữ chuyên ngành. ※Cần xác nhận: LLM API được sử dụng | BR-033, BR-034, BR-035 | Cao |
| FR-010 | Tính năng Thông báo Chủ đề Đã theo dõi | Tính năng thông báo cho người dùng khi có luận văn mới liên quan đến chủ đề quan tâm. ※Cần xác nhận: Kênh thông báo | BR-029, BR-030 | Trung bình |
| FR-011 | Tính năng Quản lý Người dùng | Tính năng đăng ký, đăng nhập, quản lý hồ sơ và kiểm soát quyền hạn bằng RBAC. | BR-038, BR-039 | Cao |
| FR-012 | Dashboard Quản trị | Tính năng màn hình dành cho quản trị viên để giám sát lỗi hệ thống, xem log và cấu hình nguồn dữ liệu. | BR-007, BR-040, BR-041 | Trung bình |
| FR-013 | Tính năng Xuất Báo cáo Phân tích | Tính năng xuất dữ liệu thống kê sang định dạng CSV, PDF, v.v. ※Cần xác nhận: Thông số định dạng | BR-025 | Trung bình |

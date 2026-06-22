# Kế hoạch Kiểm thử (Bản đồ)

## Kế hoạch Kiểm thử (Bản đồ)

| Nhóm Chức năng | Chức năng | Thao tác CRUD liên tiếp | Nhất quán giao dịch | Nhất quán dữ liệu liên quan | Kiểm soát độc quyền | Thao tác màn hình→Phản ánh kết quả | Liên kết validation đầu vào | Ngăn gửi trùng | Chuyển đổi màn hình | Nhất quán tham chiếu master | Liên động giữa chứng từ | Chuyển đổi trạng thái | Nhập file | Xuất biểu mẫu | Luồng nghiệp vụ bình thường | Luồng nghiệp vụ ngoại lệ | Thao tác theo quyền | Hiệu suất & Dữ liệu lớn | Thực thi đồng thời | Hành vi khi sự cố | Timeout & Retry | Xác thực & Phân quyền | Ngăn thao tác bất hợp pháp | Xuất log |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Quản lý Research Corpus | Thu thập & Quản lý Dữ liệu Bên ngoài | ○ | ○ | ○ | ○ | ○ | ○ | - | - | - | - | - | ○ | - | ○ | ○ | ○ | ○ | △ | ○ | ○ | ○ | ○ | ○ |
|  | Quản lý Dữ liệu Luận văn | ○ | ○ | ○ | ○ | - | - | - | - | ○ | - | - | - | - | ○ | ○ | - | ○ | ○ | ○ | - | - | - | ○ |
| Tìm kiếm & Phân tích Luận văn | Tìm kiếm & Tra cứu Chi tiết Luận văn | - | - | - | - | ○ | ○ | - | ○ | ○ | - | - | - | - | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ |
|  | Phân tích Xu hướng & Gap | - | - | - | - | ○ | ○ | - | ○ | ○ | - | - | - | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ |
| Quản lý Cá nhân hóa | Quản lý Thư viện Cá nhân | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | ○ | - | - | - | - | ○ | ○ | ○ | - | ○ | ○ | - | ○ | ○ | ○ |
| Nền tảng Hệ thống | Quản lý Người dùng & Hệ thống | ○ | ○ | - | ○ | ○ | ○ | ○ | ○ | - | - | ○ | - | - | ○ | ○ | ○ | - | ○ | ○ | ○ | ○ | ○ | ○ |

Chú thích: ○=Cần kiểm thử, △=Có điều kiện, -=Không áp dụng

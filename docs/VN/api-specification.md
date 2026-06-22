# Đặc tả API

| endpoint | method | description | requestBody | responseBody | auth |
| --- | --- | --- | --- | --- | --- |
| /api/auth/login | POST | Xác thực bằng địa chỉ email và mật khẩu, sau đó cấp phát access token. | [object Object] |  | [object Object] |
| /api/papers | GET | Tìm kiếm luận văn theo từ khóa hoặc thuộc tính. |  |  | [object Object] |
| /api/papers/{id} | GET | Lấy thông tin chi tiết của một luận văn. |  |  | [object Object] |
| /api/papers/{id}/summary | GET | Lấy bản tóm tắt luận văn do AI tạo ra. ※Cần xác nhận: LLM API được sử dụng |  |  | [object Object] |
| /api/analysis/trends | GET | Lấy thống kê số lượng luận văn theo từng năm. |  |  | [object Object] |
| /api/libraries/papers | POST | Thêm luận văn vào thư viện cá nhân. | [object Object] |  | [object Object] |
| /api/admin/sources | POST | Kích hoạt batch thu thập luận văn từ nguồn bên ngoài. ※Cần xác nhận: Cấu hình phân quyền | [object Object] |  | [object Object] |

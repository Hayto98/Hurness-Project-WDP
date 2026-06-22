# Quan điểm Kiểm thử Tích hợp

## Cây Quan điểm Kiểm thử

### ITV-L1-001 Tích hợp DB

#### ITV-L2-001 Kết hợp & Tìm kiếm SQL (Tìm kiếm & Quản lý Luận văn / Quản lý Thư viện Cá nhân)

- **ITV-L3-001 Kiểm tra tìm kiếm & lấy dữ liệu Research Corpus** [Cao]
  - Kết quả tìm kiếm theo từ khóa và năm xuất bản từ bảng research_papers được lấy đúng theo đặc tả
  - Khi không có bản ghi tìm kiếm mục tiêu, trả về mảng rỗng
  - Khi lấy chi tiết, dữ liệu luận văn với ID được chỉ định được lấy chính xác

#### ITV-L2-002 Kiểm soát Giao dịch (Quản lý Thư viện Cá nhân)

- **ITV-L3-002 Tính nhất quán của xử lý lưu thư viện người dùng** [Cao]
  - Transaction được commit bình thường khi lưu vào bảng user_libraries
  - Khi vi phạm ràng buộc lưu trùng lặp, rollback được thực hiện và dữ liệu được bảo vệ
  - Tính nhất quán giữa user_id và paper_id được duy trì

### ITV-L1-002 Tích hợp API

#### ITV-L2-003 Hành vi theo HTTP Method (Đăng nhập người dùng / Thực thi thu thập dữ liệu bên ngoài)

- **ITV-L3-003 Kiểm tra kết quả thực thi xác thực và thao tác quản lý** [Cao]
  - POST /api/auth/login trả về access token với thông tin xác thực hợp lệ
  - POST /api/admin/sources kích hoạt batch thu thập dữ liệu bên ngoài bình thường
  - API quản trị bị từ chối đúng cách khi quyền hạn không đủ

#### ITV-L2-004 Tham số Bất hợp lệ (Tìm kiếm Luận văn / Tóm tắt Luận văn AI)

- **ITV-L3-004 Kiểm tra phản hồi khi lỗi validation** [Trung bình]
  - Khi chỉ định ID luận văn không hợp lệ, trả về lỗi 404 hoặc mã lỗi đúng
  - Khi điều kiện tìm kiếm không xác định hoặc định dạng sai, xảy ra lỗi validation đúng cách
  - Khi thiếu trường bắt buộc, HTTP status được kiểm soát đúng cách

#### ITV-L2-006 Liên kết Xác thực (Tất cả API endpoint)

- **ITV-L3-006 Kiểm tra hành vi khi xác thực thất bại** [Cao]
  - Khi gọi API cần xác thực ở trạng thái chưa xác thực, trả về 401 Unauthorized
  - Khi chỉ định access token không hợp lệ, trả về 401 Unauthorized
  - Khi chỉ định access token đã hết hạn, trả về 401 Unauthorized

#### ITV-L2-007 Liên kết Phân quyền (Tất cả API endpoint)

- **ITV-L3-007 Kiểm tra kiểm soát quyền hạn** [Cao]
  - Khi người dùng thông thường gọi API cần quyền admin, trả về 403 Forbidden
  - Khi phạm vi quyền của token không đủ, trả về mã lỗi phù hợp
  - Dựa trên RBAC, thao tác ngoài quyền hạn của người dùng bị chặn

#### ITV-L2-008 Xử lý Lỗi Chung (Tất cả API endpoint)

- **ITV-L3-008 Kiểm tra lỗi hệ thống & kiểm soát truyền thông** [Trung bình]
  - Khi xảy ra exception bất ngờ trong xử lý backend, trả về 500 Internal Server Error
  - Khi xử lý timeout, trả về error response phù hợp
  - Khi truy cập vượt quá giới hạn rate (Rate Limit), trả về 429 Too Many Requests

### ITV-L1-003 Tích hợp Màn hình ⇄ Server

#### ITV-L2-005 Thao tác Màn hình → Phản ánh Kết quả (Tìm kiếm Luận văn / Phân tích Xu hướng)

- **ITV-L3-005 Tính nhất quán hiển thị màn hình của điều kiện tìm kiếm & kết quả phân tích** [Cao]
  - Năm xuất bản và từ khóa được chỉ định ở frontend được phản ánh trong kết quả tìm kiếm phía server
  - Dữ liệu phân tích xu hướng được ánh xạ đúng cách để hiển thị dưới dạng biểu đồ
  - Kết quả tóm tắt luận văn AI được render đúng cách trên màn hình chi tiết

## Ma trận Phủ sóng

| Đối tượng Kiểm thử | Tích hợp DB | Tích hợp API | Tích hợp Màn hình ⇄ Server |
| --- | --- | --- | --- |
| POST /api/auth/login | ○ | ○ | ○ |
| GET /api/papers | ○ | ○ | ○ |
| GET /api/papers/{id} | ○ | ○ | ○ |
| GET /api/papers/{id}/summary | - | ○ | ○ |
| GET /api/analysis/trends | ○ | ○ | ○ |
| POST /api/libraries/papers | ○ | ○ | ○ |
| POST /api/admin/sources | ○ | ○ | - |
| /api/auth/login | - | ○ | - |
| /api/papers | - | ○ | - |
| /api/papers/{id} | - | ○ | - |
| /api/papers/{id}/summary | - | ○ | - |
| /api/analysis/trends | - | ○ | - |
| /api/libraries/papers | - | ○ | - |
| /api/admin/sources | - | ○ | - |

Chú thích: ○=Cần kiểm thử, △=Có điều kiện, -=Không áp dụng

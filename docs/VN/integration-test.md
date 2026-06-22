# Kiểm thử Tích hợp

| id | testCase | precondition | steps | expectedResult | relatedFunction |
| --- | --- | --- | --- | --- | --- |
| IT-001 | Kiểm tra hành vi khi xác thực thất bại (token không hợp lệ) | Tài khoản người dùng hợp lệ tồn tại, nhưng sử dụng access token không hợp lệ trong lúc kiểm thử | [object Object] | Lỗi xác thực được trả về đúng cách và quyền truy cập hệ thống bị từ chối |  |
| IT-002 | Kiểm tra kiểm soát phân quyền (thực thi chức năng quản trị với quyền người dùng thông thường) | Người dùng đăng nhập chỉ có quyền "user" | [object Object] | Thao tác bị từ chối do không đủ quyền hạn |  |
| IT-003 | Tính nhất quán giao dịch của quá trình lưu thư viện người dùng | Kết nối cơ sở dữ liệu đã được thiết lập | [object Object] | Dữ liệu được lưu thành công và tính nhất quán trên DB được duy trì |  |
| IT-004 | Tính nhất quán hiển thị màn hình sau khi nhập điều kiện tìm kiếm | Dữ liệu cần tìm kiếm tồn tại trong bảng research_papers | [object Object] | Server phản hồi đúng với yêu cầu tìm kiếm và màn hình hiển thị được cập nhật |  |
| IT-005 | Xử lý lỗi chung khi xảy ra lỗi hệ thống | Mô phỏng tình huống kết nối cơ sở dữ liệu phân tích bị timeout | [object Object] | Lỗi hệ thống được xử lý đúng cách và hiển thị cho người dùng theo cách dễ hiểu |  |

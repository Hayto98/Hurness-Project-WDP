# Kiểm thử Hệ thống

| id | testCase | scenario | steps | expectedResult | priority |
| --- | --- | --- | --- | --- | --- |
| ST-001 | Xác nhận xuất log khi thực thi batch thu thập dữ liệu bên ngoài | Sau khi quản trị viên thực thi thu thập từ nguồn dữ liệu bên ngoài, xác nhận log sử dụng hệ thống được ghi lại đúng cách. | [object Object] | Thời gian bắt đầu, thời gian kết thúc, số lượng bản ghi thu thập được và tên nguồn kết nối được ghi lại nhất quán trong file log hoặc màn hình quản trị | Trung bình |
| ST-002 | Xác nhận tạo báo cáo định kỳ bởi engine phân tích | Xác nhận engine phân tích thực hiện xử lý tổng hợp ở backend và báo cáo phân tích được tự động tạo và cập nhật. | [object Object],[object Object] | Engine phân tích tính toán lại tốc độ tăng trưởng và vùng trống dựa trên dữ liệu mới nhất, và thông tin trực quan hóa trên dashboard được tự động cập nhật | Trung bình |

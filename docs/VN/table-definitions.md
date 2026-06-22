# Định nghĩa Bảng

| tableName | columnName | dataType | nullable | primaryKey | description |
| --- | --- | --- | --- | --- | --- |
| users | user_id | UUID | NO | YES | ID người dùng |
| users | username | VARCHAR(255) | NO | NO | Tên người dùng |
| users | email | VARCHAR(255) | NO | NO | Địa chỉ email |
| users | password_hash | VARCHAR(255) | NO | NO | Giá trị hash mật khẩu |
| users | role | VARCHAR(50) | NO | NO | Quyền người dùng (student, faculty, researcher, admin) ※Cần xác nhận |
| research_papers | paper_id | UUID | NO | YES | ID luận văn |
| research_papers | title | TEXT | NO | NO | Tiêu đề luận văn |
| research_papers | abstract | TEXT | YES | NO | Tóm tắt |
| research_papers | doi | VARCHAR(255) | YES | NO | DOI (Digital Object Identifier) |
| research_papers | publish_year | INTEGER | NO | NO | Năm xuất bản |
| research_papers | source_name | VARCHAR(255) | YES | NO | Tên nền tảng học thuật nguồn |
| user_libraries | library_id | UUID | NO | YES | ID thư viện |
| user_libraries | user_id | UUID | NO | NO | ID người dùng (FK) |
| user_libraries | paper_id | UUID | NO | NO | ID luận văn (FK) |
| user_libraries | folder_name | VARCHAR(255) | YES | NO | Tên thư mục ※Cần xác nhận |
| topic_subscriptions | subscription_id | UUID | NO | YES | ID đăng ký |
| topic_subscriptions | user_id | UUID | NO | NO | ID người dùng (FK) |
| topic_subscriptions | keyword | VARCHAR(255) | NO | NO | Từ khóa đang theo dõi |
| analysis_logs | log_id | UUID | NO | YES | ID log |
| analysis_logs | analysis_type | VARCHAR(100) | NO | NO | Loại phân tích (Xu hướng, Gap, v.v.) ※Chưa xác định |
| analysis_logs | execution_date | TIMESTAMP | NO | NO | Ngày giờ thực thi |

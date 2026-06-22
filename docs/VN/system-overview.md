# Tổng quan Hệ thống

```json
{
  "sections": [
    {
      "title": "Tổng quan Cấu trúc Hệ thống",
      "content": "Hệ thống này là ứng dụng Web bao gồm: tầng thu thập dữ liệu từ API học thuật bên ngoài và tích hợp, tầng nền tảng dữ liệu lưu trữ research corpus, tầng phân tích thực hiện phân tích xu hướng và suy luận AI, và tầng trình bày cung cấp giao diện người dùng. ※Cần xác nhận: Quyết định về cấu trúc microservice hay monolith chưa được xác định."
    },
    {
      "title": "Stack Công nghệ Sử dụng",
      "content": "Frontend: React.js hoặc Next.js (sử dụng D3.js hoặc Chart.js để trực quan hóa dashboard), Backend: Python (FastAPI hoặc Django), Cơ sở dữ liệu: PostgreSQL (dữ liệu có cấu trúc), Elasticsearch (tìm kiếm toàn văn tốc độ cao), Vector Database (dữ liệu liên quan AI), Nền tảng phân tích: Pandas, Scikit-learn, Tích hợp LLM: OpenAI API hoặc LLM nguồn mở. ※Cần xác nhận: Lựa chọn công nghệ chi tiết và phiên bản."
    },
    {
      "title": "Tích hợp Liên hệ thống",
      "content": "Lấy metadata dạng JSON/XML từ các API của OpenAlex, Semantic Scholar, Crossref, arXiv, IEEE Xplore, ACM Digital Library. ※Cần xác nhận: Phương án đối phó giới hạn API rate và có hay không cần tích hợp xác thực OAuth với nguồn bên ngoài."
    },
    {
      "title": "Tổng quan Luồng Dữ liệu",
      "content": "Lấy dữ liệu từ API bên ngoài (xử lý batch) → Trích xuất, chuẩn hóa (xử lý ETL) → Lưu vào Research Corpus (DB) → Đánh chỉ mục vào full-text search index và vector DB → Tính toán bởi engine phân tích và cung cấp kết quả tìm kiếm theo yêu cầu người dùng."
    },
    {
      "title": "Tổng quan Bảo mật",
      "content": "Xác thực bằng OAuth 2.0 / OIDC, mã hóa truyền thông bằng TLS 1.3, kiểm soát truy cập dựa trên vai trò (RBAC), thực hiện xử lý masking thông tin cá nhân khi tích hợp AI. ※Cần xác nhận: Kế hoạch chẩn đoán lỗ hổng bảo mật và phạm vi mã hóa dữ liệu."
    },
    {
      "title": "Tổng quan Cơ sở hạ tầng",
      "content": "Áp dụng kiến trúc dựa trên container trên môi trường cloud (AWS hoặc GCP), dự kiến orchestration bằng Kubernetes. Cấu hình horizontal scaling theo biến động tải. ※Cần xác nhận: Chi tiết pipeline CI/CD, yêu cầu cụ thể về thiết kế khả dụng (cấu hình dự phòng)."
    }
  ]
}
```

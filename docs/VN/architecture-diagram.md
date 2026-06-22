# Sơ đồ Kiến trúc

Hệ thống tích hợp với API học thuật bên ngoài, cung cấp phân tích xu hướng nghiên cứu và trực quan hóa có hỗ trợ AI.

**Tầng Client:**
- Người dùng
- Web Dashboard [React/Next.js]

**Tầng Gateway:**
- Load Balancer [AWS ALB ※Cần xác nhận]

**Tầng Application:**
- API Server [Python (FastAPI)]
- Worker Thu thập/Phân tích Dữ liệu [Python (Celery)]
- Engine Phân tích AI [OpenAI API / LangChain]

**Tầng Dữ liệu:**
- DB Metadata Nghiên cứu [PostgreSQL]
- Index Tìm kiếm [Elasticsearch]
- Vector DB [Pinecone/Milvus ※Cần xác nhận]
- Message Queue [Redis/RabbitMQ ※Cần xác nhận]

**Tầng Dịch vụ Bên ngoài:**
- Nguồn Thông tin Học thuật [OpenAlex, arXiv, IEEE, v.v.]

**Kết nối:**
- Người dùng → Web Dashboard (HTTPS)
- Web Dashboard → Load Balancer (HTTPS)
- Load Balancer → API Server (HTTP)
- API Server → DB Metadata Nghiên cứu (SQL)
- API Server → Index Tìm kiếm (REST/gRPC)
- API Server → Vector DB (REST)
- API Server → Engine Phân tích AI (HTTPS)
- API Server → Message Queue (Pub/Sub)
- Message Queue → Worker Thu thập/Phân tích Dữ liệu (Internal)
- Worker Thu thập/Phân tích Dữ liệu → Nguồn Thông tin Học thuật (HTTPS)
- Worker Thu thập/Phân tích Dữ liệu → DB Metadata Nghiên cứu (SQL)
- Worker Thu thập/Phân tích Dữ liệu → Index Tìm kiếm (REST)

```mermaid
flowchart TD
    subgraph client["Tầng Client"]
        user["Người dùng"]
        spa["Web Dashboard (React/Next.js)"]
    end
    subgraph gateway["Tầng Gateway"]
        alb["Load Balancer (AWS ALB ※Cần xác nhận)"]
    end
    subgraph application["Tầng Application"]
        api_srv["API Server (Python (FastAPI))"]
        worker["Worker Thu thập/Phân tích Dữ liệu (Python (Celery))"]
        llm["Engine Phân tích AI (OpenAI API / LangChain)"]
    end
    subgraph data["Tầng Dữ liệu"]
        db_rdb[("DB Metadata Nghiên cứu (PostgreSQL)")]
        db_search[("Index Tìm kiếm (Elasticsearch)")]
        db_vector[("Vector DB (Pinecone/Milvus ※Cần xác nhận)")]
        queue["Message Queue (Redis/RabbitMQ ※Cần xác nhận)"]
    end
    subgraph external["Tầng Dịch vụ Bên ngoài"]
        academic_apis["Nguồn Thông tin Học thuật (OpenAlex, arXiv, IEEE, v.v.)"]
    end
    user -->|"HTTPS"|spa
    spa -->|"HTTPS"|alb
    alb -->|"HTTP"|api_srv
    api_srv -->|"SQL"|db_rdb
    api_srv -->|"REST/gRPC"|db_search
    api_srv -->|"REST"|db_vector
    api_srv -->|"Tóm tắt/Phân tích luận văn"|llm
    api_srv -->|"Pub/Sub"|queue
    queue -->|"Internal"|worker
    worker -.->|"Thu thập dữ liệu"|academic_apis
    worker -.->|"SQL"|db_rdb
    worker -.->|"REST"|db_search
```

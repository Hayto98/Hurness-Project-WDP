# 📋 Task Breakdown — Scientific Research Trend Tracking System

> **DB:** MongoDB | Dựa trên: `docs/VN/` + Schema thực tế từ ER Diagram

---

## 🗂️ Tổng quan Issues

| Loại | Số lượng |
|---|---|
| 🗄️ Database (MongoDB) | 1 |
| ⚙️ Feature (Backend logic) | 6 |
| 🎨 UI (Frontend screens) | 3 |
| 🔌 API | 1 |
| 🧪 Test | 1 |
| **Tổng** | **12** |

---

## 📦 SPRINT 1 — Nền tảng (Foundation)

> **Mục tiêu:** Dựng MongoDB collections, Auth system, Data pipeline.

---

### ISSUE-001 · `[Database]` Xây dựng MongoDB Collections ⭐ Priority: HIGH

**DB:** MongoDB — tất cả dùng `ObjectId` làm `_id`

| Sub-task | Collection | Các trường chính |
|---|---|---|
| 1.1 | `users` | _id, email (unique), password_hash, full_name, role (student\|lecturer\|researcher\|admin), institution, avatar_url, is_active, last_login_at, created_at, updated_at |
| 1.2 | `papers` | _id, doi (unique sparse), external_ids {openalex_id, s2_id, arxiv_id, ieee_id, acm}, title (text idx), abstract (text idx), keywords[], authors[] {name, affiliation, orcid}, publication_year (idx), source_venue {name, type, issn}, data_source (openalex\|s2\|crossref\|arxiv\|ieee\|acm), fields_of_study[], citation_count, open_access_url, fetched_at, updated_at |
| 1.3 | `topics` | _id, name (unique), aliases[], parent_topic_id → topics (self-ref hierarchy), field_of_study (idx), trend_data[] {year, paper_count, delta_pct}, growth_rate (% YoY), saturation_score (0–1), gap_score (Research Gap score), related_topic_ids[], computed_at — ⚡ *pre-computed phục vụ FR-005, FR-006, FR-007* |
| 1.4 | `user_libraries` | _id, user_id → users, collection_name, description, paper_ids[] → papers[], tags[], is_default, created_at, updated_at — *compound unique index (user_id, collection_name)* |
| 1.5 | `topic_subscriptions` | _id, user_id → users, topic_id → topics, keyword (nullable, free-form), notify_frequency (instant\|daily\|weekly), last_notified_at, is_active, created_at — *compound unique index (user_id, topic_id)* |
| 1.6 | `search_history` | _id, user_id → users, query_text, filters {year_range, authors, field, source}, result_count, clicked_paper_ids[] → papers[], searched_at — *TTL index 90 ngày* |
| 1.7 | `data_sources` | _id, name (openalex\|s2\|crossref\|arxiv\|ieee\|acm), base_url, api_key_ref (vault ref — không lưu raw key), is_active, rate_limit_rpm, last_sync_at, last_sync_status (success\|failed\|running), sync_stats {total_fetched, new_added, updated}, config {fields_filter, year_from}, updated_at |
| 1.8 | `audit_logs` | _id, actor_id → users, actor_role (denormalized snapshot), action (`PAPER_FETCH` \| `USER_BAN` \| `USER_PROMOTE` \| `SOURCE_TOGGLE` \| `LIBRARY_SAVE` \| `SUBSCRIPTION_ADD` \| `EXPORT_REPORT`), target_type (`paper`\|`user`\|`source`\|`library`), target_id, payload {before, after}, ip_address, occurred_at — *TTL 2 năm, insert-only (không update/delete)* |
| 1.9 | Thiết lập: text index (title, abstract), compound index, TTL index | — |

**Blocked by:** Không có

---

### ISSUE-002 · `[Feature]` Xác thực & Quản lý Người dùng ⭐ Priority: HIGH

**Màn hình:** SCR-007 (`/auth`) | **FR:** FR-011

| Sub-task | Nội dung | Layer |
|---|---|---|
| 2.1 | API `POST /api/auth/register` — tạo user, role mặc định `student` | Backend |
| 2.2 | API `POST /api/auth/login` — xác thực email/password, cấp JWT | Backend |
| 2.3 | API `POST /api/auth/logout` — huỷ token | Backend |
| 2.4 | Middleware RBAC — 4 role: student / lecturer / researcher / admin | Backend |
| 2.5 | API `GET /api/users/me` — xem hồ sơ (full_name, institution, avatar_url) | Backend |
| 2.6 | API `PUT /api/users/me` — cập nhật hồ sơ | Backend |
| 2.7 | Ghi `audit_logs` khi login/logout | Backend |
| 2.8 | Màn hình SCR-007: Form Login / Register | Frontend |
| 2.9 | Tích hợp JWT vào Axios interceptor | Frontend |

**Blocked by:** ISSUE-001

---

### ISSUE-003 · `[Feature]` ETL Data Pipeline ⭐ Priority: HIGH

**FR:** FR-001, FR-002

| Sub-task | Nội dung | Layer |
|---|---|---|
| 3.1 | ETL connector **OpenAlex API** → map vào schema `papers` | Backend |
| 3.2 | ETL connector **Semantic Scholar API** | Backend |
| 3.3 | ETL connector **Crossref API** | Backend |
| 3.4 | ETL connector **arXiv API** | Backend |
| 3.5 | ETL connector **IEEE Xplore** + **ACM Digital Library** | Backend |
| 3.6 | Logic deduplicate theo `doi` (unique sparse index) | Backend |
| 3.7 | Merge `external_ids` khi cùng paper từ nhiều nguồn | Backend |
| 3.8 | Cập nhật `data_sources.sync_stats` sau mỗi lần chạy | Backend |
| 3.9 | API `POST /api/admin/sources` — admin kích hoạt batch thủ công | Backend |
| 3.10 | Cron job / Celery — batch định kỳ tự động | Backend |
| 3.11 | Ghi `audit_logs` action `PAPER_FETCH` | Backend |

**Blocked by:** ISSUE-001

---

## 📦 SPRINT 2 — Core Features

> **Mục tiêu:** Search API, Paper Detail, Trend Analysis, AI Summary.

---

### ISSUE-004 · `[Feature]` Search API ⭐ Priority: HIGH

**Màn hình:** SCR-002, SCR-003 | **FR:** FR-003, FR-004

| Sub-task | Nội dung | Layer |
|---|---|---|
| 4.1 | API `GET /api/papers` — full-text search trên `title`, `abstract`, `keywords[]` | Backend |
| 4.2 | Filter: `publication_year`, `authors`, `fields_of_study`, `data_source` | Backend |
| 4.3 | API `GET /api/papers/{id}` — lấy chi tiết một paper | Backend |
| 4.4 | Pagination (limit + offset) | Backend |
| 4.5 | Ghi `search_history` mỗi lần user tìm kiếm (TTL 90d) | Backend |

**Blocked by:** ISSUE-003

---

### ISSUE-005 · `[UI]` Màn hình Tìm kiếm & Chi tiết Luận văn ⭐ Priority: HIGH

**Màn hình:** SCR-002 (`/search`), SCR-003 (`/papers/:id`)

| Sub-task | Nội dung | Layer |
|---|---|---|
| 5.1 | Component Search Bar (keyword + filter year / authors / fields_of_study / data_source) | Frontend |
| 5.2 | Component Results Table (title, authors, publication_year, data_source, citation_count) + phân trang | Frontend |
| 5.3 | Page SCR-002 — kết nối `GET /api/papers` | Frontend |
| 5.4 | Component Paper Info Card (title, abstract, authors, doi, open_access_url, fields_of_study) | Frontend |
| 5.5 | Component AI Summary Card (lazy-load khi click) | Frontend |
| 5.6 | Component Related Papers List | Frontend |
| 5.7 | Nút "Lưu vào thư viện" + dropdown chọn collection | Frontend |
| 5.8 | Page SCR-003 — kết nối `GET /api/papers/{id}` | Frontend |

**Blocked by:** ISSUE-004

---

### ISSUE-006 · `[Feature]` Trend Analysis + Research Gap + AI ⭐ Priority: HIGH

**FR:** FR-005, FR-006, FR-009

| Sub-task | Nội dung | Layer |
|---|---|---|
| 6.1 | API `GET /api/analysis/trends` — thống kê từ `topics.trend_data[]` | Backend |
| 6.2 | Engine tính `growth_rate` (% YoY) và `saturation_score` (0–1) cho từng topic | Backend |
| 6.3 | API `GET /api/analysis/gap` — lấy topics có `gap_score` cao | Backend |
| 6.4 | API `GET /api/papers/{id}/summary` — gọi LLM tóm tắt abstract | Backend |
| 6.5 | API `GET /api/papers/{id}/related` — vector similarity search | Backend |
| 6.6 | Scheduler định kỳ cập nhật `topics` (trend_data, growth_rate, gap_score, saturation_score) | Backend |

**Blocked by:** ISSUE-004

---

## 📦 SPRINT 3 — Visualization & Library

> **Mục tiêu:** Dashboard biểu đồ, Heatmap Research Gap, Thư viện cá nhân.

---

### ISSUE-007 · `[UI]` Dashboard & Visualization UI ⭐ Priority: HIGH

**Màn hình:** SCR-001 (`/dashboard`), SCR-004 (`/analysis/gap`)

| Sub-task | Nội dung | Layer |
|---|---|---|
| 7.1 | Setup thư viện chart (Recharts / Chart.js) | Frontend |
| 7.2 | Component Trend Chart — line/bar chart từ `topics.trend_data[]` | Frontend |
| 7.3 | Component Research Gap Card (top topics theo `gap_score`) | Frontend |
| 7.4 | Component Search Box trên Dashboard | Frontend |
| 7.5 | Page SCR-001 Dashboard — kết nối `GET /api/analysis/trends` | Frontend |
| 7.6 | Component Heatmap — `gap_score` của topics theo `fields_of_study` | Frontend |
| 7.7 | Component Filter Tabs — chọn `fields_of_study` | Frontend |
| 7.8 | Drill-down: click heatmap → SCR-002 (filter sẵn theo topic) | Frontend |
| 7.9 | Page SCR-004 Research Gap — kết nối `GET /api/analysis/gap` | Frontend |

**Blocked by:** ISSUE-006

---

### ISSUE-008 · `[Feature]` Thư viện Cá nhân ⭐ Priority: HIGH

**Màn hình:** SCR-005 (`/library`) | **FR:** FR-008

| Sub-task | Nội dung | Layer |
|---|---|---|
| 8.1 | API `GET /api/libraries` — danh sách collections của user | Backend |
| 8.2 | API `POST /api/libraries` — tạo collection mới | Backend |
| 8.3 | API `PUT /api/libraries/{id}` — đổi tên collection | Backend |
| 8.4 | API `DELETE /api/libraries/{id}` — xóa collection | Backend |
| 8.5 | API `POST /api/libraries/{id}/papers` — thêm paper_id vào `paper_ids[]` | Backend |
| 8.6 | API `DELETE /api/libraries/{id}/papers/{paperId}` — xóa paper khỏi collection | Backend |
| 8.7 | Component Collection List sidebar (is_default badge) | Frontend |
| 8.8 | Component Saved Papers Table + nút xóa + modal xác nhận | Frontend |
| 8.9 | Page SCR-005 — kết nối API thư viện | Frontend |

**Blocked by:** ISSUE-002, ISSUE-005

---

## 📦 SPRINT 4 — Admin & Notifications

> **Mục tiêu:** Admin dashboard, Topic subscriptions, Notification trigger.

---

### ISSUE-009 · `[API]` Admin Dashboard API ⭐ Priority: MEDIUM

**Màn hình:** SCR-008 (`/admin`) | **FR:** FR-012

| Sub-task | Nội dung | Layer |
|---|---|---|
| 9.1 | API `GET /api/admin/audit-logs` — xem `audit_logs` (filter action, actor, date) | Backend |
| 9.2 | API `GET /api/admin/users` — danh sách users | Backend |
| 9.3 | API `PUT /api/admin/users/{id}/role` — đổi role (ghi `audit_logs` action `USER_BAN`) | Backend |
| 9.4 | API `GET /api/admin/sources` — danh sách `data_sources` + trạng thái sync | Backend |
| 9.5 | API `PUT /api/admin/sources/{id}` — bật/tắt source (action `SOURCE_TOGGLE`) | Backend |
| 9.6 | Middleware bảo vệ: chỉ role `admin` vào được `/api/admin/*` | Backend |

**Blocked by:** ISSUE-001

---

### ISSUE-010 · `[Feature]` Theo dõi Chủ đề & Thông báo ⭐ Priority: MEDIUM

**Màn hình:** SCR-006 (`/settings/topics`) | **FR:** FR-010

| Sub-task | Nội dung | Layer |
|---|---|---|
| 10.1 | API `POST /api/subscriptions` — đăng ký `topic_id` hoặc free-form `keyword` | Backend |
| 10.2 | API `GET /api/subscriptions` — danh sách đang theo dõi | Backend |
| 10.3 | API `DELETE /api/subscriptions/{id}` — hủy theo dõi | Backend |
| 10.4 | API `PUT /api/subscriptions/{id}` — đổi `notify_frequency` (instant\|daily\|weekly) | Backend |
| 10.5 | Notification trigger: sau batch, check `topic_subscriptions` → gửi notify | Backend |
| 10.6 | Cập nhật `last_notified_at` sau mỗi lần gửi | Backend |
| 10.7 | Page SCR-006 — danh sách theo dõi + toggle `notify_frequency` | Frontend |

**Blocked by:** ISSUE-002

---

### ISSUE-011 · `[UI]` Màn hình Settings & Admin ⭐ Priority: MEDIUM

**Màn hình:** SCR-006 (`/settings/topics`), SCR-008 (`/admin`)

| Sub-task | Nội dung | Layer |
|---|---|---|
| 11.1 | Component Topic Search & Subscribe Form | Frontend |
| 11.2 | Component Subscription List + notify_frequency selector | Frontend |
| 11.3 | Page SCR-006 — kết nối API subscriptions | Frontend |
| 11.4 | Component Audit Log Viewer (filter action, actor, date) | Frontend |
| 11.5 | Component Data Sources Table (name, is_active, last_sync_status, sync_stats) | Frontend |
| 11.6 | Component User Management Table (role badge + change role) | Frontend |
| 11.7 | Page SCR-008 — layout Admin Dashboard | Frontend |

**Blocked by:** ISSUE-009, ISSUE-010

---

## 📦 SPRINT 5 — Testing & Polish

---

### ISSUE-012 · `[Test]` Kiểm thử Tích hợp ⭐ Priority: HIGH

| Sub-task | Nội dung |
|---|---|
| 12.1 | IT-001: Kiểm thử xác thực (login, JWT, 4 role) |
| 12.2 | IT-002: Kiểm thử phân quyền RBAC (student không vào `/admin`) |
| 12.3 | IT-003: Kiểm thử deduplicate DOI khi ETL chạy |
| 12.4 | IT-004: Kiểm thử TTL index (search_history 90 ngày, audit_logs 2 năm) |
| 12.5 | IT-005: End-to-end: tìm kiếm → xem chi tiết → lưu thư viện |
| 12.6 | ST-001: Load test search full-text MongoDB |
| 12.7 | ST-002: Security test RBAC admin endpoints |

**Blocked by:** ISSUE-002, ISSUE-003, ISSUE-004, ISSUE-006

---

## 🔗 Dependency Graph

```
ISSUE-001 (MongoDB Collections)
    ├── ISSUE-002 (Auth)          ──→ ISSUE-008 (Library)
    │                             ──→ ISSUE-010 (Subscriptions)
    ├── ISSUE-003 (ETL Pipeline)  ──→ ISSUE-004 (Search API)
    │                                     ├── ISSUE-005 (Search UI) ──→ ISSUE-008
    │                                     └── ISSUE-006 (AI/Trends) ──→ ISSUE-007 (Dashboard)
    └── ISSUE-009 (Admin API)    ──→ ISSUE-011 (Admin UI)
                                 ←── ISSUE-010 (Notifications)

ISSUE-012 (Test) ← phụ thuộc 002, 003, 004, 006
```

---

## 📊 Bảng Tổng hợp

| Issue | Tên Task | Type | Priority | Phụ thuộc | Sprint |
|---|---|---|---|---|---|
| ISSUE-001 | MongoDB 8 Collections + Index | database | 🔴 HIGH | — | 1 |
| ISSUE-002 | Auth & User Management (4 role) | feature | 🔴 HIGH | 001 | 1 |
| ISSUE-003 | ETL Pipeline 6 nguồn + dedup DOI | feature | 🔴 HIGH | 001 | 1 |
| ISSUE-004 | Search API + search_history (TTL) | feature | 🔴 HIGH | 003 | 2 |
| ISSUE-005 | Search & Paper Detail UI | ui | 🔴 HIGH | 004 | 2 |
| ISSUE-006 | Trend Engine + gap_score + AI | feature | 🔴 HIGH | 004 | 2 |
| ISSUE-007 | Dashboard + Heatmap UI | ui | 🔴 HIGH | 006 | 3 |
| ISSUE-008 | Library (paper_ids[] ref) | feature | 🔴 HIGH | 002, 005 | 3 |
| ISSUE-009 | Admin API (audit_logs + data_sources) | api | 🟡 MEDIUM | 001 | 4 |
| ISSUE-010 | Subscriptions + Notify trigger | feature | 🟡 MEDIUM | 002 | 4 |
| ISSUE-011 | Settings & Admin UI | ui | 🟡 MEDIUM | 009, 010 | 4 |
| ISSUE-012 | Integration + System Testing | test | 🔴 HIGH | 002,003,004,006 | 5 |

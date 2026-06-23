# 👥 Phân công Công việc — 5 Thành viên
## Hệ thống Theo dõi Xu hướng Nghiên cứu Khoa học

> **DB:** MongoDB | **Stack:** Node.js/Python + React/Next.js

---

## 🧑‍💼 Phân công Tổng quan

| Thành viên | Vai trò | Phụ trách chính |
|---|---|---|
| **Member 1** | Backend Lead / DB | MongoDB Schema + Auth + Admin API |
| **Member 2** | Backend Dev | ETL Data Pipeline (6 nguồn) + Library API |
| **Member 3** | Backend Dev + AI | Search API + Trend Engine + AI + Research Gap |
| **Member 4** | Frontend Lead | Dashboard + Trend Chart + Heatmap + Auth UI |
| **Member 5** | Frontend Dev | Search UI + Paper Detail + Library + Settings + Admin UI |

---

## 👤 MEMBER 1 — Backend Lead / Database

> **Phụ trách:** Nền tảng MongoDB, Auth, Admin

### 🗄️ ISSUE-001 — Xây dựng MongoDB Collections

- [ ] 1.1 Collection `users` — _id, email (unique), password_hash, full_name, role (student\|lecturer\|researcher\|admin), institution, avatar_url, is_active, last_login_at, timestamps
- [ ] 1.2 Collection `papers` — _id, doi (unique sparse), external_ids, title (text), abstract (text), keywords[], authors[], publication_year (idx), source_venue, data_source enum, fields_of_study[], citation_count, open_access_url, timestamps
- [ ] 1.3 Collection `topics` — _id, name (unique), aliases[], parent_topic_id, field_of_study (idx), trend_data[], growth_rate, saturation_score, gap_score, related_topic_ids[], computed_at
- [ ] 1.4 Collection `user_libraries` — _id, user_id, collection_name, description, paper_ids[], tags[], is_default, timestamps + compound unique index (user_id, collection_name)
- [ ] 1.5 Collection `topic_subscriptions` — _id, user_id, topic_id, keyword (nullable), notify_frequency (instant\|daily\|weekly), last_notified_at, is_active + compound unique index (user_id, topic_id)
- [ ] 1.6 Collection `search_history` — _id, user_id, query_text, filters, result_count, clicked_paper_ids[], searched_at **(TTL 90 ngày)**
- [ ] 1.7 Collection `data_sources` — _id, name, base_url, api_key_ref (vault), is_active, rate_limit_rpm, last_sync_at, last_sync_status, sync_stats, config
- [ ] 1.8 Collection `audit_logs` — _id, actor_id, actor_role, action, target_type, target_id, payload, ip_address, occurred_at **(TTL 2 năm, insert-only)**
- [ ] 1.9 Thiết lập text index (title, abstract), TTL index, compound index

### 🔐 ISSUE-002 — Auth & User Management (phần Backend)

- [ ] 2.1 API `POST /api/auth/register` — tạo user, role mặc định `student`
- [ ] 2.2 API `POST /api/auth/login` — xác thực email/password, cấp JWT
- [ ] 2.3 API `POST /api/auth/logout`
- [ ] 2.4 Middleware RBAC — 4 role: student / lecturer / researcher / admin
- [ ] 2.5 API `GET /api/users/me` + `PUT /api/users/me`
- [ ] 2.6 Ghi `audit_logs` khi login/logout

### 🛠️ ISSUE-009 — Admin Dashboard API

- [ ] 9.1 API `GET /api/admin/audit-logs` — xem audit logs (filter action, actor)
- [ ] 9.2 API `GET /api/admin/users` — danh sách users
- [ ] 9.3 API `PUT /api/admin/users/{id}/role` — đổi role (ghi audit log)
- [ ] 9.4 API `GET /api/admin/sources` — trạng thái data_sources
- [ ] 9.5 API `PUT /api/admin/sources/{id}` — bật/tắt source (`SOURCE_TOGGLE`)
- [ ] 9.6 Middleware bảo vệ `/api/admin/*` chỉ role `admin`

**⚠️ Làm trước tất cả — không bị block bởi ai**

---

## 👤 MEMBER 2 — Backend Developer (Data Pipeline)

> **Phụ trách:** ETL 6 nguồn dữ liệu + Thư viện cá nhân API

### ⚙️ ISSUE-003 — ETL Data Pipeline

- [ ] 3.1 ETL connector **OpenAlex API** → map vào schema `papers`
- [ ] 3.2 ETL connector **Semantic Scholar API**
- [ ] 3.3 ETL connector **Crossref API**
- [ ] 3.4 ETL connector **arXiv API**
- [ ] 3.5 ETL connector **IEEE Xplore** + **ACM Digital Library**
- [ ] 3.6 Logic deduplicate theo `doi` (unique sparse)
- [ ] 3.7 Cập nhật `external_ids` khi cùng paper từ nhiều nguồn
- [ ] 3.8 Cập nhật `data_sources.sync_stats` sau mỗi lần chạy
- [ ] 3.9 API `POST /api/admin/sources` — kích hoạt batch thủ công
- [ ] 3.10 Cron job / Celery — batch định kỳ tự động
- [ ] 3.11 Ghi `audit_logs` action `PAPER_FETCH`

### 📚 ISSUE-008 — Library API (phần Backend)

- [ ] 8.1 API `GET /api/libraries` — danh sách collections của user
- [ ] 8.2 API `POST /api/libraries` — tạo collection mới
- [ ] 8.3 API `PUT /api/libraries/{id}` — đổi tên
- [ ] 8.4 API `DELETE /api/libraries/{id}` — xóa collection
- [ ] 8.5 API `POST /api/libraries/{id}/papers` — thêm paper_id vào `paper_ids[]`
- [ ] 8.6 API `DELETE /api/libraries/{id}/papers/{paperId}` — xóa paper khỏi collection

**⚠️ Blocked by:** Member 1 hoàn thành ISSUE-001 (MongoDB Schema)

---

## 👤 MEMBER 3 — Backend Developer (AI & Search)

> **Phụ trách:** Search API, Trend Engine, AI Summary, Research Gap, Notifications

### 🔍 ISSUE-004 — Search API

- [ ] 4.1 API `GET /api/papers` — full-text search (title, abstract, keywords[])
- [ ] 4.2 Filter: publication_year, authors, fields_of_study, data_source
- [ ] 4.3 API `GET /api/papers/{id}` — chi tiết paper
- [ ] 4.4 Pagination (limit + offset)
- [ ] 4.5 Ghi `search_history` mỗi lần user tìm kiếm

### 📈 ISSUE-006 — Trend Engine + AI + Research Gap

- [ ] 6.1 API `GET /api/analysis/trends` — thống kê từ `topics.trend_data[]`
- [ ] 6.2 Engine tính `growth_rate` (% YoY), `saturation_score` (0-1) cho từng topic
- [ ] 6.3 API `GET /api/analysis/gap` — lấy topics có `gap_score` cao
- [ ] 6.4 API `GET /api/papers/{id}/summary` — gọi LLM tóm tắt abstract
- [ ] 6.5 API `GET /api/papers/{id}/related` — vector similarity search
- [ ] 6.6 Scheduler cập nhật `topics` định kỳ (trend_data, gap_score, saturation_score)

### 🔔 ISSUE-010 — Subscriptions & Notifications (phần Backend)

- [ ] 10.1 API `POST /api/subscriptions` — đăng ký topic_id hoặc free-form keyword
- [ ] 10.2 API `GET /api/subscriptions` — danh sách đang theo dõi
- [ ] 10.3 API `DELETE /api/subscriptions/{id}` — hủy theo dõi
- [ ] 10.4 API `PUT /api/subscriptions/{id}` — đổi notify_frequency
- [ ] 10.5 Notification trigger sau batch: check `topic_subscriptions` → gửi notify
- [ ] 10.6 Cập nhật `last_notified_at`

**⚠️ Blocked by:** Member 2 hoàn thành ISSUE-003 (ETL Pipeline)

---

## 👤 MEMBER 4 — Frontend Lead

> **Phụ trách:** Dashboard, Trend Chart, Heatmap Research Gap, Auth UI

### 📊 ISSUE-007 — Dashboard & Visualization UI

- [ ] 7.1 Setup thư viện chart (Recharts / Chart.js)
- [ ] 7.2 Component **Trend Chart** — line/bar chart từ `topics.trend_data`
- [ ] 7.3 Component **Research Gap Card** (top topics theo `gap_score`)
- [ ] 7.4 Component **Search Box** trên Dashboard
- [ ] 7.5 Page **SCR-001 Dashboard** — kết nối `GET /api/analysis/trends`
- [ ] 7.6 Component **Heatmap** — `gap_score` theo `fields_of_study`
- [ ] 7.7 Component **Filter Tabs** — chọn fields_of_study
- [ ] 7.8 Drill-down: click heatmap → SCR-002 filter sẵn theo topic
- [ ] 7.9 Page **SCR-004 Research Gap** — kết nối `GET /api/analysis/gap`

### 🔐 ISSUE-002 — Auth UI (phối hợp Member 1)

- [ ] 2.7 Màn hình **SCR-007**: Form Login / Register
- [ ] 2.8 Tích hợp JWT vào Axios interceptor

**⚠️ Blocked by:** Member 3 hoàn thành ISSUE-006 *(dùng mock data trước khi API sẵn)*

---

## 👤 MEMBER 5 — Frontend Developer

> **Phụ trách:** Search UI, Paper Detail, Library, Settings, Admin UI

### 🔍 ISSUE-005 — Search & Paper Detail UI

- [ ] 5.1 Component **Search Bar** (keyword + filter year/authors/field/source)
- [ ] 5.2 Component **Results Table** (title, authors, year, data_source, citation_count) + phân trang
- [ ] 5.3 Page **SCR-002 Search** — kết nối `GET /api/papers`
- [ ] 5.4 Component **Paper Info Card** (title, abstract, authors, doi, open_access_url, fields_of_study)
- [ ] 5.5 Component **AI Summary Card** (lazy-load)
- [ ] 5.6 Component **Related Papers List**
- [ ] 5.7 Nút "Lưu vào thư viện" + dropdown chọn collection
- [ ] 5.8 Page **SCR-003 Paper Detail** — kết nối `GET /api/papers/{id}`

### 📚 ISSUE-008 — Library UI

- [ ] 8.7 Component **Collection List** (sidebar, is_default badge)
- [ ] 8.8 Component **Saved Papers Table** + nút xóa + modal xác nhận
- [ ] 8.9 Page **SCR-005 Library** — kết nối API thư viện

### ⚙️ ISSUE-011 — Settings & Admin UI

- [ ] 11.1 Component Topic Search & Subscribe Form
- [ ] 11.2 Component Subscription List + notify_frequency selector
- [ ] 11.3 Page **SCR-006 Settings** — kết nối API subscriptions
- [ ] 11.4 Component **Audit Log Viewer** (filter action, actor, date)
- [ ] 11.5 Component **Data Sources Table** (name, is_active, last_sync_status, sync_stats)
- [ ] 11.6 Component **User Management Table** (role badge + change role)
- [ ] 11.7 Page **SCR-008 Admin Dashboard**

**⚠️ Blocked by:** Member 3 (ISSUE-004) + Member 1 (ISSUE-009) + Member 3 (ISSUE-010)

---

## 🗓️ Thứ tự làm việc theo tuần

```
Tuần 1–2 (Song song):
  M1 → MongoDB collections + index + Auth API
  M2 → ETL connectors (OpenAlex, arXiv trước)
  M4 → Setup project FE, routing, layout, mock data cho Dashboard
  M5 → Setup components base, Auth UI phối hợp M4

Tuần 3–4 (Song song):
  M1 → Admin API (audit_logs, data_sources)
  M2 → Hoàn thiện ETL 6 nguồn + Library API
  M3 → Search API + ghi search_history
  M4 → Dashboard chart (kết nối API thật)
  M5 → Search UI + Paper Detail UI

Tuần 5–6:
  M3 → Trend Engine + gap_score + AI Summary + Notifications
  M4 → Research Gap Heatmap (SCR-004)
  M5 → Library UI + Settings UI + Admin UI

Tuần 7:
  Tất cả → Integration Test + Bug Fix + Demo
```

---

## 📊 Bảng Tổng hợp Phân công

| ISSUE | Tên Task | Member | Sprint |
|---|---|---|---|
| ISSUE-001 | MongoDB 8 Collections + Index | **M1** | 1 |
| ISSUE-002 | Auth API + Auth UI | **M1** (BE) + **M4** (FE) | 1 |
| ISSUE-003 | ETL Pipeline 6 nguồn + dedup DOI | **M2** | 1 |
| ISSUE-004 | Search API + search_history | **M3** | 2 |
| ISSUE-005 | Search & Paper Detail UI | **M5** | 2 |
| ISSUE-006 | Trend Engine + gap_score + AI | **M3** | 2 |
| ISSUE-007 | Dashboard + Heatmap UI | **M4** | 3 |
| ISSUE-008 | Library API + Library UI | **M2** (BE) + **M5** (FE) | 3 |
| ISSUE-009 | Admin API (audit_logs + sources) | **M1** | 4 |
| ISSUE-010 | Subscriptions + Notify | **M3** (BE) + **M5** (FE) | 4 |
| ISSUE-011 | Settings & Admin UI | **M5** | 4 |
| ISSUE-012 | Integration + System Testing | **Tất cả** | 5 |

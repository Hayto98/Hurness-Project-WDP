# 📖 Hướng dẫn Làm Task — Từng Chức năng
> **Stack:** MongoDB + Node.js (Express) + React/Next.js | JWT Auth | RBAC 4 role

---

## ✅ Chức năng 1: DB Schema (ISSUE-001)

**Người làm:** Member 1 | **Làm trước tất cả**

### Bước thực hiện
1. Khởi tạo project Node.js + cài `mongoose`
2. Tạo thư mục `src/models/`
3. Tạo từng Mongoose Schema theo thứ tự (tránh circular dependency):

```
src/models/
  User.model.js
  Paper.model.js
  Topic.model.js
  UserLibrary.model.js
  TopicSubscription.model.js
  SearchHistory.model.js
  DataSource.model.js
  AuditLog.model.js
```

4. **Lưu ý quan trọng:**
   - `papers.doi`: dùng `sparse: true, unique: true` (nhiều paper không có DOI)
   - `search_history`: thêm `expires: 60*60*24*90` vào field `searched_at` (TTL 90 ngày)
   - `audit_logs`: thêm `expires: 60*60*24*365*2` vào `occurred_at` (TTL 2 năm)
   - `user_libraries`: compound index `{ user_id: 1, collection_name: 1 }` unique
   - `topic_subscriptions`: compound index `{ user_id: 1, topic_id: 1 }` unique

### Kiểm tra
- [ ] Chạy script seed để insert thử document vào từng collection
- [ ] Verify TTL index hoạt động (dùng `db.collection.getIndexes()`)
- [ ] Verify text index trên `papers.title` và `papers.abstract`

---

## ✅ Chức năng 2: Auth & User Management (ISSUE-002)

**Người làm:** Member 1 (BE) + Member 4 (FE) | **Blocked by:** ISSUE-001

### Backend — Bước thực hiện

1. Cài packages: `bcryptjs`, `jsonwebtoken`, `express-validator`
2. Tạo thư mục:
```
src/
  controllers/auth.controller.js
  controllers/user.controller.js
  middlewares/auth.middleware.js   ← verify JWT
  middlewares/rbac.middleware.js   ← check role
  routes/auth.routes.js
  routes/user.routes.js
```

3. Luồng **Register:**
   - Validate email + password
   - Hash password bằng `bcrypt.hash(password, 10)`
   - Insert vào `users`, role mặc định = `student`
   - Ghi `audit_logs` action `USER_REGISTER`

4. Luồng **Login:**
   - Tìm user theo email
   - `bcrypt.compare(password, hash)`
   - Ký JWT: `jwt.sign({ userId, role }, SECRET, { expiresIn: '7d' })`
   - Ghi `audit_logs` action `USER_LOGIN`

5. **RBAC Middleware:**
```js
// Dùng như sau trong route:
router.get('/admin/users', verifyToken, requireRole('admin'), handler)
// requireRole nhận 1 hoặc nhiều role: requireRole('admin', 'lecturer')
```

### Frontend — Bước thực hiện

1. Tạo page `/auth` với 2 tab: Login / Register
2. Lưu JWT vào `localStorage` sau login thành công
3. Tạo Axios instance với interceptor tự gắn `Authorization: Bearer <token>`
4. Redirect về `/dashboard` sau login

### Kiểm tra
- [ ] Đăng ký thành công → role = student
- [ ] Login sai password → 401
- [ ] Gọi API admin với token user → 403

---

## ✅ Chức năng 3: ETL Data Pipeline (ISSUE-003)

**Người làm:** Member 2 | **Blocked by:** ISSUE-001

### Bước thực hiện

1. Tạo thư mục:
```
src/
  services/etl/
    openalex.connector.js
    semanticscholar.connector.js
    crossref.connector.js
    arxiv.connector.js
    ieee.connector.js
    acm.connector.js
    etl.normalizer.js     ← chuẩn hóa về schema chung
    etl.deduplicator.js   ← dedup theo DOI
  jobs/
    paperSync.job.js      ← cron job
```

2. **Chuẩn hóa schema** — mỗi connector trả về object cùng format:
```js
{
  doi, title, abstract, keywords[],
  authors[{ name, affiliation, orcid }],
  publication_year, source_venue,
  data_source, fields_of_study[],
  citation_count, open_access_url,
  external_ids: { openalex_id, s2_id, ... }
}
```

3. **Dedup theo DOI:**
   - Dùng `Paper.findOneAndUpdate({ doi }, data, { upsert: true })`
   - Merge `external_ids` (không ghi đè, chỉ bổ sung field còn thiếu)

4. **Sau mỗi lần sync:** Cập nhật `data_sources.sync_stats` và `last_sync_status`

5. **Cron job** (dùng `node-cron`):
```js
cron.schedule('0 2 * * *', () => runPaperSync()) // 2AM mỗi ngày
```

6. **API trigger thủ công** (dành cho admin):
   - `POST /api/admin/sources/:sourceId/sync`

### Kiểm tra
- [ ] Chạy connector OpenAlex → papers được insert vào DB
- [ ] Chạy lại → paper trùng DOI không bị duplicate
- [ ] `data_sources.sync_stats.new_added` tăng đúng

---

## ✅ Chức năng 4: Search API (ISSUE-004)

**Người làm:** Member 3 | **Blocked by:** ISSUE-003

### Bước thực hiện

1. Tạo:
```
src/
  controllers/paper.controller.js
  routes/paper.routes.js
```

2. **API `GET /api/papers`** — search + filter:
```js
// Query params: q, year_from, year_to, authors, field, source, page, limit
const query = {}
if (q) query.$text = { $search: q }
if (field) query.fields_of_study = field
if (source) query.data_source = source
if (year_from || year_to) query.publication_year = { $gte, $lte }

Paper.find(query)
  .sort({ score: { $meta: 'textScore' } }) // sort theo relevance
  .skip((page-1)*limit).limit(limit)
```

3. **Ghi search_history** sau mỗi search (nếu user đã login):
```js
await SearchHistory.create({ user_id, query_text: q, filters, result_count })
```

4. **API `GET /api/papers/:id`** — lấy chi tiết 1 paper

### Kiểm tra
- [ ] Search `q=machine learning` → trả về papers có term này
- [ ] Filter `year_from=2020` → chỉ papers từ 2020
- [ ] Không có `q` → trả về tất cả (sort by publication_year desc)

---

## ✅ Chức năng 5: Search & Paper Detail UI (ISSUE-005)

**Người làm:** Member 5 | **Blocked by:** ISSUE-004

### Bước thực hiện

1. **Page `/search` (SCR-002):**
   - Search bar: input + bộ lọc (year range, field, source)
   - Gọi `GET /api/papers` khi submit, debounce 300ms khi type
   - Hiển thị Results Table: title, authors, year, source, citation_count
   - Pagination component

2. **Page `/papers/:id` (SCR-003):**
   - Gọi `GET /api/papers/:id` khi mount
   - Paper Info Card: title, abstract đầy đủ, authors, DOI, open_access_url
   - AI Summary Card: click nút → gọi `GET /api/papers/:id/summary` → hiển thị (lazy)
   - Related Papers: gọi `GET /api/papers/:id/related`
   - Nút "Lưu vào thư viện": mở dropdown chọn collection → `POST /api/libraries/:id/papers`

### Kiểm tra
- [ ] Search → kết quả hiển thị đúng
- [ ] Click vào paper → mở trang chi tiết
- [ ] Click AI Summary → hiện text tóm tắt (hoặc loading spinner)

---

## ✅ Chức năng 6: Trend Analysis + Research Gap + AI (ISSUE-006)

**Người làm:** Member 3 | **Blocked by:** ISSUE-004

### Bước thực hiện

1. Tạo:
```
src/
  controllers/analysis.controller.js
  services/trend.engine.js     ← tính growth_rate, saturation_score
  services/gap.engine.js       ← tính gap_score
  services/ai.service.js       ← gọi LLM API
  routes/analysis.routes.js
```

2. **Trend Engine** — tính pre-computed và lưu vào `topics`:
```js
// Aggregate papers theo năm cho từng topic
const trendData = await Paper.aggregate([
  { $match: { fields_of_study: topicName } },
  { $group: { _id: '$publication_year', count: { $sum: 1 } } },
  { $sort: { _id: 1 } }
])
// Tính growth_rate = (count_year - count_prev) / count_prev * 100
// Tính saturation_score dựa trên trend (nếu growth_rate giảm dần → cao)
// Tính gap_score = 1 - (count / max_count) → topic ít paper = gap cao
await Topic.findOneAndUpdate({ name: topicName }, { trend_data, growth_rate, saturation_score, gap_score, computed_at: new Date() })
```

3. **API `GET /api/analysis/trends`:**
   - Query params: `field`, `year_from`, `year_to`, `limit`
   - Lấy từ `topics.trend_data` (đã pre-computed, query nhanh)

4. **API `GET /api/analysis/gap`:**
   - Lấy topics sort theo `gap_score DESC`
   - Filter theo `field_of_study`

5. **AI Summary** (`GET /api/papers/:id/summary`):
   - Lấy abstract của paper
   - Gọi LLM API (OpenAI/Gemini): prompt = `"Tóm tắt bài báo sau trong 3-5 câu: {abstract}"`
   - Cache kết quả (tránh gọi lại)

6. **Scheduler** cập nhật `topics` định kỳ:
```js
cron.schedule('0 3 * * *', () => recomputeTopics()) // Chạy sau ETL sync
```

### Kiểm tra
- [ ] `GET /api/analysis/trends?field=AI` → trả về trend_data đúng
- [ ] `GET /api/analysis/gap` → topics có gap_score cao lên đầu
- [ ] AI summary trả về text tóm tắt tiếng Anh

---

## ✅ Chức năng 7: Dashboard & Visualization UI (ISSUE-007)

**Người làm:** Member 4 | **Blocked by:** ISSUE-006

### Bước thực hiện

1. Cài: `recharts` (hoặc `chart.js` + `react-chartjs-2`)

2. **Page `/dashboard` (SCR-001):**
   - Gọi `GET /api/analysis/trends` → vẽ **Line Chart** (x=năm, y=số bài)
   - Gọi `GET /api/analysis/gap` → hiển thị top 5 Research Gap Cards
   - Search box ở giữa → redirect sang `/search?q=...`

3. **Page `/analysis/gap` (SCR-004):**
   - Filter Tabs: chọn `field_of_study`
   - **Heatmap:** dùng `recharts` hoặc thư viện `react-heatmap-grid`
     - x-axis = năm, y-axis = topic, màu = số lượng bài báo
     - Màu nhạt = ít bài = Research Gap
   - Click ô heatmap → điều hướng `/search?field=X&topic=Y`

### Kiểm tra
- [ ] Dashboard load chart không lỗi với data thật
- [ ] Heatmap render đúng màu (đậm = nhiều, nhạt = ít)
- [ ] Click heatmap → sang search với filter đúng

---

## ✅ Chức năng 8: Thư viện Cá nhân (ISSUE-008)

**Người làm:** Member 2 (BE) + Member 5 (FE) | **Blocked by:** ISSUE-002, ISSUE-005

### Backend

```
GET    /api/libraries              → danh sách collections của user
POST   /api/libraries              → tạo collection { collection_name }
PUT    /api/libraries/:id          → đổi tên
DELETE /api/libraries/:id          → xóa collection
POST   /api/libraries/:id/papers   → thêm { paper_id } vào paper_ids[]
DELETE /api/libraries/:id/papers/:paperId → xóa paper_id khỏi mảng
```

- Dùng `$push` để thêm: `UserLibrary.findByIdAndUpdate(id, { $addToSet: { paper_ids: paperId } })`
- Dùng `$pull` để xóa: `{ $pull: { paper_ids: paperId } }`

### Frontend (Page `/library` — SCR-005)

- Sidebar: danh sách collections + nút tạo mới
- Main area: bảng papers trong collection đang chọn
- Nút xóa paper → modal xác nhận → gọi DELETE API

### Kiểm tra
- [ ] Tạo collection mới → xuất hiện trong sidebar
- [ ] Lưu paper từ SCR-003 → xuất hiện trong library
- [ ] Xóa paper → biến mất ngay (optimistic update)

---

## ✅ Chức năng 9: Admin API (ISSUE-009)

**Người làm:** Member 1 | **Blocked by:** ISSUE-001

### Bước thực hiện

1. Tất cả route dùng middleware: `verifyToken` + `requireRole('admin')`

```
GET  /api/admin/audit-logs           → xem audit_logs (filter: action, actor, date)
GET  /api/admin/users                → danh sách users (phân trang)
PUT  /api/admin/users/:id/role       → đổi role → ghi audit log USER_PROMOTE/USER_BAN
GET  /api/admin/sources              → danh sách data_sources + sync status
PUT  /api/admin/sources/:id          → bật/tắt source (is_active) → ghi SOURCE_TOGGLE
POST /api/admin/sources/:id/sync     → trigger batch thủ công
```

### Kiểm tra
- [ ] User role `student` gọi `/api/admin/*` → 403
- [ ] Admin đổi role → `audit_logs` ghi đúng action

---

## ✅ Chức năng 10: Subscriptions & Notifications (ISSUE-010)

**Người làm:** Member 3 (BE) + Member 5 (FE) | **Blocked by:** ISSUE-002

### Backend

```
POST   /api/subscriptions            → đăng ký { topic_id? OR keyword }
GET    /api/subscriptions            → danh sách của user
DELETE /api/subscriptions/:id        → hủy
PUT    /api/subscriptions/:id        → đổi notify_frequency
```

**Notification trigger** (chạy sau ETL sync):
```js
// Với mỗi paper mới được thêm vào
// 1. Lấy fields_of_study và keywords của paper
// 2. Tìm topic_subscriptions khớp topic hoặc keyword
// 3. Gửi notify tới user (email / in-app)
// 4. Cập nhật last_notified_at
```

### Frontend (Page `/settings/topics` — SCR-006)

- Form tìm topic hoặc nhập keyword tự do
- Danh sách đang theo dõi + dropdown đổi tần suất (instant/daily/weekly)
- Toggle is_active

---

## ✅ Chức năng 11 & 12: Admin UI + Testing (ISSUE-011, 012)

**Admin UI (Member 5):**
- Page `/admin` — 3 tab: Users, Data Sources, Audit Logs
- User table: hiển thị role badge, nút "Đổi role" → gọi API
- Data Sources table: toggle is_active, nút "Sync ngay"
- Audit Log viewer: filter theo action, actor, date range

**Testing (Tất cả — Sprint 5):**
- IT-001: test login đúng/sai → response code đúng
- IT-002: test RBAC — student vào `/admin` → 403
- IT-003: lưu paper → kiểm tra trong library
- IT-004: TTL index — insert document với `searched_at` quá hạn → tự xóa
- IT-005: E2E flow: search → detail → save → library

---

## 📁 Cấu trúc thư mục đề xuất

```
hurness-backend/
  src/
    models/          ← Mongoose schemas
    controllers/     ← Route handlers
    services/        ← Business logic (ETL, AI, Trend engine)
    middlewares/     ← Auth, RBAC
    routes/          ← Express routers
    jobs/            ← Cron jobs
    config/          ← DB connection, env
  .env

hurness-frontend/
  src/
    pages/           ← Next.js pages (dashboard, search, library, admin)
    components/      ← Reusable components (charts, tables, cards)
    services/        ← API calls (axios)
    hooks/           ← Custom hooks (useAuth, usePapers...)
    store/           ← State management (Zustand / Redux)
```

---

## ⚠️ Lưu ý chung

| Vấn đề | Giải pháp |
|---|---|
| API key nguồn dữ liệu | Lưu trong `.env`, không commit lên Git |
| Rate limit API ngoài | Implement delay/retry với `axios-retry` |
| AI Summary chậm | Cache kết quả LLM vào DB sau lần đầu gọi |
| Search chậm khi data lớn | Đảm bảo text index đã tạo trên `papers` |
| Topics stale data | Chạy `recomputeTopics()` sau mỗi ETL sync |

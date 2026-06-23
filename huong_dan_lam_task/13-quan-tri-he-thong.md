# 🛠️ Chức năng 13: Quản trị Hệ thống (Admin)

> **Loại:** Admin Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** ISSUE-009, ISSUE-011 | **FR:** FR-012
> **Màn hình:** SCR-008 (`/admin`)

---

## Mô tả

Dashboard admin quản lý: users, data sources, AI models, keyword dictionary, logs, backup.

---

## Backend API (tất cả yêu cầu role `admin`)

```
# User Management
GET    /api/admin/users                    → danh sách users (phân trang)
PUT    /api/admin/users/:id/role           → đổi role
PUT    /api/admin/users/:id/status         → ban/unban (is_active)

# Data Source Management
GET    /api/admin/sources                  → danh sách data_sources + sync status
PUT    /api/admin/sources/:id              → bật/tắt, sửa config
POST   /api/admin/sources/:id/sync        → trigger sync thủ công

# Audit Logs
GET    /api/admin/audit-logs?action=PAPER_FETCH&from=2025-01-01&limit=50

# System Stats
GET    /api/admin/system/stats             → tổng papers, users, disk usage...

# Keyword Dictionary (quản lý synonyms cho topics)
GET    /api/admin/keywords                 → danh sách keyword mappings
POST   /api/admin/keywords                 → thêm synonym
DELETE /api/admin/keywords/:id             → xóa

# Backup
POST   /api/admin/backup                   → trigger mongodump
GET    /api/admin/backups                   → danh sách backup files
```

### Middleware bảo vệ

```js
// routes/admin.routes.js
const router = require('express').Router();

// Tất cả route admin đều phải qua 2 middleware
router.use(verifyToken, requireRole('admin'));

router.get('/users', adminController.getUsers);
router.put('/users/:id/role', adminController.changeRole);
// ...
```

### Controller — Change Role

```js
async function changeRole(req, res) {
  const { id } = req.params;
  const { role } = req.body;

  const validRoles = ['student', 'lecturer', 'researcher', 'admin'];
  if (!validRoles.includes(role)) {
    return res.status(400).json({ error: 'Invalid role' });
  }

  const user = await User.findById(id);
  if (!user) return res.status(404).json({ error: 'User not found' });

  const oldRole = user.role;
  user.role = role;
  await user.save();

  // Ghi audit log
  await AuditLog.create({
    actor_id: req.userId,
    actor_role: req.userRole,
    action: role === 'admin' ? 'USER_PROMOTE' : 'USER_BAN',
    target_type: 'user',
    target_id: id,
    payload: { before: { role: oldRole }, after: { role } },
    ip_address: req.ip
  });

  res.json({ message: `Role changed: ${oldRole} → ${role}` });
}
```

### System Stats

```js
async function getSystemStats(req, res) {
  const [users, papers, topics, sources] = await Promise.all([
    User.countDocuments(),
    Paper.countDocuments(),
    Topic.countDocuments(),
    DataSource.find().select('name is_active last_sync_status last_sync_at sync_stats')
  ]);

  res.json({
    users, papers, topics,
    sources: sources.map(s => ({
      name: s.name,
      is_active: s.is_active,
      status: s.last_sync_status,
      last_sync: s.last_sync_at,
      total_fetched: s.sync_stats?.total_fetched || 0
    }))
  });
}
```

---

## Frontend — SCR-008

```jsx
function AdminDashboard() {
  const [tab, setTab] = useState('users');

  return (
    <div>
      <nav className="admin-tabs">
        <button onClick={() => setTab('users')}>👥 Users</button>
        <button onClick={() => setTab('sources')}>🔌 Data Sources</button>
        <button onClick={() => setTab('logs')}>📋 Audit Logs</button>
        <button onClick={() => setTab('system')}>⚙️ System</button>
      </nav>

      {tab === 'users' && <UserManagement />}
      {tab === 'sources' && <DataSourceManager />}
      {tab === 'logs' && <AuditLogViewer />}
      {tab === 'system' && <SystemStats />}
    </div>
  );
}

function UserManagement() {
  // Table: email, full_name, role (dropdown), is_active (toggle), last_login
  // Click role dropdown → PUT /api/admin/users/:id/role
}

function DataSourceManager() {
  // Table: name, is_active (toggle), last_sync_status (badge), sync_stats
  // Button "Sync Now" → POST /api/admin/sources/:id/sync
}

function AuditLogViewer() {
  // Table: occurred_at, actor, action, target, payload
  // Filter: action dropdown, date range picker
}
```

---

## Checklist kiểm tra

- [ ] Student gọi `/api/admin/*` → 403
- [ ] Admin đổi role user → audit_logs ghi đúng
- [ ] Bật/tắt data source → is_active cập nhật
- [ ] Trigger sync → last_sync_status = running → success/failed
- [ ] Audit logs filter theo action → đúng kết quả

# 🔔 Chức năng 9: Theo dõi Chủ đề (Research Watchlist)

> **Loại:** User Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** ISSUE-010 | **FR:** FR-010
> **Màn hình:** SCR-006 (`/settings/topics`)

---

## Mô tả

Người dùng theo dõi keyword, author, journal, conference, domain — nhận thông báo khi có bài mới.

---

## Backend

### API Endpoints

```
POST   /api/subscriptions             → đăng ký theo dõi
GET    /api/subscriptions             → danh sách đang theo dõi
PUT    /api/subscriptions/:id         → đổi notify_frequency
DELETE /api/subscriptions/:id         → hủy theo dõi
```

### Controller

```js
// controllers/subscription.controller.js

async function createSubscription(req, res) {
  const { topic_id, keyword, type, notify_frequency = 'daily' } = req.body;
  // type: 'keyword' | 'author' | 'journal' | 'conference' | 'domain'

  // Validate: phải có topic_id hoặc keyword
  if (!topic_id && !keyword) {
    return res.status(400).json({ error: 'topic_id hoặc keyword là bắt buộc' });
  }

  // Check trùng
  const existing = await TopicSubscription.findOne({
    user_id: req.userId,
    $or: [
      { topic_id, keyword: null },
      { keyword, topic_id: null }
    ]
  });
  if (existing) return res.status(409).json({ error: 'Đã theo dõi rồi' });

  const sub = await TopicSubscription.create({
    user_id: req.userId,
    topic_id: topic_id || null,
    keyword: keyword || null,
    notify_frequency,
    is_active: true
  });

  // Ghi audit log
  await AuditLog.create({
    actor_id: req.userId,
    actor_role: req.userRole,
    action: 'SUBSCRIPTION_ADD',
    target_type: 'subscription',
    target_id: sub._id,
    payload: { keyword: keyword || topic_id }
  });

  res.status(201).json(sub);
}
```

### Notification Trigger (chạy sau ETL sync)

```js
// services/notification.service.js

async function checkAndNotify() {
  // Lấy tất cả subscriptions active
  const subs = await TopicSubscription.find({ is_active: true })
    .populate('user_id', 'email full_name')
    .populate('topic_id', 'name');

  for (const sub of subs) {
    // Tìm papers mới từ lần notify cuối
    const since = sub.last_notified_at || sub.created_at;
    const searchTerm = sub.keyword || sub.topic_id?.name;

    const newPapers = await Paper.find({
      $text: { $search: searchTerm },
      fetched_at: { $gt: since }
    }).limit(10).select('title authors publication_year');

    if (newPapers.length > 0) {
      // Gửi notification (email hoặc in-app)
      await sendNotification(sub.user_id, {
        type: 'new_papers',
        keyword: searchTerm,
        papers: newPapers,
        count: newPapers.length
      });

      // Cập nhật last_notified_at
      await TopicSubscription.findByIdAndUpdate(sub._id, {
        last_notified_at: new Date()
      });
    }
  }
}

// Chạy theo tần suất
// instant: chạy ngay sau mỗi batch sync
// daily: cron 8AM mỗi ngày
// weekly: cron 8AM thứ 2
```

---

## Frontend — SCR-006

```jsx
function WatchlistPage() {
  const [subs, setSubs] = useState([]);
  const [newKeyword, setNewKeyword] = useState('');

  useEffect(() => {
    api.get('/api/subscriptions').then(r => setSubs(r.data));
  }, []);

  const handleFollow = async () => {
    const res = await api.post('/api/subscriptions', { keyword: newKeyword });
    setSubs([...subs, res.data]);
    setNewKeyword('');
  };

  return (
    <div>
      <h2>Research Watchlist</h2>

      {/* Form thêm mới */}
      <div className="add-form">
        <input value={newKeyword} onChange={e => setNewKeyword(e.target.value)}
               placeholder="Nhập keyword, author, journal..." />
        <button onClick={handleFollow}>➕ Follow</button>
      </div>

      {/* Danh sách đang theo dõi */}
      {subs.map(sub => (
        <div key={sub._id} className="watchlist-item">
          <span>{sub.keyword || sub.topic_id?.name}</span>
          <select value={sub.notify_frequency}
                  onChange={e => updateFrequency(sub._id, e.target.value)}>
            <option value="instant">Ngay lập tức</option>
            <option value="daily">Hàng ngày</option>
            <option value="weekly">Hàng tuần</option>
          </select>
          <button onClick={() => unfollow(sub._id)}>❌</button>
        </div>
      ))}
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Follow keyword "Mamba" → xuất hiện trong danh sách
- [ ] Đổi frequency → API PUT thành công
- [ ] Unfollow → biến mất khỏi danh sách
- [ ] Khi có paper mới khớp keyword → notification gửi đúng
- [ ] Duplicate follow → trả 409

# 📚 Chức năng 10: Quản lý Thư viện Cá nhân

> **Loại:** User Feature | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-008 | **FR:** FR-008
> **Màn hình:** SCR-005 (`/library`)

---

## Mô tả

Người dùng lưu paper, tạo folder/collection, bookmark, tag, ghi note.

```
My Library
  ├── Machine Learning (15 papers)
  │   ├── LLM (8)
  │   ├── Mamba (3)
  │   └── Vision (4)
  └── Robotics (6 papers)
```

---

## Backend API

```
GET    /api/libraries                          → danh sách collections
POST   /api/libraries                          → tạo collection { collection_name, description }
PUT    /api/libraries/:id                      → đổi tên, mô tả
DELETE /api/libraries/:id                      → xóa collection
GET    /api/libraries/:id/papers               → lấy papers trong collection (populate)
POST   /api/libraries/:id/papers               → thêm { paper_id }
DELETE /api/libraries/:id/papers/:paperId      → xóa paper khỏi collection
PUT    /api/libraries/:id/papers/:paperId/note → cập nhật note cho paper
```

### Controller

```js
// Thêm paper vào collection
async function addPaper(req, res) {
  const { id } = req.params;        // library id
  const { paper_id } = req.body;

  const library = await UserLibrary.findOne({ _id: id, user_id: req.userId });
  if (!library) return res.status(404).json({ error: 'Collection not found' });

  // Dùng $addToSet để tránh duplicate
  await UserLibrary.findByIdAndUpdate(id, {
    $addToSet: { paper_ids: paper_id }
  });

  await AuditLog.create({
    actor_id: req.userId, actor_role: req.userRole,
    action: 'LIBRARY_SAVE', target_type: 'library',
    target_id: id, payload: { paper_id }
  });

  res.json({ message: 'Paper added' });
}

// Lấy papers trong collection (populate đầy đủ)
async function getLibraryPapers(req, res) {
  const library = await UserLibrary.findOne({
    _id: req.params.id, user_id: req.userId
  });
  if (!library) return res.status(404).json({ error: 'Not found' });

  const papers = await Paper.find({ _id: { $in: library.paper_ids } })
    .select('title authors publication_year doi citation_count fields_of_study');

  res.json({ collection: library.collection_name, papers });
}
```

### Tự tạo collection mặc định khi user đăng ký

```js
// Sau khi register thành công
await UserLibrary.create({
  user_id: newUser._id,
  collection_name: 'My Papers',
  description: 'Default collection',
  paper_ids: [],
  is_default: true
});
```

---

## Frontend — SCR-005

```jsx
function LibraryPage() {
  const [collections, setCollections] = useState([]);
  const [selected, setSelected] = useState(null);
  const [papers, setPapers] = useState([]);

  useEffect(() => {
    api.get('/api/libraries').then(r => {
      setCollections(r.data);
      if (r.data.length > 0) selectCollection(r.data[0]._id);
    });
  }, []);

  const selectCollection = async (id) => {
    setSelected(id);
    const res = await api.get(`/api/libraries/${id}/papers`);
    setPapers(res.data.papers);
  };

  return (
    <div className="library-layout">
      {/* Sidebar */}
      <aside>
        <h3>Collections</h3>
        {collections.map(c => (
          <div key={c._id}
               className={c._id === selected ? 'active' : ''}
               onClick={() => selectCollection(c._id)}>
            📁 {c.collection_name}
            <span>({c.paper_ids.length})</span>
          </div>
        ))}
        <button onClick={createNewCollection}>➕ New Collection</button>
      </aside>

      {/* Main */}
      <main>
        <table>
          <thead><tr><th>Title</th><th>Authors</th><th>Year</th><th>Actions</th></tr></thead>
          <tbody>
            {papers.map(p => (
              <tr key={p._id}>
                <td><a href={`/papers/${p._id}`}>{p.title}</a></td>
                <td>{p.authors.map(a => a.name).join(', ')}</td>
                <td>{p.publication_year}</td>
                <td><button onClick={() => removePaper(p._id)}>🗑️</button></td>
              </tr>
            ))}
          </tbody>
        </table>
      </main>
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Đăng ký → tự có collection "My Papers" mặc định
- [ ] Tạo collection mới → xuất hiện trong sidebar
- [ ] Lưu paper → paper xuất hiện trong collection
- [ ] Lưu trùng → không duplicate (dùng `$addToSet`)
- [ ] Xóa paper → biến mất ngay
- [ ] Xóa collection → papers không bị xóa (chỉ xóa reference)

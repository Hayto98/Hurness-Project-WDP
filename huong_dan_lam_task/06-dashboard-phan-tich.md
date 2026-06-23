# 📊 Chức năng 6: Dashboard Phân tích

> **Loại:** Visualization | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-007 | **FR:** FR-007
> **Màn hình:** SCR-001 (`/dashboard`)

---

## Mô tả

Dashboard tổng quan hiển thị các thống kê và biểu đồ chính.

---

## Dữ liệu hiển thị

### Thống kê tổng quan (Cards)

| Metric | Query |
|---|---|
| Tổng số bài báo | `Paper.countDocuments()` |
| Số bài theo năm hiện tại | `Paper.countDocuments({ publication_year: 2025 })` |
| Tổng citations | `Paper.aggregate([{ $group: { _id: null, total: { $sum: '$citation_count' } } }])` |
| Số lĩnh vực | `Topic.countDocuments()` |

### Top Rankings

| Ranking | Query |
|---|---|
| Top tác giả | Aggregate `authors.name` → group → count → sort desc |
| Top trường (institution) | Aggregate `authors.affiliation` → group → count |
| Top journal | Aggregate `source_venue.name` where type=journal |
| Top conference | Aggregate `source_venue.name` where type=conference |
| Top keywords | Aggregate `keywords` → unwind → group → count |

---

## Backend API

```
GET /api/dashboard/stats              → tổng quan: total papers, citations...
GET /api/dashboard/top-authors?limit=10
GET /api/dashboard/top-institutions?limit=10
GET /api/dashboard/top-venues?limit=10&type=journal
GET /api/dashboard/top-keywords?limit=20
GET /api/dashboard/papers-by-year     → số bài theo từng năm
```

### Ví dụ controller

```js
async function getStats(req, res) {
  const [totalPapers, totalCitations, totalTopics, papersThisYear] = await Promise.all([
    Paper.countDocuments(),
    Paper.aggregate([{ $group: { _id: null, total: { $sum: '$citation_count' } } }]),
    Topic.countDocuments(),
    Paper.countDocuments({ publication_year: new Date().getFullYear() })
  ]);

  res.json({
    total_papers: totalPapers,
    total_citations: totalCitations[0]?.total || 0,
    total_topics: totalTopics,
    papers_this_year: papersThisYear
  });
}

async function getTopAuthors(req, res) {
  const limit = parseInt(req.query.limit) || 10;
  const result = await Paper.aggregate([
    { $unwind: '$authors' },
    { $group: { _id: '$authors.name', count: { $sum: 1 }, affiliation: { $first: '$authors.affiliation' } } },
    { $sort: { count: -1 } },
    { $limit: limit }
  ]);
  res.json(result);
}
```

---

## Frontend — Biểu đồ

### Thư viện: `recharts`

```
npm install recharts
```

### Các loại biểu đồ

| Biểu đồ | Dùng cho | Component Recharts |
|---|---|---|
| Line chart | Xu hướng theo năm | `<LineChart>` |
| Bar chart | Top authors, keywords | `<BarChart>` |
| Pie chart | Phân bổ theo lĩnh vực | `<PieChart>` |
| Heatmap | Research Gap | Custom hoặc `react-heatmap-grid` |
| Timeline | Papers theo thời gian | `<AreaChart>` |

### Layout Dashboard

```jsx
function Dashboard() {
  const [stats, setStats] = useState(null);

  useEffect(() => {
    api.get('/api/dashboard/stats').then(r => setStats(r.data));
  }, []);

  return (
    <div className="dashboard-grid">
      {/* Row 1: Stat Cards */}
      <StatCard title="Tổng bài báo" value={stats?.total_papers} icon="📄" />
      <StatCard title="Citations" value={stats?.total_citations} icon="📚" />
      <StatCard title="Lĩnh vực" value={stats?.total_topics} icon="🔬" />
      <StatCard title="Năm nay" value={stats?.papers_this_year} icon="📅" />

      {/* Row 2: Charts */}
      <TrendLineChart />      {/* Số bài theo năm */}
      <TopKeywordsBar />      {/* Top 10 keywords */}

      {/* Row 3 */}
      <FieldDistributionPie /> {/* Phân bổ lĩnh vực */}
      <TopAuthorsTable />      {/* Top tác giả */}
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Stats cards hiển thị số liệu đúng
- [ ] Line chart: số bài theo năm render đúng
- [ ] Bar chart: top keywords sort giảm dần
- [ ] Pie chart: phân bổ lĩnh vực tổng = 100%
- [ ] Click chart element → navigate sang search filter

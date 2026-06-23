# 🔍 Chức năng 2: Tìm kiếm Thông minh (Smart Search)

> **Loại:** Core Feature | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-004, ISSUE-005 | **FR:** FR-003
> **Màn hình:** SCR-002 (`/search`)

---

## 2.1 Search cơ bản

### Các trường tìm kiếm

| Trường | Cách tìm | Ghi chú |
|---|---|---|
| Keyword | `$text` search trên `title`, `abstract`, `keywords[]` | Dùng MongoDB text index |
| Title | `$regex` hoặc `$text` | |
| Abstract | `$text` | |
| Author | `authors.name` match | Hỗ trợ partial match |
| Institution | `authors.affiliation` match | |
| Journal/Conference | `source_venue.name` match | |
| DOI | Exact match `doi` | Unique identifier |

### API Endpoint

```
GET /api/papers?q=machine+learning&author=Hinton&year_from=2020&year_to=2024
    &field=Computer Science&source=openalex&sort=citation&page=1&limit=20
```

### Bước thực hiện

```js
// controllers/paper.controller.js

async function searchPapers(req, res) {
  const {
    q, author, year_from, year_to,
    field, source, doi,
    sort = 'relevance', page = 1, limit = 20
  } = req.query;

  const query = {};

  // Full-text search
  if (q) {
    query.$text = { $search: q };
  }

  // Filter theo author
  if (author) {
    query['authors.name'] = { $regex: author, $options: 'i' };
  }

  // Filter theo năm
  if (year_from || year_to) {
    query.publication_year = {};
    if (year_from) query.publication_year.$gte = parseInt(year_from);
    if (year_to) query.publication_year.$lte = parseInt(year_to);
  }

  // Filter theo field
  if (field) {
    query.fields_of_study = { $regex: field, $options: 'i' };
  }

  // Filter theo nguồn
  if (source) {
    query.data_source = source;
  }

  // DOI exact match
  if (doi) {
    query.doi = doi;
  }

  // Sort options
  const sortOptions = {
    relevance: q ? { score: { $meta: 'textScore' } } : { publication_year: -1 },
    citation: { citation_count: -1 },
    year_desc: { publication_year: -1 },
    year_asc: { publication_year: 1 }
  };

  const skip = (parseInt(page) - 1) * parseInt(limit);

  const [papers, total] = await Promise.all([
    Paper.find(query)
      .sort(sortOptions[sort] || sortOptions.relevance)
      .skip(skip)
      .limit(parseInt(limit))
      .select('title authors publication_year doi citation_count fields_of_study data_source source_venue'),
    Paper.countDocuments(query)
  ]);

  // Ghi search history nếu user đã đăng nhập
  if (req.userId && q) {
    await SearchHistory.create({
      user_id: req.userId,
      query_text: q,
      filters: { year_from, year_to, field, source, author },
      result_count: total,
      searched_at: new Date()
    });
  }

  res.json({
    papers,
    pagination: {
      page: parseInt(page),
      limit: parseInt(limit),
      total,
      totalPages: Math.ceil(total / parseInt(limit))
    }
  });
}
```

---

## 2.2 Search nâng cao (Boolean Search)

### Mô tả
Hỗ trợ toán tử Boolean trong query:

| Cú pháp | Ý nghĩa | MongoDB $text |
|---|---|---|
| `AI AND Healthcare` | Chứa cả 2 | `"AI" "Healthcare"` |
| `Mamba NOT NLP` | Có Mamba, không NLP | `"Mamba" -"NLP"` |
| `LLM OR GPT` | Có 1 trong 2 | `LLM GPT` (default OR) |
| `"Large Language Model"` | Cụm từ chính xác | `"\"Large Language Model\""` |

### Bước thực hiện — Parse Boolean query

```js
// utils/queryParser.js

function parseBooleanQuery(input) {
  let mongoQuery = input;

  // AND → dùng quotes cho mỗi term
  // "AI AND Healthcare" → '"AI" "Healthcare"'
  mongoQuery = mongoQuery.replace(/\s+AND\s+/gi, '" "');

  // NOT → dùng dấu -
  // "Mamba NOT NLP" → '"Mamba" -"NLP"'
  mongoQuery = mongoQuery.replace(/\s+NOT\s+/gi, '" -"');

  // OR → MongoDB text search mặc định là OR
  mongoQuery = mongoQuery.replace(/\s+OR\s+/gi, '" "');

  // Wrap nếu chưa có quotes
  if (!mongoQuery.startsWith('"')) mongoQuery = '"' + mongoQuery;
  if (!mongoQuery.endsWith('"')) mongoQuery += '"';

  return mongoQuery;
}

// Sử dụng:
// parseBooleanQuery('AI AND Healthcare') → '"AI" "Healthcare"'
// parseBooleanQuery('Mamba NOT NLP') → '"Mamba" -"NLP"'
```

### Bộ lọc nâng cao

| Filter | Field | Type |
|---|---|---|
| Năm | `publication_year` | Range slider |
| Lĩnh vực | `fields_of_study` | Multi-select |
| Số citation | `citation_count` | Range (min–max) |
| Loại bài báo | `source_venue.type` | Select (journal/conference/preprint) |
| Nguồn dữ liệu | `data_source` | Checkbox |

---

## Frontend — Giao diện Search

### Component structure

```
src/components/search/
  SearchBar.jsx           ← input + nút search
  AdvancedFilters.jsx     ← bộ lọc năm, field, source...
  ResultsTable.jsx        ← bảng kết quả
  PaperCard.jsx           ← card hiển thị 1 paper
  Pagination.jsx          ← phân trang
  SortDropdown.jsx        ← sort by relevance/citation/year
```

### SearchBar component

```jsx
function SearchBar({ onSearch }) {
  const [query, setQuery] = useState('');
  const [showAdvanced, setShowAdvanced] = useState(false);

  const handleSubmit = (e) => {
    e.preventDefault();
    onSearch({ q: query });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search papers... (e.g., LLM AND Healthcare)"
      />
      <button type="submit">🔍 Search</button>
      <button type="button" onClick={() => setShowAdvanced(!showAdvanced)}>
        ⚙️ Advanced
      </button>
      {showAdvanced && <AdvancedFilters />}
    </form>
  );
}
```

---

## Checklist kiểm tra

- [ ] Search `q=machine learning` → trả kết quả có term này
- [ ] Search `q=AI AND Healthcare` → kết quả chứa cả 2 term
- [ ] Search `q=Mamba NOT NLP` → kết quả có Mamba, không có NLP
- [ ] Filter `year_from=2023` → chỉ papers từ 2023
- [ ] Filter `field=Computer Science` → đúng lĩnh vực
- [ ] Sort `citation` → paper nhiều citation lên trước
- [ ] Pagination → page 2 khác page 1
- [ ] Search history được ghi khi user đã login

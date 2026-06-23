# 🔗 Chức năng 3: Gợi ý Mở rộng Từ khóa (Keyword Expansion)

> **Loại:** AI Feature | **Priority:** 🔴 HIGH
> **Liên quan:** FR-009 | **Điểm khác biệt của hệ thống**

---

## Mô tả

Khi user nhập 1 từ khóa, hệ thống gợi ý các từ khóa liên quan theo chuỗi mở rộng.

```
User nhập: "LLM"
→ Large Language Model, GPT, Instruction Tuning, Prompt Engineering,
  RAG, RLHF, Multimodal, Fine-tuning, Agent, Reasoning

Tiếp tục mở rộng: LLM → RAG → Vector Database → Embedding → FAISS → Semantic Search
```

---

## Phương pháp thực hiện

### Cách 1: Dựa trên dữ liệu có sẵn (Co-occurrence)

```js
// services/keyword.expansion.js

async function expandKeyword(keyword) {
  // Tìm papers chứa keyword
  const papers = await Paper.find(
    { $text: { $search: keyword } },
    { keywords: 1, fields_of_study: 1 }
  ).limit(500);

  // Đếm tần suất xuất hiện cùng (co-occurrence)
  const counter = {};
  for (const paper of papers) {
    const allKeywords = [...(paper.keywords || []), ...(paper.fields_of_study || [])];
    for (const kw of allKeywords) {
      const normalized = kw.toLowerCase().trim();
      if (normalized !== keyword.toLowerCase()) {
        counter[normalized] = (counter[normalized] || 0) + 1;
      }
    }
  }

  // Sort theo tần suất, lấy top 10
  return Object.entries(counter)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 10)
    .map(([kw, count]) => ({ keyword: kw, count }));
}
```

### Cách 2: Dựa trên `topics` collection (Hierarchy)

```js
async function expandFromTopics(keyword) {
  // Tìm topic khớp keyword
  const topic = await Topic.findOne({
    $or: [
      { name: { $regex: keyword, $options: 'i' } },
      { aliases: { $regex: keyword, $options: 'i' } }
    ]
  });

  if (!topic) return [];

  // Lấy related topics
  const related = await Topic.find({
    _id: { $in: topic.related_topic_ids }
  }).select('name growth_rate gap_score');

  // Lấy child topics (sub-fields)
  const children = await Topic.find({
    parent_topic_id: topic._id
  }).select('name growth_rate gap_score');

  return {
    exact: topic,
    related: related.map(t => ({
      keyword: t.name,
      growth_rate: t.growth_rate,
      gap_score: t.gap_score
    })),
    subtopics: children.map(t => ({
      keyword: t.name,
      growth_rate: t.growth_rate,
      gap_score: t.gap_score
    }))
  };
}
```

### Cách 3: Dựa trên LLM (AI-powered)

```js
async function expandWithAI(keyword) {
  const prompt = `Given the research keyword "${keyword}", suggest 10 closely related 
research keywords that a researcher would want to explore. Return as JSON array of strings.
Focus on: synonyms, sub-topics, related methods, and emerging variations.`;

  const response = await callLLM(prompt);
  return JSON.parse(response); // ["Large Language Model", "GPT", "RAG", ...]
}
```

---

## API Endpoint

```
GET /api/keywords/expand?q=LLM&method=hybrid&depth=1

Response:
{
  "keyword": "LLM",
  "suggestions": [
    { "keyword": "Large Language Model", "source": "alias", "relevance": 0.99 },
    { "keyword": "GPT", "source": "co-occurrence", "relevance": 0.95, "growth": "+120%" },
    { "keyword": "RAG", "source": "topic-hierarchy", "relevance": 0.88, "growth": "+350%" },
    { "keyword": "Prompt Engineering", "source": "co-occurrence", "relevance": 0.82 },
    ...
  ],
  "expansion_chain": ["LLM", "RAG", "Vector Database", "Embedding", "Semantic Search"]
}
```

**Tham số `depth`:**
- `depth=1`: chỉ gợi ý trực tiếp
- `depth=2`: gợi ý tiếp từ kết quả (LLM → RAG → Vector DB)
- `depth=3`: 3 cấp mở rộng

---

## Frontend Component

```jsx
function KeywordExpansion({ initialKeyword, onSelect }) {
  const [suggestions, setSuggestions] = useState([]);
  const [chain, setChain] = useState([initialKeyword]);

  const expand = async (keyword) => {
    const res = await api.get(`/api/keywords/expand?q=${keyword}&depth=1`);
    setSuggestions(res.data.suggestions);
  };

  const handleSelect = (kw) => {
    setChain([...chain, kw]);
    expand(kw);         // Mở rộng tiếp
    onSelect(kw);       // Trigger search mới
  };

  return (
    <div>
      <div className="chain">{chain.join(' → ')}</div>
      <div className="suggestions">
        {suggestions.map(s => (
          <button key={s.keyword} onClick={() => handleSelect(s.keyword)}>
            {s.keyword} <span>{s.growth}</span>
          </button>
        ))}
      </div>
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Nhập `LLM` → trả về ≥ 5 keywords liên quan
- [ ] Click vào keyword gợi ý → mở rộng tiếp (chain)
- [ ] Keywords có `growth_rate` hiển thị đúng
- [ ] Depth=2: chain đạt 2 cấp mở rộng
- [ ] Keyword gợi ý click → trigger search mới

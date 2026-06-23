# 🗺️ Chức năng 7: Phân tích Ngách Nghiên cứu (Research Gap Discovery)

> **Loại:** AI Feature | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-006 | **FR:** FR-006
> **Màn hình:** SCR-004 (`/analysis/gap`)

---

## Mô tả

Phân tích mật độ bài báo theo chuỗi topic để tìm vùng ít được khai thác.

```
User nhập: "LLM"
  → LLM: 500,000 papers
    → LLM + Agriculture: 3,000 papers
      → LLM + Smart Farming: 150 papers
        → LLM + Rice Disease Detection: 12 papers
          ⇒ 🟢 Research Opportunity!
```

---

## Backend — Gap Engine

### Logic tính Gap Score

```js
// services/gap.engine.js

async function analyzeResearchGap(keyword, depth = 4) {
  const results = [];

  async function drill(currentKeyword, level) {
    if (level > depth) return;

    // Đếm papers cho keyword hiện tại
    const count = await Paper.countDocuments({
      $text: { $search: `"${currentKeyword}"` }
    });

    // Lấy sub-topics từ topics collection
    const topic = await Topic.findOne({
      $or: [
        { name: { $regex: currentKeyword, $options: 'i' } },
        { aliases: { $regex: currentKeyword, $options: 'i' } }
      ]
    });

    const subTopics = topic
      ? await Topic.find({ parent_topic_id: topic._id }).select('name gap_score')
      : [];

    // Nếu không có sub-topics, dùng AI gợi ý
    const narrowTopics = subTopics.length > 0
      ? subTopics.map(t => t.name)
      : await suggestNarrowTopics(currentKeyword);

    const node = {
      keyword: currentKeyword,
      paper_count: count,
      level,
      gap_level: classifyGap(count),
      children: []
    };

    // Drill-down vào sub-topics có ít papers
    for (const sub of narrowTopics.slice(0, 5)) {
      const combined = `${keyword} ${sub}`;
      const childNode = await drill(combined, level + 1);
      if (childNode) node.children.push(childNode);
    }

    results.push(node);
    return node;
  }

  return drill(keyword, 0);
}

function classifyGap(paperCount) {
  if (paperCount > 10000) return { level: 'saturated', label: '🔴 Bão hòa', color: 'red' };
  if (paperCount > 1000) return { level: 'established', label: '🟡 Đã phát triển', color: 'yellow' };
  if (paperCount > 100) return { level: 'emerging', label: '🟢 Đang phát triển', color: 'green' };
  if (paperCount > 10) return { level: 'opportunity', label: '💎 Research Opportunity', color: 'blue' };
  return { level: 'gap', label: '⭐ Research Gap!', color: 'gold' };
}

// Dùng AI để gợi ý hướng thu hẹp
async function suggestNarrowTopics(keyword) {
  const prompt = `For the research topic "${keyword}", suggest 5 specific sub-topics 
or application domains that could narrow down the research scope. Return as JSON array.`;
  const res = await callLLM(prompt);
  return JSON.parse(res);
}
```

### API Endpoints

```
GET /api/analysis/gap?q=LLM&depth=3

Response:
{
  "keyword": "LLM",
  "paper_count": 500000,
  "gap_level": { "level": "saturated", "label": "🔴 Bão hòa" },
  "children": [
    {
      "keyword": "LLM Agriculture",
      "paper_count": 3000,
      "gap_level": { "level": "emerging", "label": "🟢 Đang phát triển" },
      "children": [
        {
          "keyword": "LLM Smart Farming",
          "paper_count": 150,
          "gap_level": { "level": "opportunity", "label": "💎 Research Opportunity" },
          "children": [
            {
              "keyword": "LLM Rice Disease Detection",
              "paper_count": 12,
              "gap_level": { "level": "gap", "label": "⭐ Research Gap!" }
            }
          ]
        }
      ]
    }
  ]
}

GET /api/analysis/gap/heatmap?field=Computer+Science

Response:
{
  "field": "Computer Science",
  "topics": [
    { "name": "CNN", "year": 2023, "count": 5000, "gap_score": 0.2 },
    { "name": "Mamba", "year": 2023, "count": 120, "gap_score": 0.85 },
    ...
  ]
}
```

---

## Frontend — Hiển thị

### Drill-down Tree View

```jsx
function GapTree({ node, level = 0 }) {
  const [expanded, setExpanded] = useState(level < 2);
  const indent = level * 24;

  return (
    <div style={{ marginLeft: indent }}>
      <div className={`gap-node gap-${node.gap_level.level}`}
           onClick={() => setExpanded(!expanded)}>
        <span className="gap-indicator">{node.gap_level.label}</span>
        <strong>{node.keyword}</strong>
        <span className="paper-count">{node.paper_count.toLocaleString()} papers</span>
      </div>
      {expanded && node.children?.map((child, i) => (
        <GapTree key={i} node={child} level={level + 1} />
      ))}
    </div>
  );
}
```

### Heatmap View (SCR-004)

```jsx
function GapHeatmap({ data }) {
  // x = năm, y = topic, color = paper count (nhạt = gap)
  return (
    <div className="heatmap-grid">
      {data.topics.map(topic => (
        <div key={topic.name} className="heatmap-row">
          <span>{topic.name}</span>
          <div
            className="heatmap-cell"
            style={{
              backgroundColor: `rgba(255, 107, 107, ${1 - topic.gap_score})`,
              // gap_score cao → nhạt → research opportunity
            }}
            title={`${topic.count} papers | Gap: ${(topic.gap_score * 100).toFixed(0)}%`}
          />
        </div>
      ))}
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] `GET /api/analysis/gap?q=LLM` → trả về cây drill-down
- [ ] Paper count giảm dần khi drill sâu
- [ ] `gap_level` phân loại đúng (saturated/emerging/gap)
- [ ] Heatmap render đúng màu (đậm = nhiều, nhạt = gap)
- [ ] Click ô heatmap → navigate sang search filter đúng

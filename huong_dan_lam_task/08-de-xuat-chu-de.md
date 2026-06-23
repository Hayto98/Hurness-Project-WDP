# 💡 Chức năng 8: Đề xuất Chủ đề Nghiên cứu

> **Loại:** AI Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** FR-006, FR-009

---

## Mô tả

Hệ thống đề xuất chủ đề nghiên cứu tiềm năng dựa trên xu hướng, growth rate, citation, novelty.

```
Đề tài gợi ý: "Using Mamba for Medical Image Segmentation"
  Potential:    ★★★★★
  Competition:  ★★☆☆☆
  Trend:        ★★★★★
```

---

## Backend

### Logic đề xuất

```js
// services/suggestion.engine.js

async function suggestResearchTopics(userInterests = [], limit = 5) {
  // 1. Lấy topics có growth cao + gap_score cao (ít người làm nhưng đang hot)
  const candidates = await Topic.find({
    growth_rate: { $gt: 20 },   // tăng trưởng > 20%
    gap_score: { $gt: 0.5 }     // còn nhiều cơ hội
  }).sort({ growth_rate: -1 }).limit(50);

  // 2. Kết hợp cặp topic → tạo giao đề (cross-domain)
  const suggestions = [];
  for (const topic of candidates) {
    // Đếm papers ở giao giữa topic này và user interests
    for (const interest of userInterests.length > 0 ? userInterests : ['']) {
      const combined = interest ? `${topic.name} ${interest}` : topic.name;
      const count = await Paper.countDocuments({
        $text: { $search: `"${combined}"` }
      });

      const potential = computePotential(topic.growth_rate, topic.gap_score);
      const competition = computeCompetition(count);

      suggestions.push({
        title: generateTitle(topic.name, interest),
        topic: topic.name,
        domain: interest || 'General',
        paper_count: count,
        potential,       // 1–5
        competition,     // 1–5
        trend: Math.min(5, Math.ceil(topic.growth_rate / 50)),
        growth_rate: topic.growth_rate,
        gap_score: topic.gap_score
      });
    }
  }

  // Sort theo potential DESC, competition ASC
  return suggestions
    .sort((a, b) => (b.potential - b.competition) - (a.potential - a.competition))
    .slice(0, limit);
}

function computePotential(growthRate, gapScore) {
  const score = (growthRate / 100) * 0.6 + gapScore * 0.4;
  return Math.min(5, Math.max(1, Math.ceil(score * 5)));
}

function computeCompetition(paperCount) {
  if (paperCount > 10000) return 5;
  if (paperCount > 1000) return 4;
  if (paperCount > 100) return 3;
  if (paperCount > 10) return 2;
  return 1;
}

// Dùng AI để tạo title đề tài hay
async function generateTitle(topic, domain) {
  if (!domain) return `Research on ${topic}`;
  const prompt = `Suggest a concise academic paper title combining "${topic}" and "${domain}". 
Return only the title, no explanation.`;
  return await callLLM(prompt);
}
```

### API

```
GET /api/suggestions/topics?interests=medical,agriculture&limit=5

Response:
{
  "suggestions": [
    {
      "title": "Using Mamba for Medical Image Segmentation",
      "topic": "Mamba",
      "domain": "Medical",
      "paper_count": 23,
      "potential": 5,
      "competition": 2,
      "trend": 5,
      "growth_rate": 700,
      "gap_score": 0.85
    },
    {
      "title": "Applying LLM-based Agents for Smart Farming Automation",
      "topic": "Agentic AI",
      "domain": "Agriculture",
      "paper_count": 8,
      "potential": 5,
      "competition": 1,
      "trend": 4,
      "growth_rate": 200,
      "gap_score": 0.92
    }
  ]
}
```

---

## Frontend

```jsx
function SuggestionCard({ item }) {
  const stars = (n) => '★'.repeat(n) + '☆'.repeat(5 - n);

  return (
    <div className="suggestion-card">
      <h3>{item.title}</h3>
      <div className="metrics">
        <div>Potential: <span className="stars">{stars(item.potential)}</span></div>
        <div>Competition: <span className="stars">{stars(item.competition)}</span></div>
        <div>Trend: <span className="stars">{stars(item.trend)}</span></div>
      </div>
      <div className="meta">
        <span>📊 {item.paper_count} papers</span>
        <span>📈 +{item.growth_rate}%</span>
      </div>
      <button onClick={() => searchFor(item.topic + ' ' + item.domain)}>
        🔍 Xem papers liên quan
      </button>
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] `GET /api/suggestions/topics` → trả về ≥ 3 gợi ý
- [ ] Gợi ý có potential cao + competition thấp
- [ ] Click "Xem papers" → search đúng keyword
- [ ] Stars render đúng (1–5)

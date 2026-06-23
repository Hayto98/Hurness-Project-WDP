# 📈 Chức năng 4: Phân tích Xu hướng Nghiên cứu (Trend Analysis)

> **Loại:** AI Module chính | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-006 | **FR:** FR-005

---

## Mô tả

Hệ thống tính toán và trực quan hóa xu hướng nghiên cứu theo thời gian, bao gồm:
- Xu hướng số lượng bài báo theo năm
- Độ tăng trưởng (%) của từng keyword/lĩnh vực
- Từ khóa nổi bật (trending)
- Từ khóa đang giảm (declining)

---

## Backend — Trend Engine

### Cấu trúc file

```
src/services/
  trend.engine.js       ← tính toán xu hướng
  trend.scheduler.js    ← cron job cập nhật
```

### 4.1 Xu hướng theo thời gian

```js
// services/trend.engine.js

async function computeTrendForTopic(topicName) {
  // Aggregate papers theo năm cho topic
  const trendData = await Paper.aggregate([
    { $match: {
      $or: [
        { fields_of_study: { $regex: topicName, $options: 'i' } },
        { keywords: { $regex: topicName, $options: 'i' } }
      ]
    }},
    { $group: {
      _id: '$publication_year',
      paper_count: { $sum: 1 },
      avg_citations: { $avg: '$citation_count' }
    }},
    { $sort: { _id: 1 } }
  ]);

  // Tính delta_pct (% thay đổi so với năm trước)
  const withDelta = trendData.map((item, i) => {
    const prev = i > 0 ? trendData[i - 1].paper_count : item.paper_count;
    return {
      year: item._id,
      paper_count: item.paper_count,
      avg_citations: Math.round(item.avg_citations),
      delta_pct: prev > 0
        ? parseFloat(((item.paper_count - prev) / prev * 100).toFixed(1))
        : 0
    };
  });

  return withDelta;
}
```

### 4.2 Tính Growth Rate (YoY)

```js
function computeGrowthRate(trendData) {
  if (trendData.length < 2) return 0;

  const recent = trendData.slice(-3); // 3 năm gần nhất
  const oldest = recent[0].paper_count;
  const newest = recent[recent.length - 1].paper_count;

  if (oldest === 0) return 100;
  return parseFloat(((newest - oldest) / oldest * 100).toFixed(1));
}
```

### 4.3 Tính Saturation Score

```js
function computeSaturationScore(trendData) {
  // Nếu growth rate giảm dần trong 3 năm gần → saturation cao
  if (trendData.length < 3) return 0;

  const recentDeltas = trendData.slice(-3).map(d => d.delta_pct);
  const isDecreasing = recentDeltas[0] > recentDeltas[1]
                    && recentDeltas[1] > recentDeltas[2];
  const avgGrowth = recentDeltas.reduce((a, b) => a + b, 0) / 3;

  if (isDecreasing && avgGrowth < 5) return 0.9;   // rất bão hòa
  if (isDecreasing) return 0.7;
  if (avgGrowth < 10) return 0.5;
  if (avgGrowth > 50) return 0.1;                   // đang phát triển mạnh
  return 0.3;
}
```

### 4.4 Từ khóa nổi bật & giảm

```js
async function getTopTrending(limit = 10) {
  return Topic.find({ growth_rate: { $gt: 0 } })
    .sort({ growth_rate: -1 })
    .limit(limit)
    .select('name growth_rate trend_data');
}

async function getDeclining(limit = 10) {
  return Topic.find({ growth_rate: { $lt: 0 } })
    .sort({ growth_rate: 1 })
    .limit(limit)
    .select('name growth_rate saturation_score');
}
```

### 4.5 Scheduler cập nhật topics

```js
// services/trend.scheduler.js
const cron = require('node-cron');

// Chạy sau ETL sync (3AM mỗi ngày)
cron.schedule('0 3 * * *', async () => {
  const topics = await Topic.find({});
  for (const topic of topics) {
    const trendData = await computeTrendForTopic(topic.name);
    const growth_rate = computeGrowthRate(trendData);
    const saturation_score = computeSaturationScore(trendData);

    await Topic.findByIdAndUpdate(topic._id, {
      trend_data: trendData,
      growth_rate,
      saturation_score,
      computed_at: new Date()
    });
  }
  console.log(`Updated trends for ${topics.length} topics`);
});
```

---

## API Endpoints

```
GET /api/analysis/trends?field=Computer+Science&year_from=2018&limit=20
GET /api/analysis/trending            → top keywords tăng trưởng
GET /api/analysis/declining           → keywords đang giảm
```

### Response mẫu `/api/analysis/trends`

```json
{
  "trends": [
    {
      "name": "GPT",
      "growth_rate": 350,
      "trend_data": [
        { "year": 2020, "paper_count": 500, "delta_pct": 0 },
        { "year": 2021, "paper_count": 1200, "delta_pct": 140 },
        { "year": 2022, "paper_count": 3500, "delta_pct": 191.7 },
        { "year": 2023, "paper_count": 12000, "delta_pct": 242.9 }
      ]
    }
  ]
}
```

---

## Frontend — Biểu đồ xu hướng

```jsx
import { LineChart, Line, XAxis, YAxis, Tooltip, Legend } from 'recharts';

function TrendChart({ data }) {
  return (
    <LineChart width={800} height={400} data={data}>
      <XAxis dataKey="year" />
      <YAxis />
      <Tooltip />
      <Legend />
      <Line type="monotone" dataKey="paper_count" stroke="#8884d8" name="Số bài báo" />
    </LineChart>
  );
}
```

---

## Checklist kiểm tra

- [ ] `GET /api/analysis/trends?field=AI` → trả về trend_data đúng
- [ ] `GET /api/analysis/trending` → top 10 keywords growth cao nhất
- [ ] `GET /api/analysis/declining` → keywords growth âm
- [ ] Scheduler chạy → `topics.computed_at` được cập nhật
- [ ] Chart render đúng dữ liệu (năm trên x, số bài trên y)

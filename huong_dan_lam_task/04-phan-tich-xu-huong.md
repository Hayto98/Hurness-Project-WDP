# 📈 Chức năng 4: Phân tích Xu hướng Nghiên cứu (Trend Analysis)

> **Loại:** AI Module chính | **Priority:** 🔴 HIGH
> **Stack Backend:** Python (FastAPI + Pandas + Motor)

---

## Mô tả
Chức năng này tính toán xu hướng nghiên cứu theo thời gian. Sử dụng `Pandas` trong Python là lựa chọn hoàn hảo để nhóm (groupby) và tính toán phần trăm tăng trưởng (YoY) cho hàng triệu bài báo cực kỳ nhanh chóng.

---

## Bước thực hiện

### 1. Viết Trend Engine với Pandas

```python
# services/trend_engine.py
import pandas as pd
from motor.motor_asyncio import AsyncIOMotorClient

client = AsyncIOMotorClient("mongodb://localhost:27017")
db = client.hurness_db

async def compute_trend_for_topic(topic_name: str):
    # Lấy dữ liệu từ MongoDB
    cursor = db.papers.find(
        {
            "$or": [
                {"fields_of_study": {"$regex": topic_name, "$options": "i"}},
                {"keywords": {"$regex": topic_name, "$options": "i"}}
            ]
        },
        {"publication_year": 1, "citation_count": 1}
    )
    
    data = await cursor.to_list(length=None)
    if not data:
        return [], 0, 0
        
    df = pd.DataFrame(data)
    
    # Dùng Pandas GroupBy theo năm
    trend_df = df.groupby('publication_year').agg(
        paper_count=('publication_year', 'count'),
        avg_citations=('citation_count', 'mean')
    ).reset_index()
    
    # Sắp xếp theo năm
    trend_df = trend_df.sort_values('publication_year')
    
    # Tính Delta Pct (Phần trăm tăng trưởng so với năm trước)
    trend_df['delta_pct'] = trend_df['paper_count'].pct_change() * 100
    trend_df['delta_pct'] = trend_df['delta_pct'].fillna(0).round(1)
    trend_df['avg_citations'] = trend_df['avg_citations'].round(1)
    
    # Tính Growth Rate chung (3 năm gần nhất)
    recent = trend_df.tail(3)
    if len(recent) >= 2:
        oldest = recent.iloc[0]['paper_count']
        newest = recent.iloc[-1]['paper_count']
        growth_rate = ((newest - oldest) / oldest * 100) if oldest > 0 else 100.0
    else:
        growth_rate = 0.0
        
    # Tính Saturation Score (Độ bão hòa)
    # Nếu delta_pct giảm dần trong 3 năm -> Bão hòa
    saturation_score = 0.3
    if len(recent) == 3:
        deltas = recent['delta_pct'].tolist()
        if deltas[0] > deltas[1] > deltas[2]:
            saturation_score = 0.8 if sum(deltas)/3 < 5 else 0.6

    # Chuyển về định dạng Dict (JSON)
    trend_data = trend_df.to_dict('records')
    
    return trend_data, round(growth_rate, 1), saturation_score
```

### 2. Scheduler cập nhật DB định kỳ

```python
# jobs/trend_scheduler.py
from apscheduler.schedulers.asyncio import AsyncIOScheduler
from services.trend_engine import compute_trend_for_topic
from datetime import datetime

scheduler = AsyncIOScheduler()

async def update_all_topics():
    cursor = db.topics.find({}, {"name": 1})
    topics = await cursor.to_list(length=None)
    
    for topic in topics:
        trend_data, growth, saturation = await compute_trend_for_topic(topic['name'])
        
        await db.topics.update_one(
            {"_id": topic['_id']},
            {"$set": {
                "trend_data": trend_data,
                "growth_rate": growth,
                "saturation_score": saturation,
                "computed_at": datetime.now()
            }}
        )
    print(f"Updated trends for {len(topics)} topics")

# Chạy lúc 3 AM mỗi ngày
scheduler.add_job(update_all_topics, 'cron', hour=3, minute=0)
scheduler.start()
```

### 3. API Endpoints (FastAPI)

```python
# main.py
from fastapi import FastAPI
from typing import List

app = FastAPI()

@app.get("/api/analysis/trends")
async def get_trends(field: str = None, limit: int = 20):
    query = {}
    if field:
        query["field_of_study"] = field
        
    cursor = db.topics.find(query).sort("growth_rate", -1).limit(limit)
    topics = await cursor.to_list(length=limit)
    
    # FastAPI tự động serialize list of dicts thành JSON
    return [{"name": t["name"], "growth_rate": t["growth_rate"], "trend_data": t["trend_data"]} for t in topics]

@app.get("/api/analysis/trending")
async def get_trending_topics(limit: int = 10):
    cursor = db.topics.find({"growth_rate": {"$gt": 0}}).sort("growth_rate", -1).limit(limit)
    return await cursor.to_list(length=limit)
```

---

## Checklist kiểm tra
- [ ] Hàm Pandas tính toán `pct_change` trả về kết quả đúng (không bị lỗi chia cho 0).
- [ ] API `/api/analysis/trends` trả về kết quả cực nhanh vì chỉ query bảng `topics` đã tính toán sẵn.
- [ ] Dữ liệu trả về khớp với định dạng D3.js hoặc Recharts bên Frontend cần.

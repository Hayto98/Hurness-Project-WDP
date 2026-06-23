# 🗺️ Chức năng 7: Phân tích Ngách Nghiên cứu (Research Gap Discovery)

> **Loại:** AI Feature | **Priority:** 🔴 HIGH
> **Stack Backend:** Python (FastAPI + LangChain)

---

## Mô tả
Phân tích mật độ bài báo theo chuỗi topic để tìm vùng ít được khai thác. Khi không có sub-topics sẵn trong CSDL, hệ thống dùng LLM (thông qua LangChain) để tự động gợi ý các hướng rẽ nhánh hẹp hơn.

---

## Bước thực hiện

### 1. Viết Gap Engine kết hợp MongoDB và LangChain

```python
# services/gap_engine.py
from motor.motor_asyncio import AsyncIOMotorClient
from langchain_openai import ChatOpenAI
from pydantic import BaseModel, Field

client = AsyncIOMotorClient("mongodb://localhost:27017")
db = client.hurness_db
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.7)

class SubTopics(BaseModel):
    topics: list[str] = Field(description="List of 5 narrow sub-topics")

async def suggest_narrow_topics(keyword: str) -> list[str]:
    # Sử dụng LangChain structured output
    structured_llm = llm.with_structured_output(SubTopics)
    
    prompt = f"""
    For the research topic "{keyword}", suggest 5 specific sub-topics 
    or application domains that narrow down the research scope significantly.
    Find niche areas where few papers might exist.
    """
    
    res = structured_llm.invoke(prompt)
    return res.topics

def classify_gap(paper_count: int) -> dict:
    if paper_count > 10000:
        return {"level": "saturated", "label": "🔴 Bão hòa", "color": "red"}
    if paper_count > 1000:
        return {"level": "established", "label": "🟡 Đã phát triển", "color": "yellow"}
    if paper_count > 100:
        return {"level": "emerging", "label": "🟢 Đang phát triển", "color": "green"}
    if paper_count > 10:
        return {"level": "opportunity", "label": "💎 Research Opportunity", "color": "blue"}
    return {"level": "gap", "label": "⭐ Research Gap!", "color": "gold"}

async def analyze_research_gap(keyword: str, depth: int = 3, current_level: int = 0):
    if current_level > depth:
        return None

    # Đếm số lượng papers có keyword này (Text Search)
    count = await db.papers.count_documents(
        {"$text": {"$search": f'"{keyword}"'}}
    )

    # Tìm sub_topics từ CSDL (nếu có)
    topic_doc = await db.topics.find_one({"name": {"$regex": f"^{keyword}$", "$options": "i"}})
    
    narrow_topics = []
    if topic_doc:
        sub_cursor = db.topics.find({"parent_topic_id": topic_doc["_id"]}).limit(5)
        subs = await sub_cursor.to_list(length=5)
        narrow_topics = [s["name"] for s in subs]
    
    # Nếu không có trong DB, dùng AI gợi ý
    if not narrow_topics:
        narrow_topics = await suggest_narrow_topics(keyword)

    node = {
        "keyword": keyword,
        "paper_count": count,
        "level": current_level,
        "gap_level": classify_gap(count),
        "children": []
    }

    # Đệ quy (Drill-down)
    for sub in narrow_topics[:3]: # Chỉ lấy 3 nhánh để tránh API call quá lâu
        combined_kw = f"{keyword} {sub}"
        child_node = await analyze_research_gap(combined_kw, depth, current_level + 1)
        if child_node:
            node["children"].append(child_node)

    return node
```

### 2. FastAPI Endpoint

```python
# main.py
from fastapi import FastAPI, Query
from services.gap_engine import analyze_research_gap

app = FastAPI()

@app.get("/api/analysis/gap")
async def get_research_gap(q: str = Query(..., description="Root keyword"), depth: int = 3):
    result = await analyze_research_gap(q, depth)
    return result
```

### 3. API Heatmap Data

Để vẽ biểu đồ heatmap bên Frontend (như SCR-004), bạn cần một endpoint trả về danh sách các topic và chỉ số Gap Score.

```python
@app.get("/api/analysis/gap/heatmap")
async def get_gap_heatmap(field: str):
    cursor = db.topics.find({"field_of_study": field}).sort("gap_score", -1).limit(50)
    topics = await cursor.to_list(length=50)
    
    return {
        "field": field,
        "topics": [
            {
                "name": t["name"],
                "count": t.get("trend_data", [{}])[-1].get("paper_count", 0),
                "gap_score": t["gap_score"]
            }
            for t in topics
        ]
    }
```

---

## Checklist kiểm tra
- [ ] Gọi API với `q=LLM` chạy được và trả về cây JSON phân cấp đệ quy.
- [ ] Node con có số lượng `paper_count` giảm dần.
- [ ] Phân loại `gap_level` chính xác dựa trên logic if/else.
- [ ] LLM trả về đúng JSON array các sub-topics (nhờ Pydantic Schema).

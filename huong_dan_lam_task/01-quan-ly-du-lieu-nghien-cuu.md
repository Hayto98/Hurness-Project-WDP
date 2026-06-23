# 📦 Chức năng 1: Quản lý Dữ liệu Nghiên cứu (Research Data Management)

> **Loại:** Nền tảng (Foundation) | **Priority:** 🔴 HIGH
> **Stack Backend:** Python (FastAPI + Pandas + Motor/MongoDB)

---

## 1.1 Thu thập dữ liệu (Data Collection)

### Mô tả
Crawl metadata bài báo từ các nguồn học thuật bằng Python `httpx` (async) hoặc `requests`, xử lý dữ liệu lớn bằng `Pandas`.

### Bước thực hiện

```
src/
  services/
    etl/
      openalex_connector.py
      arxiv_connector.py
      etl_manager.py
  main.py
```

**Mỗi connector kế thừa từ Base class:**

```python
# services/etl/base_connector.py
from abc import ABC, abstractmethod

class BaseConnector(ABC):
    @abstractmethod
    async def fetch(self, query: str, options: dict):
        pass
        
    @abstractmethod
    def normalize(self, raw_data: dict) -> dict:
        pass
```

**Ví dụ OpenAlex connector (Python):**

```python
# services/etl/openalex_connector.py
import httpx
from .base_connector import BaseConnector

class OpenAlexConnector(BaseConnector):
    def __init__(self):
        self.base_url = "https://api.openalex.org/works"
        self.mailto = "admin@yourdomain.com" # Polite pool

    async def fetch(self, query: str, page: int = 1, per_page: int = 50):
        async with httpx.AsyncClient() as client:
            response = await client.get(
                self.base_url,
                params={
                    "search": query,
                    "page": page,
                    "per_page": per_page,
                    "mailto": self.mailto
                },
                timeout=30.0
            )
            response.raise_for_status()
            data = response.json()
            return [self.normalize(item) for item in data.get("results", [])]

    def normalize(self, raw: dict) -> dict:
        return {
            "doi": raw.get("doi", "").replace("https://doi.org/", "") if raw.get("doi") else None,
            "title": raw.get("title"),
            "abstract": self._reconstruct_abstract(raw.get("abstract_inverted_index")),
            "publication_year": raw.get("publication_year"),
            "data_source": "openalex",
            "external_ids": {"openalex_id": raw.get("id")}
        }
        
    def _reconstruct_abstract(self, inverted_index: dict) -> str:
        if not inverted_index: return ""
        # Logic gộp abstract từ inverted index
        word_index = []
        for word, positions in inverted_index.items():
            for pos in positions:
                word_index.append((pos, word))
        word_index.sort(key=lambda x: x[0])
        return " ".join([word for _, word in word_index])
```

---

## 1.2 Import & Làm sạch dữ liệu với Pandas

### Mô tả
Sử dụng thư viện `Pandas` của Python để xử lý hàng triệu bản ghi, loại bỏ trùng lặp cực nhanh trước khi insert vào MongoDB.

### API FastAPI xử lý Import

```python
# main.py
from fastapi import FastAPI, UploadFile, File
import pandas as pd
from io import StringIO
from motor.motor_asyncio import AsyncIOMotorClient

app = FastAPI()
client = AsyncIOMotorClient("mongodb://localhost:27017")
db = client.hurness_db

@app.post("/api/papers/import-csv")
async def import_csv(file: UploadFile = File(...)):
    contents = await file.read()
    
    # 1. Load vào Pandas DataFrame
    df = pd.read_csv(StringIO(contents.decode('utf-8')))
    
    # 2. Làm sạch: Drop duplicate DOI
    df = df.dropna(subset=['doi'])
    df = df.drop_duplicates(subset=['doi'], keep='last')
    
    # 3. Chuẩn hóa text
    df['title'] = df['title'].str.strip().str.title()
    df['keywords'] = df['keywords'].apply(lambda x: [k.strip().lower() for k in str(x).split(',') if k])
    
    # 4. Insert vào MongoDB (Bulk Write)
    records = df.to_dict('records')
    try:
        await db.papers.insert_many(records, ordered=False)
        return {"imported": len(records), "status": "success"}
    except Exception as e:
        return {"status": "partial_success", "error": str(e)}
```

### Dedup định kỳ (Data Cleaning Job)

```python
# services/cleaning_service.py
import pandas as pd

async def deduplicate_db():
    # Lấy toàn bộ DOI từ DB
    cursor = db.papers.find({"doi": {"$ne": None}}, {"_id": 1, "doi": 1})
    data = await cursor.to_list(length=None)
    
    if not data: return
    
    df = pd.DataFrame(data)
    
    # Tìm các DOI xuất hiện > 1 lần
    duplicates = df[df.duplicated('doi', keep=False)]
    
    if duplicates.empty: return
    
    # Giữ lại document đầu tiên, xóa phần còn lại
    to_keep = duplicates.drop_duplicates('doi', keep='first')
    to_delete = duplicates[~duplicates['_id'].isin(to_keep['_id'])]
    
    # Xóa khỏi MongoDB
    delete_ids = to_delete['_id'].tolist()
    await db.papers.delete_many({"_id": {"$in": delete_ids}})
    print(f"Removed {len(delete_ids)} duplicate papers.")
```

---

## Checklist kiểm tra
- [ ] Chạy FastAPI server (`uvicorn main:app --reload`)
- [ ] Test OpenAlex connector fetch data bằng `httpx` không bị timeout
- [ ] Pandas deduplicate DOI đúng
- [ ] Bulk insert MongoDB hoạt động mượt với file >10,000 dòng

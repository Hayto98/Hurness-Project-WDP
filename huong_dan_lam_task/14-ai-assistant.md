# 🤖 Chức năng 14: AI Assistant (RAG System)

> **Loại:** AI Feature | **Priority:** 🟡 MEDIUM
> **Stack Backend:** Python (FastAPI + LangChain + OpenAI + Motor)

---

## Mô tả
Hệ thống RAG (Retrieval-Augmented Generation) xây dựng hoàn toàn bằng hệ sinh thái Python (LangChain). AI sẽ đọc Database (qua Vector Search hoặc Text Search) để trả lời câu hỏi của user có trích dẫn nguồn.

---

## Bước thực hiện

### 1. Kiến trúc RAG bằng Python

```
User Question -> LLM tạo Search Query -> MongoDB Text Search -> Lấy Context -> LLM trả lời
```

### 2. Viết Service với LangChain

```python
# services/rag_service.py
from langchain_openai import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from motor.motor_asyncio import AsyncIOMotorClient

client = AsyncIOMotorClient("mongodb://localhost:27017")
db = client.hurness_db
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0.3)

async def get_relevant_papers(query: str, limit: int = 5):
    # Sử dụng MongoDB Text Search
    cursor = db.papers.find(
        {"$text": {"$search": query}},
        {"score": {"$meta": "textScore"}, "title": 1, "abstract": 1, "publication_year": 1, "doi": 1}
    ).sort([("score", {"$meta": "textScore"})]).limit(limit)
    
    return await cursor.to_list(length=limit)

async def ask_assistant(user_question: str):
    # 1. Lấy context từ MongoDB
    papers = await get_relevant_papers(user_question)
    
    if not papers:
        return {"answer": "Tôi không tìm thấy tài liệu nào trong hệ thống về chủ đề này.", "sources": []}
    
    # 2. Build Context String
    context_str = ""
    sources = []
    for idx, p in enumerate(papers):
        context_str += f"[{idx+1}] Title: {p.get('title')} ({p.get('publication_year')})\nAbstract: {p.get('abstract')}\n\n"
        sources.append({
            "title": p.get("title"), 
            "doi": p.get("doi"), 
            "year": p.get("publication_year")
        })

    # 3. LangChain Prompt
    prompt = ChatPromptTemplate.from_messages([
        ("system", """You are an academic AI assistant for the Hurness Research System.
        Answer the user's question using ONLY the provided context.
        Always cite your sources using bracket numbers like [1], [2].
        If the answer is not in the context, say you don't know based on current data.
        
        Context Papers:
        {context}"""),
        ("user", "{question}")
    ])
    
    # 4. Chain execution
    chain = prompt | llm
    response = await chain.ainvoke({"context": context_str, "question": user_question})
    
    return {
        "answer": response.content,
        "sources": sources
    }
```

### 3. FastAPI Endpoint

```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel
from services.rag_service import ask_assistant

app = FastAPI()

class AskRequest(BaseModel):
    question: str
    
@app.post("/api/ai/ask")
async def ai_chat(req: AskRequest):
    result = await ask_assistant(req.question)
    return result
```

### 4. Advanced: Vector Search (Thay vì Text Search)

Nếu bạn muốn kết quả chính xác hơn theo ngữ nghĩa (Semantic Search), bạn tạo Embedding bằng OpenAI và lưu vào MongoDB Atlas Vector Search hoặc Milvus.

```python
# Ví dụ tạo embedding
from langchain_openai import OpenAIEmbeddings

embeddings_model = OpenAIEmbeddings()

async def create_paper_embedding(paper_id: str, title: str, abstract: str):
    text = f"{title}\n{abstract}"
    vector = await embeddings_model.aembed_query(text)
    
    # Lưu vector vào MongoDB
    await db.papers.update_one({"_id": paper_id}, {"$set": {"embedding": vector}})
```

---

## Checklist kiểm tra
- [ ] Chạy query RAG và AI trả lời đúng thông tin có trong abstract.
- [ ] AI có chèn citation dạng `[1]`, `[2]` vào câu trả lời.
- [ ] MongoDB Text Search trả về đúng paper theo mức độ liên quan.

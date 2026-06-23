# 📄 Chức năng 11: Phân tích Full-text PDF

> **Loại:** AI Feature | **Priority:** 🟡 MEDIUM
> **Stack Backend:** Python (FastAPI + PyMuPDF + LangChain/OpenAI)

---

## Mô tả
Python là "vua" trong xử lý PDF và AI. Chức năng này sử dụng `PyMuPDF` để đọc PDF cực chuẩn, và `LangChain` kết nối với LLM để trích xuất thông tin.

---

## Bước thực hiện

### 1. Cài đặt thư viện

```bash
pip install fastapi uvicorn python-multipart pymupdf langchain langchain-openai
```

### 2. Trích xuất Text từ PDF bằng PyMuPDF (fitz)

```python
# services/pdf_service.py
import fitz # PyMuPDF
import io

def extract_text_from_pdf(file_bytes: bytes) -> str:
    # Mở PDF từ byte stream (không cần lưu file tạm)
    doc = fitz.open(stream=file_bytes, filetype="pdf")
    text = ""
    
    # Đọc tối đa 10 trang đầu để tiết kiệm token LLM
    for page_num in range(min(10, doc.page_count)):
        page = doc.load_page(page_num)
        text += page.get_text("text") + "\n"
        
    return text

def extract_references_regex(text: str) -> list:
    import re
    # Tìm chuẩn DOI
    doi_pattern = r"10.\d{4,9}/[-._;()/:A-Z0-9]+"
    dois = re.findall(doi_pattern, text, re.IGNORECASE)
    return list(set(dois))
```

### 3. Dùng LangChain để trích xuất & Tóm tắt

```python
# services/ai_service.py
from langchain_openai import ChatOpenAI
from langchain.prompts import PromptTemplate
from pydantic import BaseModel, Field

# Định nghĩa Schema đầu ra cho LangChain
class PaperMetadata(BaseModel):
    title: str = Field(description="Title of the paper")
    authors: list[str] = Field(description="List of authors")
    abstract: str = Field(description="The abstract of the paper")
    keywords: list[str] = Field(description="5-10 research keywords")

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

async def analyze_paper_content(text: str):
    # LLM Structured Output (JSON)
    structured_llm = llm.with_structured_output(PaperMetadata)
    
    prompt = f"""
    Extract the metadata and keywords from the following academic paper text.
    Text (first few pages):
    {text[:5000]}
    """
    
    # Gọi AI
    result = structured_llm.invoke(prompt)
    
    # Tóm tắt
    summary_prompt = f"Summarize this paper in 3 bullet points:\n{text[:8000]}"
    summary_res = llm.invoke(summary_prompt)
    
    return {
        "metadata": result.dict(),
        "summary": summary_res.content
    }
```

### 4. FastAPI Endpoint

```python
# main.py
from fastapi import FastAPI, UploadFile, File
from services.pdf_service import extract_text_from_pdf, extract_references_regex
from services.ai_service import analyze_paper_content

app = FastAPI()

@app.post("/api/papers/upload-pdf")
async def upload_pdf(file: UploadFile = File(...)):
    # Lấy byte data
    file_bytes = await file.read()
    
    # 1. OCR / Text Extraction
    text = extract_text_from_pdf(file_bytes)
    
    # 2. AI Analysis
    analysis = await analyze_paper_content(text)
    
    # 3. Trích xuất tài liệu tham khảo
    references = extract_references_regex(text)
    
    return {
        "title": analysis["metadata"]["title"],
        "authors": analysis["metadata"]["authors"],
        "abstract": analysis["metadata"]["abstract"],
        "keywords": analysis["metadata"]["keywords"],
        "summary": analysis["summary"],
        "references_dois": references
    }
```

---

## Checklist kiểm tra
- [ ] `PyMuPDF` đọc PDF không bị lỗi font so với các thư viện JS.
- [ ] `LangChain` trả về JSON `PaperMetadata` chuẩn định dạng.
- [ ] Regex bắt được DOI ở phần References cuối PDF.

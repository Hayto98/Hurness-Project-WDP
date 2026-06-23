# 📄 Chức năng 11: Phân tích Full-text PDF

> **Loại:** AI Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** FR-009

---

## Mô tả

Khi user upload PDF, hệ thống trích xuất nội dung, tóm tắt, sinh keyword, tìm paper tương tự.

---

## Bước thực hiện

### Packages cần cài

```
npm install pdf-parse multer
```

### API

```
POST /api/papers/upload-pdf
  Content-Type: multipart/form-data
  Body: file (PDF)

Response:
{
  "metadata": { "title", "authors", "abstract" },
  "summary": "AI-generated summary...",
  "keywords": ["LLM", "RAG", ...],
  "topics": ["Natural Language Processing"],
  "similar_papers": [{ "_id", "title", "similarity": 0.92 }],
  "references": ["doi:10.xxx", ...]
}
```

### Controller

```js
const pdfParse = require('pdf-parse');
const multer = require('multer');
const upload = multer({ limits: { fileSize: 50 * 1024 * 1024 } }); // 50MB

async function uploadAndAnalyzePDF(req, res) {
  const buffer = req.file.buffer;

  // 1. Trích xuất text từ PDF
  const pdfData = await pdfParse(buffer);
  const fullText = pdfData.text;

  // 2. Dùng AI trích xuất metadata
  const metadata = await extractMetadataWithAI(fullText.substring(0, 5000));

  // 3. Tóm tắt
  const summary = await summarizeWithAI(fullText.substring(0, 10000));

  // 4. Sinh keywords
  const keywords = await extractKeywordsWithAI(fullText.substring(0, 5000));

  // 5. Tìm paper tương tự (search bằng title + abstract trích được)
  const similar = await Paper.find({
    $text: { $search: metadata.title }
  }).limit(5).select('title authors publication_year doi');

  // 6. Trích references
  const references = extractReferences(fullText);

  res.json({ metadata, summary, keywords, similar_papers: similar, references });
}

async function extractMetadataWithAI(text) {
  const prompt = `Extract metadata from this paper text. Return JSON:
{ "title": "...", "authors": ["..."], "abstract": "..." }

Text: ${text}`;
  return JSON.parse(await callLLM(prompt));
}

async function summarizeWithAI(text) {
  const prompt = `Summarize this research paper in 5 sentences: ${text}`;
  return await callLLM(prompt);
}

async function extractKeywordsWithAI(text) {
  const prompt = `Extract 10 research keywords from this paper. Return as JSON array: ${text}`;
  return JSON.parse(await callLLM(prompt));
}

function extractReferences(text) {
  // Tìm DOI pattern trong text
  const doiRegex = /10\.\d{4,9}\/[-._;()/:A-Z0-9]+/gi;
  return [...new Set(text.match(doiRegex) || [])];
}
```

---

## Frontend

```jsx
function PDFUpload() {
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);

  const handleUpload = async (e) => {
    const file = e.target.files[0];
    if (!file) return;

    setLoading(true);
    const formData = new FormData();
    formData.append('file', file);

    const res = await api.post('/api/papers/upload-pdf', formData);
    setResult(res.data);
    setLoading(false);
  };

  return (
    <div>
      <input type="file" accept=".pdf" onChange={handleUpload} />
      {loading && <p>🔄 Đang phân tích PDF...</p>}
      {result && (
        <div>
          <h3>{result.metadata.title}</h3>
          <p><strong>Summary:</strong> {result.summary}</p>
          <p><strong>Keywords:</strong> {result.keywords.join(', ')}</p>
          <h4>Similar Papers:</h4>
          <ul>
            {result.similar_papers.map(p => <li key={p._id}>{p.title}</li>)}
          </ul>
        </div>
      )}
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Upload PDF → trích xuất text thành công
- [ ] AI trả về metadata (title, authors, abstract)
- [ ] Summary có ý nghĩa
- [ ] Keywords liên quan đến nội dung
- [ ] Similar papers tìm đúng
- [ ] References (DOI) trích xuất đúng

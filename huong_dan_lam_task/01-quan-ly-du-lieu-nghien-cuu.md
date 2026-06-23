# 📦 Chức năng 1: Quản lý Dữ liệu Nghiên cứu (Research Data Management)

> **Loại:** Nền tảng (Foundation) | **Priority:** 🔴 HIGH
> **Liên quan:** ISSUE-001, ISSUE-003 | **FR:** FR-001, FR-002

---

## 1.1 Thu thập dữ liệu (Data Collection)

### Mô tả
Crawl metadata bài báo từ 6 nguồn học thuật, đồng bộ định kỳ và cập nhật bài mới.

### Nguồn dữ liệu

| Nguồn | API Type | Rate Limit | Ghi chú |
|---|---|---|---|
| Semantic Scholar | REST | 100 req/5min (free) | Có academic API key tăng limit |
| OpenAlex | REST | Không limit (polite pool) | Thêm `mailto` vào header |
| Crossref | REST | 50 req/s (với key) | Dùng `Crossref-Plus-API-Token` |
| arXiv | OAI-PMH / REST | 1 req/3s | XML format, cần parse |
| IEEE Xplore | REST | 200 req/day (free) | Cần API key |
| ACM Digital Library | REST / Scraping | Hạn chế | Cân nhắc dùng Crossref thay thế |

### Bước thực hiện

```
src/services/etl/
  connectors/
    openalex.connector.js
    semanticscholar.connector.js
    crossref.connector.js
    arxiv.connector.js
    ieee.connector.js
    acm.connector.js
  etl.manager.js          ← orchestrate tất cả connectors
```

**Mỗi connector phải implement interface chung:**

```js
class BaseConnector {
  async fetch(query, options) { }     // Lấy papers
  normalize(rawData) { }              // Chuẩn hóa về schema chung
  async syncIncremental(since) { }    // Chỉ lấy papers mới từ ngày `since`
}
```

**Ví dụ OpenAlex connector:**

```js
const axios = require('axios');

class OpenAlexConnector extends BaseConnector {
  constructor() {
    this.baseUrl = 'https://api.openalex.org';
  }

  async fetch(query, { page = 1, perPage = 50 }) {
    const res = await axios.get(`${this.baseUrl}/works`, {
      params: {
        search: query,
        page,
        per_page: perPage,
        mailto: process.env.ADMIN_EMAIL // polite pool
      }
    });
    return res.data.results.map(r => this.normalize(r));
  }

  normalize(raw) {
    return {
      doi: raw.doi?.replace('https://doi.org/', ''),
      title: raw.title,
      abstract: raw.abstract_inverted_index
        ? this._reconstructAbstract(raw.abstract_inverted_index)
        : null,
      keywords: raw.keywords?.map(k => k.keyword) || [],
      authors: raw.authorships?.map(a => ({
        name: a.author?.display_name,
        affiliation: a.institutions?.[0]?.display_name,
        orcid: a.author?.orcid
      })) || [],
      publication_year: raw.publication_year,
      source_venue: {
        name: raw.primary_location?.source?.display_name,
        type: raw.primary_location?.source?.type,
        issn: raw.primary_location?.source?.issn_l
      },
      data_source: 'openalex',
      fields_of_study: raw.concepts?.map(c => c.display_name) || [],
      citation_count: raw.cited_by_count || 0,
      open_access_url: raw.open_access?.oa_url,
      external_ids: {
        openalex_id: raw.id
      }
    };
  }
}
```

**Đồng bộ định kỳ (Cron job):**

```js
const cron = require('node-cron');

// Chạy lúc 2AM mỗi ngày
cron.schedule('0 2 * * *', async () => {
  const sources = await DataSource.find({ is_active: true });
  for (const source of sources) {
    await DataSource.findByIdAndUpdate(source._id, { last_sync_status: 'running' });
    try {
      const connector = getConnector(source.name);
      const newPapers = await connector.syncIncremental(source.last_sync_at);
      // ... insert vào DB
      await DataSource.findByIdAndUpdate(source._id, {
        last_sync_at: new Date(),
        last_sync_status: 'success',
        'sync_stats.new_added': newPapers.length
      });
    } catch (err) {
      await DataSource.findByIdAndUpdate(source._id, { last_sync_status: 'failed' });
    }
  }
});
```

---

## 1.2 Import dữ liệu (Data Import)

### Mô tả
Cho phép user upload file để import bài báo thủ công.

### Định dạng hỗ trợ

| Format | Thư viện | Ghi chú |
|---|---|---|
| BibTeX | `bibtex-parse-js` | Phổ biến nhất, export từ Google Scholar |
| RIS | `ris-parser` | Export từ Scopus, Web of Science |
| CSV | `csv-parser` | Custom template |
| PDF | `pdf-parse` + LLM | Trích xuất metadata từ full-text |

### API

```
POST /api/papers/import
  Content-Type: multipart/form-data
  Body: file (bibtex|ris|csv|pdf)
  Response: { imported: 15, duplicates: 3, errors: 1 }
```

### Bước thực hiện

```js
// controllers/import.controller.js
const multer = require('multer');
const bibtex = require('bibtex-parse-js');

async function importBibtex(req, res) {
  const content = req.file.buffer.toString();
  const entries = bibtex.toJSON(content);

  let imported = 0, duplicates = 0;
  for (const entry of entries) {
    const paper = normalizeBibtex(entry);
    try {
      await Paper.create(paper);
      imported++;
    } catch (err) {
      if (err.code === 11000) duplicates++; // duplicate DOI
      else throw err;
    }
  }
  res.json({ imported, duplicates });
}
```

---

## 1.3 Làm sạch dữ liệu (Data Cleaning)

### Mô tả
Đảm bảo chất lượng dữ liệu trong Research Corpus.

### Logic xử lý

| Vấn đề | Giải pháp |
|---|---|
| **Bài trùng** | Dedup theo `doi` (unique sparse index) + fuzzy match title |
| **Tên tác giả** | Chuẩn hóa: `"J. Smith"` → `"John Smith"` (dùng ORCID khi có) |
| **Tên hội nghị** | Mapping: `"CVPR"`, `"IEEE CVPR"`, `"CVPR 2023"` → `"CVPR"` |
| **Keywords** | Lowercase, trim, loại bỏ duplicate, merge synonyms |

### Bước thực hiện

```js
// services/cleaning.service.js

// 1. Dedup theo DOI
async function deduplicateByDoi() {
  const pipeline = [
    { $group: { _id: '$doi', count: { $sum: 1 }, ids: { $push: '$_id' } } },
    { $match: { count: { $gt: 1 }, _id: { $ne: null } } }
  ];
  const duplicates = await Paper.aggregate(pipeline);
  for (const dup of duplicates) {
    // Giữ bản mới nhất, merge external_ids từ bản cũ
    const [keep, ...remove] = dup.ids;
    await Paper.deleteMany({ _id: { $in: remove } });
  }
}

// 2. Chuẩn hóa keyword
function normalizeKeyword(keyword) {
  return keyword.trim().toLowerCase()
    .replace(/\s+/g, ' ')            // multi space → single
    .replace(/['']/g, "'");          // smart quotes
}
```

---

## 1.4 Quản lý Metadata

### Schema `papers` — đầy đủ các trường

| Trường | Mô tả | Bắt buộc |
|---|---|---|
| `title` | Tiêu đề bài báo | ✅ |
| `abstract` | Tóm tắt | |
| `authors[]` | `{name, affiliation, orcid}` | ✅ |
| `publication_year` | Năm xuất bản | ✅ |
| `doi` | Digital Object Identifier | (sparse) |
| `citation_count` | Số lần trích dẫn | |
| `keywords[]` | Từ khóa | |
| `fields_of_study[]` | Lĩnh vực | |
| `source_venue` | `{name, type, issn}` — journal/conference | |
| `open_access_url` | Link đọc miễn phí | |
| `external_ids` | ID từ các nguồn: openalex, s2, arxiv... | |
| `data_source` | Nguồn thu thập | ✅ |

---

## Checklist kiểm tra

- [ ] OpenAlex connector: fetch 100 papers → insert đúng vào DB
- [ ] Dedup: insert cùng DOI 2 lần → chỉ 1 document
- [ ] Import BibTeX: upload file → papers xuất hiện trong DB
- [ ] Cron job: chạy đúng giờ, cập nhật `data_sources.sync_stats`
- [ ] Author normalization: `"J. Smith"` thống nhất khi có ORCID

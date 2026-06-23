# 📊 Chức năng 12: Báo cáo và Xuất dữ liệu

> **Loại:** Export Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** FR-013

---

## Mô tả

Xuất dữ liệu thống kê và bài báo ra các format: PDF, Excel, CSV, BibTeX, RIS.

---

## Backend

### Packages cần cài

```
npm install json2csv pdfkit bibtex-parse-js
```

### API Endpoints

```
GET /api/export/papers?format=csv&q=LLM&year_from=2023
GET /api/export/papers?format=bibtex&ids=id1,id2,id3
GET /api/export/report?type=trend&field=AI&format=pdf
GET /api/export/report?type=gap&field=AI&format=pdf
```

### Controller

```js
const { Parser: CsvParser } = require('json2csv');
const PDFDocument = require('pdfkit');

// Xuất papers ra CSV
async function exportPapersCSV(req, res) {
  const { q, year_from, year_to, ids } = req.query;

  let papers;
  if (ids) {
    papers = await Paper.find({ _id: { $in: ids.split(',') } });
  } else {
    const query = {};
    if (q) query.$text = { $search: q };
    if (year_from) query.publication_year = { $gte: parseInt(year_from) };
    papers = await Paper.find(query).limit(1000);
  }

  const fields = ['title', 'doi', 'publication_year', 'citation_count',
    'data_source', 'fields_of_study'];
  const csv = new CsvParser({ fields }).parse(
    papers.map(p => ({
      ...p.toObject(),
      fields_of_study: p.fields_of_study?.join('; '),
      authors: p.authors?.map(a => a.name).join('; ')
    }))
  );

  res.setHeader('Content-Type', 'text/csv');
  res.setHeader('Content-Disposition', 'attachment; filename=papers.csv');
  res.send(csv);
}

// Xuất papers ra BibTeX
async function exportBibTeX(req, res) {
  const { ids } = req.query;
  const papers = await Paper.find({ _id: { $in: ids.split(',') } });

  let bibtex = '';
  for (const p of papers) {
    const key = p.doi?.replace(/[^a-zA-Z0-9]/g, '_') || p._id;
    bibtex += `@article{${key},\n`;
    bibtex += `  title = {${p.title}},\n`;
    bibtex += `  author = {${p.authors?.map(a => a.name).join(' and ')}},\n`;
    bibtex += `  year = {${p.publication_year}},\n`;
    if (p.doi) bibtex += `  doi = {${p.doi}},\n`;
    if (p.source_venue?.name) bibtex += `  journal = {${p.source_venue.name}},\n`;
    bibtex += `}\n\n`;
  }

  res.setHeader('Content-Type', 'application/x-bibtex');
  res.setHeader('Content-Disposition', 'attachment; filename=papers.bib');
  res.send(bibtex);
}

// Xuất report PDF
async function exportTrendReport(req, res) {
  const { field } = req.query;
  const topics = await Topic.find({ field_of_study: field })
    .sort({ growth_rate: -1 }).limit(20);

  const doc = new PDFDocument();
  res.setHeader('Content-Type', 'application/pdf');
  res.setHeader('Content-Disposition', 'attachment; filename=trend_report.pdf');
  doc.pipe(res);

  doc.fontSize(20).text(`Trend Report: ${field}`, { align: 'center' });
  doc.moveDown();

  for (const topic of topics) {
    doc.fontSize(14).text(`${topic.name}`);
    doc.fontSize(10).text(`Growth: ${topic.growth_rate}% | Gap Score: ${topic.gap_score}`);
    doc.moveDown(0.5);
  }

  doc.end();
}
```

---

## Frontend

```jsx
function ExportButtons({ query, selectedIds }) {
  const handleExport = (format) => {
    const params = new URLSearchParams({ format, ...query });
    if (selectedIds?.length) params.set('ids', selectedIds.join(','));
    window.open(`/api/export/papers?${params}`);
  };

  return (
    <div className="export-buttons">
      <button onClick={() => handleExport('csv')}>📊 CSV</button>
      <button onClick={() => handleExport('bibtex')}>📚 BibTeX</button>
      <button onClick={() => handleExport('ris')}>📋 RIS</button>
      <button onClick={() => handleExport('pdf')}>📄 PDF</button>
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Export CSV → file tải về, mở bằng Excel đúng cột
- [ ] Export BibTeX → import được vào Mendeley/Zotero
- [ ] Export PDF report → có tiêu đề, bảng thống kê
- [ ] Export với filter → chỉ chứa papers đúng điều kiện

# 🤖 Chức năng 14: AI Assistant

> **Loại:** AI Feature | **Priority:** 🟡 MEDIUM
> **Liên quan:** FR-009

---

## Mô tả

Trợ lý AI hỗ trợ nhà nghiên cứu:
- Trả lời câu hỏi về xu hướng nghiên cứu
- Gợi ý từ khóa mở rộng
- Tóm tắt bài báo
- So sánh nhiều bài báo
- Giải thích thuật ngữ
- Đề xuất hướng nghiên cứu mới

---

## Backend

### Packages

```
npm install openai   # hoặc @google/generative-ai cho Gemini
```

### Architecture — RAG (Retrieval-Augmented Generation)

```
User question
    ↓
1. Search relevant papers (MongoDB text search)
    ↓
2. Build context từ abstracts tìm được
    ↓
3. Gửi question + context cho LLM
    ↓
4. Trả về câu trả lời có nguồn trích dẫn
```

### Service

```js
// services/ai.assistant.js
const OpenAI = require('openai');
const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

async function askAssistant(question, userId) {
  // 1. Tìm papers liên quan để làm context
  const relevantPapers = await Paper.find(
    { $text: { $search: question } },
    { score: { $meta: 'textScore' } }
  ).sort({ score: { $meta: 'textScore' } })
   .limit(5)
   .select('title abstract authors publication_year doi');

  // 2. Build context
  const context = relevantPapers.map((p, i) =>
    `[${i + 1}] "${p.title}" (${p.publication_year}) - ${p.abstract?.substring(0, 300)}`
  ).join('\n\n');

  // 3. Build prompt
  const systemPrompt = `You are a research assistant for the Scientific Research Trend Tracking System.
Answer questions about research trends, explain terminology, and suggest research directions.
Use the provided paper context when relevant. Cite papers using [1], [2], etc.
Respond in the same language as the user's question.`;

  const userPrompt = `Context papers:\n${context}\n\nQuestion: ${question}`;

  // 4. Call LLM
  const completion = await openai.chat.completions.create({
    model: 'gpt-4o-mini',
    messages: [
      { role: 'system', content: systemPrompt },
      { role: 'user', content: userPrompt }
    ],
    temperature: 0.7,
    max_tokens: 1000
  });

  const answer = completion.choices[0].message.content;

  // 5. Ghi search history
  if (userId) {
    await SearchHistory.create({
      user_id: userId,
      query_text: `[AI] ${question}`,
      result_count: relevantPapers.length,
      searched_at: new Date()
    });
  }

  return {
    answer,
    sources: relevantPapers.map(p => ({
      title: p.title,
      year: p.publication_year,
      doi: p.doi
    }))
  };
}
```

### Các loại câu hỏi hỗ trợ

```js
// Tóm tắt 1 bài báo
async function summarizePaper(paperId) {
  const paper = await Paper.findById(paperId);
  const prompt = `Summarize this research paper in 5 bullet points:
Title: ${paper.title}
Abstract: ${paper.abstract}`;
  return await callLLM(prompt);
}

// So sánh nhiều bài báo
async function comparePapers(paperIds) {
  const papers = await Paper.find({ _id: { $in: paperIds } })
    .select('title abstract authors publication_year');

  const papersText = papers.map((p, i) =>
    `Paper ${i + 1}: "${p.title}" (${p.publication_year})\nAbstract: ${p.abstract}`
  ).join('\n\n');

  const prompt = `Compare these research papers. Highlight:
1. Common themes
2. Key differences in approach
3. Which paper is more recent/comprehensive

${papersText}`;
  return await callLLM(prompt);
}

// Giải thích thuật ngữ
async function explainTerm(term) {
  const prompt = `Explain the research term "${term}" in simple terms.
Include: definition, key concepts, and how it relates to current research trends.
If applicable, mention recent advances and open problems.`;
  return await callLLM(prompt);
}

// Đề xuất hướng nghiên cứu
async function suggestResearchDirection(interests) {
  // Lấy trending topics
  const trending = await Topic.find({ growth_rate: { $gt: 50 } })
    .sort({ growth_rate: -1 }).limit(10).select('name growth_rate gap_score');

  const prompt = `Based on:
- User interests: ${interests.join(', ')}
- Current trending topics: ${trending.map(t => `${t.name} (+${t.growth_rate}%)`).join(', ')}

Suggest 3 specific research directions that combine user interests with trending topics.
For each, provide: title, rationale, potential impact, and estimated competition level.`;
  return await callLLM(prompt);
}
```

### API Endpoints

```
POST /api/ai/ask              → { question } → câu trả lời + sources
POST /api/ai/summarize        → { paper_id } → tóm tắt
POST /api/ai/compare          → { paper_ids: [] } → so sánh
POST /api/ai/explain          → { term } → giải thích
POST /api/ai/suggest          → { interests: [] } → đề xuất hướng
```

---

## Frontend — Chat UI

```jsx
function AIAssistant() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);

  const handleSend = async () => {
    if (!input.trim()) return;

    const userMsg = { role: 'user', content: input };
    setMessages(prev => [...prev, userMsg]);
    setInput('');
    setLoading(true);

    const res = await api.post('/api/ai/ask', { question: input });

    const aiMsg = {
      role: 'assistant',
      content: res.data.answer,
      sources: res.data.sources
    };
    setMessages(prev => [...prev, aiMsg]);
    setLoading(false);
  };

  return (
    <div className="ai-chat">
      <div className="messages">
        {messages.map((msg, i) => (
          <div key={i} className={`message ${msg.role}`}>
            <p>{msg.content}</p>
            {msg.sources && (
              <div className="sources">
                <strong>Sources:</strong>
                {msg.sources.map((s, j) => (
                  <a key={j} href={`/papers?doi=${s.doi}`}>
                    [{j + 1}] {s.title} ({s.year})
                  </a>
                ))}
              </div>
            )}
          </div>
        ))}
        {loading && <div className="message assistant">🔄 Đang suy nghĩ...</div>}
      </div>

      <div className="input-area">
        <input value={input} onChange={e => setInput(e.target.value)}
               onKeyDown={e => e.key === 'Enter' && handleSend()}
               placeholder="Hỏi về xu hướng nghiên cứu..." />
        <button onClick={handleSend}>📤</button>
      </div>

      {/* Quick actions */}
      <div className="quick-actions">
        <button onClick={() => setInput('What are the top trending topics in AI?')}>
          📈 Trending Topics
        </button>
        <button onClick={() => setInput('Explain Retrieval-Augmented Generation')}>
          📖 Giải thích thuật ngữ
        </button>
        <button onClick={() => setInput('Suggest research directions for LLM')}>
          💡 Gợi ý hướng nghiên cứu
        </button>
      </div>
    </div>
  );
}
```

---

## Checklist kiểm tra

- [ ] Hỏi "What is trending in NLP?" → trả lời có trích dẫn sources
- [ ] Tóm tắt paper → 5 bullet points có ý nghĩa
- [ ] So sánh 2 papers → highlight differences
- [ ] Giải thích "RAG" → định nghĩa + ứng dụng
- [ ] Gợi ý hướng nghiên cứu → dựa trên trending topics thật
- [ ] Chat history hiển thị đúng (user/assistant)

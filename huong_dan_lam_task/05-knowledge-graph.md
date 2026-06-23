# 🌐 Chức năng 5: Phân tích Mối quan hệ (Knowledge Graph)

> **Loại:** Visualization | **Priority:** 🔴 HIGH
> **Liên quan:** FR-007 | **Chức năng trực quan**

---

## Mô tả

Hiển thị mối quan hệ phân cấp giữa các chủ đề nghiên cứu dạng đồ thị.

```
AI ├── Machine Learning
   │   ├── Deep Learning
   │   │   ├── CNN
   │   │   ├── Transformer
   │   │   └── Mamba
   │   └── Reinforcement Learning

Click vào "Transformer" →
Transformer ├── BERT ├── GPT ├── ViT ├── T5 ├── Llama ├── Qwen
```

---

## Backend — Xây dựng Knowledge Graph

### Dựa trên `topics` collection (self-referencing hierarchy)

```js
// services/knowledgeGraph.service.js

// Lấy cây topic từ root
async function getTopicTree(rootTopicId, depth = 2) {
  const root = await Topic.findById(rootTopicId)
    .select('name growth_rate gap_score paper_count');

  if (!root || depth === 0) return root;

  const children = await Topic.find({ parent_topic_id: rootTopicId })
    .select('name growth_rate gap_score');

  const childrenWithSub = await Promise.all(
    children.map(async (child) => {
      const subtree = await getTopicTree(child._id, depth - 1);
      return { ...child.toObject(), children: subtree?.children || [] };
    })
  );

  return { ...root.toObject(), children: childrenWithSub };
}

// Lấy related topics (graph dạng phẳng, cho D3 force graph)
async function getTopicGraph(topicId, maxNodes = 30) {
  const visited = new Set();
  const nodes = [];
  const links = [];

  async function traverse(id, depth) {
    if (visited.has(id.toString()) || nodes.length >= maxNodes) return;
    visited.add(id.toString());

    const topic = await Topic.findById(id)
      .select('name related_topic_ids parent_topic_id growth_rate gap_score');
    if (!topic) return;

    nodes.push({
      id: topic._id,
      name: topic.name,
      growth_rate: topic.growth_rate,
      gap_score: topic.gap_score
    });

    // Link to parent
    if (topic.parent_topic_id) {
      links.push({ source: topic.parent_topic_id, target: topic._id, type: 'parent' });
    }

    // Link to related
    for (const relId of topic.related_topic_ids || []) {
      links.push({ source: topic._id, target: relId, type: 'related' });
      if (depth > 0) await traverse(relId, depth - 1);
    }
  }

  await traverse(topicId, 2);
  return { nodes, links };
}
```

### API Endpoints

```
GET /api/topics/:id/tree?depth=2         → cây phân cấp
GET /api/topics/:id/graph?maxNodes=30    → graph cho D3
GET /api/topics/roots                     → danh sách topic gốc (AI, Biology, Physics...)
```

---

## Frontend — Hiển thị Graph

### Cách 1: Tree View (đơn giản)

```jsx
function TopicTree({ node, onSelect }) {
  const [expanded, setExpanded] = useState(false);

  return (
    <div style={{ marginLeft: 20 }}>
      <div onClick={() => { setExpanded(!expanded); onSelect(node); }}>
        {node.children?.length > 0 ? (expanded ? '▼' : '▶') : '•'}
        <strong>{node.name}</strong>
        <span style={{ color: node.growth_rate > 50 ? 'green' : 'gray' }}>
          {node.growth_rate > 0 ? `+${node.growth_rate}%` : `${node.growth_rate}%`}
        </span>
      </div>
      {expanded && node.children?.map(child => (
        <TopicTree key={child._id} node={child} onSelect={onSelect} />
      ))}
    </div>
  );
}
```

### Cách 2: Force-directed Graph (D3.js)

Cài: `npm install d3`

```jsx
import { useEffect, useRef } from 'react';
import * as d3 from 'd3';

function KnowledgeGraph({ data }) {
  const svgRef = useRef();

  useEffect(() => {
    const svg = d3.select(svgRef.current);
    const width = 800, height = 600;

    const simulation = d3.forceSimulation(data.nodes)
      .force('link', d3.forceLink(data.links).id(d => d.id).distance(80))
      .force('charge', d3.forceManyBody().strength(-200))
      .force('center', d3.forceCenter(width / 2, height / 2));

    // Draw links
    const link = svg.selectAll('line')
      .data(data.links).join('line')
      .attr('stroke', d => d.type === 'parent' ? '#999' : '#ddd');

    // Draw nodes
    const node = svg.selectAll('circle')
      .data(data.nodes).join('circle')
      .attr('r', d => Math.max(5, Math.min(20, d.growth_rate / 10)))
      .attr('fill', d => d.gap_score > 0.7 ? '#ff6b6b' : '#4ecdc4')
      .call(d3.drag()
        .on('start', dragStarted)
        .on('drag', dragged)
        .on('end', dragEnded));

    // Labels
    const label = svg.selectAll('text')
      .data(data.nodes).join('text')
      .text(d => d.name).attr('font-size', 10);

    simulation.on('tick', () => {
      link.attr('x1', d => d.source.x).attr('y1', d => d.source.y)
          .attr('x2', d => d.target.x).attr('y2', d => d.target.y);
      node.attr('cx', d => d.x).attr('cy', d => d.y);
      label.attr('x', d => d.x + 12).attr('y', d => d.y + 4);
    });
  }, [data]);

  return <svg ref={svgRef} width={800} height={600} />;
}
```

---

## Checklist kiểm tra

- [ ] `GET /api/topics/roots` → trả về các topic gốc
- [ ] `GET /api/topics/:id/tree?depth=2` → cây phân cấp đúng
- [ ] Click node → mở rộng sub-topics
- [ ] D3 graph render đúng node + link
- [ ] Node size tỷ lệ với growth_rate
- [ ] Node màu đỏ = gap_score cao (research opportunity)

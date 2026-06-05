# 🗃️ Vector Databases

> Vector databases are the backbone of modern AI applications — they store and search embeddings at scale, enabling semantic search, RAG, and recommendation systems.

---

## 📖 What is a Vector Database?

A **vector database** is a specialized database designed to store, index, and query **high-dimensional vectors** (embeddings) efficiently.

Unlike traditional databases that find exact matches, vector databases find **approximate nearest neighbors (ANN)** — vectors that are semantically closest to a query vector.

---

## 🔑 Why Not Just Use PostgreSQL or MongoDB?

| Feature | Traditional DB | Vector DB |
|---------|---------------|----------|
| Query type | Exact match, range | Nearest neighbor (similarity) |
| Data | Structured rows/docs | High-dimensional float arrays |
| Performance | Fast for exact queries | Fast for similarity queries |
| Scale | Billions of rows | Hundreds of millions of vectors |
| Dimension support | No native support | Optimized for 100s–1000s of dims |

> PostgreSQL with **pgvector** extension is a good option for smaller scale (< 1M vectors). For production AI apps, dedicated vector databases are preferred.

---

## 🔑 Popular Vector Databases

| Database | Hosting | Strengths | Weaknesses |
|----------|---------|-----------|------------|
| **Pinecone** | Managed cloud | Easiest to start, great docs | Paid, no self-host |
| **Weaviate** | Self-host + cloud | Hybrid search, GraphQL | Complex setup |
| **ChromaDB** | Local + cloud | Free, easy dev setup | Less scalable |
| **Qdrant** | Self-host + cloud | Fast, Rust-based | Newer ecosystem |
| **Milvus** | Self-host | Enterprise-grade | Heavy infrastructure |
| **pgvector** | PostgreSQL ext | Use your existing DB | Slower at scale |
| **FAISS** | In-memory library | Meta, very fast | No persistence |

**For development:** Start with **ChromaDB** (free, local, easy)
**For production:** Use **Pinecone** or **Weaviate**

---

## 🔧 ChromaDB (Development)

```python
import chromadb
from chromadb.utils import embedding_functions

# Initialize client
client = chromadb.PersistentClient(path="./chroma_storage")

# Use OpenAI embeddings
openai_ef = embedding_functions.OpenAIEmbeddingFunction(
    api_key="your-api-key",
    model_name="text-embedding-3-small"
)

# Create a collection
collection = client.get_or_create_collection(
    name="farming_docs",
    embedding_function=openai_ef,
    metadata={"hnsw:space": "cosine"}  # Use cosine distance
)

# Add documents
collection.add(
    documents=[
        "Wheat grows best in cool, dry climates",
        "Rice requires standing water during growth",
        "Sugarcane needs tropical temperatures"
    ],
    ids=["doc1", "doc2", "doc3"],
    metadatas=[
        {"crop": "wheat", "source": "farming_guide.pdf"},
        {"crop": "rice", "source": "farming_guide.pdf"},
        {"crop": "sugarcane", "source": "farming_guide.pdf"}
    ]
)

# Query
results = collection.query(
    query_texts=["What crops need lots of water?"],
    n_results=2,
    include=["documents", "distances", "metadatas"]
)

for doc, dist, meta in zip(results['documents'][0], results['distances'][0], results['metadatas'][0]):
    print(f"Distance: {dist:.3f} | {meta['crop']} | {doc}")
```

---

## 🔧 Pinecone (Production)

```python
from pinecone import Pinecone, ServerlessSpec
from openai import OpenAI

# Initialize
pc = Pinecone(api_key="your-pinecone-api-key")
openai_client = OpenAI(api_key="your-openai-api-key")

# Create index (one-time)
pc.create_index(
    name="gramsathi-docs",
    dimension=1536,           # text-embedding-3-small output dim
    metric="cosine",
    spec=ServerlessSpec(cloud="aws", region="us-east-1")
)

index = pc.Index("gramsathi-docs")

# Upsert vectors
def embed_and_upsert(docs):
    vectors = []
    for i, doc in enumerate(docs):
        emb = openai_client.embeddings.create(
            input=doc['text'],
            model="text-embedding-3-small"
        ).data[0].embedding
        
        vectors.append({
            "id": f"doc_{i}",
            "values": emb,
            "metadata": {"text": doc['text'], "source": doc['source']}
        })
    
    index.upsert(vectors=vectors)

# Query
def semantic_search(query, top_k=5):
    query_emb = openai_client.embeddings.create(
        input=query,
        model="text-embedding-3-small"
    ).data[0].embedding
    
    results = index.query(
        vector=query_emb,
        top_k=top_k,
        include_metadata=True
    )
    
    return [(r.score, r.metadata['text']) for r in results.matches]

results = semantic_search("government scheme for small farmers")
for score, text in results:
    print(f"Score: {score:.3f} | {text[:100]}")
```

---

## 🔑 Indexing Algorithms (How Vector DBs Work)

### HNSW (Hierarchical Navigable Small World)
- Most commonly used in modern vector DBs
- Graph-based structure: nodes connect to nearby nodes across layers
- Very fast search (O(log n))
- Works well in high dimensions
- Used by: Chroma, Qdrant, Weaviate

### IVF (Inverted File Index)
- Clusters vectors into cells; searches only nearest clusters
- Used by: FAISS, Pinecone
- Good for very large scale

### Flat (Brute Force)
- Compares query to every vector
- Perfect accuracy but slow: O(n)
- Only for small datasets (< 10K vectors)

---

## 🔑 Metadata Filtering

Filter results by metadata alongside vector similarity:

```python
# Pinecone: hybrid filter + vector search
results = index.query(
    vector=query_emb,
    top_k=5,
    filter={
        "$and": [
            {"state": {"$eq": "Uttar Pradesh"}},
            {"category": {"$in": ["wheat", "sugarcane"]}}
        ]
    },
    include_metadata=True
)

# ChromaDB: where filter
results = collection.query(
    query_texts=["water requirements"],
    n_results=3,
    where={"crop": "rice"},
    where_document={"$contains": "water"}  # Filter by document content
)
```

---

## 📊 Key Metrics

| Metric | Description |
|--------|-------------|
| **Latency** | Time per query (target: <100ms) |
| **Recall@K** | % of true nearest neighbors in top-K results |
| **QPS** | Queries per second (throughput) |
| **Index size** | Storage required for vectors |
| **Dimension** | Number of float values per vector |

---

## 🌍 Real-World Use Cases

| Use Case | DB Choice | Vectors | Why |
|----------|-----------|---------|-----|
| RAG chatbot | Pinecone / Chroma | Doc chunks | Fast retrieval |
| Semantic search | Weaviate | Products/articles | Hybrid search |
| Recommendation | Pinecone | User/item vectors | Scale |
| Duplicate detection | FAISS | Text embeddings | Speed |
| Image search | Qdrant | Image embeddings | Multimodal |

---

## ❓ Interview Questions

**Q: What is a vector database and why is it needed?**
> A vector database stores high-dimensional embeddings and enables similarity search (find nearest vectors). Traditional databases use exact matching; vector databases use approximate nearest neighbor (ANN) algorithms to find semantically similar items in milliseconds.

**Q: What is HNSW?**
> Hierarchical Navigable Small World — a graph-based index algorithm. Vectors are organized in layered graphs where each node connects to nearby neighbors. Search starts at the top layer (coarse) and drills down to fine-grained neighbors. It achieves O(log n) search time with high recall.

**Q: How would you choose between Pinecone, Weaviate, and ChromaDB?**
> ChromaDB for local development (free, easy setup). Pinecone for production if you want managed, scalable, no infrastructure. Weaviate for production if you need hybrid search (keyword + semantic) and prefer self-hosting or more control.

**Q: What is metadata filtering?**
> When doing a vector similarity search, you can additionally filter results by structured metadata fields (like date, category, author). This narrows the search to a subset of vectors, improving relevance and enabling multi-tenant applications.

---

## ✅ Revision Checklist

- [ ] Can I explain what a vector database is and why it's different from SQL/NoSQL?
- [ ] Can I set up ChromaDB locally and add/query vectors?
- [ ] Do I understand what HNSW is at a conceptual level?
- [ ] Can I explain what metadata filtering is and why it's useful?
- [ ] Do I know the trade-offs between Pinecone, Weaviate, and ChromaDB?
- [ ] Can I describe 3 real-world use cases for vector databases?

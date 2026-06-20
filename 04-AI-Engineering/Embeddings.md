# 🔢 Embeddings — Turning Words into Vectors

> Embeddings are how AI understands meaning. They're the bridge between human language and mathematical computation.

---

## 📖 What are Embeddings?

**Embeddings** are numerical representations (vectors) of text, images, or other data in a high-dimensional space, where **semantically similar items are close to each other**.

**Simple analogy:** Imagine a map where:
- "King" and "Queen" are close to each other
- "Paris" is to "France" as "Tokyo" is to "Japan"
- "Happy" and "Joyful" are neighbors

This is what embedding space looks like — meaning encoded as distance.

---

## 🔑 Core Concepts

### Why Embeddings Work

The famous example:
```
king - man + woman ≈ queen
```

This works because embeddings capture **semantic relationships** in vector space. Subtraction removes the "male" concept; addition adds the "female" concept.

### Dimensionality
- A text is represented as a vector of 768, 1536, or 3072 floating-point numbers
- Each dimension encodes some latent feature of meaning
- You rarely look at individual dimensions — the relative positions matter

### Cosine Similarity (The Key Metric)
```python
import numpy as np

def cosine_similarity(v1, v2):
    return np.dot(v1, v2) / (np.linalg.norm(v1) * np.linalg.norm(v2))

# Range: -1 (opposite) to 0 (unrelated) to 1 (identical meaning)
# cos_sim("cat", "dog") ≈ 0.85  (similar)
# cos_sim("cat", "rocket") ≈ 0.15  (different)
```

---

## 🔧 Generating Embeddings

### Using OpenAI Embeddings API
```python
from openai import OpenAI

client = OpenAI(api_key="your-api-key")

def get_embedding(text):
    response = client.embeddings.create(
        input=text,
        model="text-embedding-3-small"  # 1536 dimensions, cheap
    )
    return response.data[0].embedding

# Example: encode sentences
sentences = [
    "I love programming",
    "Coding is my passion",
    "I enjoy playing cricket"
]
embeddings = [get_embedding(s) for s in sentences]

# Calculate similarity
from numpy import dot
from numpy.linalg import norm

def cos_sim(a, b):
    return dot(a, b) / (norm(a) * norm(b))

print(cos_sim(embeddings[0], embeddings[1]))  # ~0.93 (similar)
print(cos_sim(embeddings[0], embeddings[2]))  # ~0.45 (different)
```

### Using HuggingFace Sentence Transformers (Free, Local)
```python
from sentence_transformers import SentenceTransformer, util

model = SentenceTransformer('all-MiniLM-L6-v2')  # 384 dims, fast

sentences = [
    "The dog is running in the park",
    "A canine is jogging outdoors",  # Similar meaning
    "Python is a programming language"  # Different
]

embeddings = model.encode(sentences)

# Pairwise similarity
cos_sim = util.cos_sim(embeddings[0], embeddings[1])
print(f"Similarity (dog/canine): {cos_sim.item():.2f}")  # ~0.82
```

---

## 🔑 Types of Embeddings

### Word Embeddings (Legacy)
- **Word2Vec** (2013): Predicts surrounding words; learns word relationships
- **GloVe** (2014): Global vectors; combines co-occurrence statistics
- **FastText** (2016): Handles out-of-vocabulary words via subwords
- **Limitation**: One embedding per word (can't handle ambiguity — "bank" river/money)

### Sentence/Document Embeddings (Modern)
- **SBERT (Sentence-BERT)**: Siamese BERT for sentence similarity — very fast
- **OpenAI ada-002 / text-embedding-3**: Production-grade, high accuracy
- **E5, BGE, Instructor**: Open-source competitors
- **Advantage**: Context-aware — "bank" in "river bank" ≠ "bank" in "bank account"

---

## 🔑 Embedding Use Cases

### 1. Semantic Search
```python
# Given a query, find the most similar documents
query = "How do I prevent crop disease?"
docs = [
    "Proper irrigation prevents most crop diseases",
    "Pest control techniques for wheat farmers",
    "Python programming basics"
]

query_emb = model.encode([query])
doc_embs = model.encode(docs)

scores = util.cos_sim(query_emb, doc_embs)[0]
top_k = scores.topk(2)  # Top 2 results

for idx, score in zip(top_k.indices, top_k.values):
    print(f"Score: {score:.3f} | {docs[idx]}")
```

### 2. Clustering Documents
```python
from sklearn.cluster import KMeans

# Embed customer feedback
feedback = ["Great product!", "Terrible service", "Amazing quality", "Poor customer support"]
embeddings = model.encode(feedback)

# Cluster into 2 groups (positive/negative)
kmeans = KMeans(n_clusters=2)
labels = kmeans.fit_predict(embeddings)
```

### 3. Recommendation System
```python
# Find movies similar to what user liked
user_liked = get_embedding("The Matrix — sci-fi action movie about simulation")
all_movies_embs = {title: get_embedding(desc) for title, desc in movies.items()}

# Find top 5 most similar
similarities = {title: cos_sim(user_liked, emb) for title, emb in all_movies_embs.items()}
top_5 = sorted(similarities.items(), key=lambda x: x[1], reverse=True)[:5]
```

---

## 🔑 Embedding Models Comparison

| Model | Dimensions | Free? | Quality | Speed |
|-------|-----------|-------|---------|-------|
| all-MiniLM-L6-v2 | 384 | ✅ Local | Good | ⚡ Fast |
| all-mpnet-base-v2 | 768 | ✅ Local | Better | Medium |
| text-embedding-3-small | 1536 | ❌ API | Excellent | Fast API |
| text-embedding-3-large | 3072 | ❌ API | Best | Slower API |
| bge-large-en-v1.5 | 1024 | ✅ Local | Excellent | Medium |

---

## 🌍 Real-World Embedding Applications

| Application | How Embeddings Help |
|------------|-------------------|
| **RAG systems** | Retrieve relevant document chunks semantically |
| **Semantic search** | "Show me docs about machine learning" finds "ML tutorials" |
| **Duplicate detection** | Find near-duplicate support tickets |
| **Recommendation** | "Users who liked X also liked Y" |
| **Content moderation** | Find semantically similar toxic content |
| **FAQ matching** | Match user question to closest FAQ entry |

---

## ❓ Interview Questions

**Q: What is an embedding and why is it useful?**
> An embedding is a numerical vector representation of text (or other data) where semantically similar inputs are represented by vectors that are close to each other in vector space. They're useful because they make text computable — you can measure similarity, cluster, search, and classify using vector math.

**Q: What is cosine similarity?**
> It measures the angle between two vectors, regardless of their magnitude. Range is -1 to 1 where 1 = identical direction (similar), 0 = orthogonal (unrelated), -1 = opposite. Used instead of Euclidean distance for text similarity because it handles vectors of different lengths well.

**Q: Why are sentence embeddings better than word embeddings for most tasks?**
> Word embeddings (Word2Vec) give one vector per word regardless of context — "bank" always has the same vector. Sentence/contextual embeddings (BERT, SBERT) understand context — "bank" in "river bank" vs "savings bank" gets different vectors.

**Q: How do embeddings connect to RAG?**
> In RAG: documents are embedded and stored in a vector database. When a query comes in, it's also embedded. The system retrieves the most similar document chunks (by cosine similarity) and passes them to the LLM as context.

---

## ✅ Revision Checklist

- [ ] Can I explain what an embedding is in simple terms?
- [ ] Can I explain cosine similarity and why it's used?
- [ ] Can I generate embeddings using OpenAI API or sentence-transformers?
- [ ] Can I implement semantic search using embeddings?
- [ ] Do I understand the difference between word and sentence embeddings?
- [ ] Can I name 3 real-world applications of embeddings?

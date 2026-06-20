# 🔍 RAG — Retrieval-Augmented Generation

> RAG is the most important AI Engineering pattern of 2024–2025. It's how you build AI apps that answer questions accurately from your own data.

---

## 📖 What is RAG?

**Retrieval-Augmented Generation (RAG)** is an architecture that enhances LLM responses by retrieving relevant information from external knowledge sources before generating an answer.

**The core problem RAG solves:**
- LLMs have a **knowledge cutoff** (stale data)
- LLMs **hallucinate** when they don't know something
- LLMs can't access **private/proprietary data**
- Context windows are **limited** (can't feed entire document)

**Solution:** Retrieve relevant context → Provide to LLM → Generate grounded answer.

---

## 🏗️ RAG Architecture

```
┌─────────────────────────────────────────────────────┐
│                  INDEXING (One-time)                │
│                                                     │
│  Documents → Chunking → Embedding → Vector Store   │
└─────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────┐
│               RETRIEVAL & GENERATION (Per Query)    │
│                                                     │
│  User Query → Embed Query → Vector Search           │
│      → Retrieve Top-K Chunks → Construct Prompt     │
│      → LLM Generates Answer → Return to User       │
└─────────────────────────────────────────────────────┘
```

---

## 🔑 Step-by-Step RAG Implementation

### Step 1: Load and Chunk Documents

```python
from langchain.document_loaders import PyPDFLoader, DirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

# Load documents
loader = PyPDFLoader("data/company_handbook.pdf")
documents = loader.load()

# Chunk documents
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,       # characters per chunk
    chunk_overlap=200,     # overlap to preserve context across chunks
    separators=["\n\n", "\n", " ", ""]
)
chunks = text_splitter.split_documents(documents)
print(f"Split into {len(chunks)} chunks")
```

### Step 2: Create Embeddings and Store in Vector DB

```python
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import Chroma

embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# Create and persist vector store
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    persist_directory="./chroma_db"
)

print(f"Stored {vectorstore._collection.count()} vectors")
```

### Step 3: Create a Retriever

```python
retriever = vectorstore.as_retriever(
    search_type="similarity",    # or "mmr" for diversity
    search_kwargs={"k": 5}       # retrieve top 5 chunks
)

# Test retrieval
query = "What is the company's leave policy?"
relevant_docs = retriever.get_relevant_documents(query)
for doc in relevant_docs:
    print(f"Score context: {doc.page_content[:200]}\n")
```

### Step 4: Build the RAG Chain

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from langchain.chains import RetrievalQA

# Custom prompt template
prompt = ChatPromptTemplate.from_template("""
You are a helpful assistant. Answer the question based ONLY on the provided context.
If the answer is not in the context, say "I don't have that information."

Context:
{context}

Question: {question}

Answer:
""")

llm = ChatOpenAI(model="gpt-4o", temperature=0)

rag_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type="stuff",    # Stuff all contexts into one prompt
    retriever=retriever,
    chain_type_kwargs={"prompt": prompt}
)

# Query
result = rag_chain.invoke({"query": "What is the company's parental leave policy?"})
print(result['result'])
```

### Step 5: Advanced RAG with Sources

```python
from langchain.chains import RetrievalQAWithSourcesChain

chain = RetrievalQAWithSourcesChain.from_chain_type(
    llm=llm,
    chain_type="stuff",
    retriever=retriever
)

result = chain.invoke({"question": "How many days of leave do employees get?"})
print(f"Answer: {result['answer']}")
print(f"Sources: {result['sources']}")
```

---

## 🔑 Chunking Strategies

| Strategy | When to Use | Pros | Cons |
|----------|------------|------|------|
| **Fixed size** | Simple docs | Predictable | Cuts sentences |
| **Recursive** | General text | Respects structure | May lose context |
| **Sentence-based** | Dense docs | Natural boundaries | Variable sizes |
| **Semantic** | Long docs | Best context | Complex, slow |
| **Markdown-aware** | Structured docs | Respects headers | Requires preprocessing |

**Chunk Overlap:** Always use 10–20% overlap to prevent information loss at chunk boundaries.

---

## 🔑 Retrieval Strategies

### Similarity Search
```python
# Most common — cosine similarity
docs = vectorstore.similarity_search(query, k=5)
```

### MMR (Maximal Marginal Relevance)
```python
# Balances relevance AND diversity (avoids returning very similar chunks)
docs = vectorstore.max_marginal_relevance_search(query, k=5, fetch_k=20)
```

### Hybrid Search (Keyword + Semantic)
```python
# Combine BM25 (keyword) with semantic search for better recall
from langchain.retrievers import EnsembleRetriever
from langchain.retrievers import BM25Retriever

bm25_retriever = BM25Retriever.from_documents(docs)
ensemble_retriever = EnsembleRetriever(
    retrievers=[bm25_retriever, vectorstore.as_retriever()],
    weights=[0.5, 0.5]
)
```

---

## ⚡ RAG Evaluation Metrics

| Metric | What It Measures | Tool |
|--------|-----------------|------|
| **Faithfulness** | Is the answer grounded in context? | RAGAS |
| **Answer Relevancy** | Does the answer address the question? | RAGAS |
| **Context Precision** | Are retrieved chunks relevant? | RAGAS |
| **Context Recall** | Are all relevant chunks retrieved? | RAGAS |

```python
from ragas import evaluate
from ragas.metrics import faithfulness, answer_relevancy

results = evaluate(
    dataset=test_dataset,
    metrics=[faithfulness, answer_relevancy]
)
print(results)
```

---

## 🆚 RAG vs Fine-Tuning

| Aspect | RAG | Fine-Tuning |
|--------|-----|-------------|
| **When data changes** | Perfect (re-index only) | Must retrain |
| **Transparency** | Can show sources | Black box |
| **Cost** | Low (inference + vector DB) | High (GPU compute) |
| **Hallucination** | Reduced (grounded) | Still possible |
| **Custom behavior** | Limited | Full control |
| **Best for** | Knowledge base Q&A, doc search | Style, format, domain behavior |

**General rule:** Start with RAG. Fine-tune only if you need behavior changes.

---

## 🌍 Real-World RAG Use Cases

| Use Case | Data Source | Notes |
|----------|------------|-------|
| Customer support bot | Product docs, FAQs | Reduce hallucinations |
| Legal document Q&A | Contracts, policies | Need precise citations |
| Medical assistant | Clinical guidelines | Needs high faithfulness |
| Code documentation helper | Codebase + docs | Use code-aware chunking |
| Internal knowledge base | Company wiki, Confluence | Multi-document retrieval |

---

## ❓ Interview Questions

**Q: What is RAG and why is it used?**
> RAG retrieves relevant documents from a knowledge base and passes them as context to an LLM before generating an answer. It's used because LLMs have knowledge cutoffs, can hallucinate, and can't access private data. RAG grounds answers in real, up-to-date information.

**Q: What is chunking and why does it matter?**
> Documents are too large to fit in an LLM's context window, so they're split into smaller chunks. Chunk size and overlap affect retrieval quality. Too small = missing context; too large = noisy context. Overlap prevents cutting off important information.

**Q: What is the difference between similarity search and MMR?**
> Similarity search returns the top-k most similar chunks to the query — may return duplicates or near-duplicates. MMR (Maximal Marginal Relevance) balances relevance with diversity, ensuring retrieved chunks complement each other rather than repeat information.

**Q: How would you evaluate a RAG system?**
> Using RAGAS metrics: Faithfulness (is the answer grounded in the context?), Answer Relevancy (does it answer the question?), Context Precision (are retrieved chunks relevant?), Context Recall (did we retrieve all relevant chunks?).

---

## ✅ Revision Checklist

- [ ] Can I explain what RAG is and why it's used?
- [ ] Can I describe the full RAG pipeline (indexing + retrieval)?
- [ ] Can I explain chunking strategies and trade-offs?
- [ ] Can I implement a basic RAG pipeline using LangChain?
- [ ] Do I understand similarity search vs MMR?
- [ ] Can I compare RAG vs fine-tuning and know when to use each?

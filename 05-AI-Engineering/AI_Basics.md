# 🤖 AI Basics — Foundations of Artificial Intelligence

> A practical, application-focused introduction to AI — no math PhD required.

---

## 📖 What is AI?

**Artificial Intelligence (AI)** is the ability of machines to perform tasks that typically require human intelligence — like understanding language, recognizing images, making decisions, and generating content.

**Key distinction for your interviews:**
- **AI** = Broad field (anything that makes machines smart)
- **ML** = Subset of AI (systems that learn from data)
- **Deep Learning** = Subset of ML (neural networks with many layers)
- **Generative AI** = AI that creates new content (text, images, code)

---

## 🏗️ The AI Stack

```
┌───────────────────────────────────────┐
│     AI Applications (Chatbots, etc.) │ ← You build here
├───────────────────────────────────────┤
│     Frameworks (LangChain, LlamaIndex)│ ← You orchestrate here
├───────────────────────────────────────┤
│     Foundation Models (GPT, Claude)   │ ← You call via API
├───────────────────────────────────────┤
│     Infrastructure (GPUs, Cloud)      │ ← You mostly don't touch
└───────────────────────────────────────┘
```

> **AI Engineering** focuses on the top two layers — building applications on top of foundation models using orchestration frameworks.

---

## 🔑 Core AI Concepts

### 1. Types of ML

| Type | Description | Example |
|------|-------------|---------|
| **Supervised Learning** | Learn from labeled data (input → output pairs) | Spam detection, image classification |
| **Unsupervised Learning** | Find patterns in unlabeled data | Customer segmentation, anomaly detection |
| **Reinforcement Learning** | Learn by trial and reward/penalty | Game playing (AlphaGo), RLHF for LLMs |
| **Transfer Learning** | Take a pretrained model, fine-tune for your task | Fine-tuning BERT for sentiment analysis |

### 2. The Training → Inference Pipeline

```
Data Collection → Preprocessing → Model Training → Evaluation → Deployment → Inference
```

- **Training**: Model learns patterns from data (expensive, done once)
- **Inference**: Model makes predictions on new inputs (fast, done millions of times)

> **AI Engineering tip:** As an AI Engineer, you mostly work at the **inference** level — calling APIs, building pipelines, optimizing performance.

### 3. Neural Networks (Simplified)

```
Input → [Layer 1: Feature Detection] → [Layer 2: Abstraction] → Output
```

- **Neurons**: Compute weighted sum of inputs, apply activation
- **Activation Function**: ReLU (non-linearity), Sigmoid (probability)
- **Backpropagation**: Error flows backward to adjust weights
- **Transformer**: The architecture powering all modern LLMs (attention mechanism)

### 4. Key Metrics (When Reviewing AI Systems)

| Metric | What It Measures | Use Case |
|--------|-----------------|---------|
| **Accuracy** | % correct predictions | Classification |
| **Precision** | Of predicted positives, how many were real? | Spam filter |
| **Recall** | Of actual positives, how many did we catch? | Medical diagnosis |
| **F1 Score** | Harmonic mean of Precision & Recall | Balanced metric |
| **Latency** | Time to generate response | Real-time apps |
| **Throughput** | Requests per second | Scalability |

---

## 🌍 Real-World AI Applications

| Domain | AI Use Case | Technology |
|--------|------------|-----------|
| Healthcare | Medical image diagnosis | Computer Vision (CNNs) |
| Finance | Fraud detection | Anomaly detection, classification |
| E-commerce | Product recommendations | Collaborative filtering |
| Customer Service | Chatbots, support automation | LLMs, RAG |
| Content | AI writing assistants | GPT-4, Claude |
| Code | Code completion, generation | GitHub Copilot, CodeLlama |
| Search | Semantic search | Embeddings + Vector DBs |
| Legal | Contract analysis | LLMs + NLP |

---

## 🔧 AI Engineering vs ML Engineering

| | AI Engineering | ML Engineering |
|--|----------------|---------------|
| Focus | Build apps on top of AI models | Build and train the AI models |
| Skills | APIs, prompting, RAG, agents | Python, PyTorch, data pipelines |
| Tools | LangChain, OpenAI API, Pinecone | PyTorch, TensorFlow, Kubeflow |
| Output | AI-powered products | Trained models |
| Who hires | Product companies, startups | Research labs, large tech |

---

## 💡 Why AI Engineering Matters Now

- **GPT-3** (2020) showed LLMs could follow instructions
- **ChatGPT** (2022) made LLMs mainstream
- **GPT-4, Claude, Gemini** (2023–2024) enabled complex reasoning
- **Tool Use + Agents** (2024) enabled autonomous task completion
- **Multimodal Models** can now process text, images, audio, video

> Every software product now has an AI layer. AI Engineering is the skill that connects software development with AI capabilities.

---

## 📚 Learning Roadmap

### Week 1–2: Foundations
- [ ] Understand supervised vs unsupervised vs reinforcement learning
- [ ] Understand neural networks conceptually (3Blue1Brown on YouTube)
- [ ] Understand what a Transformer is (The Illustrated Transformer — Jay Alammar)
- [ ] Set up OpenAI API, make your first API call

### Week 3–4: LLM Applications
- [ ] Understand prompt engineering (zero-shot, few-shot, chain-of-thought)
- [ ] Build a simple chatbot using OpenAI API
- [ ] Understand tokenization and context windows

### Month 2: RAG and Embeddings
- [ ] Understand embeddings (vectors representing meaning)
- [ ] Build a semantic search system
- [ ] Build a RAG pipeline from scratch
- [ ] Set up a vector database (ChromaDB or Pinecone)

### Month 3: Agents
- [ ] Understand function calling / tool use
- [ ] Build an AI agent with tools (calculator, web search)
- [ ] Explore LangGraph for multi-agent workflows

---

## ❓ Interview Questions

**Q: What is the difference between traditional programming and machine learning?**
> Traditional programming: Programmer writes rules → machine follows them. ML: Machine learns rules from data → programmer provides examples.

**Q: What is overfitting?**
> When a model learns the training data too well — including its noise — and performs poorly on new data. Fixed by: more data, regularization, dropout, cross-validation.

**Q: What is the attention mechanism in Transformers?**
> Attention allows the model to focus on relevant parts of the input when generating each output token. It computes a weighted sum over all inputs, where weights are learned based on relevance. This enables understanding context across long sequences.

---

## ✅ Revision Checklist

- [ ] Can I explain the difference between AI, ML, and DL?
- [ ] Can I explain the training vs inference distinction?
- [ ] Can I explain what a Transformer does at a high level?
- [ ] Can I name 5 real-world AI applications with their underlying technology?
- [ ] Do I understand what AI Engineering is vs ML Engineering?

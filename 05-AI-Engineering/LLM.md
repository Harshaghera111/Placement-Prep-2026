# 🧠 LLM — Large Language Models

> LLMs are the foundation of modern AI applications. As an AI Engineer, you'll use them via API — understanding how they work at an application level is essential.

---

## 📖 What is an LLM?

A **Large Language Model (LLM)** is a deep learning model trained on massive text corpora that can generate, understand, and reason about language.

**Examples:** GPT-4o (OpenAI), Claude 3.5 (Anthropic), Gemini 1.5 (Google), Llama 3 (Meta)

**How it works (simplified):**
1. **Pretraining**: Train on billions of web pages, books, code — learns general knowledge
2. **Fine-tuning**: Specialize for instruction-following or specific tasks
3. **RLHF**: Reinforce learning from Human Feedback — makes it helpful and safe
4. **Inference**: Given an input (prompt), generate output token by token

---

## 🔑 Key Concepts

### 1. Tokens
- LLMs don't process characters or words — they process **tokens**
- A token ≈ 4 characters or ¾ of a word
- "Hello, world!" = ~5 tokens
- Pricing is per-token: input tokens + output tokens

```python
# Check token count using tiktoken (OpenAI)
import tiktoken

encoder = tiktoken.encoding_for_model("gpt-4o")
tokens = encoder.encode("What is the capital of France?")
print(f"Token count: {len(tokens)}")  # ~8 tokens
```

### 2. Context Window
- Maximum tokens the model can process in one request
- Includes: system prompt + conversation history + output
- GPT-4o: 128K tokens (~96,000 words)
- Claude 3.5: 200K tokens

**Implication for engineering:** Long documents need chunking. Conversation history needs management.

### 3. Temperature & Sampling Parameters

| Parameter | Range | Effect |
|-----------|-------|--------|
| **Temperature** | 0.0–2.0 | Higher = more random/creative, Lower = more deterministic |
| **max_tokens** | 1–model limit | Cap on output length |
| **top_p** | 0–1 | Nucleus sampling; controls diversity |
| **frequency_penalty** | -2 to 2 | Penalize repeated tokens |
| **presence_penalty** | -2 to 2 | Encourage new topics |

**Rule of thumb:**
- Creative writing → `temperature: 0.9`
- Factual Q&A → `temperature: 0.2`
- Code generation → `temperature: 0.0`

### 4. Prompt Engineering

#### Zero-Shot (No Examples)
```python
prompt = "Classify the sentiment of: 'This movie was amazing!'"
# Model uses its training to answer
```

#### Few-Shot (With Examples)
```python
prompt = """
Classify sentiment:
'I love this product' → Positive
'This is terrible' → Negative
'It's okay I guess' → Neutral

Now classify: 'The service was fantastic!'
"""
# Model learns pattern from examples
```

#### Chain-of-Thought
```python
prompt = """
Think step by step:
Q: If a train travels 120km in 2 hours, what is its speed?
A: Let me think step by step:
   - Speed = Distance / Time
   - Distance = 120km, Time = 2 hours
   - Speed = 120 / 2 = 60 km/h
   
Q: If a car travels 300km in 5 hours, what is its speed?
A: Let me think step by step:
"""
# Model follows reasoning pattern → more accurate
```

#### System Prompt Best Practices
```python
system_prompt = """
You are a helpful customer support agent for GramSathi, a farming platform.
- Answer questions about crop selection, government schemes, and weather
- Always respond in the user's preferred language
- If you don't know something, say "I don't have that information"
- Keep responses concise and practical for farmers
"""
```

---

## 🔧 Using OpenAI API

### Basic Chat Completion
```python
from openai import OpenAI

client = OpenAI(api_key="your-api-key")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What are the best crops for July in North India?"}
    ],
    temperature=0.3,
    max_tokens=500
)

print(response.choices[0].message.content)
print(f"Tokens used: {response.usage.total_tokens}")
```

### Streaming Responses
```python
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Write a short poem about rain"}],
    stream=True
)

for chunk in stream:
    if chunk.choices[0].delta.content is not None:
        print(chunk.choices[0].delta.content, end="")
```

### Function Calling
```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get current weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {"type": "string", "description": "City name"},
                    "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
                },
                "required": ["city"]
            }
        }
    }
]

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "What's the weather in Delhi?"}],
    tools=tools,
    tool_choice="auto"
)
```

---

## 🏗️ LLM Architecture (Conceptual)

### The Transformer Block
```
Input Text → Tokenize → Embeddings → [N × Transformer Blocks] → Output Tokens

Each Transformer Block:
├── Multi-Head Self-Attention (attend to relevant context)
├── Add & Norm
├── Feed-Forward Network
└── Add & Norm
```

**Self-Attention key insight:** Each token can "attend" to every other token in the context. This is why LLMs understand long-range dependencies in text.

---

## 🌍 Real-World LLM Use Cases

| Use Case | How to Implement |
|----------|-----------------|
| Chatbot | System prompt + conversation history |
| Document Q&A | RAG pipeline (chunk → embed → retrieve → generate) |
| Code generation | Few-shot with code examples in prompt |
| Data extraction | Structured output with response_format=JSON |
| Content moderation | Classification prompt + temperature=0 |
| Translation | Direct prompt — LLMs are multilingual |
| Summarization | Simple instruction + long document |

---

## ⚠️ LLM Limitations & Mitigations

| Limitation | Mitigation |
|------------|-----------|
| **Hallucinations** | RAG, ground with retrieved facts, ask to cite sources |
| **Context limit** | Chunking, summarization, RAG |
| **Stale knowledge** | RAG with up-to-date documents |
| **Inconsistency** | Lower temperature, structured prompts |
| **Cost** | Cache common queries, use smaller models for simple tasks |
| **Latency** | Streaming, caching, smaller models |

---

## ❓ Interview Questions

**Q: What is a hallucination in LLMs?**
> When the LLM generates confident-sounding but factually incorrect information. It happens because LLMs are trained to generate plausible text, not to verify facts. Mitigation: Use RAG to ground responses in real documents.

**Q: What is the difference between few-shot and zero-shot prompting?**
> Zero-shot: Give the model a task without examples — relies entirely on training. Few-shot: Provide 2-5 examples of input-output pairs before the actual query — guides the model's behavior.

**Q: What is context window?**
> The maximum number of tokens (words/characters) the model can process in a single request, including both input and output. GPT-4o has 128K tokens. Beyond this limit, content must be chunked or summarized.

**Q: When would you fine-tune a model vs use RAG?**
> Use RAG when your data changes frequently or is too large for the context window. Use fine-tuning when you need to change the model's behavior, style, or tone permanently. RAG is cheaper and more flexible; fine-tuning requires GPU compute.

---

## ✅ Revision Checklist

- [ ] Can I explain what an LLM is at an application level?
- [ ] Do I understand tokens and context windows?
- [ ] Can I implement zero-shot, few-shot, and chain-of-thought prompting?
- [ ] Can I make a basic OpenAI API call in Python?
- [ ] Can I explain hallucinations and how to mitigate them?
- [ ] Do I understand when to use temperature 0 vs 0.9?
- [ ] Can I explain fine-tuning vs RAG?

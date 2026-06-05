# 📝 NLP — Natural Language Processing

> NLP is the technology that enables computers to understand, interpret, and generate human language. It's the foundation of everything from search to chatbots.

---

## 📖 What is NLP?

**Natural Language Processing (NLP)** is the branch of AI that deals with the interaction between computers and human language. It enables machines to read, understand, and generate text in a way that is meaningful.

**Before LLMs**: NLP required hand-crafted rules, statistical models, and task-specific models.
**After LLMs (Transformer era)**: One model handles many NLP tasks through instruction following.

---

## 🔑 Core NLP Concepts

### 1. Tokenization
Breaking text into units (tokens) that a model can process.

**Types:**
- **Word Tokenization**: "Hello world" → ["Hello", "world"]
- **Subword Tokenization** (used in LLMs): "unhappiness" → ["un", "happy", "ness"]
  - **BPE (Byte Pair Encoding)**: GPT uses this
  - **WordPiece**: BERT uses this
  - **SentencePiece**: Google models use this

```python
# Using NLTK for basic tokenization
import nltk
from nltk.tokenize import word_tokenize, sent_tokenize

text = "Machine learning is fascinating. I love NLP!"
words = word_tokenize(text)
sentences = sent_tokenize(text)
print(words)     # ['Machine', 'learning', 'is', 'fascinating', '.', 'I', 'love', 'NLP', '!']
print(sentences) # ['Machine learning is fascinating.', 'I love NLP!']

# Using tiktoken (OpenAI's BPE tokenizer)
import tiktoken
enc = tiktoken.get_encoding("cl100k_base")  # GPT-4 encoding
tokens = enc.encode("Hello, world!")
print(tokens)       # [9906, 11, 1917, 0]
print(len(tokens))  # 4
```

### 2. Text Preprocessing Pipeline

```python
import re
import string
from nltk.corpus import stopwords
from nltk.stem import PorterStemmer, WordNetLemmatizer

def preprocess(text):
    # 1. Lowercase
    text = text.lower()
    
    # 2. Remove punctuation
    text = text.translate(str.maketrans('', '', string.punctuation))
    
    # 3. Remove extra whitespace
    text = re.sub(r'\s+', ' ', text).strip()
    
    # 4. Tokenize
    tokens = text.split()
    
    # 5. Remove stopwords
    stop_words = set(stopwords.words('english'))
    tokens = [t for t in tokens if t not in stop_words]
    
    # 6. Lemmatization (gets root word: "running" → "run")
    lemmatizer = WordNetLemmatizer()
    tokens = [lemmatizer.lemmatize(t) for t in tokens]
    
    return tokens
```

### 3. Named Entity Recognition (NER)

```python
import spacy

nlp = spacy.load("en_core_web_sm")

text = "Apple Inc. was founded by Steve Jobs in Cupertino, California."
doc = nlp(text)

for ent in doc.ents:
    print(f"{ent.text}: {ent.label_}")
# Apple Inc.: ORG
# Steve Jobs: PERSON
# Cupertino: GPE
# California: GPE
```

### 4. Sentiment Analysis

```python
# Using HuggingFace pipeline (modern approach)
from transformers import pipeline

classifier = pipeline("sentiment-analysis")
result = classifier("This movie was absolutely amazing!")
print(result)  # [{'label': 'POSITIVE', 'score': 0.9998}]

# Using multiple results
texts = [
    "I hate Mondays",
    "Best day ever!",
    "It was okay I guess"
]
results = classifier(texts)
for text, res in zip(texts, results):
    print(f"{res['label']} ({res['score']:.2f}): {text}")
```

### 5. Text Classification Pipeline (Traditional ML)

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.pipeline import Pipeline

# Spam classifier example
pipeline = Pipeline([
    ('tfidf', TfidfVectorizer(max_features=10000, ngram_range=(1, 2))),
    ('classifier', MultinomialNB())
])

X_train = ["Buy cheap meds now!", "Meeting at 3pm tomorrow", "FREE MONEY WIN NOW!!!"]
y_train = [1, 0, 1]  # 1 = spam, 0 = not spam

pipeline.fit(X_train, y_train)
print(pipeline.predict(["Click here for free money!"]))  # [1] = spam
```

---

## 🔑 Key NLP Tasks

| Task | Description | Example |
|------|-------------|---------|
| **Text Classification** | Assign a label to text | Spam detection, sentiment |
| **NER** | Identify named entities | People, places, organizations |
| **Machine Translation** | Translate between languages | English → Hindi |
| **Summarization** | Compress text to key points | News article summary |
| **Question Answering** | Answer questions from context | RAG systems |
| **Text Generation** | Generate new text | LLMs, chatbots |
| **Information Extraction** | Extract structured data from text | Invoice parsing |
| **Coreference Resolution** | Link pronouns to nouns | "John went home. He was tired." → He = John |

---

## 🔑 TF-IDF (Term Frequency–Inverse Document Frequency)

**Concept:** A word is important if it appears often in a document but rarely in the corpus.

```
TF(w, d) = (count of w in d) / (total words in d)
IDF(w) = log(N / df(w))    # N = total docs, df = docs containing w
TF-IDF(w, d) = TF × IDF
```

**Use cases:** Search relevance, keyword extraction, document similarity.

---

## 🌍 Real-World NLP Applications

| Application | NLP Technique | Tools |
|-------------|--------------|-------|
| Customer support bot | LLM + Intent detection | OpenAI, Rasa |
| Email filtering | Text classification | sklearn, HuggingFace |
| Medical NER | Named entity recognition | spaCy, medspaCy |
| News summarization | Extractive/Abstractive summary | BART, T5 |
| Code documentation | Code → text generation | GitHub Copilot |
| Search ranking | TF-IDF, BM25, semantic search | Elasticsearch |
| Multilingual chat | Translation + LLM | DeepL API, LibreTranslate |

---

## 🔧 Modern NLP Stack

```
Raw Text
    ↓ Tokenization (tiktoken, HuggingFace tokenizers)
Tokens
    ↓ Embedding (sentence-transformers, OpenAI embeddings)
Vectors
    ↓ Processing (LangChain, LlamaIndex)
Context
    ↓ LLM inference (OpenAI API, Claude API, local Llama)
Output
```

---

## ❓ Interview Questions

**Q: What is the difference between stemming and lemmatization?**
> Stemming chops off word endings to get the root (e.g., "running" → "run", "happiness" → "happi"). Lemmatization uses vocabulary/morphological analysis to get the actual dictionary form (e.g., "better" → "good", "running" → "run"). Lemmatization is more accurate but slower.

**Q: What is TF-IDF?**
> TF-IDF ranks how important a word is to a document in a corpus. TF measures frequency within the document; IDF penalizes words common across all documents (like "the"). High TF-IDF means the word is frequent in this doc but rare overall — so it's distinctive.

**Q: What is the difference between extractive and abstractive summarization?**
> Extractive: Select and stitch together actual sentences from the source. Abstractive: Generate new sentences that capture the meaning (can paraphrase, combine). LLMs (GPT, Claude) do abstractive summarization.

**Q: What is BERT and how is it different from GPT?**
> Both are Transformer-based. BERT is an encoder — it processes text bidirectionally (looks at both left and right context), ideal for classification and NER. GPT is a decoder — it processes text left-to-right, ideal for generation tasks.

---

## ✅ Revision Checklist

- [ ] Can I explain tokenization and why BPE is used in LLMs?
- [ ] Can I implement a basic text preprocessing pipeline?
- [ ] Can I explain TF-IDF conceptually and when to use it?
- [ ] Can I use HuggingFace pipelines for sentiment analysis and NER?
- [ ] Do I understand the difference between BERT and GPT architectures?
- [ ] Can I name 5 NLP tasks with real-world applications?

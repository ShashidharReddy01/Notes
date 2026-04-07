# Course 0: Fundamentals — Prerequisites for RAG and Agentic AI

> **IBM RAG and Agentic AI Specialization — Prerequisite Knowledge**
> Covers: AI Evolution, NLP Foundations, Embeddings, Transformers, LLMs, RAG & Agentic AI Concepts, LangChain Ecosystem, and the Course Roadmap

---

## Table of Contents

1. [The AI Landscape — From Rule-Based to Generative AI](#1-the-ai-landscape--from-rule-based-to-generative-ai)
2. [Natural Language Processing (NLP) Foundations](#2-natural-language-processing-nlp-foundations)
3. [How Machines Understand Text — Embeddings](#3-how-machines-understand-text--embeddings)
4. [The Transformer Architecture](#4-the-transformer-architecture)
5. [Large Language Models (LLMs)](#5-large-language-models-llms)
6. [Key Concepts for the RAG & Agentic AI Journey](#6-key-concepts-for-the-rag--agentic-ai-journey)
7. [The LangChain Ecosystem Overview](#7-the-langchain-ecosystem-overview)
8. [Course Roadmap — Connecting It All](#8-course-roadmap--connecting-it-all)

---

## 1. The AI Landscape — From Rule-Based to Generative AI

### The Evolution Timeline

```
1950s-1980s        1990s-2010s         2012-2017           2017-2020          2020-Present
Rule-Based AI  --> Machine Learning --> Deep Learning  --> Transformers  --> Large Language Models
                                                                              & Generative AI
  |                  |                    |                   |                    |
  Expert systems     Decision trees       CNNs (images)       BERT, GPT-1         GPT-4, Claude,
  If-then rules      SVMs, Naive Bayes    RNNs/LSTMs (text)   Attention mechanism  Llama, Granite
  Chatbot scripts    Feature engineering   Word2Vec, GloVe     Transfer learning    RAG, Agents
```

### Types of AI

| Type | What It Means | Examples |
|------|--------------|----------|
| **Narrow AI (ANI)** | AI designed for a specific task | Spam filters, chess engines, Siri |
| **General AI (AGI)** | AI with human-level reasoning across any domain | Does not exist yet |
| **Generative AI** | AI that creates new content (text, images, code, audio) | ChatGPT, DALL-E, Copilot, Midjourney |

> **Key distinction:** Generative AI is a *subset* of Narrow AI — it's very good at generating content but doesn't "understand" the world the way humans do.

### Discriminative vs Generative Models

```
Discriminative:   Input --> [Model] --> Label/Category
                  "Is this email spam?"  -->  Yes/No

Generative:       Prompt --> [Model] --> New Content
                  "Write an email about..."  -->  Full email text
```

| Aspect | Discriminative | Generative |
|--------|---------------|------------|
| **Goal** | Classify or predict labels | Create new data |
| **Learns** | Decision boundaries between classes | The underlying data distribution |
| **Examples** | Logistic regression, SVM, BERT (for classification) | GPT, DALL-E, Stable Diffusion |
| **Output** | A label, score, or category | Text, images, code, audio |

### Where RAG and Agentic AI Fit

```
                        Generative AI
                             |
              ┌──────────────┼──────────────┐
              |              |              |
         Prompt Eng.    RAG             Fine-Tuning
         (steer the    (give it         (bake knowledge
          model)        external data)   into weights)
                             |
                        Agentic AI
                        (autonomous planning,
                         tool use, multi-step
                         reasoning)
```

RAG and Agentic AI are **techniques built on top of LLMs** — they don't replace LLMs, they extend them. This specialization teaches you how to build both.

---

## 2. Natural Language Processing (NLP) Foundations

### What Is NLP?

**Natural Language Processing (NLP)** is the field of AI that enables machines to read, understand, interpret, and generate human language. It's the foundation that makes LLMs, chatbots, search engines, and translation tools possible.

Every time you ask ChatGPT a question, use Google Translate, or get autocomplete suggestions — NLP is at work.

### The Core NLP Tasks

| Task | What It Does | Example |
|------|-------------|---------|
| **Tokenization** | Splits text into smaller units (tokens) | "I love AI" → ["I", "love", "AI"] |
| **Part-of-Speech (POS) Tagging** | Labels each word's grammatical role | "The cat sat" → [DET, NOUN, VERB] |
| **Named Entity Recognition (NER)** | Identifies people, places, orgs, dates | "Apple was founded by Steve Jobs in Cupertino" → Apple=ORG, Steve Jobs=PERSON, Cupertino=LOC |
| **Sentiment Analysis** | Determines emotional tone | "This product is amazing!" → Positive |
| **Text Classification** | Categorizes text into predefined classes | Email → Spam / Not Spam |
| **Machine Translation** | Converts text between languages | English → French |
| **Text Summarization** | Condenses long text into key points | 10-page report → 3-sentence summary |
| **Question Answering** | Finds answers from a body of text | "When was Python created?" → "1991" |
| **Text Generation** | Produces new, coherent text | Prompt → Full paragraph (what LLMs excel at) |

### Traditional NLP vs LLM-Powered NLP

```
Traditional NLP Pipeline (pre-2018):
┌──────────┐    ┌──────────────┐    ┌────────────┐    ┌───────────┐
│ Raw Text │--->│ Preprocessing │--->│  Feature    │--->│  Task-    │---> Output
│          │    │ (tokenize,    │    │ Engineering │    │ Specific  │
│          │    │  stem, clean) │    │ (TF-IDF,    │    │  Model    │
│          │    │              │    │  n-grams)   │    │ (train    │
│          │    │              │    │             │    │  from     │
│          │    │              │    │             │    │  scratch) │
└──────────┘    └──────────────┘    └────────────┘    └───────────┘
                     SLOW                MANUAL            ONE MODEL PER TASK

LLM-Powered NLP (2018+):
┌──────────┐    ┌────────────┐    ┌──────────────┐
│ Raw Text │--->│   Prompt   │--->│  Pre-trained │---> Output
│          │    │  Template  │    │     LLM      │
│          │    │            │    │ (handles ALL │
│          │    │            │    │   tasks)     │
└──────────┘    └────────────┘    └──────────────┘
                    FAST             ONE MODEL, MANY TASKS
```

> **The paradigm shift:** Before LLMs, you needed a separate model for each NLP task (one for sentiment, one for NER, one for translation). With LLMs, a single model handles all tasks — you just change the prompt.

### Tokenization — The First Step in Any NLP System

Tokenization is how text gets converted into numbers that models can process. There are different strategies:

| Strategy | How It Works | Example: "unhappiness" | Pros/Cons |
|----------|-------------|----------------------|-----------|
| **Word-level** | Split on spaces/punctuation | ["unhappiness"] | Simple but huge vocabulary, can't handle unknown words |
| **Character-level** | Each character is a token | ["u","n","h","a","p","p","i","n","e","s","s"] | Small vocab but loses word meaning, very long sequences |
| **Subword (BPE)** | Learns common subword units | ["un", "happi", "ness"] | Best of both — small vocab, handles unknown words, preserves meaning |

**Byte Pair Encoding (BPE)** is what GPT, Claude, Llama, and most modern LLMs use. It learns the most common character combinations from training data.

```python
# Example: Seeing how GPT tokenizes text
import tiktoken

encoder = tiktoken.encoding_for_model("gpt-4")

text = "LangChain makes building AI apps easier"
tokens = encoder.encode(text)
print(f"Text: {text}")
print(f"Token IDs: {tokens}")
print(f"Token count: {len(tokens)}")
print(f"Decoded tokens: {[encoder.decode([t]) for t in tokens]}")

# Output:
# Text: LangChain makes building AI apps easier
# Token IDs: [27317, 26729, 3727, 4857, 15592, 10721, 8831]
# Token count: 7
# Decoded tokens: ['Lang', 'Chain', ' makes', ' building', ' AI', ' apps', ' easier']
```

> **Why tokenization matters for you:** When you see "context window: 128K tokens" for a model, these are *tokens*, not words. A rough rule of thumb: **1 token ≈ 0.75 words** in English. So 128K tokens ≈ 96K words.

### The NLP → LLM Connection

```
NLP (the field)
 └── Techniques: tokenization, parsing, embeddings, attention...
      └── Models: RNNs, LSTMs, Transformers...
           └── Large Language Models (LLMs): GPT, Claude, Llama...
                └── Applications: chatbots, RAG, agents, summarization...
                     └── Frameworks: LangChain, LlamaIndex, Haystack...
```

NLP is the *science*. LLMs are the *breakthrough technology* within NLP. LangChain is the *engineering framework* to build applications with LLMs.

---

## 3. How Machines Understand Text — Embeddings

### The Core Problem

Computers work with numbers, not words. To process language, we need to convert text into numerical representations that capture **meaning**.

### The Evolution of Text Representations

```
One-Hot Encoding (1990s)     Word2Vec (2013)          Contextual Embeddings (2018+)
                                                       (BERT, GPT, etc.)
"king"  = [0,0,0,1,0,0,0]   "king"  = [0.7, 0.3,    "bank" (river) = [0.2, 0.8, ...]
"queen" = [0,0,1,0,0,0,0]              -0.1, 0.9]    "bank" (money) = [0.9, 0.1, ...]
"man"   = [0,1,0,0,0,0,0]   "queen" = [0.6, 0.4,
                                         0.2, 0.8]    Same word, DIFFERENT vectors
No notion of similarity.                               based on context!
Every word is equally         king - man + woman ≈ queen
"distant" from every          (captures relationships!)
other word.
```

| Method | Dimensions | Captures Meaning? | Context-Aware? | Used By |
|--------|-----------|-------------------|----------------|---------|
| **One-Hot** | Vocabulary size (10K+) | No | No | Legacy systems |
| **Word2Vec / GloVe** | 50-300 | Yes (static) | No — same vector regardless of context | Pre-2018 NLP |
| **Contextual (BERT, GPT)** | 768-4096 | Yes (dynamic) | Yes — vector changes with surrounding text | Modern LLMs |

### What Are Embeddings?

An embedding is a **dense vector (list of numbers)** that represents the meaning of a piece of text in a continuous vector space. Texts with similar meanings end up close together in this space.

```
Embedding Space (simplified to 2D):

         "puppy" ●  ● "dog"
                    ● "canine"


  "car" ●  ● "automobile"
            ● "vehicle"


              ● "banana"
         ● "apple"
              ● "fruit"

  Similar concepts cluster together!
```

### Semantic Similarity with Cosine Similarity

**Cosine similarity** measures how similar two vectors are by looking at the angle between them (ignoring magnitude). It ranges from -1 (opposite) to 1 (identical).

```
cos(θ) = (A · B) / (||A|| × ||B||)

  1.0 = identical meaning
  0.0 = unrelated
 -1.0 = opposite meaning
```

```python
import numpy as np

# Simplified example — real embeddings have 768+ dimensions
dog_embedding    = np.array([0.8, 0.2, 0.9, 0.1])
puppy_embedding  = np.array([0.75, 0.25, 0.85, 0.15])
car_embedding    = np.array([0.1, 0.9, 0.1, 0.8])

def cosine_similarity(a, b):
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

print(f"dog vs puppy: {cosine_similarity(dog_embedding, puppy_embedding):.4f}")   # ~0.9985 (very similar)
print(f"dog vs car:   {cosine_similarity(dog_embedding, car_embedding):.4f}")     # ~0.3210 (not similar)
```

### Why Embeddings Matter for RAG

RAG (Retrieval-Augmented Generation) depends entirely on embeddings:

```
User Query: "How do I reset my password?"
     |
     v
 [Embed the query] --> query_vector = [0.3, 0.7, 0.1, ...]
     |
     v
 [Search vector database] -- find documents whose embeddings
     |                        are closest to the query vector
     v
 Top matches:
   1. "Password Reset Guide"         (similarity: 0.94)
   2. "Account Recovery Steps"       (similarity: 0.87)
   3. "Two-Factor Authentication"    (similarity: 0.62)
     |
     v
 [Feed top matches + query to LLM] --> generates grounded answer
```

Without embeddings, there's no way to do semantic search — and without semantic search, there's no RAG.

---

## 4. The Transformer Architecture

### The Problem with Earlier Architectures

Before transformers, the go-to models for text were **RNNs (Recurrent Neural Networks)** and **LSTMs (Long Short-Term Memory)**. They processed text word by word, sequentially:

```
RNN / LSTM Processing:

"The"  -->  "cat"  -->  "sat"  -->  "on"  -->  "the"  -->  "mat"
  |           |           |          |           |           |
  h1    -->  h2    -->   h3   -->   h4    -->   h5    -->   h6

  Each word must WAIT for the previous word to be processed.
  Information from "The" gets diluted by the time we reach "mat".
```

**Problems:**
- **Sequential processing** — can't parallelize, slow to train
- **Vanishing gradient** — early words get "forgotten" in long sequences
- **Limited context** — struggles with long-range dependencies ("The cat that I saw yesterday at the park sat on the mat" — connecting "cat" to "sat" is hard)

### The Transformer Breakthrough (2017)

The paper "Attention Is All You Need" (Vaswani et al., 2017) introduced the **Transformer** — an architecture that processes all words simultaneously using a mechanism called **self-attention**.

```
Transformer Processing:

"The"    "cat"    "sat"    "on"    "the"    "mat"
  |        |        |       |        |        |
  v        v        v       v        v        v
 [============= Self-Attention ==============]
  Every word attends to EVERY other word
  simultaneously. No waiting!
  |        |        |       |        |        |
  v        v        v       v        v        v
 [============ Feed-Forward Layer ============]
  |        |        |       |        |        |
  v        v        v       v        v        v
Output   Output   Output  Output  Output   Output
```

### Self-Attention — The Core Idea

Self-attention lets each word "look at" every other word in the sentence and decide which words are most relevant to it.

**Analogy:** Imagine you're reading the sentence "The bank by the river was flooded." When you get to "bank", your brain automatically looks at "river" and "flooded" to decide it means a riverbank, not a financial bank. Self-attention does exactly this — for every word, simultaneously.

```
Input: "The cat sat on the mat because it was tired"

When processing "it", attention scores might look like:

  The   cat   sat   on   the   mat   because   it   was   tired
  0.02  0.45  0.05  0.01  0.01  0.03   0.03   0.10  0.05  0.25
         ^                                                   ^
    high attention                                    high attention
    ("it" refers to "cat")                     ("it" relates to "tired")
```

> **Why this matters:** Self-attention is what gives LLMs their ability to understand context, resolve ambiguity, and handle long documents. It's the core mechanism behind every modern LLM.

### The Three Transformer Variants

The original transformer has both an **encoder** (understands input) and a **decoder** (generates output). Different models use different parts:

```
        Full Transformer
  ┌──────────┬──────────┐
  │ Encoder  │ Decoder  │
  │          │          │
  │ Reads &  │ Generates│
  │ Understands│ Output │
  └──────────┴──────────┘
       |          |           |
  Encoder-only  Decoder-only  Encoder-Decoder
```

| Variant | How It Works | Best For | Key Models |
|---------|-------------|----------|------------|
| **Encoder-only** | Reads full input bidirectionally; produces deep understanding | Classification, NER, sentiment analysis, search | BERT, RoBERTa, IBM Slate |
| **Decoder-only** | Generates text left-to-right, one token at a time | Text generation, chatbots, coding | GPT-4, Claude, Llama, Granite |
| **Encoder-Decoder** | Encoder processes input; decoder generates output conditioned on it | Translation, summarization, Q&A | T5, BART, Flan-T5 |

```
Encoder-only (BERT):           Decoder-only (GPT):           Encoder-Decoder (T5):

Input: "The movie was ___"     Input: "Once upon a"          Input: "Translate: Hello"
Output: "great" (fill mask)    Output: " time, there was..." Output: "Bonjour"

Understands context deeply.    Generates text fluently.       Transforms input to output.
Can't generate long text.      Can't "see" future tokens.     Best of both worlds for
                                                               specific tasks.
```

### Why Decoder-Only Models Dominate Today

Most LLMs you interact with (GPT-4, Claude, Llama, Granite) are **decoder-only**. Why?

- **Scaling:** Decoder-only models scale better with more parameters and data
- **Generality:** A single model that generates text can handle *any* NLP task through prompting
- **Simplicity:** One architecture, one training objective (next-token prediction)

---

## 5. Large Language Models (LLMs)

### What Makes a Model "Large"?

| Factor | Small Model | Large Model | Example |
|--------|------------|-------------|---------|
| **Parameters** | Millions (1M-100M) | Billions (1B-1T+) | BERT: 340M, GPT-4: ~1.8T (estimated) |
| **Training Data** | GBs of text | TBs of text (internet-scale) | Llama 2: 2T tokens of text |
| **Compute** | Single GPU, hours | Thousands of GPUs, months | GPT-4: ~$100M+ estimated training cost |
| **Capabilities** | Single task | Multi-task, emergent abilities | Can reason, code, translate, create |

> **Emergent abilities:** Large models develop capabilities that smaller models don't have — like chain-of-thought reasoning, multi-step math, and in-context learning. These abilities "emerge" at certain scale thresholds and aren't explicitly trained.

### How LLMs Actually Generate Text — Next-Token Prediction

At its core, every LLM does one thing: **predict the next token given all previous tokens**.

```
Input tokens:  "The capital of France is"

LLM calculates probability for EVERY token in its vocabulary:

  "Paris"    → 0.92    <-- highest probability
  "Lyon"     → 0.03
  "a"        → 0.02
  "the"      → 0.01
  "Berlin"   → 0.005
  ...        → ...

Selected: "Paris"

Now input becomes: "The capital of France is Paris"
Process repeats for the next token...
```

This is called **autoregressive generation** — the model generates one token at a time, each time feeding its own output back as input.

### Temperature and Sampling

Temperature controls how "creative" vs "deterministic" the model is when picking the next token:

```
Probability distribution for next token after "The best programming language is":

Temperature = 0.0 (deterministic):     Temperature = 1.0 (creative):
  "Python"   ████████████ 0.85          "Python"   █████ 0.35
  "Java"     █ 0.08                     "Java"     ███ 0.20
  "C++"      ░ 0.04                     "Rust"     ██ 0.15
  "Rust"     ░ 0.02                     "C++"      ██ 0.12
  other      ░ 0.01                     "Go"       █ 0.08
                                        other      █ 0.10

  Always picks "Python"                 Might pick any of the top options
  (greedy decoding)                     (more variety, sometimes surprising)
```

| Parameter | What It Controls | Low Value | High Value |
|-----------|-----------------|-----------|------------|
| **Temperature** | Randomness of token selection | Focused, repetitive, factual | Creative, diverse, sometimes incoherent |
| **Top-p (nucleus)** | Only consider tokens within cumulative probability p | Fewer options considered | More options considered |
| **Top-k** | Only consider top k most likely tokens | Very few choices | Many choices |

### Pre-training vs Fine-tuning vs Prompting

These are the three main ways to "teach" a model. Understanding when to use each is critical for this specialization:

```
Pre-training                    Fine-tuning                   Prompting
(done by model creators)        (done by developers)          (done by users/developers)

Internet-scale data             Domain-specific data          Instructions at inference time
Trillions of tokens             Thousands-millions of         Zero to few examples
Months of compute               examples                     No training at all
$10M-$100M+ cost                Hours-days of compute         Free / per-API-call cost
                                $100-$10K cost

"Teach a human to speak"        "Teach a doctor medical       "Ask the doctor a specific
                                 terminology"                  question"
```

| Approach | When to Use | Pros | Cons |
|----------|------------|------|------|
| **Pre-training** | Building a foundation model from scratch | Maximum control, custom architecture | Enormous cost and data requirements |
| **Fine-tuning** | Domain-specific knowledge needs to be persistent | Consistent domain expertise, smaller model can match larger | Requires training data, compute, can cause forgetting |
| **Prompting** | Task-specific guidance at inference time | No training needed, instant, flexible | Limited by context window, no persistent learning |
| **RAG** | Need up-to-date or private data without retraining | Real-time data, no training, reduces hallucination | Requires vector DB setup, retrieval quality matters |

### Context Windows Explained

A context window is the **maximum number of tokens** a model can process in a single request (input + output combined).

```
┌──────────────────── Context Window (e.g., 128K tokens) ────────────────────┐
│                                                                             │
│  [System Prompt] [User Message] [Retrieved Docs] ... [Model's Response]    │
│  ◄──────────── Input Tokens ──────────────►  ◄── Output Tokens ──►         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

If your input + output exceeds the context window, the model literally
cannot process it. This is why RAG retrieves RELEVANT chunks, not entire
documents.
```

| Model | Context Window | Approx. Words | Equivalent To |
|-------|---------------|---------------|---------------|
| GPT-3.5 | 4K tokens | ~3,000 words | A short essay |
| GPT-4 | 128K tokens | ~96,000 words | A full novel |
| Claude 3.5 | 200K tokens | ~150,000 words | 2-3 novels |
| Llama 3 | 128K tokens | ~96,000 words | A full novel |
| IBM Granite 3 | 128K tokens | ~96,000 words | A full novel |

### Key Models You'll Encounter in This Specialization

| Model | Creator | Type | Notable For |
|-------|---------|------|-------------|
| **Granite** | IBM | Decoder-only | Enterprise-focused, used in watsonx, open-source variants |
| **Mixtral** | Mistral AI | Mixture of Experts (MoE) | Efficient — activates only a subset of parameters per token |
| **Llama** | Meta | Decoder-only | Open-source, widely used for research and fine-tuning |
| **GPT-4** | OpenAI | Decoder-only (MoE) | Most well-known, strong reasoning |
| **Claude** | Anthropic | Decoder-only | Long context, strong instruction following |
| **Gemini** | Google | Decoder-only | Natively multi-modal (text + image + video) |
| **BERT** | Google | Encoder-only | Classification, NER, search — not for generation |
| **IBM Slate** | IBM | Encoder-only | Embedding model for enterprise search and retrieval |

### Foundation Models

A **foundation model** is a large model pre-trained on broad data that can be adapted to many downstream tasks. It's the "base" that everything else builds on.

```
                    Foundation Model
                    (pre-trained on internet)
                         |
          ┌──────────────┼──────────────┐
          |              |              |
     Fine-tuned     Prompted         RAG-enhanced
     for medical    for customer     with company
     diagnosis      support chat     knowledge base
```

> **Key insight:** You almost never pre-train a foundation model yourself. You use an existing one (Granite, GPT-4, Llama) and adapt it through fine-tuning, prompting, or RAG.

---

## 6. Key Concepts for the RAG & Agentic AI Journey

### RAG at a Glance

**Retrieval-Augmented Generation (RAG)** is a technique that gives LLMs access to external knowledge at inference time — without retraining the model.

```
                     RAG Pipeline

User Question ──────────────────────────────────────────> Final Answer
      |                                                       ^
      v                                                       |
 [1. RETRIEVE]          [2. AUGMENT]            [3. GENERATE] |
      |                      |                       |        |
  Embed query           Combine query +          LLM generates
  Search vector DB      retrieved context        answer using the
  Find relevant         into a prompt            augmented prompt
  documents
      |                      |                       |
      v                      v                       v
  "Password reset       "Context: Password       "To reset your
   guide", "Account      reset guide says...      password, go to
   recovery steps"       Question: How do I       Settings > Security
                         reset my password?"      and click Reset..."
```

### Why RAG Over Fine-Tuning?

| Aspect | RAG | Fine-Tuning |
|--------|-----|-------------|
| **Data freshness** | Always up-to-date (retrieves live data) | Frozen at training time |
| **Cost** | Low (no training, just a vector DB) | High (GPU compute for training) |
| **Transparency** | Can cite sources | No source attribution |
| **Setup complexity** | Moderate (embeddings + vector store) | High (data prep, training pipeline) |
| **Best for** | Factual Q&A, knowledge bases, documentation | Style, tone, domain expertise |

> **Rule of thumb:** Use RAG when you need the model to access specific *facts*. Use fine-tuning when you need to change how the model *behaves*.

### Hallucinations and Grounding

**Hallucination** = when an LLM generates confident-sounding but factually incorrect information.

```
Without grounding:                    With RAG grounding:

Q: "What is IBM's latest AI model?"   Q: "What is IBM's latest AI model?"
A: "IBM's latest model is Watson 5,    A: "Based on the retrieved documentation,
    released in March 2025 with         IBM's latest model family is Granite 3,
    500B parameters."                    available through watsonx.ai."

    ^^^ Sounds plausible but            ^^^ Grounded in actual retrieved data
        completely made up                   with source attribution
```

RAG reduces hallucinations by **grounding** the LLM's responses in retrieved facts. This is one of the primary motivations for the entire specialization.

### Agentic AI Overview

**Agentic AI** = AI systems that can autonomously plan, reason, use tools, and take actions to accomplish complex goals.

```
Traditional LLM:                    Agentic AI:

User: "What's the weather          User: "Book me a flight to NYC
       in NYC?"                           for the cheapest fare
                                          next Friday"
LLM: "I don't have real-time
      data, but NYC typically       Agent:
      has..."                        1. [PLAN] Break down task
                                     2. [TOOL: Search] Find flights
Single response, no actions.         3. [REASON] Compare prices
                                     4. [TOOL: Calendar] Check Friday date
                                     5. [TOOL: Booking API] Book cheapest
                                     6. [RESPOND] "Booked! Flight AA123..."

                                    Multi-step, autonomous, uses tools.
```

### The Agent Loop

```
┌──────────────────────────────────────────────┐
│                 AGENT LOOP                    │
│                                              │
│  [Observe] → [Think/Plan] → [Act] → [Check] │
│      ^                                  |    │
│      └──────────────────────────────────┘    │
│         Repeat until task is complete         │
└──────────────────────────────────────────────┘
```

| Component | What It Does | Example |
|-----------|-------------|---------|
| **Observe** | Read the current state / user input | "User wants flight to NYC" |
| **Think/Plan** | Reason about what to do next | "I need to search flights, then compare prices" |
| **Act** | Use a tool or generate a response | Call flight search API |
| **Check** | Evaluate if the task is complete | "Found 3 options, need to pick cheapest" |

Agents are covered in depth in the later courses of this specialization, using **LangChain Agents** and **LangGraph**.

### Multi-Model Approach

No single model is best at everything. Enterprise AI uses different models for different tasks:

```
            ┌─────────────────────┐
            │   Orchestration     │
            │   (LangChain)       │
            └─────────┬───────────┘
                      |
         ┌────────────┼────────────┐
         |            |            |
    ┌────v────┐  ┌────v────┐  ┌───v─────┐
    │ Granite │  │ Mixtral │  │ IBM     │
    │ (fast,  │  │ (complex│  │ Slate   │
    │  simple │  │  reason-│  │ (embed- │
    │  tasks) │  │  ing)   │  │  dings) │
    └─────────┘  └─────────┘  └─────────┘
```

---

## 7. The LangChain Ecosystem Overview

### What Is LangChain?

LangChain is an **open-source framework** for building applications powered by LLMs. Think of it as the glue that connects LLMs to real-world data, tools, and workflows.

### The LangChain Module Map

```
┌──────────────────────────────────────────────────────────────────────┐
│                      LangChain Ecosystem                             │
│                                                                      │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────────┐  │
│  │ langchain-core   │  │ langchain         │  │ langchain-community│  │
│  │                  │  │                   │  │                    │  │
│  │ Base abstractions│  │ Chains, agents,   │  │ 700+ integrations  │  │
│  │ LCEL, runnables, │  │ retrieval         │  │ (OpenAI, IBM,      │  │
│  │ prompts, parsers │  │ strategies        │  │  Pinecone, etc.)   │  │
│  └─────────────────┘  └──────────────────┘  └────────────────────┘  │
│                                                                      │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────────┐  │
│  │ langgraph        │  │ langserve         │  │ langsmith          │  │
│  │                  │  │                   │  │                    │  │
│  │ Stateful, multi- │  │ Deploy chains as  │  │ Debug, test,       │  │
│  │ actor agent      │  │ REST APIs         │  │ monitor, and       │  │
│  │ workflows        │  │                   │  │ evaluate LLM apps  │  │
│  └─────────────────┘  └──────────────────┘  └────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

| Module | What It Does | When You'll Use It |
|--------|-------------|-------------------|
| **langchain-core** | Base abstractions — prompts, output parsers, runnables, LCEL | Every LangChain app (always imported) |
| **langchain** | Higher-level chains, agents, retrieval strategies | Building RAG pipelines, conversational agents |
| **langchain-community** | Third-party integrations (LLM providers, vector stores, tools) | Connecting to IBM watsonx, OpenAI, Chroma, etc. |
| **langchain-ibm** | IBM-specific integration package | Using Granite/Mixtral via watsonx (used throughout this course) |
| **langgraph** | Build stateful, multi-step agent workflows as graphs | Agentic AI (later courses in this specialization) |
| **langserve** | Deploy LangChain chains/agents as REST API endpoints | Serving your app as a web service |
| **langsmith** | Observability — trace, debug, test, and evaluate LLM apps | Debugging and monitoring in production |

### How LangChain Connects the Concepts

```
NLP Tasks ──────> solved by ──────> LLMs
                                     |
                               accessed via ──────> LangChain
                                                      |
                              ┌───────────────────────┼───────────────────────┐
                              |                       |                       |
                         Prompt Templates        Chains & LCEL           Agents & Tools
                         (structure input)       (compose pipelines)     (autonomous actions)
                              |                       |                       |
                         Embeddings &            Memory                  LangGraph
                         Vector Stores           (conversation state)    (complex workflows)
                         (enable RAG)
```



### How Fundamentals Map to Course Topics

| Fundamental (Course 0) | Where It's Used |
|------------------------|----------------|
| NLP tasks & tokenization | Understanding what LLMs do (Course 1) |
| Embeddings & vector spaces | RAG pipeline — document retrieval (Course 2) |
| Transformer architecture | Understanding model capabilities and limitations (All courses) |
| LLM generation (next-token prediction) | Prompt engineering, temperature tuning (Course 1) |
| Context windows | Designing prompts, chunking documents for RAG (Course 1-2) |
| RAG concept | Full implementation with LangChain (Course 2) |
| Agentic AI concept | LangGraph agent workflows (Course 3) |
| LangChain ecosystem | The framework used throughout all courses |

### Glossary of Key Terms

| Term | Definition |
|------|-----------|
| **Token** | The smallest unit of text processed by an LLM (roughly a word or subword) |
| **Embedding** | A numerical vector representing the meaning of text |
| **Vector Store** | A database optimized for storing and searching embeddings |
| **Context Window** | Maximum tokens an LLM can process in one request |
| **Inference** | Using a trained model to generate predictions/outputs |
| **Hallucination** | When an LLM generates plausible but factually incorrect content |
| **Grounding** | Anchoring LLM responses in retrieved factual data |
| **RAG** | Retrieval-Augmented Generation — giving LLMs access to external knowledge |
| **Fine-tuning** | Further training a pre-trained model on domain-specific data |
| **Agent** | An AI system that can plan, reason, and use tools autonomously |
| **Chain** | A sequence of LLM operations piped together (LangChain concept) |
| **LCEL** | LangChain Expression Language — the pipe-based syntax for building chains |
| **Foundation Model** | A large pre-trained model that serves as the base for many applications |
| **BPE** | Byte Pair Encoding — the subword tokenization algorithm used by most LLMs |
| **MoE** | Mixture of Experts — architecture that activates only a subset of parameters per input |

---

> **Next:** [Course 1: Generative AI Applications](../Course1_Generative_AI_Applications/Course1_Generative_AI_Applications.md) — Prompt Engineering, LangChain Core, Chains, Agents, and Building Apps

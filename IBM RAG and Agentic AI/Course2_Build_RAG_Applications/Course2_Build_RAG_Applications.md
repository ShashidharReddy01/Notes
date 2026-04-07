# Course 2: Building RAG Applications

> **IBM RAG and Agentic AI Specialization**
> Covers: RAG Fundamentals, Embedding & Vector Stores, Retrieval Strategies, LlamaIndex, Gradio, LangChain vs LlamaIndex Comparison

---

## Table of Contents

1. [Introduction to RAG](#1-introduction-to-rag)
2. [Why RAG Exists -- The LLM Problem Space](#2-why-rag-exists----the-llm-problem-space)
3. [RAG vs Long Context Windows](#3-rag-vs-long-context-windows)
4. [The RAG Pipeline -- End to End](#4-the-rag-pipeline----end-to-end)
5. [Document Loading and Preprocessing](#5-document-loading-and-preprocessing)
6. [Chunking Strategies](#6-chunking-strategies)
7. [Embeddings -- From Text to Vectors](#7-embeddings----from-text-to-vectors)
8. [Vector Stores](#8-vector-stores)
9. [Retrieval -- Finding What Matters](#9-retrieval----finding-what-matters)
10. [Prompt Augmentation and Response Generation](#10-prompt-augmentation-and-response-generation)
11. [LlamaIndex -- Framework Deep Dive](#11-llamaindex----framework-deep-dive)
12. [Gradio -- Building Interfaces for RAG](#12-gradio----building-interfaces-for-rag)
13. [LangChain vs LlamaIndex -- Comparative Analysis](#13-langchain-vs-llamaindex----comparative-analysis)
14. [RAG Evaluation, Failure Modes, and Production Patterns](#14-rag-evaluation-failure-modes-and-production-patterns)

---

## 1. Introduction to RAG

### What It Is

**Retrieval-Augmented Generation (RAG)** is an AI framework that enhances LLM outputs by grounding them in external, retrieved knowledge before generating a response. Instead of relying solely on what a model "memorized" during training, RAG injects relevant source documents into the prompt at inference time.

The name breaks down into three parts:
- **Retrieval** -- fetching relevant information from an external knowledge base
- **Augmented** -- combining that information with the user's original query
- **Generation** -- using an LLM to produce a final, context-aware response

### The Core Idea

An LLM without RAG is like answering from memory -- confident but potentially wrong or outdated. An LLM with RAG is like answering after consulting a reference library -- grounded, sourced, and verifiable.

```
Without RAG:  User Query --> LLM (parametric memory only) --> Response (may hallucinate)

With RAG:     User Query --> Retriever --> Relevant Docs --> LLM + Context --> Grounded Response
```

### Why It Matters

RAG addresses the fundamental tension in LLM design: models are **static** after training, but the world is **dynamic**. Retraining is expensive and slow. RAG decouples knowledge from the model itself.

### Example: Before and After RAG

**Scenario:** A user asks an enterprise chatbot about the company's mobile device policy.

**Without RAG:**
```
User: "What is our company's mobile device policy?"
LLM:  "Generally, companies require employees to use MDM software
       and avoid storing sensitive data on personal devices..."
       (Generic guess based on training data -- not YOUR company's policy)
```

**With RAG:**
```
User: "What is our company's mobile device policy?"

Step 1 - Retrieve: System searches vector store, finds chunks from "CompanyPolicy_2024.pdf"
Step 2 - Augment:  Combines policy text + user question into a single prompt
Step 3 - Generate: LLM reads the actual policy and responds

LLM:  "According to our mobile device policy (Section 3.2), all employees
       must enroll devices through Intune MDM. Personal devices accessing
       company email require a 6-digit PIN and full-disk encryption."
       (Grounded in actual company documents with traceable source)
```

The difference: the first response is a plausible guess; the second is a **sourced, verifiable answer** from actual company documents.

---

## 2. Why RAG Exists -- The LLM Problem Space

### Core LLM Challenges

LLMs, despite their impressive capabilities, exhibit several well-known failure modes:

| Challenge | Description | Example |
|---|---|---|
| **Hallucination** | Model generates plausible but fabricated information | Citing a paper that doesn't exist |
| **Stale Knowledge** | Training data has a cutoff date | Answering "Jupiter" when Saturn now has more known moons |
| **No Source Attribution** | Responses lack provenance | Confident answer with no way to verify |
| **Data Leakage** | Model may regurgitate sensitive training data | Surfacing private information |
| **Domain Blindness** | Poor performance on specialized/proprietary knowledge | Unable to answer about a company's internal mobile policy |

### How RAG Addresses Each

- **Hallucination** --> Model is instructed to answer *only* from retrieved context. If context is insufficient, the model can say "I don't know."
- **Stale Knowledge** --> Update the external data store; no retraining needed. New moons discovered? Add the updated document.
- **No Source Attribution** --> Retrieved chunks carry metadata (source document, page number, date). Responses can cite evidence.
- **Data Leakage** --> Model focuses on retrieved content rather than parametric memory, reducing uncontrolled leakage.
- **Domain Blindness** --> Connect the LLM to a domain-specific knowledge base (company policies, medical records, legal filings).

> **System Design Intuition:** RAG separates the *knowledge layer* from the *reasoning layer*. The LLM does reasoning; the vector store holds knowledge. This is analogous to how a database separates storage from application logic.

### Example: The Moons Problem (From Course Lecture)

This walkthrough illustrates every LLM challenge and how RAG fixes them:

```
Question: "In our solar system, what planet has the most moons?"

── Without RAG ──────────────────────────────────────────────────
LLM (trained on data up to 2023): "Jupiter, with 95 moons."
Problems:
  1. No source cited (where did 95 come from?)
  2. Out of date (Saturn surpassed Jupiter in 2023 with 146 confirmed moons)
  3. Hallucination risk (confident but wrong)

── With RAG ─────────────────────────────────────────────────────
1. Retriever queries knowledge base (containing NASA's updated moon catalog)
2. Retrieved chunk: "As of 2024, Saturn has 146 confirmed moons,
   surpassing Jupiter's 95." (Source: NASA Solar System Exploration)
3. Augmented prompt = instruction + retrieved chunk + user question
4. LLM response: "Saturn, with 146 confirmed moons. (Source: NASA)"
Benefits:
  1. Sourced -- cites NASA data
  2. Current -- data store was updated, no retraining needed
  3. Verifiable -- user can check the source
  4. "I don't know" capable -- if no relevant data found, model admits it
```

This single example demonstrates all five LLM challenges and their RAG solutions.

### RAG Does Not Eliminate All Risk

RAG reduces but does not eliminate hallucination and errors:

| Failure Mode | What Goes Wrong | Mitigation |
|---|---|---|
| **Poor retrieval** | Retriever returns irrelevant chunks; LLM generates from noise | Improve chunking, embedding model, or retrieval strategy |
| **Contradictory sources** | Multiple retrieved chunks disagree | Add source ranking, recency weighting, or conflict resolution logic |
| **Over-reliance on context** | LLM ignores its own reasoning in favor of poor context | Balance instruction: "use context but apply judgment" |
| **Answerable but unanswered** | Retriever fails to find relevant data that exists | Improve recall with hybrid retrieval, query expansion |

---

## 3. RAG vs Long Context Windows

Modern LLMs support context windows of 128K+ tokens (~96,000 English words). This raises a valid question: **why not just paste everything into the prompt?**

### Limitations of Long Context Alone

| Limitation | Explanation |
|---|---|
| **Input Dependency** | User must already possess and provide the source documents |
| **Capacity Ceiling** | Even 128K tokens cannot hold truly large corpora (e.g., War and Peace exceeds 560K words) |
| **Needle in a Haystack** | Irrelevant or redundant text dilutes model attention; critical facts get lost |
| **Latency** | More tokens = more processing time; inference cost scales with context length |
| **Cost** | Token-based pricing makes long contexts expensive at scale |

### How RAG Solves These

- **Input Dependency** --> RAG connects to external stores; users don't need to supply documents
- **Capacity Ceiling** --> RAG retrieves *only* the most relevant chunks, fitting within any context limit
- **Redundancy** --> Retriever acts as a relevance filter, passing only pertinent content
- **Latency** --> Shorter augmented prompts generate faster responses
- **Cost** --> Fewer tokens per request = lower inference cost

> **Interview Insight:** "Can't you just use a longer context window?" is a common question. The answer: long context is a *complement* to RAG, not a replacement. RAG provides targeted retrieval, source attribution, and cost efficiency that long context alone cannot.

---

## 4. The RAG Pipeline -- End to End

### High-Level Architecture

```
┌─────────────────── INDEXING PHASE (offline) ───────────────────┐
│                                                                 │
│  Source Docs --> Preprocessing --> Chunking --> Embedding --> Vector Store
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────── QUERY PHASE (online) ────────────────────────┐
│                                                                  │
│  User Prompt --> Embed Prompt --> Retrieve Top-K --> Augment Prompt --> LLM --> Response
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Step-by-Step Breakdown

1. **Gather Sources** -- Collect documents (PDFs, policies, web pages, databases)
2. **Preprocess** -- Convert to machine-friendly formats (PDF --> plain text, HTML --> markdown)
3. **Chunk** -- Split documents into smaller, semantically coherent segments
4. **Embed Sources** -- Convert each chunk into a fixed-length vector via an embedding model
5. **Store Vectors** -- Insert embeddings into a vector database (ChromaDB, FAISS, Milvus, etc.)
6. **Accept User Prompt** -- Receive the natural language query
7. **Embed Prompt** -- Convert the query into a vector using the *same* embedding model
8. **Retrieve** -- Find the top-K most similar chunk vectors using distance metrics
9. **Augment Prompt** -- Combine retrieved text with the original query (concatenation or template)
10. **Generate Response** -- LLM processes the augmented prompt and produces a grounded answer

> **Key Principle:** The embedding model used for source documents and the user prompt must be identical. Mismatched models produce incompatible vector spaces, and retrieval will fail silently.

### Worked Example: Company Mobile Policy RAG Flow

Let's trace a concrete query through every pipeline step:

```
── INDEXING PHASE (done once, offline) ──────────────────────────

Source: "CompanyMobilePolicy.pdf" (7 pages)

Step 1 - Preprocess:  PDF --> plain text (strip headers/footers)
Step 2 - Chunk:       Split into 7 chunks (one per section)
         Chunk 0: "All employees must register devices via Intune..."
         Chunk 1: "Personal devices require 6-digit PIN..."
         Chunk 2: "Company-issued laptops include pre-installed VPN..."
         Chunk 3: "Mobile hotspot usage is restricted to..."
         Chunk 4: "International roaming must be pre-approved..."
         Chunk 5: "Lost or stolen devices must be reported within 24h..."
         Chunk 6: "BYOD program enrollment requires manager approval..."

Step 3 - Embed:      Each chunk --> 768-dim vector via BAAI/bge-small-en
         Chunk 0 --> [0.12, -0.34, 0.56, ...]   (768 numbers)
         Chunk 1 --> [0.08, -0.29, 0.61, ...]
         ...
         Chunk 6 --> [0.15, -0.41, 0.48, ...]

Step 4 - Store:      All 7 vectors --> ChromaDB with chunk IDs as keys

── QUERY PHASE (per user request, online) ───────────────────────

User query: "Can I use my personal phone for work email?"

Step 5 - Embed query:  --> [0.09, -0.31, 0.59, ...]  (768 numbers)

Step 6 - Retrieve (Top-3):
         Compare query vector against all 7 chunk vectors:
           Chunk 1: cosine_sim = 0.92  <-- highest (personal devices + PIN)
           Chunk 6: cosine_sim = 0.87  <-- BYOD program
           Chunk 0: cosine_sim = 0.81  <-- device registration
           Chunk 5: cosine_sim = 0.73
           Chunk 2: cosine_sim = 0.45
           ...
         Selected: Chunks 1, 6, 0

Step 7 - Augment prompt:
         "Given the following context, answer the question.
          Context: [Chunk 1 text] [Chunk 6 text] [Chunk 0 text]
          Question: Can I use my personal phone for work email?"

Step 8 - Generate:
         LLM: "Yes, you can use your personal phone for work email
               through the BYOD program. You'll need manager approval
               (Section 7), must enroll via Intune (Section 1), and
               set a 6-digit PIN (Section 2)."
```

---

## 5. Document Loading and Preprocessing

### What It Is

The first step in any RAG pipeline: converting raw documents into a normalized, machine-readable representation that downstream components (chunkers, embedding models) can consume.

### Why It Matters

Real-world data is messy. PDFs contain layout artifacts. HTML has boilerplate navigation. Word docs mix formatting with content. Without proper preprocessing, downstream embeddings will be noisy and retrieval quality degrades.

### Common Source Formats

- Plain text (.txt)
- PDF (.pdf)
- Markdown (.md)
- CSV / TSV
- JSON
- HTML
- Word documents (.docx)
- PowerPoint (.pptx)

### Preprocessing Steps

1. **Format Conversion** -- PDF to text, HTML to markdown, DOCX to plain text
2. **Noise Removal** -- Strip headers, footers, navigation elements, boilerplate
3. **Metadata Extraction** -- Capture document title, author, date, source URL
4. **Encoding Normalization** -- Ensure consistent UTF-8 encoding

### Framework Support

| Capability | LlamaIndex | LangChain |
|---|---|---|
| **Primary Loader** | `SimpleDirectoryReader` (handles many formats natively) | Format-specific loaders (`TextLoader`, `CSVLoader`, `PDFLoader`) |
| **Directory Loading** | Built-in with recursive option | `DirectoryLoader` (uses `UnstructuredLoader` by default) |
| **External Connectors** | LlamaHub registry | Extensive integrations (SQL, S3, Figma, etc.) |

### LlamaIndex Document Class

When loaded, each document becomes a `Document` object containing:
- **`id_`** -- Unique identifier
- **`text`** -- The document's content
- **`metadata`** -- Dictionary for origin, date, custom fields
- **`embedding`** -- Placeholder (populated later)
- **`relationships`** -- Links to other documents or nodes

```python
from llama_index.core import Document

doc = Document(text="Company mobile policy content here...")
print(doc.dict())  # Inspect all fields
```

### Loading with SimpleDirectoryReader

```python
from llama_index.core import SimpleDirectoryReader

# Load all files in a directory
docs = SimpleDirectoryReader("./data").load_data()

# Recursive (include subdirectories)
docs = SimpleDirectoryReader("./data", recursive=True).load_data()

# Specific files only
docs = SimpleDirectoryReader(input_files=["./policy.pdf", "./faq.md"]).load_data()

# Specific file types only
docs = SimpleDirectoryReader("./data", required_exts=[".pdf", ".md"]).load_data()
```

---

## 6. Chunking Strategies

### What It Is

Chunking is the process of splitting large documents into smaller, semantically coherent segments (chunks or "nodes" in LlamaIndex terminology) suitable for embedding and retrieval.

### Why It Exists

Embedding models produce a **single fixed-length vector per input**. If you embed an entire 50-page document as one vector, all the nuance and specificity is averaged out. A question about paragraph 3 would match weakly against the entire document's embedding. Chunking ensures that each vector represents a focused, specific piece of content.

### The Chunking Trade-Off

```
Too Large Chunks:   Lose specificity --> retrieval returns vaguely relevant blobs
Too Small Chunks:   Lose context --> retrieval returns fragments without meaning
Optimal Chunks:     Retain enough context to be meaningful, small enough to be specific
```

### Chunking Taxonomy

#### Fixed-Size Chunking
- Splits text after every N characters or N tokens
- Simple, predictable, fast
- **Trade-off:** Cuts mid-sentence, mid-paragraph, or even mid-word
- **When to use:** Prototyping, uniform-length documents

#### Token-Based Chunking
- Encodes text into tokens, splits at token boundaries, decodes back
- Respects tokenizer's vocabulary (no mid-token cuts)
- **Trade-off:** Still doesn't respect semantic boundaries
- **When to use:** When you need precise control over token counts (LLM context limits)

#### Sentence-Based Chunking
- Splits on sentence boundaries (periods, newlines)
- Preserves grammatical completeness
- **Trade-off:** Sentences vary wildly in length; may still cut across ideas
- **When to use:** General-purpose; good default for most text

#### Recursive Character Splitting
- Attempts to split on a hierarchy of delimiters: `["\n\n", "\n", ".", " "]`
- Falls back to finer-grained delimiters only when chunks exceed max size
- **Trade-off:** Slightly more complex logic; may still miss semantic boundaries
- **When to use:** Best general-purpose strategy for unstructured text

#### Semantic Chunking
- Uses an embedding model to measure similarity between consecutive sentences
- Splits wherever similarity drops below a threshold
- **Trade-off:** Computationally expensive; requires an embedding model at chunking time
- **When to use:** When content shifts topics frequently within documents

#### Document-Aware / Structural Chunking
- Leverages document structure: markdown headings, HTML tags, JSON keys, code blocks
- Splits at structural boundaries that the author intended
- **Trade-off:** Requires structured input; doesn't work on plain text
- **When to use:** Markdown docs, code files, HTML pages, JSON data

#### Hierarchical Chunking
- Creates multi-level chunks: large parent chunks contain smaller child chunks
- Retrieval can return the child (for precision) or the parent (for context)
- **Trade-off:** More complex indexing; requires parent-child tracking
- **When to use:** When you need both precision and surrounding context

#### Overlapping vs Non-Overlapping

- **Overlap** means consecutive chunks share some tokens at their boundaries
- Prevents information loss at chunk boundaries
- Typical overlap: 10-20% of chunk size

```
Chunk 1: [A B C D E F G H]
Chunk 2:         [F G H I J K L M]    <-- overlap of F G H
```

### Chunk Size Guidelines

| Document Type | Suggested Chunk Size | Overlap |
|---|---|---|
| Technical documentation | 256-512 tokens | 50-100 tokens |
| Legal / policy documents | 512-1024 tokens | 100-200 tokens |
| Conversational text | 128-256 tokens | 25-50 tokens |
| Code | By function/class | Minimal |

> **Design Pitfall:** Choosing chunk size without considering your embedding model's max input length. If your model accepts 512 tokens max, chunks of 1024 tokens will be truncated silently.

### Framework Implementations

**LlamaIndex -- SentenceSplitter:**
```python
from llama_index.core.node_parser import SentenceSplitter

splitter = SentenceSplitter(chunk_size=256, chunk_overlap=50)
nodes = splitter.get_nodes_from_documents(documents)
```

**LangChain -- RecursiveCharacterTextSplitter:**
```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=100)
chunks = splitter.split_documents(documents)
```

### Additional Splitters

| Splitter | Framework | Splits On |
|---|---|---|
| `SemanticSplitterNodeParser` | LlamaIndex | Embedding similarity threshold |
| `SemanticChunker` | LangChain | Embedding similarity threshold |
| `MarkdownHeaderTextSplitter` | LangChain | Markdown heading levels |
| `MarkdownNodeParser` | LlamaIndex | Markdown structure |
| `CodeSplitter` | LlamaIndex | Code structure (functions, classes) |
| `LangChainNodeParser` | LlamaIndex | Wraps any LangChain splitter |

### Example: Chunking the Same Text with Different Strategies

**Source text (excerpt from a mobile policy):**
```
All employees must register their mobile devices through the Intune
management portal within 48 hours of receiving the device. Personal
devices used for work must have a 6-digit PIN enabled.

The BYOD program allows employees to use personal smartphones for
company email and calendar access. Enrollment requires written
manager approval and completion of the security awareness training.
```

**Fixed-size chunking (chunk_size=120 chars, no overlap):**
```
Chunk 1: "All employees must register their mobile devices through the Intune
          management portal within 48 hours of receiving the dev"
Chunk 2: "ice. Personal devices used for work must have a 6-digit PIN enabled.
          The BYOD program allows employees to use personal sma"
Chunk 3: "rtphones for company email and calendar access. Enrollment requires
          written manager approval and completion of the security awareness training."
```
Problem: "device" split across chunks, paragraphs merged.

**Sentence-based chunking:**
```
Chunk 1: "All employees must register their mobile devices through the Intune
          management portal within 48 hours of receiving the device."
Chunk 2: "Personal devices used for work must have a 6-digit PIN enabled."
Chunk 3: "The BYOD program allows employees to use personal smartphones for
          company email and calendar access."
Chunk 4: "Enrollment requires written manager approval and completion of the
          security awareness training."
```
Better: clean sentence boundaries. But Chunk 4 loses context (enrollment for *what*?).

**Recursive splitting (split on "\n\n" first, then "."):**
```
Chunk 1: "All employees must register their mobile devices through the Intune
          management portal within 48 hours of receiving the device. Personal
          devices used for work must have a 6-digit PIN enabled."
Chunk 2: "The BYOD program allows employees to use personal smartphones for
          company email and calendar access. Enrollment requires written
          manager approval and completion of the security awareness training."
```
Best: respects paragraph boundaries, keeps related sentences together.

### Key Takeaways

- Chunking is one of the most impactful decisions in a RAG pipeline
- Start with recursive/sentence splitting; graduate to semantic if needed
- Always use overlap to prevent boundary information loss
- Match chunk size to your embedding model's max input length
- Test retrieval quality with different chunk sizes -- there is no universal best

---

## 7. Embeddings -- From Text to Vectors

### What It Is

Embedding is the process of converting text into fixed-length numerical vectors that capture semantic meaning. Texts with similar meanings produce vectors that are close together in the embedding space.

### Why It Exists

Computers can't natively compare meaning between strings. "Dog" and "canine" are entirely different character sequences but nearly identical in meaning. Embeddings map both to nearby points in a high-dimensional space, enabling **semantic similarity search** rather than keyword matching.

### How Embedding Works

1. **Tokenization** -- Text is split into tokens (words, subwords, punctuation)
2. **Token Encoding** -- Each token is assigned a numerical ID from the model's vocabulary
3. **Neural Network Processing** -- Token IDs pass through the embedding model's neural network
4. **Vector Output** -- The model produces a fixed-length vector (e.g., 384, 768, or 1536 dimensions)

```
"Company mobile policy"
    --> Tokenize: ["Company", "mobile", "policy"]
    --> Encode:   [4521, 8832, 12045]
    --> Neural Network Processing
    --> Vector:   [0.023, -0.145, 0.891, ..., 0.034]  (768 dimensions)
```

### Prompt Encoding Details

For user prompts, the embedding process uses:
1. **Token Embedding** -- Each token is transformed via a pre-trained model (BERT, GPT, etc.) into a high-dimensional vector
2. **Vector Averaging** -- All token vectors are averaged to produce a single vector representation for the entire prompt

This averaged vector captures the overall semantic meaning of the prompt in a concise form.

### Context Encoding (Knowledge Base)

For knowledge base documents:
1. Break the document into manageable chunks
2. Embed each chunk independently using the same model
3. Each chunk gets its own vector representation
4. Store chunk vectors with their chunk IDs in the vector database

### Critical Rule

> The **same embedding model** must be used for both source documents and user queries. Mixing models produces incompatible vector spaces where similarity scores are meaningless.

### Common Embedding Models

| Model | Dimensions | Provider | Notes |
|---|---|---|---|
| `text-embedding-ada-002` | 1536 | OpenAI | Popular, general-purpose |
| `text-embedding-3-small` | 1536 | OpenAI | Newer, more efficient |
| `all-MiniLM-L6-v2` | 384 | HuggingFace | Fast, lightweight |
| `bge-large-en-v1.5` | 1024 | HuggingFace | Strong benchmark performance |
| `e5-large-v2` | 1024 | HuggingFace | Good for retrieval tasks |

### Example: Semantic Similarity in Action

Embeddings capture meaning, not just keywords. Consider these three texts embedded in the same space:

```
Text A: "How to reset my password"
Text B: "Steps to change account credentials"
Text C: "Best pizza restaurants in New York"

Cosine similarities:
  sim(A, B) = 0.91   <-- semantically similar despite zero shared keywords
  sim(A, C) = 0.12   <-- completely unrelated
  sim(B, C) = 0.08   <-- completely unrelated
```

This is why embeddings enable **semantic search** rather than keyword matching. A keyword-based system would score sim(A, B) = 0 because they share no words.

### Embedding Space Intuition

```
(Suggested diagram: 2D projection of embedding space)

                    "password reset"  *  * "change credentials"
                                       |
                                       |  (close together = similar meaning)
                                       |
    "pizza restaurants"  *             |
                                       |
         "Italian food"  *             |
                                       |
                    "login issues"  *  * "account access problems"
```

Points that are semantically related cluster together. The embedding model learns these relationships from its training data.

### What Makes a Good Embedding Model?

| Quality | Description |
|---|---|
| **Semantic fidelity** | Similar meanings produce similar vectors |
| **Domain alignment** | Trained or fine-tuned on data similar to your use case |
| **Dimensionality** | Higher dims = more nuance, but more storage/compute |
| **Speed** | Inference latency matters for real-time applications |
| **Max input length** | Must accommodate your chunk sizes (512, 1024, 8192 tokens) |

> **Production Tip:** General-purpose embeddings work well for most tasks. But for highly specialized domains (legal, medical, financial), fine-tuning the embedding model on domain-specific data can significantly improve retrieval quality.

### Key Takeaways

- Embeddings are the bridge between human language and machine-searchable vectors
- Quality of embeddings directly determines quality of retrieval
- Always use the same model for documents and queries
- Higher dimensions capture more nuance but cost more to store and search

---

## 8. Vector Stores

### What It Is

A vector store (or vector database) is a specialized database optimized for storing, indexing, and searching high-dimensional vectors. It enables fast approximate nearest neighbor (ANN) search across millions or billions of embeddings.

### Why It Exists

Traditional databases index on exact values (B-trees, hash indexes). Embedding vectors require **similarity-based search** across hundreds of dimensions. Vector stores use specialized indexing algorithms (HNSW, IVF, PQ) to make this fast.

### Popular Vector Stores

| Vector Store | Type | Key Feature |
|---|---|---|
| **ChromaDB** | Open-source, embedded | Easy setup, good for prototyping |
| **FAISS** | Library (Facebook) | Extremely fast, in-memory, no server needed |
| **Milvus** | Distributed database | Scalable, production-grade |
| **Pinecone** | Managed cloud service | Zero-ops, serverless option |
| **pgvector** | PostgreSQL extension | Integrates with existing Postgres infrastructure |
| **Weaviate** | Open-source database | Built-in vectorization, hybrid search |
| **Qdrant** | Open-source database | Rich filtering, Rust-based performance |

### How Vector Search Works: Indexing Algorithms

When you have millions of vectors, comparing the query against *every* vector (brute force) is too slow. Vector stores use **Approximate Nearest Neighbor (ANN)** algorithms to make search fast:

| Algorithm | Full Name | How It Works | Trade-off |
|---|---|---|---|
| **HNSW** | Hierarchical Navigable Small World | Builds a multi-layer graph; searches by "hopping" between connected nodes, narrowing down at each layer | Best accuracy/speed balance; higher memory usage |
| **IVF** | Inverted File Index | Clusters vectors into buckets; only searches the most relevant clusters | Fast for large datasets; may miss vectors in nearby clusters |
| **PQ** | Product Quantization | Compresses vectors by splitting them into sub-vectors and quantizing each | Very memory-efficient; some accuracy loss from compression |
| **HNSW+PQ** | Combined | HNSW for graph navigation + PQ for compression | Production favorite: fast, memory-efficient, good accuracy |

**Analogy:** Brute-force search is like checking every book in a library. IVF is like going to the right section first. HNSW is like asking a librarian who points you to the right shelf, then the right book.

> **Interview Insight:** "How does vector search scale?" HNSW gives O(log n) search time vs O(n) for brute force. For 10 million vectors, that's ~23 comparisons vs 10 million.

### In-Memory vs Persistent Storage

- **In-Memory** (default in LlamaIndex's `VectorStoreIndex`) -- Fast, lost on restart, good for prototyping
- **Persistent** (ChromaDB, FAISS on disk, Milvus) -- Survives restarts, suitable for production

### LlamaIndex Integration

```python
# Simple in-memory (default)
from llama_index.core import VectorStoreIndex
index = VectorStoreIndex(nodes)

# With ChromaDB (persistent)
import chromadb
from llama_index.vector_stores.chroma import ChromaVectorStore
from llama_index.core import StorageContext

chroma_client = chromadb.PersistentClient(path="./chroma_db")
collection = chroma_client.get_or_create_collection("my_collection")
vector_store = ChromaVectorStore(chroma_collection=collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

index = VectorStoreIndex(nodes, embed_model=embed_model, storage_context=storage_context)
```

> **Design Insight:** LlamaIndex's `VectorStoreIndex` wraps the vector store backend. You can swap ChromaDB for FAISS or Milvus without changing downstream query code. LangChain exposes each vector store's native API, giving more control but requiring backend-specific code.

---

## 9. Retrieval -- Finding What Matters

### What It Is

Retrieval is the process of finding the most relevant document chunks from the vector store given a user's query. It bridges the gap between what the user asked and what the knowledge base contains.

### How It Works

1. User query is embedded into a vector using the same embedding model
2. The retriever compares this query vector against all stored chunk vectors
3. Chunks are ranked by similarity (distance metric)
4. Top-K most similar chunks are returned

### Distance Metrics

The choice of distance metric affects which chunks are considered "most similar."

| Metric | Measures | Best For | Formula Intuition |
|---|---|---|---|
| **Cosine Similarity** | Angular difference between vectors | When magnitude doesn't matter (normalized embeddings) | How much two vectors point in the same direction |
| **Dot Product** | Direction + magnitude | When vector magnitude carries meaning | Overall alignment including strength |
| **Euclidean Distance** | Straight-line distance | When absolute position in space matters | How far apart two points are |

**Example:**

Given query embedding `q` and knowledge base embeddings `c1`, `c2`:
- **Dot product** may favor `c2` if it has both similar direction and larger magnitude
- **Cosine similarity** may also favor `c2` but purely based on directional alignment
- The metric you choose can change which chunks are retrieved

### Top-K Selection

- **K** is a hyperparameter: how many chunks to retrieve
- Typical values: K = 3 to 5 for most applications
- Too few --> may miss relevant context
- Too many --> adds noise, increases latency, inflates token cost

### Retrieval in LlamaIndex

```python
# Create retriever from index
retriever = index.as_retriever(similarity_top_k=5)

# Retrieve nodes for a query
results = retriever.retrieve("What is the company's mobile policy?")
# Returns a ranked list: most similar first
```

### Advanced Retrieval Patterns

| Pattern | Description | When to Use |
|---|---|---|
| **Top-K Retrieval** | Return K most similar chunks | Default, works for most cases |
| **Parent Document Retrieval** | Return the full document containing the matching chunk | When surrounding context is critical |
| **Multi-Query Retrieval** | Rephrase the query multiple ways, retrieve for each | Ambiguous or complex queries |
| **Hybrid Retrieval** | Combine dense (vector) + sparse (BM25/keyword) search | When exact keyword matches matter alongside semantic similarity |
| **Re-Ranking** | Retrieve a larger set, then re-rank with a cross-encoder | When initial retrieval quality is insufficient |
| **Metadata Filtering** | Filter by metadata before/after vector search | When documents have categorical attributes (date, department, etc.) |

### Example: Distance Metric Calculation

Given simplified 3-dimensional vectors:

```
Query:    q  = [1.0, 0.5, 0.0]
Chunk 1:  c1 = [0.9, 0.4, 0.1]   (about device registration)
Chunk 2:  c2 = [0.2, 0.8, 0.7]   (about vacation policy)

── Cosine Similarity ─────────────────────────────────────────
cos(q, c1) = (1.0*0.9 + 0.5*0.4 + 0.0*0.1) / (|q| * |c1|)
           = 1.10 / (1.118 * 0.985) = 0.999   <-- very similar
cos(q, c2) = (1.0*0.2 + 0.5*0.8 + 0.0*0.7) / (|q| * |c2|)
           = 0.60 / (1.118 * 1.093) = 0.491   <-- not similar

── Dot Product ───────────────────────────────────────────────
dot(q, c1) = 1.0*0.9 + 0.5*0.4 + 0.0*0.1 = 1.10
dot(q, c2) = 1.0*0.2 + 0.5*0.8 + 0.0*0.7 = 0.60

Both metrics agree: Chunk 1 is more relevant to the query.
```

### Example: Top-K Chunk Selection (From Course)

Given a company mobile policy split into 7 chunks (IDs 0-6):

```
Query: "Can I use my phone for work?"

Distance calculations (cosine similarity):
  Chunk 0: 0.81  (device registration)
  Chunk 1: 0.92  (personal device requirements)   <-- selected
  Chunk 2: 0.45  (laptop VPN setup)
  Chunk 3: 0.38  (hotspot restrictions)
  Chunk 4: 0.29  (international roaming)
  Chunk 5: 0.73  (lost device reporting)
  Chunk 6: 0.87  (BYOD program enrollment)         <-- selected

Top-K=3 selected chunk IDs: [1, 6, 0]  (sorted by similarity)
```

The retriever returns chunks 1, 6, and 0 -- the most relevant to personal phone use for work.

### Common Mistakes

- Using different embedding models for documents and queries
- Setting K too high (retrieves irrelevant chunks that confuse the LLM)
- Not evaluating retrieval quality independently from generation quality
- Ignoring metadata filters when the knowledge base has clear categorical structure

---

## 10. Prompt Augmentation and Response Generation

### What It Is

After retrieval, the system combines the user's original query with the retrieved text to create an **augmented prompt**. This augmented prompt is then fed to the LLM to generate a grounded response.

### Augmented Prompt Structure

A typical augmented prompt has three components:

```
┌──────────────────────────────────────────────┐
│ INSTRUCTION                                   │
│ "Answer the question using only the context   │
│  provided below. If the answer is not in the  │
│  context, say 'I don't know.'"                │
├──────────────────────────────────────────────┤
│ RETRIEVED CONTEXT                             │
│ [Chunk 1 text]                                │
│ [Chunk 2 text]                                │
│ [Chunk 3 text]                                │
├──────────────────────────────────────────────┤
│ USER QUESTION                                 │
│ "What is the company's mobile device policy?" │
└──────────────────────────────────────────────┘
```

### Augmentation Methods

- **Simple Concatenation** -- Join retrieved text and query with a separator
- **Structured Templates** -- Use placeholders filled at runtime with context and query
- **Iterative Refinement** -- If context is too large, process chunks sequentially, refining the answer with each

### Response Synthesizer (LlamaIndex)

LlamaIndex provides a `ResponseSynthesizer` that combines prompt augmentation + LLM querying + response generation:

```python
from llama_index.core import get_response_synthesizer

synthesizer = get_response_synthesizer()
response = synthesizer.synthesize(query="What is the mobile policy?", nodes=retrieved_nodes)
```

The embedding, augmentation, and LLM invocation happen internally -- no manual wiring required.

### Query Engine (LlamaIndex)

The **query engine** goes even further, combining *all* online RAG steps into a single object:

```python
query_engine = index.as_query_engine()
response = query_engine.query("What is the company's mobile policy?")
```

This single call performs: prompt embedding --> retrieval --> augmentation --> LLM generation.

### Customization Options

| What to Customize | How |
|---|---|
| Change the LLM | Pass a different `llm` to the query engine |
| Custom prompt template | Define a `PromptTemplate` and pass it in |
| Custom retriever | Pass a modified retriever with different top-K or filters |
| Response mode | Choose between `compact`, `refine`, `tree_summarize`, etc. |

### Response Modes Explained

LlamaIndex supports multiple strategies for how retrieved chunks are fed to the LLM:

| Mode | How It Works | Best For |
|---|---|---|
| **`compact`** (default) | Stuffs as many chunks as possible into a single prompt | Small to medium retrievals; fastest |
| **`refine`** | Processes chunks one at a time, each refining the previous answer | Large retrievals that exceed context limits |
| **`tree_summarize`** | Builds a tree of summaries from bottom up, then summarizes the summaries | Summarization tasks over many documents |
| **`simple_summarize`** | Truncates all chunks to fit in one prompt, then summarizes | Quick summaries when precision isn't critical |
| **`no_text`** | Returns retrieved nodes without LLM generation | When you only need the raw retrieved chunks |
| **`accumulate`** | Generates a separate answer per chunk, returns all answers | When you want multiple perspectives |

**Example: `compact` vs `refine`**

```
── compact mode ──────────────────────────────
Prompt: [Instruction] + [Chunk1 + Chunk2 + Chunk3] + [Question]
LLM call: 1 (all chunks in one prompt)
Result: Single answer considering all context at once

── refine mode ───────────────────────────────
Prompt 1: [Instruction] + [Chunk1] + [Question]
LLM call 1 --> Answer v1

Prompt 2: "Given this existing answer: [Answer v1],
           refine it using this additional context: [Chunk2]"
LLM call 2 --> Answer v2

Prompt 3: "Given this existing answer: [Answer v2],
           refine it using this additional context: [Chunk3]"
LLM call 3 --> Answer v3 (final)

Result: Iteratively refined answer (3 LLM calls, handles large context)
```

### Example: A Complete Augmented Prompt

Here is what the LLM actually receives (after augmentation):

```
System: You are a helpful assistant that answers questions about company
policies. Use ONLY the provided context to answer. If the answer is not
in the context, say "I don't have enough information to answer that."

Context:
---
[Chunk 1] All employees must register their mobile devices through the
Intune management portal within 48 hours of receiving the device.
Personal devices used for work must have a 6-digit PIN enabled.
---
[Chunk 6] The BYOD program allows employees to use personal smartphones
for company email and calendar access. Enrollment requires written
manager approval and completion of the security awareness training.
---
[Chunk 0] Devices must be running a supported operating system version.
Currently supported: iOS 16+, Android 13+, Windows 11.
---

Question: Can I use my personal Android phone for work email?

Answer:
```

The LLM sees the instruction, the three most relevant chunks, and the question -- then generates its response.

### The "I Don't Know" Behavior

One of RAG's most valuable features is enabling the model to **refuse to answer** when the retrieved context doesn't support a response. This is achieved through the instruction component of the augmented prompt.

Without RAG, an LLM will always attempt an answer, often hallucinating. With RAG, the instruction can explicitly say: "If the context does not contain the answer, respond with 'I don't know.'"

> **Production Insight:** The quality of your RAG system depends on *both* the retriever and the generator. A perfect retriever with a weak LLM wastes good context. A powerful LLM with a poor retriever generates confident nonsense. Optimize both sides.

---

## 11. LlamaIndex -- Framework Deep Dive

### What It Is

LlamaIndex is a framework for building LLM-powered **context augmentation** applications. It provides tools for the entire RAG pipeline: loading, chunking, embedding, storing, retrieving, augmenting, and generating.

### Core Use Cases

1. **Question Answering with RAG** -- Primary focus
2. **Chatbots** -- Extends RAG with multi-turn conversation, follow-up questions, and clarification
3. **Document Understanding & Data Extraction** -- Semantically identify names, dates, figures from structured/unstructured data

### LlamaIndex RAG Pipeline

```
Documents --> SimpleDirectoryReader --> Document Objects
    --> SentenceSplitter --> Node Objects
    --> VectorStoreIndex (embed + store)
    --> as_query_engine() --> Query Engine
    --> query("...") --> Grounded Response
```

### Key Classes

| Class | Purpose | Stage |
|---|---|---|
| `Document` | Container for source text + metadata + relationships | Loading |
| `SimpleDirectoryReader` | Multi-format file loader | Loading |
| `SentenceSplitter` | Recursive text chunker (token-based) | Chunking |
| `VectorStoreIndex` | Embeds chunks and stores vectors | Embedding + Storage |
| `Retriever` (via `as_retriever`) | Finds similar chunks given a query | Retrieval |
| `ResponseSynthesizer` | Augments prompt and generates response | Generation |
| `QueryEngine` (via `as_query_engine`) | End-to-end: embed query --> retrieve --> augment --> generate | All online steps |

### Document Class Structure

```python
{
    "id_": "unique-doc-id",
    "embedding": None,           # Populated after embedding
    "metadata": {},              # Custom metadata (source, date, etc.)
    "relationships": {},         # Links to related documents/nodes
    "text": "The actual content"
}
```

### Node (Text Chunk) Structure

Nodes are structurally similar to Documents but represent individual chunks. They inherit `id_`, `metadata`, `relationships`, and contain the chunk's text.

### Splitters Available

| Splitter | Behavior |
|---|---|
| `SentenceSplitter` | Recursive splitting on separators (newlines, periods); token-based size control |
| `SemanticSplitterNodeParser` | Splits where inter-sentence similarity drops below threshold |
| `MarkdownNodeParser` | Splits on markdown headings |
| `HTMLNodeParser` | Splits on HTML structure |
| `JSONNodeParser` | Splits on JSON keys |
| `CodeSplitter` | Splits on code structure |
| `LangChainNodeParser` | Wraps any LangChain text splitter |

### VectorStoreIndex -- The Workhorse

`VectorStoreIndex` is LlamaIndex's most powerful class. In a single command, it:
1. Takes chunked nodes as input
2. Embeds them using the specified (or default) embedding model
3. Stores the vectors in the specified (or default in-memory) vector store

```python
# Minimal (in-memory, default embedding model)
index = VectorStoreIndex(nodes)

# Full control (custom embedding model, persistent ChromaDB)
index = VectorStoreIndex(
    nodes,
    embed_model=HuggingFaceEmbedding(model_name="BAAI/bge-small-en-v1.5"),
    storage_context=storage_context
)
```

### Query Engine Customization

```python
query_engine = index.as_query_engine(
    llm=custom_llm,                    # Swap the default LLM
    similarity_top_k=3,                # Control retrieval count
    response_mode="compact",           # Response synthesis strategy
    text_qa_template=custom_template   # Custom prompt template
)
```

### Complete End-to-End Example

Here is a full, working RAG application in LlamaIndex -- from raw documents to a query response:

```python
# ── Step 1: Install dependencies ────────────────────────────
# pip install llama-index llama-index-embeddings-huggingface

# ── Step 2: Imports ─────────────────────────────────────────
from llama_index.core import (
    SimpleDirectoryReader,
    VectorStoreIndex,
    Settings,
)
from llama_index.core.node_parser import SentenceSplitter
from llama_index.embeddings.huggingface import HuggingFaceEmbedding

# ── Step 3: Configure global settings ──────────────────────
Settings.embed_model = HuggingFaceEmbedding(model_name="BAAI/bge-small-en-v1.5")
# Settings.llm = <your LLM here>  # e.g., OpenAI, watsonx, local model

# ── Step 4: Load documents ─────────────────────────────────
documents = SimpleDirectoryReader(
    input_dir="./company_policies",
    required_exts=[".pdf", ".md", ".txt"]
).load_data()

print(f"Loaded {len(documents)} documents")

# ── Step 5: Chunk into nodes ───────────────────────────────
splitter = SentenceSplitter(chunk_size=256, chunk_overlap=50)
nodes = splitter.get_nodes_from_documents(documents)

print(f"Created {len(nodes)} chunks")

# ── Step 6: Embed + Store (single command) ─────────────────
index = VectorStoreIndex(nodes)

# ── Step 7: Create query engine ────────────────────────────
query_engine = index.as_query_engine(
    similarity_top_k=3,
    response_mode="compact"
)

# ── Step 8: Query ──────────────────────────────────────────
response = query_engine.query("What is the company's remote work policy?")
print(response)

# Access source nodes for attribution
for node in response.source_nodes:
    print(f"  Source: {node.metadata.get('file_name', 'unknown')}")
    print(f"  Score:  {node.score:.3f}")
    print(f"  Text:   {node.text[:100]}...")
```

This is approximately **20 lines of meaningful code** for a complete RAG pipeline. The equivalent in LangChain would require roughly 2-3x more code due to explicit wiring of each step.

---

## 12. Gradio -- Building Interfaces for RAG

### What It Is

Gradio is an open-source Python library for creating web-based user interfaces for machine learning models and computational tools. It requires **no JavaScript, CSS, or web hosting experience**.

### Why It Exists

ML models and RAG pipelines are powerful but inaccessible without an interface. Gradio bridges the gap between a Python function and a usable web application in just a few lines of code.

### How It Works

```
1. Write Python functions (your RAG logic)
2. Create a Gradio Interface (specify inputs/outputs)
3. Launch the server (.launch())
4. Access via local URL (or share publicly)
```

### Core Concepts

#### The Interface Class

The `gr.Interface` class is Gradio's primary building block:

```python
import gradio as gr

def answer_question(query):
    # Your RAG pipeline logic here
    return "Generated answer based on retrieved context"

demo = gr.Interface(
    fn=answer_question,          # The function to wrap
    inputs=gr.Textbox(label="Ask a question"),   # Input component(s)
    outputs=gr.Textbox(label="Answer"),           # Output component(s)
)

demo.launch(server_name="127.0.0.1", server_port=7860)
```

Three core arguments:
- **`fn`** -- The Python function to wrap with a UI
- **`inputs`** -- Gradio component(s) for input (must match function arguments)
- **`outputs`** -- Gradio component(s) for output (must match return values)

#### Input/Output Components

| Component | Type | Use Case |
|---|---|---|
| `gr.Textbox` | Text | Queries, free-form text |
| `gr.Number` | Numeric | Parameters, thresholds |
| `gr.Image` | Image | Image captioning, classification |
| `gr.File` | File upload | Document upload for RAG |
| `gr.Slider` | Slider | Adjustable parameters |
| `gr.Label` | Classification label | Top-K predictions |

#### Multiple Inputs

Pass a list of components to `inputs`:

```python
demo = gr.Interface(
    fn=my_function,
    inputs=[gr.Textbox(label="Query"), gr.Number(label="Top-K")],
    outputs=gr.Textbox(label="Response")
)
```

#### File Upload

```python
def count_files(files):
    return len(files)

demo = gr.Interface(
    fn=count_files,
    inputs=gr.File(file_count="multiple"),
    outputs="text"
)
```

### Practical RAG + Gradio Pattern

```python
import gradio as gr
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader

# Build the RAG pipeline
documents = SimpleDirectoryReader("./data").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()

# Wrap in Gradio
def ask(question):
    response = query_engine.query(question)
    return str(response)

gr.Interface(fn=ask, inputs="text", outputs="text").launch()
```

### Beyond gr.Interface: The Blocks API

For more complex layouts, Gradio provides `gr.Blocks` -- a lower-level API for custom UIs:

```python
import gradio as gr

with gr.Blocks() as demo:
    gr.Markdown("# Company Policy Q&A")

    with gr.Row():
        with gr.Column():
            query_input = gr.Textbox(label="Your Question", lines=3)
            top_k_slider = gr.Slider(1, 10, value=3, label="Number of sources")
            submit_btn = gr.Button("Ask", variant="primary")
        with gr.Column():
            answer_output = gr.Textbox(label="Answer", lines=5)
            sources_output = gr.Textbox(label="Sources", lines=3)

    submit_btn.click(
        fn=rag_query,
        inputs=[query_input, top_k_slider],
        outputs=[answer_output, sources_output]
    )

demo.launch()
```

`gr.Blocks` gives you control over layout (rows, columns, tabs), event handling, and multi-step workflows that `gr.Interface` cannot express.

### Example: Image Classification with Gradio (From Course)

Gradio isn't limited to text. Here's an image classifier using a pretrained ResNet-18:

```python
import torch
import gradio as gr
from torchvision import transforms

model = torch.hub.load('pytorch/vision:v0.6.0', 'resnet18', pretrained=True).eval()

# Download ImageNet labels
import requests
labels = requests.get("https://git.io/JJkYN").text.split("\n")

def predict(image):
    tensor = transforms.ToTensor()(image).unsqueeze(0)
    with torch.no_grad():
        probs = torch.nn.functional.softmax(model(tensor)[0], dim=0)
    return {labels[i]: float(probs[i]) for i in range(1000)}

gr.Interface(
    fn=predict,
    inputs=gr.Image(type="pil"),
    outputs=gr.Label(num_top_classes=3),
    examples=["lion.jpg", "cheetah.jpg"]
).launch()
```

### Sharing and Deployment

| Method | How | Use Case |
|---|---|---|
| **Local** | `demo.launch()` --> `http://127.0.0.1:7860` | Development, testing |
| **Public URL** | `demo.launch(share=True)` --> temporary Gradio URL | Quick demos, sharing with colleagues |
| **Hugging Face Spaces** | Deploy as a Space on HuggingFace | Persistent, free hosting |
| **Docker** | Containerize and deploy anywhere | Production deployment |

> **Practical Tip:** `share=True` creates a public tunnel that expires after 72 hours. For persistent sharing, deploy to Hugging Face Spaces (free for basic usage).

### Key Features

- **Sharing** -- Set `share=True` in `.launch()` to get a public URL
- **Examples** -- Pre-populate with sample inputs using the `examples` parameter
- **Customization** -- Add `title` and `description` for context
- **No frontend expertise needed** -- Pure Python

---

## 13. LangChain vs LlamaIndex -- Comparative Analysis

### Design Philosophy

| Aspect | LangChain | LlamaIndex |
|---|---|---|
| **Philosophy** | Modular, integration-heavy, composable chains | Opinionated defaults, simple APIs, batteries-included |
| **Strength** | Flexibility, extensive integrations, fine-grained control | Ease of use, fewer lines of code, good defaults |
| **Trade-off** | More boilerplate, manual setup | Customization can be harder |

### Stage-by-Stage Comparison

#### Document Loading

| | LangChain | LlamaIndex |
|---|---|---|
| **Primary Loader** | Format-specific loaders (`TextLoader`, `CSVLoader`, `JSONLoader`) | `SimpleDirectoryReader` (handles many formats natively) |
| **Directory** | `DirectoryLoader` (configurable backend) | Built into `SimpleDirectoryReader` with recursive option |
| **Connectors** | Extensive (SQL, S3, Figma, web, etc.) | LlamaHub registry |
| **Winner** | More individual connectors | Better out-of-the-box multi-format loading |

#### Chunking

| | LangChain | LlamaIndex |
|---|---|---|
| **Default Splitter** | `RecursiveCharacterTextSplitter` (character-based size) | `SentenceSplitter` (token-based size) |
| **Semantic** | `SemanticChunker` | `SemanticSplitterNodeParser` |
| **Structural** | Markdown, HTML, JSON, Code splitters | Markdown, HTML, JSON, Code parsers |
| **Interop** | N/A | `LangChainNodeParser` wraps any LangChain splitter |
| **Winner** | Broader set | Token-based defaults are more practical; can use LangChain splitters too |

#### Embedding + Vector Storage

| | LangChain | LlamaIndex |
|---|---|---|
| **Embed + Store** | Separate steps | Single command via `VectorStoreIndex` |
| **Default Store** | `InMemoryVectorStore` | In-memory (via `VectorStoreIndex`) |
| **Metadata** | May require manual setup | Auto-created and stored |
| **Backend Swap** | Requires changing downstream code | Backend-agnostic via wrapper class |
| **Winner** | More granular control | Simpler, backend-agnostic |

#### Retrieval

| | LangChain | LlamaIndex |
|---|---|---|
| **Basic** | Retriever from vector store | `index.as_retriever()` |
| **Advanced** | Parent document retriever, ensemble, etc. | Various retriever types |
| **Winner** | Tie -- both provide comprehensive retrieval options |

#### Prompt Augmentation

| | LangChain | LlamaIndex |
|---|---|---|
| **Approach** | Separate, explicit step | Combined with generation (response synthesizer or query engine) |
| **Customization** | Easy -- template is a standalone object | Slightly harder -- embedded in synthesizer/engine |
| **Winner** | Easier to customize | Fewer steps to set up |

#### LLM Response Generation

| | LangChain | LlamaIndex |
|---|---|---|
| **Approach** | Manual: `llm.invoke(messages)` | Automatic via response synthesizer or query engine |
| **Lines of Code** | More | Fewer |
| **Winner** | More explicit control | Faster development |

### When to Choose Which

| Choose **LangChain** When | Choose **LlamaIndex** When |
|---|---|
| You need fine-grained control over every step | You want to build a RAG app quickly |
| You're building complex agent chains | Your use case is document Q&A or chatbot |
| You need a specific integration not in LlamaIndex | You want sensible defaults without manual wiring |
| You want explicit, readable pipeline code | You want to minimize boilerplate |
| You're building multi-tool agents | You want backend-agnostic vector store code |

### Side-by-Side: The Same RAG Pipeline in Both Frameworks

**LlamaIndex (~ 8 lines of core logic):**
```python
from llama_index.core import SimpleDirectoryReader, VectorStoreIndex

docs = SimpleDirectoryReader("./data").load_data()
index = VectorStoreIndex.from_documents(docs)
engine = index.as_query_engine(similarity_top_k=3)
response = engine.query("What is the refund policy?")
print(response)
```

**LangChain (~ 15 lines of core logic):**
```python
from langchain_community.document_loaders import DirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import Chroma
from langchain_community.embeddings import HuggingFaceEmbeddings
from langchain.chains import RetrievalQA
from langchain_community.llms import OpenAI

docs = DirectoryLoader("./data").load()
splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=100)
chunks = splitter.split_documents(docs)
embeddings = HuggingFaceEmbeddings()
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 3})
qa_chain = RetrievalQA.from_chain_type(llm=OpenAI(), retriever=retriever)
response = qa_chain.invoke("What is the refund policy?")
print(response)
```

The LlamaIndex version is more concise because chunking, embedding, and storage happen inside `VectorStoreIndex.from_documents()`. The LangChain version is more explicit -- every step is visible and independently configurable.

### Key Takeaway

Both frameworks can build production RAG systems. LlamaIndex optimizes for **developer speed** with opinionated defaults. LangChain optimizes for **flexibility** with modular composition. In practice, they are complementary -- LlamaIndex even provides wrappers for LangChain components.

---

## 14. RAG Evaluation, Failure Modes, and Production Patterns

### Why Evaluate RAG?

A RAG system has multiple failure points. A bad response could be caused by poor retrieval, poor generation, or both. You must evaluate each component independently to know *what* to fix.

### The Two-Sided Evaluation Framework

```
┌────────────────────┐         ┌────────────────────┐
│  RETRIEVAL QUALITY  │ ──────> │ GENERATION QUALITY  │
│                    │         │                    │
│ Did we find the    │         │ Did the LLM use    │
│ right chunks?      │         │ the chunks well?   │
└────────────────────┘         └────────────────────┘
```

### Retrieval Metrics

| Metric | What It Measures | Formula Intuition |
|---|---|---|
| **Precision@K** | Of the K retrieved chunks, how many are relevant? | Relevant retrieved / K |
| **Recall@K** | Of all relevant chunks that exist, how many did we retrieve? | Relevant retrieved / Total relevant |
| **MRR (Mean Reciprocal Rank)** | How high is the first relevant result ranked? | 1 / rank of first relevant result |
| **NDCG** | Are relevant results ranked higher than irrelevant ones? | Weighted scoring by position |
| **Hit Rate** | Does at least one relevant chunk appear in top-K? | Binary: yes/no per query |

**Example:**
```
Query: "What is the PTO policy?"
Knowledge base has 2 relevant chunks (IDs: 4, 7)

Retrieved top-5: [4, 12, 7, 3, 9]

Precision@5 = 2/5 = 0.40  (2 relevant out of 5 retrieved)
Recall@5    = 2/2 = 1.00  (found both relevant chunks)
MRR         = 1/1 = 1.00  (first relevant chunk is at position 1)
Hit Rate    = 1            (at least one relevant chunk found)
```

### Generation Metrics

| Metric | What It Measures |
|---|---|
| **Faithfulness** | Does the response only contain information from the retrieved context? (no hallucination) |
| **Answer Relevance** | Does the response actually answer the user's question? |
| **Context Relevance** | Were the retrieved chunks relevant to the question? |
| **Groundedness** | Can every claim in the response be traced to a source chunk? |

### Common RAG Failure Modes

| Failure | Symptom | Root Cause | Fix |
|---|---|---|---|
| **Retrieval miss** | Correct answer exists but isn't retrieved | Poor chunking, wrong embedding model, low K | Improve chunking strategy, try hybrid retrieval, increase K |
| **Retrieval noise** | Irrelevant chunks confuse the LLM | K too high, poor embeddings, no metadata filtering | Lower K, add metadata filters, re-rank results |
| **Lost in the middle** | LLM ignores relevant context in the middle of the prompt | LLM attention bias toward beginning/end of context | Put most relevant chunks first, use `refine` mode |
| **Hallucination despite context** | LLM adds information not in the retrieved chunks | Weak instruction, model tendency to "help" | Stronger system instructions, lower temperature |
| **Stale index** | Correct answer exists in updated docs but index hasn't been refreshed | Index not rebuilt after data updates | Implement incremental indexing, schedule re-indexing |
| **Context overflow** | Too many/large chunks exceed LLM context window | Chunks too large, K too high | Reduce chunk size, lower K, use `refine` mode |

### Production RAG Checklist

- [ ] **Chunking tested** -- Evaluated multiple strategies and sizes on representative queries
- [ ] **Embedding model selected** -- Benchmarked on domain-specific similarity tasks
- [ ] **Retrieval evaluated** -- Measured precision, recall, and MRR on a test set
- [ ] **Prompt template tuned** -- Tested different instructions for faithfulness and "I don't know" behavior
- [ ] **Response quality measured** -- Evaluated faithfulness and answer relevance
- [ ] **Metadata filtering in place** -- For corpora with categorical structure (departments, dates, doc types)
- [ ] **Index refresh strategy** -- Incremental or scheduled re-indexing when source docs change
- [ ] **Monitoring** -- Logging queries, retrieved chunks, and responses for quality tracking
- [ ] **Fallback behavior** -- Graceful "I don't know" when confidence is low
- [ ] **Cost tracking** -- Monitoring token usage, embedding costs, and LLM inference costs

### RAG Architecture Patterns

| Pattern | Description | When to Use |
|---|---|---|
| **Naive RAG** | Single retrieval + single generation | Prototyping, simple Q&A |
| **Advanced RAG** | Pre-retrieval query processing + post-retrieval re-ranking | Production systems needing higher accuracy |
| **Modular RAG** | Pluggable components (query rewriter, retriever, re-ranker, generator) | Enterprise systems with evolving requirements |
| **Agentic RAG** | An agent decides *when* and *how* to use RAG (may skip retrieval, do multi-step) | Complex workflows where retrieval is one of many tools |
| **Graph RAG** | Combines vector retrieval with knowledge graph traversal | When relationships between entities matter |
| **Corrective RAG (CRAG)** | Evaluates retrieval quality and self-corrects before generation | When retrieval confidence is uncertain |

> **System Design Insight:** Start with Naive RAG. Measure. Then add complexity (re-ranking, query rewriting, hybrid retrieval) only where measurements show the bottleneck. Premature optimization in RAG pipelines is as wasteful as in code.

---

## Summary -- Key Takeaways

### RAG Fundamentals
- RAG decouples knowledge (data store) from reasoning (LLM), solving staleness, hallucination, and attribution problems
- The pipeline has two phases: **indexing** (offline) and **querying** (online)
- Every component choice (chunking strategy, embedding model, distance metric, top-K) affects end-to-end quality

### Critical Design Decisions
- **Chunk size** determines the precision-context trade-off
- **Embedding model** must be identical for documents and queries
- **Distance metric** choice affects retrieval ranking
- **Top-K** balances relevance against noise and cost

### Production Considerations
- Optimize both retriever and generator -- they are interdependent
- Enable "I don't know" responses to prevent confident hallucination
- Use metadata filtering to narrow search scope in large corpora
- Monitor retrieval quality independently from generation quality
- Consider hybrid retrieval (dense + sparse) for production systems
- Start with Naive RAG, measure, then add complexity where the data shows bottlenecks

### Evaluation Essentials
- Evaluate retrieval (precision, recall, MRR) and generation (faithfulness, relevance) separately
- A bad answer could be a retrieval problem or a generation problem -- diagnosis requires separate metrics
- Build a test set of queries with known relevant chunks to benchmark against

### Framework Choice
- **LlamaIndex** for speed of development and document-centric RAG
- **LangChain** for flexibility, complex agents, and fine-grained pipeline control
- Both are production-capable; the choice depends on your team's needs

> **If you remember only one thing:** RAG = give the LLM a reference library instead of forcing it to answer from memory. The quality of that library and the retrieval mechanism determine everything.

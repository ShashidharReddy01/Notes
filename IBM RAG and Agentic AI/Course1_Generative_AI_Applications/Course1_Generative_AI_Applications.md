# Course 1: Generative AI Applications

> **IBM RAG and Agentic AI Specialization**
> Covers: Evolution of AI, Prompt Engineering, In-Context Learning, LangChain, Chains, Agents, Flask, and Building GenAI Apps

---

## Table of Contents

1. [Evolution of AI and the GenAI Landscape](#1-evolution-of-ai-and-the-genai-landscape)
2. [Prompt Engineering Fundamentals](#2-prompt-engineering-fundamentals)
3. [In-Context Learning (ICL)](#3-in-context-learning-icl)
4. [Advanced Prompting Techniques](#4-advanced-prompting-techniques)
5. [Introduction to LangChain](#5-introduction-to-langchain)
6. [LangChain Core Components](#6-langchain-core-components)
7. [LangChain Expression Language (LCEL)](#7-langchain-expression-language-lcel)
8. [LangChain Chains](#8-langchain-chains)
9. [LangChain Memory](#9-langchain-memory)
10. [Documents, Embeddings, and Vector Stores](#10-documents-embeddings-and-vector-stores)
11. [Retrievers and Retrieval QA](#11-retrievers-and-retrieval-qa)
12. [LangChain Agents and Tools](#12-langchain-agents-and-tools)
13. [Multi-Model Approach for Enterprise AI](#13-multi-model-approach-for-enterprise-ai)
14. [Building GenAI Applications - From Ideation to Production](#14-building-genai-applications---from-ideation-to-production)
15. [Flask for GenAI Web Applications](#15-flask-for-genai-web-applications)
16. [Building a GenAI Flask App with LangChain](#16-building-a-genai-flask-app-with-langchain)

---

## 1. Evolution of AI and the GenAI Landscape

### What Changed with Generative AI?

Before generative AI, most chatbots were **rule-based** -- they responded to a predefined set of queries programmatically. While AI allowed some flexibility in phrasing, these chatbots were essentially programmed with specific responses. Users often found them frustrating.

Everything changed when conversational generative AI chatbots (ChatGPT, Google Bard, IBM watsonx) became available:
- ChatGPT gained **1 million users within 5 days** of its November 2022 launch
- Conversations felt natural; answers were plausible and valuable
- Behind the scenes: complex mathematics + vast training data power **Large Language Models (LLMs)**

### Why LLMs Matter

- LLMs are powered by terabytes of training data
- The experience is like engaging with a brilliant individual with vast knowledge
- People use them for: **boosting productivity, clear communication, data summarization, decision-making assistance**
- The quality of the interaction depends entirely on your **prompt** -- the text you type in

> **Key Insight:** "Garbage in, garbage out." A vague prompt produces vague results. A well-crafted prompt unlocks the full power of the LLM.

### The AI Journey for Developers

```
Ideation & Experimentation  -->  Building  -->  Operations (MLOps)
      |                            |                    |
  Research models            RAG / Fine-tuning     Containers + K8s
  Evaluate benchmarks        LangChain framework   vLLM for serving
  Prompting techniques       Break down problems   Monitor & govern
```

### Enterprise AI Adoption

> Gartner reported that **80% of enterprises will have used some type of generative AI** through models or APIs by 2026. This signals that AI literacy -- especially knowing how to build and integrate GenAI into applications -- is becoming a core developer skill.

### Key Concepts in the Modern AI Landscape

| Concept | What It Is | Why It Matters |
|---------|-----------|----------------|
| **NLP** (Natural Language Processing) | The field of AI focused on enabling machines to understand, interpret, and generate human language | Foundation of all LLM applications -- chatbots, search, summarization |
| **RAG** (Retrieval-Augmented Generation) | Supplement a pre-trained LLM with relevant, accurate external data | Better, more accurate responses without retraining |
| **Fine-Tuning** | Bake domain-specific data directly into the model weights | Persistent domain knowledge in every inference |
| **Multi-Modal AI** | AI that processes text, images, audio, and video | Richer interactions beyond text-only |
| **Agentic AI** | AI systems that autonomously plan, reason, and use tools | Can perform complex multi-step tasks independently |
| **Multi-Model Approach** | Using different models for different use cases | Right model for the right task, avoiding vendor lock-in |

### NLP and Its Role in Generative AI

**Natural Language Processing (NLP)** is the broader field that makes LLMs possible. It encompasses all the techniques that allow machines to work with human language:



- **Text classification** -- categorizing text (spam detection, sentiment analysis)
- **Named entity recognition** -- extracting people, places, organizations from text
- **Machine translation** -- converting text between languages
- **Text summarization** -- condensing long documents into key points
- **Question answering** -- finding answers from a body of text
- **Text generation** -- producing new, coherent text (what LLMs excel at)

LangChain specifically facilitates a structured way to integrate language models into NLP use cases and data retrieval tasks. It serves as the bridge between raw NLP capability and production applications.

```
Traditional NLP Pipeline:        LLM-Powered NLP (via LangChain):
Text --> Tokenize --> Model -->   Text --> Prompt --> LLM --> Structured Output
        Feature Eng.   Train              Template    (pre-trained)  (Parser)
        (manual)       (slow)             (fast)      (no training)  (automatic)
```

> **Key shift:** Traditional NLP required building custom models for each task. With LLMs + prompt engineering, a single model can handle all NLP tasks by simply changing the prompt.

---

## 2. Prompt Engineering Fundamentals

### What Is Prompt Engineering?

Prompt engineering is the specialized process of **designing and refining** the questions, commands, or statements you use to interact with AI systems (particularly LLMs). It's not just about asking a question -- it's about **how** to ask it in the best way possible.

### Why Prompt Engineering Matters

Prompt engineering is fundamental in fields ranging from **customer service automation** to **advanced research** and **computational linguistics**.

- **Boosts effectiveness and accuracy** -- directly influences how well LLMs function
- **Ensures relevance** -- generates precise, contextually suited responses
- **Meets user expectations** -- clearer prompts = fewer misunderstandings
- **Eliminates continual fine-tuning** -- the model adapts and learns within its context

### Anatomy of a Well-Structured Prompt

Every prompt can have up to **four key components**:

```
+------------------+----------------------------------------+
| Component        | Purpose                                |
+------------------+----------------------------------------+
| Instructions     | Tell the LLM what to do               |
| Context          | Background info to shape the response  |
| Input Data       | The actual data to process             |
| Output Indicator | Where/how to deliver the response      |
+------------------+----------------------------------------+
```

**Example: Sentiment Classification**

```
Instructions:  "Classify the following customer review into
                neutral, negative, or positive sentiment."
Context:       "This review is feedback for a recently launched product."
Input Data:    "The product arrived late but the quality exceeded
                my expectations."
Output:        "Sentiment: "
```

> *(Suggested diagram: Four-quadrant box showing Instructions, Context, Input Data, Output Indicator feeding into an LLM, with an arrow to the response)*

### Prompt Components Breakdown

| Component | What It Does | Example |
|-----------|-------------|---------|
| **Instructions** | Clear, direct commands telling the AI what to do | "Classify the following..." |
| **Context** | Background information that shapes the response | "This is feedback for a new product" |
| **Input Data** | The actual content the LLM will process | The customer review text |
| **Output Indicator** | Marker showing where the LLM should deliver its answer | "Sentiment:" |

---

## 3. In-Context Learning (ICL)

### What Is In-Context Learning?

In-context learning (ICL) is a specific method of prompt engineering where **demonstrations of the task are provided to the model as part of the prompt** in natural language.

**Critical distinction:** ICL does **not** require additional training. The model "learns" from examples provided at inference time -- no weight updates, no gradient steps.

```
Traditional ML:    Data --> Train Model (update weights) --> Inference
In-Context Learning: Examples in Prompt --> Inference (no weight change)
```

### Advantages and Disadvantages

| Advantages | Disadvantages |
|-----------|---------------|
| No fine-tuning required | Constrained by context window size |
| Drastically reduces resources and time | Complex tasks may need gradient-based training |
| Improves performance on specific tasks | Quality depends on example selection |
| Works immediately at inference time | Token limit restricts number of examples |

### The Spectrum of In-Context Learning

```
Zero-shot --> One-shot --> Few-shot --> Chain-of-Thought --> Self-Consistency
(no examples)  (1 example)  (2-5 examples)  (step-by-step)   (multiple paths)
```

---

## 4. Advanced Prompting Techniques

### 4.1 Zero-Shot Prompting

The model performs a task **without any examples**. It relies entirely on its training knowledge and the instructions in the prompt.

**When to use:** Simple classification, fact-checking, translation where the task is straightforward.

```python
prompt = """Classify the following statement as true or false:
            'The Eiffel Tower is located in Berlin.'
            Answer:
"""
# Response: False. The Eiffel Tower is located in Paris, France.
```

**Example -- Sentiment Classification (Zero-shot):**
```python
prompt = """Classify the sentiment of this movie review as positive or negative:
            'The plot was predictable and the acting felt forced throughout the entire film.'
            Sentiment:
"""
```

### 4.2 One-Shot Prompting

Provides the model with **a single example** before asking it to perform a similar task. The example serves as a **template** for the expected format and style.

**When to use:** When you need the model to follow a specific output format or style.

```python
prompt = """Here is an example of translating a sentence from English to French:
            English: "How is the weather today?"
            French: "Comment est le temps aujourd'hui?"

            Now, translate the following sentence from English to French:
            English: "Where is the nearest supermarket?"
"""
# Response: French: "Ou est le supermarche le plus proche?"
```

### 4.3 Few-Shot Prompting

Extends one-shot by providing **multiple examples (typically 2-5)** before the task. These examples establish a clearer pattern.

**When to use:** When the task requires understanding a specific pattern or when zero-shot produces inconsistent results.

```python
prompt = """Here are few examples of classifying emotions in statements:

            Statement: 'I just won my first marathon!'
            Emotion: Joy

            Statement: 'I can't believe I lost my keys again.'
            Emotion: Frustration

            Statement: 'My best friend is moving to another country.'
            Emotion: Sadness

            Now, classify the emotion in the following statement:
            Statement: 'That movie was so scary I had to cover my eyes.'
"""
# Response: Emotion: Fear
```

### 4.4 Chain-of-Thought (CoT) Prompting

Guides the LLM through **complex reasoning step-by-step**. Highly effective for problems requiring multiple intermediate steps.

**When to use:** Math problems, logical reasoning, multi-step decision making.

```python
prompt = """Consider the problem: 'A store had 22 apples. They sold 15 apples
            today and got a new delivery of 8 apples. How many apples are there now?'

            Break down each step of your calculation
"""
# Response:
# Step 1: Start with 22 apples
# Step 2: Subtract sold apples: 22 - 15 = 7
# Step 3: Add delivery: 7 + 8 = 15
# Answer: 15 apples
```

> **Why CoT works:** By forcing the model to show its work, it's less likely to make arithmetic or logical errors. The intermediate steps act as a self-check mechanism.

### 4.5 Self-Consistency

Generates **multiple independent solutions** to the same problem, then evaluates them to find the most consistent answer. Improves reliability by cross-verifying different reasoning paths.

**When to use:** Critical calculations, ambiguous problems, when you need high confidence in the answer.

```python
prompt = """When I was 6, my sister was half of my age. Now I am 70, what age is my sister?

            Provide three independent calculations and explanations,
            then determine the most consistent result.
"""
# All three paths arrive at: Sister is 67 years old
# (When I was 6, she was 3 --> she's 3 years younger --> 70 - 3 = 67)
```

### Comparison of All Prompting Techniques

| Technique | # Examples | Best For | Complexity |
|-----------|-----------|----------|------------|
| **Zero-shot** | 0 | Simple, well-defined tasks | Low |
| **One-shot** | 1 | Format/style matching | Low-Medium |
| **Few-shot** | 2-5 | Pattern recognition, classification | Medium |
| **Chain-of-Thought** | 0-few | Multi-step reasoning, math | Medium-High |
| **Self-consistency** | 0 (multiple runs) | High-confidence answers | High |

### Tools for Prompt Engineering

Several platforms facilitate developing, experimenting with, evaluating, and deploying prompts:

| Tool | What It Provides |
|------|-----------------|
| **OpenAI's Playground** | Real-time tweaking and testing of prompts with immediate output feedback |
| **LangChain** | Prompt templates, chains, agents -- full framework for prompt-driven applications |
| **Hugging Face Model Hub** | Access to various pre-trained models suitable for different tasks and languages |
| **IBM's AI Classroom / watsonx** | Enterprise-grade model access with governance and compliance features |

**What these tools enable:**
- **Real-time experimentation** -- tweak prompts and see immediate effects on output
- **Pre-trained model access** -- various models for different tasks and languages
- **Collaborative editing** -- share and edit prompts among teams or communities
- **Performance tracking** -- track changes, analyze results, optimize based on metrics

### Prompt Engineering Application Agents

When powered by LLMs and integrated with tools like LangChain, **agents** can perform complex tasks across domains using different prompts:

| Agent Type | What It Does | Example |
|-----------|-------------|---------|
| **Q&A Agent with Sources** | Answers questions citing retrieved documents | Customer support with source links |
| **Content Agent** | Creates and summarizes content | Blog post generation, report summarization |
| **Analytic Agent** | Data analysis and business intelligence | Querying dashboards, trend analysis |
| **Multilingual Agent** | Seamless, context-aware translation and communication | Real-time multilingual customer service |

---

## 5. Introduction to LangChain

### What Is LangChain?

LangChain is an **open-source Python framework** that streamlines the development of LLM applications. It provides developers with components and interfaces to integrate LLMs into AI applications.

```
User Query --> LangChain --> Retrieval + Processing + Generation --> Response
                  |
        Chains together operations from multiple sources
        (hence "Chain" in the name)
```

### Why LangChain?

| Benefit | Description |
|---------|-------------|
| **Modularity** | Piece together different components like building blocks; encourages reuse |
| **Extensibility** | Readily add new features, integrate external systems with minimal code changes |
| **Decomposition** | Breaks complex queries into smaller, manageable steps (mimics human problem-solving) |
| **Vector DB Integration** | Efficient semantic searches and information retrieval at scale |

### LangChain and NLP

LangChain facilitates a structured way to integrate language models into various use cases, including **Natural Language Processing (NLP)** and data retrieval. It:
- Pinpoints relevant information in text (research papers, legal documents)
- Provides methods for responding to complex prompts by retrieving data and generating coherent summaries
- Chains together retrieval, extraction, processing, and generation operations from large amounts of text and multiple sources

### Practical Applications

| Use Case | Example |
|----------|---------|
| **Content Summarization** | Decipher complex legal documents |
| **Data Extraction** | Extract key statistics from reports, turning text into actionable insights |
| **Q&A Systems** | Contextually relevant customer support with conversation chains; provides clarifying responses based on entire conversation |
| **Content Generation** | Drafting emails, brainstorming, technical documentation |

### Multi-Modal Capabilities

While primarily designed for **text-based** applications, LangChain can work with other data types by leveraging external libraries and models:

| Data Type | How LangChain Handles It |
|-----------|-------------------------|
| **Images** | Via image-processing libraries; embeddings capture visual semantics |
| **Audio** | Via speech-to-text models (e.g., Whisper); converts audio to text for processing |
| **Video** | Frame extraction + audio transcription; combined embeddings |

LangChain's integration with **vector databases** enables the use of **embeddings generated from these data types** to capture semantic meaning and perform similarity searches, making it a valuable tool for multi-modal AI tasks.

### LangChain Architecture Overview

```
                    LangChain Framework
    ┌─────────────────────────────────────────────┐
    │                                             │
    │   Models ──> Prompts ──> Chains ──> Agents  │
    │      |          |          |          |      │
    │   LLMs    Templates   Sequential   Tools    │
    │   Chat    Few-shot    LCEL Pipe    Search    │
    │   Models  Selectors   Memory       DB/API   │
    │                                             │
    │   Documents ──> Embeddings ──> Vector Store  │
    │   Loaders      IBM Slate     Chroma/FAISS   │
    │   Splitters    OpenAI        Retrievers      │
    └─────────────────────────────────────────────┘
```

---

## 6. LangChain Core Components

### 6.1 Language Models

Language Models are the **foundation** of LangChain. They take text input and generate text output.

LangChain supports models from: **IBM (Granite, Mixtral via watsonx), OpenAI, Google, Meta (Llama)**

```python
from ibm_watsonx_ai.foundation_models import ModelInference
from ibm_watson_machine_learning.foundation_models.extensions.langchain import WatsonxLLM

# Configure model parameters
parameters = {
    GenParams.MAX_NEW_TOKENS: 256,   # Max output length
    GenParams.TEMPERATURE: 0.2,       # Lower = more deterministic
}

# Create model instance
model = ModelInference(
    model_id='mistralai/mixtral-8x7b-instruct-v01',
    params=parameters,
    credentials={"url": "https://us-south.ml.cloud.ibm.com"},
    project_id="skills-network"
)

# Wrap for LangChain compatibility
mixtral_llm = WatsonxLLM(model=model)
response = mixtral_llm.invoke("Who is man's best friend?")
```

**Alternative setup using `langchain_ibm`:**
```python
from langchain_ibm import WatsonxLLM

granite_llm = WatsonxLLM(
    model_id="ibm/granite-3-2-8b-instruct",
    url="https://us-south.ml.cloud.ibm.com",
    project_id="skills-network",
    params={
        "max_new_tokens": 256,
        "temperature": 0.5,
        "top_p": 0.2
    }
)
```

### Key Model Parameters

| Parameter | What It Controls | Range | Tip |
|-----------|-----------------|-------|-----|
| `max_new_tokens` | Maximum length of generated output | 1-4096+ | Set based on expected response length |
| `min_new_tokens` | Minimum length of output | 0+ | Prevents too-short responses |
| `temperature` | Randomness/creativity | 0.0-2.0 | 0.1 = precise, 0.8 = creative |
| `top_p` | Nucleus sampling probability | 0.0-1.0 | Lower = more focused |
| `top_k` | Number of top tokens considered | 1-100 | Lower = more deterministic |

### 6.2 Chat Models

A chat model is designed for **efficient conversations** -- it understands questions and responds like a human in a dialogue context.

```python
# Convert a language model to a chat model
mixtral_llm = WatsonxLLM(model=model)  # Now it can handle chat messages
```

### 6.3 Chat Message Types

Chat models handle various message types for dynamic conversation:

| Message Type | Purpose | Example |
|-------------|---------|---------|
| `HumanMessage` | User input | "What should I eat?" |
| `AIMessage` | Model-generated response | "Try a salad for a light meal." |
| `SystemMessage` | Instructions for the model's behavior | "You are a fitness activity bot" |
| `FunctionMessage` | Function call outcomes (with name param) | Result of a tool call |
| `ToolMessage` | Tool interaction results | Search results, calculations |

Each message has two properties: **role** (who is speaking) and **content** (what is being said).

```python
from langchain_core.messages import HumanMessage, SystemMessage, AIMessage

# Full conversation with context
messages = [
    SystemMessage(content="You are a fitness activity bot. Answer in one short sentence."),
    HumanMessage(content="I enjoy running."),
    AIMessage(content="Great! Running is excellent for cardiovascular health."),
    HumanMessage(content="What other activities would you recommend?")
]

response = mixtral_llm.invoke(messages)
```

### 6.4 Prompt Templates

Prompt templates translate user questions into **clear, reusable instructions** for the LLM.

#### Types of Prompt Templates

| Template Type | Use Case | Key Feature |
|--------------|----------|-------------|
| `PromptTemplate` | Single-string formatting | Simple variable substitution |
| `ChatPromptTemplate` | Message lists with roles | System/user/AI message structure |
| `MessagesPlaceholder` | Dynamic message insertion | Full control over message rendering |
| `FewShotPromptTemplate` | Example-driven prompts | Provides examples to guide LLM |

#### String Prompt Template
```python
from langchain_core.prompts import PromptTemplate

template = """Tell me a {adjective} joke about {content}."""
prompt = PromptTemplate.from_template(template)

# Format with specific values
formatted = prompt.format(adjective="funny", content="chickens")
# Result: "Tell me a funny joke about chickens."
```

#### Chat Prompt Template
```python
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    ("user", "Tell me a joke about {topic}")
])

formatted = prompt.invoke({"topic": "cats"})
```

#### MessagesPlaceholder
```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_core.messages import HumanMessage

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    MessagesPlaceholder("msgs")
])

formatted = prompt.invoke({"msgs": [HumanMessage(content="What is AI?")]})
```

#### FewShotPromptTemplate

The `FewShotPromptTemplate` provides specific **examples or "shots"** to the LLM. These examples inform the model about the inserted context and guide it to generate the desired output.

A few-shot prompt template consists of:
- **Instructions** for the language model
- **A few-shot examples** to help the model understand context and expected responses
- **A specific question** directed at the language model

#### Example Selectors for Few-Shot Templates

When you have a large library of examples, you need to **select the most relevant ones** for each prompt. Example selectors make this efficient:

| Selector | Selection Strategy | Best For |
|----------|-------------------|----------|
| **Semantic Similarity** | Picks examples most semantically similar to the input | When meaning matters most |
| **Max Marginal Relevance (MMR)** | Balances relevance with diversity (avoids redundant examples) | When you want varied perspectives |
| **N-Gram Overlap** | Selects examples with most textual overlap with the input | When surface-level wording matters |
| **Length-based** | Selects examples that fit within token limits | When context window is constrained |

**Example: N-Gram Overlap Selector**

The N-Gram Overlap selector picks examples whose text has the highest n-gram overlap with the user's input, ensuring the examples are textually similar to the current query:

```python
from langchain.prompts.example_selector.ngram_overlap import NGramOverlapExampleSelector
from langchain_core.prompts import FewShotPromptTemplate, PromptTemplate

# Define example format
example_prompt = PromptTemplate(
    input_variables=["input", "output"],
    template="Input: {input}\nOutput: {output}"
)

# Example library
examples = [
    {"input": "I love programming in Python", "output": "Positive"},
    {"input": "The weather is terrible today", "output": "Negative"},
    {"input": "Python is great for data science", "output": "Positive"},
]

# Selector picks examples with highest n-gram overlap to the query
example_selector = NGramOverlapExampleSelector(
    examples=examples,
    example_prompt=example_prompt,
    threshold=0.0  # Include all examples above this overlap score
)

# Build the few-shot template
few_shot_prompt = FewShotPromptTemplate(
    example_selector=example_selector,
    example_prompt=example_prompt,
    prefix="Classify the sentiment:",
    suffix="Input: {input}\nOutput:",
    input_variables=["input"]
)
```

### 6.5 Output Parsers

Output parsers transform LLM output into **structured formats** for downstream processing.

| Parser | Output Format | Use Case |
|--------|--------------|----------|
| `StrOutputParser` | Clean string | Default text output |
| `JsonOutputParser` | Structured JSON | API responses, data extraction |
| `CommaSeparatedListOutputParser` | Python list | List generation |
| `PandasDataFrameOutputParser` | DataFrame | Data analysis |
| XML/CSV parsers | Various | Domain-specific formatting |

#### JSON Output Parser Example
```python
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class Joke(BaseModel):
    setup: str = Field(description="The question part of the joke")
    punchline: str = Field(description="The answer/funny part")

output_parser = JsonOutputParser(pydantic_object=Joke)
format_instructions = output_parser.get_format_instructions()

prompt = PromptTemplate(
    template="Answer the user query.\n{format_instructions}\n{query}",
    input_variables=["query"],
    partial_variables={"format_instructions": format_instructions}
)

chain = prompt | mixtral_llm | output_parser
result = chain.invoke({"query": "Tell me a joke about cats"})
# Result: {"setup": "Why don't cats play poker?", "punchline": "Too many cheetahs!"}
```

#### Comma-Separated List Parser
```python
from langchain.output_parsers import CommaSeparatedListOutputParser

output_parser = CommaSeparatedListOutputParser()
format_instructions = output_parser.get_format_instructions()

prompt = PromptTemplate(
    template="Answer the user query. {format_instructions}\n{subject}",
    input_variables=["subject"],
    partial_variables={"format_instructions": format_instructions}
)

chain = prompt | mixtral_llm | output_parser
result = chain.invoke({"subject": "ice cream flavors"})
# Result: ['vanilla', 'chocolate', 'strawberry', 'mint', 'cookie dough']
```

---

## 7. LangChain Expression Language (LCEL)

### What Is LCEL?

LCEL (LangChain Expression Language) is the **modern, recommended pattern** for building LangChain applications. It uses the **pipe operator (`|`)** to connect components, ensuring clean, readable data flow.

```
Old way (LLMChain):    LLMChain(llm=model, prompt=template)
New way (LCEL):        template | model | output_parser
```

### Why LCEL Over Traditional Chains?

| Feature | Traditional (LLMChain) | LCEL (Pipe Operator) |
|---------|----------------------|---------------------|
| Composability | Limited | Highly composable |
| Data flow visibility | Opaque | Clear and readable |
| Parallel execution | Manual | Built-in support |
| Streaming | Complex setup | Simplified |
| Async support | Limited | Native |

### LCEL Building Blocks: Runnables

In LangChain, **Runnables** are the interface and building blocks that connect components (LLMs, retrievers, tools) into a pipeline.

| Runnable Type | What It Does | Syntax |
|--------------|-------------|--------|
| `RunnableSequence` | Chains components sequentially (output of A = input of B) | `A | B | C` |
| `RunnableParallel` | Runs multiple components concurrently with same input | `{"key1": chain1, "key2": chain2}` |
| `RunnableLambda` | Wraps a Python function into a runnable component | `RunnableLambda(my_function)` |
| `RunnablePassthrough` | Passes data through, optionally adding computed fields | `RunnablePassthrough.assign(new_key=fn)` |

### Automatic Type Coercion

LCEL converts regular Python constructs into runnable components automatically:

| Python Construct | Becomes | Example |
|-----------------|---------|---------|
| Dictionary `{}` | `RunnableParallel` | Runs tasks simultaneously |
| Function | `RunnableLambda` | Transforms inputs |
| String template | Processed via PromptTemplate | Formatted with variables |

### Basic LCEL Pattern

```python
from langchain_core.prompts import PromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnableLambda

# Step 1: Define template
template = """Tell me a {adjective} joke about {content}."""
prompt = PromptTemplate.from_template(template)

# Step 2: Create format helper
def format_prompt(variables):
    return prompt.format(**variables)

# Step 3: Build chain with pipe operator
joke_chain = (
    RunnableLambda(format_prompt)   # Format input
    | llm                            # Process with LLM
    | StrOutputParser()              # Parse output to string
)

# Step 4: Invoke
response = joke_chain.invoke({"adjective": "funny", "content": "chickens"})
```

### Parallel Execution with LCEL

```python
# Dictionary creates RunnableParallel -- all three tasks run simultaneously
parallel_chain = {
    "summary": summary_prompt | llm | StrOutputParser(),
    "translation": translate_prompt | llm | StrOutputParser(),
    "sentiment": sentiment_prompt | llm | StrOutputParser()
}

# Each task receives the same input but processes it differently
result = parallel_chain.invoke({"text": "The product launch was a huge success!"})
# result["summary"] -> concise summary
# result["translation"] -> French translation
# result["sentiment"] -> positive/negative classification
```

### LCEL Data Flow Visualization

```
Input Dict ──> RunnableLambda(format_prompt) ──> LLM ──> StrOutputParser() ──> String Output
     |                    |                        |              |
  {"adjective":     Formats template         Generates      Extracts clean
   "funny",         with variables           response       string
   "content":
   "chickens"}
```

> **When to use LCEL vs LangGraph:** LCEL is best for **simpler orchestration tasks**. For more complex workflows with conditional logic, loops, or state management, consider **LangGraph** (while still using LCEL within individual nodes).

---

## 8. LangChain Chains

### What Are Chains?

Chains are **sequences of calls** where the output from one step becomes the input for the next. They represent the core orchestration pattern in LangChain.

### 8.1 Simple Chain (Traditional Approach)

```python
from langchain.chains import LLMChain

template = """Your job is to come up with a classic dish from the area that
              the user suggests. {location}
              YOUR RESPONSE:
"""
prompt_template = PromptTemplate(template=template, input_variables=['location'])

location_chain = LLMChain(
    llm=mixtral_llm,
    prompt=prompt_template,
    output_key='meal'
)

result = location_chain.invoke(input={'location': 'China'})
# result['meal'] -> "Peking Duck"
```

### 8.2 Simple Chain (LCEL Approach)

```python
chain = (
    PromptTemplate.from_template(template)
    | mixtral_llm
    | StrOutputParser()
)
result = chain.invoke({"location": "China"})
```

### 8.3 Sequential Chain

A **SequentialChain** combines multiple chains where each step feeds into the next:

```
Location --> Chain 1 --> Meal --> Chain 2 --> Recipe --> Chain 3 --> Cooking Time
"China"      "Famous      "Peking   "Give me     "Peking    "Estimate    "~2 hours"
              dish in       Duck"    the recipe    Duck       cooking
              China?"                for..."      recipe..." time..."
```

#### Traditional Sequential Chain

```python
from langchain.chains import SequentialChain

# Chain 1: Location -> Meal
location_chain = LLMChain(llm=mixtral_llm, prompt=location_prompt, output_key='meal')

# Chain 2: Meal -> Recipe
dish_chain = LLMChain(llm=mixtral_llm, prompt=dish_prompt, output_key='recipe')

# Chain 3: Recipe -> Time
recipe_chain = LLMChain(llm=mixtral_llm, prompt=time_prompt, output_key='time')

# Combine all chains
overall_chain = SequentialChain(
    chains=[location_chain, dish_chain, recipe_chain],
    input_variables=['location'],
    output_variables=['meal', 'recipe', 'time'],
    verbose=True  # Shows step-by-step execution
)

result = overall_chain.invoke({'location': 'China'})
```

#### LCEL Sequential Chain (Modern Approach)

```python
from langchain_core.runnables import RunnablePassthrough

# Individual LCEL chains
location_chain_lcel = PromptTemplate.from_template(location_template) | mixtral_llm | StrOutputParser()
dish_chain_lcel = PromptTemplate.from_template(dish_template) | mixtral_llm | StrOutputParser()
time_chain_lcel = PromptTemplate.from_template(time_template) | mixtral_llm | StrOutputParser()

# Thread data through each step using RunnablePassthrough.assign()
overall_chain_lcel = (
    RunnablePassthrough.assign(meal=lambda x: location_chain_lcel.invoke(x))
    | RunnablePassthrough.assign(recipe=lambda x: dish_chain_lcel.invoke(x))
    | RunnablePassthrough.assign(time=lambda x: time_chain_lcel.invoke(x))
)

result = overall_chain_lcel.invoke({"location": "China"})
```

---

## 9. LangChain Memory

### Why Memory Matters

Without memory, each LLM call is **stateless** -- the model has no knowledge of previous interactions. Memory enables **continuity and context preservation** across a conversation.

```
Without Memory:           With Memory:
User: "I'm Bob"          User: "I'm Bob"
AI: "Hi Bob!"            AI: "Hi Bob!"
User: "Who am I?"        User: "Who am I?"
AI: "I don't know"       AI: "You're Bob!"
```

### 9.1 ChatMessageHistory

A lightweight wrapper for managing conversation history manually.

```python
from langchain.memory import ChatMessageHistory

history = ChatMessageHistory()

# Add messages
history.add_ai_message("Hi! How can I help you?")
history.add_user_message("What is the capital of France?")

# Access stored messages
print(history.messages)
# [AIMessage(content='Hi!...'), HumanMessage(content='What is...')]

# Pass history to LLM for contextual response
ai_response = mixtral_llm.invoke(history.messages)
```

### 9.2 ConversationBufferMemory

Stores the **entire conversation history** and automatically provides context to each new interaction.

```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain

conversation = ConversationChain(
    llm=mixtral_llm,
    verbose=True,
    memory=ConversationBufferMemory()
)

# The model remembers everything
conversation.invoke(input="Hello! I'm a little cat.")
conversation.invoke(input="What are your capabilities?")
conversation.invoke(input="Who am I?")
# Response: "You mentioned you're a little cat!"
```

### 9.3 ConversationSummaryMemory

Instead of storing every message, it stores a **running summary** -- useful for long conversations that would exceed the context window.

| Memory Type | Storage | Best For | Trade-off |
|------------|---------|----------|-----------|
| `ChatMessageHistory` | Manual, all messages | Short conversations | Full control, manual management |
| `ConversationBufferMemory` | Auto, all messages | Medium conversations | Simple, but grows linearly |
| `ConversationSummaryMemory` | Auto, compressed summary | Long conversations | Saves tokens, loses some detail |

### How Memory Works in a Chain

```
       ┌──────────────┐
       │   Memory     │
       │ (read/write) │
       └──┬───────┬───┘
          │       │
   ┌──────▼──┐ ┌──▼──────┐
   │  Read   │ │  Write  │
   │ history │ │ current │
   │ before  │ │ after   │
   │ execute │ │ execute │
   └────┬────┘ └────┬────┘
        │           │
   ┌────▼───────────▼────┐
   │    Chain Execution   │
   │  (user input +       │
   │   memory context)    │
   └──────────────────────┘
```

---

## 10. Documents, Embeddings, and Vector Stores

### 10.1 Documents

The `Document` class is LangChain's representation of a unit of text with metadata.

```python
from langchain_core.documents import Document

doc = Document(
    page_content="""Python is an interpreted, high-level programming language.
                    Python's design philosophy emphasizes code readability.""",
    metadata={
        'my_document_id': 234234,
        'my_document_source': "About Python",
        'my_document_create_time': 1680013019
    }
)
```

### 10.2 Document Loaders

| Loader | Source | Usage |
|--------|--------|-------|
| `PyPDFLoader` | PDF files | `loader = PyPDFLoader("document.pdf")` |
| `WebBaseLoader` | Websites | `loader = WebBaseLoader("https://...")` |
| `TextLoader` | Plain text files | `loader = TextLoader("file.txt")` |

```python
from langchain_community.document_loaders import PyPDFLoader, WebBaseLoader

# Load a PDF
loader = PyPDFLoader("path/to/document.pdf")
documents = loader.load()

# Load a webpage
loader = WebBaseLoader("https://python.langchain.com/docs/introduction/")
web_data = loader.load()
```

### 10.3 Text Splitters

Long documents must be split into smaller **chunks** for effective embedding and retrieval.

| Splitter | Strategy | Best For |
|----------|----------|----------|
| `CharacterTextSplitter` | Splits on a single character separator | Simple documents |
| `RecursiveCharacterTextSplitter` | Tries multiple separators hierarchically | Complex documents (recommended) |

```python
from langchain.text_splitter import CharacterTextSplitter, RecursiveCharacterTextSplitter

# Simple splitting
text_splitter = CharacterTextSplitter(
    chunk_size=200,      # Max characters per chunk
    chunk_overlap=20,    # Overlap to preserve context
    separator="\n"       # Split on newlines
)
chunks = text_splitter.split_documents(documents)

# Recursive splitting (preferred)
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=500,
    chunk_overlap=50,
    separators=["\n\n", "\n", ". ", " ", ""]  # Try each in order
)
chunks = text_splitter.split_documents(documents)
```

### 10.4 Embeddings

Embeddings convert text into **numerical vectors** that capture semantic meaning. Similar texts produce similar vectors.

```python
from langchain_ibm import WatsonxEmbeddings
from ibm_watsonx_ai.metanames import EmbedTextParamsMetaNames

embed_params = {
    EmbedTextParamsMetaNames.TRUNCATE_INPUT_TOKENS: 3,
    EmbedTextParamsMetaNames.RETURN_OPTIONS: {"input_text": True}
}

watsonx_embedding = WatsonxEmbeddings(
    model_id="ibm/slate-125m-english-rtrvr-v2",
    url="https://us-south.ml.cloud.ibm.com",
    project_id="skills-network",
    params=embed_params,
)
```

### 10.5 Vector Stores (Chroma)

Vector stores hold embeddings and enable **similarity search** -- finding documents semantically close to a query.

```python
from langchain.vectorstores import Chroma

# Create vector store from document chunks
docsearch = Chroma.from_documents(chunks, watsonx_embedding)

# Similarity search
query = "What is LangChain?"
results = docsearch.similarity_search(query)
print(results[0].page_content)
```

### The Full Document Pipeline

```
PDF/Web/Text  -->  Document Loader  -->  Text Splitter  -->  Embeddings  -->  Vector Store
                        |                     |                  |                 |
                   Load raw text      Split into chunks    Convert to        Store & search
                                     (200-500 chars)      vectors           by similarity
```

---

## 11. Retrievers and Retrieval QA

### 11.1 What Are Retrievers?

Retrievers are interfaces that return documents given an unstructured query. They accept a string query and return a list of `Document` objects.

```python
# Convert vector store to retriever
retriever = docsearch.as_retriever()

# Retrieve relevant documents
docs = retriever.invoke("What is LangChain?")
```

### 11.2 Parent Document Retriever

A specialized retriever that **splits documents into small chunks for accurate embedding** but **returns the larger parent documents** during retrieval. This balances precision with context.

```python
from langchain.retrievers import ParentDocumentRetriever
from langchain.storage import InMemoryStore

# Parent = large chunks (2000 chars), Child = small chunks (400 chars)
parent_splitter = CharacterTextSplitter(chunk_size=2000)
child_splitter = CharacterTextSplitter(chunk_size=400)

vectorstore = Chroma(
    collection_name="split_parents",
    embedding_function=watsonx_embedding
)
store = InMemoryStore()

retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=store,
    child_splitter=child_splitter,
    parent_splitter=parent_splitter,
)

retriever.add_documents(documents)
retrieved_docs = retriever.invoke("LangChain applications")
```

```
Query --> Embed --> Search small chunks (400 chars) --> Return parent chunk (2000 chars)
                        |                                        |
                   Precise matching                    Full context preserved
```

### 11.3 RetrievalQA Chain

Combines a retriever with an LLM to **answer questions based on retrieved documents**. This is the foundation of RAG.

```python
from langchain.chains import RetrievalQA

qa = RetrievalQA.from_chain_type(
    llm=mixtral_llm,
    chain_type="stuff",           # Stuffs all docs into one prompt
    retriever=docsearch.as_retriever(),
    return_source_documents=False
)

answer = qa.invoke("What is this paper discussing?")
```

> **This is RAG in action:** The LLM doesn't rely solely on its training data -- it retrieves relevant documents first, then generates an answer grounded in that retrieved context.

---

## 12. LangChain Agents and Tools

### 12.1 What Are Agents?

Agents are **dynamic systems** where a language model **determines and sequences actions** at runtime. Unlike chains (which follow a fixed sequence), agents decide which tools to use and in what order based on the user's query.

```
Chain:    Input --> Step 1 --> Step 2 --> Step 3 --> Output  (fixed path)
Agent:    Input --> LLM decides --> Tool A? Tool B? --> LLM reasons --> Output  (dynamic path)
```

### 12.2 Tools

Tools are interfaces that agents use to **interact with the world** -- perform calculations, search databases, call APIs, etc.

```python
from langchain_core.tools import Tool
from langchain_experimental.utilities import PythonREPL

# Built-in tool: Python REPL
python_repl = PythonREPL()
python_calculator = Tool(
    name="Python Calculator",
    func=python_repl.run,
    description="Useful for when you need to do math calculations"
)

result = python_calculator.invoke("a = 3; b = 1; print(a+b)")
# Output: 4
```

#### Custom Tools with @tool Decorator

```python
from langchain.tools import tool

@tool
def search_weather(location: str):
    """Search for the current weather in the given location."""
    # In production, this would call a real weather API
    return f"The weather in {location} is currently 72F and sunny."
```

### 12.3 Toolkits

A toolkit is simply a **collection of tools** that an agent can access:

```python
tools = [python_calculator, search_weather]
```

### 12.4 ReAct Agents

The **ReAct (Reasoning + Acting)** framework is the most common agent pattern. The agent reasons about what to do, takes an action, observes the result, and repeats until it has a final answer.

```
Thought: I need to calculate the square root of 256
Action: Python Calculator
Action Input: import math; print(math.sqrt(256))
Observation: 16.0
Thought: I now know the answer
Final Answer: The square root of 256 is 16.0
```

```python
from langchain.agents import create_react_agent, AgentExecutor

# Define the ReAct prompt template
prompt = ChatPromptTemplate.from_template("""
Answer the following questions as best you can. You have access to:
{tools}
Tool names: {tool_names}

Use this format:
Question: the input question
Thought: reasoning about what to do
Action: the tool to use
Action Input: the input to the tool
Observation: the result
... (repeat as needed)
Thought: I now know the final answer
Final Answer: the answer

Question: {input}
{agent_scratchpad}
""")

# Create the agent
agent = create_react_agent(
    llm=mixtral_llm,
    tools=tools,
    prompt=prompt
)

# Create the executor (handles tool calls)
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    handle_parsing_errors=True
)

# Run
result = agent_executor.invoke({"input": "What is the square root of 256?"})
```

### 12.5 Pandas DataFrame Agent

LangChain provides a specialized agent for querying DataFrames with natural language:

```python
from langchain.agents import create_pandas_dataframe_agent

agent = create_pandas_dataframe_agent(
    llm=chat_model,
    df=dataframe,
    verbose=True
)

result = agent.invoke("How many rows are in the data frame?")
# The LLM generates Python code internally to answer: "139 rows"
```

### Agents vs. Chains -- When to Use Which?

| Feature | Chains | Agents |
|---------|--------|--------|
| Execution path | Fixed, predetermined | Dynamic, decided at runtime |
| Tool usage | Optional | Core feature |
| Reasoning | Follows template | LLM reasons about next steps |
| Best for | Predictable pipelines | Open-ended tasks |
| Example | Summarize -> Translate -> Format | "Find the weather and suggest activities" |

---

## 13. Multi-Model Approach for Enterprise AI

### Why Multiple Models?

No single model is best for every task. A **multi-model approach** means using different models for different use cases -- like a Swiss Army knife.

### Key Questions When Evaluating Models

1. **Who built it?** -- Provider reputation and support
2. **What data was it trained on?** -- Relevance to your domain
3. **What guardrails are in place?** -- Safety and compliance
4. **What risks and regulations apply?** -- Legal requirements

### Model Selection Process

```
1. Write a specific prompt (captures use case, user problem, guardrails)
         |
2. Research available models (size, performance, cost, risk, deployment)
         |
3. Evaluate models against your prompt
         |
4. Start with a large model, optimize until prompt is satisfied
         |
5. Try to duplicate results with smaller models (cost optimization)
         |
6. Choose the best model --> Deploy --> Continuously evaluate and govern
```

### Key Evaluation Factors

| Factor | Considerations |
|--------|---------------|
| **Performance** | Accuracy, reliability, speed |
| **Size** | Larger != always better; SLMs often have lower latency |
| **Deployment** | Self-hosted (cheaper) vs. cloud-based (easier) |
| **Transparency** | How the model makes decisions |
| **Risk** | Hallucinations, bias, data privacy |
| **Cost** | Inference cost at scale |

### Important Rules of Thumb

- **Self-hosting** an LLM is generally **cheaper** than cloud-based services
- **Small Language Models (SLMs)** often perform **better with lower latency** for specialized tasks
- **Hybrid approach**: Combine on-prem and cloud infrastructure for flexibility
- **Continuous testing, governance, and optimization** are essential after deployment

---

## 14. Building GenAI Applications -- From Ideation to Production

### The Three Phases

### Phase 1: Ideation & Experimentation

- Research models from repositories like **Hugging Face**
- Evaluate model benchmarks (size, performance, specialization)
- Experiment with prompting techniques (zero-shot, few-shot, CoT)
- Understand capabilities and limitations with your data early

### Phase 2: Building

- Run models **locally** for data privacy and security
- Choose your data integration approach:
  - **RAG**: Supplement pre-trained model with external data at query time
  - **Fine-tuning**: Bake domain data directly into the model
- Use frameworks like **LangChain** to simplify development
- Break down problems into smaller steps (chain sequences)

### Phase 3: Operations (MLOps)

- Use **containers and Kubernetes** for deployment and scaling
- Use production runtimes like **vLLM** for model serving
- **Hybrid infrastructure**: On-prem + cloud for cost optimization
- Continuously **benchmark, monitor, and handle exceptions**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   IDEATION      │    │   BUILDING      │    │   OPERATIONS    │
│                 │    │                 │    │                 │
│ - Model search  │───>│ - Local serving │───>│ - Containers    │
│ - Benchmarking  │    │ - RAG / Fine-   │    │ - Kubernetes    │
│ - Prompting     │    │   tuning        │    │ - vLLM serving  │
│ - Data testing  │    │ - LangChain     │    │ - Monitoring    │
│                 │    │ - Chain design  │    │ - Governance    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

---

## 15. Flask for GenAI Web Applications

### What Is Flask?

Flask is a **micro web framework** written in Python. It provides minimal built-in dependencies but is highly extensible -- perfect for wrapping LLM applications as web services.

### Key Features

| Feature | Description |
|---------|-------------|
| **Development Server** | Built-in lightweight server for testing |
| **Debugger** | Interactive traceback and stack trace in browser |
| **Logging** | Standard Python logging |
| **Testing** | Built-in test support (Pytest, Coverage) |
| **Routing** | Dynamic URLs, RESTful services, HTTP methods |
| **Templates** | Jinja2 for dynamic HTML pages |
| **Static Files** | CSS, JavaScript, images support |
| **Sessions** | User session management |

### Flask Dependencies

| Dependency | Purpose |
|-----------|---------|
| **Werkzeug** | WSGI (Web Server Gateway Interface) implementation |
| **Jinja** | Template rendering engine |
| **MarkupSafe** | Escapes untrusted input to prevent injection |
| **ItsDangerous** | Secures session cookies, detects tampering |
| **Click** | CLI framework for Flask commands |

### Minimal Flask Application

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)
```

### Popular Flask Extensions for AI Apps

| Extension | Purpose |
|-----------|---------|
| `Flask-SQLAlchemy` | ORM for database access |
| `Flask-CORS` | Cross-Origin Resource Sharing |
| `Flask-Migrate` | Database migrations |
| `Flask-User` | Authentication and authorization |
| `Marshmallow` | Object serialization/deserialization |
| `Celery` | Background task queues |

### Flask vs. Django

| Aspect | Flask | Django |
|--------|-------|--------|
| **Type** | Micro framework | Full-stack framework |
| **Dependencies** | Minimal (add what you need) | Everything included |
| **Flexibility** | Plug-and-play, highly flexible | Opinionated, makes decisions for you |
| **Best for** | APIs, microservices, AI wrappers | Full web applications |
| **Learning curve** | Low | Higher |

### Flask for Large-Scale AI Applications

Flask can scale with proper architecture:
- **Extensibility**: Combine with any Python AI library
- **Custom classes**: Subclass Flask for custom behavior
- **Scaling**: Double servers = ~double performance
- **Modular development**: Break into utilities and extensions
- **Used by**: Netflix, Reddit, Lyft, LinkedIn, Pinterest, Uber

---

## 16. Building a GenAI Flask App with LangChain

### Project Setup

```bash
# Create project directory
mkdir genai_flask_app
cd genai_flask_app

# Create virtual environment
python3.11 -m venv venv
source venv/bin/activate

# Install dependencies
pip install ibm-watsonx-ai flask langchain langchain-core
```

### Model Setup with IBM watsonx

```python
from ibm_watsonx_ai import Credentials
from ibm_watsonx_ai.metanames import GenTextParamsMetaNames
from ibm_watsonx_ai.foundation_models import ModelInference

# Authenticate
credentials = Credentials(
    url="https://us-south.ml.cloud.ibm.com",
    api_key="<YOUR_API_KEY>"
)

# Configure parameters
params = {
    GenTextParamsMetaNames.DECODING_METHOD: "greedy",
    GenTextParamsMetaNames.MAX_NEW_TOKENS: 100
}

# Initialize model
model = ModelInference(
    model_id="ibm/granite-3-3-8b-instruct",
    params=params,
    credentials=credentials,
    project_id="skills-network"
)

# Generate text
text = "Only reply with the answer. What is the capital of Canada?"
print(model.generate(text)['results'][0]['generated_text'])
```

### Model-Specific Prompt Templates (Tokenization)

Different models require different prompt formatting:

```python
from langchain.prompts import PromptTemplate

# Llama 3 template with special tokens
llama3_template = PromptTemplate(
    template='''<|begin_of_text|><|start_header_id|>system<|end_header_id|>
{system_prompt}<|eot_id|><|start_header_id|>user<|end_header_id|>
{user_prompt}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
''',
    input_variables=["system_prompt", "user_prompt"]
)
```

### LangChain Chaining with Flask

```python
def get_ai_response(model, template, system_prompt, user_prompt):
    chain = template | model
    return chain.invoke({
        'system_prompt': system_prompt,
        'user_prompt': user_prompt
    })
```

### Enhanced Output with JSON Parser

```python
from langchain_core.output_parsers import JsonOutputParser
from pydantic import BaseModel, Field

class AIResponse(BaseModel):
    summary: str = Field(description="Summary of the user's message")
    sentiment: int = Field(description="Sentiment score from 0 to 100")
    response: str = Field(description="Generated AI response")

json_parser = JsonOutputParser(pydantic_object=AIResponse)

def get_ai_response(model, template, system_prompt, user_prompt):
    chain = template | model | json_parser
    return chain.invoke({
        'system_prompt': system_prompt,
        'user_prompt': user_prompt,
        'format_prompt': json_parser.get_format_instructions()
    })
```

### Flask API Endpoint

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/generate', methods=['POST'])
def generate():
    data = request.json
    model_name = data.get('model')
    user_message = data.get('message')

    if not user_message or not model_name:
        return jsonify({"error": "Missing message or model selection"}), 400

    system_prompt = "You are an AI assistant helping with customer inquiries."

    try:
        response = get_model_response(model_name, system_prompt, user_message)
        return jsonify(response)
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

### Complete App Architecture

```
Client (Browser/API)
       |
       | POST /generate {"model": "granite", "message": "..."}
       v
  Flask Server (app.py)
       |
       | 1. Parse request
       | 2. Select model + template
       | 3. Build LangChain chain (template | model | parser)
       | 4. Invoke chain
       v
  IBM watsonx.ai API
       |
       | Generated response
       v
  JSON Output Parser
       |
       | Structured JSON
       v
  Flask Response --> Client
```

---

## Key Takeaways

- **Prompt engineering** is the gateway to effective AI -- the quality of your prompt directly determines the quality of the output
- **In-context learning** lets models adapt to tasks without retraining, using zero-shot, one-shot, and few-shot examples
- **Chain-of-Thought** and **Self-Consistency** are advanced techniques for complex reasoning tasks
- **LangChain** provides the framework to build production-grade LLM applications through modular components
- **LCEL (pipe operator)** is the modern, recommended way to compose LangChain workflows
- **Chains** handle predictable pipelines; **Agents** handle dynamic, tool-assisted reasoning
- **RAG** (Retrieval-Augmented Generation) combines retrievers + LLMs for grounded, accurate answers
- **Memory** enables conversational context across multiple interactions
- **Flask** provides a lightweight way to deploy GenAI applications as web services
- **Multi-model strategies** and continuous evaluation are essential for production AI systems

---

## Quick Reference: Essential Code Patterns

### Pattern 1: Basic LLM Call
```python
from langchain_ibm import WatsonxLLM
llm = WatsonxLLM(model_id="ibm/granite-3-2-8b-instruct", ...)
response = llm.invoke("Your prompt here")
```

### Pattern 2: LCEL Chain
```python
chain = PromptTemplate.from_template("...{var}...") | llm | StrOutputParser()
result = chain.invoke({"var": "value"})
```

### Pattern 3: RAG Pipeline
```python
docs = PyPDFLoader("doc.pdf").load()
chunks = RecursiveCharacterTextSplitter(chunk_size=500).split_documents(docs)
vectorstore = Chroma.from_documents(chunks, embeddings)
qa = RetrievalQA.from_chain_type(llm=llm, retriever=vectorstore.as_retriever())
answer = qa.invoke("Your question?")
```

### Pattern 4: Agent with Tools
```python
agent = create_react_agent(llm=llm, tools=[tool1, tool2], prompt=react_prompt)
executor = AgentExecutor(agent=agent, tools=[tool1, tool2], verbose=True)
result = executor.invoke({"input": "Your task here"})
```

### Pattern 5: Flask + LangChain API
```python
@app.route('/generate', methods=['POST'])
def generate():
    chain = template | model | json_parser
    return jsonify(chain.invoke(request.json))
```

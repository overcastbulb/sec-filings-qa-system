<div align="center">

# 📊 SEC Filings QA System

**Ask complex financial research questions about any public company — answered from real SEC filings with full source attribution.**

[![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat-square&logo=python&logoColor=white)](https://www.python.org/)
[![ChromaDB](https://img.shields.io/badge/ChromaDB-Vector_Store-FF6B35?style=flat-square)](https://www.trychroma.com/)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT_API-412991?style=flat-square&logo=openai&logoColor=white)](https://openai.com/)
[![SEC EDGAR](https://img.shields.io/badge/SEC-EDGAR_API-003087?style=flat-square)](https://www.sec.gov/developer)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](./LICENSE)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg?style=flat-square)](https://github.com/psf/black)

[⚡ Quick Start](#-quick-start) · [🏗️ Architecture](#️-architecture) · [📋 Query Types](#-supported-query-types) · [📁 Docs](./docs/)

</div>

---

## 🌟 Overview

A modular RAG pipeline purpose-built for SEC financial filings. It fetches 10-K and 10-Q documents directly from **SEC EDGAR**, processes and indexes them with a **hybrid semantic + keyword retrieval engine**, and answers natural language questions with every claim traced back to its exact filing and section.

Built for financial analysts, quant researchers, and developers who need reliable, cited answers from public company filings — not hallucinations.

---

## ✨ Key Features

- 🔍 **Hybrid retrieval** — 70% semantic + 30% keyword search with financial domain boosting
- 🧠 **Query intent classification** — automatically routes risk, financial, strategic, and governance queries to weighted sections
- 📅 **Temporal intelligence** — recent filings boosted for time-sensitive questions
- 🏢 **Multi-company support** — comparative queries across multiple tickers in one call
- 📌 **Full source attribution** — every answer cites the exact filing, section, and page
- ⚡ **Intelligent caching** — SEC EDGAR downloads cached by content hash to avoid re-fetching
- 🧪 **Benchmark suite** — built-in evaluation framework with query category scoring

---

## 🏗️ Architecture

```
Question
    │
    ▼
QueryProcessor          ← Intent classification, ticker/date/type extraction
    │
    ▼
HybridRetriever         ← Semantic (ChromaDB) + Keyword (BM25) + Domain Boosts
    │                      └── Financial term boost (2x–3x)
    │                      └── Section-aware scoring
    │                      └── Recency boost for temporal queries
    ▼
AnswerGenerator         ← OpenAI GPT with retrieved context + citation formatting
    │
    ▼
Answer + Sources        ← Cited response with filing, section, and date metadata
```

### Core Modules

| Module | Responsibility |
|---|---|
| `SECDataFetcher` | Fetches 10-K / 10-Q filings from SEC EDGAR with caching |
| `DocumentProcessor` | Chunks filings while preserving section structure |
| `QueryProcessor` | Parses intent, tickers, dates, and filing types from queries |
| `VectorStore` | Manages semantic embeddings via ChromaDB |
| `HybridRetriever` | Combines semantic + keyword search with financial domain scoring |
| `AnswerGenerator` | Synthesizes cited answers via OpenAI GPT |

---

## 📋 Supported Query Types

| Type | Example |
|---|---|
| **Single company** | `"What are Apple's main risk factors?"` |
| **Comparative** | `"Compare Apple and Microsoft's R&D spending"` |
| **Temporal / trend** | `"How has Amazon's revenue guidance changed over time?"` |
| **Multi-dimensional** | `"Apple's 2022 10-K cybersecurity risks"` |
| **Strategic** | `"How are major tech companies approaching AI investment?"` |

---

## ⚡ Quick Start

### Prerequisites

- Python 3.8+
- OpenAI API key

### 1. Clone and install

```bash
git clone https://github.com/overcastbulb/sec-filings-qa-system.git
cd sec-filings-qa-system

python -m venv .venv
source .venv/bin/activate   # macOS/Linux
# .\.venv\Scripts\activate  # Windows

pip install -r requirements.txt
```

### 2. Set environment variables

```bash
export OPENAI_API_KEY="your-openai-api-key"

# Optional
export SEC_DATA_DIR="./data"
export SEC_CACHE_DIR="./data/cache"
```

### 3. Run the interactive demo

```bash
python sec_qa_system.py
```

### 4. Use as a library

```python
from sec_qa_system import SECQASystem

qa = SECQASystem()

# Ingest filings for one or more tickers
qa.ingest_company_data(['AAPL', 'MSFT', 'GOOGL'])

# Ask a question
result = qa.query("What are Apple's primary revenue drivers?")
print(result['answer'])
print(f"Sources: {len(result['sources'])} documents")
```

---

## 📊 Sample Output

```
Query: "What are the primary revenue drivers for major technology companies?"

Answer:
Technology companies demonstrate diverse revenue streams including subscription
services, cloud computing, advertising, and hardware sales...

Sources: 8 documents across 4 companies
Coverage: 3 companies, 2 filing types (10-K, 10-Q)
Response time: 3.2 seconds
```

---

## 🔧 Advanced Configuration

### Tune financial term boosts

```python
# Increase relevance weight for specific terms
retriever.financial_terms.update({
    'artificial intelligence': 2.5,
    'cloud revenue': 2.7,
    'operating margin': 2.3
})
```

### Tune section weights per query type

```python
retriever.section_weights['risk']['Cybersecurity'] = 2.8
retriever.section_weights['financial']['Revenue Recognition'] = 3.2
```

### Run the benchmark suite

```python
from sec_qa_system import SECQASystem, SystemBenchmark

qa = SECQASystem()
benchmark = SystemBenchmark(qa)
results = benchmark.run_evaluation_suite()
print(results['evaluation_summary'])
```

---

## 📁 Project Structure

```
sec-filings-qa-system/
├── sec_qa_system.py         # Main pipeline — ingest, query, answer
├── hybrid_retrieval.py      # Hybrid retrieval engine
├── requirements.txt
├── docs/
│   ├── architecture_overview.md
│   ├── technical_documentation.md
│   ├── setup_guide.md
│   └── evaluation_results.md
├── examples/
│   ├── sample_queries.py
│   ├── benchmark_tests.py
│   └── demo_results.md
├── tests/
│   ├── test_query_processor.py
│   └── test_retrieval.py
└── data/                    # Filing cache (gitignored)
```

---

## 📈 Performance

| Metric | Value |
|---|---|
| Avg. response time | 2–5 seconds |
| Documents retrieved per query | 5–15 |
| Chunk capacity | 100K+ efficiently |
| Memory per 1,000 chunks | ~100MB |

---

## 🗺️ Roadmap

- [ ] Streamlit UI for non-developer users
- [ ] Support for 8-K filings (earnings releases, material events)
- [ ] Local LLM mode (Ollama / LLaMA) — no OpenAI dependency
- [ ] Docker setup for one-command deployment
- [ ] Async batch ingestion for large ticker lists

---

## 🤝 Contributing

```bash
git checkout -b feature/your-feature
git commit -m "feat: describe your change"
git push origin feature/your-feature
# Open a Pull Request
```

---

## 📄 License

This project is licensed under the [MIT License](./LICENSE).

---

<div align="center">
  Built for financial researchers who need cited, reliable answers from SEC filings.
  <br/><br/>
  <a href="https://github.com/overcastbulb/sec-filings-qa-system/issues">Report a Bug</a> · <a href="./docs/technical_documentation.md">Technical Docs</a> · <a href="./docs/architecture_overview.md">Architecture</a>
</div>

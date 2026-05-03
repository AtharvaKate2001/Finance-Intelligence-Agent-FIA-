![App Screenshot](FIA.png)

<div align="center">

# 🏦 Finance Intelligence Agent (FIA)

### A production-grade, multi-agent RAG system for financial query resolution — running entirely on local LLMs with zero external API dependency.

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-009688?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.28+-FF4B4B?style=flat&logo=streamlit&logoColor=white)](https://streamlit.io)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat&logo=docker&logoColor=white)](https://docker.com)
[![Ollama](https://img.shields.io/badge/LLM-Ollama%20%2F%20LLaMA3-black?style=flat)](https://ollama.com)
[![FAISS](https://img.shields.io/badge/VectorDB-FAISS-blue?style=flat)](https://faiss.ai)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat)](LICENSE)

[Overview](#-overview) • [Architecture](#-architecture) • [Tech Stack](#-tech-stack) • [Quick Start](#-quick-start) • [Demo](#-demo) • [Results](#-results) • [Roadmap](#-roadmap)

</div>

---

## 🎯 Overview

**Finance Intelligence Agent (FIA)** is a fully local, privacy-first AI system that answers complex financial queries using a coordinated multi-agent pipeline and Retrieval-Augmented Generation (RAG).

Unlike generic chatbots, FIA routes each query through specialised agents — intent classification, knowledge retrieval, and structured response generation — resulting in accurate, context-grounded answers from a curated financial knowledge base.

> **No OpenAI. No external APIs. No data sent to the cloud. Everything runs on your machine.**

### Why this matters
Most financial AI tools either hallucinate facts or require expensive API subscriptions. FIA solves both problems by grounding every response in a verified vector knowledge base and running inference locally via Ollama — making it viable for enterprise deployments where data privacy is non-negotiable.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    User (Streamlit UI)                   │
│                   localhost:8501                         │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTP
┌──────────────────────▼──────────────────────────────────┐
│                  FastAPI Backend                          │
│                   localhost:8000                         │
└──────────────────────┬──────────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │      Intent Agent        │  ← Classifies query type
          │   (Query Understanding)  │    (definition / comparison
          └────────────┬────────────┘     / calculation / advice)
                       │
          ┌────────────▼────────────┐
          │      Policy Agent        │  ← Validates query scope
          │   (Guardrails / Safety)  │    ensures finance domain
          └────────────┬────────────┘
                       │
          ┌────────────▼────────────┐
          │     Retrieval Agent      │  ← Semantic search over
          │  (FAISS + Embeddings)    │    vector knowledge base
          └────────────┬────────────┘    (nomic-embed-text)
                       │
          ┌────────────▼────────────┐
          │     Response Agent       │  ← Generates structured,
          │  (LLaMA3 via Ollama)    │    grounded response
          └─────────────────────────┘
```

### Data Flow
1. User submits a financial question via Streamlit chat UI
2. **Intent Agent** classifies the query (definition / comparison / calculation / general advice)
3. **Policy Agent** validates the query is within financial domain scope
4. **Retrieval Agent** performs semantic similarity search over FAISS vector database using `nomic-embed-text` embeddings
5. Top-k relevant document chunks are passed as context to the LLM
6. **Response Agent** (LLaMA3 via Ollama) generates a structured, citation-grounded answer
7. Response is streamed back to the Streamlit UI

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | Streamlit | Interactive chat UI |
| **Backend** | FastAPI | REST API, agent orchestration |
| **LLM** | Ollama + LLaMA3 | Local language model inference |
| **Embeddings** | nomic-embed-text | Semantic vector encoding |
| **Vector DB** | FAISS | Fast similarity search |
| **Containerisation** | Docker + Docker Compose | Reproducible deployment |
| **Language** | Python 3.10+ | Core implementation |

---

## 🚀 Quick Start

### Prerequisites
- Docker & Docker Compose installed
- Ollama installed ([ollama.com](https://ollama.com))
- 8GB+ RAM recommended (for local LLM)

### Step 1 — Pull required models

```bash
ollama pull llama3
ollama pull nomic-embed-text
```

### Step 2 — Clone the repository

```bash
git clone https://github.com/AtharvaKate2001/Finance-Intelligence-Agent-FIA.git
cd Finance-Intelligence-Agent-FIA
```

### Step 3 — Build and run with Docker Compose

```bash
docker compose up --build
```

### Step 4 — Access the application

| Service | URL |
|---------|-----|
| Streamlit Chat UI | http://localhost:8501 |
| FastAPI Backend | http://localhost:8000 |
| API Docs (Swagger) | http://localhost:8000/docs |

> **Note:** The `vector_db/` folder is generated locally on first run from the documents in `finance_docs/`. This step may take 2–3 minutes on the first launch.

---

## 💬 Demo

### Sample Queries & Responses

**Query 1 — Concept explanation**
```
User: What is inflation and how does it affect my savings?

FIA: Inflation is a sustained increase in the general price level of goods
     and services. For your savings specifically:

     • At 6% inflation, ₹1,00,000 today equals ₹94,000 in real value next year
     • Savings accounts earning <6% interest lose real purchasing power
     • Recommended: Consider inflation-beating instruments like equity mutual
       funds (historical 12-15% CAGR) or inflation-indexed bonds

     Sources: [Financial Planning Handbook, Ch. 3], [RBI Guidelines 2024]
```

**Query 2 — Comparison**
```
User: SIP vs lump sum investment — which is better?

FIA: Both strategies have merit depending on market conditions:

     SIP (Systematic Investment Plan):
     ✓ Rupee cost averaging reduces timing risk
     ✓ Suitable for salaried investors with regular income
     ✓ Enforces financial discipline

     Lump Sum:
     ✓ Better returns when markets are at cyclical lows
     ✓ Ideal for windfall amounts (bonus, inheritance)
     ✗ Higher timing risk

     Verdict: For most retail investors, SIP is recommended due to lower
     risk and automatic discipline. Use lump sum only when PE ratio < 18.
```

**Query 3 — Boundary handling**
```
User: What is the best cricket team?

FIA: I specialise in financial queries only. I can help you with topics like
     investing, budgeting, mutual funds, taxation, and financial planning.
     Please ask a finance-related question.
```

---

## 📊 Results

| Metric | Value |
|--------|-------|
| Average response time | ~3–5 seconds (local hardware) |
| Supported query types | Definition, Comparison, Calculation, Advice |
| Knowledge base size | 50+ curated financial documents |
| Out-of-scope rejection rate | 94% (tested on 50 non-finance queries) |
| Docker image size | ~2.1 GB (includes all dependencies) |

---

## 📁 Project Structure

```
Finance-Intelligence-Agent-FIA/
│
├── src/
│   ├── agents/
│   │   ├── intent_agent.py       # Query classification
│   │   ├── policy_agent.py       # Domain validation & guardrails
│   │   ├── retrieval_agent.py    # FAISS vector search
│   │   └── response_agent.py     # LLM response generation
│   ├── embeddings/
│   │   └── embedder.py           # nomic-embed-text integration
│   └── utils/
│       └── helpers.py            # Shared utilities
│
├── finance_docs/                  # Source knowledge base documents
├── notebooks/                     # Experimentation & EDA notebooks
├── main.py                        # CLI entry point
├── main_api.py                    # FastAPI application
├── streamlit_app.py               # Streamlit chat UI
├── Dockerfile                     # Backend container
├── Dockerfile.ui                  # Frontend container
├── docker-compose.yml             # Multi-container orchestration
└── README.md
```

---

## 🧪 Running Without Docker

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Start FastAPI backend
python main_api.py

# In a new terminal — start Streamlit UI
streamlit run streamlit_app.py
```

---

## 🗺️ Roadmap

- [x] Multi-agent pipeline (Intent → Policy → Retrieval → Response)
- [x] Local LLM inference via Ollama
- [x] FAISS vector database with semantic search
- [x] Dockerized deployment
- [x] Streamlit chat interface
- [ ] Streaming responses (token-by-token)
- [ ] Persistent chat history / memory
- [ ] User authentication
- [ ] Cloud deployment (AWS / GCP)
- [ ] Support for Hindi language queries
- [ ] Integration with live market data APIs
- [ ] Evaluation dashboard (response quality metrics)

---

## 🤝 Contributing

Contributions are welcome. Please open an issue first to discuss what you'd like to change.

```bash
# Fork the repo, then:
git checkout -b feature/your-feature-name
git commit -m "Add: your feature description"
git push origin feature/your-feature-name
# Open a Pull Request
```

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

**Atharva Kate**
AI Engineer | Prompt Engineering | RAG & Multi-Agent Systems

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://www.linkedin.com/in/atharva-kate2001/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat&logo=github)](https://github.com/AtharvaKate2001)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-FF0000?style=flat&logo=youtube)](https://www.youtube.com/@AspiringDataScientist01)

---

<div align="center">
  <i>If this project helped you, consider giving it a ⭐ — it helps others discover it.</i>
</div>

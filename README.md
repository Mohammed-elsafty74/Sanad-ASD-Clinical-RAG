# Sanad – Clinical ASD RAG Assistant 🏥💡

> **A safe, citation-grounded Clinical Decision Support & Guidance Assistant for Autism Spectrum Disorder (ASD).**

Sanad is a specialized Retrieval-Augmented Generation (RAG) web application engineered to assist clinicians, educators, and families by retrieving clinical evidence from authoritative autism literature (*Autism Spectrum Disorder: The Complete Guide*). The system enforces strict claim-level citation binding, zero-hallucination guardrails, and deterministic safety filters.

---

## 🌟 Key Features

* **🔒 Multi-Stage Safety & Guardrails:** Deterministic keyword pre-checker + similarity thresholding (`0.70`) to block unsafe diagnostic/dosage prompts, out-of-scope queries, and prompt injections.
* **📌 100% Citation Binding:** Every recommendation claim is strictly mapped to source document citations down to the page and chunk ID (`DOC-001-CH-XXXX`).
* **🎯 Zero-Hallucination Enforcement:** Strict JSON schema parsing with automated claim-level overlap validation against retrieved text context.
* **📄 Custom Document Upload & Targeting:** Upload supplementary PDFs dynamically and execute focused semantic queries on specific documents.
* **💬 Conversational Memory:** Maintains multi-turn context for clinical follow-up questions without compromising evidence retrieval boundaries.
* **🎨 Modern Responsive UI:** Lightweight Vanilla JS/CSS dashboard featuring real-time citation rendering, confidence indicators, and safety disclaimers.

---

## 📊 Empirical Evaluation & Benchmark Results

Evaluated against an internal clinical benchmark of **25 diverse queries** (direct evidence, compound synthesis, ambiguous requests, out-of-scope medical questions, unsafe dosage prompts, and jailbreak attempts):

| Metric | Score | Description |
| :--- | :---: | :--- |
| **Precision@5 (In-scope)** | **88.00%** | Relevant clinical chunks retrieved in Top-5 vector search |
| **Precision@3 (Baseline)** | **86.67%** | Relevant clinical chunks retrieved in Top-3 vector search |
| **Safety Pass Rate** | **100%** | Successfully blocked unsafe treatment/dosage & jailbreak inputs |
| **Out-of-Scope Refusal Rate** | **100%** | Correctly refused non-ASD queries using calibrated `0.70` threshold |
| **Citation Validity** | **100%** | All generated citations exist in retrieved vector index chunks |
| **Claim Faithfulness** | **100%** | **0.0% Hallucination Rate** across all answerable recommendations |

---

## 🛠️ System Architecture & Workflow

```
[User Input Query]
        │
        ▼
[Safety Pre-Check] ──(Unsafe Query)──► [Refusal Output]
        │ (Safe)
        ▼
[ChromaDB Vector Retrieval] (BAAI/bge-small-en-v1.5, Top-K = 5)
        │
        ▼
[Similarity Threshold Check] ──(< 0.70)──► [Insufficient Evidence Status]
        │ (≥ 0.70)
        ▼
[LLM Prompt Synthesis] (Groq API / LLM, Temp = 0.0)
        │
        ▼
[JSON Schema Parser & Citation Verifier]
        │
        ▼
[Validated Response + Citations + Confidence Level]
```

---

## 💻 Tech Stack

* **Backend Framework:** Python 3.10+, FastAPI, Pydantic, Uvicorn
* **Orchestration & RAG:** LangChain, FastEmbed
* **Vector Store & Embeddings:** ChromaDB, `BAAI/bge-small-en-v1.5`
* **Inference Engine:** Groq API (`openai/gpt-oss-120b`)
* **Frontend:** HTML5, Vanilla CSS3, JavaScript (ES6 Modules)
* **Document Processing:** PyPDFLoader, `RecursiveCharacterTextSplitter` (chunk_size: 850, overlap: 150)

---

## 📁 Repository Structure

```
.
├── backend/
│   ├── config.py                # Calibrated thresholds, paths, & env configurations
│   ├── main.py                  # FastAPI REST API endpoints & static server
│   ├── rag_pipeline.py          # Core RAG engine, safety checks, & LLM prompts
│   ├── db/                      # Persistent ChromaDB vector database directory
│   ├── document_upload/         # Custom user document indexing & retrieval routes
│   └── scripts/
│       └── build_index.py       # Offline PDF loading & vector indexing script
├── evaluation/
│   ├── DAY4_EVALUATION_REPORT.md# Complete evaluation benchmark analysis report
│   ├── day4_benchmark.csv       # 25-question clinical evaluation dataset
│   └── run_evaluation.py        # Automated benchmark evaluation runner
├── frontend/
│   ├── index.html               # Main application layout & chat dashboard
│   ├── script.js                # UI interaction, API call handling & citation renderer
│   └── style.css                # Custom UI styling & responsive themes
├── data/                        # Source PDF clinical literature
├── pyproject.toml               # Python project configuration
└── README.md                    # Project documentation
```

---

## 🚀 Getting Started

### Prerequisites
* Python 3.10 or higher
* Groq API Key ([Get a key from Groq Console](https://console.groq.com/))

### 1. Installation

Clone the repository and install the backend dependencies:

```bash
# Clone repository
git clone https://github.com/Mohanad06/Sanad-ASD-Clinical-RAG.git
cd Sanad-ASD-Clinical-RAG

# Install Python requirements
pip install -r backend/requirements.txt
```

### 2. Environment Setup

Create a `.env` file in the root directory:

```env
GROQ_API_KEY=your_groq_api_key_here
PORT=8000
HOST=0.0.0.0
```

### 3. Build Vector Index

Build the persistent vector database from the clinical literature PDFs:

```bash
python -m backend.scripts.build_index
```

### 4. Run Server

Start the FastAPI application:

```bash
python backend/main.py
```
*Or using uvicorn directly:*
```bash
uvicorn backend.main:app --reload --port 8000
```

### 5. Access Application

Open your browser and navigate to:
```text
http://localhost:8000/frontend/index.html
```

---

## 🧪 Running Benchmark Evaluation

To execute the automated evaluation suite against the 25-question clinical benchmark:

```bash
python evaluation/run_evaluation.py
```

The script will calculate Precision@K, Safety Pass Rates, Citation Binding, and output updated metrics to `evaluation/day4_results.csv`.

---

## 📡 Key API Endpoints

* `GET /api/health` — System status & database initialization status.
* `POST /api/query` — Execute RAG query against clinical vector store.
* `POST /api/documents/upload` — Upload and index custom PDF document.
* `POST /api/documents/query` — Search specifically within an uploaded document.

---

## ⚠️ Disclaimer

*Sanad is an educational and clinical decision support demonstration built for research and guidance purposes. It does not provide formal medical diagnoses or replace professional healthcare provider advice.*

---
License: MIT
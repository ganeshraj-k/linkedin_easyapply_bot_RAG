<div align="center">

# 🤖 LinkedIn EasyApply Bot — Powered by Agentic RAG

> Automate your LinkedIn EasyApply job applications with Selenium + Gen AI

[![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)](https://python.org)
[![Selenium](https://img.shields.io/badge/Selenium-Automation-43B02A?logo=selenium&logoColor=white)](https://selenium.dev)
[![FastAPI](https://img.shields.io/badge/FastAPI-API-009688?logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com)
[![LlamaIndex](https://img.shields.io/badge/LlamaIndex-RAG-6C5CE7)](https://llamaindex.ai)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--3.5-412991?logo=openai&logoColor=white)](https://openai.com)
[![Ollama](https://img.shields.io/badge/Ollama-phi--3--mini-000000?logo=ollama&logoColor=white)](https://ollama.com)

<a href="https://medium.com/@ganesh_012/automate-your-job-hunt-with-gen-ai-and-selenium-5348ad7f8119">
  <img src="https://img.shields.io/badge/Medium-Read%20the%20Article-000000?style=for-the-badge&logo=medium&logoColor=white" alt="Medium Article">
</a>
<a href="https://vimeo.com/1017782472">
  <img src="https://img.shields.io/badge/Vimeo-Watch%20Demo-1AB7EA?style=for-the-badge&logo=vimeo&logoColor=white" alt="Video Demo">
</a>

---

</div>

## 📋 Overview

This project automates job applications listed under LinkedIn's **EasyApply** feature. It combines **Selenium** for web automation with an **Agentic RAG** system that intelligently answers application questions based on your resume.

| Component | Role |
|-----------|------|
| 🖥️ **Selenium** | Logs in, searches jobs, fills forms, submits applications |
| 🧠 **Agentic RAG (LlamaIndex)** | Routes questions to the best model — short factual answers → **phi-3-mini**, long summaries → **GPT-3.5** |
| ⚡ **FastAPI + Uvicorn** | Packages the RAG engine as a local API |

---

## 🧭 Project Flowchart

<div align="center">
  <img src="flowchart.png" alt="Project Flowchart" width="80%" />
</div>

> *Click the image above for a larger view — or view the [SVG version](flowchart.svg).*

---

## ✨ Features

- ✅ **Fully automated** — LinkedIn login → job search → filter → apply → next page
- ✅ **Smart question answering** — 3-tier fallback: hash lookup → fuzzy match → RAG
- ✅ **Dual-model RAG** — Short questions → phi-3-mini (local, fast); long-form → GPT-3.5 (detailed)
- ✅ **Persistent cache** — Previously answered questions stored in JSON for O(1) retrieval
- ✅ **Resume-aware** — Generates answers dynamically from your uploaded resume
- ✅ **Progress logging** — All applications tracked in an Excel file

---

## 🧱 Tech Stack

<div align="center">

| Automation | RAG & LLMs | Infrastructure |
|:----------:|:----------:|:--------------:|
| <img src="logos_pngs/selenium.png" width="48" title="Selenium" /> | <img src="logos_pngs/phi3.png" width="48" title="Phi-3-mini" /> | <img src="logos_pngs/fastapi.jpeg" width="48" title="FastAPI" /> |
| <img src="logos_pngs/edge.jpeg" width="48" title="Edge WebDriver" /> | <img src="logos_pngs/gpt.png" width="48" title="GPT-3.5" /> | <img src="logos_pngs/logo-llamaIndex.png" width="48" title="LlamaIndex" /> |
| <img src="logos_pngs/json_icon.png" width="48" title="JSON Cache" /> | <img src="logos_pngs/database.png" width="48" title="Vector DB" /> | <img src="logos_pngs/excel.jpeg" width="48" title="Excel Logging" /> |

</div>

---

## 🚀 Quick Start

### Prerequisites

- Python 3, pip, Jupyter Notebook
- [Ollama](https://ollama.com/) (optional — only if using phi-3-mini locally)
- Edge WebDriver (or any browser driver)
- HuggingFace + OpenAI API keys

### 1. Clone & structure

```bash
git clone https://github.com/ganeshraj-k/linkedin_easyapply_bot_RAG.git
cd linkedin_easyapply_bot_RAG
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Set up Ollama (optional)

```bash
ollama run phi3:3.8b-mini-4k-instruct-q4_K_M
```

> 💡 A CUDA-enabled GPU is recommended for local inference.

### 4. Configure secrets

Create `secrets.json` in the root:

```json
{
  "linkedin_login": "your_email",
  "linkedin_password": "your_password",
  "openai_key": "sk-..."
}
```

### 5. Prepare your data

| File | Purpose |
|------|---------|
| `all_data/docs/` | Place your **resume PDF** and profile documents here |
| `all_data/search_details/search_details.json` | Set target **roles, location, filters** |
| `questions.json` | Start with `{}` — gets populated automatically |
| `applied_jobs.xlsx` | Empty Excel file for application logs |

Update file paths in `bot_sept21.ipynb` and `uvicorn_rag_apis/` to match your directory structure.

---

## ▶️ Running the Bot

### Step 1 — Start the RAG API

```bash
cd uvicorn_rag_apis
uvicorn agentic_rag_api:app --reload
```

> 🔄 Using only GPT-3.5? Replace `agentic_rag_api` with `gpt35_rag_api`.

### Step 2 — Run the automation

Open `bot_sept21.ipynb` in Jupyter Notebook and run all cells. The bot will:

1. Sign in to LinkedIn
2. Search for jobs matching your criteria
3. Apply to every EasyApply listing
4. Answer application questions using the RAG engine
5. Log results to `applied_jobs.xlsx`

### Step 3 — Monitor progress

Check `applied_jobs.xlsx` for a running log of every job applied to.

---

## 🧠 How the Query Engine Works

```
┌──────────────┐
│   Question   │
└──────┬───────┘
       ▼
┌─────────────────────────────────────┐
│  Step 1: Hash lookup (O(1))        │
│  → Exact match in questions.json    │
└─────────────────────────────────────┘
       ▼ (miss)
┌─────────────────────────────────────┐
│  Step 2: Fuzzy match (≥90%)        │
│  → Levenshtein distance on keys     │
└─────────────────────────────────────┘
       ▼ (miss)
┌─────────────────────────────────────┐
│  Step 3: Agentic RAG               │
│  ├─ Short / factual → phi-3-mini   │
│  └─ Long / summary  → GPT-3.5      │
└─────────────────────────────────────┘
```

The agent (LlamaIndex `RouterQueryEngine`) decides which model to call based on the question type.

---

## 📖 Project Walkthrough

A **detailed, step-by-step walkthrough** with code explanations, architecture diagrams, and a live demo video is available on Medium:

<div align="center">
  <a href="https://medium.com/@ganesh_012/automate-your-job-hunt-with-gen-ai-and-selenium-5348ad7f8119">
    <img src="https://img.shields.io/badge/📖_Read_the_Full_Article-000000?style=for-the-badge&logo=medium" alt="Medium Article">
  </a>
  <br><br>
  <a href="https://vimeo.com/1017782472">
    <img src="https://img.shields.io/badge/▶️_Watch_the_Demo-1AB7EA?style=for-the-badge&logo=vimeo" alt="Video Demo">
  </a>
</div>

---

## 🤝 Connect

Created by **Ganesh** — feel free to connect on LinkedIn!

[![LinkedIn](https://img.shields.io/badge/Connect_on_LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/feed/)

---

<div align="center">
  <sub>⭐ If you found this project useful, give it a star on <a href="https://github.com/ganeshraj-k/linkedin_easyapply_bot_RAG">GitHub</a>!</sub>
</div>
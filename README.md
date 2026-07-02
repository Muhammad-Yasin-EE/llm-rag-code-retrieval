# 📰 RAG News Assistant (ChromaDB + TinyLlama)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/downloads/)
[![Transformers](https://img.shields.io/badge/HuggingFace-Transformers-yellow.svg)](https://huggingface.co/docs/transformers)
[![Model](https://img.shields.io/badge/LLM-TinyLlama%201.1B-orange.svg)](https://huggingface.co/TinyLlama/TinyLlama-1.1B-Chat-v1.0)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

> A minimal, from-scratch Retrieval-Augmented Generation (RAG) demo — index a news dataset with ChromaDB, then show side-by-side how grounding a small local LLM in retrieved context changes (and improves) its answers.

---

## 🎯 What It Does

This project builds a small RAG pipeline in three parts:

1. **Index**: Loads a labeled news dataset and stores article titles + topic metadata in a **ChromaDB** collection (embedded automatically via ChromaDB's default sentence-transformer embedding function).
2. **Retrieve**: Given a query, performs a similarity search against the collection to pull the most relevant news titles as context.
3. **Generate**: Feeds that context into **TinyLlama-1.1B-Chat**, a small open-source local LLM, and compares its answer **with** vs. **without** retrieved context — a direct, visible demonstration of how RAG reduces hallucination on topics outside the model's training knowledge.

## 🧭 Architecture

```
                 ┌───────────────────────────┐
                 │  News Dataset (Kaggle)     │
                 │  labelled_newscatcher.csv  │
                 └─────────────┬─────────────┘
                                │  load + subset
                                ▼
                 ┌───────────────────────────┐
                 │  ChromaDB Collection       │
                 │  (auto-embedded titles +   │
                 │   topic metadata)          │
                 └─────────────┬─────────────┘
                                │  similarity search
                                ▼
   User question ──▶ Retrieved context (top-k titles)
                                │
                                ▼
                 ┌───────────────────────────┐
                 │  TinyLlama-1.1B-Chat       │
                 │  (HuggingFace transformers)│
                 └─────────────┬─────────────┘
                                ▼
              Answer WITH context  vs.  Answer WITHOUT context
```

## 🛠️ Tech Stack

| Layer | Tool |
|---|---|
| Vector Store | ChromaDB (persistent, local) |
| Embeddings | ChromaDB default (sentence-transformers) |
| LLM | `TinyLlama/TinyLlama-1.1B-Chat-v1.0` via HuggingFace `transformers` |
| Dataset | Kaggle [`kotartemiy/topic-labeled-news-dataset`](https://www.kaggle.com/datasets/kotartemiy/topic-labeled-news-dataset) |
| Dataset Access | `kagglehub` |
| Data Handling | Pandas, NumPy |
| Visualization | Matplotlib, scikit-learn (PCA for embedding inspection) |

## 📦 Project Structure

```
rag-news-tinyllama/
├── notebooks/
│   └── rag_news_tinyllama.ipynb   # Main notebook
├── data/                           # (gitignored) place labelled_newscatcher_dataset.csv here
├── chromadb/                       # (gitignored) local vector store, auto-created
├── requirements.txt
├── .gitignore
├── LICENSE
├── CONTRIBUTING.md
└── README.md
```

## 🏁 Quick Start

**1. Clone the repo**
```bash
git clone https://github.com/Muhammad-Yasin-EE/rag-news-tinyllama.git
cd rag-news-tinyllama
```

**2. Create a virtual environment**
```bash
python -m venv venv
venv\Scripts\activate
```

**3. Install dependencies**
```bash
pip install -r requirements.txt
```

**4. Get the dataset**

Option A — via `kagglehub` (requires a free Kaggle account + API token):
```python
import kagglehub
path = kagglehub.dataset_download("kotartemiy/topic-labeled-news-dataset")
```

Option B — download manually from [Kaggle](https://www.kaggle.com/datasets/kotartemiy/topic-labeled-news-dataset) and place `labelled_newscatcher_dataset.csv` in a `data/` folder.

**5. Run the notebook**
```bash
jupyter notebook notebooks/rag_news_tinyllama.ipynb
```

## 📝 Adjustments Needed Before Running

The original notebook was written for **Google Colab** and needs a few changes to run locally:

1. **Remove the Google Drive mount cell** (`from google.colab import drive; drive.mount(...)`).
2. **Update the dataset path** to a local file instead of a Drive path:
   ```python
   news = pd.read_csv('data/labelled_newscatcher_dataset.csv', sep=';')
   ```
3. **Update the ChromaDB persistence path** to a local folder:
   ```python
   chroma_client = chromadb.PersistentClient(path="./chromadb")
   ```
4. **First run will download the TinyLlama model weights** (~2.2GB) from HuggingFace automatically — no manual download needed, but ensure you have disk space and a stable connection.

## 💡 Example: Why RAG Matters

The notebook asks the same question two ways — with and without retrieved context — to make the effect of retrieval visible:

```python
# Without context — model answers from parametric knowledge only
question = "Can I buy a new Toshiba laptop?"

# With context — model is grounded in retrieved news titles
context = " ".join(results["documents"][0])
```

For niche or out-of-training-distribution questions (e.g. about a specific university's ranking), the ungrounded answer tends to hallucinate, while the context-grounded answer stays closer to the retrieved facts.

## 🩺 Troubleshooting

| Problem | Likely Cause | Fix |
|---|---|---|
| `kagglehub` download fails | Missing Kaggle API credentials | Set up `~/.kaggle/kaggle.json` per [Kaggle API docs](https://www.kaggle.com/docs/api), or download the CSV manually |
| Slow first run | TinyLlama weights downloading | Expected once; cached afterward in `~/.cache/huggingface` |
| ChromaDB "collection already exists" error | Re-running the notebook without clearing state | Delete the `chromadb/` folder, or uncomment the `delete_collection` line in the notebook |
| Irrelevant retrieved context | `MAX_NEWS` subset too small or query mismatched to dataset topics | Increase `MAX_NEWS` or rephrase the query to match dataset topics (business, tech, sports, etc.) |

## 🗺️ Roadmap

- [ ] Add quantitative evaluation (e.g. answer relevance scoring with/without context)
- [ ] Swap TinyLlama for a configurable model choice (Mistral, Phi-3, etc.)
- [ ] Add a simple retrieval quality metric (precision@k on topic labels)
- [ ] Package as a small CLI or Streamlit demo

## 🤝 Contributing

Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) for setup and PR guidelines.

## 📄 License

[MIT](LICENSE)

## 👤 Author

**Muhammad Yasin** — [@Muhammad-Yasin-EE](https://github.com/Muhammad-Yasin-EE)

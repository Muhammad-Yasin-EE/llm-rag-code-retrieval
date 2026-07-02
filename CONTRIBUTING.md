# Contributing to RAG News Assistant

Thanks for considering a contribution — bug fixes, new features, and documentation improvements are all welcome.

## Before You Start

- Check [open issues](../../issues) to see if your idea or bug is already tracked.
- For larger changes (new model support, new dataset, evaluation framework), open an issue first to discuss the approach before writing code.

## Setup for Development

```bash
git clone https://github.com/Muhammad-Yasin-EE/rag-news-tinyllama.git
cd rag-news-tinyllama
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

You'll also need the news dataset (see README Quick Start) — TinyLlama's weights download automatically on first run.

## Making a Change

1. **Fork** the repository
2. **Create a branch** with a descriptive name:
   ```bash
   git checkout -b feature/add-precision-at-k-eval
   ```
3. **Make your changes**
4. **Test your changes** — run the affected notebook cells end-to-end and confirm both the "with context" and "without context" example queries still produce sensible output
5. **Commit** with a clear, specific message:
   ```bash
   git commit -m "Add: precision@k retrieval evaluation"
   ```
6. **Push** to your fork:
   ```bash
   git push origin feature/add-precision-at-k-eval
   ```
7. **Open a Pull Request** against `main`, describing:
   - What the change does
   - Why it's needed
   - How you tested it

## Code Style

- Keep notebook cells focused — one logical step per cell, with a short markdown cell above new sections explaining what they do.
- Remove exploratory or leftover debug cells before merging (e.g. commented-out `delete_collection` calls left from testing).
- Prefer clear variable names over inline comments where possible.
- If you add a new dependency, pin its version in `requirements.txt`.

## Reporting Issues

Please include:
- What you expected to happen
- What actually happened (full error message/traceback if applicable)
- Your environment: OS, Python version, whether you're running CPU or GPU
- Steps to reproduce

## Reporting Security Concerns

This project doesn't handle authentication or user-submitted data in production, but if you spot something that could leak local file paths, API keys, or Kaggle credentials, please open an issue flagged `security` rather than a public PR with exploit details.

## Code of Conduct

Be respectful, assume good faith, and keep feedback constructive. This is a small educational project — the goal is learning and collaboration, not gatekeeping.

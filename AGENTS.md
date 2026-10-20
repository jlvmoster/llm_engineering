# AGENTS.md — LLM Engineering course repo

Guidance for AI coding agents working in this repository.

## What this repo is

Educational materials for **Ed Donner's LLM Engineering** course (Udemy / self-paced). It is **not** a single production application. Work is organized as weekly labs that build from API calls and scraping through Gradio UIs, Hugging Face / Colab, RAG, ML pricing models, fine-tuning, and a multi-agent deal finder.

Primary docs: `README.md`, `setup/SETUP-new.md`, `guides/`. Course resources: https://edwarddonner.com/2024/11/13/llm-engineering-resources/

## Layout (bottom-up)

| Path | Role |
|------|------|
| `pyproject.toml` / `uv.lock` | Single dependency set for the whole course (`uv sync`) |
| `.python-version` | Python **3.12.x** (project requires `>=3.11`) |
| `.env` | Local secrets only (gitignored; Cursor-ignored via `.cursorignore`) |
| `setup/` | Environment setup, diagnostics, troubleshooting |
| `guides/` | Foundations: CLI, git, notebooks, debugging, API/Ollama alternatives |
| `week1` … `week8/` | Day labs (`dayN.ipynb`), helper `.py` modules, exercises |
| `week*/community-contributions/` (and root `community-contributions/`) | Student submissions — huge trees; treat as read-only unless asked |
| `extras/` | Optional demos (e.g. trading prototypes) |
| `assets/` | README images |

### Week themes

1. **Week 1** — First LLM calls, web scraping (`scraper.py`), OpenAI / Ollama / Gemini via OpenAI-compatible clients  
2. **Week 2** — Gradio UIs, chatbots, multi-provider keys, airline assistant project  
3. **Week 3** — Google Colab + Hugging Face pipelines, tokenizers, models (GPU-friendly)  
4. **Week 4** — Code generation / multi-model apps  
5. **Week 5** — RAG (LangChain, Chroma, Insurellm knowledge-base, Gradio `app.py`)  
6. **Week 6** — “Price is Right” data/ML pipeline (`pricer/`, jsonl datasets)  
7. **Week 7** — Fine-tuning / Modal / Hugging Face (Colab links in day labs)  
8. **Week 8** — Agentic system: `agents/*`, `deal_agent_framework.py`, Modal pricer services, Gradio UI  

Later weeks reuse ideas and some code patterns from earlier weeks (especially pricing → agents).

## Environment and commands

- Package manager: **`uv`**. From project root: `uv sync`, `uv add <pkg>`, `uv run <script-or-module>`. Prefer `uv run` over activating `.venv` manually.
- Jupyter / Cursor notebooks should use the **`.venv`** kernel created by `uv sync`.
- Some labs also exist as **Marimo** apps (e.g. `week1/day1.py`); keep notebook and Marimo versions aligned only when the user is working on both.
- Week 3 / 7 GPU work often runs on **Colab**; do not assume local CUDA.
- Ollama for local models: prefer **`llama3.2`** (or `llama3.2:1b`); avoid huge models like llama3.3 70B on student machines.

## Secrets and cost

- Secrets live in **project-root** `.env`. Load with:

  ```python
  from dotenv import load_dotenv
  load_dotenv(override=True)
  ```

- Never print, commit, log, or paste API keys. Do not invent placeholders that look like real keys in shared code.
- Common env vars (add only as a lab needs them): `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `GOOGLE_API_KEY` / `GEMINI_API_KEY`, `GROQ_API_KEY`, `GROK_API_KEY`, `OPENROUTER_API_KEY`, `DEEPSEEK_API_KEY`, `HF_TOKEN`, `PUSHOVER_USER`, `PUSHOVER_TOKEN`, Modal tokens, etc. See `guides/09_ai_apis_and_ollama.ipynb`.
- Prefer cheap models when changing defaults unless the user asks for frontier quality: e.g. OpenAI `gpt-4.1-nano`, Anthropic Haiku-class. Keep API spend tiny; warn before large training/fine-tune runs.

## How to change code

### Default posture

- This is a **learning** repo: clarity and small, runnable steps beat clever abstractions.
- Prefer editing the lab the user is on (`weekN/dayM.ipynb` or adjacent helpers). Do not “clean up” unrelated weeks.
- **Do not** bulk-edit or refactor `community-contributions/` (or nested copies) unless the user explicitly names a contribution to change.
- Avoid drive-by refactors of course materials (renames, new package layouts, dependency upgrades) unless requested.
- New student experiments: put under `community-contributions/<name>/` or a clearly named file in the relevant `weekN/` folder — match existing contribution style.

### Notebooks vs modules

- Labs are primarily **Jupyter notebooks**. Keep cells educational: short narrative markdown, then code the student can run top-to-bottom.
- Extract shared logic into small `.py` helpers next to the lab when reuse is real (pattern already used: `scraper.py`, `week5/implementation/`, `week8/agents/`).
- For new features/projects: prototype in a notebook first, then promote to modules (course guidance in `guides/12_starting_your_project.ipynb`).
- Gradio UIs use `gr.Blocks` / Chatbot `type="messages"` in newer labs; match the surrounding week’s Gradio style (`gradio>=5,<6` in `pyproject.toml`).

### Idioms used in this course

```python
# OpenAI (cloud)
from openai import OpenAI
openai = OpenAI()

# Ollama via OpenAI-compatible API
ollama = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")

# Scraping: requests + BeautifulSoup, browser-like User-Agent (see week1/scraper.py)
# RAG: LangChain loaders/splitters + Chroma; embeddings often OpenAI or HuggingFace
# Week 8 agents: subclass agents.agent.Agent, set name/color, use self.log(...)
```

- Week 8 imports assume running with **`week8` as cwd** (e.g. `from agents.planning_agent import PlanningAgent`). Do not break that without updating call sites.
- Chroma / vector DBs and `memory.json` are local artifacts; do not commit large regenerated stores unless the user wants that.

## What agents should / should not do

**Do**

- Read the relevant `dayN.ipynb` (and linked helpers) before changing behavior.
- Keep solutions runnable cell-by-cell; preserve teaching comments when they explain intent.
- Use `setup/diagnostics.py` / troubleshooting labs when debugging environment issues.
- Point to `guides/09_ai_apis_and_ollama.ipynb` for free/local model alternatives.
- When helping with exercises, prefer completing `*EXERCISE*` notebooks or a new contribution file over spoiling official solutions unless asked.

**Do not**

- Commit `.env`, tokens, or notebook outputs that embed secrets.
- Run expensive Modal / HF training / huge downloads without explicit user intent.
- Search or rewrite the entire `community-contributions` tree for style consistency.
- Replace course stack (uv, Gradio, LangChain, Chroma, etc.) with unrelated frameworks for convenience.

## Quick orientation checklist

1. Confirm project root and `.venv` via `uv sync` if imports fail.  
2. Confirm `.env` exists at repo root and required keys for that lab.  
3. Open the week/day notebook the user cares about; skim markdown goals.  
4. Change the smallest surface that teaches or unblocks the task.  
5. Re-run the affected cells or `uv run` the relevant script to verify.

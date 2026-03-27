# Python TutorBot

An interactive AI-powered Python tutor that answers questions, explains concepts, and retrieves real-world code examples from Stack Overflow on demand.

Built as a portfolio project to demonstrate LLM tool-calling, conversational memory management, and agentic workflows using LangChain.

---

## Problem it solves

Learning Python from static resources is passive. This bot acts as an on-demand tutor: it explains concepts in plain language, guides students through mistakes rather than just giving answers, and can fetch real Stack Overflow examples when a topic needs a real-world reference — all inside a simple chat interface.

---

## How it works

The bot runs a LangChain agentic loop:

1. The user sends a message through the Gradio chat interface.
2. The conversation history is reconstructed as a `messages` list and sent to the LLM via OpenRouter.
3. If the model decides to call a tool (e.g., the user asks how a concept is typically used in practice), it calls `search_examples`, which queries the Stack Overflow API and returns relevant excerpts.
4. The tool result is appended as a `ToolMessage` and the model generates a final response grounded in that context.
5. The response is returned to the UI.

A session-level counter limits API calls to prevent runaway usage during testing.

---

## Tech stack

| Layer | Technology |
|---|---|
| LLM provider | [OpenRouter](https://openrouter.ai) (free tier) |
| LLM framework | [LangChain](https://python.langchain.com) |
| External tool | Stack Overflow API (`/search/excerpts`) |
| Frontend | [Gradio](https://gradio.app) `ChatInterface` |
| Env management | `python-dotenv` |
| Runtime | Google Colab / local Python ≥ 3.13 |
| Package manager | [uv](https://github.com/astral-sh/uv) |

---

## Frontend

The UI is built entirely with **Gradio** — specifically `gr.ChatInterface`. No custom HTML, CSS, or JavaScript was written. Gradio handles input, chat history rendering, and example prompts out of the box. This was a deliberate choice to keep the project focused on the LLM and tool-calling logic rather than frontend scaffolding.

---

## Run locally

### Prerequisites

- Python ≥ 3.13
- [`uv`](https://github.com/astral-sh/uv) installed

### Steps

```bash
git clone https://github.com/your-username/bot-python.git
cd bot-python

# Install dependencies
uv sync

# Copy and fill in the env file
cp .env.example .env

# Launch the notebook
uv run jupyter notebook TutorBotPython.ipynb
```

The Gradio interface will be available at `http://localhost:7860` after the last notebook cell runs.

### Run on Google Colab

Open `TutorBotPython.ipynb` in Colab and click **Run All**. The notebook installs its own dependencies and launches a public Gradio share link automatically.

---

## Environment variables

| Variable | Description |
|---|---|
| `OPENROUTER_API_KEY` | API key from [openrouter.ai](https://openrouter.ai). The free tier is sufficient to run this project. |

Create a `.env` file at the project root:

```env
OPENROUTER_API_KEY=your_key_here
```

> **Note:** The Colab notebook includes a hardcoded demo key restricted to free models only and configured to prevent any credit spending. Do not use it in production.

---

## Project structure

```
bot-python/
├── TutorBotPython.ipynb   # Main notebook — step-by-step walkthrough + runnable bot
├── main.py                # Entry point placeholder
├── pyproject.toml         # Dependencies and project metadata
├── uv.lock                # Locked dependency tree
└── .env                   # API keys (not committed)
```

---

## Key design decisions

- **Tool calling over RAG**: Instead of embedding a document corpus, the bot calls the Stack Overflow API live. This keeps the setup simple and ensures examples are real, community-vetted code.
- **Short-term memory only**: Conversation history is rebuilt from the Gradio `history` object on each turn — no external database or vector store required.
- **Low temperature (0.3)**: Keeps the tutor's explanations consistent and factual, reducing hallucinations in a domain where correctness matters.
- **Socratic guidance**: The system prompt instructs the model to give hints rather than full solutions, which is more effective for learning.

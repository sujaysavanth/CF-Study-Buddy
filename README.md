# CF-Study-Buddy

An AI-powered flashcard and Q&A tutor built on Cloudflare's developer platform.

## Overview

CF-Study-Buddy lets you paste any topic, document, or set of notes and instantly get an interactive AI tutor that quizzes you, answers follow-up questions, and tracks what you've already covered — all running at the edge with no backend servers.

## Architecture

| Component | Cloudflare Technology |
|---|---|
| LLM | Workers AI — Llama 3.3 (`@cf/meta/llama-3.3-70b-instruct-fp8-fast`) |
| Workflow / API | Cloudflare Workers |
| Frontend / Chat UI | Cloudflare Pages |
| Memory / State | Durable Objects (per-session conversation history + flashcard sets) |

## Features

- **Chat interface** — ask the tutor anything about your topic; get concise, accurate answers powered by Llama 3.3
- **Flashcard generation** — type `/flashcards` to auto-generate a set of Q&A cards from your session context
- **Session memory** — conversation history persisted in a Durable Object so the tutor remembers what you've already covered
- **Edge-native** — deployed globally on Cloudflare's network, no origin server required

## Project Structure

```
CF-Study-Buddy/
├── worker/
│   ├── src/
│   │   ├── index.ts          # Worker entry point — routing & Workers AI calls
│   │   └── studySession.ts   # Durable Object — session state & history
│   └── wrangler.toml         # Cloudflare Workers config
├── frontend/
│   ├── public/
│   │   └── index.html        # Chat UI (Pages)
│   └── src/
│       └── main.js           # Frontend logic — chat, flashcard rendering
└── README.md
```

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v18+
- [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/) — `npm install -g wrangler`
- A Cloudflare account (free tier works)

### Local Development

```bash
# Clone the repo
git clone https://github.com/sujaysavanth/CF-Study-Buddy.git
cd CF-Study-Buddy/worker

# Install dependencies
npm install

# Run locally
wrangler dev
```

Frontend (in a separate terminal):
```bash
cd ../frontend
npx serve public
```

### Deploy

```bash
# Deploy the Worker + Durable Object
cd worker
wrangler deploy

# Deploy the frontend to Pages
cd ../frontend
wrangler pages deploy public --project-name cf-study-buddy
```

## How It Works

1. User sends a message via the chat UI on Cloudflare Pages
2. The request hits a **Cloudflare Worker** which retrieves the user's **Durable Object** (keyed by session ID)
3. The Durable Object loads conversation history and appends the new message
4. The Worker calls **Workers AI** (Llama 3.3) with the full conversation context
5. The response is streamed back to the UI and the Durable Object persists the updated history
6. On `/flashcards`, the Worker prompts the LLM to generate structured Q&A cards from the session so far

## Tech Stack

- **Runtime:** Cloudflare Workers (TypeScript)
- **AI:** Cloudflare Workers AI — `@cf/meta/llama-3.3-70b-instruct-fp8-fast`
- **State:** Cloudflare Durable Objects
- **Frontend:** Cloudflare Pages (vanilla JS + HTML)

## License

MIT

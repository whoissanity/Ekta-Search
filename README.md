# [ EktaSearch](https://ektasearch.com)



### Bangladesh's Tech Search Engine

Ektai Search Lagbe.<br />
**"Most Bangladeshi e-commerce websites were shitty. So I built the fix."**

[![Frontend](https://img.shields.io/badge/Frontend-React%2018%20%2B%20Vite-61DAFB?style=for-the-badge&logo=react&logoColor=black)](#tech-stack)
[![Backend](https://img.shields.io/badge/Backend-FastAPI%20%2B%20Python-009688?style=for-the-badge&logo=fastapi&logoColor=white)](#tech-stack)
[![State](https://img.shields.io/badge/State-Zustand-7C3AED?style=for-the-badge)](#tech-stack)
[![Styling](https://img.shields.io/badge/UI-TailwindCSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)](#tech-stack)
[![Data](https://img.shields.io/badge/Data-Redis%20Cache%20%2B%20SQLite-4CAF50?style=for-the-badge)](#tech-stack)



---

## Why I Built This

PC buyers in Bangladesh usually have to:

- Open many tabs across multiple stores
- Manually compare prices and stock
- Guess part compatibility for custom builds
- Lose context between browsing, comparing, and purchasing

EktaSearch turns that fragmented flow into one product.

---

## What EktaSearch Does

- **Unified multi-retailer search** with a single query fan-out
- **Progressive streaming results** (NDJSON) so users see data as it arrives
- **Price comparison groups** for side-by-side retailer options
- **PC Builder** with live compatibility and wattage analysis
- **Guest cart sessions** tied to `x-session-id` (no login required)
- **Community forum** (posts, replies, votes, file attachments)
- **Auth system** for registered users and owner moderation signals

---

## Retailers Integrated (Live Adapter Registry)

- Ryans Computers
- Star Tech
- Tech Land BD
- Skyland
- Vibe Gaming
- Tech Diversity BD
- The Blisstronics
- PoTaka IT

---

## Architecture Overview

```text
React + Vite SPA
   |
   v
FastAPI API Layer
   |- /api/search        -> merged JSON results
   |- /api/search/stream -> progressive NDJSON chunks
   |- /api/compare       -> grouped alternatives
   |- /api/builder       -> compat + wattage + save/load
   |- /api/cart          -> session cart lifecycle
   |- /api/community     -> posts/replies/votes/attachments
   '- /api/auth          -> register/login/session token
   |
   v
Adapter Fan-out (8 retailers in parallel)
   |
   v
Relevance Scoring + De-dup + Filtering + Sorting
   |
   v
Redis cache (primary) + SQLAlchemy persistence (SQLite default)
```

---

## Why This Works

EktaSearch works because it is system-first, not page-first:

1. **Parallel retrieval** from all adapters reduces user effort and decision latency.
2. **Streaming-first UX** returns early chunks before full completion.
3. **Relevance + deterministic sorting** keeps results useful, not noisy.
4. **Stateful product flow** (search -> compare -> build -> cart -> community) keeps users in one loop.
5. **Cache strategy** cuts repeat query cost and stabilizes response time.

---

## Tech Stack

### Frontend

- React 18 + TypeScript
- Vite 5
- TailwindCSS
- Zustand (search/cart/builder state)
- Axios + Fetch streaming for NDJSON
- React Router v6

### Backend

- Python 3 + FastAPI
- Async fan-out with `asyncio`
- SQLAlchemy (async) + `aiosqlite`
- Redis cache
- Pydantic + pydantic-settings
- Tenacity, httpx/aiohttp, BeautifulSoup/lxml for resilient data ingestion

---

## Core API Surface

- `GET /api/search`
- `GET /api/search/stream`
- `GET /api/compare`
- `POST /api/builder/analyze`
- `POST /api/builder/save`
- `GET /api/builder/{build_id}`
- `GET /api/cart`, `POST /api/cart/add`, `DELETE /api/cart`
- `GET /api/community/posts`, `POST /api/community/posts`
- `POST /api/community/posts/{post_id}/replies`
- `POST /api/community/posts/{post_id}/vote`
- `POST /api/community/attachments`
- `POST /api/auth/register`, `POST /api/auth/login`, `GET /api/auth/me`
- `GET /api/health`

---

## Local Development

### 1) Backend

```bash
cd backend
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# macOS/Linux:
# source .venv/bin/activate

pip install -r requirements.txt
copy .env.example .env
uvicorn app.main:app --reload
```

### 2) Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend runs on `http://localhost:5173` and proxies to FastAPI routes under `/api`.

---

## Environment Configuration

Configure secrets and runtime behavior in `backend/.env`:

- Retailer API keys and base URLs
- `REDIS_URL`
- `DATABASE_URL`
- `CACHE_TTL_SECONDS`
- `SEARCH_SHOP_TIMEOUT_SECONDS`
- `SEARCH_MAX_RETAILER_PAGES`
- `PREWARM_ENABLED` / `PREWARM_INTERVAL_SECONDS`

Use `backend/.env.example` as the template.

---




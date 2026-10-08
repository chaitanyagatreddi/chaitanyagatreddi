# 👋 Hi, I'm Chaitanya

I build growth systems and products: agents, scrapers and data pipelines that turn public signals (SERPs, AI answers, GitHub activity, hiring posts) into decisions a GTM team can act on. 📍 Based in Bangalore.

👇 Each project below covers the **problem** it solves, **how** it works, and the **stack** it's built on.

---

## 📡 Signal & attribution systems

### 🕳️ [dark-funnel-decoder](https://github.com/chaitanyagatreddi/dark-funnel-decoder)
🏆 My Reo × Claude Builder Challenge entry.
- 🎯 **Problem:** open-source companies have heavy usage they can't tie to accounts or people.
- ⚙️ **How it works:** you paste a GitHub repo or org URL. The system pulls the owner's top repos and their contributors from the GitHub REST API and crawls profiles with Crawl4AI. It then finds contact emails through a waterfall (GitHub public events and commit patches, then the Stack Exchange API, then Prospeo, with Firecrawl scraping personal sites) and scores each contributor with gpt-4o-mini. Progress streams to the UI live over SSE.
- 🔮 **Next layer (designed in `ARCHITECTURE.md`):** BuiltWith MCP for install fingerprinting and competitor overlap, Headroom MCP to compress bulk data before the LLM call, Claude to write a 5-section teardown, and Trigger.dev for orchestration. The Trigger.dev project is scaffolded.
- 🧱 **Stack:** Python, Flask + SSE, Crawl4AI, Firecrawl, GitHub API, Stack Exchange API, Prospeo, OpenAI, Docker.

### 📡 [signalx](https://github.com/chaitanyagatreddi/signalx)
- 🎯 **Problem:** devtool outbound needs to know who actually builds in a space, not just a list of company names.
- ⚙️ **How it works:** you enter a keyword. Crawl4AI searches GitHub for matching repos, then the GitHub API maps each repo's top contributors and their profiles. An email waterfall runs (commit patches, Stack Exchange, Prospeo), and gpt-4o-mini rates each person as core, active or emerging with an activity score.
- 🧱 **Stack:** Python, Flask + SSE, Crawl4AI, Firecrawl, GitHub API, OpenAI, Docker, Render.

### 🧭 [job-signal](https://github.com/chaitanyagatreddi/job-signal)
- 🎯 **Problem:** job boards are full of stale listings, while real hiring intent shows up in founders' posts.
- ⚙️ **How it works:** a CLI pipeline of agents. The scout pulls LinkedIn posts through an Apify actor. The ranker scores them with weights adapted from twitter/the-algorithm (velocity 30%, authority 35%, recency 25%, reply ratio 10%, plus intent and location boosts). gpt-4o-mini parses the resume and scores fit with STAR. A daily cap limits scraping volume.
- 🧱 **Stack:** Python, Apify, OpenAI.

### 💼 [jobspy-ui](https://github.com/chaitanyagatreddi/jobspy-ui)
- 🎯 **Problem:** recent roles across job boards, ranked by fit instead of by recency.
- ⚙️ **How it works:** it scrapes jobs through JobSpy and resolves company names to LinkedIn company IDs for precise filtering. Full job descriptions are fetched only when a role is scored, and gpt-4o-mini scores each role 0–10 against a profile and suggests an outreach hook.
- 🧱 **Stack:** Python, Flask, JobSpy, OpenAI, Render.

---

## 🔎 SEO & AEO systems

### 🔍 [ai-visibility-audit](https://github.com/chaitanyagatreddi/ai-visibility-audit) · 🔗 [demo](https://ai-visibility-audit-chaitanya67.replit.app)
- 🎯 **Problem:** buyers now research in AI answer engines, and most brands don't know whether they show up there.
- ⚙️ **How it works:** gpt-4o-mini detects the brand and industry from a URL and writes the queries. A Browserbase cloud browser driven by Playwright over CDP runs each query on Google (to capture the AI Overview) and on Perplexity and extracts the answers. gpt-4o-mini then pulls out brand mentions, positions and competitors. A deterministic 0–100 score (mention rate, position, platform and query coverage) feeds a report with gap analysis and GEO recommendations based on the Princeton KDD 2024 GEO paper.
- 🧱 **Stack:** Python, Flask + SSE, Browserbase, Playwright, OpenAI, Docker. Deploy configs for Render, Railway and Replit.

### 🗂️ [GTM](https://github.com/chaitanyagatreddi/GTM)
🎤 I presented this pipeline at **BrightonSEO** in the talk *Schema Scraper for low volume keywords*, which covered using SERP-based clustering to find and win low-volume, high-intent keywords for niche businesses.
- 🎯 **Problem:** clustering by word similarity puts keywords with different intent on the same page.
- ⚙️ **How it works:** a Colab pipeline. It cleans a keyword export with pandas, sends batches of up to 1,000 queries to the ValueSERP batch API, and clusters keywords that share 4 or more top-ranking URLs, which means Google treats them as the same intent. The clustering step runs on a hosted endpoint, and results are visualised with a Plotly treemap and a WordCloud.
- 🧱 **Stack:** Python, Google Colab, ValueSERP, pandas, Plotly, WordCloud.

---

## 📈 Forecasting & decision tools

### 📈 [gtm-predictor](https://github.com/chaitanyagatreddi/gtm-predictor) · 🔗 [live](https://gtm-predictor-two.vercel.app)
- 🎯 **Problem:** marketers set budgets and ship pages on guesswork. This answers "what will this spend return, and is this page or ad good enough?" with cited benchmarks.
- ⚙️ **How it works:**
  - 📊 **Forecasting:** PPC and ABM funnel models adjust CTR and conversion by CRO and creative scores. Benchmarks are picked in tiers: live ZenABM data for LinkedIn, then first-party B2C SaaS funnel data, then Kaggle medians by region, then public WordStream baselines. It also runs a reverse ABM funnel (from target deals to the spend needed) and an LTV/CAC calculator.
  - 🏅 **Scoring:** gpt-4o-mini in JSON mode scores landing pages, competitor pages side by side, ad creative and cold email against a rubric, with curated reference pages as few-shot gold standards.
  - 📥 **Inputs:** URL scraping tries Firecrawl, then ZenRows with JS rendering, then a built-in fallback. Search Console data comes in through Google OAuth, and GA4 page exports are parsed from CSV.
- 🧱 **Stack:** Python, FastAPI, OpenAI, httpx, React + framer-motion (via CDN), Vercel.

### 🍅 [tomato-kitchen-agent](https://github.com/chaitanyagatreddi/tomato-kitchen-agent) · 🤗 [HF Space](https://huggingface.co/spaces/chaitubatman/cloudkitchen)
- 🎯 **Problem:** cloud kitchens over-order or under-order because they plan without demand forecasts.
- ⚙️ **How it works:** a LangGraph ReAct agent on gpt-4o-mini with 5 tools: forecast, order suggestion, city comparison, channel split and procurement list. Prophet (with weekly seasonality) forecasts demand by city and item on synthetic data, and order suggestions add a 15% safety-stock buffer. Ops managers ask questions in plain English over a WebSocket chat.
- 🧱 **Stack:** Python, LangGraph, LangChain, Prophet, pandas, FastAPI + WebSocket, React, Vite, Tailwind, Recharts, Docker, Hugging Face Spaces.

---

## 🧾 Growth audits

### 🧯 [sprinto](https://github.com/chaitanyagatreddi/sprinto)
- 🎯 **Problem:** finding where a SaaS site leaks pipeline after a redesign, and putting a revenue number on it.
- ⚙️ **How it works:** a SERP rank-factor comparison against competitors, a URL crawl of 474 pages tested against common post-redesign breakage patterns, Reddit thread mining, and CPC estimates. Leakage is sized as `visits × page→demo% × demo→close% × ACV`.
- 📄 **Output:** a static HTML report.

### 🧾 [humantic-audit](https://github.com/chaitanyagatreddi/humantic-audit)
- 🎯 **Problem:** a full GTM, SEO and AEO teardown of an enterprise SaaS site.
- ⚙️ **How it works:** a Firecrawl crawl of 40 pages for canonical, noindex and broken-link issues, AEO readiness checks (schema, llms.txt), CRO scores from gtm-predictor, and the same revenue-leak formula.
- 📄 **Output:** a markdown audit.

---

## 🧰 Tech stack

**💻 Languages**

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white) ![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white) ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black) ![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat&logo=html5&logoColor=white)

**🤖 Agents & LLMs**

![OpenAI](https://img.shields.io/badge/OpenAI_gpt--4o--mini-412991?style=flat&logo=openai&logoColor=white) ![LangGraph](https://img.shields.io/badge/LangGraph-1C3C3C?style=flat&logo=langchain&logoColor=white) ![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=flat&logo=langchain&logoColor=white) ![ScrapeGraphAI](https://img.shields.io/badge/ScrapeGraphAI-555555?style=flat)

**🕷️ Scraping & browsers**

![Browserbase](https://img.shields.io/badge/Browserbase-111111?style=flat) ![Playwright](https://img.shields.io/badge/Playwright-2EAD33?style=flat&logo=playwright&logoColor=white) ![Crawl4AI](https://img.shields.io/badge/Crawl4AI-555555?style=flat) ![Firecrawl](https://img.shields.io/badge/Firecrawl-FF6B00?style=flat) ![ZenRows](https://img.shields.io/badge/ZenRows-555555?style=flat) ![Apify](https://img.shields.io/badge/Apify-97D700?style=flat&logo=apify&logoColor=black) ![JobSpy](https://img.shields.io/badge/JobSpy-555555?style=flat) ![ValueSERP](https://img.shields.io/badge/ValueSERP-555555?style=flat) ![GitHub API](https://img.shields.io/badge/GitHub_REST_API-181717?style=flat&logo=github&logoColor=white)

**📊 Data & forecasting**

![Prophet](https://img.shields.io/badge/Prophet-0467DF?style=flat&logo=meta&logoColor=white) ![pandas](https://img.shields.io/badge/pandas-150458?style=flat&logo=pandas&logoColor=white) ![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white) ![Plotly](https://img.shields.io/badge/Plotly-3F4F75?style=flat&logo=plotly&logoColor=white) ![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C?style=flat) ![Google Colab](https://img.shields.io/badge/Colab-F9AB00?style=flat&logo=googlecolab&logoColor=black) ![Search Console API](https://img.shields.io/badge/Search_Console_API-4285F4?style=flat&logo=google&logoColor=white)

**🖥️ Backend & UI**

![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white) ![Django](https://img.shields.io/badge/Django-092E20?style=flat&logo=django&logoColor=white) ![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white) ![React](https://img.shields.io/badge/React-20232A?style=flat&logo=react&logoColor=61DAFB) ![Vite](https://img.shields.io/badge/Vite-646CFF?style=flat&logo=vite&logoColor=white) ![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat&logo=tailwindcss&logoColor=white) ![Recharts](https://img.shields.io/badge/Recharts-22B5BF?style=flat) ![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=flat&logo=streamlit&logoColor=white)

**☁️ Infra & deploy**

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white) ![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat&logo=vercel&logoColor=white) ![Render](https://img.shields.io/badge/Render-46E3B7?style=flat&logo=render&logoColor=black) ![Railway](https://img.shields.io/badge/Railway-0B0D0E?style=flat&logo=railway&logoColor=white) ![Hugging Face Spaces](https://img.shields.io/badge/Hugging_Face_Spaces-FFD21E?style=flat&logo=huggingface&logoColor=black) ![Replit](https://img.shields.io/badge/Replit-F26207?style=flat&logo=replit&logoColor=white)

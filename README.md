# 🎯 AI Automation Job Hunter

An n8n workflow that hunts relevant jobs for you **24/7**. Every 2 hours it reads job RSS feeds, filters by your keywords, skips anything it has already seen, scores each vacancy with an **LLM**, drafts a personalised cover letter, and pushes the good matches straight to **Telegram**.

Built as a personal job-search automation — it turns "manually refreshing job boards" into a hands-off pipeline.

---

## ✨ What it does

1. ⏰ **Runs every 2 hours** (Schedule Trigger).
2. 📰 **Reads two RSS feeds** — Djinni & DOU (easily extendable to more boards).
3. 🔀 **Merges** both feeds into one stream.
4. 🔎 **Keyword filter** — keeps only vacancies mentioning `n8n`, `automation`, `автоматиз`, `ai agent`, `make.com`, `zapier`.
5. 🧠 **De-dupes** against a `seen_jobs` Data Table so you never get the same job twice.
6. 🤖 **LLM scoring + draft** — an AI Agent (OpenAI `gpt-5-mini`) rates each job `0–100` against your profile and writes a short tailored cover-letter draft, returning **structured JSON** via an output parser.
7. ✅ **Relevance gate** — only jobs marked `relevant` continue.
8. 📲 **Telegram** — sends score, reason, draft and link to your chat.

---

## 🏗 Flow

```
Every 2 Hours ─┬─► Read Djinni Feed ─┐
               └─► Read DOU Feed ─────┴─► Merge Feeds ─► Keep Relevant Keywords
                                                              │
   Find New Jobs (seen_jobs table) ◄───────────────────────────┘
        │
        ▼
   Score & Draft Reply (AI Agent) ──► Format Job ──► Mark As Seen ──► Is Relevant? ──► Send To Telegram
        ├─ OpenAI Chat Model (gpt-5-mini)
        └─ Job Match Schema (structured output parser)
```

---

## 🧩 Tech stack

| Layer | Tool |
|-------|------|
| Orchestration | n8n |
| Sources | RSS (Djinni, DOU) |
| LLM | OpenAI `gpt-5-mini` + structured output parser |
| Dedup store | n8n Data Table (`seen_jobs`) |
| Delivery | Telegram Bot |

---

## 🚀 Setup

1. **Import** `workflows/job-hunter.json` into n8n (*Workflows → Import from File*).
2. **Credentials:** OpenAI API + Telegram Bot.
3. **Create a Data Table** named `seen_jobs` with a `link` (string) column.
4. **Fill placeholders:**
   - `Read Djinni Feed` / `Read DOU Feed` → your saved-search **RSS URLs**.
   - `Send To Telegram` → your **chat ID** (get it from [@get_id_bot](https://t.me/get_id_bot)).
5. **Tune** keywords in `Keep Relevant Keywords` and the candidate profile in the `Score & Draft Reply` system prompt.
6. **Activate** the workflow.

> ⚙️ The LLM returns strict JSON (`relevant`, `score`, `reason`, `draft`) enforced by the structured output parser, so downstream nodes stay reliable.

---

## 📄 License

[MIT](./LICENSE)

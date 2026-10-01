# Zyra.ai

An agentic AI voice interviewer. Riley conducts a short voice interview (3 to 4 questions, about 3 to 4 minutes), scores every answer, and rewrites the next question to be harder or easier based on how the candidate did. Every decision is saved, so the full reasoning can be replayed as a timeline and an interrupted interview can be resumed.

Built for BHARAT AGENTIC 2026 (domain: EdTech & Future Skills).

## Why it is agentic

Understand, reason, plan, use tools, act, deliver:

1. **Plan:** `generateInterview` plans 3 to 4 questions with escalating difficulty and saves the plan.
2. **Observe and reason:** after each answer, `submitAnswer` scores it with an LLM and decides the next difficulty.
3. **Replan:** the next question is rewritten if the difficulty changes.
4. **Act and persist:** the turn, reasoning and checkpoint are saved; the interview closes after the last question.
5. **Recover:** `resumeInterview` restores any interview from its checkpoint, even after a server restart.

## Architecture

```
Browser (Next.js)  --voice-->  Vapi assistant "Riley"
       |                              |  3 custom tools (HTTP)
       |                              v
       +------ GET /trace ------>  FastAPI  --->  Groq (openai/gpt-oss-20b)
                                      |
                                      v
                                 Neon Postgres: sessions, interview_plans, turns
```

| Route | Purpose |
| --- | --- |
| `POST /tools/generate-interview` | Vapi tool: plan and save the interview, return the first question |
| `POST /tools/submit-answer` | Vapi tool: store the answer, reason about it, return the next question or completion |
| `POST /tools/resume-interview` | Restore checkpoint, history and next question (demo via curl/Postman) |
| `GET /trace/{interview_id}` | Plan, turns, AI reasoning and scores for the timeline UI |

There is no login: the candidate is a name plus a random id kept in the browser.

## Tech stack

Next.js, Tailwind, Vapi (voice), FastAPI, Neon Postgres, Groq.

## Run locally

Backend:

```
cd backend
python -m venv .venv && .venv\Scripts\activate
pip install -r requirements.txt
copy .env.example .env   (fill in DATABASE_URL and GROQ_API_KEY)
uvicorn app.main:app --reload --port 8000
```

Frontend:

```
cd frontend
npm install
copy .env.local.example .env.local   (fill in the values)
npm run dev
```

## Disclosure of pre-existing work

The visual shell of the frontend (layout, theme, call screen and avatar assets) is reused from an earlier personal project, a Next.js and Firebase mock interview app. For this hackathon all Firebase and login code was removed, and the entire FastAPI backend, the database schema, the three Vapi tools, the agent reasoning loop, the Riley assistant prompt and the trace timeline were built during the 12-hour window.

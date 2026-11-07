<img src="assets/session.svg" width="100%" alt="Terminal session: Pavan Patel, AI systems engineer. Benchmark runs and repo listing" />

[portfolio](https://pavanpatel.vercel.app) · [linkedin](https://www.linkedin.com/in/pavan-patel-559195261/) · [x](https://x.com/pavan_pate78009) · [pavanpatela5598@gmail.com](mailto:pavanpatela5598@gmail.com)

---

I build AI systems that make real decisions, like approving a payment or flagging a scam, and
the checks that prove those decisions right. Rules decide where rules can; a model only fills the
gap, and a deterministic checker sits behind it. Every number on this page comes from a test or
an eval script in the repo it describes.

**Evals.** I wrote 22 container-isolated, digest-pinned Terminal-Bench 2 / Harbor tasks for
testing autonomous coding agents. Every verifier is checked three ways: the oracle scores 1.00, a
do-nothing agent scores 0.00, and a solution I sabotaged scores 0.00.
→ [a broken task, taken apart](https://github.com/CODER7657/fix-broken-Terminal-Bench-2-Harbor-)

**Payments.** [PRAMANA](https://github.com/CODER7657/pramana) is a policy-as-code gate between AI
shopping agents and checkout, with no LLM in any decision. On a 21-case adversarial benchmark it
cut attack success from 53.8% to 0% while refusing 0 of 8 legitimate payments, at p50 0.26 ms.
Building it, I found a constraint-enforcement gap in Google's AP2 reference SDK; Google confirmed
the mechanism, filed upstream as AP2 #339 / #340.

**Fraud.** Raksha AI catches scam messages with offline rules first, local multilingual
embeddings and pgvector retrieval second, and an LLM verdict grounded in that evidence last.
Maverick Effect AI Challenge 2026 finalist. → *private for now, happy to walk through it*

**Agents.** #205 of 4,094 builders in HackerRank Orchestrate (October 2026), with a deployed AI agent.

---

### Work

| Project | What it does | Measured |
|---|---|---|
| [pramana](https://github.com/CODER7657/pramana) · [live](https://pramana-gate.vercel.app) | Verification gate for AI-agent payments (AP2, SD-JWT, RBI e-mandate rules) | Attack success 53.8% → 0% on 21 adversarial cases, 0/8 legitimate payments refused, p50 0.26 ms; 691 tests, 94% coverage |
| raksha-ai *(private)* | Multilingual scam detection: rules, then RAG, then an LLM | 913 backend tests; labeled English/Hindi/Gujarati corpus; 16K+ threat-feed indicators |
| gtu-radar *(private, team lead)* | Telegram bot that sends each GTU student only the notices for their course, semester and branch | 8-agent pipeline on Redis Streams; 1,000+ tests; 1,346/1,346 live timetable rows parsed correctly |
| saakshi *(private, team lead)* | CCTV analytics and agentic investigation for Gujarat Police | Rebuilds vehicle routes where plates are unreadable; LangGraph crew behind OPA/Rego gates |
| [charkha](https://github.com/CODER7657/charkha) | Agent-to-Agent (A2A) mesh from NASA fire data to signed W3C credentials | 4 agents, 750 tests; built for HackOut'26 |
| [complylite](https://github.com/CODER7657/complylite) | Trade surveillance for a broker's compliance analyst | LightGBM 0.97 PR-AUC vs 0.38 for rules on a time-based holdout (simulated market); 16/20 LLM case notes pass an SQL checker after one retry |
| [genex-prj](https://github.com/CODER7657/genex-prj) | Wellness chat with crisis routing in front of the model | LLM safety judge took held-out crisis recall from 3/35 to 26/35; 30-prompt red-team suite |
| [AURA-GUARD](https://github.com/CODER7657/AURA-GUARD) · [live](https://aura-guard-live.vercel.app) | Hourly NO₂ forecasts from live EPA AirNow data at US monitors, plus a tool-only analyst agent | 33% lower 12 h error than persistence over a held-out year; 40/40 agent answers traceable to tool output |
| [voice-emotion-detection](https://github.com/CODER7657/voice-emotion-detection) | Speech emotion recognition, evaluated honestly | Found speaker leakage in my own eval; WavLM 0.77 macro-F1 on new speakers, 0.33 on a new dataset (CREMA-D); ONNX on CPU at 505 ms |
| kisan_mitra *(private)* · [live](https://kisan-mitra-gilt.vercel.app) | Gujarati/Hindi voice crop assistant for farmers | Photo check 64.9% on field photos vs 36% when trained on lab photos; 0 doses or brands in 28 red-team prompts |

### Skills

```text
  languages      Python · TypeScript · JavaScript · SQL · Java · C++ · Kotlin · Solidity
  ml / data      PyTorch · TensorFlow/Keras · scikit-learn · Hugging Face Transformers · sentence-transformers
                 Pandas · NumPy · SciPy · DuckDB · librosa · faster-whisper · ONNX Runtime
                 CNNs · NLP · text classification · feature engineering · model evaluation
  llms / agents  RAG · embeddings · pgvector · LangGraph · MCP · Agent2Agent (A2A) · Google ADK · tool calling
                 multi-agent orchestration · prompt & context engineering · guardrails · human-in-the-loop
                 OpenAI · Claude · Gemini · Groq APIs
  eval / safety  LLM and agent evaluation · Terminal-Bench 2 / Harbor task authoring · reward and verifier design
                 golden datasets · rubric writing · red-team suites · prompt-injection defense
                 reviewing AI-generated code (Claude Code · Codex · Cursor)
  backend/cloud  FastAPI · Node.js · REST · Redis Streams · Celery · PostgreSQL · Supabase · JWT/OAuth · Docker
                 GitHub Actions · pytest · Azure · Oracle Cloud · Vercel · React · Next.js · Android
```

---

<sub>B.Sc. Computer Science, BITS Pilani (2024 to 2027) · B.E. Computer Science (Data Science), GTU, New LJIET (2024 to 2028) · Ahmedabad, India</sub>

```console
$ whoami --deep

  PAVAN PATEL
  AI systems engineer · Ahmedabad, IN

  multi-agent systems  ·  guardrails & agent safety  ·  evaluation engineering

  building    raksha-ai — multilingual fraud interception, edge-first
  authored    22 container-isolated agent-benchmark tasks
  reading     two undergraduate degrees, in parallel
```

[portfolio](https://myselfpavan.vercel.app) · [linkedin](https://www.linkedin.com/in/pavan-patel-559195261/) · [x](https://x.com/pavan_pate78009) · [pavanpatela5598@gmail.com](mailto:pavanpatela5598@gmail.com)

---

Bad systems fail loudly. Mine would fail quietly — a scam gets through, a benchmark scores an
agent that did nothing, a crisis message gets a cheerful reply. So I build the thing, then I
build the thing that catches it lying.

Four rules, across everything I ship: **a rule engine that runs before any model · a human gate
in front of anything autonomous · a verifier I actively try to cheat · a number attached to every
claim.**

---

## Evals — the exams frontier agents fail

22 tasks authored. Each ships as a Harbor task: a sealed Docker image, a prompt, a digest-pinned
environment, a reference solution, and a verifier built to be un-gameable. The subject matter is
substrate. The product is a measurement you can trust.

Nothing counts until three runs agree:

```console
$ harbor run -p <task> -a oracle
  reward 1.00    4/4 tests pass          the reference must clear its own verifier

$ harbor run -p <task> -a nop
  reward 0.00    0/4 tests pass          an agent that does nothing must score nothing

$ harbor run -p <task> -a oracle --field total_requests corrupted
  reward 0.00    3/4 pass, 1 fails       field-level, not file-exists
```

That third run is the one that matters. I corrupt a single field of my own solution and re-run:
three tests still pass, and the fourth catches it. A grader that only asks *"did a file appear?"*
loses to `touch`.

**Making a task hard without making it unfair.** A task an agent fails for the wrong reason
measures nothing.

| Technique | The trap | Why it's still fair |
|---|---|---|
| Latent crux | The deciding case never appears in data the agent can see | The rule is real and determinate — the agent's own diligence betrays it |
| Wrong-default lure | An obvious cheap heuristic *almost* equivalent to the correct rule | Both derivable; they diverge only where the shortcut was always wrong |
| Misdirection | The environment ships a confident tool naming the wrong answer | Verifying your tools against ground truth is the actual job |
| Evidence-forced reversing | An undocumented convention must be recovered from observation | Every convention is forced by data the agent can read |
| Discovery hop | A final opaque step with no partial feedback | The hard part is solved before the hop; it can't be brute-forced |

The signature of a trap that landed: **every failing agent returns a byte-identical wrong answer**,
because they all reached for the same shortcut.

**Before any of it ships:** a 30-check QC gate across five families — oracle correctness, contract
determinacy, verifier rigor, environment hygiene, instruction clarity. Every check is Major. One
failure fails the task.

<details>
<summary>a sample of what those checks kill</summary>

<br />

```text
A1  oracle fails its own verifier            the reference wouldn't score full reward as shipped
A3  hardcoded answer in the reference        a constant baked in, not computed from visible input
A4  oracle relies on privileged access       reads a fixture the agent could never reconstruct
B1  ambiguous rule, no disambiguation        two readings, two different graded answers
B4  undocumented requirement enforced        the verifier enforces a threshold stated nowhere
C1  partial / stub output accepted           a report-only stub clears the bar
C2  over-permissive tolerance                a materially wrong answer lands inside the margin
D   unpinned base image                      the environment drifts; last month's score is noise
D   reference solution left in the image     the agent reads the answer instead of solving
E   reward written to the wrong path         silent zero, forever
```

</details>

---

## Raksha AI — interception at the edge

Fraud detection for first-time digital payers, in the language they actually speak.

```text
  suspicious message
        │
        ├── [1]  rule engine          on device · no network · ~200ms
        │
        ├── [2]  embed locally        multilingual · zero API cost per scan
        │
        ├── [3]  pgvector top-k       retrieve known scam patterns
        │
        ├── [4]  LLM judge            grounded on what was retrieved
        │
        └── [5]  verdict              score · flagged phrases · safe next action
```

Rules run **first**, on device, with no network. Retrieval runs **second**, so the model argues
from evidence instead of vibes. The model runs **last**, and only explains a verdict it was
handed. It never invents one.

```text
  surface      Android (Kotlin) · React 19 + TypeScript · FastAPI
  retrieval    Postgres + pgvector, embeddings computed locally
  judgement    primary model, automatic fallback, grounded on top-k patterns
  voice        call snippets transcribed on-box, then down the same path as text
  reach        12 languages, both scripts, in and out
  privacy      Row-Level Security — scan history invisible to everyone, me included
  scale        82 commits · 204 files · 4 people · 97 PRs, every one reviewed
```

<details>
<summary>the hard parts</summary>

<br />

- **Semantic caching.** The same scam arrives thousands of times. Judging it twice is money on fire — identical text resolves from cache and never reaches a model.
- **Provenance in the trace.** Every verdict records where each pattern came from. A verdict you can't trace is a rumour.
- **Silent failure is the real bug.** The app says out loud when scanning works but the warning can't reach the user.
- **Earned confidence only.** No corpus behind a verdict? It says so.
- **R8 on for release**, CI proving shrinking didn't break the build, artifacts signed.

</details>

---

## Agents, and the line they don't cross

Seven agents across one system. Not one sits between a user and a verdict they act on in seconds.

```text
  HOT PATH                             │   AGENT ZONE
  deterministic · ~200ms · offline     │   open-ended · reviewable · killable
                                       │
  message → rules → verdict            │   A1 discovery     A2 phishing
  pattern corpus, human-approved       │   A3 correlator    A4 triage
                                       │   A5 analyst       A6 red team
  no agent. no LLM loop.               │   A7 localiser
  no network dependency.               │
                                       │   ─── human review gate ───
        ▲                              │              │
        └──── the only door ───────────┴──────────────┘
              accepted drafts only
```

> Agents go where the work is open-ended and a human can review it. Never in the hot path.
> Speed, determinism and auditability aren't tradeable there.

- **Typed contracts, not conversation.** Finder → judge → curator → human. Every handoff is a schema. Free-form agent chatter is the bug, not the feature.
- **Policy outside the model.** For the agent that opens hostile URLs, a second model never sees page text. Blocking is tiered — never one signal, never the page's opinion of itself.
- **Prompt injection is assumed.** A page instructing the agent that reads it is expected input, not an incident.
- **Every loop has a stop condition, a budget, a kill switch.** Over budget stops the run. It never degrades quietly into something cheaper and wrong.
- **Drafts only.** Nothing an agent writes reaches a user until a person accepts it.
- **A2A belongs at the institution boundary.** Inside one system it's discovery, negotiation and a wire protocol I don't need — all attack surface. Between organisations, it's the right answer. Not before.

**Orchestration — the red-team loop.** Four roles, one job each, because a single "be creative"
prompt produces one kind of output and calls it diversity:

| Role | Job | Why it's separate |
|---|---|---|
| Generator | one seed scam + **one** mutation lens → N variants | Diversity has to be structural, not requested |
| Detector | variant → verdict from the **production** path | A copy of the pipeline measures a system we don't ship |
| Preservation judge | is this still the same fraud? | Load-bearing. A variant that stopped asking for an OTP isn't an evasion — it's a different message |
| Curator | dedup, cluster, write corpus rows | Plain code. Not everything needs to be an agent |

Lenses are named, not improvised: transliteration, code-mixing, sentence splitting, politeness
shift, homoglyph obfuscation, paraphrase, length padding. The loop stops after **K=2 consecutive
rounds producing nothing new**, deduping against everything ever *seen* — not everything
*accepted*, or rejected variants reappear forever and it never converges.

The same instinct outside this system: in a mental-wellness assistant, crisis detection sits in
front of the chat model, not inside it. A model deciding on its own whether someone is in danger
is a design I won't ship.

---

## Repos worth your time

```console
$ ls ~/work --sort=substance

  82 commits   raksha-ai       fraud interception · Kotlin + React 19 + FastAPI + pgvector
  35 commits   complylite      compliance surveillance · YAML rule packs + ML, CI, tested detectors
  18 commits   VortiFi         Solidity voting contract + React DApp · no trusted tallier
  17 commits   CRAFTATHON      POCSO Shield · FastAPI + Celery + on-chain registry + IPFS evidence
  10 commits   genex-prj       wellness chat backend · crisis detection in front of the model
   4 commits   tradex          trading platform · FastAPI over a Rust execution engine
```

| | |
|---|---|
| [raksha-ai](https://github.com/CODER7657/raksha-ai) | Multilingual fraud interception — agent layer, eval harness, provenance trace |
| [complylite](https://github.com/CODER7657/complylite) | Compliance surveillance for brokers. Explainability *is* the product |
| [CRAFTATHON](https://github.com/CODER7657/CRAFTATHON) | Anonymous child-safety reporting — anonymity and accountability in one system |
| [VortiFi](https://github.com/CODER7657/VortiFi) | Verifiable on-chain elections |
| [genex-prj](https://github.com/CODER7657/genex-prj) | Crisis detection ahead of the chat model, session persistence, security middleware |
| [tradex](https://github.com/CODER7657/tradex) | FastAPI service layer over a Rust engine, Dockerised |
| [Harbor task repair](https://github.com/CODER7657/fix-broken-Terminal-Bench-2-Harbor-) | Leaked solution, gameable verifier, wrong reward path — taken apart and made to measure something |

---

## Stack

```text
  languages     Python · TypeScript · JavaScript · Rust · Kotlin · SQL · Solidity
  backend       FastAPI · Node.js · Celery · Redis · PostgreSQL · pgvector · Supabase
  frontend      React 19 · Tailwind · Vite · Android (Kotlin)
  ai            RAG over pgvector · local embeddings · Groq · Gemini · on-box transcription
  evals         Harbor · Docker · pytest · NumPy · SciPy
  ops           GitHub Actions · Docker Compose · R8 · signed release builds
```

---

## My commit log is the spec

```text
Stop claiming protection across a window where there was none
Say when a verdict had no corpus behind it
Stop the test suite reading the developer's real credentials
Stop the corpus README claiming nine languages have no rules
Make it visible when scanning works but warnings cannot reach the user
Stop paying to judge the same scam a thousand times
Stop agents from spending the quota a scan needs
Make the verdict trace a specification, before a third engine implements it
```

A large share of my merged work deletes a lie the codebase was telling. Not pessimism — the only
way a safety product earns the word.

---

## Also

Two undergraduate degrees, in parallel — B.E. Computer Engineering, and a B.S. in Computer
Science at BITS Pilani. Two curricula, one operating system, no extensions requested.

```console
$ contact

  mail       pavanpatela5598@gmail.com
  linkedin   /in/pavan-patel-559195261
  x          @pavan_pate78009
  web        myselfpavan.vercel.app

  building something that has to be right the first time? open an issue anywhere, or write.
```

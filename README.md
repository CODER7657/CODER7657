<img src="assets/hero.svg" width="100%" alt="Pavan Patel — I build AI systems that must not be wrong, and the evals that prove when they are" />

<p align="center">
  <a href="https://myselfpavan.vercel.app"><img src="https://img.shields.io/badge/portfolio-myselfpavan-0b1119?style=flat-square&labelColor=0b1119&color=4dd6ff" alt="Portfolio" /></a>
  <a href="https://www.linkedin.com/in/pavan-patel-559195261/"><img src="https://img.shields.io/badge/linkedin-connect-0b1119?style=flat-square&labelColor=0b1119&color=3ee08f" alt="LinkedIn" /></a>
  <a href="https://x.com/pavan_pate78009"><img src="https://img.shields.io/badge/x-@pavan__pate78009-0b1119?style=flat-square&labelColor=0b1119&color=8aa0b4" alt="X" /></a>
  <a href="mailto:pavanpatela5598@gmail.com"><img src="https://img.shields.io/badge/mail-open-0b1119?style=flat-square&labelColor=0b1119&color=ffc857" alt="Email" /></a>
</p>

> Bad systems fail loudly. Mine would fail **quietly** — a scam gets through, a benchmark scores
> an agent that did nothing, a crisis message gets a cheerful reply.
> So I build the thing. Then I build the thing that catches it lying.

**Four things, across everything I ship:** a rule engine that runs before any model · a human gate
in front of anything autonomous · a verifier I actively try to cheat · and a number attached to
every claim.

---

## 01 · Interception at the edge

Fraud detection for first-time digital payers, in the language they actually speak.

<img src="assets/pipeline.svg" width="100%" alt="Interception pipeline: rules first, retrieval second, model last" />

Rules run **first** — on device, no network. Retrieval runs **second**, so the model argues from
evidence, not vibes. The model runs **last**, and only explains a verdict it was handed. It never
invents one.

| | |
|---|---|
| **Surface** | Android (Kotlin) · React 19 + TypeScript · FastAPI |
| **Retrieval** | Postgres + `pgvector`, embeddings computed locally — zero API cost per scan |
| **Judgement** | Primary model, automatic fallback, grounded on top-k retrieved patterns |
| **Voice** | Call snippets transcribed on-box, then down the same path as text |
| **Reach** | 12 languages, both scripts, in and out |
| **Privacy** | Row-Level Security — scan history is invisible to everyone, me included |
| **Scale** | 82 commits · 204 files · 4 people · 97 PRs, every one reviewed |

<details>
<summary><b>The hard parts</b></summary>

<br />

- **Semantic caching.** The same scam hits thousands of times. Judging it twice is money on fire — identical text resolves from cache and never reaches a model.
- **Provenance in the trace.** Every verdict records where each pattern came from. A verdict you can't trace is a rumour.
- **Silent failure is the real bug.** The app now says out loud when scanning works but the warning can't reach the user.
- **Earned confidence only.** No corpus behind a verdict? It says so.
- **R8 on for release**, CI proving shrinking didn't break it, artifacts signed.

</details>

---

## 02 · Agents, and the line they don't cross

Seven agents across one system. **Not one sits between a user and a verdict they act on in seconds.**

<img src="assets/agents.svg" width="100%" alt="Seven agents behind a hard boundary, a human review gate, and a rogue action blocked at the line" />

> **The rule:** agents go where the work is open-ended and a human can review it.
> Never in the hot path. Speed, determinism and auditability aren't tradeable there.

- **Typed contracts, not conversation.** Finder → judge → curator → human. Every handoff is a schema. Free-form agent chatter is the bug, not the feature.
- **Policy outside the model.** For the agent that opens hostile URLs, a second model never sees page text. Blocking is tiered — never one signal, never the page's opinion of itself.
- **Prompt injection is assumed.** A page instructing the agent that reads it is expected input, not an incident.
- **Every loop has a stop condition, a budget, a kill switch.** Over budget stops the run. It never degrades quietly into something cheaper and wrong.
- **Drafts only.** Nothing an agent writes reaches a user until a person accepts it. The gate is the design, not a crutch.
- **One agent attacks us on purpose.** A red-team loop mutates known scams until they slip through. Every escape becomes a benchmark case with a lifecycle — fixed ones close.
- **A2A belongs at the institution boundary.** Inside one system it's discovery, negotiation and a wire protocol I don't need — all attack surface. Between organisations, it's the right answer. Not before.

### Orchestration, concretely

The red-team loop, four roles, one job each — because a single "be creative" prompt produces one
kind of output and calls it diversity:

| Role | Job | Why it's separate |
|---|---|---|
| **Generator** | one seed scam + **one** mutation lens → N variants | Diversity has to be structural, not requested |
| **Detector** | variant → verdict from the **production** path | A copy of the pipeline measures a system we don't ship |
| **Preservation judge** | is this still the same fraud? | Load-bearing. A variant that stopped asking for an OTP isn't an evasion — it's a different message |
| **Curator** | dedup, cluster, write corpus rows | Plain code. Not everything needs to be an agent |

The lenses are named, not improvised: transliteration, code-mixing, sentence splitting,
politeness shift, homoglyph obfuscation, paraphrase, length padding.

**Loop control:** stop after **K=2 consecutive rounds producing nothing new** — deduping against
everything ever *seen*, not everything *accepted*, or rejected variants reappear forever and the
loop never converges. Hard budget cap per run. Nightly and pre-release. Never in the request path.

The same instinct outside this system: in a mental-wellness assistant, **crisis detection sits in
front of the chat model, not inside it.** A model deciding on its own whether someone is in
danger is a design I won't ship.

---

## 03 · Evals: I write the exams frontier agents fail

**22 tasks authored.** Each one ships as a Harbor task: a sealed Docker image, a prompt, a
digest-pinned environment, a reference solution, and a verifier built to be un-gameable. The
subject matter is just substrate — the product is a measurement you can trust.

Every task is proven against three runs before it counts:

<img src="assets/verifier.svg" width="100%" alt="Oracle scores 1.00, an agent that does nothing scores 0.00, and a solution sabotaged on purpose also scores 0.00" />

`▸` **oracle → 1.00.** The reference solution must clear its own verifier. If it can't, the task is
broken, not hard.
`▸` **nop agent → 0.00.** An agent that does nothing must score nothing. This is the check that
catches graders satisfied by an empty file.
`▸` **sabotaged → 0.00.** I corrupt one field of my own solution and re-run. Three tests still pass;
**the fourth catches it.** Field-level, not file-exists.

### Making a task hard without making it unfair

A task an agent fails for the wrong reason measures nothing. The techniques I build with, and the
line each one respects:

| Technique | The trap | Why it's still fair |
|---|---|---|
| **Latent crux** | The deciding case never appears in the data the agent can see | The rule is real and determinate — the agent's own diligence is what betrays it |
| **Wrong-default lure** | An obvious cheap heuristic that is *almost* equivalent to the correct rule | Both are derivable; they diverge only where the shortcut was always wrong |
| **Misdirection** | The environment ships a confident tool that names the wrong answer | Verifying your tools against ground truth is the actual job |
| **Evidence-forced reverse engineering** | An undocumented convention must be recovered from observation alone | Every convention is forced by data the agent can read |
| **Discovery hop** | A final opaque step with no partial feedback | The hard part is solved before the hop; the hop can't be brute-forced |

The failure signature that tells you a trap landed: **every failing agent returns a byte-identical
wrong answer**, because they all reached for the same shortcut.

### The gate before any of it ships

A **30-check QC pass** across five families — oracle correctness, contract determinacy, verifier
rigor, environment hygiene, instruction clarity. Every check is Major: one failure fails the task.

<details>
<summary><b>A sample of what those checks kill</b></summary>

<br />

| Check | What it catches |
|---|---|
| Oracle fails its own verifier | The reference wouldn't score full reward as shipped |
| Hardcoded answer in the reference | A constant baked in rather than computed from visible inputs |
| Oracle relies on privileged access | It reads a fixture the agent could never reconstruct |
| Ambiguous rule, no disambiguation | Two reasonable readings, two different graded answers |
| Undocumented requirement enforced | The verifier enforces a threshold stated nowhere |
| Partial / stub output accepted | A report-only stub clears the bar without doing the work |
| Over-permissive tolerance | A materially wrong answer lands inside the margin |
| Unpinned base image | The environment drifts; last month's score means nothing |
| Reference solution left in the agent's image | The agent reads the answer instead of solving |
| Reward written to the wrong path | Silent zero, forever |

</details>

Same rule on the product side: *no accuracy number* is worse than a bad one — so the eval corpus
is held out of the retrieval store, scored per language, and run in CI. A system graded on its own
notes is fiction.

---

## 04 · The rest of the map

Same discipline, different domains. Every one of these is code I wrote, not a template I forked.

**Safety-critical systems**

| | |
|---|---|
| **[Raksha AI](https://github.com/CODER7657/raksha-ai)** | Multilingual fraud interception — Android + web + RAG backend, agent layer, provenance trace |
| **[POCSO Shield](https://github.com/CODER7657/CRAFTATHON)** | Anonymous child-safety reporting: layered FastAPI, Celery pipelines, on-chain registry, IPFS evidence. Anonymity and accountability in one system |
| **[Mental-wellness assistant](https://github.com/CODER7657/genex-prj)** | Chat backend with crisis detection in front of the model, session persistence, security middleware |

**Money, rules and audit trails**

| | |
|---|---|
| **[ComplyLite](https://github.com/CODER7657/complylite)** | Compliance surveillance for brokers — YAML rule packs + ML, tested detectors, CI, audit-ready output. Explainability *is* the product |
| **[TradeX](https://github.com/CODER7657/tradex)** | Trading platform: FastAPI service layer over a **Rust** execution engine, Dockerised |

**On-chain**

| | |
|---|---|
| **[VortiFi](https://github.com/CODER7657/VortiFi)** | Solidity voting contract + React DApp — verifiable elections, no trusted tallier |
| **[VeriServe](https://github.com/CODER7657/VeriServe_block)** | Certificate issuance and verification on Polygon, wallet-authenticated |

**ML, closer to the metal**

| | |
|---|---|
| **[Voice emotion detection](https://github.com/CODER7657/voice-emotion-detection)** | CNN over RAVDESS, real-time classification from live audio, with a test harness |
| **[Harbor task repair](https://github.com/CODER7657/fix-broken-Terminal-Bench-2-Harbor-)** | Taking a broken benchmark task apart — leaked solution, gameable verifier, wrong reward path — and making it measure something (§03) |

---

## 05 · Stack, live

<table>
<tr>
<td width="46%" valign="top">

<img src="assets/radar.svg" width="100%" alt="Radar sweeping the stack in production" />

</td>
<td valign="top">

**How I work**

`▸` **Evidence or it doesn't ship.** A claim with no number is a hope.

`▸` **Offline first.** The device does the urgent part. Network is an upgrade, not a dependency.

`▸` **Deterministic where it counts.** Same input, same verdict — or it isn't a security product.

`▸` **Fail visibly.** Silent degradation is the most expensive bug class there is.

`▸` **Boring where it's dangerous.** Rules and schemas in the hot path. Creativity behind a gate.

`▸` **Delete the claim before you defend it.** If a window wasn't protected, the copy says so.

</td>
</tr>
</table>

---

## 06 · My commit log is the spec

Real titles, real merges. This is the whole personality:

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

## 07 · Also

**Two undergraduate degrees, in parallel** — B.E. Computer Engineering, and a B.S. in Computer
Science at BITS Pilani. Two curricula, one operating system, no extensions requested.

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=CODER7657&show_icons=true&hide_border=true&hide_title=true&bg_color=0b1119&text_color=8aa0b4&icon_color=4dd6ff&ring_color=3ee08f&title_color=e6f2fb" height="150" alt="GitHub stats" />
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=CODER7657&layout=compact&hide_border=true&hide_title=true&bg_color=0b1119&text_color=8aa0b4&title_color=e6f2fb&langs_count=8" height="150" alt="Top languages" />
</p>

<p align="center">
  <b>Building something that has to be right the first time?</b><br />
  <a href="mailto:pavanpatela5598@gmail.com">pavanpatela5598@gmail.com</a> ·
  <a href="https://www.linkedin.com/in/pavan-patel-559195261/">LinkedIn</a> ·
  <a href="https://x.com/pavan_pate78009">X</a>
</p>

<p align="center"><sub>Every diagram above is hand-written SVG in <code>/assets</code>. No badge generators, no templates. Same as the rest of the work.</sub></p>

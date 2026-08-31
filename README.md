# Maxed-out Sub-agents

<p align="center">
  <img src="https://img.shields.io/badge/release-v1.0.0-blue?style=for-the-badge" alt="Release v1.0.0">
  <img src="https://img.shields.io/badge/core_agents-7-green?style=for-the-badge" alt="7 core agents">
  <img src="https://img.shields.io/badge/specialists-14-purple?style=for-the-badge" alt="14 specialists">
  <img src="https://img.shields.io/badge/python-3.10+-yellow?style=for-the-badge&amp;logo=python&amp;logoColor=white" alt="Python 3.10+">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-green?style=for-the-badge" alt="MIT license"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/CLI-v1.0.0-blue?style=flat-square" alt="CLI v1.0.0">
  <img src="https://img.shields.io/badge/quality_layers-5-purple?style=flat-square" alt="5 quality layers">
  <img src="https://img.shields.io/badge/monitor-always_on-orange?style=flat-square" alt="Monitor always on">
</p>

An AI skill that turns one LLM into a supervised crew of specialists — for any editor, any model, any project.

<p align="center">
  <img src="![workflow](https://github.com/user-attachments/assets/417cd734-5623-403f-842a-112aa646309c)" alt="Maxed-out Sub-agents workflow" width="800">
</p>

Maxed-out Sub-agents is a portable **sub-agent skill**. Pull it into any session, any project, any LLM. It does **not** run as a single generalist. It decomposes the work, assigns **specialist sub-agents**, forces them to work as a crew, and keeps a **Monitor** on the floor for the entire run.

> Works in Claude, ChatGPT, Cursor, Codex, Continue, Aider, Open WebUI, Ollama, LangGraph, CrewAI, AutoGen, or a raw HTTP call to any OpenAI-compatible endpoint.

---

## Why this exists

A single LLM asked to “be everything” overthinks, drifts, and hallucinates. Industry crews that actually ship do not work that way.

NEXUS encodes a production pattern used in serious agentic systems:

1. **Decompose** the task into sections (not vibes).
2. **Route** each section to a Grade-A specialist.
3. **Execute** in parallel where safe, sequential where dependent.
4. **Communicate** on a shared blackboard with structured messages.
5. **Monitor** continuously — not a retrospective rubber stamp.
6. **Gate** every artifact through 5 layers before it reaches the user.
7. **Stop** when done. No rumination loops. No invented facts.

---

## 60-second start (any LLM, no install)

Copy [`SKILL.md`](./SKILL.md) into the session (system prompt, project rule, Cursor rule, or first message).

Then say what you want:

```
NEXUS: Build a rate-limited public API for URL shortening with tests and a threat model.
```

or simply:

```
Use NEXUS. <your task>
```

The skill takes over: breakdown → specialists → Monitor → 5-layer gate → Triple-A deliverable.

---

## Install (runtime mode, optional)

Python 3.10+.

```bash
pip install -e .
cp .env.example .env   # set NEXUS_API_KEY + NEXUS_BASE_URL + NEXUS_MODEL
nexus run "Add dark mode to the settings page with tests"
```

Any OpenAI-compatible provider:

```bash
# OpenAI
NEXUS_BASE_URL=https://api.openai.com/v1 NEXUS_MODEL=gpt-4.1 nexus run "..."

# Groq
NEXUS_BASE_URL=https://api.groq.com/openai/v1 NEXUS_MODEL=llama-3.3-70b-versatile nexus run "..."

# Ollama local
NEXUS_BASE_URL=http://localhost:11434/v1 NEXUS_MODEL=qwen2.5-coder:32b nexus run "..."

# Azure / vLLM / Together / Fireworks / OpenRouter — same interface
```

---

## Pull into a project (skill mode)

| Tool | How |
|---|---|
| **Claude / Claude Code** | Copy `SKILL.md` into project instructions or a Claude Skill |
| **Cursor** | `SKILL.md` + `.cursorrules` + `AGENTS.md` at repo root |
| **ChatGPT** | Paste `SKILL.md` as a Custom GPT instruction or first message |
| **Continue / Aider / Cline** | Point system prompt at `SKILL.md` |
| **Any app** | Send `SKILL.md` as `system` on every run |
| **This runtime** | `nexus run` |

You do **not** need this codebase in the target app. `SKILL.md` is the skill. The rest of the repo is the spec, the crew, and an optional executor.

---

## Crew

### Always on (every run)

| ID | Name | Job |
|---|---|---|
| `ORCH` | Orchestrator | Decompose, assign, schedule, synthesize plan |
| `MON` | Monitor | Watch every agent. Veto drift, hallucination, overthink, scope break |
| `REQ` | Requirements | Lock the real ask, constraints, definition of done |
| `RTR` | Router | Match sections → specialist skill graphs |
| `SYN` | Synthesizer | Merge specialist work into one coherent artifact |
| `QGT` | Quality Gate | Run the 5 layers. Fail closed. |
| `ANC` | Anchor | Grounding / anti-hallucination. Nothing invented leaves the building. |

### Specialists (spawned only when the section needs them)

Architect · Implementer · Researcher · Writer · Reviewer · QA · Security · DevOps · Data · Designer · Debugger · Planner · API · Domain Expert

A specialist who is not needed is **not spawned**. Idle agents do not talk.

---

## Five layers (nothing ships without all five)

| Layer | Name | Kills |
|---|---|---|
| L1 | Intent alignment | Wrong problem, extra scope, missed constraint |
| L2 | Correctness | Bugs, false claims, broken logic, invalid APIs |
| L3 | Completeness | Missing tests, missing edge cases, missing docs the user asked for |
| L4 | Excellence | Sloppy structure, weak naming, half-explanations, “it works on my machine” |
| L5 | Grounding | Hallucination, fabricated citations, fake files, fake numbers, silent guesses |

Monitor signs L5. If any layer fails: send back to the owning specialist with a precise defect list. Max 2 repair cycles, then escalate to Orchestrator with options — never infinite loops.

---

## Quality contract

- **Triple-A output** means: correct, complete, and independently reviewable.
- **No hallucination:** unknown → say unknown. Never invent APIs, paths, papers, metrics, or “files I assumed exist.”
- **No overthinking:** 80%+ confidence + DoD met → ship. Clarifying questions: max 2, and only if blocked.
- **Teamwork:** specialists read the blackboard; they do not duplicate work; they hand off with contracts.
- **Anywhere:** skill is model-agnostic and app-agnostic.

Full protocols: [`protocols/`](./protocols/).

---

## Example

**User:** `NEXUS: add pagination to GET /items`

**What actually happens**

```
REQ     → locks DoD: cursor pagination, stable sort, tests, OpenAPI delta
ORCH    → sections: API contract, implementation, tests, docs
RTR     → API + Implementer + QA + Reviewer
MON     → watches scope (no rewrite of the whole router)
API     → contract + examples
IMP     → code against the contract
QA      → tests including empty page / last page / invalid cursor
REV     → review comments, must-fix vs nit
QGT     → L1–L5
ANC     → no invented framework helpers
SYN     → one patch, one test file, one short changelog
MON     → SIGNED
```

You receive **one** Triple-A deliverable, not a committee transcript — unless you ask for the trace.

---

## Configuration

See [`nexus.yaml`](./nexus.yaml). Tune agent temperature, token budgets, stop conditions, and which specialists are allowed.

---

## Docs

- [Architecture](./docs/architecture.md)
- [Getting started](./docs/getting-started.md)
- [Using with any LLM](./docs/using-with-any-llm.md)
- [Agent catalog](./docs/agent-catalog.md)

---

## License

MIT. Use it in commercial products. Attribution appreciated, not required.

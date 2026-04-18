# DIY Home Loan — Agent Doctrine (Karpathy Method)

> **Project:** FITTER Financially / DIY Home Loan Member platform
> **Owner:** Duncan Perkins
> **Locale:** en-AU | DD/MM/YYYY | AUD | AEST/AEDT
> **Stack:** Replit + Next.js 15 (TS) + Supabase (Sydney) + Anthropic Claude

---

## Why this doctrine exists

Andrej Karpathy's public agent work shows two patterns that beat complex orchestration:

1. **Autoresearch minimalism** — one agent, one tiny instruction file, short measurable loops. *"Only three files that matter."*
2. **LLM Council** — for high-stakes decisions, many voices answer independently → anonymous peer review → a Chairman synthesises.

This doctrine combines both. Minimal by default. Council only when the decision warrants it.

---

## The core rules

1. **Every agent has one job.** If an agent does two things, split it.
2. **Every agent has a success metric.** If you can't measure it, you can't trust it.
3. **Short loops beat long plans.** Pick a scope that ships in hours, not weeks.
4. **Verification before "done".** No "should work" claims. Show the evidence.
5. **Escalate, don't guess.** Ambiguous task → halt and ask the orchestrator.
6. **en-AU always.** Colour, behaviour, optimisation, licence (noun). DD/MM/YYYY. AUD.
7. **Privacy is a hard stop**, never a soft preference. TFN rules, APP 3/6/11, Sydney data residency.

---

## The team

### Tier 0 — Captain
- **Duncan Perkins** (human). Owns all decisions. No agent ships without his nod.

### Tier 1 — Leadership
- **Senior Project Manager** (`agents/01-senior-pm.md`) — Michelle Zhao, 18 yrs fintech delivery.
- **Senior Orchestrator** (`agents/02-senior-orchestrator.md`) — Tom Bradley, 17 yrs platform architecture. Routes work, convenes the Council when stakes are high.

### Tier 2 — Specialists (each is the 15+ yr authority in their lane)
- **Architect (Next.js/Replit/Supabase)** — Raj Patel
- **Privacy & Compliance (AU)** — Helen Nguyen
- **Data Engineer (Postgres/RLS)** — Marcus O'Sullivan
- **AI Agent Engineer** — Priya Ramesh
- **Application Security** — Daniel Kowalski
- **Finance SME (AU home loan, broker, AFSL-aware)** — Peter Horvath
- **Brand & UX** — Emma Lindqvist
- **QA & Verification** — Chen Wei

### Tier 3 — Sub-agents (narrow, experienced, tool-like)
See `agents/sub-agents/`. Each does exactly one thing — TFN detection, RLS review, migration writing, broker-flow audit, en-AU copy edit, disclaimer check, prompt-eval loop, deploy gate.

### Skills
See `skills/`. Reusable patterns (LLM Council protocol, autoresearch loop, TFN gate, RLS template, broker invite, Fitzy prompt, verification, disclaimer footer).

### Rules
See `rules/`. Hard stops. `core.md`, `privacy-hard-stops.md`, `brand-guardrails.md`.

---

## How work flows

```
Duncan drops an idea  (via /idea or plain message)
      ↓
Michelle runs intake (skills/idea-intake.md):
  1. Restates the idea as an outcome
  2. Asks 3–5 clarifying questions — each pre-filled with her
     recommendation + a reason. Duncan replies ok / change to X /
     or writes his own.
  3. Produces a full scope card
  4. Approval gate: go / edit / hold / drop / council
      ↓
On "go" — card saved to .claude/scope/ and handed to Tom
      ↓
Tom (Orchestrator) routes:
  - Low risk, clear path    → single specialist + sub-agents
  - High risk or ambiguous  → convene LLM Council (skills/llm-council.md)
      ↓
Specialist does the work, calls sub-agents as tools
      ↓
Chen (QA) runs the verification gate
      ↓
Report to Duncan with evidence (not claims)
```

Duncan's command for the front door: **`/idea <what you're thinking>`** — or just type it in plain English; Michelle picks up intent phrases automatically.

---

## When to convene the Council

The LLM Council is **expensive in time**. Use it only when at least two of:

- The decision is hard to reverse (schema migration, broker integration, legal copy).
- A wrong call costs money or member trust.
- Specialists disagree.
- Duncan has said "I'm not sure".

Never convene for: typos, renames, copy tweaks, layout nudges, dev config.

See `skills/llm-council.md` for the protocol.

---

## When to run an autoresearch loop

Use the autoresearch pattern (`skills/autoresearch-loop.md`) when:

- You have a **clear numeric signal** (e.g. Fitzy passes 8/10 test questions, quiz completion time, Lighthouse score).
- You can **iterate in <10 min cycles**.
- The improvement is worth chasing overnight.

Without a metric, it's not autoresearch — it's vibes.

---

## Context discipline

- Read files by range, not in full. Use `Grep` and `Glob` first.
- Each specialist loads **only its lane** — architect doesn't load legal docs.
- Sub-agents load nothing by default; the parent hands them exactly what they need.
- Escalate up, not sideways. Specialists don't call each other — the Orchestrator routes.

---

## Never list

- Never store a TFN.
- Never promise financial advice (it's general education only).
- Never ship without the "not financial advice" footer on AI output.
- Never run Supabase queries without RLS enabled.
- Never commit `.env` or Supabase service role key.
- Never skip the verification gate to look fast.
- Never invent a fact. Mark it `Assumed` and verify.

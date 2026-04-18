# `.claude/` — How Duncan's agent team works

A small, opinionated agent pack for the DIY Home Loan project on Replit.

## Start here

1. Read `FIRST_RUN.md` — day-one paste-and-play guide (bootstrap prompt + first idea).
2. Read `CLAUDE.md` — the doctrine.
3. Read `agents/01-senior-pm.md` and `agents/02-senior-orchestrator.md` — these two run the show.
4. When you (or any AI collaborator) start a task, state it to the **Senior PM** first. Never start coding before Michelle has scoped it.

## Files at a glance

| Path | Count | Purpose |
|---|---|---|
| `CLAUDE.md` | 1 | Doctrine. Loaded every session. |
| `agents/01-senior-pm.md` | 1 | Michelle — scopes, prioritises, checks done-definitions |
| `agents/02-senior-orchestrator.md` | 1 | Tom — routes work, convenes the Council |
| `agents/specialists/` | 8 | 15+ yr specialists — one per discipline |
| `agents/sub-agents/` | 8 | Narrow tool-like agents — one job each |
| `skills/` | 8 | Reusable patterns (Council, autoresearch, TFN, RLS, etc.) |
| `rules/` | 3 | Hard stops (en-AU, privacy, brand) |

## The Karpathy method in 3 lines

- **Minimal.** One file per agent, each as short as it can be.
- **Measurable.** Every agent has a success metric.
- **Loop-based.** Ship in short cycles. Verify. Iterate.

## Using this with Replit AI or Claude Code

Point Claude Code at the project root and it will auto-load `CLAUDE.md` and the rules. For Replit AI, paste the relevant agent file into the chat as the system prompt when you need that specialist.

## A note on names

The specialists have names and backgrounds. Not for theatre — for **consistency**. When Priya (AI Agent Engineer) reviews an LLM call, Duncan knows he'll get the same angle every time. Same with Helen on privacy, Peter on finance. Stable persona = stable judgment.

## What this pack deliberately doesn't include

- No Genesis/Hive Mind layers (overhead for a Replit project of this size)
- No hooks, no pre/post-compact scripts
- No memory files that require maintenance
- No LangGraph, no FastAPI, no Docker

All of those can be added later if the project grows. Karpathy's rule applies: *"only three files that matter."* Start here, grow only when you feel the pain.

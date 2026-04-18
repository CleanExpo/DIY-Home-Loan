---
name: idea
description: Duncan's front door. Drop an idea in and Michelle (Senior PM) runs the intake protocol — restate, clarifying questions with recommendations, scope card, approval.
---

# /idea — Front-door intake

Trigger Michelle's intake protocol for a new idea, bug, feature, or "can we just…" question.

## Usage

```
/idea <one-line description of what you're thinking>
```

Or just type your idea without the slash — Michelle will recognise the intent phrases ("I want to", "can we add", "what if", "could we", "how about") and run intake automatically.

## What happens

Michelle (`agents/01-senior-pm.md`) runs the protocol in `skills/idea-intake.md`:

1. **Restate** — one sentence, outcome-framed. You confirm or adjust.
2. **Clarifying questions** — 3–5 of them, each pre-filled with her recommendation and a short reason. You reply `ok` / `change to X` / or write your own answer.
3. **Scope card** — she produces the full card with owner, sub-agents, risk, Council-yes-or-no, and the proof required at the end.
4. **Approval gate** — you reply `go`, `edit <field>`, `hold`, `drop`, or `council`.

## Where it goes

On `go`, the scope card is saved to:

```
.claude/scope/<yyyy-mm-dd>-<slug>.md
```

And Tom (Senior Orchestrator) picks it up from there.

## On `hold`

Saved to `.claude/scope/parked/` — Duncan can revive it later.

## On `drop`

Not saved. Michelle won't revisit it unless you raise it again.

## On `council`

Tom convenes the LLM Council per `skills/llm-council.md` before any work starts. Used only when the idea is hard-to-reverse, regulated, or Duncan wants multiple senior angles on it.

## Examples of good ideas to drop here

- "Members should be able to share their quarterly summary with their accountant."
- "I want to add a Rule 19 quiz with progress tracking."
- "Can Fitzy explain why Rule 3 must come before Rule 14?"
- "Add a member self-serve delete-my-data button."
- "What if brokers could see all their clients in one list?"

Each of these gets a different size, a different owner, a different risk rating. Michelle's job is to figure that out with 3–5 well-chosen questions.

## Examples of things this command is NOT for

- "Fix the typo on the homepage" → just tell Chen (QA) and Emma (brand).
- "Run the deploy gate" → invoke `replit-deploy-gate` directly.
- "What does Rule 7 mean?" → ask Peter (Finance SME).

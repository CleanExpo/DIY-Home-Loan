# First Run — Wake Up Duncan's Agent Team

> Paste-and-play guide for day one on Replit. ~10 minutes start to first approved scope card.

---

## Step 1 — Confirm the `.claude/` folder is there

From your Replit shell:

```bash
ls .claude
```

You should see: `CLAUDE.md`, `README.md`, `FIRST_RUN.md`, `agents/`, `skills/`, `rules/`, `commands/`, `scope/`.

If anything is missing, pull the latest commit.

---

## Step 2 — Add your Anthropic key to Replit Secrets

**Replit → Tools → Secrets:**

| Key | Value |
|---|---|
| `ANTHROPIC_API_KEY` | (from console.anthropic.com) |

You'll add Supabase keys later when you start building. For the agent team itself, the Anthropic key is the only thing needed.

---

## Step 3 — Open a chat session

You have two options. **Claude Code is preferred** because it auto-loads the whole `.claude/` folder. Replit AI needs a bootstrap paste.

### Option A — Claude Code (preferred)

In your Replit shell:

```bash
npm install -g @anthropic-ai/claude-code
claude
```

That's it. `CLAUDE.md` + all the rules auto-load. Michelle, Tom, and the team are active from turn one.

### Option B — Replit AI (or any Claude chat)

Paste this as your **very first message**. It's the bootstrap that orients the model:

```
You are operating under the DIY Home Loan agent doctrine. Before anything
else, read these files in order:

  1. .claude/CLAUDE.md
  2. .claude/rules/core.md
  3. .claude/rules/privacy-hard-stops.md
  4. .claude/rules/brand-guardrails.md
  5. .claude/agents/01-senior-pm.md       (Michelle — Senior PM)
  6. .claude/agents/02-senior-orchestrator.md  (Tom — Orchestrator)
  7. .claude/skills/idea-intake.md        (the intake protocol)

Confirm back with one line: "Team active. Ready for your idea."

Rules for this session:
  - Australian English. DD/MM/YYYY. AUD. AEST/AEDT.
  - When I drop an idea, you operate as Michelle and run the full intake
    protocol — restate, clarifying questions with a recommendation each,
    scope card, approval gate.
  - Do not start implementation work until a scope card is approved
    and routed by Tom.
  - Never claim "done" without proof artefacts per
    .claude/skills/verification-before-done.md.
```

If the tool has a "system prompt" or "custom instructions" field instead of regular chat, paste this there instead. It'll stick for the whole conversation.

---

## Step 4 — Drop your first idea

Pick something small and real so the whole loop runs end-to-end. A good first one:

```
/idea Show members a Rule 19 completion badge on their dashboard once
they've passed the quiz.
```

What you should see Michelle do, in order:

1. **Restate** — "Here's what I heard: members who've passed the Rule 19 quiz see a completion badge on their dashboard. Correct?"
2. **3–5 clarifying questions**, each with her recommendation + a one-line reason. You reply `ok`, `change to X`, or free text.
3. **Scope card** — a full card with owner (probably Emma + Raj), size (S or M), privacy impact (None), proof required.
4. **Approval gate** — reply `go` to route it to Tom, or `edit <field>`, `hold`, `drop`, `council`.

On `go`, the card is saved to `.claude/scope/2026-04-18-rule-19-completion-badge.md` and you're off.

---

## Step 5 — Feel the whole loop once

Before you pile in, push the first idea all the way through:

1. Intake → approved scope card
2. Tom routes to Emma (UX copy) + Raj (dashboard component)
3. They propose a minimal implementation (one component, one query)
4. Chen runs the verification gate (screenshot, type-check, golden-path E2E)
5. Card moves to `.claude/scope/done/`

This might take an hour of chat. Do it anyway — you'll know the rhythm.

---

## If the AI drifts

Pasteable corrections, in plain English:

| Drift | Paste this |
|---|---|
| Starts coding before the intake | *"Stop. Run the intake protocol per `.claude/skills/idea-intake.md`. Step 1 — Restate."* |
| Forgets it's Michelle | *"Re-read `.claude/agents/01-senior-pm.md` and continue as Michelle."* |
| Skips the recommendation on a question | *"Every clarifying question must include your recommendation and a one-line reason. Redo that question."* |
| Claims "done" without evidence | *"Verification gate — show the proof artefacts per `.claude/skills/verification-before-done.md`."* |
| Uses US spelling | *"en-AU. Revise."* |
| Suggests a specific loan product or rate | *"Back to `.claude/rules/privacy-hard-stops.md` section 6. Rewrite without product recommendations."* |
| Proposes storing a TFN anywhere | *"Hard stop — `.claude/skills/tfn-redaction-gate.md`. Redesign without retention."* |

---

## Which files load when

You don't manually load these — CLAUDE.md points to them and the agents reference each other. Just so you know what's running:

| Situation | Active files |
|---|---|
| Every session | `CLAUDE.md`, `rules/core.md`, `rules/privacy-hard-stops.md`, `rules/brand-guardrails.md` |
| You drop an idea | Michelle + `skills/idea-intake.md` |
| Idea approved, routing | Tom (`agents/02-senior-orchestrator.md`) |
| Privacy-adjacent work | Helen (`specialists/privacy-compliance.md`) |
| Fitzy prompt work | Priya + `skills/fitzy-persona-prompt.md` + `skills/autoresearch-loop.md` |
| Schema change | Marcus + `skills/supabase-rls-pattern.md` + migration scribe + RLS reviewer |
| Broker flow | Broker-invite skill + broker-flow auditor + Helen |
| Finishing up | Chen + `skills/verification-before-done.md` + replit-deploy-gate |

---

## Rules of thumb

- **Use `/idea` for anything that smells like work.** Not for tiny typo fixes.
- **Approve the scope card fast.** Don't polish it for an hour — you can always edit a field. Perfect scope cards are not the goal. Approved, routed ones are.
- **Trust the Council flag.** If Michelle says *"Council needed: YES"* on a scope card, don't overrule her unless you know why she's wrong.
- **One idea at a time.** Don't drop three in parallel for the first week. Let the loop settle.
- **When in doubt, ask "what would Peter say?"** — or Helen, or Daniel. You can invoke any specialist by name: *"Peter, is this phrasing advice-adjacent?"*

---

## What NOT to paste into the chat

- Your private PDFs (*Book V_2 Getting FITTER Financially*, etc.) — these are your IP; keep them out of model context
- Real member data
- Actual TFNs, bank account numbers, or API keys (the doctrine blocks the product from handling them; don't accidentally paste them into a chat either)

---

## Where things live

```
.claude/
  CLAUDE.md                  ← doctrine (always loaded)
  README.md                  ← how the team works
  FIRST_RUN.md               ← this file
  agents/
    01-senior-pm.md          ← Michelle (intake + scoping)
    02-senior-orchestrator.md ← Tom (routing + Council + verification)
    specialists/             ← 8 × 15+ yr specialists
    sub-agents/              ← 8 × narrow tool-like helpers
  skills/                    ← 9 × reusable patterns (intake, Council, autoresearch, etc.)
  rules/                     ← 3 × always-on hard stops
  commands/
    idea.md                  ← /idea slash command
  scope/
    TEMPLATE.md              ← scope card template
    2026-04-18-*.md          ← approved cards
    parked/                  ← "hold" cards
    done/                    ← verified + shipped
```

---

## One last thing

The team is here to **help you decide faster and ship with less doubt**. It is not here to replace your judgement. Duncan is Tier 0 — every scope card, every Council memo, every deploy gate ends with *"Duncan approves"*.

If a specialist recommends something that feels wrong to you, say so. The Council exists exactly for that. Your product instincts built this — don't outsource them.

Ready. Drop your first `/idea` and watch Michelle work.

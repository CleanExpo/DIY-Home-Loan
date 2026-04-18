---
name: idea-intake
description: Front-door protocol. When Duncan inputs an idea, Michelle restates it, asks targeted clarifying questions WITH a recommended answer for each, and produces a scope card for his approval.
triggers: [/idea, new idea, i want to, can we add, what if, could we, how about]
owned_by: michelle-zhao-senior-pm
---

# Idea Intake — Duncan's Front Door

> Duncan types an idea. Michelle does not guess. She restates, asks the few questions that actually matter, **pre-fills each one with her recommendation**, and lets Duncan accept, modify, or override per-question. The output is a scope card Tom can route.

## The four-step protocol

### Step 1 — Restate (single sentence)

Michelle writes one sentence that captures what Duncan meant, framed as an **outcome**, not a feature.

```
MICHELLE: Here's what I heard —

  "Members can upload a payslip, and we confirm it's TFN-free before it's saved."

  Is that the outcome you want? (yes / adjust / completely different)
```

Duncan replies in plain language. Michelle only moves on when the restatement is confirmed.

---

### Step 2 — Clarifying questions (each with a recommendation)

Michelle picks **3–5 questions** from the pool below based on the idea. She never asks all of them. Each question is presented in this exact format:

```
Q<N>. <question>

  Michelle recommends:  <answer>
  Why:                  <one-line reasoning from the idea>

  Duncan:
    ▸ Reply "ok"           — accept the recommendation
    ▸ Reply "change to X"  — swap the answer
    ▸ Reply "<free text>"  — write your own answer
```

### The question pool (Michelle picks the relevant ones)

| # | Question | When to ask |
|---|---|---|
| 1 | Who is this for? | Always |
| 2 | What does "done" look like, as one observable thing? | Always |
| 3 | Rough size — S (<2h) / M (2–8h) / L (1–3d) / XL (split it)? | Always |
| 4 | Privacy impact — None / Standard / TFN-adjacent / Regulated? | If any data touched |
| 5 | Reversibility — Easy / Hard / Irreversible? | If schema, integrations, or live deploys |
| 6 | Time horizon — this week / this month / someday? | Always |
| 7 | What's explicitly out of scope? | If the idea sprawls |
| 8 | What's this blocked by? | If it depends on something else |
| 9 | Does this need a Council call? | If HIGH risk OR Irreversible OR Regulated |

Never more than 5 questions in one intake. If Michelle wants to ask more, she writes the scope card with her best defaults marked `(assumed)` and lets Duncan correct at Step 4.

---

### Step 3 — Scope card

After the questions, Michelle produces one card using the template in `.claude/scope/TEMPLATE.md`. Every field is filled.

```
SCOPE CARD — <short title> — DD/MM/YYYY
════════════════════════════════════════
Outcome:          <from Step 1>
Done when:        <from Q2>
  □ <criterion 1>
  □ <criterion 2>

For:              <from Q1>
Size:             <from Q3>
Privacy impact:   <from Q4>
Reversibility:    <from Q5>
Time horizon:     <from Q6>
Out of scope:     <from Q7>
Blocked by:       <from Q8 — or "nothing">

Proposed owner:   <specialist name>
Sub-agents:       <list>
Reviewers:        <list>

Risk rating:      LOW | MEDIUM | HIGH
Council needed:   NO / YES (reason)

Proof at end:
  □ <artefact 1>
  □ <artefact 2>
════════════════════════════════════════
```

---

### Step 4 — Approval gate

Michelle closes with one question:

```
MICHELLE: Ready to hand this to <owner>?

  ▸ Reply "go"               — approved, Tom routes it
  ▸ Reply "edit <field>"     — change a field before approval
  ▸ Reply "hold"             — park it, save the card to .claude/scope/parked/
  ▸ Reply "drop"             — not doing this; card is discarded
  ▸ Reply "council"          — convene the LLM Council before routing
```

On "go": Michelle saves the card to `.claude/scope/<yyyy-mm-dd>-<slug>.md` and hands off to Tom.

---

## Recommendation heuristics (how Michelle picks good defaults)

- **Owner:** match Tom's routing map (`agents/02-senior-orchestrator.md`). Schema talk → Marcus. Auth talk → Raj. Privacy copy → Helen. Fitzy behaviour → Priya.
- **Privacy impact:** if the word "upload", "bank", "statement", "payslip", "TFN", "broker" appears → at least **Standard**. If "TFN" or "identity" → **TFN-adjacent**. If anything around advice, product recommendations, fees → **Regulated**.
- **Size:** bias toward the larger bucket when uncertain. Better to plan for M and come in early than to scope S and overrun.
- **Reversibility:** any schema change = Hard by default. Any live integration = Hard. Any domain change = Hard.
- **Council:** auto-recommend if (HIGH risk) OR (Irreversible) OR (Regulated). Duncan can still say no, but she flags it.

---

## What Michelle never does at intake

- Skip Step 1 (restate). Without it, everything downstream is guessing.
- Ask more than 5 questions in one round.
- Ask yes/no questions without a recommendation attached.
- Let Duncan approve a card with any `(assumed)` field still in it.
- Route to a specialist without a completed scope card.

---

## Worked example

```
DUNCAN: I want members to upload their last quarter of bank statements and
        have Fitzy pull out the repeating expenses automatically.

MICHELLE:
  Here's what I heard —
  "Members upload 90 days of bank statements; Fitzy analyses them and
   suggests repeating expense categories for the quarterly budget."

  Is that the outcome? (yes / adjust / completely different)

DUNCAN: yes

MICHELLE:
  Q1. Who is this for?
      Recommends: Member
      Why:        "Members" in the idea.
      Reply "ok" / "change to X" / free text.

DUNCAN: ok

MICHELLE:
  Q2. What does "done" look like?
      Recommends:
        "A member can upload up to 3 statement PDFs; after TFN scan, Fitzy
         returns a categorised list of repeating expenses; member can edit
         and confirm; result saves into budget_quarter."
      Why: Matches Rule 3 workflow in FITZY_BUDGET_HAND_HOLDING.md.

DUNCAN: ok but only 1 PDF at a time for phase 1

MICHELLE:
  Q3. Rough size?
      Recommends: L (1–3d)
      Why:        Upload path + TFN scan + Claude extraction + UI for
                  category edit + DB write. Not trivial.

DUNCAN: ok

MICHELLE:
  Q4. Privacy impact?
      Recommends: TFN-adjacent
      Why:        Bank statements routinely contain TFNs in transaction
                  references; the TFN sentinel is already in the path.

DUNCAN: ok

MICHELLE:
  Q5. Council needed?
      Recommends: NO
      Why:        TFN gate and RLS pattern already defined; not
                  irreversible; pattern is well-understood.

DUNCAN: ok

MICHELLE:
  SCOPE CARD — Auto-categorise bank statements — 18/04/2026
  ════════════════════════════════════════
  Outcome:          Members upload one 90-day statement; Fitzy suggests
                    repeating expense categories; member confirms; saved
                    to budget_quarter.
  Done when:
    □ Upload one PDF up to 20 MB, MIME-checked
    □ TFN sentinel runs and rejects on detection
    □ Claude Sonnet 4.5 returns categorised expenses (zod-validated)
    □ Member can edit categories and confirm
    □ Data writes to budget_quarter.notes and a new expense_line table
    □ Golden-path E2E test green

  For:              Member
  Size:             L (1–3d)
  Privacy impact:   TFN-adjacent
  Reversibility:    Hard (schema change)
  Time horizon:     this month
  Out of scope:     Multi-file upload; OCR-only PDFs; bank API auto-pull
  Blocked by:       nothing

  Proposed owner:   Raj (architect — upload flow + UI)
  Sub-agents:       TFN Sentinel, Migration Scribe, RLS Reviewer, en-AU Editor
  Reviewers:        Helen (privacy), Marcus (schema), Priya (Fitzy call), Chen (E2E)

  Risk rating:      MEDIUM
  Council needed:   NO

  Proof at end:
    □ Screenshot of a clean-PDF flow end-to-end
    □ Screenshot of a TFN-PDF rejection
    □ SQL of a saved expense_line rowset
    □ Golden-path E2E video
  ════════════════════════════════════════

  Ready to hand this to Raj?
  Reply: go / edit <field> / hold / drop / council

DUNCAN: go

MICHELLE: Saved to .claude/scope/2026-04-18-auto-categorise-bank-statements.md.
          Routing to Tom.
```

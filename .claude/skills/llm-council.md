---
name: llm-council
description: Karpathy-style multi-model deliberation for high-stakes decisions. Spawn N perspectives → anonymous peer review → Chairman synthesis.
triggers: [council, deliberate, high-stakes, hard to reverse, regulated decision]
---

# LLM Council

> For decisions where a wrong call is expensive and a single agent's confidence is not enough.

## When to convene

The Orchestrator (Tom) convenes only when at least two of these are true:

- Decision is hard or impossible to reverse
- HIGH risk per Michelle's scope sheet
- Specialists disagree
- Duncan has said "I'm not sure"
- Regulated privacy or AFSL-edge impact

Not for: copy tweaks, bugs, renames, layout nudges.

## The three phases

### Phase 1 — Independent opinions

Spin up at least **4 council members**. Each receives the same prompt, blind to the others.

Default council for DIY Home Loan (mix of angles, not just models):

- **Helen** (Privacy / Compliance view)
- **Peter** (Finance SME view)
- **Daniel** (AppSec view)
- **Raj** (Architecture view)
- **Chen** (Risk / Verification view) — optional 5th

Each writes a short position (≤ 300 words) ending in:

```
RECOMMENDATION: <do X / do Y / hold>
CONFIDENCE:     <0–10>
KEY RISK:       <one sentence>
```

### Phase 2 — Anonymous peer review

Positions are shuffled and **stripped of author identity**. Each member reviews the others' positions and ranks them 1..N for:

- Accuracy (does it hold up?)
- Risk coverage (does it see what matters?)
- Clarity of reasoning

No member reviews their own position.

### Phase 3 — Chairman synthesis

**Tom** (Orchestrator) is the default Chairman. Tom reads all positions + rankings and writes a single memo:

```
COUNCIL MEMO — <topic> — DD/MM/YYYY
════════════════════════════════════════
Question:       <what was asked>

Positions (in ranked order):
  1. <summary — top-ranked>
  2. <summary>
  3. <summary>

Consensus:      <what everyone agreed on>
Disagreement:   <where the cracks were>

Recommendation: <Chairman's synthesis, one paragraph>
Unresolved:     <what Duncan needs to decide>

Next action:    <single, specific>
════════════════════════════════════════
```

Tom saves the memo to `.claude/memos/<topic>-<date>.md` for audit.

## Rules

- **Minimum 4 members.** Three is not a council.
- **Real diversity.** If every member would answer the same, don't convene.
- **Blind review.** No names attached to positions during ranking.
- **Chairman never overrules unanimously.** If all 4 members agree, the Chairman's job is to write it up, not to dissent.
- **Duncan decides.** The memo is a recommendation. The human makes the call.

## Topics that warrant the Council (DIY Home Loan examples)

- Scope for the first real-member launch
- Pursuing AFSL vs staying firmly in general-information lane
- CDR partner vs interim Frollo
- Broker revenue model (if any)
- Storing any additional data class
- Major Fitzy persona change

## Anti-patterns

- Convening weekly "just to check" — the Council loses weight
- Stacking members from the same angle — no real diversity
- Skipping Phase 2 because it's fiddly — that's where the signal is

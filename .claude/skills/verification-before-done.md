---
name: verification-before-done
description: No "should work" claims. Proof artefacts precede any "done" report. Chen owns the gate.
triggers: [done, complete, ready, finished, ship, deploy]
---

# Verification Before Done

> When any agent or human says "it's done", the next words must be "here's the proof".

## The rule

A task is not done until **every Definition of Done item** from Michelle's scope sheet has a matching **proof artefact** attached.

## What counts as proof

| Work type | Acceptable proof |
|---|---|
| New route / page | Screenshot at the live URL; Playwright E2E green |
| Data change | SQL result showing the row(s); migration file link |
| Fitzy behaviour | Golden-set score; sampled response pasted |
| Privacy feature | Audit log entry; test run of the sentinel |
| Performance fix | Before/after Lighthouse numbers |
| Bug fix | Reproduction test (red → green) |
| Copy change | Screenshot of final layout; en-AU editor pass |

## What does NOT count

- "I checked locally and it works"
- "The types are green" (necessary, not sufficient)
- "The lint passed" (necessary, not sufficient)
- Screenshots of old state
- Promises like "it will work once deployed"

## Banned phrases

If any of these appear without attached proof, the verification gate fails:

- "Should work"
- "Probably working"
- "Ready for testing" *(if nothing was tested)*
- "All set"
- "Done"

## The gate (Chen runs it)

```
VERIFICATION — <task title>
════════════════════════════════════════
DoD item                    | Proof                    | ✓/✗
─────────────────────────────|──────────────────────────|─────
<criterion 1>                | <file / url / output>    |
<criterion 2>                | <file / url / output>    |
<criterion N>                | <file / url / output>    |

Cross-cutting checks:
  □ type-check green
  □ lint green
  □ golden paths still green
  □ en-AU editor pass (if copy changed)
  □ disclaimer sentinel pass (if AI output changed)
  □ RLS reviewer pass (if schema changed)
  □ TFN sentinel regression (if upload path changed)

Decision: PASS / FAIL
════════════════════════════════════════
```

## Recovery from a failed gate

If the gate fails:

1. **Do not re-claim done.** Reopen the task.
2. **Fix the missing proof**, not the message.
3. Re-submit for gate.
4. If the same item fails twice → back to Michelle for re-scope.

## Small tasks aren't exempt

Even a one-line copy tweak needs a screenshot showing the before/after. It takes 30 seconds and it's the habit that prevents silent regressions.

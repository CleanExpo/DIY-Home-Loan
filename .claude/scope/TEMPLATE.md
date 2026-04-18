# Scope Card — TEMPLATE

> Copy this file to `.claude/scope/<yyyy-mm-dd>-<slug>.md` when Michelle produces a card via `/idea`. Every field is filled; no blanks, no `(assumed)` left in.

```
SCOPE CARD — <short title> — DD/MM/YYYY
════════════════════════════════════════
Outcome:          <one sentence, outcome-framed, confirmed by Duncan>

Done when:
  □ <criterion 1 — testable>
  □ <criterion 2 — testable>
  □ <criterion N>

For:              Member | Broker | Duncan | Public
Size:             S (<2h) | M (2–8h) | L (1–3d) | XL (split it)
Privacy impact:   None | Standard | TFN-adjacent | Regulated
Reversibility:    Easy | Hard | Irreversible
Time horizon:     this week | this month | this quarter | someday
Out of scope:     <explicit exclusions>
Blocked by:       <prereq or "nothing">

Proposed owner:   <specialist>
Sub-agents:       <list of tool-like helpers>
Reviewers:        <other specialists who must approve>

Risk rating:      LOW | MEDIUM | HIGH
Council needed:   NO | YES — <reason>

Proof at end:
  □ <artefact 1 — screenshot / test / SQL result / log>
  □ <artefact 2>
  □ <artefact N>
════════════════════════════════════════

Intake dialogue:
<paste the Q&A from the intake session — short>

Approved by:      Duncan — DD/MM/YYYY HH:MM AEST
Routed to:        <owner>, at DD/MM/YYYY HH:MM AEST
```

## Scope card lifecycle

| State | Lives in | Moved by |
|---|---|---|
| Approved (`go`) | `.claude/scope/` | Michelle on approval |
| Parked (`hold`) | `.claude/scope/parked/` | Michelle on hold |
| Done | `.claude/scope/done/` | Chen on passing verification gate |
| Dropped (`drop`) | deleted | not retained |

## Why cards are kept after "done"

- Audit trail (what we shipped, when, why, and on what proof)
- Prior-art when the same idea returns
- Teaching material when Duncan onboards someone new
- Council evidence if a decision is ever questioned

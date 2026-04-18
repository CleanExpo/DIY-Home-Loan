---
name: prompt-eval-runner
role: Sub-agent — Autoresearch Loop for Fitzy
reports_to: priya-ramesh-ai-agent-engineer
---

# Prompt Eval Runner (Autoresearch Loop)

> Karpathy's autoresearch pattern, applied to Fitzy's system prompt. Small changes, short cycles, measured improvement.

## One job

Run the Fitzy golden set against the current prompt, score, propose one change at a time, measure again.

## The loop

```
1. Load:
     - current system prompt (prompts/fitzy-system.md)
     - golden set (eval/fitzy-golden.jsonl — 30 questions)
     - baseline score (last known mean)

2. Run:
     - For each question, call Claude Sonnet 4.5 with current prompt
     - Save responses to eval/runs/<timestamp>/

3. Score:
     - Peter scores factual accuracy       (0–5)
     - Emma scores tone                    (0–5)
     - Helen scores compliance             (0–5)
     - Duncan scores helpfulness           (0–5)
     - Mean = average across the four axes and 30 questions

4. Compare:
     - New mean >= baseline + 0.05  →  keep change, update baseline
     - New mean <  baseline         →  revert, log why
     - New mean ≈  baseline         →  keep if compliance score improved; otherwise revert

5. Log:
     - Append to eval/log.md with: change, delta, decision
     - One line per iteration — short is the point
```

## Rules (Karpathy-strict)

- **One change per iteration.** Change wording OR add a constraint OR rewrite a section — never all three.
- **No prompt-bloat.** If total prompt length grows by > 20% with no score gain, revert.
- **No reordering without measuring.** Order matters in prompts; don't rearrange blindly.
- **Overnight runs are fine** — just cap at 20 iterations per run so the log stays readable.

## Minimum golden-set construction (Priya + Peter + Duncan)

30 realistic questions covering:

- 6 × basic product explanation ("what is a LOC?")
- 6 × rule-specific ("I'm stuck on Rule 3")
- 6 × boundary-testing ("what should I invest in?") — must redirect to broker/accountant
- 6 × budget coaching ("my quarterly surplus dropped — what now?")
- 6 × meta ("who sees my bank statements?")

## Outputs

- `eval/log.md` — one-line changelog
- `eval/runs/<ts>/score.json` — raw scores
- `eval/baseline.json` — current best

## Success metric

- Mean score trending up week-over-week
- Compliance score = 5.0 (perfect) on every run — any drop reverts immediately
- Prompt length stable or shrinking while scores rise

## Escalation path

- Score plateaus for 3 iterations → Priya picks a different prompt section to probe
- Compliance score drops → Helen reviews the specific failure case before any more prompt changes

---
name: autoresearch-loop
description: Karpathy-style minimalist improvement loop. One change, short cycle, measured delta, keep or revert. Works when you have a numeric metric.
triggers: [improve, iterate, tune, eval, benchmark, autoresearch]
---

# Autoresearch Loop

> Karpathy's autoresearch uses a single agent, a tiny instruction file, short runs, and one metric. That's the whole method. The rest is discipline.

## Pre-conditions (all must be true)

- **Numeric metric exists.** (Fitzy golden-set mean, Lighthouse, time-to-first-token, TFN detection recall.)
- **Loop is short.** A cycle fits in < 10 min of compute or < 1 hr human review.
- **Baseline recorded.** You know today's number before you touch anything.
- **One variable at a time.** Change exactly one thing per cycle.

If any of these is false — don't call it autoresearch. It's vibes.

## The loop

```
while still_worth_it():
    change   = propose_one_change()
    result   = run(change)
    score    = measure(result)

    if score >= baseline + threshold:
        keep(change)
        baseline = score
        log("kept", change, score)
    else:
        revert(change)
        log("reverted", change, score)
```

## What "change" means — by domain

| Domain | "One change" examples |
|---|---|
| Fitzy system prompt | Reword one section; add one constraint; remove one example |
| TFN detector | Tighten regex; swap vision model; adjust confidence cutoff |
| Lighthouse | Lazy-load one component; compress one image; defer one script |
| Supabase query | Add one index; rewrite one JOIN; add one LIMIT |
| UX copy | Rewrite one heading; shorten one paragraph; swap one CTA verb |

## Logging format (one line per iteration — Karpathy strict)

```
eval/log.md:

DD/MM HH:MM | <change> | baseline <N> → <M> | kept | <short reason>
DD/MM HH:MM | <change> | baseline <N> → <M> | reverted | <short reason>
```

That's it. Don't write essays.

## Stopping rules

- **Three reverts in a row** → stop, step back, pick a different axis to probe.
- **Score plateaus for 5 iterations** → stop; baseline is good enough; go do something else.
- **Compliance metric drops** → stop immediately, revert, escalate to the owning specialist.

## Good candidates for autoresearch

- Fitzy prompt against golden set (owned by Priya + prompt-eval-runner sub-agent)
- TFN detector recall/precision (owned by tfn-sentinel + Daniel)
- Member signup completion rate (owned by Emma)
- Page-weight / Lighthouse (owned by Raj)
- Supabase query plan cost (owned by Marcus)

## Bad candidates (don't use autoresearch here)

- Product direction
- Brand choices
- Legal copy
- Anything where "the metric" is someone's gut

For those → LLM Council or Duncan decides directly.

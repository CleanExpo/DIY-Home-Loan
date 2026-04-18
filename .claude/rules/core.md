# Core Rules — DIY Home Loan

> Always loaded. Every agent and every session operates under these.

## Locale

- **Language:** Australian English (en-AU)
- **Dates:** DD/MM/YYYY
- **Currency:** AUD
- **Timezone:** AEST/AEDT
- **Phone:** `0x xxxx xxxx` or `+61 x xxxx xxxx`

## Spelling discipline

`colour`, `behaviour`, `organisation`, `optimise`, `analyse`, `centre`, `licence` (noun) / `license` (verb), `favourite`, `dialogue` (conversation) / `dialog` (UI modal).

## Intent classification

Before acting, classify the request:

| Mode | Signals | Governance |
|---|---|---|
| **BUILD** | implement, create, add, write | Standard — Michelle scopes first |
| **FIX** | bug, broken, failing | Standard — root-cause before patch |
| **PLAN** | evaluate, compare, design | Light — no execution gates |
| **AUDIT** | review, check, verify | Light — read-only |
| **EXPLORE** | what is, how does, show me | Minimal — just answer |

Default to lower governance when ambiguous.

## Validation gates

Never proceed without confirming:

- Referenced files exist (Glob / Grep / Read)
- Import paths verified against actual locations
- Database tables confirmed in schema before querying
- Environment variables declared in `.env.example`

## Execution safety

| Risk | Examples | Action |
|---|---|---|
| **LOW** | Read, add comments, create new files | Execute |
| **MEDIUM** | Edit existing code, add dependencies | Proceed with verification |
| **HIGH** | Delete files, schema changes, auth changes, broker path changes | Pause and confirm with Duncan |

Privacy-impacting work is never LOW risk, regardless of code size.

## Anti-hallucination

Classify every factual claim:

- **Confirmed** — read from file, verified by tool
- **Inferred** — deduced from confirmed facts
- **Assumed** — not verified → pause and verify before acting

Never invent: API endpoints, schema, file paths, env var names, package versions, Anthropic model IDs.

## Conventions

- **React:** `PascalCase.tsx`
- **Utils:** `kebab-case.ts`
- **Agents:** `kebab-case.md`
- **Skills:** `kebab-case.md`
- **Migrations:** `YYYYMMDDHHMMSS_verb_noun.sql`
- **Commits:** `<type>(<scope>): <description>` — `feat`, `fix`, `docs`, `chore`, `refactor`, `test`

## Architecture layers

```
UI Components → Hooks → Server Actions / Route Handlers → Supabase / Anthropic
```

No cross-layer imports. Each layer imports only from the layer directly below.

## The three mandatory skills

Every task touches these:

1. **`skills/verification-before-done.md`** — proof before any "done" claim
2. **`skills/tfn-redaction-gate.md`** — TFN hard stop on any upload path
3. **`skills/not-advice-footer.md`** — disclaimer on money-touching AI output

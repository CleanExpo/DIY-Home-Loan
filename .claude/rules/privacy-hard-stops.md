# Privacy Hard Stops

> Always loaded. These are not preferences. They are the things that, if broken, break the product's right to exist.

## 1. TFN rule

- **We never store a Tax File Number.**
- Every upload passes the TFN Sentinel.
- On detection → reject, delete temp, log, return redaction guidance.
- No exceptions for "admin convenience".
- No "encrypted at rest" loophole — we don't accept them at all.

Legal: Privacy (Tax File Number) Rule 2015.

## 2. Data residency

- Supabase project region = **`ap-southeast-2` (Sydney)**.
- No workaround for cost.
- Sub-processors outside AU (Anthropic — US) are disclosed in the Privacy Policy.
- No shifting to a different region without a documented migration + member notice.

## 3. APP compliance baseline

- **APP 3** — Collect only what the current step needs. No speculative fields.
- **APP 5** — Notify the member at collection: what, why, who sees it.
- **APP 6** — Use data for the member's plan and the broker they invited. Nothing else.
- **APP 11** — HTTPS + RLS + time-boxed access + encryption at rest (Supabase default).
- **APP 12** — Member can access their data (self-serve download).
- **APP 13** — Member can correct their data (self-serve edit where possible).

## 4. Consent audit

Every data movement writes a row to `consent_audit`:

- Upload accepted / rejected
- Broker invited / accepted / revoked
- Broker view (per read, sampled)
- Export requested
- Delete requested / executed

Member can view their own audit log.

## 5. Broker access

- Member-initiated only
- Time-boxed: default 90 days
- Scoped: `quarterly_summary` | `full` (explicit opt-in)
- Revocable: ≤ 2 s lockout on revoke
- Never "account-wide" — always per household

## 6. Fitzy language

- Never personal advice
- Never specific product recommendations
- Never current interest rate quotes
- Never "you will save $X" promises
- Always footer on money-touching responses (`skills/not-advice-footer.md`)

## 7. Logging

- Logger redactor scrubs:
  - TFN regex
  - Email addresses
  - Bank account numbers
  - BSB numbers
  - Supabase service role key (should never be in a log line anyway)
- No `console.log(JSON.stringify(req.body))`.
- No Fitzy response bodies in logs beyond sampling with member consent.

## 8. Deletion

- Member can request full household deletion.
- Deletion cascades via foreign keys.
- Storage objects are deleted from Supabase Storage in the same transaction.
- Final audit record: `action = 'household_deleted'` (kept 7 years as an operational record, contains no member content beyond the household id and timestamp).

## 9. Sub-processors (disclosed in Privacy Policy)

- Supabase (data, AU region)
- Anthropic (LLM, US region — flagged in the policy)
- Replit (hosting)
- Resend (transactional email)

Any new sub-processor requires a Privacy Policy update and member notification.

## 10. Breach response

If a data issue is detected:

1. **Stop the bleed** (Daniel leads technical containment).
2. **Assess scope** (Helen chairs).
3. **Notify OAIC within Notifiable Data Breaches timeframes** if the threshold is met.
4. **Notify affected members** with what happened, what's done, what they should do.
5. **Post-mortem** written and published internally.

No covering up. No "probably fine". When in doubt, disclose.

---
name: broker-invite-pattern
description: Member-initiated, time-boxed, revocable broker access. Scoped, audited, no exceptions.
triggers: [broker, handoff, invite, share with jack]
---

# Broker Invite Pattern

> The broker never initiates access. The member always invites. Access is scoped, time-boxed, revocable, and audited.

## The flow (happy path)

```
Member             DIY Platform              Broker (e.g. Jack)
  │                     │                           │
  │ — start invite —→   │                           │
  │ (scope, broker email)                           │
  │                     │ — signed token, 48h TTL → │  (email via Resend)
  │                     │                           │
  │                     │ ←——  click + sign in ——— │
  │                     │ create broker_access      │
  │                     │ (90d, scope, revoked_at   │
  │                     │  NULL)                    │
  │                     │ audit log                 │
  │                     │                           │
  │                     │ ←—————  read data  ——————│
  │                     │ (RLS checks expires_at,   │
  │                     │  revoked_at, scope)       │
  │                     │ audit log on every read   │
```

## Scopes

| Scope | What the broker sees |
|---|---|
| `quarterly_summary` (default) | Current quarter summary: surplus, rule progress, next steps. No TFN, no full bank numbers. |
| `full` | Above + redacted bank statement metadata (filenames + dates, not contents) + consent audit |

`full` requires a distinct checkbox in the invite UI labelled *"Let the broker see my evidence list (filenames and dates only — never contents)"*.

## Token generation

- Server-side only (route handler)
- Uses `jose` or Supabase-issued JWT with:
  - `sub` = invite id
  - `exp` = now + 48h
  - `aud` = `broker-invite`
  - `scope` = chosen scope
- Single use: on consumption, mark the invite row `consumed_at = now()`
- Re-use attempt returns 410 Gone

## `broker_access` table (columns)

- `id`, `household_id`, `broker_auth_id` (Supabase user), `broker_email`, `scope`, `granted_at`, `expires_at` (default: now + 90d), `revoked_at`, `created_via_invite_id`

## RLS rule for broker reads

Every broker-visible table has the policy from `skills/supabase-rls-pattern.md`:

```
exists (
  select 1 from broker_access ba
  where ba.household_id = <this>.household_id
    and ba.broker_auth_id = auth.uid()
    and ba.revoked_at is null
    and now() < ba.expires_at
    and ba.scope in ('quarterly_summary', 'full')
)
```

## Revoke

- Member clicks "Revoke access" on the member broker list
- Sets `revoked_at = now()` — next query denies (≤ 2 s lockout by design)
- Broker sees a "Your access to this household has been revoked" page on next request
- Audit log: `action = 'broker_revoked'`

## Expiry

- At `expires_at`, policy auto-denies. No broker lockout notification from the platform (broker already knew the expiry date from the invite acceptance screen).
- Renewal requires a new invite from the member.

## Emails (owned by Emma + Helen, sent via Resend)

- Invite: subject "Your client [name] has invited you to their DIY Home Loan household"
- Revoked: none sent (silent lockout; member decides if they want to message the broker)
- Expiring soon (7d): optional, Phase 2
- Audit trail export: on member request, Phase 2

## Hard rules

- No "permanent" broker access.
- No multi-household broker roles.
- No scope outside the two defined.
- No sharing tokens across email addresses — the token ties to the invited email.
- No TFN ever surfaces to a broker.

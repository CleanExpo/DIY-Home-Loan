# DIY Home Loan — Reinforcement Plan

> **For:** Duncan Perkins (FITTER Financially / DIY Home Loan)
> **Prepared by:** Phill McGurk (informal advisory)
> **Date:** 18/04/2026
> **Purpose:** Strengthen the existing project and guide the next build phase on **Replit**, without replacing what you already have.

This doc is advisory. Keep your brand, your content, and your voice. Borrow the production-grade patterns from the `CleanExpo/NodeJS-Starter-V1` framework only where they genuinely save you time or protect your members.

---

## 1. What you have today

| Asset | Location | Keep as-is? |
|---|---|---|
| Landing pages (deep-green/cream brand) | `index.html`, `mandy-and-dave.html` | Yes — port straight into Next.js |
| Fitzy orchestrator + 8 sub-agent specs | `agents/*.md` | Yes — these become your system prompts |
| Rule 19 question bank + answer key | `rule-19/*.md` | Yes — becomes seed content |
| MoneySmart budget template | `budget-planner_moneysmart.xls` | Yes — users start here |
| Source-of-truth PDFs (`Book V_2`, `DIY Home Loan`, `Super Home Loan`) | (not in repo; `CURRENT_SOURCE_OF_TRUTH.md`) | Keep private; treat as reference only |

**You already have the hard part done** — the product thinking, the rules, the privacy design, the agent personalities. The code is the remaining step.

---

## 2. Definition of "production" for the Member area MVP

What needs to be true before you let a real member log in:

- [ ] A member can register with email + password (AU-hosted)
- [ ] A member can start a conversation with Fitzy and pick up where they left off
- [ ] A member can upload a bank statement or payslip; TFN is detected and upload is rejected if present
- [ ] A member's quarterly budget is saved and retrievable
- [ ] A broker (e.g. Jack) can be invited by the member to view a specific household
- [ ] Privacy Policy and Terms of Service published
- [ ] Data lives in an AU region (Supabase ap-southeast-2, Sydney)
- [ ] HTTPS enforced; secrets are in Replit Secrets, not in the repo

That is Phase 1. Everything else is Phase 2+.

---

## 3. Recommended stack (Replit-fit, simplified)

The `NodeJS-Starter-V1` framework is a full monorepo (Next.js + FastAPI + Docker). **That's overkill for Replit and for Phase 1.** Use this instead:

| Layer | Recommendation | Why |
|---|---|---|
| **Hosting** | Replit Deployments (Autoscale) | Zero-config HTTPS, AU edge, Duncan stays in Replit |
| **App** | Single Next.js 15 app (App Router, TypeScript) | Works on Replit out of the box; one service to deploy |
| **Database** | Supabase (region: `ap-southeast-2` Sydney) | Free tier is generous; built-in auth; RLS for privacy |
| **Auth** | Supabase Auth (email + OTP) | No Passport/NextAuth complexity |
| **Storage** | Supabase Storage (bucket: `evidence`) | For bank statements, payslips |
| **AI** | Anthropic SDK direct, Claude Sonnet 4.5 | Fitzy calls Claude; no LangGraph needed in Phase 1 |
| **Email** | Resend (AU-compatible) | Transactional only; broker invites |
| **Secrets** | Replit Secrets | Never commit `.env` |

**Skip for now:** FastAPI backend, Docker Compose, Turbo/pnpm monorepo, Percy, Lighthouse CI, ZAP. These are Phase 3 concerns.

---

## 4. Patterns to borrow from the framework

Copy **files and patterns**, not the whole scaffold. Specific pulls:

### 4.1 Governance (5 minutes, huge payoff)

Copy these into Duncan's project root:

| From `NodeJS-Starter-V1` | Put in Duncan's repo at | Why |
|---|---|---|
| `CLAUDE.md` (trimmed) | `CLAUDE.md` | Keeps Claude disciplined when he uses Replit AI or Claude Code |
| `.claude/rules/core.md` | `.claude/rules/core.md` | en-AU, validation gates, execution safety |
| `.claude/rules/verification-gate.md` | `.claude/rules/verification-gate.md` | Prevents "it's done" claims without proof |
| `.claude/rules/slop-prevention.md` (adapted for deep-green brand, not OLED) | `.claude/rules/design.md` | Protects his brand from AI drift |

### 4.2 Database patterns

From `.claude/rules/database/supabase-migrations.md` — copy the RLS pattern and adapt. Minimum tables Duncan needs:

```sql
-- household (one per member)
create table household (
  id uuid primary key default gen_random_uuid(),
  owner_id uuid references auth.users(id) on delete cascade not null,
  display_name text,
  created_at timestamptz default now()
);

-- quarterly budget snapshot (Rule 3)
create table budget_quarter (
  id uuid primary key default gen_random_uuid(),
  household_id uuid references household(id) on delete cascade not null,
  quarter text not null,              -- e.g. '2026-Q2'
  net_income_monthly numeric,
  total_spend_monthly numeric,
  surplus_monthly numeric,
  notes text,
  created_at timestamptz default now()
);

-- rule progress (20 rules)
create table rule_state (
  household_id uuid references household(id) on delete cascade,
  rule_number int not null,           -- 1..20
  status text not null,               -- 'not_started' | 'in_progress' | 'met' | 'blocked'
  evidence_ref text,
  updated_at timestamptz default now(),
  primary key (household_id, rule_number)
);

-- evidence uploads
create table evidence (
  id uuid primary key default gen_random_uuid(),
  household_id uuid references household(id) on delete cascade not null,
  kind text not null,                 -- 'payslip' | 'bank_statement' | 'credit_card' | 'other'
  storage_path text not null,
  tfn_scan_status text not null,      -- 'clean' | 'rejected_tfn' | 'pending'
  uploaded_at timestamptz default now()
);

-- consent + audit (Privacy Act, APP 11)
create table consent_audit (
  id uuid primary key default gen_random_uuid(),
  household_id uuid references household(id) on delete cascade not null,
  actor_id uuid,                      -- member or broker
  action text not null,               -- 'upload', 'broker_view_granted', 'broker_revoked', 'export'
  detail jsonb,
  at timestamptz default now()
);

-- broker access grants (time-boxed)
create table broker_access (
  id uuid primary key default gen_random_uuid(),
  household_id uuid references household(id) on delete cascade not null,
  broker_email text not null,
  scope text not null,                -- 'quarterly_summary' | 'full'
  granted_at timestamptz default now(),
  expires_at timestamptz not null,
  revoked_at timestamptz
);
```

Then enable RLS on every table. Pattern:

```sql
alter table household enable row level security;
create policy "owner can see own household"
  on household for all
  using (auth.uid() = owner_id);
```

### 4.3 Agent structure (matches Fitzy's existing design)

Duncan's agent briefs already describe a master → specialists model. Implement Phase 1 as **one Claude system prompt per agent**, swapped by route:

```
/app/api/chat/fitzy/route.ts          → FITZY_MASTER_ORCHESTRATOR.md as system prompt
/app/api/chat/budget/route.ts         → BUDGET_FOUNDATION_AGENT_RULE_3.md
/app/api/chat/broker-invite/route.ts  → FITZY_BROKER_INVITE_FLOW.md
```

Don't build LangGraph yet. One `ANTHROPIC_API_KEY` + one `claude-sonnet-4-5` call per route is enough for Phase 1.

### 4.4 TFN hard rule (critical compliance gate)

From `agents/FITZY_DATA_INTAKE_AND_PRIVACY.md`. Implementation path:

1. **PDF uploads:** extract text with `pdf-parse`, run regex `/\b\d{3}\s?\d{3}\s?\d{3}\b/` (TFN is 8 or 9 digits in 3-3-3 pattern)
2. **Image uploads (photos of statements):** send to Claude Sonnet 4.5 with a "does this image contain a TFN?" prompt
3. **On detection:** reject the upload, set `tfn_scan_status = 'rejected_tfn'`, log to `consent_audit`, return UI message: *"This file appears to contain a Tax File Number. Please redact it and upload again."*
4. **Privacy (Tax File Number) Rule 2015** — Duncan must never store TFNs, full stop.

---

## 5. Phase 1 scope (2–3 weeks, Replit)

**Goal:** One member can go from signup to a saved Q2 budget.

| Week | Milestone |
|---|---|
| 1 | Next.js on Replit + Supabase project (AU region) + Auth working + deep-green brand ported from `index.html` |
| 2 | Fitzy chat shell (streaming Claude responses) + household + budget_quarter schema + Member dashboard shell |
| 3 | Evidence upload + TFN gate + quarterly budget form + broker invite (email only, no portal yet) + Privacy Policy / ToS |

### Pages needed
- `/` — keep existing `index.html` content, port to Next.js (unchanged look)
- `/mandy-and-dave` — port from existing
- `/signup`, `/login`, `/logout` — Supabase Auth
- `/member` — dashboard (welcome, next step, last quarter summary)
- `/member/chat` — Fitzy chat
- `/member/budget` — quarterly budget form + upload
- `/member/rules` — 20-rule checklist (read-only in Phase 1)
- `/privacy`, `/terms` — legal

---

## 6. Phase 2 (later — 3–6 weeks of work)

Only start when Phase 1 has real members using it.

- Broker portal: `/broker` — brokers accept an invite and see a single household's quarterly summary
- Open Banking via Frollo (member opens Frollo in a new tab, uploads the export file — no direct CDR integration yet)
- Full rule dashboard with evidence capture per rule
- Rule 19 quiz (you already have the question bank)
- Quarterly reminder emails (AEST timing, Resend + cron via Replit scheduled deployments)

---

## 7. Phase 3 (12+ months out)

- Own CDR accreditation or partner-channel with an accredited data recipient
- LangGraph agent orchestration (copy framework's `apps/backend/src/agents`)
- Multi-household for accountants / financial coaches
- Separate Next.js + FastAPI services (at this point, adopting the full `NodeJS-Starter-V1` monorepo makes sense)

---

## 8. Privacy & compliance checklist (Australia)

Non-negotiable for a financial product holding bank statements:

- [ ] Supabase project region: **Sydney (`ap-southeast-2`)** — confirmed at creation
- [ ] TFN never stored, ever; detection gate on every upload
- [ ] Privacy Policy lists: what's collected, why, retention, who can access, how to request deletion
- [ ] APP 3 (collection) — only collect what's needed for the quarterly review
- [ ] APP 6 (use) — don't use data for anything except the member's plan and the broker they explicitly invited
- [ ] APP 11 (security) — HTTPS, RLS enforced, time-boxed broker access
- [ ] Consent audit log (`consent_audit` table) records every data movement
- [ ] "Not personal financial advice" disclaimer on every Fitzy response
- [ ] Broker handoff: broker access expires after 90 days unless renewed
- [ ] Member can download their data and request deletion

Duncan is **not** a licensed financial adviser unless he holds an AFSL or is an authorised representative. Every agent response needs the *general-education-not-advice* footer.

---

## 9. Concrete first steps on Replit

Duncan can do these in order:

1. **Create a Replit** — template: `Next.js` (TypeScript)
2. **Create a Supabase project** at supabase.com, region Sydney, note the URL and anon key
3. **Replit Secrets** — add:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `SUPABASE_SERVICE_ROLE_KEY` (server-only)
   - `ANTHROPIC_API_KEY`
   - `RESEND_API_KEY` (when ready for emails)
4. **Install**:
   ```bash
   npm i @supabase/supabase-js @supabase/ssr @anthropic-ai/sdk zod
   npm i -D @types/node
   ```
5. **Paste the SQL** from §4.2 into Supabase SQL editor, run it, enable RLS
6. **Port `index.html`** into `app/page.tsx` — keep the deep-green `#1a4d2e` / cream `#f5f0e8` brand exactly
7. **Create `app/api/chat/fitzy/route.ts`** — stream Claude responses using the system prompt from `agents/FITZY_MASTER_ORCHESTRATOR.md`
8. **Wire Supabase Auth** following Supabase's Next.js App Router guide
9. **Deploy** via Replit Deployments → Autoscale

If Duncan wants AI help building this, the borrowed `CLAUDE.md` + `.claude/rules/` will keep Claude on-brand and en-AU.

---

## 10. What NOT to do (yet)

- ❌ Don't adopt the full `NodeJS-Starter-V1` monorepo — too much infrastructure for Replit's model
- ❌ Don't switch to the "Scientific Luxury" OLED black theme — your deep-green brand is your identity
- ❌ Don't build FastAPI or LangGraph now — Claude API routes do the job
- ❌ Don't integrate CDR directly — use Frollo until members demand seamless
- ❌ Don't store TFNs, even redacted, even encrypted — reject, don't retain
- ❌ Don't take member bank credentials — Open Banking flow only
- ❌ Don't let brokers see data without explicit time-boxed invites
- ❌ Don't publish without Privacy Policy + ToS + "not financial advice" disclaimer

---

## 11. If Duncan wants to talk through any of this

The clearest next conversation is: **"Show me Phase 1 Week 1 as a running Replit."** That would mean a working Next.js app on Replit, Supabase wired, Auth working, with the ported landing page. ~8–12 hours of focused work, or a weekend.

Everything else follows.

---

*Prepared informally. Duncan owns all decisions. No code in this document is production-tested against his specific setup — it's a pattern guide based on the framework and his existing specs.*

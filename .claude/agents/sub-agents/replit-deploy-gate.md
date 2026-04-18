---
name: replit-deploy-gate
role: Sub-agent — Pre-Deploy Checklist on Replit
reports_to: chen-wei-qa-verification
---

# Replit Deploy Gate

> Runs before Duncan promotes a Replit to production. If any box is red, the deploy does not happen.

## One job

Mechanical pre-deploy check. No judgment, just checklist.

## The gate (all must pass)

### Build & runtime
- [ ] `npm run build` passes with zero warnings
- [ ] `npm run start` boots on PORT from env
- [ ] `.replit` has a correct `run` command
- [ ] Node version pinned in `package.json` `engines.node` (>= 20)

### Environment
- [ ] Every `process.env.*` referenced in code is set in Replit Secrets (prod) — script diffs code vs secrets
- [ ] `SUPABASE_SERVICE_ROLE_KEY` is present, scoped to server only (no `NEXT_PUBLIC_` prefix)
- [ ] `ANTHROPIC_API_KEY` present, server only
- [ ] No `.env*` file committed (grep the repo)
- [ ] Production Supabase URL ≠ dev Supabase URL (guard against dev-pointing prod)

### Database
- [ ] All migrations applied to prod Supabase (list from `supabase db migration list`)
- [ ] RLS review is green for every migration in the diff
- [ ] Seed data script did NOT run against prod (flag if it has)

### Quality
- [ ] Type-check green
- [ ] Lint green
- [ ] Unit + integration tests green
- [ ] Golden-path E2E green
- [ ] Lighthouse performance ≥ 85 on `/` and `/member` (on preview deploy)

### Privacy / compliance
- [ ] Privacy Policy and ToS pages live
- [ ] Disclaimer Sentinel passing on sampled Fitzy responses
- [ ] TFN Sentinel integration test green
- [ ] Consent audit logging verified for: upload, broker invite, broker view

### Observability
- [ ] Error logging wired (console is fine for Phase 1; Sentry Phase 2)
- [ ] Log redactor scrubs TFN regex + email addresses
- [ ] Health check endpoint `/api/health` returns 200

### Rollback
- [ ] Previous Replit Deployment revision is pinned and promote-able
- [ ] Rollback takes < 2 minutes (timed)
- [ ] DB migration rollback path documented for anything in this deploy

## Output

```
REPLIT DEPLOY GATE — <version> — DD/MM/YYYY HH:MM AEST
────────────────────────────────────────
Build:         PASS / FAIL
Env:           PASS / FAIL
Database:      PASS / FAIL
Quality:       PASS / FAIL
Privacy:       PASS / FAIL
Observability: PASS / FAIL
Rollback:      PASS / FAIL
────────────────────────────────────────
Decision:      DEPLOY / HOLD
Evidence:      [links to logs, screenshots]
```

## Hard rules

- No "override" switch.
- If a box is yellow (unclear), treat it as red.
- Deploys happen during business hours AEST. No 2 AM heroics.

## Success metric

- Zero production incidents within 24 h of a green-gate deploy, rolling average
- Time to run the full gate ≤ 10 min
- Rollback drill executed monthly and timed

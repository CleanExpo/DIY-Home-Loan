---
name: supabase-rls-pattern
description: Canonical table + RLS template for DIY Home Loan. Every new table starts here.
triggers: [migration, new table, schema, RLS, supabase]
---

# Supabase RLS Pattern

> Copy, adapt the table name and columns, keep the structure. Marcus reviews every deviation.

## Template

```sql
-- supabase/migrations/YYYYMMDDHHMMSS_create_<table>.sql
-- =============================================================================
-- Migration:     create_<table>
-- Date:          DD/MM/YYYY
-- Description:   <one line>
-- Reversible:    yes
-- Rollback:      drop table public.<table>;
-- =============================================================================

create table if not exists public.<table> (
    id               uuid primary key default gen_random_uuid(),
    household_id     uuid not null references public.household(id) on delete cascade,
    -- domain columns here
    created_at       timestamptz not null default now(),
    updated_at       timestamptz not null default now()
);

-- Updated-at trigger (function defined once, in an earlier migration)
create trigger <table>_updated_at
    before update on public.<table>
    for each row execute function public.handle_updated_at();

-- Indexes: add for the most-likely query pattern
create index if not exists <table>_household_id_idx
    on public.<table>(household_id);

-- RLS — ALWAYS ON
alter table public.<table> enable row level security;

-- Policy: household owner can read
create policy "<table>_owner_select"
    on public.<table> for select
    using (
        auth.uid() = (
            select owner_id from public.household where id = <table>.household_id
        )
    );

-- Policy: household owner can insert
create policy "<table>_owner_insert"
    on public.<table> for insert
    with check (
        auth.uid() = (
            select owner_id from public.household where id = <table>.household_id
        )
    );

-- Policy: household owner can update
create policy "<table>_owner_update"
    on public.<table> for update
    using (
        auth.uid() = (
            select owner_id from public.household where id = <table>.household_id
        )
    );
```

## Broker-readable table pattern (additive policy)

When a table is visible to an invited broker under a scope:

```sql
create policy "<table>_broker_select"
    on public.<table> for select
    using (
        exists (
            select 1
            from public.broker_access ba
            where ba.household_id = <table>.household_id
              and ba.broker_auth_id = auth.uid()
              and ba.revoked_at is null
              and now() < ba.expires_at
              and ba.scope in ('quarterly_summary', 'full')
        )
    );
```

## The `handle_updated_at` function (migration 0001)

```sql
create or replace function public.handle_updated_at()
returns trigger
language plpgsql
as $$
begin
    new.updated_at = now();
    return new;
end;
$$;
```

## Anti-patterns (RLS Reviewer will reject)

- `using (true)` on any member-data table
- Missing `enable row level security`
- No policy at all
- Policies that check a client-supplied header instead of `auth.uid()`
- `grant all to authenticated` (RLS is the gate, not grants)

## The 8 core tables for Phase 1

`household`, `budget_quarter`, `rule_state`, `evidence`, `consent_audit`, `broker_access`, `chat_session`, `chat_message`.

See Marcus's `data-engineer.md` for the full column list and Migration Scribe for authored files.

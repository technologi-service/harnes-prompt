# 🏛️ Architecture and Strict Technical Rules

> **Binding rules.** The agent may NOT skip any rule in this document.
> If a task requires breaking a rule, it must stop first and discuss it with the human;
> rules are never broken unilaterally. This document describes the technical "how";
> the "what" and "status" live in `doc/map.md`; the "execution order" in `doc/sequences.md`.
>
> 🇪🇸 Versión en español: [`architecture.md`](architecture.md) — keep both in sync.

---

## 0. Non-negotiable principles

1. **Strict TypeScript.** `strict: true`. `any` is forbidden unless justified in code (`// eslint-disable` with a reason). Prefer `unknown` + narrowing.
2. **Secrets server-side only.** No private key (including Supabase `service_role`) may reach the client (web or mobile). The client only uses public keys (`anon`).
3. **RLS always on.** Every Supabase table has Row Level Security enabled **before** being exposed. No policy = no access.
4. **Single source of truth for types.** Database types are generated from Supabase; no hand-written models that duplicate the schema.
5. **Do not hallucinate state.** Before modifying, validate against the real repo (see `CLAUDE.md` → Execution Flow).

---

## 1. Monorepo structure

```
apps/web          → Next.js (App Router)
apps/mobile       → Expo (Expo Router)
packages/core     → Domain, types and pure logic (no React, no I/O)
packages/ui       → Shared presentational components
packages/supabase → Supabase client, generated types and data-access
supabase/         → SQL migrations, seeds and config (Supabase CLI)
doc/              → Living documentation
```

Dependency rules between packages:

- `apps/*` may depend on `packages/*`. **Never** the other way around.
- `packages/core` is **pure**: no React, no Next, no Expo, no direct network/disk access.
- Data-access logic lives in `packages/supabase`, not scattered across components.
- No imports between apps (`apps/web` does not import from `apps/mobile` or vice versa).

---

## 2. Next.js rules (App Router) — `apps/web`

1. **Server Components by default.** `'use client'` only when browser state/effects/events are needed, and as low in the tree as possible.
2. **Mutations via Server Actions** or Route Handlers (`app/api/.../route.ts`). The client never talks to private services directly.
3. **Server-side data fetching.** Reads with sensitive data or requiring `service_role` happen in server components / actions, never on the client.
4. **Environment variables:** only `NEXT_PUBLIC_`-prefixed vars are client-accessible. Everything else is server-only. Validate env with a schema (e.g. Zod) at startup.
5. **Explicit caching.** Decide and document `revalidate` / `cache` per route; never leave caching behavior to chance.
6. **Route structure:** feature colocation within `app/`. Reusable UI components go to `packages/ui`.
7. **No domain logic in components.** Business logic lives in `packages/core`; components orchestrate and present.

---

## 3. Expo rules (React Native) — `apps/mobile`

1. **Expo Router** (file-based) as the navigation system.
2. **No secrets in the bundle.** Public keys only. Sensitive config via build env vars (EAS) and never committed.
3. **Mobile Supabase client** with secure session storage (e.g. `expo-secure-store`) for tokens; never plain `AsyncStorage` for sensitive credentials.
4. **Shared UI** from `packages/ui` where feasible; platform-specific components use suffixes (`.native.tsx` / `.web.tsx`) when they coexist.
5. **Shared domain logic** from `packages/core` — never duplicate business rules between web and mobile.

---

## 4. Supabase rules

### 4.1 Access and keys
1. **Client (web/mobile):** `anon key` + user session only. All access goes through RLS.
2. **Server (Next.js server / Edge Functions):** may use `service_role` **only** server-side and for justified admin operations. Every `service_role` use is documented.
3. **One client per environment** centralized in `packages/supabase` (one for browser, one for server). No ad-hoc clients scattered across the app.

### 4.2 Schema and migrations
4. **Every schema change is a migration** versioned via Supabase CLI (`supabase/migrations`). Editing the schema by hand in the dashboard without a matching migration is forbidden.
5. **Generated types:** after each migration, regenerate types (`supabase gen types typescript`) and commit them in `packages/supabase`.
6. **SQL naming:** tables `snake_case` plural, columns `snake_case`, foreign keys `<table>_id`, timestamps `created_at` / `updated_at`.

### 4.3 RLS (Row Level Security) — critical
7. **RLS enabled on EVERY table** that holds or relates to user data, in the same migration that creates the table.
8. **Explicit policies per operation** (`select`, `insert`, `update`, `delete`). No `using (true)` policies except for truly public, documented data.
9. **Per-user/tenant isolation** with `auth.uid()` (or a tenant column) in the `using` / `with check` clause.
10. **`with check` on inserts/updates** to prevent a user from writing rows they do not own.
11. **Test the policies:** every RLS table ships at least one test/seed case verifying that a user cannot access another user's data.

### 4.4 Edge Functions
12. Edge Functions validate input (schema), never trust client data, and keep secrets in the function environment.

---

## 5. Code quality and conventions

1. **Lint + format** mandatory (ESLint + Prettier). Code is committed formatted.
2. **Validate external data with Zod** (or equivalent) at the boundaries: forms, API responses, function payloads.
3. **Explicit error handling.** Never swallow errors silently; propagate or log with context.
4. **English names in code** (variables, functions, tables); documentation in Spanish + English.
5. **Tests** for domain logic in `packages/core` and for critical RLS policies.
6. **Small, descriptive commits**, one intent per commit.

---

## 6. Security (actionable summary)

- [ ] Could any secret reach the client? → Must be **no**.
- [ ] Does the new table have RLS + per-operation policies? → Must be **yes**.
- [ ] Are external inputs validated? → Must be **yes**.
- [ ] Is `service_role` used only server-side and documented? → Must be **yes**.

> This checklist applies to every PR/task touching data or authentication.

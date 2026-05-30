# 🧭 Prompt Sequencer

> **Living, ordered document.** This is the step-by-step roadmap the agent reads and
> updates. The agent works **one step at a time**, in order, and marks the step status
> when done. Steps are not skipped without agreeing it with the human.
>
> 🇪🇸 Versión en español: [`sequences.md`](sequences.md) — keep both in sync.

## How to use this file

1. The agent opens `sequences.en.md` and finds the **first incomplete step** (`⬜` or `🟡`).
2. Reads its "Goal", "Definition of done" and "Applicable rules".
3. Validates the real repo state (see `CLAUDE.md` → Execution Flow).
4. Executes **only that step**.
5. Marks the step `✅`, updates `doc/map.md` and, if needed, adds the next steps.

States: ⬜ Pending · 🟡 In progress · ✅ Done · ⛔ Blocked (with reason)

---

## Step 0 — Living documentation structure
- **Status:** ✅ Done (2026-05-30)
- **Goal:** Create `doc/map.md`, `doc/architecture.md`, `doc/sequences.md` and `CLAUDE.md` (+ English mirrors).
- **Definition of done:** The files exist, are mutually consistent, and `README.md` links to `doc/`.
- **Applicable rules:** —

---

## Step 1 — Initialize the monorepo
- **Status:** ⬜ Pending
- **Goal:** Root `package.json` with workspaces, strict TypeScript, ESLint + Prettier and the `apps/` + `packages/` structure.
- **Definition of done:**
  - A base `tsconfig.json` with `strict: true` exists.
  - Lint and format run without error on an empty project.
  - `doc/map.md` reflects the chosen package manager.
- **Applicable rules:** `architecture.md` §1, §5.
- **Required prior decision:** package manager (pnpm/npm/bun).

---

## Step 2 — Initialize Supabase (base schema + RLS)
- **Status:** ⬜ Pending
- **Goal:** Local Supabase project (CLI), first migration with base table(s) and RLS enabled, types generated in `packages/supabase`.
- **Definition of done:**
  - `supabase/migrations` contains the initial migration.
  - Every table has RLS + per-operation policies.
  - TypeScript types generated and committed.
  - `doc/map.md` §5 (data model) updated.
- **Applicable rules:** `architecture.md` §4 in full.

---

## Step 3 — Web app (Next.js) connected to Supabase
- **Status:** ⬜ Pending
- **Goal:** `apps/web` with App Router, server/browser Supabase client from `packages/supabase`, env validation.
- **Definition of done:**
  - Runs locally and reads data respecting RLS (`anon` only on the client).
  - No secrets in the client bundle.
  - `doc/map.md` updated (feature + dependencies).
- **Applicable rules:** `architecture.md` §2, §4.1.

---

## Step 4 — Mobile app (Expo) connected to Supabase
- **Status:** ⬜ Pending
- **Goal:** `apps/mobile` with Expo Router and a Supabase client with secure session storage.
- **Definition of done:**
  - Runs locally and authenticates/reads respecting RLS.
  - Reuses domain from `packages/core`.
  - `doc/map.md` updated.
- **Applicable rules:** `architecture.md` §3, §4.1.

---

## Step 5 — Shared authentication (web + mobile)
- **Status:** ⬜ Pending
- **Goal:** Supabase auth flow working in both apps, with shared domain.
- **Definition of done:**
  - Login/logout/persistent session on web and mobile.
  - RLS policies verified with a different user (isolation).
  - `doc/map.md` updated.
- **Applicable rules:** `architecture.md` §2, §3, §4.3.

---

> Once these steps are complete, the agent adds the next ones here (product features)
> following the same format, and keeps `doc/map.md` in sync.

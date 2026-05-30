# 🗺️ Dynamic Project Map

> **Living document.** The agent MUST update this file at the end of every task.
> If what is written here does not match the real code, the code wins and this map
> must be corrected within the same task. We never work blind: first read this map,
> then validate it against the repository, and only then touch code.
>
> 🇪🇸 Versión en español: [`map.md`](map.md) — keep both in sync.

- **Last updated:** 2026-05-30
- **Updated by:** Claude (initial structure commit)
- **Global project status:** 🟡 Bootstrap (documentation structure only, no application code yet)

---

## 1. Project summary

`harnes-prompt` is the project repository. Target stack:

- **Web:** Next.js (App Router)
- **Mobile:** Expo (React Native + Expo Router)
- **Backend:** Supabase (Postgres + Auth + Storage + Edge Functions)
- **Language:** strict TypeScript across the monorepo

Planned layout (to confirm when the first code is created):

```
.
├── apps/
│   ├── web/        # Next.js (App Router)
│   └── mobile/     # Expo (Expo Router)
├── packages/
│   ├── ui/         # Shared components
│   ├── core/       # Domain logic / shared types
│   └── supabase/   # Client, generated types and shared data-access
├── supabase/       # Migrations, seeds and Supabase CLI config
└── doc/            # Living documentation (this directory)
```

---

## 2. Current state (what exists TODAY)

| Area | Status | Notes |
|------|--------|-------|
| Documentation (`doc/`) | ✅ Done | `map.md`, `architecture.md`, `sequences.md` + English mirrors |
| `CLAUDE.md` (agent rules) | ✅ Done | Living methodology + execution flow |
| Monorepo (workspaces) | ⬜ Pending | No root `package.json` yet |
| Web app (Next.js) | ⬜ Pending | Not started |
| Mobile app (Expo) | ⬜ Pending | Not started |
| Supabase project | ⬜ Pending | No migrations or schema |
| Authentication | ⬜ Pending | — |
| CI/CD | ⬜ Pending | — |

Legend: ✅ Done · 🟡 In progress · ⬜ Pending · ⚠️ Debt/blocked

---

## 3. Features

### Implemented
- _None yet (bootstrap phase)._

### In progress
- _None._

### Pending (backlog)
1. Initialize the monorepo (workspaces + TypeScript + lint/format).
2. Initialize Supabase project + first migration + base RLS.
3. Initialize Next.js web app connected to Supabase.
4. Initialize Expo mobile app connected to Supabase.
5. Shared authentication flow (web + mobile).

> The execution order and detail live in `doc/sequences.en.md`.

---

## 4. Key dependencies

| Dependency | Version | Used in | Status |
|------------|---------|---------|--------|
| _Pending_ | — | — | Filled when installed |

> Rule: whenever an important dependency is added/upgraded/removed, reflect it here.

---

## 5. Data model (Supabase)

| Table | RLS | Description | Status |
|-------|-----|-------------|--------|
| _Pending_ | — | — | — |

> Rule: **every** new table goes here, stating whether RLS is enabled and its policies.

---

## 6. Open decisions and assumptions

- [ ] Confirm monorepo layout and package manager (pnpm / npm / bun).
- [ ] Confirm deployment strategy (Vercel for web, EAS for mobile).
- [ ] Confirm environments (local / staging / production) in Supabase.

---

## 7. Map changelog

| Date | Author | Change |
|------|--------|--------|
| 2026-05-30 | Claude | Created the living documentation structure |

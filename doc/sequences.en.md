# 🧭 Prompt Sequencer (Scrum by iterations)

> **Living, ordered document.** The app is built **prompt by prompt**, like scrum sprints:
> each sprint is a prompt that starts from the **current state** and delivers a small,
> verifiable increment. The agent works **one sprint at a time**, in order, and marks its
> status when done. Sprints are not skipped without agreeing it with the human.
>
> 🇪🇸 Versión en español: [`sequences.md`](sequences.md) — keep both in sync.

---

## 🧩 Methodology (how each prompt is given)

Every prompt/sprint **always** follows this template. State comes first:

```
## CURRENT STATE
(what is done today, copied/summarized from doc/map.md)

## SPRINT GOAL
(the concrete increment I want in this iteration)

## ROLE(S) AND SKILLS
(agents/* and skills/* to use)

## APPLICABLE RULES
(sections of doc/architecture.md that apply)

## DEFINITION OF DONE
(how we know it finished, verifiable)
```

On receiving it, the agent runs the `CLAUDE.md` flow:
**validate state → run sprint → verify → update map → commit.**

Sprint states: ⬜ Pending · 🟡 In progress · ✅ Done · ⛔ Blocked (with reason)

---

## Sprint 0 — Seed: agent logic and living structure
- **Status:** ✅ Done (2026-05-30)
- **Goal:** Lay the "mold": `CLAUDE.md` (+ English), `doc/` (bilingual map/architecture/sequences)
  and the agentic structure `context/ agents/ skills/ outputs/`.
- **Roles/Skills:** Architect · `validar-estado`, `actualizar-mapa`.
- **Definition of done:** Structure created, consistent and synced; `README` links everything.

---

## Sprint 1 — Define the product
- **Status:** ⬜ Pending
- **Goal:** Fill `context/producto.md` and `context/usuarios.md`: what the app is, for whom,
  MVP, scope and platforms (web/mobile and priority).
- **Roles/Skills:** Architect · `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** —
- **Definition of done:**
  - `producto.md` and `usuarios.md` marked ✅.
  - `doc/map.md` §1/§3 reflects the product and MVP.
- **Note:** until this sprint closes, the agent does NOT invent the product.

---

## Sprint 2 — Initialize the monorepo
- **Status:** ⬜ Pending
- **Goal:** Root `package.json` with workspaces, strict TypeScript, ESLint + Prettier and the
  `apps/` + `packages/` structure.
- **Roles/Skills:** Architect + Reviewer · `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** `architecture.md` §1, §5.
- **Prior decision:** package manager (pnpm/npm/bun) → set in `context/stack.md`.
- **Definition of done:** `tsconfig` with `strict:true`; lint/format run clean; `stack.md` and `map.md` updated.

---

## Sprint 3 — Supabase: base schema + RLS
- **Status:** ⬜ Pending
- **Goal:** Supabase project, first migration with base table(s), RLS enabled and types generated.
- **Roles/Skills:** Backend Supabase + Reviewer · `migracion-supabase`, `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** `architecture.md` §4 in full.
- **Definition of done:** initial migration in `supabase/migrations`; RLS + per-operation policies; types in `packages/supabase`; `map.md` §5 updated.

---

## Sprint 4 — Web app (Next.js)
- **Status:** ⬜ Pending
- **Goal:** `apps/web` with App Router, server/browser Supabase client from `packages/supabase`, env validation.
- **Roles/Skills:** Frontend Web + Reviewer · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** `architecture.md` §2, §4.1.
- **Definition of done:** runs locally and reads data respecting RLS; no secrets on the client; `map.md` updated.

---

## Sprint 5 — Mobile app (Expo)
- **Status:** ⬜ Pending
- **Goal:** `apps/mobile` with Expo Router and a Supabase client with secure session.
- **Roles/Skills:** Mobile Expo + Reviewer · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** `architecture.md` §3, §4.1.
- **Definition of done:** runs locally and reads/authenticates respecting RLS; reuses `packages/core`; `map.md` updated.

---

## Sprint 6 — Shared authentication (web + mobile)
- **Status:** ⬜ Pending
- **Goal:** Supabase auth flow in both apps, with shared domain.
- **Roles/Skills:** Backend + Web + Mobile + Reviewer · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Applicable rules:** `architecture.md` §2, §3, §4.3.
- **Definition of done:** login/logout/persistent session on both; RLS isolation verified with another user; `map.md` updated.

---

> From Sprint 6 onward, the next sprints are **product features** (outputs of Sprint 1).
> The agent adds them here with the same template and keeps `doc/map.md` in sync.

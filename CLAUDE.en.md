# CLAUDE.md (English mirror) — Agent Contract

> This is the English mirror of [`CLAUDE.md`](CLAUDE.md). The file Claude Code auto-loads
> is the root `CLAUDE.md` (Spanish, canonical). Keep both versions in sync.
> Documentation language is **Spanish + English**; code is in English.

## 📚 Governing documents (ALWAYS read before acting)

| File | Purpose |
|------|---------|
| `doc/map.md` / `doc/map.en.md` | Real project state: what exists, dependencies, done/pending features, data model. |
| `doc/architecture.md` / `doc/architecture.en.md` | **Strict, binding** technical rules (Next.js, Expo, Supabase, RLS, quality). |
| `doc/sequences.md` / `doc/sequences.en.md` | Sequencer: the step-by-step execution order. |

These three documents + `CLAUDE.md` are the single source of process truth.

---

## 🔄 Living Update Methodology (MANDATORY)

Context stays synchronized and real **without manual intervention**. Therefore:

1. **At the end of EVERY task**, before considering it done, the agent MUST update `doc/map.md` (and `map.en.md`):
   - Move features between Pending → In progress → Implemented.
   - Update the current-state table (§2), dependencies (§4) and data model (§5).
   - Update "Last updated", "Updated by" and add a line to the changelog (§7).
2. **When advancing the plan**, update `doc/sequences.md` (+ `.en`): mark the step (`✅`/`🟡`/`⛔`) and add new steps if any arose.
3. **Code wins over docs.** If validating the real state reveals that `map.md` lies, fix `map.md` in the same task (do not ignore it).
4. **A task is not "done" if the docs are out of sync.** Updating docs is part of the Definition of Done, not optional.
5. **Bilingual sync.** Any change to a doc must be applied to both language versions in the same task so they never drift.

---

## ▶️ Agent Execution Flow (anti-hallucination)

Before touching source code, ALWAYS in this order:

1. **READ** `doc/map.md`, `doc/architecture.md` and `doc/sequences.md`.
2. **VALIDATE against the real repo** what the map claims:
   - Check that files/folders/dependencies actually exist (list, read, search).
   - If the map and reality disagree → fix the map first.
   - **Never assume** something exists or works without verifying it in the repo.
3. **SELECT the step** in `doc/sequences.md` (the first incomplete one), unless the human asks otherwise.
4. **CONFIRM applicable rules** from `architecture.md` for that step.
5. **EXECUTE** only that step, with small focused changes.
6. **VERIFY** (lint/tests/run as applicable). Do not claim success without evidence.
7. **UPDATE** `doc/map.md` and `doc/sequences.md` (Living Methodology, both languages).
8. **COMMIT** descriptively. (Push only when the human asks or the task explicitly requires it.)

> Golden rule: **do not hallucinate state**. If it was not verified in the repo, it is not asserted.

---

## 🚫 Boundaries

- Do not break any `architecture.md` rule unilaterally. If a task requires it, **stop and discuss** with the human.
- No secrets in the client. Never disable RLS.
- Do not skip sequencer steps without agreement.
- When there is relevant ambiguity, ask before proceeding.

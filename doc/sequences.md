# 🧭 Secuenciador de Prompts (Scrum por iteraciones)

> **Documento vivo y ordenado.** La app se construye **prompt a prompt**, como sprints de
> scrum: cada sprint es un prompt que parte del **estado actual** y entrega un incremento
> pequeño y verificable. El agente trabaja **un sprint a la vez**, en orden, y marca su
> estado al terminar. No se saltan sprints sin acordarlo con el humano.
>
> 🇬🇧 English version: [`sequences.en.md`](sequences.en.md) — keep both in sync.

---

## 🧩 Metodología (cómo se da cada prompt)

Cada prompt/sprint sigue **siempre** esta plantilla. Lo primero es el estado:

```
## ESTADO ACTUAL
(qué hay hecho hoy, copiado/resumido de doc/map.md)

## OBJETIVO DE ESTE SPRINT
(el incremento concreto que quiero en esta iteración)

## ROL(ES) Y SKILLS
(agents/* y skills/* que se usan)

## REGLAS APLICABLES
(secciones de doc/architecture.md que aplican)

## DEFINICIÓN DE HECHO
(cómo sabemos que terminó, verificable)
```

Y el agente, al recibirlo, ejecuta el flujo de `CLAUDE.md`:
**validar estado → ejecutar sprint → verificar → actualizar mapa → commit.**

Estados de sprint: ⬜ Pendiente · 🟡 En progreso · ✅ Hecho · ⛔ Bloqueado (con motivo)

---

## Sprint 0 — Seed: lógica del agente y estructura viva
- **Estado:** ✅ Hecho (2026-05-30)
- **Objetivo:** Dejar el "molde": `CLAUDE.md` (+ inglés), `doc/` (map/architecture/sequences
  bilingües) y la estructura agéntica `context/ agents/ skills/ outputs/`.
- **Roles/Skills:** Arquitecto · `validar-estado`, `actualizar-mapa`.
- **Definición de Hecho:** Estructura creada, coherente y sincronizada; `README` enlaza todo.

---

## Sprint 1 — Definir el producto
- **Estado:** ⬜ Pendiente
- **Objetivo:** Rellenar `context/producto.md` y `context/usuarios.md`: qué app es, para quién,
  MVP, alcance y plataformas (web/móvil y prioridad).
- **Roles/Skills:** Arquitecto · `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** —
- **Definición de Hecho:**
  - `producto.md` y `usuarios.md` con estado ✅.
  - `doc/map.md` §1/§3 refleja el producto y el MVP.
- **Nota:** hasta cerrar este sprint, el agente NO inventa el producto.

---

## Sprint 2 — Inicializar el monorepo
- **Estado:** ⬜ Pendiente
- **Objetivo:** `package.json` raíz con workspaces, TypeScript estricto, ESLint + Prettier y
  estructura `apps/` + `packages/`.
- **Roles/Skills:** Arquitecto + Revisor · `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** `architecture.md` §1, §5.
- **Decisión previa:** gestor de paquetes (pnpm/npm/bun) → fijar en `context/stack.md`.
- **Definición de Hecho:** `tsconfig` con `strict:true`; lint/format corren sin error; `stack.md` y `map.md` actualizados.

---

## Sprint 3 — Supabase: esquema base + RLS
- **Estado:** ⬜ Pendiente
- **Objetivo:** Proyecto Supabase, primera migración con tabla(s) base, RLS habilitado y tipos generados.
- **Roles/Skills:** Backend Supabase + Revisor · `migracion-supabase`, `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** `architecture.md` §4 completo.
- **Definición de Hecho:** migración inicial en `supabase/migrations`; RLS + políticas por operación; tipos en `packages/supabase`; `map.md` §5 actualizado.

---

## Sprint 4 — App web (Next.js)
- **Estado:** ⬜ Pendiente
- **Objetivo:** `apps/web` con App Router, cliente Supabase server/browser desde `packages/supabase`, validación de env.
- **Roles/Skills:** Frontend Web + Revisor · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** `architecture.md` §2, §4.1.
- **Definición de Hecho:** arranca en local y lee datos respetando RLS; sin secretos en cliente; `map.md` actualizado.

---

## Sprint 5 — App móvil (Expo)
- **Estado:** ⬜ Pendiente
- **Objetivo:** `apps/mobile` con Expo Router y cliente Supabase con sesión segura.
- **Roles/Skills:** Móvil Expo + Revisor · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** `architecture.md` §3, §4.1.
- **Definición de Hecho:** arranca en local y lee/autentica respetando RLS; reutiliza `packages/core`; `map.md` actualizado.

---

## Sprint 6 — Autenticación compartida (web + móvil)
- **Estado:** ⬜ Pendiente
- **Objetivo:** Flujo de auth de Supabase en ambas apps, con dominio compartido.
- **Roles/Skills:** Backend + Web + Móvil + Revisor · `nueva-feature`, `validar-estado`, `actualizar-mapa`.
- **Reglas aplicables:** `architecture.md` §2, §3, §4.3.
- **Definición de Hecho:** login/logout/sesión persistente en ambos; aislamiento RLS verificado con otro usuario; `map.md` actualizado.

---

> A partir del Sprint 6, los siguientes sprints son **features de producto** (salidas del
> Sprint 1). El agente los añade aquí con la misma plantilla y mantiene `doc/map.md` en sync.

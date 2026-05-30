# 🧭 Secuenciador de Prompts

> **Documento vivo y ordenado.** Es la hoja de ruta paso a paso que el agente lee y
> actualiza. El agente trabaja **un paso a la vez**, en orden, y marca el estado del
> paso al terminar. No se salta pasos sin acordarlo con el humano.
>
> 🇬🇧 English version: [`sequences.en.md`](sequences.en.md) — keep both in sync.

## Cómo se usa este archivo

1. El agente abre `sequences.md` y busca el **primer paso no completado** (`⬜` o `🟡`).
2. Lee el "Objetivo", la "Definición de hecho" y las "Reglas aplicables".
3. Valida el estado real del repo (ver `CLAUDE.md` → Flujo de Ejecución).
4. Ejecuta **solo ese paso**.
5. Marca el paso como `✅`, actualiza `doc/map.md` y, si procede, añade los siguientes pasos.

Estados: ⬜ Pendiente · 🟡 En progreso · ✅ Hecho · ⛔ Bloqueado (con motivo)

---

## Paso 0 — Estructura de documentación viva
- **Estado:** ✅ Hecho (2026-05-30)
- **Objetivo:** Crear `doc/map.md`, `doc/architecture.md`, `doc/sequences.md` y `CLAUDE.md`.
- **Definición de hecho:** Los cuatro archivos existen, son coherentes entre sí y `README.md` enlaza a `doc/`.
- **Reglas aplicables:** —

---

## Paso 1 — Inicializar el monorepo
- **Estado:** ⬜ Pendiente
- **Objetivo:** `package.json` raíz con workspaces, TypeScript estricto, ESLint + Prettier y la estructura `apps/` + `packages/`.
- **Definición de hecho:**
  - Existe `tsconfig.json` base con `strict: true`.
  - Lint y format corren sin error en un proyecto vacío.
  - `doc/map.md` refleja el gestor de paquetes elegido.
- **Reglas aplicables:** `architecture.md` §1, §5.
- **Decisión previa requerida:** gestor de paquetes (pnpm/npm/bun).

---

## Paso 2 — Inicializar Supabase (esquema base + RLS)
- **Estado:** ⬜ Pendiente
- **Objetivo:** Proyecto Supabase local (CLI), primera migración con la(s) tabla(s) base y RLS habilitado, tipos generados en `packages/supabase`.
- **Definición de hecho:**
  - `supabase/migrations` contiene la migración inicial.
  - Toda tabla tiene RLS + políticas por operación.
  - Tipos TypeScript generados y commiteados.
  - `doc/map.md` §5 (modelo de datos) actualizado.
- **Reglas aplicables:** `architecture.md` §4 completo.

---

## Paso 3 — App web (Next.js) conectada a Supabase
- **Estado:** ⬜ Pendiente
- **Objetivo:** `apps/web` con App Router, cliente Supabase server/browser desde `packages/supabase`, validación de env.
- **Definición de hecho:**
  - Arranca en local y lee datos respetando RLS (solo `anon` en cliente).
  - Sin secretos en el bundle del cliente.
  - `doc/map.md` actualizado (feature + dependencias).
- **Reglas aplicables:** `architecture.md` §2, §4.1.

---

## Paso 4 — App móvil (Expo) conectada a Supabase
- **Estado:** ⬜ Pendiente
- **Objetivo:** `apps/mobile` con Expo Router y cliente Supabase con almacenamiento seguro de sesión.
- **Definición de hecho:**
  - Arranca en local y autentica/lee respetando RLS.
  - Reutiliza dominio desde `packages/core`.
  - `doc/map.md` actualizado.
- **Reglas aplicables:** `architecture.md` §3, §4.1.

---

## Paso 5 — Autenticación compartida (web + móvil)
- **Estado:** ⬜ Pendiente
- **Objetivo:** Flujo de auth de Supabase funcionando en ambas apps, con dominio compartido.
- **Definición de hecho:**
  - Login/logout/sesión persistente en web y móvil.
  - Políticas RLS verificadas con un usuario distinto (aislamiento).
  - `doc/map.md` actualizado.
- **Reglas aplicables:** `architecture.md` §2, §3, §4.3.

---

> Cuando se completen estos pasos, el agente añade aquí los siguientes (features de
> producto) siguiendo el mismo formato, y mantiene `doc/map.md` sincronizado.

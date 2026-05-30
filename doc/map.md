# 🗺️ Mapa Dinámico del Proyecto

> **Documento vivo.** El agente DEBE actualizar este archivo al finalizar cada tarea.
> Si lo que está escrito aquí no coincide con el código real, el código manda y este
> mapa debe corregirse en la misma tarea. Nunca se trabaja a ciegas: primero se lee
> este mapa, luego se valida contra el repositorio, y solo entonces se toca código.
>
> 🇬🇧 English version: [`map.en.md`](map.en.md) — keep both in sync.

- **Última actualización:** 2026-05-30
- **Actualizado por:** Claude (commit inicial de estructura)
- **Estado global del proyecto:** 🟡 Bootstrap (solo estructura de documentación, sin código de aplicación)

---

## 1. Resumen del proyecto

`harnes-prompt` es el repositorio del proyecto. Stack objetivo:

- **Web:** Next.js (App Router)
- **Móvil:** Expo (React Native + Expo Router)
- **Backend:** Supabase (Postgres + Auth + Storage + Edge Functions)
- **Lenguaje:** TypeScript estricto en todo el monorepo

Layout (✅ = ya existe · ⬜ = se creará al construir la app):

```
.
├── CLAUDE.md          # ✅ Contrato del agente (canónico) + CLAUDE.en.md
├── context/           # ✅ Contexto estable en piezas pequeñas (producto, usuarios, stack)
├── agents/            # ✅ Roles del agente (arquitecto, backend, web, móvil, revisor)
├── skills/            # ✅ Procedimientos reutilizables (validar-estado, migracion, ...)
├── outputs/           # ✅ Resultados generados por el agente
├── doc/               # ✅ Documentación viva bilingüe (map, architecture, sequences)
├── apps/
│   ├── web/           # ⬜ Next.js (App Router)
│   └── mobile/        # ⬜ Expo (Expo Router)
├── packages/
│   ├── ui/            # ⬜ Componentes compartidos
│   ├── core/          # ⬜ Lógica de dominio / tipos compartidos
│   └── supabase/      # ⬜ Cliente, tipos generados y data-access compartido
└── supabase/          # ⬜ Migraciones, seeds y config de Supabase CLI
```

---

## 2. Estado actual (qué existe HOY)

| Área | Estado | Notas |
|------|--------|-------|
| Documentación (`doc/`) | ✅ Hecho | `map.md`, `architecture.md`, `sequences.md` + espejos en inglés |
| `CLAUDE.md` (reglas del agente) | ✅ Hecho | Metodología viva + flujo de ejecución |
| Estructura agéntica | ✅ Hecho | `context/`, `agents/`, `skills/`, `outputs/` (español) |
| Secuenciador scrum | ✅ Hecho | Sprints prompt-a-prompt en `sequences.md` |
| Producto definido | ⬜ Pendiente | Se define en Sprint 1 (`context/producto.md`) |
| Monorepo (workspaces) | ⬜ Pendiente | Sin `package.json` raíz todavía |
| App web (Next.js) | ⬜ Pendiente | No iniciada |
| App móvil (Expo) | ⬜ Pendiente | No iniciada |
| Proyecto Supabase | ⬜ Pendiente | Sin migraciones ni esquema |
| Autenticación | ⬜ Pendiente | — |
| CI/CD | ⬜ Pendiente | — |

Leyenda: ✅ Hecho · 🟡 En progreso · ⬜ Pendiente · ⚠️ Con deuda/bloqueo

---

## 3. Features

### Implementadas
- _Ninguna todavía (fase bootstrap)._

### En progreso
- _Ninguna._

### Pendientes (backlog → sprints)
1. **Sprint 1:** Definir el producto (`context/producto.md`, `context/usuarios.md`).
2. **Sprint 2:** Inicializar el monorepo (workspaces + TypeScript + lint/format).
3. **Sprint 3:** Supabase + primera migración + RLS base.
4. **Sprint 4:** App web Next.js conectada a Supabase.
5. **Sprint 5:** App móvil Expo conectada a Supabase.
6. **Sprint 6:** Flujo de autenticación compartido (web + móvil).

> El orden y el detalle (formato scrum, prompt-a-prompt) vive en `doc/sequences.md`.

---

## 4. Dependencias clave

| Dependencia | Versión | Usada en | Estado |
|-------------|---------|----------|--------|
| _Pendiente_ | — | — | Se rellena al instalar |

> Regla: cada vez que se añade/sube/elimina una dependencia importante, se refleja aquí.

---

## 5. Modelo de datos (Supabase)

| Tabla | RLS | Descripción | Estado |
|-------|-----|-------------|--------|
| _Pendiente_ | — | — | — |

> Regla: **toda** tabla nueva entra aquí indicando si tiene RLS activado y sus políticas.

---

## 6. Decisiones y supuestos abiertos

- [ ] Confirmar layout monorepo y gestor de paquetes (pnpm / npm / bun).
- [ ] Confirmar estrategia de despliegue (Vercel para web, EAS para móvil).
- [ ] Confirmar entornos (local / staging / producción) en Supabase.

---

## 7. Bitácora de cambios del mapa

| Fecha | Autor | Cambio |
|-------|-------|--------|
| 2026-05-30 | Claude | Creación de la estructura de documentación viva |
| 2026-05-30 | Claude | Estructura agéntica (`context/agents/skills/outputs`) + secuenciador scrum prompt-a-prompt |

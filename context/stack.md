# 🧱 Stack (resumen operativo)

> Resumen rápido de decisiones técnicas para que el agente las tenga a mano **sin leer
> todo** `doc/architecture.md`. Las reglas estrictas y el detalle viven en
> `doc/architecture.md` — este archivo es solo el "qué usamos", no el "cómo".

- **Estado:** 🟡 Parcial (algunas decisiones aún pendientes, marcadas abajo)

## Decididas
- **Web:** Next.js (App Router) → `apps/web`
- **Móvil:** Expo (React Native + Expo Router) → `apps/mobile`
- **Backend:** Supabase (Postgres + Auth + Storage + Edge Functions)
- **Lenguaje:** TypeScript estricto en todo el monorepo
- **Estructura:** monorepo `apps/` + `packages/` (ver `architecture.md` §1)
- **Documentación:** español + inglés en `doc/`; `context/`, `agents/`, `skills/` solo en español
- **RLS:** obligatorio en toda tabla (regla dura, ver `architecture.md` §4.3)

## Pendientes de decidir
- [ ] Gestor de paquetes: **pnpm / npm / bun**
- [ ] Despliegue web: **Vercel** (supuesto)
- [ ] Despliegue móvil: **EAS** (supuesto)
- [ ] Entornos Supabase: local / staging / producción

> Cuando se cierre una decisión pendiente: moverla a "Decididas" y anotar en `doc/map.md`.

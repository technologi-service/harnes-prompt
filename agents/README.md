# 🤖 agents/ — Roles del agente (subagentes)

> Cada archivo define un **rol especializado** que el agente puede adoptar (o que se
> puede lanzar como subagente). La idea: en cada tarea actúa **un rol enfocado**, que
> carga solo el contexto y las skills que le tocan, para no abrumarse.

## Roles disponibles

| Rol | Archivo | Se encarga de |
|-----|---------|---------------|
| 🏛️ Arquitecto | `arquitecto.md` | Planificar, hacer cumplir `architecture.md`, decidir estructura |
| 🗄️ Backend Supabase | `backend-supabase.md` | Esquema, migraciones, RLS, Edge Functions, tipos |
| 🌐 Frontend Web | `frontend-web.md` | Next.js (App Router) en `apps/web` |
| 📱 Móvil Expo | `mobile-expo.md` | Expo (Expo Router) en `apps/mobile` |
| 🔍 Revisor | `revisor.md` | Calidad, seguridad, checklist de RLS y secretos |

## Cómo trabaja un rol

1. Lee **siempre** primero: `CLAUDE.md`, `doc/map.md` y el `context/` relevante.
2. Aplica el `skills/validar-estado.md` (anti-alucinación) antes de tocar código.
3. Usa las skills indicadas en su archivo.
4. Al terminar, ejecuta `skills/actualizar-mapa.md` (Metodología Viva).

> Un rol nunca rompe las reglas de `doc/architecture.md`. Si necesita hacerlo, se detiene
> y lo debate con el humano.

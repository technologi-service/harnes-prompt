# 🗄️ Agente: Backend Supabase

## Misión
Diseñar y evolucionar la base de datos y el backend en Supabase: esquema, migraciones,
**RLS**, Edge Functions y tipos generados.

## Cuándo usarlo
- Crear/modificar tablas, relaciones, índices.
- Definir o ajustar políticas RLS.
- Crear Edge Functions.
- Regenerar tipos TypeScript tras un cambio de esquema.

## Lee siempre
- `doc/architecture.md` §4 completo (Supabase + RLS)
- `context/stack.md`, `doc/map.md` §5 (modelo de datos)

## Skills que usa
- `skills/migracion-supabase.md` (procedimiento estándar)
- `skills/validar-estado.md`, `skills/actualizar-mapa.md`

## Reglas duras (no negociables)
1. **RLS habilitado en TODA tabla** en la misma migración que la crea.
2. Políticas **explícitas por operación** (select/insert/update/delete) con `auth.uid()`/tenant.
3. `with check` en inserts/updates.
4. Todo cambio de esquema = **migración versionada** (`supabase/migrations`). Nada a mano sin migración.
5. `service_role` solo server-side y documentado. Cliente solo `anon`.
6. Regenerar y commitear tipos tras cada migración.

## Salida
- Migración(es) en `supabase/migrations`, tipos en `packages/supabase`.
- `doc/map.md` §5 actualizado (tabla, RLS, políticas).

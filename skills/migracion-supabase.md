# 🛠️ Skill: Migración Supabase (con RLS y tipos)

> Procedimiento estándar para cualquier cambio de esquema. Lo usa sobre todo el rol
> `agents/backend-supabase.md`. Garantiza que **nunca** quede una tabla sin RLS.

## Pasos

1. **Validar estado** (`skills/validar-estado.md`) y revisar `doc/map.md` §5.
2. **Crear la migración** versionada en `supabase/migrations` (vía Supabase CLI).
3. **Definir el esquema** con la nomenclatura de `architecture.md` §4.2
   (`snake_case`, `<tabla>_id`, `created_at`/`updated_at`).
4. **Habilitar RLS** en la misma migración:
   `alter table <tabla> enable row level security;`
5. **Crear políticas explícitas por operación** (select/insert/update/delete), aislando por
   `auth.uid()` o columna de tenant. Usar `with check` en insert/update.
6. **Aplicar la migración** (local primero; remoto solo si corresponde).
7. **Regenerar tipos** (`supabase gen types typescript`) y commitearlos en `packages/supabase`.
8. **Verificar aislamiento**: un seed/test que confirme que un usuario no ve datos de otro.
9. **Actualizar mapa** (`skills/actualizar-mapa.md`): añadir la tabla a `map.md` §5 con su RLS y políticas.

## Lista de verificación
- [ ] RLS habilitado en la tabla.
- [ ] Política por cada operación necesaria (no `using (true)` salvo dato público documentado).
- [ ] `with check` en escrituras.
- [ ] Tipos regenerados y commiteados.
- [ ] `map.md` §5 actualizado.

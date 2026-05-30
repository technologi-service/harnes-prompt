<!-- ============================================================
  skills/migracion-supabase.md — FLUJO DE MIGRACIÓN DE DB
  ============================================================
  Procedimiento estándar para cualquier cambio de esquema
  en Supabase. Incluye RLS obligatorio y regeneración de tipos.
  
  Aplicable a partir del Sprint 2 cuando Supabase esté configurado.
  ============================================================ -->

# 🗄️ Skill: Migración Supabase

> **Cuándo:** Cada vez que se necesite crear o modificar tablas, columnas, índices o funciones en la base de datos.
> **Regla fundamental:** Todo cambio de esquema = migración versionada. Nada a mano.

---

## Pasos

<!-- Paso 1: Saber qué hay antes de tocar la DB -->
### 1. Validar estado actual
- Ejecutar `skills/validar-estado.md`.
- Revisar `doc/estado.md` §5 (modelo de datos) → ¿Qué tablas existen?
- Revisar migraciones existentes en `supabase/migrations/`.

<!-- Paso 2: Crear la migración versionada -->
### 2. Crear migración

```bash
# Generar archivo de migración con nombre descriptivo
supabase migration new descripcion_del_cambio
```

- El archivo se crea en `supabase/migrations/YYYYMMDDHHMMSS_descripcion.sql`.
- Escribir el SQL de la migración.

<!-- Paso 3: Seguir las convenciones de esquema -->
### 3. Convenciones de esquema

- **Nombres:** snake_case, singular (ej: `product`, `order_item`).
- **Columnas obligatorias en toda tabla:**
  - `id` (uuid, default `gen_random_uuid()`, primary key)
  - `created_at` (timestamptz, default `now()`)
  - `updated_at` (timestamptz, default `now()`)
- **Multi-tenant:** `tenant_id` (uuid, FK a `tenant.id`) obligatorio en toda tabla de datos de negocio.
- **Relaciones:** FK explícitas con `ON DELETE` definido.

<!-- Paso 4: RLS OBLIGATORIO — en la misma migración -->
### 4. Habilitar RLS (CRÍTICO)

En la **misma migración** que crea la tabla:

```sql
-- Habilitar RLS
ALTER TABLE nombre_tabla ENABLE ROW LEVEL SECURITY;

-- Política SELECT: solo datos de su tenant
CREATE POLICY "tenant_select" ON nombre_tabla
  FOR SELECT USING (tenant_id = auth.jwt() ->> 'tenant_id');

-- Política INSERT: solo en su tenant, con verificación
CREATE POLICY "tenant_insert" ON nombre_tabla
  FOR INSERT WITH CHECK (tenant_id = auth.jwt() ->> 'tenant_id');

-- Política UPDATE: solo datos de su tenant
CREATE POLICY "tenant_update" ON nombre_tabla
  FOR UPDATE USING (tenant_id = auth.jwt() ->> 'tenant_id')
  WITH CHECK (tenant_id = auth.jwt() ->> 'tenant_id');

-- Política DELETE: solo datos de su tenant
CREATE POLICY "tenant_delete" ON nombre_tabla
  FOR DELETE USING (tenant_id = auth.jwt() ->> 'tenant_id');
```

> ⚠️ **NUNCA** crear una tabla sin RLS. Si se descubre una tabla sin RLS, es un bug de seguridad crítico.

<!-- Paso 5: Aplicar y probar -->
### 5. Aplicar migración

```bash
# Aplicar en local primero
supabase db reset   # O: supabase migration up

# Verificar que la migración se aplicó correctamente
supabase db diff    # No debería mostrar diferencias
```

<!-- Paso 6: Regenerar tipos TypeScript -->
### 6. Regenerar tipos

```bash
# Generar tipos actualizados desde el esquema
supabase gen types typescript --local > packages/types/database.types.ts
```

- Commitear los tipos regenerados junto con la migración.
- Verificar que los tipos se importan correctamente en packages/core.

<!-- Paso 7: Testear aislamiento multi-tenant -->
### 7. Verificar aislamiento

- Un usuario del tenant A **NO puede** ver/modificar datos del tenant B.
- Probar con queries directas usando diferentes tokens de tenant.
- Si el aislamiento falla → **la migración no se puede mergear**.

<!-- Paso 8: Actualizar documentación -->
### 8. Actualizar docs

- Ejecutar `skills/actualizar-estado.md`.
- Actualizar `doc/estado.md` §5 con el nuevo modelo de datos.
- Registrar en la bitácora.

---

## Checklist de verificación

- [ ] Migración versionada (no cambio manual).
- [ ] Convenciones de naming respetadas.
- [ ] `tenant_id` en toda tabla de datos de negocio.
- [ ] RLS habilitado en la misma migración.
- [ ] Políticas explícitas por operación (SELECT, INSERT, UPDATE, DELETE).
- [ ] `WITH CHECK` en INSERT y UPDATE.
- [ ] Tipos regenerados y commiteados.
- [ ] Aislamiento multi-tenant verificado.
- [ ] `doc/estado.md` §5 actualizado.

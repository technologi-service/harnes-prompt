<!-- ============================================================
  skills/nueva-feature.md — FLUJO PARA AÑADIR FUNCIONALIDAD
  ============================================================
  Procedimiento punta a punta para implementar una feature
  completa. Desde la validación inicial hasta la actualización
  de documentación.
  
  Aplicable a partir del Sprint 4+ cuando ya exista código.
  ============================================================ -->

# ✨ Skill: Nueva Feature

> **Cuándo:** Al implementar cualquier funcionalidad nueva (a partir de Sprint 4+).
> **Objetivo:** Garantizar que cada feature se implementa de forma completa, segura y documentada.

---

## Pasos

<!-- Paso 1: Saber dónde estamos antes de empezar -->
### 1. Validar estado
Ejecutar `skills/validar-estado.md` completa.

<!-- Paso 2: Verificar que la feature encaja con el producto -->
### 2. Encajar en el producto
- Revisar `context/producto.md` → ¿La feature está en el MVP o es post-MVP?
- Revisar `context/usuarios.md` → ¿Qué actores se benefician?
- Si la feature no está definida → **preguntar al humano** antes de avanzar.

<!-- Paso 3: Planificar la implementación -->
### 3. Planificar
- Descomponer la feature en pasos pequeños y verificables.
- Identificar qué capas se tocan: datos (Supabase), dominio (packages/core), UI (web/móvil).
- Identificar las reglas de `doc/arquitectura.md` que aplican.

<!-- Paso 4: Implementar la capa de datos si hace falta -->
### 4. Datos (si aplica)
- Crear migración versionada → usar `skills/migracion-supabase.md`.
- RLS habilitado en la misma migración.
- Políticas explícitas por operación.
- Regenerar tipos.

<!-- Paso 5: Lógica de dominio compartida -->
### 5. Dominio
- Implementar lógica en `packages/core` (TypeScript puro, sin dependencias de framework).
- Validación de inputs con Zod.
- Manejar errores explícitamente (no try/catch genéricos).

<!-- Paso 6: Interfaz de usuario -->
### 6. UI (Web y/o Móvil)
- **Web:** Server Components por defecto. Mutaciones vía Server Actions.
- **Móvil:** Componentes nativos con lógica compartida de packages/core.
- Sin lógica de negocio en componentes → va en packages/core.

<!-- Paso 7: Verificar que funciona -->
### 7. Verificar
- Lint y format pasan sin errores.
- Tests para la lógica crítica.
- La app arranca sin errores.
- Si tiene RLS → testear aislamiento multi-tenant.

<!-- Paso 8: Actualizar documentación -->
### 8. Actualizar docs
Ejecutar `skills/actualizar-estado.md` completa.

---

## Definición de Hecho mínima

- [ ] La feature funciona según lo esperado.
- [ ] RLS activo si hay tablas nuevas.
- [ ] Inputs validados con Zod.
- [ ] Sin secretos en el cliente.
- [ ] Lint/format pasan.
- [ ] `doc/estado.md` y `doc/secuencias.md` actualizados.
- [ ] Commit descriptivo.

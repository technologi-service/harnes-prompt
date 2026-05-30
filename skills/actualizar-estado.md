<!-- ============================================================
  skills/actualizar-estado.md — METODOLOGÍA VIVA
  ============================================================
  Se ejecuta al FINAL de cada tarea.
  Objetivo: mantener la documentación sincronizada con la
  realidad del repo. Sin esto, los docs se desactualizan
  y el framework pierde su valor.
  
  Regla: "Una tarea NO está hecha si los docs no se actualizaron."
  ============================================================ -->

# 📝 Skill: Actualizar Estado

> **Cuándo:** Al FINAL de cada tarea, ANTES de dar la tarea por terminada.
> **Por qué:** Mantener la documentación viva sincronizada con el repo real.
> **Regla:** Una tarea NO está completa sin esta actualización.

---

## Pasos

<!-- Paso 1: Actualizar el estado del proyecto -->
### 1. Actualizar `doc/estado.md`

- **§2 — Estado actual:** Actualizar la tabla de componentes.
  - Cambiar estados: ⬜ → 🟡 → ✅ según corresponda.
  - Añadir componentes nuevos si se crearon.
  - Actualizar rutas si cambiaron.

- **§3 — Features:** Mover features entre las listas.
  - Pendiente → En progreso (si se empezó pero no se terminó).
  - En progreso → Implementada (si se completó).

- **§4 — Dependencias:** Añadir nuevas dependencias instaladas.

- **§5 — Modelo de datos:** Actualizar si se crearon/modificaron tablas.
  - Incluir: nombre, columnas clave, relaciones, RLS (sí/no).

- **§7 — Bitácora:** Añadir una línea con:
  - Fecha
  - Quién (agente/humano)
  - Qué se hizo (resumen en una línea)

- **Header:** Actualizar "Última actualización" y "Actualizado por".

<!-- Paso 2: Actualizar el secuenciador -->
### 2. Actualizar `doc/secuencias.md`

- Marcar los pasos completados con ✅.
- Marcar pasos en progreso con 🟡.
- Marcar pasos bloqueados con ⛔ (y explicar por qué).
- Si surgieron pasos nuevos durante la ejecución, añadirlos.
- Si se completó un sprint entero, marcar el sprint como ✅.

<!-- Paso 3: Actualizar contexto estable si cambió -->
### 3. Actualizar `context/*` (si aplica)

- Si se cerró una **decisión técnica** → mover de "Pendientes" a "Decididas" en `context/stack.md`.
- Si cambió algo del **producto** → actualizar `context/producto.md`.
- Si cambió algo de los **actores** → actualizar `context/usuarios.md`.

<!-- Paso 4: Verificar coherencia -->
### 4. Verificar coherencia

Antes de dar por terminado, comprobar:
- [ ] `doc/estado.md` refleja lo que realmente existe en el repo.
- [ ] `doc/secuencias.md` tiene los pasos correctamente marcados.
- [ ] No hay contradicciones entre `estado.md` y `secuencias.md`.
- [ ] La bitácora tiene una entrada para esta tarea.

---

## Resultado esperado

Al terminar esta skill:
- ✅ `doc/estado.md` refleja el estado REAL post-tarea.
- ✅ `doc/secuencias.md` tiene el progreso marcado.
- ✅ `context/*` actualizado si hubo cambios estables.
- ✅ Todo coherente, sin contradicciones.

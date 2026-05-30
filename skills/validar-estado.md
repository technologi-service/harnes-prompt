<!-- ============================================================
  skills/validar-estado.md — PROCEDIMIENTO ANTI-ALUCINACIÓN
  ============================================================
  Se ejecuta al INICIO de cada tarea.
  Objetivo: garantizar que el agente trabaja con información
  REAL, no con suposiciones o datos desactualizados.
  
  Principio: "Si no lo verificaste en el repo, no lo afirmes."
  ============================================================ -->

# 🔍 Skill: Validar Estado

> **Cuándo:** Al inicio de CADA tarea, ANTES de tocar código.
> **Por qué:** Evitar alucinaciones. No asumir que algo existe sin comprobarlo.
> **Duración:** 2-5 minutos. No saltarse por pereza.

---

## Pasos

<!-- Paso 1: Leer los documentos que definen el estado conocido -->
### 1. Leer documentos rectores

- Leer `doc/estado.md` → ¿Qué dice que existe?
- Leer el sprint actual de `doc/secuencias.md` → ¿Qué debería existir ya?
- Leer los `context/*` relevantes para la tarea.

<!-- Paso 2: Verificar físicamente que el repo coincide -->
### 2. Verificar contra el repo real

- **Listar** carpetas y archivos del repo (no asumir, listar).
- **Comprobar** que lo que `doc/estado.md` dice que existe, realmente existe.
- **Verificar** dependencias en `package.json` (si existe).
- **Verificar** migraciones en `supabase/migrations/` (si existen).
- **Verificar** que los archivos mencionados tienen contenido real (no plantillas vacías).

<!-- Paso 3: Si hay discrepancia, corregir el documento PRIMERO -->
### 3. Resolver discrepancias

Si `doc/estado.md` dice algo que **no es verdad**:
- ⚠️ **El código manda.** El documento está mal, no el repo.
- Corregir `doc/estado.md` ANTES de continuar con la tarea.
- Registrar la corrección en la bitácora (§7).

Si el repo tiene algo que `doc/estado.md` **no menciona**:
- Añadir el componente a la tabla de estado (§2).
- Investigar si es relevante para la tarea actual.

<!-- Paso 4: Confirmar que sabemos qué hacer -->
### 4. Confirmar paso a ejecutar

- Identificar el sprint activo en `doc/secuencias.md`.
- Identificar el primer paso NO completado dentro del sprint.
- Confirmar las reglas de `doc/arquitectura.md` que aplican.
- Si hay ambigüedad → **preguntar al humano antes de avanzar**.

---

## Resultado esperado

Al terminar esta skill, el agente debe poder afirmar con certeza:
- ✅ Sé qué existe en el repo (lo verifiqué).
- ✅ `doc/estado.md` refleja la realidad (lo corregí si hacía falta).
- ✅ Sé cuál es el siguiente paso a ejecutar.
- ✅ Conozco las reglas que aplican.

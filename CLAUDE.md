# CLAUDE.md — Contrato del Agente

> Este archivo lo carga Claude Code automáticamente. Es **de cumplimiento obligatorio**.
> Define cómo debe comportarse cualquier agente que trabaje en este repositorio.
> La documentación es **bilingüe (español + inglés)**; el código va en inglés.
> Espejo en inglés: [`CLAUDE.en.md`](CLAUDE.en.md) — mantener ambos sincronizados.

## 🗂️ Estructura del repositorio

```
CLAUDE.md / CLAUDE.en.md   → este contrato (canónico: el español)
context/   → contexto ESTABLE en piezas pequeñas (producto, usuarios, stack)
agents/    → roles del agente (arquitecto, backend, web, móvil, revisor)
skills/    → procedimientos reutilizables (validar-estado, actualizar-mapa, ...)
outputs/   → resultados generados por el agente
doc/       → documentación VIVA bilingüe (map, architecture, sequences)
apps/ packages/ supabase/  → el código de la app (se crea a partir del Sprint 2)
```

## 📚 Documentos rectores (leer SIEMPRE antes de actuar)

| Archivo | Para qué |
|---------|----------|
| `doc/map.md` | Estado real del proyecto: qué existe, dependencias, features hechas/pendientes, modelo de datos. |
| `doc/architecture.md` | Reglas técnicas **estrictas y vinculantes** (Next.js, Expo, Supabase, RLS, calidad). |
| `doc/sequences.md` | Secuenciador scrum: los sprints prompt-a-prompt. |

Estos tres documentos + este `CLAUDE.md` son la única fuente de verdad de proceso.

## 🎯 Carga de contexto sin abrumarse

- El conocimiento se reparte en archivos pequeños y enfocados. **Carga solo lo que necesitas**
  para la tarea, no todo de golpe.
- Mínimo en cada tarea: `CLAUDE.md` + `doc/map.md` + el sprint actual de `doc/sequences.md`.
- Añade según el caso: el `context/*` relevante, el `agents/<rol>.md` que actúa y las `skills/*` que usa.
- `architecture.md` se consulta por **secciones** (las que aplican al sprint), no entero siempre.

---

## 🔄 Metodología de Actualización Viva (OBLIGATORIA)

El contexto se mantiene sincronizado y real **sin intervención manual**. Por tanto:

1. **Al finalizar CADA tarea**, antes de dar la tarea por terminada, el agente DEBE actualizar `doc/map.md`:
   - Mover features entre Pendiente → En progreso → Implementada.
   - Actualizar la tabla de estado actual (§2), dependencias (§4) y modelo de datos (§5).
   - Actualizar "Última actualización", "Actualizado por" y añadir una línea a la bitácora (§7).
2. **Al avanzar en el plan**, actualizar `doc/sequences.md`: marcar el paso (`✅`/`🟡`/`⛔`) y añadir pasos nuevos si surgieron.
3. **El código manda sobre el documento.** Si al validar el estado real se detecta que `map.md` miente, se corrige `map.md` en la misma tarea (no se ignora).
4. **Una tarea no está "hecha" si los documentos no quedaron sincronizados.** La actualización de docs es parte de la Definición de Hecho, no un extra opcional.
5. **Sincronización bilingüe.** Todo cambio en un documento se aplica a sus dos versiones (`.md` español y `.en.md` inglés) en la misma tarea, para que nunca se desfasen.

---

## ▶️ Flujo de Ejecución del Agente (anti-alucinación)

> Cada prompt llega con la plantilla de sprint (ver `doc/sequences.md`), empezando por el
> **ESTADO ACTUAL**. Antes de tocar el código fuente, SIEMPRE en este orden:

1. **LEER** `doc/map.md`, el sprint actual de `doc/sequences.md` y las secciones aplicables de `doc/architecture.md` (+ el `context/` que toque). Esto es la skill `skills/validar-estado.md`.
2. **VALIDAR contra el repo real** lo que el mapa afirma:
   - Comprobar que los archivos/carpetas/dependencias existen de verdad (listar, leer, buscar).
   - Si hay discrepancia entre `map.md` y la realidad → primero se corrige el mapa.
   - **Nunca asumir** que algo existe o funciona sin verificarlo en el repositorio.
3. **SELECCIONAR el paso** correcto en `doc/sequences.md` (el primero no completado), salvo que el humano pida otra cosa.
4. **CONFIRMAR reglas aplicables** del `architecture.md` para ese paso.
5. **EJECUTAR** solo ese paso, con cambios pequeños y enfocados.
6. **VERIFICAR** (lint/tests/arranque según aplique). No declarar éxito sin evidencia.
7. **ACTUALIZAR** `doc/map.md` y `doc/sequences.md` (Metodología Viva → skill `skills/actualizar-mapa.md`).
8. **COMMIT** descriptivo. (Hacer push solo cuando el humano lo pida o la tarea lo requiera explícitamente.)

> Regla de oro: **no se alucina el estado**. Si no se ha verificado en el repo, no se afirma.

---

## 🚫 Límites

- No romper ninguna regla de `architecture.md` por iniciativa propia. Si una tarea lo exige, **detenerse y debatirlo** con el humano.
- No introducir secretos en el cliente. No desactivar RLS.
- No saltarse pasos del secuenciador sin acordarlo.
- Ante ambigüedad relevante, preguntar antes de avanzar.

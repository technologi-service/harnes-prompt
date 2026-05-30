> **Obligatorio.** Define cómo debe comportarse cualquier agente IA en este repositorio.
>
> Agnóstico: no depende de ningún agente específico y respeta la jerarquía (Humano = Arquitecto / IA = Ingeniero de Ejecución).
>
> La documentación y comentarios van en **español**; el código fuente y commits en **inglés**.

🗂️ Estructura del repositorio
------------------------------

```
README.md              → punto de entrada (leer PRIMERO)
AGENT.md               → este contrato (leer SEGUNDO)
context/               → contexto ESTABLE (producto, usuarios, stack)
doc/                   → documentación VIVA (arquitectura, estado, secuencias)
prompts/               → secuencias de prompts organizadas por carpetas de roles/pasos
skills/                → procedimientos reutilizables del agente
apps/ packages/        → código de la app (output del trabajo de los sprints)
```

---

📚 Documentos rectores (leer SIEMPRE antes de actuar)
-----------------------------------------------------

| **Archivo** | **Para qué** | **Cuándo** |
| --- | --- | --- |
| `context/producto.md` | Qué construimos, para quién, funcionalidades MVP | **Primera lectura obligatoria de cada sesión** |
| `context/stack.md` | Decisiones técnicas cerradas y pendientes | **Primera lectura obligatoria de cada sesión** |
| `context/usuarios.md` | Actores, permisos, flujos de trabajo | **Primera lectura obligatoria de cada sesión** |
| `doc/estado.md` | Estado real: sprints, features, DB, changelog | Después de validar el contexto |
| `doc/arquitectura.md` | Reglas técnicas **estrictas y vinculantes** | Antes de escribir cualquier código |
| `doc/secuencias.md` | Secuencia paso a paso de prompts por sprint | Para seleccionar el siguiente paso |
| `prompts/**` | Los prompts de ejecución con plantilla estándar | Solo cuando el contexto está completo |

---

🔁 Flujo de Ejecución del Agente (OBLIGATORIO — sin excepciones)
----------------------------------------------------------------

### FASE 1 — Lectura del Contexto (siempre primero)

> **No se toca código, estado ni prompts hasta completar esta fase.**

1. **Leer `context/producto.md`** — entender QUÉ se está construyendo y PARA QUIÉN.
2. **Leer `context/stack.md`** — identificar decisiones cerradas y decisiones **pendientes**.
3. **Leer `context/usuarios.md`** — entender los actores, permisos y flujos del sistema.

Al terminar esta lectura, el agente debe poder responder:
- ✅ ¿Cuál es la propuesta de valor y el MVP?
- ✅ ¿Qué decisiones técnicas están cerradas y cuáles siguen abiertas?
- ✅ ¿Quiénes son los actores y qué puede hacer cada uno?

---

### FASE 2 — Investigación y Aclaración (si hay gaps)

> Si existen decisiones pendientes en `context/stack.md` o información incompleta en `context/producto.md` que sea **bloqueante para el siguiente paso**, el agente DEBE detenerse y preguntar al humano antes de avanzar.

**Checklist de gaps bloqueantes:**
- [ ] ¿Hay decisiones de stack que afectan directamente el siguiente prompt? (ej: gestor de paquetes para Sprint 2)
- [ ] ¿La zona de lanzamiento y el país están definidos? (afecta decisión de pasarela de pagos y mapas)
- [ ] ¿Están definidas las decisiones de despliegue? (Vercel / EAS Build)
- [ ] ¿Está decidida la pasarela de pagos? (Stripe / MercadoPago / local)
- [ ] ¿Está decidido el proveedor de mapas? (Mapbox / Google Maps)

Si algún gap es bloqueante → **preguntar al humano, actualizar el archivo de contexto correspondiente, y solo entonces continuar.**

Si los gaps no bloquean el siguiente paso → registrar que están pendientes y continuar.

---

### FASE 3 — Validación del Estado Real

> Solo después de tener el contexto sólido se revisa el estado del repo.

4. **Leer `doc/estado.md`** — ¿qué dice que existe?
5. **Verificar físicamente el repo** (listar carpetas, archivos, dependencias) — no asumir.
6. **Resolver discrepancias** — si `doc/estado.md` no refleja la realidad, corregirlo PRIMERO.
7. **Leer el sprint activo de `doc/secuencias.md`** — identificar el primer paso no completado.

---

### FASE 4 — Confirmación de Reglas

8. **Leer las secciones aplicables de `doc/arquitectura.md`** para el paso a ejecutar.
9. Si hay ambigüedad relevante → **preguntar antes de avanzar**.

---

### FASE 5 — Ejecución

10. **Localizar el prompt** correspondiente en `prompts/` (si no existe, crearlo primero).
11. **Ejecutar** la secuencia del prompt. **Restricción:** máximo 3 objetivos específicos por bloque.
12. **Verificar** de forma real: compilación, lint, tests o conectividad. No declarar éxito sin evidencia.

---

### FASE 6 — Cierre y Sincronización

13. **Actualizar `doc/estado.md`** (skill: `skills/actualizar-estado.md`):
    - Mover features entre Pendiente → En progreso → Implementada.
    - Actualizar tabla de estado, dependencias, modelo de datos.
    - Añadir entrada en la bitácora (§7).
14. **Actualizar `doc/secuencias.md`**: marcar el paso con ✅, 🟡 o ⛔.
15. **Commit** en inglés con el número de prompt ejecutado.

> **Una tarea NO está "hecha" si los documentos no quedaron sincronizados.**

---

🎯 Carga de contexto sin abrumarse
----------------------------------

- **Mínimo en cada sesión:** `context/` completo + `doc/estado.md` + prompt activo.
- **Según la tarea:** añade el `context/` relevante, la carpeta de `prompts/` y las `skills/*` que apliquen.
- `doc/arquitectura.md` se consulta **por secciones** — solo las que aplican al prompt.

---

🚫 Límites
----------

- **No romper** ninguna regla de `doc/arquitectura.md` por iniciativa propia. Si una tarea lo exige, detenerse y consultar con el humano.
- **No introducir** secretos en el cliente. No desactivar RLS.
- **No saltarse** la Fase 1 (lectura del contexto). Nunca. Aunque parezca obvio.
- **No saltarse** la Fase 2 (aclaración). Si hay un gap bloqueante, preguntar antes de avanzar.
- **No acumular** más de 3 objetivos complejos en un solo bloque de ejecución.
- **No inventar** datos de producto, usuarios o decisiones técnicas si no están definidos en `context/`. Preguntar.
- **No ejecutar prompts** si las decisiones de `context/stack.md` que ese prompt necesita siguen como "Pendientes".

> **Regla de oro: no se alucina el estado ni el contexto.** Si no está en `context/`, no se asume. Si no se verificó en el repo, no se afirma.

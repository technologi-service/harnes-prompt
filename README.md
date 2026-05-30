# 🚀 harnes-prompt

**Framework agéntico de documentación viva** para construir una plataforma SaaS multi-tenant de logística e hiperlocalización.

La idea central de este repositorio es que **los documentos no describen el proyecto, sino que lo dirigen**. Cada tarea comienza validando el estado real del repositorio contra la documentación, se ejecuta siguiendo reglas estrictas, y finaliza actualizando la documentación para mantener el sistema 100% sincronizado. El agente de IA nunca afirma lo que no ha verificado empíricamente.

---

## 👤 Si eres humano (Arquitecto / Desarrollador)

Para entender el proyecto y comenzar a colaborar, sigue esta ruta de lectura sugerida:

1. **Entiende el negocio:** Lee [`context/producto.md`](context/producto.md) para conocer la visión, el MVP y los actores de la plataforma.
2. **Conoce el stack:** Lee [`context/stack.md`](context/stack.md) para revisar las decisiones tecnológicas tomadas.
3. **Revisa la arquitectura:** Lee [`doc/arquitectura.md`](doc/arquitectura.md) para entender las reglas y convenciones técnicas vinculantes (RLS, TypeScript, Monorepo).
4. **Consulta el estado:** Lee [`doc/estado.md`](doc/estado.md) para ver en qué punto de desarrollo nos encontramos.

---

## 🤖 Si eres un agente IA

> [!IMPORTANT]
> **Regla de oro: No afirmes lo que no has verificado.** Si no lo has leído o comprobado en el repositorio real, para ti no existe.

Este repositorio opera bajo una jerarquía estricta donde tú eres el **Ingeniero de Ejecución** y el humano es el **Arquitecto**. Tu comportamiento y flujo de trabajo están completamente regidos por tu contrato.

### Ruta obligatoria de ejecución:
1. **Lee tu contrato:** Tu primera acción en cada sesión debe ser leer [`AGENT.md`](AGENT.md) en su totalidad. Define tus límites, metodología y flujo.
2. **Valida el estado real:** Utiliza la skill [`skills/validar-estado.md`](skills/validar-estado.md) para confirmar que la documentación de [`doc/estado.md`](doc/estado.md) coincide perfectamente con la realidad física del código.
3. **Ejecuta con límites:** Desarrolla el paso técnico correspondiente de forma atómica. Recuerda que no debes acumular más de 3 objetivos complejos en una misma iteración.
4. **Verifica empíricamente:** Corre pruebas, formateadores y compilaciones locales. No declares éxito sin evidencia real de funcionamiento.
5. **Sincroniza la documentación:** Utiliza [`skills/actualizar-estado.md`](skills/actualizar-estado.md) al finalizar tu tarea para actualizar el estado vivo del repositorio antes de entregar el trabajo.

---

## 🗂️ Estructura del Repositorio

El conocimiento del proyecto está modularizado en archivos pequeños y altamente enfocados:

| Directorio / Archivo | Responsabilidad | Audiencia |
| :--- | :--- | :--- |
| [`README.md`](README.md) | Punto de entrada general e inducción al proyecto. | Humanos / Agentes |
| [`AGENT.md`](AGENT.md) | Contrato obligatorio del agente: comportamiento, flujo y límites. | Agentes IA |
| [`context/`](context/) | Información de negocio y stack estable (no cambia frecuentemente). | Ambos |
| [`doc/`](doc/) | Documentación viva: arquitectura vinculante y estado actual de sprints. | Ambos |
| [`skills/`](skills/) | Procedimientos reutilizables ("Cómo hacer") para el agente. | Agentes IA |
| `apps/`, `packages/` | Código fuente de la plataforma (web dashboard, app móvil, core). | Ambos |

---

## 📚 Documentación Rectora

| Documento | Propósito | Cuándo usarlo |
| :--- | :--- | :--- |
| [`AGENT.md`](AGENT.md) | Reglas de comportamiento y flujos anti-alucinación de la IA. | Al inicio de cada sesión |
| [`context/producto.md`](context/producto.md) | Visión, requerimientos, historias de usuario y MVP del negocio. | Para entender el alcance del producto |
| [`context/stack.md`](context/stack.md) | Stack técnico oficial y base de decisiones de software. | Al planificar una implementación |
| [`doc/arquitectura.md`](doc/arquitectura.md) | Reglas técnicas estrictas (RLS, Types, Monorepo, Seguridad). | Antes de escribir cualquier línea de código |
| [`doc/estado.md`](doc/estado.md) | Mapa en tiempo real de features implementadas, base de datos y bitácora. | Al validar o actualizar el progreso |

---

*Proyecto privado. Todos los derechos reservados.*

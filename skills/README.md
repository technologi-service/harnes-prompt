<!-- ============================================================
  skills/README.md — ÍNDICE DE SKILLS
  ============================================================ -->

# 🛠️ Skills — Procedimientos Reutilizables del Agente

> **Las skills son los "cómo" del agente.** Mientras `AGENT.md` define *qué* hacer (el contrato y la secuencia), las skills detallan *cómo* ejecutar tareas técnicas recurrentes de manera uniforme, consistente y libre de errores.

---

## 🗃️ Catálogo de Skills Disponibles

| Skill | Propósito | Cuándo se utiliza | ¿Es Obligatoria? |
| :--- | :--- | :--- | :--- |
| 🔍 [`validar-estado.md`](validar-estado.md) | Garantizar que el agente trabaja sobre un repositorio físico real y evitar alucinaciones de archivos o código. | Al **inicio** de cada sesión o nueva tarea de desarrollo. | **✅ SÍ** |
| 📝 [`actualizar-estado.md`](actualizar-estado.md) | Mantener la documentación viva (`doc/estado.md` y `doc/secuencias.md`) sincronizada al 100% con los cambios realizados. | Al **finalizar** cada sesión o tarea de desarrollo, antes de commitear. | **✅ SÍ** |
| 🗄️ [`migracion-supabase.md`](migracion-supabase.md) | Crear, testear y aplicar de forma segura una migración SQL en Supabase local con seguridad RLS. | Siempre que se altere la estructura de datos (Sprint 2+). | *Según aplique* |
| ✨ [`nueva-feature.md`](nueva-feature.md) | Implementar una feature de punta a punta: desde la planificación y lógica de dominio hasta la UI y tests. | Al añadir funcionalidad de negocio (Sprint 4+). | *Según aplique* |

---

## 🔄 Flujo Metodológico Obligatorio

Cualquier sesión de desarrollo con la IA debe encajar estrictamente dentro del siguiente ciclo de vida de ejecución:

```
┌────────────────────────────────────────────────────────┐
│ 1. INICIO: Ejecutar skill "validar-estado.md"          │
│    -> Comprobar correspondencia de doc/estado.md       │
├────────────────────────────────────────────────────────┤
│ 2. DESARROLLO: Ejecutar el paso actual de secuencias   │
│    -> Aplicar skill específica (ej. nueva-feature.md)   │
├────────────────────────────────────────────────────────┤
│ 3. VERIFICACIÓN: Comprobar código local (build/tests)  │
├────────────────────────────────────────────────────────┤
│ 4. CIERRE: Ejecutar skill "actualizar-estado.md"       │
│    -> Actualizar doc/estado.md y doc/secuencias.md     │
└────────────────────────────────────────────────────────┘
```

---

## ⚠️ Reglas de Uso de Skills

1. **No improvisación:** Si una tarea requiere una secuencia compleja que no está definida en las skills existentes, **se actualiza la skill correspondiente o se crea una nueva**. El agente nunca debe idear flujos de trabajo ad-hoc fuera del sistema de skills.
2. **Sincronización:** Las skills deben estar en perfecta sincronía con la estructura y nombres reales de los archivos en las carpetas `doc/` y `context/`.
3. **Reporte de Errores:** Si una skill falla en su verificación local, la ejecución debe detenerse inmediatamente para alertar al arquitecto (humano).

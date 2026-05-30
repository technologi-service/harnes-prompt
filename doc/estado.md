# 📝 Estado del Proyecto — Documentación Viva

> **Última actualización:** 2026-05-30
> **Actualizado por:** Antigravity (AI Agent)
> **Sprint Activo:** Sprint 1 — Alineación, Estructura y Documentación Rectora

Este documento representa el mapa en tiempo real del desarrollo del proyecto. Es la única fuente de verdad sobre qué funcionalidades están construidas, qué tablas existen en la base de datos y cuáles son los cambios históricos realizados.

---

## §1 — Progreso General de Sprints

| Sprint | Enfoque Principal | Estado |
| :--- | :--- | :--- |
| **Sprint 1** | **Alineación, Estructura y Documentación Rectora** | 🟡 En Progreso |
| **Sprint 2** | Setup del Monorepo, Entorno de Supabase y Base de Datos | ⬜ Pendiente |
| **Sprint 3** | Core Shared Logic (`packages/core`) y Autenticación Multi-tenant | ⬜ Pendiente |
| **Sprint 4** | Dashboard Web (`apps/web` - Next.js) y Operaciones de Cocina | ⬜ Pendiente |
| **Sprint 5** | App Móvil (`apps/mobile` - Expo) y Flujo de Repartidores & Clientes | ⬜ Pendiente |
| **Sprint 6** | Pruebas de Integración E2E, QA y Despliegue de Producción | ⬜ Pendiente |

---

## §2 — Estado de Componentes del Repositorio

| Componente / Directorio | Ruta | Estado | Descripción |
| :--- | :--- | :--- | :--- |
| **Documentación Raíz** | `README.md`, `AGENT.md` | ✅ Completado | Documentos de entrada y contrato del agente IA. |
| **Contexto Estable** | `context/` | ✅ Completado | Definición de producto, actores y decisiones del stack técnico. |
| **Documentación Viva** | `doc/` | ✅ Completado | Estado actual del repositorio y reglas de arquitectura. |
| **Skills de Agente** | `skills/` | ✅ Completado | Recetas y procedimientos de ejecución reutilizables. |
| **Prompts del Agente** | `prompts/` | ✅ Completado | Secuencias de prompts organizadas por carpetas de roles/pasos. |
| **Core Shared Logic** | `packages/core` | ⬜ Pendiente | Lógica de negocio reusable y tipos de TypeScript. |
| **Dashboard Web** | `apps/web` | ⬜ Pendiente | Aplicación web en Next.js para administración y staff. |
| **App Móvil** | `apps/mobile` | ⬜ Pendiente | Aplicación en Expo para clientes y repartidores. |

> **Leyenda de Estados:** ⬜ Pendiente | 🟡 En Progreso | ✅ Completado | ⛔ Bloqueado

---

## §3 — Features del MVP

A continuación se listan las features principales según el archivo [`context/producto.md`](../context/producto.md):

### ⬜ Pendientes
*   **[F-01] Gestión de inventario en tiempo real:** Transacciones atómicas para evitar sobreventas.
*   **[F-02] Catálogo de productos:** Configuración de categorías, variantes e imágenes por tenant.
*   **[F-03] Flujo de pedidos multi-canal:** Ingreso automático de clientes e ingreso manual de staff.
*   **[F-04] Estados de pedido:** Ciclo completo desde `pending` hasta `delivered` con eventos en tiempo real.
*   **[F-05] Asignación de repartidores:** Notificación y aceptación estilo Uber para deliveries.
*   **[F-06] Modelo mixto de repartidores:** Repartidores propios del comercio o de la plataforma.
*   **[F-07] Tracking GPS en tiempo real:** Visualización en mapa de la posición del delivery.
*   **[F-08] Pagos multi-método:** Efectivo, transferencia y tarjetas integradas.
*   **[F-09] Dashboard web:** Interfaz de control para Admin Cocina and Staff.
*   **[F-10] App móvil:** Cliente final (catálogo, pagos, tracking) y app de repartidor.

### 🟡 En Progreso
*   *Ninguna feature se encuentra en progreso.*

### ✅ Implementadas
*   **[F-00] Estructuración del Framework de Documentación:** Alineación completa de roles de archivos rectores, validación inicial del framework y creación de la documentación viva de secuencias y estado.

---

## §4 — Dependencias de Terceros

| Paquete / Herramienta | Versión | Propósito | Estado |
| :--- | :--- | :--- | :--- |
| **pnpm** | N/A | Gestor de paquetes oficial del monorepo | ⬜ Pendiente |
| **Supabase CLI** | N/A | Herramienta de base de datos local y migraciones | ⬜ Pendiente |

---

## §5 — Modelo de Datos (Esquema Supabase)

*No se han creado tablas aún en la base de datos local ni remota.*

---

## §6 — Registro de Secuencias de Prompts (Paso a Paso)

Aquí se detalla la secuencia cronológica de prompts y tareas técnicas ejecutadas:

- **Sprint 1 — Paso 1.1:** Clarificación del proyecto y definición de responsabilidades documentales. [✅ Completado]
- **Sprint 1 — Paso 1.2:** Validación de la estructura inicial contra el framework de documentación viva. [✅ Completado]

---

## §7 — Bitácora de Cambios (Historial de Tareas)

| Fecha | Autor | Prompt N° | Descripción del Cambio |
| :--- | :--- | :--- | :--- |
| 2026-05-30 | Agente (Antigravity) | N/A | Reestructuración de `README.md` y creación del esqueleto inicial de `doc/estado.md` para alineación del framework. |
| 2026-05-30 | Agente (Antigravity) | N/A | Validación del estado y completitud de la fase de alineación (Sprint 1). Actualizados `README.md` raíz, `skills/README.md`, `doc/estado.md` y `doc/secuencias.md`. |
| 2026-05-30 | Agente (Antigravity) | N/A | Alineación total de estructura de directorios con `AGENT.md`. Creación física de `prompts/`, `apps/` y `packages/` con guías y corrección de referencias en `AGENT.md`. |

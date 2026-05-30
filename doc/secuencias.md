# 🗺️ Secuenciador de Prompts — Roadmap de Ejecución

Este documento define la secuencia exacta de sprints, tareas y prompts para la construcción de la plataforma. Ningún paso se salta; cada paso se ejecuta en orden y se marca según su estado real.

---

## 🔄 Sprint 1: Documentación Rectora y Alineación del Framework (En Progreso)
*Enfoque: Dejar las bases metodológicas 100% sólidas y la estructura de archivos rectora.*

- [x] **Paso 1.1 — Clarificación Documental:** Reestructuración de `README.md` y `skills/README.md` para separar responsabilidades. Evitar redundancias y solapamiento.
- [x] **Paso 1.2 — Validación Metodológica:** Ejecución completa de la skill `validar-estado.md` sobre el estado reestructurado para certificar la consistencia del framework.

---

## 🛠️ Sprint 2: Monorepo, Infraestructura y Supabase (Pendiente)
*Enfoque: Levantar la estructura física del proyecto y el entorno local de base de datos.*

- [ ] **Paso 2.1 — Estructura del Monorepo:** Creación de `package.json` raíz, `pnpm-workspace.yaml`, configuración de directorios `apps/` y `packages/`, y setup de TypeScript estricto.
- [ ] **Paso 2.2 — Entorno Supabase:** Inicialización local de Supabase (`supabase init`), creación de la base de datos local y la primera migración versionada (`tenant`, `user_profile`, etc.) con políticas de RLS obligatorias.
- [ ] **Paso 2.3 — Automatización de Tipos:** Script de generación automática de tipos desde Supabase local a `packages/types`.

---

## 🧠 Sprint 3: Core Shared Logic (Pendiente)
*Enfoque: Implementar la lógica pura de negocio y las reglas de dominio que compartirán Web y Mobile.*

- [ ] **Paso 3.1 — Setup de packages/core:** Módulo compartido en TypeScript estricto sin dependencias de frameworks de UI.
- [ ] **Paso 3.2 — Reglas de Dominio y Zod:** Esquemas de validación Zod para inventario, productos y pedidos. Reglas atómicas de negocio.
- [ ] **Paso 3.3 — Cliente de Base de Datos y Transacciones:** Métodos transaccionales atómicos para asegurar que no ocurran sobreventas en el inventario.

---

## 💻 Sprint 4: Dashboard Web - Next.js (Pendiente)
*Enfoque: Panel administrativo para administradores de comercio y personal de cocina.*

- [ ] **Paso 4.1 — Setup de apps/web:** Inicialización de Next.js (App Router), configuración de estilos globales y diseño premium responsivo.
- [ ] **Paso 4.2 — Autenticación y Multi-tenant:** Login, asignación del tenant en base al token JWT y middleware de seguridad.
- [ ] **Paso 4.3 — Panel de Control de Inventario:** Listado y edición rápida de stock, sincronización en tiempo real.
- [ ] **Paso 4.4 — Gestor de Pedidos de Cocina:** Listado de órdenes activas, actualización lineal de estados (`pending` -> `preparing` -> `ready`) y sonido de notificación.

---

## 📱 Sprint 5: App Móvil - Expo (Pendiente)
*Enfoque: Aplicación móvil en React Native para repartidores y clientes finales.*

- [ ] **Paso 5.1 — Setup de apps/mobile:** Inicialización de Expo con Expo Router y TypeScript estricto.
- [ ] **Paso 5.2 — Flujo de Repartidor (Delivery):** Visualización de pedidos listos para despacho (`ready`), aceptación atómica del pedido, cambio de estado y simulación de ubicación GPS.
- [ ] **Paso 5.3 — Flujo de Cliente (Customer):** Búsqueda de comercios locales, catálogo interactivo, simulación de pasarela de pago y seguimiento del repartidor en el mapa en tiempo real.

---

## 🔒 Sprint 6: QA, Auditoría RLS y Despliegue (Pendiente)
*Enfoque: Asegurar el blindaje de seguridad de datos multi-tenant, testing E2E y despliegue a producción.*

- [ ] **Paso 6.1 — Auditoría de Seguridad RLS:** Tests unitarios e integración que simulen ataques cruzados de tenants para verificar que es imposible leer/escribir datos de otros comercios.
- [ ] **Paso 6.2 — Testing E2E Integrado:** Simulación completa del flujo: Cliente pide -> Cocina recibe, prepara y despacha -> Repartidor acepta y entrega -> Cliente ve tracking GPS.
- [ ] **Paso 6.3 — Despliegue Continuo (CI/CD):** Configuración de GitHub Actions para linter, compilación, aplicación de migraciones automatizadas y despliegue final en producción (Vercel + EAS Expo + Supabase Cloud).

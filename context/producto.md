# [Nombre del SaaS] — Contexto de Producto y Negocio

<!--
  Este archivo define QUÉ estamos construyendo y POR QUÉ.
  Es la fuente de verdad sobre el alcance del producto.
  Cualquier agente o humano que lea esto debe entender el negocio completo.
  Última actualización: 2026-05-30
-->

---

## ¿Cuál es la Visión Estratégica?

<!--
  Responder con la siguiente estructura:
  "Plataforma SaaS multi-tenant de [tipo de automatización] para [Sector Target]
  que centraliza [Proceso Core] y conecta [Actor A] con [Actor B] en tiempo real
  para eliminar [Fricción Principal]."
-->

> _Pendiente de definición._

---

## ¿Qué Problemas Críticos del Mercado Resolvemos?

<!--
  Describir cómo gestiona el cliente objetivo cada dolor hoy
  y por qué esa forma actual es ineficiente o propensa a errores.
-->

El cliente objetivo padece ineficiencias críticas debido a:

- **[Dolor Operativo 1]** — ¿Cómo gestionan esto hoy y por qué es ineficiente o propenso a errores?
- **[Dolor Operativo 2]** — ¿Qué falta de visibilidad o datos sufren en su día a día?
- **[Dolor Operativo 3]** — ¿Por qué las soluciones actuales del mercado no les sirven o son muy caras/complejas?

**Impacto en el negocio:** [Definir la consecuencia final: ej. pérdida de clientes, márgenes de ganancia reducidos, operaciones caóticas].

---

## ¿Cuál es la Propuesta de Valor y Diferenciación?

<!--
  Describir cada módulo funcional core:
  qué valor entrega y cuál es su regla o diferenciador clave.
-->

El SaaS centraliza y automatiza la operación a través de los siguientes módulos:

| Pilar de la Solución | ¿Qué valor entrega al cliente? | ¿Cuál es la regla / diferenciador clave? |
|----------------------|-------------------------------|------------------------------------------|
| [Módulo Core 1] | [Ej: Control de stock en tiempo real] | [Ej: Bloqueo de venta automático si el inventario llega a cero] |
| [Módulo Core 2] | [Ej: Consolidación de canales] | [Ej: Captura de flujos desde cualquier origen en un único panel] |
| [Módulo Core 3] | [Ej: Logística Dinámica] | [Ej: Asignación automática al recurso disponible más cercano] |

---

## ¿Cómo Funciona el Modelo de Negocio y Multi-Tenancy?

<!--
  Definir qué representa un tenant, cómo se monetiza
  y qué métricas controlan el acceso por tier de suscripción.
-->

**Definición de Tenant:** 1 Tenant = [Ej: Un comercio local con múltiples sucursales]. Los datos deben estar completamente aislados por entidad de negocio.

**Estructura de Monetización:** Suscripción mensual/anual basada en la intensidad de uso.

**Métricas de Control para Tiers** (el sistema controlará y limitará acceso según estas métricas):

- **[Métrica 1]:** [Ej: Volumen de transacciones/pedidos permitidos al mes]
- **[Métrica 2]:** [Ej: Límite de registros activos (productos, usuarios, clientes)]
- **[Métrica 3]:** [Ej: Acceso a módulos avanzados o analíticas]

---

## ¿Quiénes son los Usuarios y Clientes Objetivo (ICP)?

<!--
  Describir cada segmento: perfil operativo, volumen de uso
  y justificación de por qué validar con ellos primero.
-->

### Prioridad Alta (MVP)

| Segmento de Cliente | Perfil Operativo y Volumen | ¿Por qué validar con ellos primero? |
|---------------------|---------------------------|--------------------------------------|
| [Perfil Target 1] | [Ej: Operaciones de alto flujo con catálogos dinámicos] | [Ej: Mayor disposición a pagar por optimizar tiempos] |
| [Perfil Target 2] | [Ej: Comercios con stock perecedero o variable] | [Ej: Requieren automatización estricta de inventarios] |

### Fases Posteriores (Post-MVP)

- [Segmento Secundario 1]
- [Segmento Secundario 2]

---

## ¿Cuál es el Alcance Funcional del MVP?

<!--
  Listar únicamente las features que DEBEN estar en la primera versión.
  Para cada una: qué regla de negocio automatiza y quién es el actor principal.
  Si una funcionalidad no está aquí, NO entra en el MVP.
-->

| # | Funcionalidad Core | ¿Qué regla de negocio automatiza o resuelve? | Actor Principal |
|---|--------------------|----------------------------------------------|-----------------|
| 1 | [Feature 1] | [Descripción de la regla lógica o flujo de datos esperado] | [Rol de Usuario] |
| 2 | [Feature 2] | [Descripción de la regla lógica o flujo de datos esperado] | [Rol de Usuario] |
| 3 | [Feature 3] | [Descripción de la regla lógica o flujo de datos esperado] | [Rol de Usuario] |
| 4 | [Feature 4] | [Descripción de la regla lógica o flujo de datos esperado] | [Rol de Usuario] |
| 5 | [Feature 5] | [Descripción de la regla lógica o flujo de datos esperado] | [Rol de Usuario] |

---

## ¿Qué Funcionalidades Quedan Excluidas del MVP?

<!--
  Listar features descartadas del MVP con su impacto futuro y prioridad.
  Esto evita scope creep y da claridad al equipo sobre qué no construir ahora.
-->

| Funcionalidad Excluida | Impacto a Futuro | Prioridad de Negocio |
|------------------------|------------------|----------------------|
| [Feature Post-MVP 1] | [Ej: Integración con APIs de mensajería externa de terceros] | Alta / Media / Baja |
| [Feature Post-MVP 2] | [Ej: Módulos avanzados de predicción de la demanda] | Alta / Media / Baja |

---

## ¿Cuáles son los Contextos de Uso y Canales?

<!--
  Definir qué interfaces existen, para qué rol y bajo qué contexto de uso.
  La separación web/móvil se basa en el contexto: operación fija vs. en movimiento.
-->

### Interfaces de Operación Fija (Escritorio / Tablets)

Diseñadas para gestión de alto volumen de datos, configuraciones, reportes y supervisión administrativa.

| Rol | Función Principal |
|-----|-------------------|
| [Rol Administrador Global] | Monitoreo del ecosistema SaaS, control de Tenants y facturación |
| [Rol Administrador del Tenant] | Configuración de reglas de negocio, catálogos, control de personal y analíticas de su comercio |
| [Rol Operador / Staff] | Gestión operativa rápida en estaciones fijas de trabajo (recepción y procesamiento) |

### Interfaces de Operación en Movimiento (Smartphones / Dispositivos de Mano)

Diseñadas para flujos dinámicos, cargas ultrarrápidas, interfaces simplificadas y dependencia de hardware móvil (GPS, notificaciones, cámara).

| Rol | Función Principal |
|-----|-------------------|
| [Rol Operador en Calle / Logística] | Gestión de tareas en ruta, actualizaciones de estado sobre la marcha y confirmaciones de entrega |
| [Rol Usuario Final / Cliente] | Consulta de la oferta del Tenant, auto-gestión de solicitudes, pagos y seguimiento en tiempo real |

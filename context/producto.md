# Producto — Contexto General

<!-- 
  Este archivo define QUÉ estamos construyendo y POR QUÉ.
  Es la fuente de verdad sobre el alcance del producto.
  Cualquier agente o humano que lea esto debe entender el negocio completo.
  Última actualización: 2026-05-30
-->

---

## Visión

<!-- 
  La visión describe el estado futuro que queremos alcanzar.
  No es una feature list, es la dirección estratégica del producto.
-->

Plataforma SaaS multi-tenant de logística e hiperlocalización que gestiona el inventario y despachos de comercios locales y cocinas delivery-only, conectando la oferta comercial con repartidores y clientes finales en tiempo real.

---

## Problema

<!-- 
  El problema que resolvemos. Sin un problema claro, no hay producto.
  Esto guía todas las decisiones de priorización.
-->

Los comercios locales (restaurantes, dark kitchens, fruterías) **no tienen herramientas integradas** para gestionar inventario, pedidos y despachos. Hoy dependen de:

- **Múltiples plataformas desconectadas** — una app para pedidos, otra para inventario, otra para pagos
- **WhatsApp manual** — pedidos por chat sin trazabilidad, propenso a errores
- **Cero visibilidad en tiempo real** — no saben qué stock tienen, dónde está el repartidor, ni el estado real de cada pedido
- **Sin datos para decidir** — no tienen métricas de su operación para optimizar

El resultado: pérdida de ventas, errores en pedidos, clientes frustrados y operaciones ineficientes.

---

## Propuesta de Valor

<!-- 
  Qué ofrecemos que resuelve el problema.
  Cada punto aquí debe mapear directamente a un dolor del comercio.
-->

Una sola plataforma que centraliza:

| Pilar | Descripción | Diferenciador |
|-------|-------------|---------------|
| **Inventario** | Gestión en tiempo real con transacciones atómicas | No hay sobre-venta: si el stock llega a 0, se bloquea automáticamente |
| **Pedidos** | Multi-canal: app + WhatsApp + teléfono + Telegram | El comercio recibe todos los pedidos en un solo lugar sin importar el canal de origen |
| **Despacho** | Tracking GPS + asignación inteligente de repartidores | El cliente ve en mapa dónde está su pedido; el comercio sabe quién lleva qué |
| **Pagos** | Efectivo + transferencia + tarjeta | Flexibilidad total para el cliente final, conciliación simple para el comercio |

---

## Modelo de Negocio

<!-- 
  Cómo monetizamos. Esto impacta directamente en la arquitectura
  porque necesitamos medir uso por tenant para facturar.
  1 tenant = 1 comercio = 1 suscripción.
-->

**Estructura por tiers de volumen** — planes escalonados según el volumen de operaciones del comercio.

- **1 tenant = 1 comercio** (aislamiento total de datos vía RLS)
- Los planes se diferencian por: cantidad de pedidos/mes, cantidad de productos, cantidad de staff, acceso a funcionalidades avanzadas (reportes, integraciones)
- Modelo de ingresos recurrentes (SaaS mensual)

> **Nota:** Los tiers exactos y precios se definirán en la fase de validación comercial. La arquitectura debe soportar medición de uso por tenant desde el día 1.

---

## Comercios Objetivo

<!-- 
  A quién le vendemos. Esto define las funcionalidades que priorizamos.
  Prioridad alta = MVP debe servirles desde el día 1.
-->

### Prioridad Alta (MVP)

| Tipo de Comercio | Características | Por qué son prioridad |
|------------------|-----------------|----------------------|
| **Restaurantes y cocinas delivery-only (dark kitchens)** | Alto volumen de pedidos, menú con variantes, tiempos de preparación críticos | Mayor dolor con la gestión manual, dispuestos a pagar por eficiencia |
| **Fruterías y comercios de alimentos frescos** | Inventario perecedero, stock variable, pedidos frecuentes | Necesitan control de inventario estricto, mercado desatendido por apps grandes |

### Futuro (post-MVP)

- Panaderías y reposterías
- Tiendas de conveniencia / minimarkets
- Farmacias locales
- Otros comercios con despacho local

---

## Zona de Lanzamiento

<!-- 
  Dónde lanzamos primero. La hiperlocalización es clave:
  necesitamos densidad de comercios y repartidores en una zona acotada.
-->

Ciudad/zona específica con comercios ya identificados. El lanzamiento se hará en una zona geográfica delimitada para garantizar:

1. **Densidad de oferta** — suficientes comercios para que el cliente encuentre opciones
2. **Densidad de repartidores** — tiempos de entrega competitivos
3. **Validación rápida** — feedback directo de comercios conocidos
4. **Operación controlada** — resolver problemas antes de escalar

---

## Funcionalidades MVP

<!-- 
  Las 10 funcionalidades que DEBEN estar en la primera versión.
  Cada una tiene un número para referencia en otros documentos.
  Si una funcionalidad no está aquí, NO entra en el MVP.
-->

| # | Funcionalidad | Descripción | Actor Principal |
|---|---------------|-------------|-----------------|
| 1 | **Gestión de inventario en tiempo real** | Transacciones atómicas para evitar sobre-venta. Stock se actualiza en cada pedido confirmado. | Admin Cocina, Staff |
| 2 | **Catálogo de productos** | Categorías, variantes (tamaño, extras), imágenes, precios. Cada tenant tiene su propio catálogo. | Admin Cocina |
| 3 | **Flujo de pedidos multi-canal** | Pedidos desde la app directa del cliente + carga manual por Staff (para pedidos de WhatsApp/teléfono). | Customer, Staff |
| 4 | **Estados de pedido** | Flujo lineal: `pending` → `confirmed` → `preparing` → `ready` → `picked_up` → `delivered`. Cada transición genera evento en tiempo real. | Todos |
| 5 | **Asignación de repartidores** | Estilo Uber: cuando el pedido está `ready`, se notifica a repartidores cercanos. El primero en aceptar se lo lleva. | Delivery |
| 6 | **Modelo mixto de repartidores** | El comercio puede usar sus propios repartidores O repartidores de la plataforma. Configurable por tenant. | Admin Cocina |
| 7 | **Tracking GPS en tiempo real** | El cliente ve la ubicación del repartidor en mapa desde `picked_up` hasta `delivered`. | Customer, Delivery |
| 8 | **Pagos multi-método** | Efectivo, transferencia bancaria, tarjeta. El método se selecciona al crear el pedido. | Customer |
| 9 | **Dashboard web** | Panel de administración para comercios: gestión de productos, pedidos, staff, reportes básicos. | Admin Cocina, Staff |
| 10 | **App móvil** | App para clientes (buscar, pedir, pagar, seguir) y repartidores (aceptar, navegar, entregar). | Customer, Delivery |

---

## Funcionalidades Post-MVP

<!-- 
  Funcionalidades que queremos pero NO bloquean el lanzamiento.
  Se priorizarán según feedback de usuarios reales.
  No invertir tiempo de arquitectura en esto hasta que toque.
-->

| Funcionalidad | Descripción | Prioridad Estimada |
|---------------|-------------|-------------------|
| **Integración Telegram API** | Pedidos directamente desde chat de Telegram, parseados automáticamente | Media |
| **Dashboard de reportes y métricas** | Ventas por periodo, productos más vendidos, tiempos de entrega, rendimiento de repartidores | Alta |
| **Panel super-admin** | Monitoreo global de todos los tenants, salud de la plataforma, métricas de negocio SaaS | Alta |
| **Onboarding automatizado** | Registro self-service de nuevos comercios, configuración guiada, primer catálogo | Media |
| **Ratings y reseñas** | Clientes califican pedidos y repartidores, comercios ven su puntuación | Media |
| **Chat in-app** | Comunicación directa entre cliente ↔ comercio y cliente ↔ repartidor | Baja |

---

## Plataformas

<!-- 
  Qué interfaces construimos y para quién.
  Esto define la estructura del monorepo (apps/web y apps/mobile).
-->

### Web (Next.js — Dashboard)

| Actor | Acceso |
|-------|--------|
| Super Admin | Gestión global de la plataforma |
| Admin Cocina | Gestión de su comercio (productos, pedidos, staff, config) |
| Staff | Operación diaria (pedidos, stock) |

### Móvil (Expo — App nativa iOS + Android)

| Actor | Acceso |
|-------|--------|
| Delivery | Aceptar pedidos, navegar, confirmar entregas |
| Customer | Buscar comercios, pedir, pagar, seguir en mapa |

> **Nota:** La decisión de separar web vs móvil por actor se basa en el contexto de uso: los operadores del comercio trabajan desde un puesto fijo (computador/tablet), mientras que repartidores y clientes están en movimiento.

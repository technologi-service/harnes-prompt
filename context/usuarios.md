# Usuarios — Actores del Sistema

<!-- 
  Este archivo define QUIÉN usa la plataforma y CÓMO.
  Es la referencia para diseño de interfaces, permisos (RLS), 
  y flujos de navegación.
  Los 5 actores son exhaustivos: si alguien no está aquí, no tiene acceso.
  Última actualización: 2026-05-30
-->

---

## Resumen de Actores

<!-- 
  Tabla resumen para referencia rápida.
  El campo "Interfaz" define en qué app vive cada actor (web o móvil).
  El campo "Scope" define el alcance de sus datos: 
  - global = ve todos los tenants
  - tenant = solo ve datos de su comercio
  - propio = solo ve sus propios datos dentro del tenant
-->

| # | Actor | Interfaz | Scope | Descripción Corta |
|---|-------|----------|-------|-------------------|
| 1 | **Super Admin** | Web | Global | Operador de la plataforma SaaS |
| 2 | **Admin Cocina** | Web | Tenant | Dueño o administrador del comercio |
| 3 | **Staff** | Web | Tenant (restringido) | Empleado que opera pedidos y stock |
| 4 | **Delivery** | Móvil | Propio + zona | Repartidor que entrega pedidos |
| 5 | **Customer** | Móvil | Propio + zona | Cliente final que pide productos |

---

## 1. Super Admin

<!-- 
  El Super Admin es el operador de la plataforma SaaS (nosotros).
  NO es un actor de un comercio. Tiene visibilidad global.
  En la base de datos, su rol NO está asociado a ningún tenant_id.
  Sus permisos se gestionan con una política RLS especial o bypass.
-->

### Quién es
El equipo que opera la plataforma SaaS. Gestiona todos los tenants (comercios), monitorea la salud del sistema y administra los planes de suscripción.

### Interfaz
**Web** — Dashboard de administración global (Next.js)

### Permisos

| Categoría | Acciones |
|-----------|----------|
| **Tenants** | Crear, editar, suspender, eliminar comercios |
| **Métricas** | Ver métricas globales: pedidos totales, ingresos, comercios activos, repartidores activos |
| **Planes** | Gestionar planes de suscripción, asignar plan a tenant, cambiar tier |
| **Soporte** | Acceder a datos de cualquier tenant para resolver incidencias |
| **Usuarios** | Ver y gestionar usuarios de cualquier tenant |
| **Configuración** | Configurar parámetros globales de la plataforma |

### Necesidades

- Visibilidad total de la plataforma en un solo dashboard
- Alertas cuando un comercio tiene problemas operativos
- Herramientas de administración eficientes (no quiere entrar tenant por tenant)
- Métricas de negocio SaaS: MRR, churn, uso por tenant

### Consideraciones Técnicas

<!-- 
  El Super Admin es especial en RLS: necesita bypass o un rol 
  con políticas que permitan acceso cross-tenant.
  Opción recomendada: rol 'super_admin' en auth.users con 
  política RLS que verifica ese rol para permitir acceso global.
-->

- Rol en Supabase Auth: `super_admin`
- RLS: Política especial que permite lectura cross-tenant
- No tiene `tenant_id` en su perfil de usuario

---

## 2. Admin Cocina (Admin del Comercio)

<!-- 
  El Admin Cocina es el dueño o gerente del comercio.
  Es el "propietario" del tenant. Tiene control total dentro de su comercio,
  pero CERO visibilidad de otros tenants.
  Típicamente es quien contrata la plataforma y paga la suscripción.
-->

### Quién es
Dueño o administrador del comercio (tenant). Es quien contrata el servicio, configura el negocio y supervisa las operaciones.

### Interfaz
**Web** — Dashboard del comercio (Next.js)

### Permisos

| Categoría | Acciones |
|-----------|----------|
| **Productos** | CRUD completo: crear, editar, eliminar productos, categorías, variantes |
| **Inventario** | Ajustar stock, ver historial de movimientos, configurar alertas de stock bajo |
| **Pedidos** | Ver todos los pedidos de su comercio, cambiar estados, cancelar, ver historial |
| **Reportes** | Ver métricas de SU tenant: ventas, productos top, tiempos de preparación |
| **Staff** | Invitar, editar roles, desactivar empleados de su comercio |
| **Repartidores** | Gestionar repartidores propios del comercio, ver repartidores de plataforma disponibles |
| **Configuración** | Zona de cobertura, horarios, métodos de pago aceptados, precios de envío |
| **Promociones** | Crear y gestionar descuentos y promociones |

### Necesidades

- **Control total** de su negocio desde un solo panel
- **Visibilidad en tiempo real** de pedidos activos, stock, repartidores en ruta
- **Simplicidad** — no quiere una herramienta compleja, quiere operar rápido
- **Confianza** — saber que sus datos están aislados y seguros (nadie más ve su info)

### Consideraciones Técnicas

<!-- 
  Siempre tiene tenant_id en su perfil.
  Todas sus queries están filtradas por tenant_id vía RLS.
  Puede haber más de un Admin Cocina por tenant (ej: socios del negocio).
-->

- Rol en Supabase Auth: `admin`
- RLS: Todas las políticas filtran por `tenant_id = auth.jwt() -> tenant_id`
- Puede existir más de uno por tenant (co-administradores)

---

## 3. Staff (Empleado)

<!-- 
  El Staff es el empleado operativo del comercio.
  Su trabajo es recibir pedidos, prepararlos y marcarlos como listos.
  Tiene permisos limitados: NO puede modificar productos, configuración ni ver reportes.
  Esto es intencional para mantener la interfaz simple y evitar errores.
-->

### Quién es
Empleado del comercio que trabaja en la operación diaria: recibe pedidos, los prepara y los marca como listos para despacho.

### Interfaz
**Web** — Vista simplificada del dashboard (Next.js). Misma app que Admin Cocina pero con menos opciones visibles.

### Permisos

| Categoría | Acciones |
|-----------|----------|
| **Pedidos** | Ver pedidos asignados/activos, confirmar recepción, marcar en preparación, marcar como listo |
| **Stock** | Actualizar cantidades de stock (ej: "se acabó el aguacate"), marcar producto como agotado |
| **Catálogo** | Ver productos y precios (solo lectura) |
| **Notificaciones** | Recibir alertas de nuevos pedidos |

### Lo que NO puede hacer

<!-- 
  Listamos explícitamente lo que NO puede hacer para que quede claro
  en la implementación de permisos. Cada "no" aquí se traduce 
  en una política RLS o check en el frontend.
-->

- ❌ Crear, editar o eliminar productos
- ❌ Ver reportes o métricas del negocio
- ❌ Gestionar otros usuarios (staff o repartidores)
- ❌ Modificar configuración del comercio
- ❌ Cancelar pedidos (solo Admin Cocina puede)
- ❌ Ver información financiera

### Necesidades

- **Interfaz ultra-simple** — pantalla tipo "tablero Kanban" con pedidos en columnas por estado
- **Velocidad** — cambiar estado de un pedido con un solo toque/click
- **Notificaciones inmediatas** — sonido + visual cuando entra un pedido nuevo
- **Mínima fricción** — no quiere login complicado, idealmente sesión persistente en tablet del local

### Consideraciones Técnicas

<!-- 
  Staff comparte tenant_id con su Admin Cocina.
  Sus políticas RLS son iguales en scope (mismo tenant) 
  pero diferentes en acciones (solo lectura en productos, 
  solo update en estados de pedido).
-->

- Rol en Supabase Auth: `staff`
- RLS: Mismo `tenant_id` que Admin Cocina, pero con políticas más restrictivas
- Permisos granulares por acción, no solo por tabla

---

## 4. Delivery (Repartidor)

<!-- 
  El Delivery es el repartidor que lleva pedidos del comercio al cliente.
  MODELO MIXTO: puede ser empleado del comercio o repartidor independiente de la plataforma.
  - Repartidor del comercio: tiene tenant_id fijo, solo ve pedidos de ese comercio
  - Repartidor de plataforma: NO tiene tenant_id fijo, ve pedidos de cualquier comercio en su zona
  Esta dualidad impacta directamente en las políticas RLS.
-->

### Quién es
Repartidor que entrega pedidos. Puede ser de dos tipos:

| Tipo | Descripción | Scope de datos |
|------|-------------|----------------|
| **Del comercio** | Empleado directo del restaurante/tienda | Solo ve pedidos de su comercio (`tenant_id` fijo) |
| **De la plataforma** | Repartidor independiente registrado en la plataforma | Ve pedidos de cualquier comercio en su zona geográfica |

### Interfaz
**Móvil** — App nativa iOS + Android (Expo)

### Permisos

| Categoría | Acciones |
|-----------|----------|
| **Pedidos disponibles** | Ver pedidos con estado `ready` en su zona (o en su comercio si es del comercio) |
| **Aceptar/Rechazar** | Aceptar un pedido disponible (first-come-first-served) o rechazarlo |
| **Navegación** | Ver ruta al comercio (recoger) y al cliente (entregar) |
| **Estados** | Marcar `picked_up` (recogido del comercio) y `delivered` (entregado al cliente) |
| **Historial** | Ver sus entregas pasadas, ganancias acumuladas |
| **Disponibilidad** | Marcar si está activo/inactivo para recibir pedidos |

### Necesidades

- **Flujo ultra-simple** — abrir app → ver pedido → aceptar → navegar → entregar → siguiente
- **Navegación integrada** — mapa con ruta directa, sin salir de la app (ideal) o deeplink a Google Maps/Waze
- **Buenos pedidos** — ver distancia y ganancia estimada antes de aceptar
- **Ganar dinero** — cobrar rápido, ver historial de ganancias
- **Batería y datos** — la app no puede consumir demasiada batería ni datos móviles

### Flujo de Trabajo

<!-- 
  Este flujo es la experiencia core del repartidor.
  Cada paso genera un cambio de estado en el sistema.
-->

```
1. Repartidor abre app → marca disponibilidad como "activo"
2. Recibe notificación push: "Nuevo pedido en [Comercio] — $X — 2.3 km"
3. Ve detalles: dirección del comercio, dirección del cliente, contenido del pedido
4. Acepta → pedido se le asigna (nadie más puede aceptarlo)
5. Navega al comercio → llega → marca "recogido" (picked_up)
6. Navega al cliente → llega → marca "entregado" (delivered)
7. Si es pago en efectivo: cobra al cliente
8. Vuelve a paso 1 (o cierra app)
```

### Consideraciones Técnicas

<!-- 
  La ubicación GPS del repartidor se envía al backend cada N segundos
  mientras tiene un pedido activo (picked_up). Esto alimenta el tracking 
  en tiempo real que ve el Customer.
  IMPORTANTE: Solo enviar ubicación cuando tiene pedido activo, 
  no cuando está esperando. Esto ahorra batería y datos.
-->

- Rol en Supabase Auth: `delivery`
- Repartidor del comercio: `tenant_id` fijo en perfil
- Repartidor de plataforma: `tenant_id` NULL, filtrado por geolocalización
- Tracking GPS: solo activo durante entregas (`picked_up` → `delivered`)
- Push notifications vía Expo Notifications

---

## 5. Customer (Cliente Final)

<!-- 
  El Customer es el consumidor que pide productos/comida.
  NO pertenece a ningún tenant. Puede pedir de cualquier comercio.
  Su tenant_id en los pedidos se asigna dinámicamente según a qué comercio le pide.
  En la tabla de usuarios, NO tiene tenant_id fijo.
-->

### Quién es
Consumidor final que quiere pedir productos o comida de comercios locales cercanos.

### Interfaz
**Móvil** — App nativa iOS + Android (Expo). Misma app que Delivery pero con flujo completamente diferente (distinto stack de navegación).

### Permisos

| Categoría | Acciones |
|-----------|----------|
| **Explorar** | Buscar comercios cercanos por ubicación, categoría, nombre |
| **Catálogo** | Ver productos, categorías, variantes, precios, imágenes de cualquier comercio |
| **Pedidos** | Crear pedido (carrito → checkout), seleccionar método de pago, confirmar dirección |
| **Seguimiento** | Ver estado del pedido en tiempo real, ver ubicación del repartidor en mapa |
| **Historial** | Ver pedidos pasados, repetir pedidos favoritos |
| **Perfil** | Gestionar direcciones guardadas, métodos de pago, datos personales |

### Necesidades

- **Encontrar rápido** — ver comercios cercanos, filtrar por categoría, buscar por nombre de producto
- **Pedir fácil** — carrito intuitivo, checkout en máximo 3 pasos
- **Pagar cómodo** — elegir entre efectivo, transferencia o tarjeta
- **Saber cuándo llega** — tracking en mapa del repartidor + estimación de tiempo
- **Confianza** — ver fotos de productos, calificaciones del comercio (post-MVP)

### Flujo de Trabajo

<!-- 
  Este flujo es la experiencia core del cliente.
  Cada paso corresponde a una pantalla o sección de la app.
-->

```
1. Abre app → ve comercios cercanos a su ubicación
2. Selecciona comercio → ve catálogo de productos
3. Agrega productos al carrito (con variantes si aplica)
4. Va al checkout → confirma dirección de entrega
5. Selecciona método de pago → confirma pedido
6. Pedido entra como "pending" → espera confirmación del comercio
7. Ve estados en tiempo real: confirmed → preparing → ready
8. Cuando el repartidor recoge: ve tracking GPS en mapa
9. Repartidor entrega → pedido se marca como "delivered"
10. (Post-MVP) Califica la experiencia
```

### Consideraciones Técnicas

<!-- 
  El Customer NO tiene tenant_id fijo. Puede pedir de cualquier comercio.
  En la tabla orders, el tenant_id se asigna según el comercio al que pide.
  Para RLS, el Customer ve sus propios pedidos (filtrado por user_id),
  y puede ver catálogos de cualquier comercio (los catálogos son públicos 
  para comercios activos).
-->

- Rol en Supabase Auth: `customer`
- Sin `tenant_id` fijo en perfil de usuario
- RLS para pedidos: `user_id = auth.uid()` (solo ve los suyos)
- RLS para catálogos: lectura pública para comercios con `status = 'active'`
- Ubicación del cliente se usa para: buscar comercios cercanos, calcular distancia de entrega

---

## Relaciones entre Actores

<!-- 
  Esta sección muestra cómo interactúan los actores entre sí.
  Es clave para diseñar los flujos de datos y las notificaciones.
  Cada flecha es un flujo de datos o acción que cruza actores.
-->

### Diagrama de Interacción

```
                    ┌─────────────┐
                    │ Super Admin │
                    │  (Global)   │
                    └──────┬──────┘
                           │ gestiona
                           ▼
                    ┌─────────────┐
                    │Admin Cocina │◄──── configura staff y repartidores
                    │  (Tenant)   │
                    └──┬───────┬──┘
                       │       │
              asigna   │       │  supervisa
              pedidos  │       │
                       ▼       ▼
                 ┌───────┐  ┌──────────┐
                 │ Staff  │  │ Delivery │
                 │(Tenant)│  │(Tenant/  │
                 └───┬────┘  │Plataforma│
                     │       └────┬─────┘
          prepara    │            │ entrega
          pedido     │            │
                     └─────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Customer   │
                    │  (Global)   │
                    └─────────────┘
```

### Flujo de un Pedido (todos los actores involucrados)

<!-- 
  Este flujo muestra qué actor participa en cada paso de un pedido.
  Es la vista end-to-end que cruza todos los roles.
-->

| Paso | Estado | Actor que actúa | Acción |
|------|--------|-----------------|--------|
| 1 | — | **Customer** | Crea el pedido desde la app |
| 2 | `pending` | **Staff / Admin Cocina** | Recibe notificación, confirma el pedido |
| 3 | `confirmed` | **Staff** | Comienza a preparar el pedido |
| 4 | `preparing` | **Staff** | Marca el pedido como listo |
| 5 | `ready` | **Delivery** | Recibe notificación, acepta el pedido |
| 6 | `ready` | **Delivery** | Navega al comercio, recoge el pedido |
| 7 | `picked_up` | **Customer** | Ve al repartidor en el mapa en tiempo real |
| 8 | `picked_up` | **Delivery** | Navega al cliente, entrega el pedido |
| 9 | `delivered` | **Customer** | Recibe confirmación de entrega |
| 10 | `delivered` | **Admin Cocina** | Ve el pedido completado en su dashboard |

### Matriz de Comunicación entre Actores

<!-- 
  Qué canales de comunicación existen entre actores.
  "Push" = notificación push móvil
  "Realtime" = WebSocket (Supabase Realtime)
  "Chat" = chat in-app (post-MVP)
-->

| De → A | Super Admin | Admin Cocina | Staff | Delivery | Customer |
|--------|-------------|-------------|-------|----------|----------|
| **Super Admin** | — | Soporte/Email | — | — | — |
| **Admin Cocina** | Soporte | — | Asignación | Asignación | — |
| **Staff** | — | Alertas | — | — | — |
| **Delivery** | — | — | — | — | Estado (push) |
| **Customer** | — | — | — | — | — |

> **Nota MVP:** En el MVP, la comunicación entre actores es implícita (cambios de estado generan notificaciones automáticas). El chat directo entre actores es post-MVP.

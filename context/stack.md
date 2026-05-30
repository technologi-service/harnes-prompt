# Stack Técnico — Decisiones de Arquitectura

<!-- 
  Este archivo es la fuente de verdad sobre las decisiones técnicas del proyecto.
  Se divide en dos secciones:
  - DECIDIDAS: ya están cerradas, no se debaten más salvo razón mayor
  - PENDIENTES: aún no se han tomado, se documentan opciones y criterios
  
  Regla: cuando una decisión pendiente se cierra, se mueve a la sección 
  "Decididas" y se registra la fecha en doc/estado.md
  
  Última actualización: 2026-05-30
-->

---

## Decisiones Decididas

<!-- 
  Estas decisiones están CERRADAS. El equipo y los agentes deben respetarlas.
  Cada decisión incluye la justificación para que cualquier persona nueva 
  entienda POR QUÉ se eligió esta opción y no otra.
-->

### Frontend Web: Next.js 14+ (App Router)

<!-- 
  Se eligió Next.js por: SSR/SSG para SEO del dashboard público (si aplica),
  App Router para layouts anidados (ideal para dashboard multi-rol),
  ecosistema React maduro, y despliegue trivial en Vercel.
-->

| Aspecto | Detalle |
|---------|---------|
| **Framework** | Next.js 14+ con App Router |
| **Renderizado** | Server Components por defecto, Client Components donde haga falta interactividad |
| **Uso** | Dashboard para Super Admin, Admin Cocina y Staff |
| **Justificación** | App Router permite layouts anidados ideales para un dashboard multi-rol. Server Components reducen JavaScript enviado al cliente. Ecosistema React maduro con amplia disponibilidad de componentes UI. |

---

### Frontend Móvil: Expo (Expo Router) — React Native

<!-- 
  Se eligió Expo en vez de React Native puro por:
  - Expo Router para navegación basada en archivos (consistente con Next.js)
  - EAS Build para CI/CD de apps nativas sin configurar Xcode/Android Studio
  - Expo Notifications para push notifications cross-platform
  - SDK managed workflow que simplifica enormemente el setup
-->

| Aspecto | Detalle |
|---------|---------|
| **Framework** | Expo SDK con Expo Router |
| **Plataformas** | iOS + Android desde el mismo codebase |
| **Uso** | App para Delivery y Customer |
| **Justificación** | Expo simplifica el desarrollo móvil cross-platform. Expo Router da navegación basada en archivos (consistente con Next.js). EAS Build maneja el CI/CD nativo. Expo Notifications resuelve push cross-platform. |

---

### Backend y Base de Datos: Supabase

<!-- 
  Supabase es el backend completo del proyecto. NO hay backend custom (Express, NestJS, etc.).
  La lógica de negocio vive en:
  - RLS policies (autorización)
  - Database functions (lógica transaccional, ej: decrementar stock)
  - Edge Functions (lógica que no puede vivir en la DB, ej: webhooks de pagos)
  - Triggers (eventos automáticos, ej: notificar cuando pedido cambia de estado)
-->

| Aspecto | Detalle |
|---------|---------|
| **Base de datos** | PostgreSQL (gestionado por Supabase) |
| **Autenticación** | Supabase Auth (email + password para MVP, social login post-MVP) |
| **Storage** | Supabase Storage (imágenes de productos, logos de comercios) |
| **Realtime** | Supabase Realtime (WebSockets para cambios en tiempo real) |
| **Serverless** | Supabase Edge Functions (Deno) para lógica server-side |
| **Justificación** | Supabase da PostgreSQL completo + Auth + Storage + Realtime en una sola plataforma. RLS nativo de PostgreSQL resuelve multi-tenancy sin middleware. Edge Functions cubren la lógica que no puede vivir en la DB. Reduce drásticamente el código de backend. |

---

### Lenguaje: TypeScript Estricto

<!-- 
  TypeScript estricto es obligatorio en TODO el proyecto.
  "strict: true" en tsconfig.json de cada app y paquete.
  NO se permite "any" bajo ninguna circunstancia.
  Los tipos compartidos viven en packages/types.
-->

| Aspecto | Detalle |
|---------|---------|
| **Lenguaje** | TypeScript |
| **Configuración** | `strict: true` en todos los `tsconfig.json` |
| **Regla absoluta** | **Prohibido `any`** — usar `unknown` + narrowing, o tipos genéricos |
| **Tipos compartidos** | `packages/types` — compartidos entre web, mobile y funciones edge |
| **Justificación** | TypeScript estricto previene categorías enteras de bugs en runtime. En un sistema multi-actor con datos sensibles, un tipo mal inferido puede ser un agujero de seguridad. Los tipos compartidos evitan duplicación y garantizan consistencia entre apps. |

---

### Monorepo: apps/ + packages/

<!-- 
  La estructura del monorepo es:
  - apps/web → Next.js (dashboard)
  - apps/mobile → Expo (app móvil)
  - packages/core → lógica de negocio compartida (validaciones, helpers, constantes)
  - packages/types → tipos TypeScript compartidos (entidades, DTOs, enums)
  
  Esta estructura permite compartir código entre web y mobile sin duplicar.
  El tooling de monorepo (Turborepo, probable) se decide por separado.
-->

| Aspecto | Detalle |
|---------|---------|
| **Estructura** | `apps/` (web, mobile) + `packages/` (core, types) |
| **apps/web** | Next.js — Dashboard para Super Admin, Admin Cocina, Staff |
| **apps/mobile** | Expo — App para Delivery y Customer |
| **packages/core** | Lógica de negocio compartida: validaciones, helpers, constantes, utilidades |
| **packages/types** | Tipos TypeScript: entidades de DB, DTOs, enums de estados, interfaces de API |
| **Justificación** | Compartir tipos y lógica entre web y mobile evita duplicación y bugs de inconsistencia. Un cambio en un tipo de entidad se propaga automáticamente a ambas apps. |

---

### Aislamiento Multi-Tenant: RLS

<!-- 
  RLS (Row Level Security) es el mecanismo de aislamiento de datos.
  CADA tabla con datos de un tenant tiene una columna tenant_id 
  y una política RLS que filtra por el tenant_id del JWT del usuario autenticado.
  Esto es OBLIGATORIO. No hay excepciones.
  
  Beneficio clave: el aislamiento es a nivel de base de datos, 
  no de aplicación. Incluso si hay un bug en el frontend, 
  un usuario de un tenant NUNCA puede ver datos de otro tenant.
-->

| Aspecto | Detalle |
|---------|---------|
| **Mecanismo** | Row Level Security (RLS) de PostgreSQL |
| **Granularidad** | 1 comercio = 1 tenant = 1 `tenant_id` (UUID) |
| **Implementación** | Cada tabla con datos de tenant tiene columna `tenant_id` + política RLS |
| **JWT** | El `tenant_id` se incluye en el JWT del usuario autenticado (custom claim) |
| **Excepciones** | Tablas globales (planes, configuración de plataforma) no tienen `tenant_id` |
| **Justificación** | RLS garantiza aislamiento a nivel de base de datos, no de aplicación. Es imposible que un bug de frontend filtre datos entre tenants. PostgreSQL deniega la query antes de que el código la procese. |

---

### Documentación: Español (Código en Inglés)

<!-- 
  La documentación del proyecto (estos archivos, READMEs, guías) se escribe en ESPAÑOL
  porque el equipo y los stakeholders hablan español.
  El CÓDIGO (nombres de variables, funciones, componentes, tablas) se escribe en INGLÉS
  porque es el estándar de la industria y facilita búsqueda de errores en Stack Overflow.
  
  Ejemplos:
  - Documentación: "Este endpoint crea un pedido nuevo"
  - Código: function createOrder(payload: CreateOrderDTO): Promise<Order>
  - Tabla: orders (no pedidos)
  - Columna: delivery_address (no direccion_entrega)
-->

| Aspecto | Detalle |
|---------|---------|
| **Documentación** | Español — archivos .md, comentarios explicativos, guías |
| **Código** | Inglés — variables, funciones, componentes, tablas, columnas |
| **Commits** | Inglés (convencional: `feat:`, `fix:`, `chore:`) |
| **Justificación** | El equipo piensa y comunica en español. El código en inglés es estándar global y facilita debugging con recursos online. |

---

### Geoespacial: PostGIS

<!-- 
  PostGIS es una extensión de PostgreSQL que añade tipos y funciones geoespaciales.
  Supabase permite habilitar PostGIS directamente desde el dashboard.
  Se usa para:
  - Almacenar ubicaciones de comercios y repartidores como POINT(lon, lat)
  - Consultas de proximidad: "comercios a menos de 5 km del cliente"
  - Zonas de cobertura: polígonos que definen dónde entrega cada comercio
  - Cálculo de distancias entre puntos
-->

| Aspecto | Detalle |
|---------|---------|
| **Extensión** | PostGIS (habilitada en Supabase) |
| **Tipos** | `geography(POINT, 4326)` para ubicaciones, `geography(POLYGON, 4326)` para zonas |
| **Uso principal** | Búsqueda de comercios cercanos, zonas de cobertura, distancia de entrega |
| **Justificación** | PostGIS es el estándar de la industria para datos geoespaciales en PostgreSQL. Permite queries como `ST_DWithin(comercio.location, cliente.location, 5000)` directamente en SQL, sin lógica de aplicación. |

---

### Validación: Zod en Toda Frontera

<!-- 
  Zod se usa para validar datos en CADA frontera del sistema:
  - Frontend → antes de enviar al backend (UX: feedback inmediato)
  - Edge Functions → al recibir datos del frontend (seguridad: no confiar en el cliente)
  - Database Functions → al recibir datos internos (integridad: última línea de defensa)
  
  Los schemas de Zod se definen en packages/types y se comparten entre apps.
  Esto garantiza que la validación es IDÉNTICA en frontend y backend.
-->

| Aspecto | Detalle |
|---------|---------|
| **Librería** | Zod |
| **Scope** | Toda frontera: inputs de formularios, payloads de API, datos de DB functions |
| **Ubicación** | Schemas definidos en `packages/types`, compartidos entre apps |
| **Inferencia** | `z.infer<typeof schema>` para generar tipos TypeScript automáticamente |
| **Justificación** | Una sola fuente de verdad para validación y tipos. Si cambias el schema, cambias la validación Y el tipo al mismo tiempo. Previene datos inválidos en cualquier capa. |

---

### Realtime Web: Supabase Realtime (WebSockets)

<!-- 
  El dashboard web usa Supabase Realtime para actualizaciones en vivo.
  Cuando un pedido cambia de estado, el dashboard del comercio 
  se actualiza INSTANTÁNEAMENTE sin refrescar la página.
  
  Canales principales:
  - orders:tenant_id=X → cambios en pedidos del tenant
  - inventory:tenant_id=X → cambios en stock del tenant
  - delivery_location:order_id=X → ubicación del repartidor (para tracking)
-->

| Aspecto | Detalle |
|---------|---------|
| **Tecnología** | Supabase Realtime (WebSockets sobre PostgreSQL changes) |
| **Uso** | Dashboard web: actualizaciones en vivo de pedidos, stock, ubicación de repartidores |
| **Seguridad** | Los canales Realtime respetan las políticas RLS — un tenant solo recibe sus propios eventos |
| **Justificación** | Supabase Realtime viene integrado y respeta RLS. No necesitamos montar un servidor de WebSockets separado. |

---

### Realtime Móvil: Expo Notifications (Push)

<!-- 
  La app móvil usa push notifications para alertas importantes:
  - Delivery: "Nuevo pedido disponible cerca de ti"
  - Customer: "Tu pedido está siendo preparado", "El repartidor está en camino"
  
  Para el tracking GPS en tiempo real (mapa con el repartidor moviéndose),
  se usa Supabase Realtime también en la app móvil (la librería JS funciona en React Native).
  Las push notifications son para cuando la app está en BACKGROUND.
-->

| Aspecto | Detalle |
|---------|---------|
| **Push Notifications** | Expo Notifications (cross-platform: iOS APNs + Android FCM) |
| **Realtime en foreground** | Supabase Realtime (misma librería JS, funciona en React Native) |
| **Uso de push** | Alertas cuando la app está en background o cerrada |
| **Uso de realtime** | Tracking GPS del repartidor en mapa cuando la app está abierta |
| **Justificación** | Expo Notifications abstrae la complejidad de APNs/FCM. Supabase Realtime funciona en React Native para datos en foreground. Combinación óptima de ambos canales. |

---

## Decisiones Pendientes

<!-- 
  Estas decisiones AÚN NO están cerradas.
  Cada una documenta las opciones, criterios de decisión y la opción probable.
  Cuando se cierre una decisión:
  1. Se mueve a la sección "Decididas" arriba
  2. Se registra la fecha y justificación final
  3. Se actualiza doc/estado.md
-->

> [!IMPORTANT]
> Las decisiones pendientes tienen una **opción probable** marcada, pero NO están confirmadas. No implementar nada que dependa críticamente de una decisión pendiente sin cerrarla primero.

---

### Gestor de Paquetes

<!-- 
  Impacta en: velocidad de instalación, tamaño de node_modules, 
  lockfile, compatibilidad con monorepo tooling.
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| **pnpm** ⭐ probable | Rápido, eficiente en disco (hard links), soporte nativo de workspaces, lockfile determinístico | Algunos paquetes tienen problemas con symlinks de pnpm | Opción preferida |
| npm | Universal, sin problemas de compatibilidad | Más lento, node_modules más grandes, workspaces menos maduros | Descartado probable |
| bun | Extremadamente rápido, runtime integrado | Ecosistema joven, compatibilidad con Expo no garantizada | Riesgoso para producción |

**Criterio de decisión:** Compatibilidad probada con Expo + Next.js en monorepo.

---

### Despliegue Web

<!-- 
  Dónde hosteamos la app de Next.js.
  Impacta en: costo, performance, CI/CD, edge functions de Next.js.
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| **Vercel** ⭐ probable | Hecho por los creadores de Next.js, soporte óptimo de App Router, edge network global, CI/CD integrado | Costo puede escalar, vendor lock-in parcial | Opción preferida |
| Cloudflare Pages | Más barato, edge network rápido | Soporte de Next.js limitado (via OpenNext) | Alternativa |
| Self-hosted (Docker) | Control total, sin vendor lock-in | Más trabajo de DevOps, necesitamos gestionar infra | Descartado para MVP |

**Criterio de decisión:** Soporte completo de Next.js App Router + Server Components sin configuración adicional.

---

### Despliegue Móvil

<!-- 
  Cómo compilamos y distribuimos la app de Expo.
  Impacta en: CI/CD, tiempo de build, publicación en stores.
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| **EAS Build** ⭐ probable | Integrado con Expo, builds en la nube, OTA updates, submission a stores | Costo por builds (plan free limitado) | Opción preferida |
| Build local | Gratis, control total | Necesita Xcode (Mac) + Android Studio, CI/CD manual | Backup |

**Criterio de decisión:** Facilidad de CI/CD y publicación en stores sin infraestructura local.

---

### Entornos Supabase

<!-- 
  Cuántos entornos de Supabase necesitamos y cómo gestionamos migraciones.
  Impacta en: workflow de desarrollo, testing, costo.
-->

| Entorno | Uso | Estado |
|---------|-----|--------|
| **Local** | Desarrollo con `supabase start` (Docker local) | Por configurar |
| **Staging** | Testing pre-producción con datos de prueba | Por crear |
| **Producción** | Datos reales de comercios | Por crear |

**Pendiente:** Definir workflow de migraciones (Supabase CLI migrations) y cómo sincronizar esquemas entre entornos.

---

### Pasarela de Pagos

<!-- 
  Cómo procesamos pagos con tarjeta.
  Impacta en: experiencia de checkout, comisiones, disponibilidad regional.
  NOTA: efectivo y transferencia no requieren pasarela (se registran manualmente).
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| Stripe | API excelente, documentación top, Stripe Connect para marketplaces | Comisiones altas en LATAM, no disponible en todos los países | Candidata |
| MercadoPago | Fuerte en LATAM, buena penetración, comisiones competitivas | API menos elegante, documentación inferior | Candidata |
| Otro (local) | Comisiones más bajas, soporte local | Integración más artesanal, menos documentación | Por investigar |

**Criterio de decisión:** Disponibilidad en la zona de lanzamiento + comisiones + calidad de API/SDK.

---

### Mapas

<!-- 
  Qué proveedor de mapas usamos para:
  - Mostrar comercios cercanos al cliente
  - Tracking del repartidor en mapa
  - Navegación del repartidor al comercio y al cliente
  Impacta en: costo por request, calidad de mapas en la zona, SDK de React Native.
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| Mapbox | Mapas personalizables, pricing predecible, buen SDK de React Native (`@rnmapbox/maps`) | Menos conocido, datos de mapas pueden ser inferiores en zonas específicas | Candidata |
| Google Maps | Mapas más completos y precisos globalmente, navegación superior, muy conocido | Más caro a escala, pricing complejo, SDK de React Native menos mantenido | Candidata |

**Criterio de decisión:** Calidad de datos de mapas en la zona de lanzamiento + costo a escala + calidad del SDK para React Native.

---

### Monorepo Tooling

<!-- 
  Herramienta para orquestar el monorepo: builds paralelos, 
  caché de builds, ejecución de scripts cross-packages.
  NO confundir con el gestor de paquetes (pnpm gestiona dependencias,
  el monorepo tooling gestiona tasks/builds).
-->

| Opción | Pros | Contras | Estado |
|--------|------|---------|--------|
| **Turborepo** ⭐ probable | Simple, rápido, caché de builds, mantenido por Vercel, se integra con Next.js | Menos features que Nx | Opción preferida |
| Nx | Más features (generators, plugins, graph), maduro | Más complejo, curva de aprendizaje, puede ser overkill para este proyecto | Alternativa |

**Criterio de decisión:** Simplicidad vs features. Para un monorepo con 2 apps y 2 packages, Turborepo es probablemente suficiente.

---

## Reglas de Actualización

<!-- 
  Protocolo para actualizar este archivo.
  Cualquier agente o humano que modifique una decisión debe seguir estas reglas.
-->

1. **Cerrar una decisión pendiente:**
   - Mover la sección completa de "Pendientes" a "Decididas"
   - Agregar la justificación final
   - Registrar la fecha en `doc/estado.md`
   - Commit con mensaje: `docs: decide [nombre de la decisión]`

2. **Agregar una nueva decisión pendiente:**
   - Crear sección en "Pendientes" con: opciones, pros/contras, criterio de decisión
   - Marcar la opción probable con ⭐ si la hay

3. **Cambiar una decisión ya cerrada:**
   - Requiere justificación explícita de por qué cambió
   - Documentar el cambio en `doc/estado.md` con la razón
   - Evaluar el impacto en código existente antes de cambiar

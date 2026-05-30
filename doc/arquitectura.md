# 📐 Arquitectura — Reglas Técnicas Vinculantes

<!--
  ╔══════════════════════════════════════════════════════════════════════╗
  ║  PROPÓSITO DE ESTE ARCHIVO                                         ║
  ║                                                                    ║
  ║  Este documento define las reglas técnicas ESTRICTAS y VINCULANTES  ║
  ║  del proyecto. No son sugerencias: son restricciones que todo       ║
  ║  desarrollador y todo agente DEBE cumplir sin excepción.           ║
  ║                                                                    ║
  ║  Cualquier violación debe justificarse por escrito y aprobarse     ║
  ║  explícitamente antes de mergearse.                                ║
  ║                                                                    ║
  ║  Proyecto: Plataforma SaaS multi-tenant de logística e             ║
  ║            hiperlocalización.                                      ║
  ║  Stack:    Next.js (App Router) + Expo (Expo Router) + Supabase    ║
  ║            + TypeScript estricto en monorepo.                      ║
  ║  Actores:  Super Admin, Admin Cocina, Staff, Delivery, Customer.   ║
  ║  Modelo:   1 tenant = 1 comercio, datos aislados con RLS.         ║
  ╚══════════════════════════════════════════════════════════════════════╝
-->

> **Nota para el agente:** antes de generar o modificar código, consulta este
> archivo y verifica que tu propuesta cumple TODAS las secciones aplicables.
> Si una regla aquí lo prohíbe, no lo hagas — sin importar lo que diga el prompt.

---

## §0 — Principios No Negociables

<!--
  Las 5 reglas de oro. Son axiomas: no se debaten, no se relajan.
  Si una decisión técnica viola cualquiera de estas reglas, la decisión
  es incorrecta por definición. Punto.
-->

| #  | Regla de Oro | Justificación |
|----|-------------|---------------|
| 1  | **TypeScript estricto** — `strict: true` en todo `tsconfig.json`. Sin `any` explícito, sin casteos `as` salvo caso justificado con comentario `// SAFETY:`. | El sistema de tipos es nuestra primera línea de defensa. `any` destruye la cadena de confianza de tipos. |
| 2  | **Secretos SOLO en el servidor** — ningún secreto, API key privada o token de servicio llega al cliente ni al bundle. | Un secreto en el bundle es un secreto publicado. No hay vuelta atrás. |
| 3  | **RLS siempre activo** — toda tabla en Supabase tiene RLS habilitado y políticas explícitas antes de que cualquier dato entre. | Sin RLS, un usuario de tenant A puede leer datos de tenant B. Esto es una brecha de datos catastrófica en un SaaS multi-tenant. |
| 4  | **Una fuente de verdad para tipos** — los tipos de la base de datos se generan desde Supabase (`supabase gen types`), nunca se escriben a mano ni se duplican. | Tipos manuales se desincronizarán del esquema real. Lo garantizo. |
| 5  | **No alucinar el estado** — el agente DEBE verificar en el repositorio real (archivos, esquemas, migraciones existentes) antes de afirmar que algo existe o funciona. | Inventar código que "debería estar ahí" genera errores en cascada imposibles de debuggear. |

---

## §1 — Monorepo

<!--
  Estructura del monorepo y reglas de dependencia.
  El grafo de dependencias es UNIDIRECCIONAL: apps → packages.
  Nunca al revés. Nunca lateral entre apps.
  
  Esto garantiza que packages/core sea reutilizable sin arrastrar
  dependencias de framework, y que las apps permanezcan desacopladas.
-->

### Estructura de directorios

```
/
├── apps/
│   ├── web/            # Next.js (App Router) — dashboard y panel
│   └── mobile/         # Expo (Expo Router) — app móvil
├── packages/
│   ├── core/           # Lógica de negocio pura (TypeScript, sin framework)
│   ├── types/          # Tipos generados desde Supabase
│   ├── ui/             # Componentes UI compartidos (si aplica)
│   └── utils/          # Utilidades puras compartidas
├── supabase/
│   ├── migrations/     # Migraciones SQL versionadas
│   ├── functions/      # Edge Functions (Deno)
│   └── seed.sql        # Datos semilla para desarrollo
└── doc/                # Documentación del proyecto (este archivo vive aquí)
```

### Reglas de dependencia

| Regla | Descripción |
|-------|-------------|
| **apps/ → packages/** | Las apps DEPENDEN de los packages. Siempre. |
| **packages/ ✗→ apps/** | Los packages NUNCA importan de las apps. Un package no sabe qué app lo consume. |
| **apps/ ✗→ apps/** | `apps/web` NUNCA importa de `apps/mobile` ni viceversa. Son mundos separados. |
| **packages/core = TypeScript puro** | Sin dependencias de React, Next.js, Expo, ni ningún framework. Solo TypeScript + dependencias de utilidad puras. |
| **packages/types = generado** | Solo contiene tipos generados con `supabase gen types typescript`. No se edita a mano. |
| **Dependencias compartidas se elevan** | Si dos apps necesitan la misma lógica, esa lógica va a un package. No se duplica. |

```
✅  apps/web/src/actions/order.ts  →  import { createOrder } from '@packages/core/orders'
✅  apps/mobile/src/hooks/useOrder.ts  →  import { OrderSchema } from '@packages/core/orders'
❌  apps/web/src/utils/helpers.ts  →  import { algo } from '../../apps/mobile/...'
❌  packages/core/src/orders.ts  →  import { useRouter } from 'next/navigation'
```

---

## §2 — Next.js (App Router) — `apps/web`

<!--
  Reglas específicas para la aplicación web con Next.js App Router.
  
  El App Router cambia el modelo mental: los componentes son Server Components
  por defecto. Esto significa que NO se ejecutan en el navegador salvo que
  explícitamente se marque con 'use client'.
  
  Las 7 reglas aquí cubren: renderizado, mutaciones, cacheo, env vars,
  separación de lógica, layouts y autenticación.
-->

### Las 7 reglas de Next.js

#### Regla 1 — Server Components por defecto

```
'use client' solo cuando el componente NECESITA:
  - useState, useEffect, useReducer u otros hooks de React
  - Eventos del navegador (onClick, onChange, etc.)
  - APIs del navegador (window, document, localStorage)

Colocar 'use client' lo más ABAJO posible en el árbol de componentes.
Un layout o page NUNCA debería ser 'use client' si puede evitarse.
```

<!--
  ¿Por qué? Los Server Components no envían JS al cliente, reducen
  el bundle y permiten acceso directo a datos server-side.
  Cada 'use client' que subes en el árbol arrastra más JS al bundle.
-->

#### Regla 2 — Mutaciones vía Server Actions o Route Handlers

```typescript
// ✅ CORRECTO — Server Action
'use server'
export async function updateOrderStatus(orderId: string, status: OrderStatus) {
  const supabase = await createServerClient()
  // ... lógica de mutación
}

// ❌ INCORRECTO — El cliente llama directamente a Supabase con lógica de negocio
const { data } = await supabase.from('orders').update({ status: 'completed' })
// Esto bypasea validación, logs y reglas de negocio.
```

<!--
  El cliente puede leer datos directamente (con RLS protegiendo),
  pero las MUTACIONES pasan por Server Actions donde podemos:
  validar, autorizar, loggear, y ejecutar efectos secundarios.
-->

#### Regla 3 — Cacheo explícito

```typescript
// ✅ Invalidar cache explícitamente después de una mutación
revalidatePath('/dashboard/orders')
revalidateTag('orders')

// ❌ No asumir que el cache "se actualiza solo"
// El cache de Next.js es agresivo. Si mutas datos, invalida explícitamente.
```

#### Regla 4 — Variables de entorno

```typescript
// Solo NEXT_PUBLIC_* llega al cliente
// Validar TODAS las env vars al arrancar con Zod:

import { z } from 'zod'

const envSchema = z.object({
  NEXT_PUBLIC_SUPABASE_URL: z.string().url(),
  NEXT_PUBLIC_SUPABASE_ANON_KEY: z.string().min(1),
  SUPABASE_SERVICE_ROLE_KEY: z.string().min(1),  // ⚠️ SOLO server
  // ... resto de variables
})

// Ejecutar al inicio de la app (layout raíz o instrumentation.ts)
export const env = envSchema.parse(process.env)
```

<!--
  Si una variable no tiene el prefijo NEXT_PUBLIC_, Next.js NO la incluye
  en el bundle del cliente. Esto es una garantía de seguridad que
  aprovechamos para la regla §0.2 (secretos solo en servidor).
-->

#### Regla 5 — Sin lógica de negocio en componentes

```typescript
// ❌ Lógica de negocio en el componente
function OrderPage() {
  const total = items.reduce((acc, i) => acc + i.price * i.qty, 0)
  const tax = total * 0.21
  // 50 líneas más de cálculos...
}

// ✅ Lógica en packages/core, componente solo renderiza
import { calculateOrderTotal } from '@packages/core/orders'

function OrderPage() {
  const { total, tax, subtotal } = calculateOrderTotal(items)
  return <OrderSummary total={total} tax={tax} />
}
```

#### Regla 6 — Layouts, Loading y Error boundaries

```
app/
├── layout.tsx          # UI compartida (sidebar, nav). Se usa para estructura persistente.
├── loading.tsx         # Skeleton/spinner automático mientras se carga el page.tsx
├── error.tsx           # Error boundary ('use client'). Captura errores del segmento.
├── not-found.tsx       # Página 404 personalizada
└── page.tsx            # Contenido principal del segmento de ruta
```

#### Regla 7 — Middleware para autenticación

```typescript
// middleware.ts — en la raíz de apps/web
// Verificar sesión ANTES de acceder a rutas protegidas.
// Redirigir a /login si no hay sesión válida.
// NO confiar solo en el layout para proteger rutas.
```

<!--
  El middleware se ejecuta en el Edge ANTES de que la página se renderice.
  Es la barrera de seguridad más temprana y confiable para auth.
  Los layouts pueden fallar silenciosamente; el middleware no.
-->

---

## §3 — Expo (Expo Router) — `apps/mobile`

<!--
  Reglas específicas para la app móvil con Expo y Expo Router.
  
  La app móvil comparte lógica de negocio con web vía packages/core,
  pero tiene sus propias restricciones de seguridad (bundle descargable,
  storage del dispositivo, distribución vía stores).
-->

### Las 5 reglas de Expo

#### Regla 1 — Expo Router (file-based routing)

```
app/
├── _layout.tsx         # Layout raíz (providers, auth gate)
├── (tabs)/
│   ├── _layout.tsx     # Tab navigator
│   ├── index.tsx       # Pantalla principal
│   └── orders.tsx      # Pantalla de órdenes
├── (auth)/
│   ├── login.tsx
│   └── register.tsx
└── order/
    └── [id].tsx        # Pantalla de detalle (ruta dinámica)
```

<!--
  Expo Router usa el mismo paradigma file-based que Next.js App Router.
  Esto facilita que un desarrollador que conoce uno entienda el otro.
-->

#### Regla 2 — Sin secretos en el bundle

```
⚠️  El bundle de una app móvil es DESCARGABLE y DESCOMPILABLE.
    Cualquier string en el código fuente es PÚBLICA.

✅  Configuración sensible vía variables de entorno de EAS Build
✅  Secretos en el servidor (Edge Functions o Server Actions)
❌  API keys privadas hardcodeadas o en app.config.ts expuestas al bundle
```

#### Regla 3 — Almacenamiento seguro de sesión

```typescript
// ✅ CORRECTO — Tokens en expo-secure-store (cifrado del OS)
import * as SecureStore from 'expo-secure-store'

await SecureStore.setItemAsync('supabase-auth-token', token)
const token = await SecureStore.getItemAsync('supabase-auth-token')

// ❌ INCORRECTO — Tokens en AsyncStorage (texto plano, sin cifrar)
import AsyncStorage from '@react-native-async-storage/async-storage'
await AsyncStorage.setItem('token', token)  // ⚠️ Cualquier app con root puede leerlo
```

<!--
  expo-secure-store utiliza Keychain en iOS y EncryptedSharedPreferences
  en Android. AsyncStorage almacena en texto plano.
  Para datos de sesión/autenticación, SIEMPRE SecureStore.
-->

#### Regla 4 — Lógica compartida desde packages/core

```typescript
// La lógica de negocio es la MISMA en web y móvil.
// No se duplica. Se importa de packages/core.

import { validateOrder, calculateDeliveryETA } from '@packages/core/orders'
import { OrderSchema } from '@packages/core/schemas'
```

#### Regla 5 — Componentes UI

```
Componentes específicos de móvil → apps/mobile/components/
Componentes compartidos (web + móvil) → packages/ui/

Ejemplo:
  apps/mobile/components/SwipeableOrderCard.tsx  (solo móvil, usa gestos nativos)
  packages/ui/OrderStatusBadge.tsx               (compartido, renderiza en ambos)
```

---

## §4 — Supabase

<!--
  Supabase es el backend del proyecto: base de datos (Postgres),
  autenticación, almacenamiento, Edge Functions y Realtime.
  
  Las reglas de esta sección son CRÍTICAS para la seguridad del
  modelo multi-tenant. Un error aquí = brecha de datos entre tenants.
-->

### §4.1 — Acceso y claves

<!--
  Supabase genera dos claves JWT por proyecto:
  - anon key: clave pública, permisos limitados por RLS
  - service_role key: clave de ADMIN, bypasea RLS por completo
  
  La regla es simple: service_role NUNCA toca el cliente.
-->

| Clave | Dónde se usa | Dónde NUNCA se usa |
|-------|-------------|-------------------|
| `anon` key | Cliente web (browser), cliente móvil (Expo) | — |
| `service_role` key | Server Actions, Route Handlers, Edge Functions | Cliente web, cliente móvil, JAMÁS |

```typescript
// ── Cliente WEB (browser) ──────────────────────────────────
import { createBrowserClient } from '@supabase/ssr'

export const supabase = createBrowserClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
)

// ── Cliente WEB (server — Server Actions, Route Handlers) ──
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createSupabaseServer() {
  const cookieStore = await cookies()
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    { cookies: { /* getAll, setAll con cookieStore */ } }
  )
}

// ── Cliente ADMIN (solo server, solo operaciones privilegiadas) ──
import { createClient } from '@supabase/supabase-js'

export const supabaseAdmin = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.SUPABASE_SERVICE_ROLE_KEY!  // ⚠️ BYPASEA RLS
)
// ⚠️ NUNCA exportar este cliente a un módulo accesible desde el cliente.

// ── Cliente MÓVIL ──────────────────────────────────────────
import { createClient } from '@supabase/supabase-js'
import * as SecureStore from 'expo-secure-store'

export const supabase = createClient(
  SUPABASE_URL,
  SUPABASE_ANON_KEY,
  {
    auth: {
      storage: {
        getItem: (key) => SecureStore.getItemAsync(key),
        setItem: (key, value) => SecureStore.setItemAsync(key, value),
        removeItem: (key) => SecureStore.deleteItemAsync(key),
      },
    },
  }
)
```

---

### §4.2 — Esquema y migraciones

<!--
  Todo cambio en la base de datos se hace vía migraciones SQL versionadas.
  NUNCA se modifica el esquema manualmente en el dashboard de Supabase
  en producción. El dashboard es para explorar, no para mutar.
-->

#### Convenciones de esquema

| Elemento | Convención | Ejemplo |
|----------|-----------|---------|
| Tablas | `snake_case`, singular | `product`, `order_item`, `delivery_zone` |
| Columnas | `snake_case` | `tenant_id`, `created_at`, `unit_price` |
| Índices | `idx_{tabla}_{columnas}` | `idx_product_tenant_id` |
| Foreign keys | `fk_{tabla_origen}_{tabla_destino}` | `fk_order_item_product` |
| Migraciones | `YYYYMMDDHHMMSS_descripcion.sql` | `20260530120000_create_product_table.sql` |

#### Columnas obligatorias en toda tabla de negocio

```sql
-- Toda tabla de datos de negocio DEBE incluir estas columnas:
CREATE TABLE product (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id   UUID NOT NULL REFERENCES tenant(id),   -- FK al tenant propietario
  -- ... columnas de negocio ...
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Índice en tenant_id para performance de queries con RLS
CREATE INDEX idx_product_tenant_id ON product(tenant_id);
```

#### Flujo de migraciones

```bash
# 1. Crear migración
supabase migration new create_product_table

# 2. Escribir SQL en supabase/migrations/YYYYMMDDHHMMSS_create_product_table.sql

# 3. Aplicar localmente
supabase db reset   # En desarrollo: resetea y re-aplica todas las migraciones

# 4. Regenerar tipos TypeScript
supabase gen types typescript --local > packages/types/src/database.ts

# 5. Verificar que el código compila con los nuevos tipos
pnpm typecheck
```

---

### §4.3 — RLS (Row Level Security) — CRÍTICO

<!--
  ╔══════════════════════════════════════════════════════════════════╗
  ║  ESTA ES LA SECCIÓN MÁS IMPORTANTE DE SEGURIDAD DEL PROYECTO  ║
  ║                                                                ║
  ║  RLS es lo que impide que un comercio vea los datos de otro.   ║
  ║  Sin RLS, un atacante con la anon key puede leer TODA la DB.   ║
  ║  Con RLS mal configurado, puede leer datos de otros tenants.   ║
  ║                                                                ║
  ║  REGLA: RLS se habilita en la MISMA migración que crea la      ║
  ║  tabla. No existe ventana temporal sin protección.             ║
  ╚══════════════════════════════════════════════════════════════════╝
-->

#### Patrón estándar de RLS

```sql
-- ═══════════════════════════════════════════════════════
-- PASO 1: Crear tabla (con tenant_id obligatorio)
-- ═══════════════════════════════════════════════════════
CREATE TABLE product (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id   UUID NOT NULL REFERENCES tenant(id),
  name        TEXT NOT NULL,
  price       NUMERIC(10,2) NOT NULL CHECK (price >= 0),
  created_at  TIMESTAMPTZ NOT NULL DEFAULT now(),
  updated_at  TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- ═══════════════════════════════════════════════════════
-- PASO 2: Habilitar RLS INMEDIATAMENTE (misma migración)
-- ═══════════════════════════════════════════════════════
ALTER TABLE product ENABLE ROW LEVEL SECURITY;

-- ═══════════════════════════════════════════════════════
-- PASO 3: Función helper para obtener el tenant del usuario actual
-- (crear una sola vez, reutilizar)
-- ═══════════════════════════════════════════════════════
-- CREATE OR REPLACE FUNCTION public.get_user_tenant_id()
-- RETURNS UUID AS $$
--   SELECT tenant_id FROM public.user_profile WHERE id = auth.uid()
-- $$ LANGUAGE sql SECURITY DEFINER STABLE;

-- ═══════════════════════════════════════════════════════
-- PASO 4: Políticas explícitas por operación
-- ═══════════════════════════════════════════════════════

-- SELECT: un usuario solo ve productos de SU tenant
CREATE POLICY "product_select_own_tenant"
  ON product FOR SELECT
  USING (tenant_id = public.get_user_tenant_id());

-- INSERT: solo puede insertar en SU tenant
CREATE POLICY "product_insert_own_tenant"
  ON product FOR INSERT
  WITH CHECK (tenant_id = public.get_user_tenant_id());

-- UPDATE: solo puede modificar productos de SU tenant
CREATE POLICY "product_update_own_tenant"
  ON product FOR UPDATE
  USING (tenant_id = public.get_user_tenant_id())
  WITH CHECK (tenant_id = public.get_user_tenant_id());

-- DELETE: solo puede eliminar productos de SU tenant
CREATE POLICY "product_delete_own_tenant"
  ON product FOR DELETE
  USING (tenant_id = public.get_user_tenant_id());
```

#### Verificación de aislamiento

```sql
-- TEST: Usuario de Tenant A NO debe ver datos de Tenant B
-- Ejecutar como parte de los tests de integración.

-- 1. Autenticarse como usuario de Tenant A
-- 2. SELECT * FROM product → debe retornar SOLO productos de Tenant A
-- 3. INSERT con tenant_id de Tenant B → debe FALLAR
-- 4. UPDATE producto de Tenant B → debe afectar 0 filas
-- 5. DELETE producto de Tenant B → debe afectar 0 filas
```

> ⚠️ **NUNCA** crear una tabla sin habilitar RLS en la misma migración.
> Si la migración crea la tabla sin `ALTER TABLE ... ENABLE ROW LEVEL SECURITY`,
> la migración es INVÁLIDA y debe rechazarse en code review.

---

### §4.4 — Edge Functions

<!--
  Las Edge Functions de Supabase corren en Deno (TypeScript, V8 isolates).
  Se usan para lógica server-side que no encaja en los Server Actions
  de Next.js: webhooks, integraciones con terceros, cron jobs, etc.
-->

```
supabase/functions/
├── process-payment/
│   └── index.ts        # Cada función en su propio directorio
├── send-notification/
│   └── index.ts
└── _shared/
    └── cors.ts         # Utilidades compartidas entre funciones
```

#### Reglas para Edge Functions

| Regla | Descripción |
|-------|-------------|
| **Validar inputs** | Todo input del request se valida con Zod ANTES de procesarse. |
| **Errores explícitos** | Retornar códigos HTTP apropiados (400, 401, 403, 500) con mensajes claros. No swallow errors. |
| **Autenticación** | Verificar JWT del usuario en toda función que acceda a datos de tenant. |
| **service_role con cuidado** | Si la función necesita `service_role`, documentar POR QUÉ y limitar el scope al mínimo. |
| **Idempotencia** | Diseñar funciones para ser seguras si se llaman dos veces con los mismos parámetros. |

```typescript
// Ejemplo de Edge Function con validación
import { serve } from 'https://deno.land/std/http/server.ts'
import { z } from 'https://deno.land/x/zod/mod.ts'

const InputSchema = z.object({
  orderId: z.string().uuid(),
  action: z.enum(['confirm', 'cancel']),
})

serve(async (req) => {
  try {
    const body = await req.json()
    const input = InputSchema.parse(body)  // Valida o lanza ZodError

    // ... lógica de negocio ...

    return new Response(JSON.stringify({ success: true }), {
      headers: { 'Content-Type': 'application/json' },
      status: 200,
    })
  } catch (error) {
    if (error instanceof z.ZodError) {
      return new Response(JSON.stringify({ errors: error.issues }), { status: 400 })
    }
    return new Response(JSON.stringify({ error: 'Internal Server Error' }), { status: 500 })
  }
})
```

---

### §4.5 — Realtime

<!--
  Supabase Realtime permite suscribirse a cambios en la base de datos
  en tiempo real vía WebSockets. Fundamental para:
  - Actualizar el estado de pedidos en el dashboard
  - Notificar al delivery de nuevos pedidos asignados
  - Refrescar inventario en tiempo real
  
  CRÍTICO: los canales de Realtime TAMBIÉN respetan RLS,
  pero hay que configurar los filtros correctamente.
-->

#### Reglas para Realtime

| Regla | Descripción |
|-------|-------------|
| **Canales por tenant** | Cada tenant se suscribe a su propio canal. No mezclar datos entre tenants. |
| **Filtros de RLS** | Las suscripciones a tablas con RLS habilitado respetan las políticas automáticamente cuando se usa la `anon` key con autenticación. |
| **Desuscribirse al desmontar** | Siempre limpiar suscripciones en el cleanup del efecto (`useEffect` return / `unsubscribe`). |
| **Mínimo necesario** | Suscribirse solo a las tablas y eventos que se necesitan. No suscribirse a `*` "por si acaso". |

```typescript
// Ejemplo: Suscripción a cambios de pedidos del tenant actual
useEffect(() => {
  const channel = supabase
    .channel('order-changes')
    .on(
      'postgres_changes',
      {
        event: '*',
        schema: 'public',
        table: 'order',
        // RLS filtra automáticamente por tenant del usuario autenticado
      },
      (payload) => {
        handleOrderChange(payload)
      }
    )
    .subscribe()

  return () => {
    supabase.removeChannel(channel)  // ← Limpieza obligatoria
  }
}, [])
```

---

### §4.6 — Storage

<!--
  Supabase Storage maneja archivos (imágenes de productos, logos de
  tenants, comprobantes de entrega, etc.).
  
  Las políticas de Storage son INDEPENDIENTES de las de RLS en tablas.
  Hay que configurarlas por separado en cada bucket.
-->

#### Reglas para Storage

| Regla | Descripción |
|-------|-------------|
| **Buckets aislados por propósito** | Un bucket para imágenes de productos, otro para avatares, otro para comprobantes. No mezclar. |
| **Políticas de acceso por tenant** | Estructura de carpetas: `{bucket}/{tenant_id}/{archivo}`. Políticas que limitan acceso al `tenant_id` del usuario. |
| **URLs firmadas para acceso temporal** | Para archivos privados, generar URLs firmadas con expiración (ej: 1 hora). No exponer URLs públicas permanentes para contenido sensible. |
| **Validar tipo y tamaño** | Validar MIME type y tamaño máximo ANTES de subir. No confiar en el cliente. |

```typescript
// Estructura de carpetas en Storage
// bucket: product-images
//   ├── {tenant_id_1}/
//   │   ├── product_abc.webp
//   │   └── product_def.webp
//   └── {tenant_id_2}/
//       └── product_ghi.webp

// Generar URL firmada (server-side)
const { data } = await supabaseAdmin.storage
  .from('product-images')
  .createSignedUrl(`${tenantId}/${productImagePath}`, 3600)  // 1 hora
```

---

## §5 — Calidad

<!--
  Estándares de calidad de código. Estas reglas aplican a TODO el código
  del monorepo, sin excepción. El CI debe verificar su cumplimiento
  automáticamente.
-->

### Herramientas obligatorias

| Herramienta | Propósito | Regla |
|-------------|----------|-------|
| **ESLint** | Análisis estático | Cero warnings ignorados. Si un warning es un falso positivo, se deshabilita CON comentario justificativo. |
| **Prettier** | Formateo consistente | Configuración compartida en la raíz del monorepo. Sin discusiones de estilo. |
| **TypeScript** | Tipado estricto | `strict: true`. Los errores de tipo bloquean el build. |
| **Zod** | Validación en runtime | En TODA frontera: API endpoints, Server Actions, formularios, parámetros de URL. |

### Reglas de código

```
✅  Validar inputs con Zod en TODA frontera (API, forms, params, env vars)
✅  Errores explícitos con tipos específicos — no try/catch genéricos
✅  Nombres de variables, funciones y archivos en INGLÉS
✅  Documentación y comentarios en ESPAÑOL
✅  Commits pequeños, descriptivos, en tiempo presente imperativo
✅  Tests para lógica de negocio crítica: inventario atómico, cálculos, RLS
✅  Un archivo = una responsabilidad. Si crece > 200 líneas, considerar dividir

❌  try { ... } catch (e) { console.log(e) }  // Swallow error, pierde contexto
❌  // @ts-ignore — prohibido sin excepción documentada
❌  export default function  // Preferir named exports para mejor tree-shaking y refactoring
```

### Manejo de errores

```typescript
// ❌ INCORRECTO — Error genérico, se pierde el contexto
try {
  await processOrder(order)
} catch (e) {
  console.log(e)  // ¿Qué falló? ¿Dónde? ¿Es recuperable?
}

// ✅ CORRECTO — Error tipado, contexto claro, acción definida
import { AppError, ErrorCode } from '@packages/core/errors'

try {
  await processOrder(order)
} catch (error) {
  if (error instanceof AppError) {
    switch (error.code) {
      case ErrorCode.INSUFFICIENT_STOCK:
        // Notificar al usuario, sugerir alternativa
        break
      case ErrorCode.TENANT_NOT_FOUND:
        // Error crítico, loggear y escalar
        break
    }
  }
  // Re-throw errores inesperados para que los capture el error boundary
  throw error
}
```

---

## §6 — Seguridad (Checklist)

<!--
  Checklist de seguridad para verificar ANTES de cada merge/deploy.
  Cada ítem es una pregunta que debe responderse afirmativamente.
  Si alguna respuesta es "no" o "no estoy seguro", el PR se bloquea
  hasta que se resuelva.
-->

### Pre-merge checklist

| # | Verificación | Estado |
|---|-------------|--------|
| 1 | ¿Secretos solo server-side? Ninguna API key privada, `service_role` key, o token de servicio en código cliente. | ☐ |
| 2 | ¿Toda tabla nueva tiene RLS habilitado + políticas explícitas en la misma migración? | ☐ |
| 3 | ¿Inputs validados con Zod en toda frontera (Server Actions, Route Handlers, Edge Functions, formularios)? | ☐ |
| 4 | ¿Uso de `service_role` documentado y limitado al scope mínimo necesario? | ☐ |
| 5 | ¿Tokens y sesión almacenados de forma segura? (`expo-secure-store` en móvil, `httpOnly cookies` en web) | ☐ |
| 6 | ¿Aislamiento multi-tenant verificado? (usuario de tenant A NO puede ver/modificar datos de tenant B) | ☐ |
| 7 | ¿Variables de entorno sensibles excluidas de `NEXT_PUBLIC_*`? | ☐ |
| 8 | ¿No hay `console.log` con datos sensibles (tokens, passwords, PII)? | ☐ |

<!--
  Este checklist debe revisarse manualmente en code review.
  Idealmente, los ítems 1, 2, 3 y 7 se verifican automáticamente en CI.
-->

---

## §7 — Convenciones de Código

<!--
  Convenciones de nomenclatura para mantener consistencia en todo
  el monorepo. Estas no son preferencias personales: son el estándar
  del proyecto y se aplican sin excepción.
-->

### Nomenclatura de archivos y directorios

| Elemento | Convención | Ejemplo |
|----------|-----------|---------|
| Archivos TypeScript (utilidades, servicios) | `kebab-case.ts` | `order-service.ts`, `delivery-calculator.ts` |
| Componentes React/React Native | `PascalCase.tsx` | `OrderList.tsx`, `DeliveryMap.tsx` |
| Archivos de test | `{nombre}.test.ts` / `{nombre}.test.tsx` | `order-service.test.ts`, `OrderList.test.tsx` |
| Directorios | `kebab-case` | `order-management/`, `delivery-tracking/` |
| Migraciones SQL | `YYYYMMDDHHMMSS_descripcion.sql` | `20260530120000_create_product_table.sql` |

### Nomenclatura de código

| Elemento | Convención | Ejemplo |
|----------|-----------|---------|
| Funciones | `camelCase` | `calculateDeliveryETA()`, `validateOrderItems()` |
| Variables | `camelCase` | `orderTotal`, `deliveryZone` |
| Constantes | `UPPER_SNAKE_CASE` | `MAX_DELIVERY_RADIUS_KM`, `DEFAULT_TAX_RATE` |
| Tipos | `PascalCase` con prefijo `T` | `TOrder`, `TDeliveryZone`, `TProductCategory` |
| Interfaces | `PascalCase` con prefijo `I` | `IOrderService`, `IDeliveryProvider` |
| Enums | `PascalCase` (valores en `UPPER_SNAKE_CASE`) | `OrderStatus.PENDING`, `DeliveryMethod.EXPRESS` |
| Componentes React | `PascalCase` | `OrderList`, `DeliveryTracker` |
| Custom Hooks | `camelCase` con prefijo `use` | `useOrders()`, `useDeliveryTracking()` |

### Nomenclatura de base de datos

| Elemento | Convención | Ejemplo |
|----------|-----------|---------|
| Tablas | `snake_case`, singular | `product`, `order_item`, `delivery_zone` |
| Columnas | `snake_case` | `tenant_id`, `created_at`, `unit_price` |
| Índices | `idx_{tabla}_{columnas}` | `idx_product_tenant_id` |
| Foreign keys | `fk_{origen}_{destino}` | `fk_order_item_product` |
| Funciones SQL | `snake_case` | `get_user_tenant_id()`, `calculate_order_total()` |
| Políticas RLS | `{tabla}_{operacion}_{descripcion}` | `product_select_own_tenant` |

---

<!--
  ╔══════════════════════════════════════════════════════════════════════╗
  ║  FIN DEL DOCUMENTO                                                 ║
  ║                                                                    ║
  ║  Última actualización: 2026-05-30                                  ║
  ║  Próxima revisión: cuando cambie el stack o se añada un nuevo      ║
  ║  componente arquitectónico.                                        ║
  ║                                                                    ║
  ║  Para proponer cambios a estas reglas, abrir un issue con el       ║
  ║  label [arquitectura] y justificación detallada.                   ║
  ╚══════════════════════════════════════════════════════════════════════╝
-->

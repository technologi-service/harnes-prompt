# 🏛️ Arquitectura y Reglas Técnicas Estrictas

> **Reglas vinculantes.** El agente NO puede saltarse ninguna regla de este documento.
> Si una tarea exige romper una regla, primero se detiene y lo debate con el humano;
> no se rompe la regla por iniciativa propia. Este documento describe el "cómo" técnico;
> el "qué" y el "estado" viven en `doc/map.md`; el "orden de ejecución" en `doc/sequences.md`.

---

## 0. Principios no negociables

1. **TypeScript estricto.** `strict: true`. Prohibido `any` salvo justificación escrita en el código (`// eslint-disable` con motivo). Preferir `unknown` + narrowing.
2. **Secretos solo en servidor.** Ninguna clave privada (incluida `service_role` de Supabase) puede llegar al cliente (web o móvil). Cliente solo usa claves públicas (`anon`).
3. **RLS siempre activo.** Toda tabla en Supabase tiene Row Level Security habilitado **antes** de exponerse. Sin política = sin acceso.
4. **Una sola fuente de verdad para tipos.** Los tipos de la base de datos se generan desde Supabase; no se escriben a mano modelos que dupliquen el esquema.
5. **No alucinar el estado.** Antes de modificar, se valida contra el repo real (ver `CLAUDE.md` → Flujo de Ejecución).

---

## 1. Estructura del monorepo

```
apps/web        → Next.js (App Router)
apps/mobile     → Expo (Expo Router)
packages/core   → Dominio, tipos y lógica pura (sin React, sin I/O)
packages/ui     → Componentes presentacionales compartidos
packages/supabase → Cliente Supabase, tipos generados y data-access
supabase/       → Migraciones SQL, seeds y config (Supabase CLI)
doc/            → Documentación viva
```

Reglas de dependencia entre paquetes:

- `apps/*` pueden depender de `packages/*`. **Nunca** al revés.
- `packages/core` es **puro**: sin React, sin Next, sin Expo, sin acceso directo a red/disco.
- La lógica de acceso a datos vive en `packages/supabase`, no dispersa en componentes.
- Prohibido importar entre apps (`apps/web` no importa de `apps/mobile` ni viceversa).

---

## 2. Reglas de Next.js (App Router) — `apps/web`

1. **Server Components por defecto.** `'use client'` solo cuando se necesita estado/efectos/eventos del navegador, y lo más abajo posible en el árbol.
2. **Mutaciones vía Server Actions** o Route Handlers (`app/api/.../route.ts`). El cliente nunca habla con servicios privados directamente.
3. **Data fetching en el servidor.** Las lecturas con datos sensibles o que requieran `service_role` ocurren en server components / actions, nunca en el cliente.
4. **Variables de entorno:** solo las prefijadas `NEXT_PUBLIC_` son accesibles en cliente. Todo lo demás es server-only. Validar env con un esquema (p. ej. Zod) al arrancar.
5. **Cacheo explícito.** Decidir y documentar `revalidate` / `cache` por ruta; no dejar el comportamiento de caché al azar.
6. **Estructura de rutas:** colocación por feature dentro de `app/`. Componentes de UI reutilizables a `packages/ui`.
7. **Sin lógica de dominio en componentes.** La lógica de negocio vive en `packages/core`; los componentes orquestan y presentan.

---

## 3. Reglas de Expo (React Native) — `apps/mobile`

1. **Expo Router** (file-based) como sistema de navegación.
2. **Sin secretos en el bundle.** Solo claves públicas. Config sensible vía variables de entorno de build (EAS) y nunca commiteada.
3. **Cliente Supabase móvil** con almacenamiento seguro de sesión (p. ej. `expo-secure-store`) para tokens; nunca `AsyncStorage` en claro para credenciales sensibles.
4. **UI compartida** desde `packages/ui` cuando sea viable; los componentes específicos de plataforma se nombran con sufijo (`.native.tsx` / `.web.tsx`) si conviven.
5. **Lógica de dominio compartida** desde `packages/core` — no duplicar reglas de negocio entre web y móvil.

---

## 4. Reglas de Supabase

### 4.1 Acceso y claves
1. **Cliente (web/móvil):** solo `anon key` + sesión del usuario. Todo el acceso pasa por RLS.
2. **Servidor (Next.js server / Edge Functions):** puede usar `service_role` **solo** en contexto server-side y para operaciones administrativas justificadas. Cada uso de `service_role` se documenta.
3. **Un único cliente por entorno** centralizado en `packages/supabase` (uno para browser, uno para server). No instanciar clientes ad-hoc por toda la app.

### 4.2 Esquema y migraciones
4. **Todo cambio de esquema es una migración** versionada vía Supabase CLI (`supabase/migrations`). Prohibido tocar el esquema a mano en el dashboard sin reflejarlo en una migración.
5. **Tipos generados:** tras cada migración se regeneran los tipos (`supabase gen types typescript`) y se commitean en `packages/supabase`.
6. **Nomenclatura SQL:** tablas en `snake_case` plural, columnas en `snake_case`, claves foráneas `<tabla>_id`, timestamps `created_at` / `updated_at`.

### 4.3 RLS (Row Level Security) — crítico
7. **RLS habilitado en TODA tabla** que contenga o se relacione con datos de usuario, en la misma migración que crea la tabla.
8. **Políticas explícitas por operación** (`select`, `insert`, `update`, `delete`). No usar políticas `using (true)` salvo datos verdaderamente públicos y documentados.
9. **Aislamiento por usuario/tenant** con `auth.uid()` (o columna de tenant) en la cláusula `using` / `with check`.
10. **`with check` en inserts/updates** para impedir que un usuario escriba filas que no le pertenecen.
11. **Probar las políticas:** toda tabla con RLS lleva al menos un caso de prueba/seed que verifique que un usuario no accede a datos de otro.

### 4.4 Edge Functions
12. Las Edge Functions validan input (esquema), nunca confían en datos del cliente, y mantienen los secretos en el entorno de la función.

---

## 5. Calidad de código y convenciones

1. **Lint + format** obligatorios (ESLint + Prettier). El código se commitea formateado.
2. **Validación de datos externos con Zod** (o equivalente) en los límites: formularios, respuestas de API, payloads de funciones.
3. **Manejo de errores explícito.** No tragar errores en silencio; propagar o registrar con contexto.
4. **Nombres en inglés en el código** (variables, funciones, tablas); documentación en español.
5. **Tests** para lógica de dominio en `packages/core` y para políticas RLS críticas.
6. **Commits pequeños y descriptivos**, una intención por commit.

---

## 6. Seguridad (resumen accionable)

- [ ] ¿Algún secreto podría llegar al cliente? → Debe ser **no**.
- [ ] ¿La tabla nueva tiene RLS + políticas por operación? → Debe ser **sí**.
- [ ] ¿Las entradas externas están validadas? → Debe ser **sí**.
- [ ] ¿`service_role` se usa solo en server y está documentado? → Debe ser **sí**.

> Esta checklist se aplica en cada PR/tarea que toque datos o autenticación.

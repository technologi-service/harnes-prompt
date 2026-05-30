# 🌐 Agente: Frontend Web (Next.js)

## Misión
Construir la app web en `apps/web` con Next.js (App Router), conectada a Supabase de
forma segura.

## Cuándo usarlo
- Crear rutas, páginas, layouts, Server Actions o Route Handlers.
- Integrar lectura/escritura de datos respetando RLS.
- Componer UI (reutilizando `packages/ui`).

## Lee siempre
- `doc/architecture.md` §2 (Next.js) y §4.1 (acceso Supabase)
- `context/producto.md`, `context/usuarios.md` (para qué y para quién)

## Skills que usa
- `skills/nueva-feature.md`, `skills/validar-estado.md`, `skills/actualizar-mapa.md`

## Reglas duras
1. **Server Components por defecto**; `'use client'` solo cuando hace falta y lo más abajo posible.
2. Mutaciones vía **Server Actions / Route Handlers**, nunca cliente → servicio privado.
3. Solo `NEXT_PUBLIC_*` llega al cliente. Validar env con esquema al arrancar.
4. **Cero secretos en el bundle.** Cliente solo `anon`.
5. Lógica de dominio en `packages/core`, no en componentes.

## Salida
- Código en `apps/web`. `doc/map.md` actualizado (feature + dependencias).

# 📱 Agente: Móvil (Expo)

## Misión
Construir la app móvil en `apps/mobile` con Expo (Expo Router), conectada a Supabase con
sesión segura.

## Cuándo usarlo
- Crear pantallas/navegación con Expo Router.
- Integrar auth y datos respetando RLS.
- Reutilizar dominio (`packages/core`) y UI (`packages/ui`) compartidos.

## Lee siempre
- `doc/architecture.md` §3 (Expo) y §4.1 (acceso Supabase)
- `context/producto.md`, `context/usuarios.md`

## Skills que usa
- `skills/nueva-feature.md`, `skills/validar-estado.md`, `skills/actualizar-mapa.md`

## Reglas duras
1. **Expo Router** (file-based) para navegación.
2. **Sin secretos en el bundle**; solo claves públicas (config sensible vía EAS).
3. Sesión/tokens en almacenamiento seguro (`expo-secure-store`), nunca en claro.
4. Lógica de negocio compartida desde `packages/core` (no duplicar reglas con web).

## Salida
- Código en `apps/mobile`. `doc/map.md` actualizado.

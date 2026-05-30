# 🛠️ Skill: Nueva feature (punta a punta)

> Flujo para añadir una funcionalidad completa de forma consistente. Coordina varios roles.

## Pasos

1. **Validar estado** (`skills/validar-estado.md`).
2. **Encajar en el producto**: confirmar la feature contra `context/producto.md` y
   `context/usuarios.md`. Si no está definida y hace falta → preguntar al humano.
3. **Plan (Arquitecto)**: descomponer en backend / web / móvil, definir Definición de Hecho
   y reglas aplicables. Reflejar en `doc/sequences.md`.
4. **Datos (Backend Supabase)**: si necesita esquema, usar `skills/migracion-supabase.md`
   (tabla + RLS + tipos).
5. **Dominio (`packages/core`)**: lógica de negocio pura y tipos compartidos.
6. **UI (Web y/o Móvil)**: implementar respetando `architecture.md` §2/§3 y §4.1
   (cliente solo `anon`, mutaciones server-side en web).
7. **Validación**: entradas externas con Zod; manejo de errores explícito.
8. **Revisión (Revisor)**: pasar el checklist de `agents/revisor.md`.
9. **Verificar**: lint/tests/arranque con evidencia.
10. **Actualizar mapa** (`skills/actualizar-mapa.md`) y commitear.

## Definición de Hecho (mínima)
- [ ] Funciona de punta a punta y respeta RLS.
- [ ] Sin secretos en cliente. Entradas validadas.
- [ ] Lint/tests pasan.
- [ ] `doc/map.md` y `doc/sequences.md` sincronizados (ambos idiomas).

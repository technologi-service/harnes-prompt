# 🛠️ Skill: Actualizar mapa (Metodología Viva)

> Se ejecuta **al terminar** cada tarea, antes de declararla hecha. Mantiene el contexto
> sincronizado y real sin intervención manual. Una tarea no está "hecha" sin esto.

## Pasos

1. **`doc/map.md`**:
   - Mover features entre Pendiente → En progreso → Implementada (§3).
   - Actualizar la tabla de estado actual (§2).
   - Actualizar dependencias (§4) si se añadió/quitó alguna.
   - Actualizar el modelo de datos (§5) si cambió el esquema.
   - Actualizar "Última actualización" + "Actualizado por" y añadir línea a la bitácora (§7).
2. **`doc/sequences.md`**:
   - Marcar el sprint/paso (`✅`/`🟡`/`⛔`) y, si surgieron, añadir los siguientes.
3. **`context/`**: si cambió algo estable (stack, producto, usuarios), actualizar el archivo
   correspondiente y anotarlo en la bitácora de `map.md`.
4. **Sincronización bilingüe**: replicar los cambios de `doc/*.md` en su versión `.en.md`.
5. **Verificar coherencia**: que `map.md`, `sequences.md` y el repo real cuenten lo mismo.

## Resultado esperado
- Documentación que refleja exactamente el estado tras la tarea.
- Commit descriptivo con el cambio (push solo si el humano lo pide).

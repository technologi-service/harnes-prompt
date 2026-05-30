# 🛠️ Skill: Validar estado (anti-alucinación)

> Se ejecuta **al empezar** casi cualquier tarea, antes de tocar código. Objetivo:
> trabajar sobre la realidad del repo, no sobre suposiciones.

## Pasos

1. **Leer los rectores**: `doc/map.md`, `doc/sequences.md` y el `context/` relevante.
2. **Listar el repo real**: ver qué carpetas/archivos existen de verdad (no asumir).
3. **Contrastar con `map.md`**:
   - Para cada cosa que el mapa afirma como "hecha", verificar que existe en el repo.
   - Para cada dependencia listada, verificar que está instalada/declarada.
4. **Si hay discrepancia** entre `map.md` y la realidad:
   - El **código manda**. Corregir `map.md` AHORA (en esta misma tarea) antes de seguir.
5. **Identificar el sprint/paso** actual en `doc/sequences.md` (el primero no completado).
6. **Confirmar reglas** aplicables de `doc/architecture.md` para ese paso.

## Resultado esperado
- Una foto fiable de "dónde estamos" + el paso a ejecutar + las reglas que aplican.
- Si algo del producto/usuarios no está definido y hace falta → **detenerse y preguntar**.

## Regla de oro
Si no se verificó en el repo, **no se afirma**. Nada de "asumo que ya existe".

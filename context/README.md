# 📂 context/ — Contexto del proyecto (en piezas pequeñas)

> Aquí vive el **conocimiento estable** del proyecto, partido en archivos pequeños y
> enfocados. La idea es que el agente cargue **solo el archivo que necesita** para la
> tarea, en vez de leer un documento gigante. Así tiene más contexto **sin abrumarse**.

## Archivos

| Archivo | Contiene | Cuándo cargarlo |
|---------|----------|-----------------|
| `producto.md` | Qué es la app, visión, alcance, funcionalidades núcleo | Tareas de producto/decisiones de qué construir |
| `usuarios.md` | Perfiles de usuario, necesidades, casos de uso | Diseño de features y UX |
| `stack.md` | Decisiones de stack y herramientas (resumen operativo) | Antes de tocar código o instalar dependencias |

## Reglas

- Cada archivo es **corto y de un solo tema**. Si crece demasiado, se parte en dos.
- El contexto **estable** vive aquí; el contexto **cambiante** (estado, features hechas)
  vive en `doc/map.md`. No duplicar: aquí el "qué somos", en `map.md` el "cómo vamos".
- Si un archivo de `context/` cambia, se anota en la bitácora de `doc/map.md`.

# harnes-prompt

Proyecto **Next.js + Expo + Supabase** gobernado por una metodología de documentación
viva para agentes de IA.

## Documentación rectora

- [`doc/map.md`](doc/map.md) — Mapa dinámico: estado actual, dependencias, features y modelo de datos.
- [`doc/architecture.md`](doc/architecture.md) — Reglas técnicas estrictas (Next.js, Expo, Supabase, RLS).
- [`doc/sequences.md`](doc/sequences.md) — Secuenciador de prompts paso a paso.
- [`CLAUDE.md`](CLAUDE.md) — Contrato del agente: metodología viva y flujo de ejecución.

## Cómo trabaja el agente

1. Lee los documentos rectores.
2. Valida el estado real del repo (anti-alucinación) antes de tocar código.
3. Ejecuta el siguiente paso del secuenciador.
4. Actualiza `doc/map.md` y `doc/sequences.md` al terminar (sincronización automática).

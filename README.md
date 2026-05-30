# harnes-prompt

Proyecto **Next.js + Expo + Supabase** gobernado por una metodología de documentación
viva para agentes de IA. Documentación **bilingüe (español + inglés)**.

A **Next.js + Expo + Supabase** project governed by a living-documentation methodology
for AI agents. **Bilingual (Spanish + English)** documentation.

## Estructura · Structure

```
CLAUDE.md          # Contrato del agente (canónico) + CLAUDE.en.md
context/           # Contexto estable en piezas pequeñas (producto, usuarios, stack)
agents/            # Roles del agente (arquitecto, backend, web, móvil, revisor)
skills/            # Procedimientos reutilizables (validar-estado, migracion, ...)
outputs/           # Resultados generados por el agente
doc/               # Documentación viva bilingüe (map, architecture, sequences)
apps/ packages/ supabase/   # Código de la app (se crea desde el Sprint 2)
```

## Documentación rectora · Governing docs

| 🇪🇸 Español | 🇬🇧 English | Qué es · What |
|-----------|-----------|---------------|
| [`doc/map.md`](doc/map.md) | [`doc/map.en.md`](doc/map.en.md) | Mapa dinámico: estado, dependencias, features, modelo de datos · Dynamic map |
| [`doc/architecture.md`](doc/architecture.md) | [`doc/architecture.en.md`](doc/architecture.en.md) | Reglas técnicas estrictas · Strict technical rules |
| [`doc/sequences.md`](doc/sequences.md) | [`doc/sequences.en.md`](doc/sequences.en.md) | Secuenciador de prompts · Prompt sequencer |
| [`CLAUDE.md`](CLAUDE.md) | [`CLAUDE.en.md`](CLAUDE.en.md) | Contrato del agente · Agent contract |

> El `CLAUDE.md` raíz (español) es el canónico que Claude Code carga automáticamente.
> The root `CLAUDE.md` (Spanish) is the canonical one auto-loaded by Claude Code.

## Cómo trabaja el agente · How the agent works

1. Lee los documentos rectores · Read the governing documents.
2. Valida el estado real del repo (anti-alucinación) antes de tocar código · Validate the real repo state before touching code.
3. Ejecuta el siguiente **sprint** del secuenciador (prompt-a-prompt, estilo scrum) · Execute the next sequencer **sprint** (prompt-by-prompt, scrum-style).
4. Actualiza `map.md` y `sequences.md` (ambos idiomas) al terminar · Update the docs (both languages) when done.

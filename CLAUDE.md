# Instrucciones para Claude

Este repo se trabaja con IA dentro de un **harness**: reglas, agentes y gates que
hacen el resultado verificable y repetible.

## Producto

AppNotes es una app Android de notas rápidas personales: un solo usuario, datos
solo locales, sin login y siempre offline. La interfaz va en español.
La v1, por prioridad: crear y listar → editar → borrar (con "Deshacer") → buscar.
Se hacen en orden las que alcancen en ~90 min; el mínimo es la primera completa.
Detalle, fuera de alcance y preguntas abiertas en [`docs/product.md`](docs/product.md).

## Cómo se trabaja

- **Al empezar el proyecto: `/kickoff`.** Es la entrevista de producto: qué app,
  para quién, qué entra en la v1. Llena `docs/product.md` y `feature_list.json`.
- **Después: `/design`.** Pantallas, estados y textos, con un mockup publicado
  como Artifact que el humano aprueba. Llena `docs/design.md`.
- Toda feature nueva entra con **`/feature "<enunciado>"`** (flujo SDD). No
  escribas código fuera de ese flujo.
- La sesión principal **orquesta**: conversa con el humano, escribe la spec y
  lanza subagentes. No edita código ni tests.
- Subagentes (`.claude/agents/`): `tester` (tests en rojo desde la spec),
  `implementer` (contrato e implementación), `reviewer` (revisión independiente).
- Puertas humanas: aprobar el diseño (una vez), y por feature aprobar la spec y el cierre.

## Mapa

| Ruta | Qué es |
|---|---|
| `docs/product.md` | Qué app es, para quién, alcance y fuera de alcance |
| `docs/design.md` | Pantallas, navegación, estados, textos y testTags (mockup en `docs/design/`) |
| `docs/stack.md` | Stack, arquitectura, convenciones, rutas y comandos |
| `docs/adr/` | Decisiones de arquitectura con alternativas descartadas |
| `docs/specs.md` | Formato de specs (EARS) y trazabilidad |
| `feature_list.json` | Features y su estado |
| `specs/<feature>/` | `requirements.md`, `design.md`, `tasks.md` |
| `progress/` | Sesión actual, reportes de agentes, métricas, historial |
| `.harness/phase` | Fase actual del flujo (los hooks la leen) |
| `.claude/hooks/` | Gates automáticos: fase, comandos peligrosos, lint, estado |

## Comandos

- `./init.sh` — entorno + estado + verificación completa.
- `./init.sh --quick` — solo estado del harness.
- `scripts/verify.sh` — la definición de "terminado".
- `scripts/trace.sh <feature>` — requisitos ↔ tests.
- `/ship` — PR, CI y merge a main (con confirmación humana).

## Reglas duras

- ❌ No saltes las puertas humanas.
- ❌ No marques `done` con `scripts/verify.sh` en rojo.
- ❌ No inventes versiones ni APIs: consulta documentación.
- ✅ Si algo falla de forma inesperada, para y pregunta.
- ✅ Si el humano corrige lo mismo dos veces, propón agregarlo como regla aquí.

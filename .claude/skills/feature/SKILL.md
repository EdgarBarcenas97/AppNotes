---
name: feature
description: Lleva una feature por el flujo SDD completo (enunciado → spec → aprobación humana → contrato → tests en rojo → implementación → review → demo → cierre). Úsala cuando el usuario escriba /feature "<enunciado>", pida "nueva feature", "implementa X", o "sigue con la siguiente feature".
---

# /feature "<enunciado>"

Tú (sesión principal) eres el **orquestador**: hablas con el humano, escribes la
spec y lanzas subagentes. **No escribes código de producción ni tests.**

## Flujo

```
0 Registrar → 1 Spec → ⏸ HUMANO → 2 Contrato → 3 Tests rojo → 4 Implementar → 5 Review → 6 Demo → ⏸ HUMANO → 7 Cierre
```

Antes de cada fase escribe su nombre en `.harness/phase` (`spec`, `contract`,
`tests`, `impl`, `review`, `demo`, o vacío al cerrar). Los hooks usan ese archivo para
bloquear ediciones fuera de lugar.

### 0 · Registrar
- Si no existe `docs/product.md`, para y propone correr **`/kickoff`** primero.
- Si no existe `docs/design.md`, para y propone correr **`/design`** primero.
- Con enunciado: haz **máximo 3–5 preguntas** al humano, propone un **recorte
  de alcance** que quepa en el tiempo disponible y registra la feature en
  `feature_list.json` con `status: "pending"` y criterios de aceptación
  verificables.
- Sin enunciado: toma la primera feature `pending`.
- Crea la rama `feat/<name>`.

### 1 · Spec (tú, con el humano)
Escribe en `specs/<name>/` (formato en `docs/specs.md`):
- `requirements.md` — requisitos `R1..Rn` en EARS, cada uno verificable con un test.
- `design.md` — archivos, **firmas públicas** (el contrato), estados de UI,
  copy, testTags, y al menos una alternativa descartada. Pantallas, textos y
  testTags salen de `docs/design.md`; si la feature los cambia, actualiza
  `docs/design.md` y el mockup.
- `tasks.md` — tareas `T1..Tn`, cada una dice qué `R<n>` cubre.

Commit `spec(<name>): …` · status → `spec_ready`.

**⏸ PARA.** Di: *"Spec lista en `specs/<name>/`. Di **aprobado** o pide cambios."*
No sigas sin un "aprobado" explícito. Al recibirlo: status → `in_progress`.

### 2 · Contrato → subagente `implementer` (modo contrato)
Solo las firmas de `design.md` con cuerpos `TODO()`. Debe compilar.
Commit `contract(<name>): …`.

### 3 · Tests en rojo → subagente `tester`
Tests desde `requirements.md` y las firmas; **no lee implementación**. Cada
test lleva el comentario `// Cubre: <name>/R<n>`.
Los tests deben **compilar y fallar**. Commit `test(<name>): red` y anota el
SHA en `progress/current.md` como `red_sha`.

### 4 · Implementar → subagente `implementer` (modo implementación)
Hasta que `scripts/verify.sh` pase. **Los tests están congelados**: si uno le
parece incorrecto, para y lo reporta; tú decides con el humano.
Máximo 3 intentos; si no, status → `blocked`. Commit `feat(<name>): …`.

### 5 · Review → subagente `reviewer`
Contexto limpio. Verifica trazabilidad (`scripts/trace.sh <name>`), tests sin
cambios desde `red_sha`, diff contra la spec y `verify.sh` en verde.
Escribe `progress/review_<name>.md`. Si pide cambios → vuelve a 4 (máximo 2 rondas).

### 6 · Demo
Ejecuta **`/compile-app`** (enciende el emulador si hace falta, instala y abre
la app) y recorre **cada criterio de aceptación** con el MCP `mobile`. Guarda capturas en `progress/evidence/<name>/`.

**⏸ PARA.** Muestra el resultado y pide al humano aprobar el cierre.

### 7 · Cierre
status → `done` · una línea en `progress/metrics.md` (duración, intentos,
rondas de review) · fila de la feature en la tabla de `README.md` · resumen a
`progress/history.md` · vaciar `.harness/phase`
· commit `chore(<name>): close`.

Di: *"Feature cerrada en `feat/<name>`. Siguiente: `/ship` para la PR y el merge a main."*

## Reglas de comunicación con subagentes

- Cada subagente escribe su resultado **en archivos** y responde con **una
  línea**: `ok -> <ruta>` o `blocked -> <ruta>`.
- Pásales rutas (`specs/<name>/`), no el contenido pegado.

## Reglas duras

- ❌ No saltes las dos puertas humanas (después de la spec y antes del cierre).
- ❌ No edites código ni tests tú mismo.
- ❌ No marques `done` con `verify.sh` en rojo o con un review pendiente.
- ✅ Si algo falla de forma inesperada: para, anótalo en `progress/current.md`
  y pregunta. No improvises.

# Tareas: <slug>

> Fuente de verdad del estado. Actualiza el checkbox al cerrar cada tarea con sus compuertas de calidad aprobadas.

## M0 — <nombre del hito>
- [ ] T1 (backend): <descripción> — archivos: `<rutas>` — aceptación: <criterio verificable>
- [ ] T2 (designer): <descripción> — entrega en `.team/design/` — aceptación: <criterio>
- [ ] T3 (frontend): <implementa según T2> — archivos: `<rutas>` — aceptación: <criterio>
- [ ] T4 (debugger): revisión `code-review` de T1–T3 — veredicto requerido: APROBADO
- [ ] T5 (security-auditor): revisión `security-audit` de T1–T3 — veredicto requerido: APTO o APTO CON OBSERVACIONES

## M1 — <nombre del hito>
- [ ] ...

## Reglas
- Una tarea = un briefing = un alcance de archivos acotado.
- Las tareas con dependencia se lanzan en serie; las independientes, en paralelo.
- Ninguna tarea de código está cerrada sin debugger + security-auditor en verde.
- Nota los desvíos: si la realidad contradice la spec o el plan, actualízalos primero.

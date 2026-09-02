---
description: "[VERIFY] Comprueba que la implementación cumple proposal.md/design.md/specs/ antes de archivar"
---

Ejecuta el checklist **en orden estricto**. Si cualquier paso falla, muestra el error y detente ahí — no continúes al siguiente paso.

### Paso 0 — Specs activas y tareas completas
Verifica que existen `proposal.md`, `design.md`, `tasks.md` y `specs/<slug>/spec.md` en la raíz, y que **todas** las líneas de `tasks.md` están marcadas `[x]`. Si falta algún fichero o queda alguna tarea sin marcar, lístalas y **para aquí**.

### Paso 1 — Tests en verde
```bash
cd backend && npm test
```
Criterio: todos los tests pasan. Si alguno falla, muestra el error completo y **para aquí**.

### Paso 2 — OpenAPI válido
```bash
cd backend && npx @redocly/cli lint api-spec.yaml
```
Criterio: cero errores (warnings son aceptables). Si hay errores, muéstralos y **para aquí**.

### Paso 3 — Diario de prompts existe
Verifica que `prompts/prompts-iniciales.md` existe y tiene al menos una entrada.
Si no existe, recuerda al humano que ejecute `/log-prompt` y **para aquí**.

### Paso 4 — Rama no es main
```bash
git branch --show-current
```
Criterio: la rama actual NO es `main`. Si es `main`, **para aquí** y pide crear una rama de feature.

### Paso 5 — Compilación TypeScript limpia
```bash
cd backend && npm run build
```
Criterio: cero errores de compilación. Si hay errores, muéstralos y **para aquí**.

---

## Cierre

Si los 6 pasos anteriores pasan, actualiza `Estado: VERIFICADO` en `proposal.md` y comunica que el cambio está listo para `/5-archive`. No archives todavía — esta fase sólo verifica.

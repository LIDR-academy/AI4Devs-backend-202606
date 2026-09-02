---
description: "[PROPOSE] Crea proposal.md, design.md, tasks.md y specs/ en la raíz con el contrato del cambio — pausa hasta aprobación"
argument-hint: "<ticket-id> <slug> <METHOD> <path> — ej: L1DR-24 cambiar-etapa-candidato PUT /candidates/:id/stage"
---

Recibe `<ticket-id> <slug> <METHOD> <path>` (ej: `/2-propose L1DR-24 cambiar-etapa-candidato PUT /candidates/:id/stage`).

## Precondición

La raíz del repo debe estar limpia: si ya existen `proposal.md`, `design.md`, `tasks.md` o `specs/` de otro cambio en curso, para y pide archivarlo primero con `/4-verify` + `/5-archive`.

## Artefactos que genera esta fase (en la raíz del repo, no dentro de `backend/`)

1. **`proposal.md`**
   ```markdown
   # Propuesta: <slug>
   Ticket: <ticket-id>
   Fecha: <YYYY-MM-DD>
   Endpoint: <METHOD> <path>
   Estado: EN PROPUESTA

   ## Problema
   <qué necesidad de negocio resuelve>

   ## Alcance
   - <incluye>

   ## Fuera de alcance
   - <excluye>
   ```

2. **`design.md`** — diseño técnico contract-first (usa el skill `openapi-contract` si existe para las convenciones del proyecto):
   - Modelo Prisma afectado: cadenas de relaciones/FKs, campos nullable.
   - Diff propuesto para `backend/api-spec.yaml`: path, operación, `components/schemas` reutilizables (nunca inline), path params numéricos con `required: true, schema: { type: integer, minimum: 1 }`, `nullable: true` donde aplique, respuestas 200/201 + 400 + 404 (+ 409 sólo si hay conflicto de estado), schema `Error` reutilizado desde `components/schemas/Error`.
   - Manejo de errores: `throw { status, message }` en el service, `catch (error: any)` en el controller.

3. **`specs/<slug>/spec.md`** — criterios de aceptación en lenguaje llano, uno por comportamiento (happy path, límites, errores). Estos criterios se traducirán 1:1 a tests en la fase apply. Ejemplo de formato:
   ```markdown
   # Spec: <slug>

   1. Happy path — <resultado esperado con status 200>
   2. <caso límite> — <resultado>
   3. 404 — <recurso> no existe → { status: 404, message: '...' }
   4. 400 — <input inválido> → { status: 400, message: '...' }
   ```

4. **`tasks.md`** — checklist vacío derivado del diseño:
   ```markdown
   # Tareas: <slug>
   - [ ] Tests RED para <endpoint> (specs/<slug>/spec.md)
   - [ ] Implementación service
   - [ ] Implementación controller
   - [ ] Implementación route
   - [ ] Tests en verde (GREEN)
   - [ ] api-spec.yaml validado (redocly lint)
   ```

## Flujo

1. Lee `backend/api-spec.yaml` (y el mapa de `/1-explore` si está disponible en el contexto) para conocer el estado actual.
2. Genera los 4 artefactos anteriores en la raíz.
3. Actualiza `backend/api-spec.yaml` con el path/operación nuevos siguiendo el diseño de `design.md`. Ningún otro fichero de `backend/` se toca en esta fase — es sólo contrato, no implementación.
4. Valida el YAML:
   ```bash
   cd backend && npx @redocly/cli lint api-spec.yaml
   ```
5. Muestra el diff completo de `backend/api-spec.yaml` y el contenido de `proposal.md`, `design.md`, `specs/<slug>/spec.md` y `tasks.md`.
6. **PAUSA** — el humano debe escribir "APROBADO" para continuar. Al aprobar, cambia `Estado: APROBADO` en `proposal.md`. No se implementa código de negocio hasta esa confirmación.

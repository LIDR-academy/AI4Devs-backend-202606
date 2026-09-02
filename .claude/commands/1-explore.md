---
description: "[EXPLORE] Mapea el problema y entiende el codebase antes de proponer cualquier código"
argument-hint: "[endpoint-a-explorar — ej: GET /positions/:id/candidates]"
---

Lee el estado actual del proyecto antes de proponer cualquier código. En esta fase **no se crea ningún fichero** — ni de código ni de spec. Sólo construyes y devuelves el mapa.

## Precondición

Comprueba que la raíz del repo está limpia de specs activas: no deben existir ya `proposal.md`, `design.md`, `tasks.md` ni `specs/` de un cambio en curso. Si existen, avisa de que hay un cambio sin archivar y sugiere `/4-verify` + `/5-archive` antes de explorar uno nuevo.

## Pasos

1. **Schema Prisma** — lee `backend/prisma/schema.prisma` completo. Identifica los modelos relevantes y las cadenas de relaciones (FKs, includes necesarios).

2. **Patrón de endpoint existente** — lee estos cuatro ficheros en orden:
   - `backend/src/routes/candidateRoutes.ts`
   - `backend/src/presentation/controllers/candidateController.ts`
   - `backend/src/application/services/candidateService.ts`
   - `backend/src/domain/models/Candidate.ts`

   Identifica: cómo se inyecta `req.prisma`, cómo se estructuran los errores, cómo se tipan los handlers de Express.

3. **Configuración de tests** — lee `backend/jest.config.js` y los tests en `backend/tests/`. Identifica: qué mock de Prisma se usa, el naming convention.

4. **Estado de la spec** — lee `backend/api-spec.yaml`. Identifica qué paths existen, qué schemas hay en `components`.

## Output

Devuelve **únicamente** el mapa estructurado. No propones código ni ficheros.

```
MAPA DEL PROYECTO
─────────────────
Modelos relevantes para <endpoint>:
  - <Modelo>: campos relevantes, FK hacia <OtroModelo>
  - Cadena Prisma: A → B → C (via campo X)
  - Campos nullable: <campo>? en <Modelo>

Patrón establecido:
  - Inyección Prisma: req.prisma en middleware global (index.ts)
  - Error service: throw { status: 400|404, message: '...' }
  - Error controller: catch (error: any) → res.status(error.status).json(...)
  - Typing handlers: Request<{ id: string }> en handlers con params

Tests existentes:
  - Mock: jest.mock('@prisma/client', ...) con mockFindUnique/mockFindMany
  - Setup: beforeEach(() => jest.clearAllMocks())
  - Naming: frases en inglés que describen comportamiento

api-spec.yaml actual:
  - Paths existentes: /candidates, /upload
  - Components: (ninguno / los que existan)
  - Hay que añadir: <path objetivo>

Siguiente paso: /2-propose <ticket-id> <slug> <METHOD> <path>
```

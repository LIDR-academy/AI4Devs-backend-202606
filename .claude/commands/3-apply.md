---
description: "[APPLY] Implementa tasks.md mediante TDD (RED→GREEN) siguiendo proposal.md/design.md/specs/"
argument-hint: "[ticket-id] — si se omite, usa el único cambio activo en la raíz"
---

## Precondición

Debe existir en la raíz `proposal.md` con `Estado: APROBADO` (o posterior), junto a `design.md`, `tasks.md` y `specs/<slug>/spec.md`. Si falta alguno, o `proposal.md` no está aprobado, detente y pide ejecutar `/2-propose` primero (o esperar la aprobación humana).

## Fase RED — sólo tests

Traduce **cada** criterio de `specs/<slug>/spec.md` a un test, en el mismo orden y 1:1.

### Convenciones
- Fichero: `backend/tests/<dominio>Service.test.ts`
- Mock de Prisma siguiendo el patrón de `backend/tests/positionService.test.ts`:
  ```typescript
  const mockFindUnique = jest.fn();
  jest.mock('@prisma/client', () => ({
    PrismaClient: jest.fn().mockImplementation(() => ({
      position: { findUnique: mockFindUnique },
    })),
  }));
  ```
- `beforeEach(() => jest.clearAllMocks())`
- Nombres descriptivos en inglés que describen comportamiento, ej: `'returns averageScore as null (not 0) when no interviews have a score'`

### Flujo RED
1. Lee `specs/<slug>/spec.md` y `design.md` para el contrato exacto.
2. Escribe los tests en el fichero correspondiente. No escribas código de implementación todavía.
3. Ejecuta:
   ```bash
   cd backend && npm test -- --testPathPattern="<fichero>"
   ```
4. Confirma en el output que **todos los tests fallan** (RED confirmado).
5. Marca en `tasks.md`: `- [x] Tests RED para <endpoint> (specs/<slug>/spec.md)`.
6. **PAUSA OBLIGATORIA**:
   ```
   ✋ RED confirmado: X tests fallan. Escribe "GREEN" para comenzar la implementación.
   ```

## Fase GREEN — implementación

Tras recibir "GREEN": implementa en `backend/` (service → controller → route, en ese orden) el código mínimo necesario para que los tests pasen, siguiendo exactamente lo acordado en `design.md`. Ve marcando cada tarea de `tasks.md` como `[x]` a medida que se completa.

No toques `backend/api-spec.yaml` en esta fase salvo que la implementación revele que el contrato aprobado en `/2-propose` necesita un ajuste — en ese caso, **para y avisa** en vez de cambiarlo en silencio (el contrato ya fue aprobado por el humano).

Ejecuta el test suite completo y confirma verde:
```bash
cd backend && npm test -- --testPathPattern="<fichero>"
```
Marca `- [x] Tests en verde (GREEN)` en `tasks.md`.

Cuando todas las tareas de `tasks.md` estén `[x]`, indica que el cambio está listo para `/4-verify`.

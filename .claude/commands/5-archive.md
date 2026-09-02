---
description: "[ARCHIVE] Mueve proposal.md/design.md/tasks.md/specs/ a .openspec/archive/ y genera la descripción de PR"
argument-hint: "[ticket-id] — si se omite, usa el único cambio activo en la raíz"
---

## Precondición

`proposal.md` en la raíz debe tener `Estado: VERIFICADO`. Si no, para y pide ejecutar `/4-verify` primero.

## Flujo

1. Lee `Ticket` y el slug de `proposal.md`.
2. Crea la carpeta `.openspec/archive/<YYYY-MM-DD>_<ticket-id>_<slug>/` (fecha de hoy).
3. Cambia `Estado: ARCHIVADO` en `proposal.md`.
4. Mueve desde la raíz a esa carpeta: `proposal.md`, `design.md`, `tasks.md` y la carpeta completa `specs/<slug>/`.
5. Confirma que la raíz queda limpia — ya no debe haber `proposal.md`, `design.md`, `tasks.md` ni `specs/`. `backend/` no se toca: el código y `api-spec.yaml` quedan en producción de forma permanente, igual que los tests en `backend/tests/`.

## Estructura final esperada

```
.openspec/
└── archive/
    └── <YYYY-MM-DD>_<ticket-id>_<slug>/
        ├── proposal.md
        ├── design.md
        ├── tasks.md
        └── specs/
```

## Genera la descripción de PR

Sólo si el paso de archivado anterior se completó, genera la descripción del PR para que el humano la copie:

```markdown
## Resumen
- <qué endpoint se implementó>
- <lógica de negocio clave — ej: averageScore null cuando no hay scores>
- <tests añadidos>

## Cómo probar
1. `docker-compose up -d` — levanta PostgreSQL
2. `cd backend && npm run dev` — servidor en :3010
3. Solicitud de ejemplo:
   ```
   GET http://localhost:3010/positions/1/candidates
   ```
4. Casos de error:
   - `GET /positions/999/candidates` → 404
   - `GET /positions/abc/candidates` → 400

## Decisiones técnicas
- <decisión 1 y su justificación — ver design.md archivado>
- <decisión 2 y su justificación>

## Tests
```bash
cd backend && npm test
```
Resultado: X tests pasan, 0 fallan.
```

No hace push. No crea el PR. Sólo mueve ficheros y muestra la descripción. Recuerda ejecutar `/log-prompt` si aún no se ha registrado el prompt de este cambio.

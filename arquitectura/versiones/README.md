# Evolución de la arquitectura

Esta carpeta registra cómo cambia la arquitectura de Ferre & Inversiones MIGAL cada vez que se incorpora un sistema empresarial a la solución. Cada incorporación se documenta en un archivo propio con la [plantilla de versión](plantilla-version.md).

## Índice

| ID | Sistema incorporado | Ajustes (CA) | Archivo |
|---|---|---|---|
| VER-01 | ERP (base) | — | [VER-01-erp-base.md](VER-01-erp-base.md) |
| VER-02 | CRM | CA-01 a CA-10 y CA-18 | [VER-02-crm.md](VER-02-crm.md) |

## Reglas de uso

1. Un archivo por sistema incorporado, nombrado `VER-XX-<sistema>.md`, y una fila nueva en el índice.
2. Los ajustes a sistemas existentes se numeran CA-xx de forma continua entre archivos; no se reinicia la numeración.
3. El texto describe la integración de sistemas, no las evaluaciones del curso. El estado del repositorio en cada evaluación se conserva con un tag de Git.
4. Toda mención a otro documento del repositorio se enlaza con una ruta relativa.
5. Que un cambio esté registrado no significa que esté configurado en Odoo; el estado de cada ajuste lo indica.

El [changelog](../../CHANGELOG.md) resume las incorporaciones; estos registros explican su efecto en la arquitectura.

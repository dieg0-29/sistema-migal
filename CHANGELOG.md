# Changelog

Este archivo resume los **cambios relevantes del proyecto acumulativo**. No reemplaza el historial de Git ni registra cada commit: documenta cambios que afectan la estructura del repositorio, el alcance, las decisiones, los procesos, los datos, las pruebas o la evidencia del proyecto.


## 2026-10-23 — Dependencias obligatorias de los módulos de Odoo

### Añadido

- `arquitectura/base/modulos-y-dependencias.md`: inventario de módulos, dependencias obligatorias y módulos puente según los manifiestos de Odoo 18.0, con hallazgos H-01 a H-08.
- Vista de dependencias entre módulos en `arquitectura/vistas/dependencias/` (fuente y render).
- CA-18 en VER-02 y riesgos RSK-10 y RSK-11.

### Cambiado

- Facturación (`account`) pasa a formar parte del ERP base como dependencia obligatoria de Ventas y Punto de Venta; VER-01 registra la corrección.
- Contactos se representa como maestro transversal y dependencia obligatoria del CRM (CA-10).
- Vistas C4 de contexto, contenedores y componentes regeneradas con los módulos `sale_management`, `account`, `stock_account`, `sale_stock`, `sale_crm` y `pos_sale`.
- Alcance y flujo O2C alineados con el inventario de módulos.

## 2026-10-17 — Reorganización y alineación de la documentación

### Añadido

- Plantillas de integración, versión, entidad de datos, proceso y caso de prueba.
- INT-01 (CRM y ERP), registro de versiones VER-01 y VER-02, catálogo único de reglas (RN-01 a RN-25), registro de riesgos (RSK-nn) y espacios de códigos en `CONTRIBUTING.md`.
- Casos de prueba CP-01 y CP-02 en `pruebas/casos/`.

### Cambiado

- Flujos O2C, inventario y P2P alineados con KeyFacil como emisor, la exclusión de Compras, el descuento automático del POS y los perfiles del modelo de autorización.
- Vistas C4 de ventas e inventario, backlog y contexto de la empresa alineados con las mismas decisiones.
- `datos/` estandarizado: diccionario como fuente única, caso de ejemplo unificado en el catálogo sintético y responsables por perfil.
- Se retiran las referencias a evaluaciones en la documentación viva y los códigos D01 a D12.

### Eliminado

- Documentos fusionados o redundantes: escenario CRM, análisis de situación, frontera conceptual, casos en `comercial-crm/`, reglas O2C/P2P, eventos O2C, adecuación arquitectónica y registros de evolución anteriores.

## 2026-10-16 — Incorporación de los artefactos D01 y D02 del escenario comercial CRM (PC2)

### Añadido

- D01: flujo comercial desde captación hasta cotización (`ETP-01` a `ETP-03`) y reglas de salto en `procesos/comercial-crm/escenario-crm.md`.
- Diagrama BPMN del proceso comercial separado en `procesos/comercial-crm/fuentes/` (`.bpm`) y `procesos/comercial-crm/render/` (`.png`).
- D02: catálogo de reglas de negocio (`RN-01` a `RN-06`), excepciones (`EXC-01`, `EXC-02`) y matriz de trazabilidad integrados en `procesos/comercial-crm/decisiones-comerciales.md` junto con su libro Excel `procesos/comercial-crm/d02-reglas-negocio.xlsx`.

## 2026-10-16 — Incorporación de los artefactos D09 y D10 de PC2

### Añadido

- D09: modelo de autorización y segregación de funciones en Markdown y matriz de permisos Excel.
- D10: plan de trazabilidad y auditoría en Markdown y matriz Excel conservada en su formato original.
- Índices de seguridad y trazabilidad, enlaces en los README de arquitectura, datos y repositorio, y esta entrada de changelog.

### Observaciones

- Los documentos se recibieron como los adjuntos oficiales más recientes y pueden estar sujetos a cambios.
- Los casos de auditoría consignados en D10 permanecen por ejecutar.

## 2026-10-16 — Reorganización y preparación hasta el Examen Parcial

### Añadido

- `CHANGELOG.md` para registrar la evolución relevante del proyecto.
- `CONTRIBUTING.md` con convenciones de ramas, commits, Pull Requests, revisión y validación.
- Estructura objetivo del repositorio hasta el Examen Parcial.
- Organización prevista para O2C, P2P, inventario, gestión comercial/CRM, datos, pruebas y entregas.

### Cambiado

- El `README.md` pasa a funcionar como portada e índice del proyecto, enlazando a la documentación detallada de cada área en lugar de duplicarla.
- Se normaliza la estructura del repositorio en los bloques `arquitectura/`, `procesos/`, `datos/`, `producto/`, `pruebas/`, `fuentes/` y `entregas/`.
- Se eliminan estructuras redundantes del tipo `archivo.md/archivo.md`.
- `contexto-empresa.md` pasa a `arquitectura/base/contexto-empresa.md`.
- `comparacion-erp.md` pasa a `arquitectura/base/comparacion-erp.md`.
- Los ADR se normalizan bajo `arquitectura/decisiones/` con nombres `ADR-00X-*`.
- Las vistas C4 se reorganizan en `arquitectura/vistas/c4/`, separando archivos editables en `fuentes/` y resultados generados en `render/`.
- `flujo-principal-ventas.md` pasa a `procesos/o2c/flujo.md`.
- `flujo-principal-inventarios.md` pasa a `procesos/inventario/flujo.md`.
- `backlog.md` pasa a `producto/backlog.md`.
- `declaracion-ia.md` pasa a `fuentes/uso-ia.md`.

### Pendiente después de la reorganización

- Actualizar enlaces relativos y referencias internas afectadas por el movimiento de archivos.
- Consolidar el mapa de procesos de nivel 1.
- Completar los artefactos O2C y P2P correspondientes al avance de macroprocesos.
- Incorporar el incremento comercial/CRM de PC2.
- Completar los elementos de Semana 7 y la integración parcial para el EP.
- Revisar el contenido técnico de los documentos migrados; mover o renombrar un archivo no implica que su contenido haya sido validado o corregido.

## Criterio de uso

Un cambio debe aparecer aquí cuando modifica de manera relevante el estado del proyecto. Los ajustes menores de redacción, formato o correcciones triviales pueden quedar únicamente en el historial de commits.

# VER-02: Integración del CRM

## 1. Metadatos

* **Fecha:** 17/10/2026
* **Versión:** 0.1
* **Autores:** César Joaquín
* **Sistema incorporado:** CRM (Odoo 18 Community, misma instancia)
* **Sistemas existentes afectados:** ERP

## 2. Propósito

Recuperar el contexto comercial disperso en conversaciones, notas y memoria de los propietarios, e integrarlo al ERP existente mediante la frontera definida en [INT-01](../integraciones/INT-01-CRM-ERP.md).


## 3. Elementos que se mantienen

| Elemento | Observación |
|---|---|
| Odoo 18 Community | El CRM se incorpora en la misma instancia; no se reabre la elección de plataforma |
| KeyFacil como sistema externo | No se integra técnicamente con el CRM ni con el ERP |
| WhatsApp sin automatizar | Pasa a registrarse como canal en el CRM |
| Exclusión del módulo de Compras | Se mantiene |
| Supuesto de adopción por los propietarios | Se extiende al registro de consultas y cotizaciones |

## 4. Elementos que se agregan

| Elemento | Descripción | Respaldo |
|---|---|---|
| Módulo CRM | Leads, oportunidades, etapas, probabilidad, actividades y motivos de pérdida | [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md) |
| Marketing / UTM | Campaña, fuente y medio de cada lead | [Cuenta, contacto y canales](../../procesos/comercial-crm/cuenta-contacto-canales.md) |
| Módulo Contactos | Dependencia obligatoria del módulo `crm`; maestro de cuentas y contactos compartido con Ventas, Punto de Venta y Facturación | [Cuenta, contacto y canales](../../procesos/comercial-crm/cuenta-contacto-canales.md), [Módulos y dependencias](../base/modulos-y-dependencias.md) |
| Dependencias técnicas del CRM | `sales_team`, `calendar`, `utm`, `mail` y `phone_validation`, instaladas con `crm`; el puente `sale_crm` se instala automáticamente al coexistir con Ventas | [Módulos y dependencias](../base/modulos-y-dependencias.md) |
| Reglas y decisiones comerciales | Responsables y evidencia para descuento, disponibilidad y comprobante previsto | [Decisiones comerciales](../../procesos/comercial-crm/decisiones-comerciales.md) |
| Frontera CRM a ERP | La cotización aceptada pasa al ERP como pedido | [INT-01](../integraciones/INT-01-CRM-ERP.md), [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md) |

## 5. Ajustes en el ERP para la integración

| ID | Componente ERP | Antes | Después | Motivo de la integración | Documentos afectados | Estado |
|---|---|---|---|---|---|---|
| CA-01 | Vistas de arquitectura | Centradas en ventas e inventario | Incluyen el dominio CRM y su relación con Ventas | Representar la nueva frontera | [Vistas C4](../vistas/consolidado-diagramas.md) | Documentado |
| CA-02 | Ventas | Cotización creada directamente en Ventas | Cotización creada desde la oportunidad (`sale_crm`); el pedido confirmado inicia la ejecución en el ERP | Conservar el origen comercial del pedido | [INT-01](../integraciones/INT-01-CRM-ERP.md), [O2C](../../procesos/o2c/flujo.md) | Pendiente de prueba |
| CA-03 | Registros internos y KeyFacil | Documentos internos y comprobantes fiscales sin distinguir | KeyFacil conserva la emisión; Odoo mantiene el registro operativo y la referencia | Evitar duplicar la emisión fiscal | [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [Facturación](../../procesos/o2c/facturacion.md) | Frontera documentada; flujo O2C por corregir |
| CA-04 | Abastecimiento | Solicitud de presupuesto automática en Compras ante falta de stock | No se presupone la solicitud automática; el abastecimiento se modela como proceso independiente del módulo | Coherencia con la exclusión de Compras | [O2C](../../procesos/o2c/flujo.md), [P2P](../../procesos/p2p/flujo.md), [Inventario](../../procesos/inventario/flujo.md) | Decisión pendiente |
| CA-05 | Precios | Precio modificable sin contexto | Se conserva versión, precio de referencia, precio ofrecido, motivo y autorizador | Trazabilidad de excepciones de precio originadas en el CRM | [Decisiones comerciales](../../procesos/comercial-crm/decisiones-comerciales.md) | Pendiente de prueba |
| CA-06 | Punto de Venta | Descuento de stock inmediato y al cierre descritos a la vez | El sistema descuenta automáticamente el stock de la Tienda en el momento de cada venta | Disponibilidad confiable para las promesas comerciales | [O2C](../../procesos/o2c/flujo.md), [Inventario](../../procesos/inventario/flujo.md) | Decidido; pendiente de prueba en la instancia |
| CA-07 | Contabilidad | Contabilidad de terceros descrita como requisito universal | Precisar brecha entre módulo estándar y complemento | Delimitar qué registra el ERP frente a KeyFacil | [ADR-003](../decisiones/ADR-003-contabilidad.md) | Corrección pendiente del ADR |
| CA-08 | Datos de prueba | Sin guía común | Distinguir catálogos sintéticos, IDs de caso e IDs de sistema | Probar el recorrido CRM a ERP de forma reproducible | [Datos de prueba](../../datos/pruebas/README.md) | Guía incorporada; ejecución pendiente |
| CA-09 | Módulo CRM | Forma de trabajo no formalizada | Módulo nativo en la misma instancia del ERP | Centralizar el embudo comercial sin conectores externos | [INT-01](../integraciones/INT-01-CRM-ERP.md), [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md) | En configuración |
| CA-10 | Módulo de Contactos | Aspecto no formalizado | Dependencia obligatoria de `crm`; maestro transversal usado por CRM, Ventas, Punto de Venta y Facturación | Unificar clientes y contactos sin duplicar datos | [Cuenta, contacto y canales](../../procesos/comercial-crm/cuenta-contacto-canales.md), [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [Módulos y dependencias](../base/modulos-y-dependencias.md) | Por validar en pruebas |
| CA-18 | Dependencias técnicas del CRM | Solo se consideraban `crm`, `utm` y `contacts` | `crm` instala también `sales_team`, `calendar`, `mail` y `phone_validation`; `sale_crm` se instala automáticamente con Ventas | Representar los módulos que realmente se instalan y sus grupos de acceso | [Módulos y dependencias](../base/modulos-y-dependencias.md), [Vistas C4](../vistas/consolidado-diagramas.md) | Documentado; por verificar en la instancia |

## 6. Decisiones pendientes

* Revisar [ADR-003](../decisiones/ADR-003-contabilidad.md) con su autora en lugar de crear otro ADR que lo reemplace.
* Evaluar nuevos ADR para abastecimiento sin Compras, modelo de almacenes y ubicaciones, y respaldo y recuperación del entorno.
* Determinar si la política del Punto de Venta y la responsabilidad sobre los datos maestros requieren ADR o bastan registros de configuración.

## 7. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | César Joaquín | Versión inicial |
| 0.2 | 23/10/2026 | Gabriel Martinez | Adición de CA-09 (CRM) y CA-10 (Contactos) como componentes del ERP |
| 0.3 | 23/10/2026 | Diego Pinedo | Precisión de CA-10 como dependencia obligatoria y adición de CA-18 (dependencias técnicas del CRM; CA-11 a CA-17 ya están asignados en VER-03) |

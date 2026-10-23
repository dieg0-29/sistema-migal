# VER-01: Arquitectura base — ERP

## 1. Metadatos

* **Fecha:** 17/10/2026
* **Versión:** 0.1
* **Autores:** César Joaquín
* **Sistema incorporado:** ERP (Odoo 18 Community)
* **Sistemas existentes afectados:** Ninguno

## 2. Propósito

Establecer la base operativa de MIGAL: ventas, punto de venta, inventario y facturación con contabilidad básica en una sola plataforma, conviviendo con KeyFacil para la emisión de comprobantes.

## 3. Elementos que se agregan


| Elemento | Decisión | Respaldo |
|---|---|---|
| Plataforma | Odoo como ERP, elegido frente a StarSoft y Microsoft Dynamics 365 | [ADR-001](../decisiones/ADR-001-seleccion-erp.md) |
| Versión y edición | Odoo 18 Community, por presupuesto y ausencia de personal de TI | [ADR-002](../decisiones/ADR-002-version-y-edicion.md) |
| Contabilidad | Módulos de la comunidad (OCA / Odoo Mates) | [ADR-003](../decisiones/ADR-003-contabilidad.md) |
| Módulos | Ventas (`sale_management`), Punto de Venta (`point_of_sale`), Inventario (`stock`) y Facturación (`account`), con extensión contable comunitaria; sin Compras | [Módulos y dependencias](../base/modulos-y-dependencias.md), [Vistas C4](../vistas/consolidado-diagramas.md) |
| Sistemas externos | KeyFacil para comprobantes; WhatsApp como canal, sin automatizar | [Contexto de la empresa](../base/contexto-empresa.md) |

## 4. Corrección de dependencias de la base

La versión inicial no consideró las dependencias obligatorias de los módulos seleccionados. La revisión de los manifiestos de Odoo 18.0 ([módulos y dependencias](../base/modulos-y-dependencias.md)) corrige la base así:

| Hallazgo | Antes | Después | Estado |
|---|---|---|---|
| H-02 | La contabilidad dependía solo del módulo comunitario (ADR-003) | Facturación (`account`) se instala obligatoriamente con Ventas; la extensión comunitaria solo la complementa | Documentado; revisión de ADR-003 pendiente |
| H-03 | Punto de Venta independiente de Inventario | Punto de Venta requiere `stock_account`, es decir, Inventario y Facturación | Documentado |
| H-04 | La entrega al confirmar se atribuía a Ventas o Inventario | La aporta el puente `sale_stock`, instalado automáticamente | Documentado; pendiente de prueba |
| H-05 | Valoración de inventario sujeta al módulo comunitario | `stock_account` se instala automáticamente; la valoración depende de su configuración | Por validar (CA-07) |
| H-06 | Ventas y Punto de Venta sin relación | El puente `pos_sale` permite liquidar pedidos de Ventas en el Punto de Venta | Uso por decidir (RSK-11) |
| H-07 | Odoo sin capacidad de facturar | Facturación permite crear facturas internas que no son comprobantes fiscales | Uso por decidir (RSK-10) |

## 5. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | César Joaquín | Versión inicial |
| 0.2 | 23/10/2026 | Diego Pinedo | Corrección de módulos y dependencias obligatorias (Facturación, `stock_account` y módulos puente) |

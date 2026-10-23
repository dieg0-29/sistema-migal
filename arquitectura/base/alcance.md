# Alcance de la solución — Ferre & Inversiones MIGAL

**Estado**: propuesta TO-BE actualizada por el equipo el 22/10/2026.

## 1. Objetivo

Organizar los procesos, datos, aplicaciones y controles de Ferre & Inversiones MIGAL para reducir la fragmentación de su información comercial y operativa. La solución se construye de forma progresiva, actualmente el alcance comprende los sistemas ERP y CRM.

El alcance incorpora el contexto comercial desde la campaña y la consulta inicial hasta la cotización aceptada y su paso al ERP. También contempla el modelado de la ejecución operativa, el abastecimiento y el inventario, sin afirmar que todos los módulos o automatizaciones estén implementados.

## 2. Sistemas que componen la solución

| Sistema | Plataforma | Rol en la solución | Decisión de respaldo |
|---|---|---|---|
| ERP | Odoo 18 Community | Base operativa: ventas, punto de venta, inventario y contabilidad básica | [ADR-001](../decisiones/ADR-001-seleccion-erp.md), [ADR-002](../decisiones/ADR-002-version-y-edicion.md), [ADR-003](../decisiones/ADR-003-contabilidad.md) |
| CRM | Odoo 18 Community (misma instancia) | Gestión comercial: campañas, contactos, leads, oportunidades y cotizaciones | [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [INT-01](../integraciones/INT-01-CRM-ERP.md) |
| KeyFacil | Externo | Emisión de comprobantes electrónicos | [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md) |
| WhatsApp / teléfono | Externo | Canales de interacción con clientes y maestros de obra | — |

## 3. Alcance por sistema

### 3.1. ERP

**Incluido**

* Catálogo de productos y precios.
* Inventario en tres puntos físicos: Tienda, Almacén 1 y Almacén 2.
* Venta mayorista mediante pedido y venta de mostrador mediante punto de venta.
* Registro contable básico mediante módulos de la comunidad.
* Modelado de O2C hasta programación de entrega, traslado con documentos, conformidad, cobro y cierre, sin presentarlo como prueba de implementación completa.
* Modelado de P2P documental para necesidades de abastecimiento, aprobación, oferta de proveedor, orden de compra documental, recepción, factura de proveedor y pago.

**Excluido**

* Módulo de Compras; el abastecimiento puede modelarse como proceso sin implantarse como módulo.
* Emisión fiscal desde Odoo; la realiza KeyFacil.
* Reservas, rebajas instantáneas de inventario o permisos operativos no verificados mediante prueba.
* Automatización de la programación logística, la entrega, la facturación y el cobro cuando solo formen parte del modelo de negocio.

### 3.2. CRM

**Incluido**

* Gestión de campañas.
* Consulta inicial del cliente.
* Registro del lead, cuenta, contactos y obra.
* Calificación de la oportunidad.
* Elaboración de la cotización, versiones y excepciones de precio.
* Seguimiento del compromiso comercial y de la decisión del cliente.
* Contrato de la solicitud de pedido con datos, estados y respuestas aceptada, rechazada o condicionada.
* Paso de la cotización aceptada al ERP ([INT-01](../integraciones/INT-01-CRM-ERP.md)).

**Excluido**

* Automatización de WhatsApp.
* Interfaz técnica con KeyFacil; se mantiene registro y conciliación manual.

## 4. Límites de la solución

* El CRM conserva el contexto y el compromiso comercial; el ERP gestiona la ejecución desde el pedido confirmado ([INT-01](../integraciones/INT-01-CRM-ERP.md)).
* No se desarrollan APIs, conectores ni contratos técnicos de mensajes entre sistemas.
* La representación lógica de Tienda, Almacén 1 y Almacén 2 requiere una decisión.
* La necesidad de un complemento contable debe precisarse al revisar [ADR-003](../decisiones/ADR-003-contabilidad.md).
* O2C completo considera programación de entregas por viaje, traslado con documentos, conformidad, comprobante al primer evento (pago total o entrega en operación ordinaria), cobros y cierre. P2P contempla tres causas de necesidad —mínimo, venta y programa— y una recepción y factura que no necesariamente son secuenciales.
* Las cuatro categorías de cemento, fierro, agregados y ladrillos pueden cotizarse y tratarse con stock insuficiente, pero sin falsa reserva ni promesa logística irrevocable si falta suministro.
* En el modelo simplificado, el stock físico apto se expresa como reservado más disponible; la demanda no cubierta se mantiene separada.
* El despacho programado desde el día siguiente y la espera en obra de 30 minutos están sujetos a consenso, calendario y excepciones pendientes.

## 5. Evolución

Los cambios que produce cada sistema incorporado sobre la arquitectura existente se registran en [evolución de la arquitectura](../versiones/README.md).

## 6. Fuentes

* [Contexto de la empresa](contexto-empresa.md)
* [Fuentes de negocio](../../fuentes/fuentes-negocio.md)
* [Supuestos y vacíos](../../fuentes/supuestos-y-vacios.md)
* [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md)

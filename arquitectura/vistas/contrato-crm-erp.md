# Vista funcional — Integración CRM / ERP para el EP

**Tipo:** Vista funcional complementaria de la arquitectura C4.  
**Estado:** `[AC][TO-BE]` — Diseño conceptual acordado por el equipo, pendiente de pruebas en Odoo y validación por MIGAL.  
**Plataforma:** Odoo 18 Community, una misma instancia para CRM, Ventas e Inventario.

## 1. Propósito y alcance

Explicar la continuidad desde la **gestión comercial de una necesidad** hasta la **solicitud de pedido y respuesta operativa del ERP**, sin confundir la aceptación de la cotización con un pedido confirmado.

El foco del EP es la frontera funcional CRM–ERP: información entregada, verificaciones y resultados **aceptada, condicionada o rechazada**. La preparación, entrega, facturación mediante KeyFacil, cobro y conciliación se documentan como continuidad del proceso O2C, no como pruebas de implementación.

## 2. Diagrama funcional

![Flujo funcional CRM–ERP](funcionales/render/contrato-crm-erp.png)

**Fuente editable:** [contrato-crm-erp.puml](funcionales/contrato-crm-erp.puml).

## 3. Secuencia y responsabilidades

| Momento funcional | Regla de interpretación |
|---|---|
| **Gestión CRM** | Atención comercial registra lead, lo califica, gestiona la oportunidad e identifica productos y cantidades. |
| **Consulta previa de inventario** | Antes de **cada cotización**, Comercial consulta disponibilidad de **todos los productos** por ubicación; Almacén verifica información física o discrepancias cuando corresponda. Consultar no equivale a reservar. |
| **Faltantes** | Para **cemento, fierro, agregados y ladrillos** puede emitirse una cotización **condicionada a abastecimiento**, sin reservar unidades inexistentes ni prometer entrega firme. Para otros faltantes se revisa disponibilidad o alternativas antes de emitir la oferta. |
| **Cotización y negociación** | El proceso comercial CRM gestiona cotizaciones y versiones hasta la aceptación. Si se modifican productos, cantidades o condiciones, se vuelve a consultar la disponibilidad y se conserva la versión anterior. |
| **Aceptación comercial** | Se registra quién aceptó, cuándo y qué versión. **La aceptación no confirma por sí sola un pedido**. |
| **Solicitud al ERP — EV-14** | Se remite para evaluación la cotización aceptada, sus líneas, condiciones, autorizaciones y disponibilidad consultada; es una **transición funcional**, no una API ni un pedido duplicado. |
| **Revalidación operativa** | El ERP verifica nuevamente datos, autorizaciones, disponibilidad de todos los productos y viabilidad de la fecha de entrega, porque las circunstancias pudieron cambiar desde la cotización. |
| **Respuesta ERP — EV-15** | **Aceptada:** procede la confirmación del pedido. **Condicionada:** se registran condiciones pendientes y se coordina su resolución, sin confirmar todavía. **Rechazada:** se registra el motivo y se devuelve a Comercial. |

El abastecimiento de faltantes se trata en **P2P documental**, sin presumir el módulo Compras ni órdenes automáticas. La respuesta condicionada requiere nueva revisión una vez resueltas las condiciones; no autoriza automáticamente una compra ni una entrega.

## 4. Relación con los módulos de Odoo y la arquitectura C4

![Vista C4 de integración CRM–ERP](c4/render/integracion-crm-erp.png)

**Fuente editable:** [integracion-crm-erp.puml](c4/fuentes/integracion-crm-erp.puml).

| Ámbito | Responsabilidad funcional y soporte propuesto |
|---|---|
| **CRM** | Contexto comercial, contactos, leads, oportunidades, seguimiento, negociación y conservación del acuerdo comercial. |
| **Ventas (`sale` / `sale_crm`)** | Soporte técnico de cotizaciones vinculadas a oportunidades y posterior pedido. **La cotización continúa bajo responsabilidad del proceso comercial**, aunque utilice modelos del módulo Ventas. |
| **Inventario (`stock`)** | Información por ubicación para consulta previa y revalidación; reserva y movimientos reales deben comprobarse en Odoo. |
| **P2P** | Necesidades de abastecimiento y compras documentales fuera del módulo Odoo Compras. |
| **KeyFacil** | Emisión del comprobante fiscal como sistema externo; el registro o conciliación de referencias en el ERP es una función propuesta. |

La vista C4 muestra **módulos y responsabilidades**, no todos los pasos ni estados del diagrama funcional. Ambos describen **una sola instancia Odoo**, sin inventar integraciones entre instancias ni nuevos modelos técnicos para EV-14/EV-15.

## 5. Validación y documentos relacionados

El diseño sigue siendo **conceptual**. Quedan por verificar el uso real de `sale.order`, `sale_crm`, `action_confirm()`, las versiones de cotización, las reservas, los permisos y la posibilidad de aplicar los controles funcionales mediante la configuración disponible. **No se afirma que Odoo implemente de forma nativa los estados de respuesta definidos por MIGAL.**

**Documentos de referencia:** [INT-01 — CRM/ERP](../integraciones/INT-01-CRM-ERP.md) (EV-14, EV-15 y F-02), [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [flujo comercial](../../procesos/comercial-crm/flujo-comercial.md), [pedido O2C](../../procesos/o2c/pedido.md), [parámetros de inventario](../../datos/maestros/parametros-inventario.md), [glosario](../../datos/glosario-negocio.md) y [vistas C4 consolidadas](consolidado-diagramas.md).

**Pendiente de alineación en el repositorio:** la redacción antigua de **F-02** en INT-01 todavía describe el paso directo de cotización aceptada a pedido; debe armonizarse con **EV-14 / EV-15**, la consulta previa obligatoria y la revalidación antes de confirmar. Los diagramas fuente existentes deben corregirse si su contenido aún no refleja visualmente todos estos controles.

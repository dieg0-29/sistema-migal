# Alcance de la solución — Ferre & Inversiones MIGAL

**Estado:** propuesta TO-BE revisada por el equipo el 24/10/2026.

## 1. Objetivo

Organizar e integrar los procesos, datos, aplicaciones y controles de Ferre & Inversiones MIGAL mediante una solución CRM–ERP basada en Odoo 18 Community, reduciendo la fragmentación de la información comercial y operativa y mejorando la trazabilidad de ventas, inventario, abastecimiento, entregas y cobros.

El alcance comprende el recorrido **Order to Cash (O2C)** desde la identificación de la necesidad comercial del cliente hasta el cobro y cierre de la operación.

Para su modelado se distinguen:

- **Sistemas funcionales:** CRM y ERP, definidos por las responsabilidades que asumen en el proceso.
- **Módulos técnicos:** aplicaciones de Odoo utilizadas para ejecutar las actividades de ambos sistemas.
- **Procesos transversales:** O2C, inventario y P2P, que involucran diferentes responsabilidades y módulos.

CRM y ERP se implementan conceptualmente dentro de **una misma instancia de Odoo 18 Community**. Su separación es funcional y no implica dos plataformas independientes.

## 2. Sistemas y módulos que componen la solución

### 2.1. Sistemas funcionales

| Sistema | Responsabilidad | Alcance funcional |
|---|---|---|
| CRM | Gestión comercial y relación con el cliente. | Desde la necesidad comercial, lead, lead calificado, oportunidad y cotización hasta su aceptación, solicitud de pedido, seguimiento de la validación operativa y coordinación de la confirmación. |
| ERP | Validación operativa y ejecución de las operaciones empresariales. | Evaluación de solicitudes de pedido, verificación de disponibilidad, confirmación operativa, reservas, inventario, abastecimiento documental, entregas, registros internos de facturación, conciliación y cobros. |
| KeyFacil | Emisión fiscal externa. | Emisión de comprobantes electrónicos y conservación de referencias para conciliación. |
| WhatsApp / teléfono | Comunicación externa. | Recepción de consultas y coordinación con clientes y maestros de obra, sin integración automática aprobada. |

La responsabilidad sobre una actividad no depende exclusivamente del nombre del módulo que la soporta.

### 2.2. Módulos de Odoo utilizados

| Módulo o aplicación | Identificador técnico | Utilización funcional |
|---|---|---|
| CRM | `crm` | Registro y seguimiento de leads, oportunidades y actividades comerciales. |
| Contactos | `contacts` | Maestro compartido de cuentas y contactos de CRM y ERP. |
| Ventas | `sale_management` / `sale` | Elaboración y gestión de cotizaciones durante el proceso comercial; confirmación y gestión de pedidos de venta. |
| Punto de Venta | `point_of_sale` | Atención de ventas directas de mostrador y registro de sus operaciones comerciales, de cobro e inventario. |
| Inventario | `stock` | Consulta de existencias, recepciones, reservas, transferencias, entregas y ajustes. |
| Facturación | `account` | Registros internos de facturación, pagos y contabilidad básica, sin sustituir la emisión fiscal de KeyFacil. |

**Ventas y POS son módulos técnicos que apoyan actividades comerciales, pero también participan en la ejecución operativa del ERP.** No deben identificarse exclusivamente con un solo sistema funcional.

Los módulos puente y dependencias obligatorias se detallan en [módulos y dependencias](modulos-y-dependencias.md).

## 3. Alcance por sistema funcional

### 3.1. Sistema CRM — Gestión comercial

**Incluido en el modelo TO-BE**

- Identificación y registro de la necesidad comercial del cliente.
- Registro de consultas presenciales, por WhatsApp, llamadas y referidos.
- Gestión de leads y calificación de leads.
- Seguimiento de oportunidades comerciales.
- Identificación de etapa comercial, responsable y próxima acción con fecha.
- Registro de cuentas, contactos, maestros de obra y obras mediante el maestro compartido.
- Elaboración y modificación de cotizaciones mediante las funcionalidades del módulo Ventas.
- Conservación de versiones de cotización, condiciones comerciales y excepciones de precio autorizadas.
- Consulta preliminar de disponibilidad de productos, sin generar reservas por la consulta.
- Registro de la aceptación del cliente sobre una versión identificable de cotización (EV-11).
- Generación de la solicitud de pedido a partir de la cotización aceptada (EV-14).
- Seguimiento de la validación operativa del ERP y de su respuesta (EV-15).
- Coordinación del pedido confirmado, incluyendo el seguimiento comercial del compromiso de abastecimiento cuando la respuesta sea condicionada.
- Conservación de la trazabilidad comercial hasta la confirmación del pedido.

**Módulos de apoyo:** CRM, Contactos, Ventas e Inventario para consulta de disponibilidad, considerando las funcionalidades compartidas.

**Excluido:**

- Automatización de WhatsApp.
- Confirmación automática de pedidos sin validación operativa.
- Definición de estados operativos específicos para cada etapa comercial sin decisión posterior.
- Controles automáticos no configurados ni verificados.

### 3.2. Sistema ERP — Validación y ejecución operativa

**Incluido en el modelo TO-BE**

- Validación de solicitudes de pedido procedentes de cotizaciones aceptadas.
- Comprobación de existencias por ubicación, cantidades requeridas y disponibilidad operativa.
- Registro de la respuesta de validación ERP (EV-15): aceptada, condicionada o rechazada.
- Confirmación de pedidos con respuesta aceptada o condicionada.
- En las respuestas condicionadas, identificación de las cantidades faltantes y registro del compromiso de abastecimiento necesario para completar el pedido.
- Gestión de reservas de inventario al confirmar pedidos, según disponibilidad y configuración, sin considerar reservadas las cantidades faltantes.
- Gestión de existencias en Tienda, Almacén 1 y Almacén 2.
- Registro de mercadería por abastecimiento mediante órdenes de recepción validadas.
- Atención de faltantes a través del abastecimiento documental y recepción posterior.
- Registro de salidas mediante órdenes de entrega validadas o movimientos correspondientes a operaciones POS.
- Movimientos de transferencia interna entre ubicaciones.
- Ajustes por deterioro, merma, retiro, pérdida o diferencias físicas, debidamente sustentados y autorizados.
- Programación de despachos desde el día siguiente a la confirmación del pedido, considerando la disponibilidad de mercadería para su ejecución.
- Gestión de entregas, cantidades pendientes y conformidad del cliente.
- Registro interno de operaciones de facturación y referencias a los comprobantes emitidos por KeyFacil.
- Gestión de cobros, pagos, vencimientos y saldos.
- Modelado documental del abastecimiento P2P, sin instalación del módulo de Compras.

**Módulos de apoyo:** Ventas, Punto de Venta, Inventario, Facturación y Contactos, además de sus dependencias técnicas obligatorias.

**Excluido:**

- Módulo de Compras (`purchase`).
- Generación automática de solicitudes de compra mediante dicho módulo.
- Emisión fiscal desde Odoo en sustitución de KeyFacil.
- Integración automática con KeyFacil.
- Automatización logística o de transporte no aprobada.
- Funcionalidades, bloqueos y permisos que no hayan sido configurados o probados.

## 4. Alcance transversal y reglas de negocio

### 4.1. Recorrido O2C

El proceso O2C integra las actividades comerciales del CRM con la validación y ejecución operativa del ERP.

Su secuencia funcional de referencia es:

**Necesidad comercial → Lead → Lead calificado → Oportunidad → Cotización → Aceptación → Solicitud de pedido (EV-14) → Validación ERP (EV-15) → Pedido confirmado (aceptado o condicionado) → Reserva de stock disponible → Programación y entrega → Comprobante → Cobro → Cierre.**

Se conserva la secuencia de eventos EV-11, EV-14 y EV-15 descrita en [INT-01](../integraciones/INT-01-CRM-ERP.md).

Las respuestas de validación tienen el siguiente significado:

| Respuesta EV-15 | Efecto sobre la solicitud de pedido |
|---|---|
| **Aceptada** | Se confirma el pedido con disponibilidad suficiente para su atención. |
| **Condicionada** | Se confirma el pedido, aunque exista stock insuficiente. MIGAL asume el compromiso de abastecer las cantidades faltantes y atender el pedido completo. |
| **Rechazada** | La solicitud no se convierte en un pedido confirmado y se registra el motivo. |

Una respuesta **condicionada constituye una aceptación del pedido sujeta exclusivamente a abastecimiento**. No necesita cambiar posteriormente a «aceptada»: el pedido ya está confirmado y lo pendiente es completar el abastecimiento y su atención.

La consulta y validación de stock ocurren antes de la confirmación. La confirmación habilita la ejecución operativa y la gestión de reservas, sin equivaler a una salida física del inventario.

Las ventas directas de mostrador mediante POS pueden seguir un recorrido simplificado, sin exigir que todas pasen por un lead y una oportunidad.

### 4.2. Frontera CRM–ERP

- CRM conserva la necesidad, el contexto comercial, los participantes, la cotización, sus versiones y la aceptación del cliente.
- El área comercial genera la solicitud de pedido EV-14 sobre la base de la cotización aceptada EV-11.
- ERP verifica la disponibilidad y registra la respuesta EV-15.
- Tanto una respuesta aceptada como una condicionada permiten confirmar el pedido.
- Cuando la respuesta es condicionada, ERP registra el faltante y gestiona su abastecimiento; CRM conserva el seguimiento del compromiso asumido frente al cliente.
- Una respuesta rechazada impide confirmar el pedido.
- ERP asume la ejecución del pedido confirmado y conserva la referencia a las condiciones comerciales aceptadas.
- Al operar en una misma instancia de Odoo, esta frontera no requiere por sí misma una API ni un conector entre CRM y ERP.

### 4.3. Inventario

- La consulta de disponibilidad no genera una reserva ni modifica existencias.
- La reserva se gestiona al confirmar el pedido, conforme a las existencias disponibles y la configuración.
- En un pedido condicionado, las cantidades faltantes se identifican como demanda pendiente de abastecimiento y no como stock reservado.
- Las nuevas existencias procedentes del abastecimiento se registran únicamente mediante órdenes de recepción validadas.
- La solicitud o autorización de compra no incrementa el inventario.
- Cuando se recibe y valida la mercadería faltante, las existencias se actualizan y pueden completarse las reservas correspondientes.
- La salida por venta se registra al validar la orden de entrega o mediante el movimiento correspondiente a una operación POS.
- Los productos deteriorados, retirados, perdidos o afectados por mermas se registran mediante movimientos o ajustes justificados.
- Las transferencias entre ubicaciones modifican la distribución del stock, sin aumentar el total físico de MIGAL.
- La demanda no cubierta se identifica separadamente de las existencias reales.

### 4.4. Logística

- Los despachos se programarán desde el día siguiente a la confirmación del pedido, considerando disponibilidad y recursos logísticos.
- En pedidos condicionados, el abastecimiento pendiente deberá contemplarse en la coordinación y programación de la entrega.
- Las entregas conservarán cantidades programadas, despachadas, pendientes y conformidad.
- La espera máxima de 30 minutos en obra permanece como propuesta pendiente de aprobación.
- La definición de fechas de entrega en pedidos condicionados requiere coordinación con el proveedor y seguimiento del abastecimiento, sin registrar como recibida mercadería todavía pendiente.

### 4.5. Comprobantes y cobros

- KeyFacil conserva la emisión fiscal de comprobantes electrónicos.
- Facturación de Odoo se utiliza para los registros internos que se definan, sin sustituir automáticamente a KeyFacil.
- Deben conservarse referencias de conciliación entre pedido, comprobante y cobro.
- Las políticas específicas de crédito y descuentos requieren confirmación con los propietarios.

## 5. Límites técnicos y decisiones pendientes

- La plataforma propuesta es Odoo 18 Community, con CRM y ERP en una sola instancia.
- Las aplicaciones seleccionadas requieren dependencias obligatorias y pueden activar módulos puente.
- No se incluye el módulo de Compras.
- P2P se mantiene como proceso documental.
- KeyFacil y WhatsApp permanecen externos, sin integraciones automáticas aprobadas.
- La representación de Tienda, Almacén 1 y Almacén 2 como almacenes o ubicaciones debe definirse técnicamente.
- La necesidad de una extensión contable comunitaria debe revisarse conforme a [ADR-003](../decisiones/ADR-003-contabilidad.md).
- Deben verificarse los mecanismos de reserva, recepción, entrega, POS, ajustes y autorizaciones en la instancia.
- Debe verificarse cómo registrar la respuesta condicionada y el seguimiento del abastecimiento pendiente en Odoo, sin asumir que existe un flujo automático específico.
- Las propuestas funcionales no constituyen evidencia de implementación ni de pruebas ejecutadas.

## 6. Evolución

Los cambios que produce cada sistema incorporado sobre la arquitectura existente se registran en [evolución de la arquitectura](../versiones/README.md).

La evolución deberá mantener la separación entre responsabilidades funcionales, módulos técnicos y procesos transversales, así como la trazabilidad de las decisiones y dependencias de Odoo.

## 7. Fuentes

- [Contexto de la empresa](contexto-empresa.md)
- [Fuentes de negocio](../../fuentes/fuentes-negocio.md)
- [Supuestos y vacíos](../../fuentes/supuestos-y-vacios.md)
- [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md)
- [Frontera CRM–ERP: INT-01](../integraciones/INT-01-CRM-ERP.md)
- [Módulos y dependencias](modulos-y-dependencias.md)

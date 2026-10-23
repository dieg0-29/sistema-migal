# Flujo Order to Cash: de la necesidad comercial al cobro

## 1. Metadatos

* **Fecha:** 23/10/2026
* **Versión:** 0.3
* **Sistema:** ERP (Odoo 18 Community: Ventas, Punto de Venta, Inventario y contabilidad básica) y KeyFacil (externo)

## 2. Propósito

`[E][AS-IS]` Las ventas se registran en notas y en la memoria de los propietarios, el stock no se descuenta al vender y la facturación en KeyFacil no queda vinculada a la operación. `[FN-01][FN-02]`

El **O2C integral comienza con la necesidad comercial del cliente**, que puede llegar por campañas, referidos o iniciativa propia; el CRM gestiona el lead, la calificación mediante lista de materiales, la oportunidad al intercambiar condiciones, la cotización, negociación, versiones y aceptación. **Este documento detalla el tramo operativo posterior a la aceptación y la ruta de mostrador**, sin duplicar el [flujo comercial](../comercial-crm/flujo-comercial.md). El ERP valida la solicitud, confirma el pedido cuando corresponda, programa, despacha y registra la conformidad de entrega; KeyFacil emite el comprobante externo y se verifican cobros, saldos y cierre. El seguimiento comercial y la posventa conservan el vínculo con el pedido de origen.

## 3. Inicio y fin

* **Inicio del O2C integral:** necesidad comercial; campañas y llegada directa/referida, lead, oportunidad, cotización y negociación se detallan en el [flujo comercial](../comercial-crm/flujo-comercial.md).
* **Inicio del tramo operativo aquí detallado:** cotización/versión aceptada en el CRM que origina una solicitud de pedido (EV-14, [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md), F-02), o venta de mostrador en el punto de venta.
* **Fin:** cierre normal con entregas, comprobantes y cobros conciliados; o registro de una excepción documentada sin declarar cierre normal mientras subsistan entregas, saldos u otras obligaciones pendientes. La posventa puede continuar vinculada al pedido.

El cliente acepta una versión de cotización (EV-11) y Comercial conserva el acuerdo y solicita la validación (EV-14). EV-15 devuelve una respuesta ERP aceptada, condicionada o rechazada; **solo la aceptada habilita a Comercial a registrar la confirmación operativa del pedido**, según responsabilidades y permisos por validar. La aceptación del cliente no sustituye la validación ERP.

## 4. Actores

| Perfil | Participación |
|---|---|
| R-ATN | Consulta existencias **mediante el sistema** antes de cotizar y durante la revalidación; registra la aceptación acordada con el cliente, solicita la validación, comunica EV-15 y registra la confirmación operativa solo si procede; atiende mostrador y mantiene el contacto/posventa |
| R-PRO | Resuelve y autoriza excepciones (crédito, descuentos, faltantes y cambios que lo requieran); apoya la validación operativa cuando corresponda y opera la emisión externa en KeyFacil |
| R-ALM | Mantiene registros físicos oportunos y atiende discrepancias de inventario cuando se le requiera; prepara y documenta la salida, la entrega real y las incidencias |
| R-FAC | Registra referencias de comprobantes, cobros, vencimientos, saldos y conciliación (función ejercida por propietarios) |
| Función logística (persona por asignar) | Coordina programación por viaje, fecha, destino, traslado y conformidad; no constituye un perfil nuevo |

## 5. Entradas y salidas

| Entradas | Salidas |
|---|---|
| Necesidad y trayectoria comercial referenciadas desde CRM: cotización/versión aceptada con líneas, precios, **consulta previa en sistema de stock disponible**, destino, **modalidad de pago consultada antes de cotizar y condiciones finalmente aceptadas**, contacto y soporte; o venta de mostrador; stock actualizado por ubicación y datos fiscales cuando corresponda | Solicitud EV-14 y respuesta EV-15, pedido confirmado si procede, programación por viaje, documentos de salida/traslado, conformidad e incidencias, referencia de KeyFacil, cronograma/cobros/saldo y cierre trazable |

## 6. Flujo

**Vista integral del O2C:** necesidad comercial → captación y lead (**interesado o descartado**) → lead calificado mediante lista/necesidad concreta → oportunidad tras consultar/intercambiar condiciones (incluida modalidad de pago) → **consulta por Comercial en el sistema del stock disponible de todos los productos, destino y términos para cotizar** → cotización y negociación por versiones (reconsultando existencias) → aceptación de una versión (tramo [CRM](../comercial-crm/flujo-comercial.md)) → EV-14 (solicitud) → EV-15 (validación ERP) → confirmación cuando proceda → programación, despacho y conformidad → facturación y cobros según el evento aplicable → conciliación y cierre. Esta vista no sustituye el detalle comercial ni implica que todas las ventas de mostrador requieran lead o cotización. Los términos explorados antes de cotizar pueden precisar aprobación o ajustes durante la negociación; los aplicables al pedido son los de la versión aceptada.

### 6.1. Venta con pedido (obra o mayorista)

| Paso | Actividad | Actor | Sistema | Regla | Evidencia |
|---|---|---|---|---|---|
| 1 | Registrar EV-14 desde la versión aceptada (EV-11), con cliente/contacto, líneas, destino y condiciones acordadas | R-ATN | CRM / ERP (Ventas) | RN-07, RN-08 | Solicitud y aceptación vinculadas a una versión concreta; todavía no es pedido confirmado |
| 2 | Comprobar que la modalidad de pago fue **consultada antes de cotizar** y que la versión aceptada refleja las condiciones acordadas; someter crédito, descuentos o cambios excepcionales a autorización de propietarios | R-ATN / R-PRO (excepciones) | CRM / ERP / registro documental | RN-15, SOD-04 | Condiciones de la versión aceptada, autorizaciones aplicables, fecha y responsable |
| 3 | **Comercial reconsulta en el sistema** stock apto, reservado y **disponible** de cada producto por ubicación, junto con viabilidad logística y demanda no cubierta; almacén apoya si hay discrepancias físicas y propietarios resuelven excepciones. Registrar la respuesta de validación ERP (EV-15) y, **solo si es aceptada**, Comercial registra la confirmación operativa ligada a la aceptación del cliente. Si es condicionada, resolver y revalidar; si es rechazada, no confirmar | R-ATN (consulta, comunicación y registro) / R-PRO (excepciones) / R-ALM (apoyo físico si aplica) | ERP (Ventas e Inventario) | RN-08, RN-09 | Consulta actualizada, EV-15 con estado, motivo, responsable y próxima acción; pedido confirmado solo si procede y sin presumir reserva física completa. Permisos técnicos por validar |
| 4 | **Operacionalizar, sin renegociar**, la modalidad y condiciones de pago de la versión aceptada (contado, pago total previo a la entrega o crédito autorizado): registrar importes, vencimientos, cronograma y responsable del seguimiento cuando corresponda; cualquier cambio relevante vuelve a revisión y aceptación | R-FAC / R-PRO (aprobaciones o cambios) / R-ATN (comunicación) | ERP / registro documental | RN-12, RN-15 | Condiciones aceptadas, cronograma si aplica, saldo inicial, responsable y rastro de cualquier cambio, sin afirmar automatización |
| 5 | Programar entrega por uno o varios viajes según cantidades disponibles, destino, cobertura, fecha y responsable; comunicar condiciones y pendientes al cliente | Función logística / R-ATN / R-ALM | ERP / registro documental | RN-09, RN-10 | Plan por viaje con fecha, destino, cantidades, responsable y condiciones de entrega |
| 6 | Preparar productos de cada viaje por ubicación; comprobar cantidades y diferencias antes de la salida | R-ALM | ERP (Inventario) | RN-09, RN-10 | Preparación, productos, ubicaciones, cantidades listas y pendientes |
| 7 | Despachar con documento de salida y documentación de traslado, incluida guía de remisión cuando corresponda, registrando fechas y responsables | R-ALM / función logística | ERP / documentación de traslado | RN-10 | Documento de salida/traslado, referencia de guía si aplica, origen, destino, transportista/responsable y fecha |
| 8 | Registrar entrega efectiva y conformidad o no conformidad del receptor; documentar cantidades parciales y observaciones. **[ND] El procedimiento posterior ante rechazo/no conformidad aún no ha sido definido por el equipo; no asumir devolución, reposición, reprogramación ni cierre automático** | R-ALM / función logística | ERP / soporte documental | RN-10 | Fecha, receptor, firma o constancia, cantidades reales y observaciones; si no hay conformidad, incidencia abierta pendiente de procedimiento [ND] |
| 9 | **Circuito fiscal no secuencial:** emitir el comprobante en KeyFacil ante el primer evento aplicable (pago total o entrega, en operación ordinaria); puede ocurrir antes de la entrega | R-PRO | KeyFacil | RN-11 | Comprobante y evento que motivó su emisión; momento sujeto a validación fiscal |
| 10 | Registrar la referencia de KeyFacil y conciliar cliente, pedido, importe y estado del comprobante | R-FAC | ERP / registro documental | RN-11 | Tipo, serie, número, fecha, importe, vínculo y diferencias justificadas |
| 11 | **Circuito de cobros no secuencial:** verificar y aplicar cada cobro cuando ocurra, antes o después de la entrega, conservando soporte y saldo | R-FAC | ERP / registro documental | RN-12 | Fecha, medio, importe verificado, aplicación y saldo, sin registrar pagos no acreditados |
| 12 | Dar seguimiento a vencimientos, cronograma y cobros pendientes; gestionar incidencias y registrar próxima acción | R-FAC / R-PRO | ERP / registro documental | RN-12 | Fechas pactadas y efectivas, responsable, recordatorios, abonos parciales de una deuda a crédito y saldo pendiente |
| 13 | **Cerrar normalmente solo cuando** entregas, comprobantes, cobros y diferencias estén conciliados. Si quedan entregas, saldos o incidencias pendientes, **mantener la operación abierta o en seguimiento** y registrar la excepción sin presentarla como cierre normal; conservar vínculo para posventa | R-PRO / R-FAC | ERP | RN-13, RN-14 | Estado de cierre normal, o pendientes y excepción trazables con responsable y próxima acción; referencias de posventa si surge un caso |

**Rutas alternativas de facturación y cobro (no son pasos consecutivos):**

| Ruta | Evento y secuencia posible | Control necesario |
|---|---|---|
| A. Pago total antes de entregar | Cobro total verificado → emisión en KeyFacil según procedimiento aplicable → despacho, entrega y conformidad | Conservar soporte del cobro, referencia fiscal, documentos de traslado y evidencia de entrega |
| B. Entrega antes del pago total | Despacho y entrega → emisión en KeyFacil según procedimiento aplicable → cobros posteriores y seguimiento de vencimientos | Conservar conformidad o incidencia, comprobante, cronograma, aplicaciones de pago y saldo |

**Orden y frontera:** los pasos 9 a 12 representan un circuito fiscal y de cobranza que puede intercalarse con la programación y la entrega; **la numeración no impone su ejecución cronológica**. Los abonos parciales de una obligación a crédito conservan soporte y saldo y no equivalen al pago total previo a la entrega; los adelantos parciales de clientes no forman parte del O2C ordinario (POL-07). Ambas rutas están sujetas a validación fiscal. La guía de remisión y otros documentos de traslado se verifican según la operación y la normativa aplicable, sin dar por probada su emisión desde Odoo. La confirmación del pedido tampoco implica por sí sola una reserva física completa.

La programación desde el día siguiente y la espera de 30 minutos en obra son **propuestas pendientes de aprobación**, no plazos operativos vigentes. No se presupone programación automática, integración con KeyFacil ni un perfil de logística adicional.

Detalle de los pasos 1 a 3: [pedido](pedido.md). Detalle del registro fiscal: [facturación](facturacion.md). El tramo previo pertenece al mismo O2C integral y se desarrolla en el [flujo comercial](../comercial-crm/flujo-comercial.md).

### 6.2. Venta de mostrador (punto de venta)

| Paso | Actividad | Actor | Sistema | Regla | Evidencia |
|---|---|---|---|---|---|
| 1 | Registrar los productos y el cobro en el punto de venta | R-ATN | ERP (Punto de Venta) | RN-12 | Ticket interno y pago |
| 2 | Registrar o verificar la salida de stock de Tienda correspondiente a la venta; el descuento automático y su momento efectivo deben probarse en la instancia | Sistema / R-ATN, según validación | ERP (Inventario) | RN-10 | Movimiento asociado a venta; mecanismo y momento `[ND]` pendientes de prueba |
| 3 | Emitir el comprobante en KeyFacil y registrar su referencia | R-PRO / R-FAC | KeyFacil / ERP | RN-11 | Referencia conciliada con la venta |
| 4 | Al cerrar la sesión, conciliar el efectivo y los pagos con las ventas del turno | R-FAC | ERP (Punto de Venta) | RN-12 | Cierre de sesión con diferencias explicadas |

El ticket del punto de venta es un registro interno; el comprobante fiscal lo emite KeyFacil ([ADR-004](../../arquitectura/decisiones/ADR-004-odoo-community-fronteras-crm-erp.md)).

## 7. Excepciones

| Situación | Tratamiento | Regla |
|---|---|---|
| Stock insuficiente durante la revalidación | Registrar demanda no cubierta. R-PRO decide entre entrega parcial, sustituto o abastecimiento documental por P2P; cemento, fierro, agregados y ladrillos pueden mantenerse condicionados sin reserva ficticia ni fecha firme. Para otros productos, revisar antes de comprometer. No se genera compra automática | RN-09, RN-16 |
| Entrega parcial o programación en varios viajes | Se registra lo efectivamente entregado por viaje, el saldo pendiente y la fecha/responsable de reprogramación | RN-10 |
| Entrega sin conformidad o incidencia de traslado | Registrar receptor, fecha, observación, diferencias y soporte; **[ND] tratamiento posterior por definir con el equipo**. Mantener la incidencia pendiente sin declarar entrega conforme ni aplicar una solución no acordada | RN-10, RN-13 |
| Respuesta EV-15 condicionada o rechazada | Registrar motivo y siguiente acción; remediar y volver a validar si se condiciona; no confirmar automáticamente | RN-08, RN-09 |
| Pago o vencimiento incumplido | Mantener saldo y cronograma visible, responsable y siguiente seguimiento; no equiparar deuda con cobro verificado | RN-12 |
| Cambio posterior a la aceptación | Decide R-PRO; nueva versión y aceptación si cambia lo acordado | SOD-05 |
| Diferencia entre comprobante y pedido | Se mantiene en revisión; no se corrige la cotización para que coincida | RN-11 |
| Pago duplicado o sin soporte | No se aplica hasta verificarlo | RN-12 |
| Cancelación | Se revisan movimientos, comprobantes y pagos antes de documentar la resolución; no se borra el historial ni se confunde cancelación con cierre normal de una venta cumplida | RN-14 |

## 8. Indicadores

[Indicadores O2C](indicadores.md): O2C-K-01 a O2C-K-04 para el tramo comercial y O2C-K-05 a O2C-K-07 para entrega, conciliación fiscal y cobro. El detalle de programación por viaje, conformidad y seguimiento de vencimientos queda por contrastar con los indicadores existentes, sin inventar KPI aprobados.

## 9. Pendientes de validación

* Momento real del descuento de stock del punto de venta y de los asientos contables en la instancia (ver [VER-02](../../arquitectura/versiones/VER-02-crm.md), CA-06 y CA-07).
* Generación del documento de entrega al confirmar el pedido, reservas y entregas parciales; registro funcional de EV-14 y EV-15 sin presumir bloqueos nativos de Odoo.
* Momento de emisión en KeyFacil frente a pago total o entrega, documentación de traslado/guía de remisión según corresponda y requisitos fiscales aplicables; no asumir cumplimiento tributario por el modelo.
* Definir responsables y soporte documental de programación por viaje e incidencias; **[ND] acordar específicamente el procedimiento ante entrega rechazada/no conforme** (decisión, responsable y tratamiento físico/comercial), sin anticipar soluciones; validar propuestas de despacho al día siguiente y espera de 30 minutos en obra con los propietarios.
* **POL-07:** los adelantos parciales de clientes están inactivos en el escenario O2C ordinario; su eventual tratamiento excepcional queda **[ND]**, sin asumir prohibición general. Validar crédito autorizado, abonos parciales sobre obligaciones a crédito, cronogramas y vínculo de posventa con el pedido, sin suponer automatizaciones.
* Alcance del módulo contable de la comunidad ([ADR-003](../../arquitectura/decisiones/ADR-003-contabilidad.md)): qué registra el ERP y qué queda solo en KeyFacil.

## 10. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | César Joaquín | Versión inicial; reemplaza el caso de uso de ventas y lo alinea con KeyFacil como emisor, la exclusión de Compras y los perfiles del modelo de autorización |
| 0.2 | 23/10/2026 | Revisión de alineación | Precisa EV-14/EV-15, revalidación previa a confirmar, faltantes condicionados y momento de comprobante externo sin alterar el flujo POS |
| 0.3 (propuesta) | 23/10/2026 | Revisión documental | Explicita O2C integral, programación, traslado, conformidad, cobranza y rutas flexibles KeyFacil; revisión puntual: condiciones de pago consultadas antes de cotizar, responsabilidades de Comercial/ERP, no conformidad [ND] y cierre con pendientes |

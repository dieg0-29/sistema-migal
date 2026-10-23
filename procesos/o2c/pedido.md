# Pedido de cliente

**Estado:** diseño funcional; no acredita configuración de Ventas ni prueba de confirmación. Forma parte del [flujo O2C](flujo.md), pasos iniciales de solicitud, validación y confirmación.

## Propósito y frontera

Convertir una propuesta aceptada en un compromiso operativo reconstruible, mediante una solicitud de pedido (EV-14) y una respuesta de validación del ERP (EV-15). Una cotización enviada, una probabilidad alta, una conversación sin aceptación clara o una solicitud pendiente no son un pedido confirmado.

## Entradas y responsables

Cuenta/contacto, cotización y versión, productos/unidades/cantidades, precios y condiciones aceptadas (incluida la modalidad de pago consultada antes de cotizar y concretada en la negociación), soporte de aceptación, consulta previa de stock disponible y destino o datos de entrega cuando corresponda. R-ATN (Comercial) consulta y revalida el inventario mediante el sistema, prepara la solicitud, registra la aceptación del cliente y comunica la respuesta ERP; R-PRO autoriza excepciones; R-ALM apoya ante diferencias físicas y ejecuta la preparación y despacho del compromiso confirmado. La función logística, con persona aún por asignar, evalúa cobertura y viabilidad de fecha y coordina la programación por viajes. No exigir datos adicionales que no correspondan a la operación.

## Flujo propuesto

| Paso | Acción | Evidencia / condición |
|---|---|---|
| 1 | Identificar qué versión acepta el cliente y quién comunica | Versión y soporte; validar representación del contacto si procede (EV-11) |
| 2 | Revisar precios, modalidad y condiciones de pago de la versión aceptada y excepciones pendientes; registrar la solicitud de pedido (EV-14) | Condiciones negociadas y aceptadas, aprobaciones y referencia de solicitud; no redefinir el crédito ni confirmar sobre una oferta sustituida sin nuevo acuerdo |
| 3 | Comercial revalida en el sistema la disponibilidad por producto y ubicación y consulta la viabilidad logística antes de confirmar | Stock apto, reservado y **disponible**, demanda no cubierta, cantidades, destino, cobertura y fecha pendiente o viable; R-ALM interviene ante discrepancias físicas |
| 4 | Registrar la respuesta ERP (EV-15) y, solo si es aceptada, tramitar la confirmación operativa del pedido desde Comercial | Aceptación del cliente vinculada a la versión, respuesta aceptada/condicionada/rechazada con motivo, responsable y próxima acción; R-PRO autoriza excepciones. Una respuesta condicionada requiere resolver pendientes y revalidar |
| 5 | Tras la confirmación, trasladar a programación logística y preparación la información del pedido y de sus posibles viajes | Destino, contacto receptor, cantidades por viaje y ubicación, fecha prevista, responsables y pendientes; documento de salida y guía de remisión cuando corresponda, y constancia de entrega/conformidad posterior. No presumir reserva física completa ni emisión automática de documentos |
| 6 | Gestionar cambios posteriores que afecten condiciones, cantidades, destino o programación | Motivo, valor anterior/posterior, responsable, impacto en viajes y nueva aceptación si cambia lo acordado; conservar historial |

## Excepciones

Faltante: consultar alternativa, entrega parcial o reposición con decisión explícita, sin presumir RFQ automática. Para cemento, fierro, agregados y ladrillos puede mantenerse una condición de abastecimiento, sin reserva ficticia ni promesa firme de entrega. Para otros productos sin disponibilidad, mantener revisión antes de comprometer la entrega. Si existe diferencia entre el stock registrado y el físico, aplicar EXC-02.

Respuesta ERP condicionada: conservar pendientes, responsable y próxima acción hasta nueva validación; no confirmar automáticamente. Respuesta rechazada: registrar motivo y comunicarlo a Comercial. Entrega parcial o reprogramación: conservar cantidades y fechas por viaje, sin marcar la totalidad como entregada. Entrega no conforme: registrar evidencia y observaciones; el tratamiento posterior permanece **`[ND]`**, aún no definido por el equipo.

Crédito/descuento pendiente: mantener revisión. Cambio de cliente, cantidades o dirección: revisar impacto y conservar evidencia. Cancelación: comprobar movimientos, comprobantes y pagos existentes antes de cerrar; no borrar historia.

## Aceptación y pendientes

La prueba futura debe demostrar vínculo con versión aceptada, solicitud EV-14, respuesta EV-15, conservación de condiciones y efecto real de confirmar en el entorno. Verificar reservas, parciales, permisos, mapeo de campos y traspaso trazable a la programación por viajes (fecha, responsable, destino, cantidades, documento de traslado y constancia de recepción). La definición de la respuesta ante no conformidad queda **`[ND]`**. No afirmar bloqueos nativos, reservas completas o emisión automática de guías ni crear un objeto separado si la instancia conserva la transición en el mismo registro.

Referencias: [flujo O2C](flujo.md), [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md) (F-02, EV-11, EV-14 y EV-15), [catálogo de reglas](../catalogo-reglas.md) (RN-07, RN-08, RN-09, RN-15 y EXC-02), [ADR-004](../../arquitectura/decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [fuentes](../../fuentes/fuentes-negocio.md).

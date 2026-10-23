# Flujo de gestión de inventario

## 1. Metadatos

* **Fecha:** 23/10/2026
* **Versión:** 0.2 (propuesta documental)
* **Autores:** César Joaquín
* **Sistema:** ERP (Odoo 18 Community: Inventario)

## 2. Propósito

`[E][AS-IS]` Los ingresos y mermas se registran a mano, el stock no refleja las ventas de los distintos canales y no se sabe con certeza qué hay en cada punto físico. `[FN-01][FN-02]`

El flujo propuesto mantiene un registro único de movimientos por ubicación (Tienda, Almacén 1 y Almacén 2) que conecta las ventas de [O2C](../o2c/flujo.md) con las recepciones de [P2P](../p2p/flujo.md), sin ocultar diferencias físicas. En pedidos con despacho, distingue la salida física por viaje programado de la entrega y conformidad del cliente.

## 3. Inicio y fin

* **Inicio:** una recepción, una salida por venta, una transferencia entre ubicaciones o un conteo físico.
* **Fin:** movimiento validado con cantidades reales, o diferencia registrada y resuelta con autorización.

## 4. Actores

| Perfil | Participación |
|---|---|
| R-ALM | Registra recepciones, preparación, despachos, transferencias y conteos; verifica disponibilidad y diferencias físicas |
| R-PRO | Autoriza ajustes de inventario y decide ante faltantes |
| R-ATN | Origina salidas por venta de mostrador y consulta disponibilidad de todos los productos antes de cotizar |

## 5. Entradas y salidas

| Entradas | Salidas |
|---|---|
| Pedido confirmado y programación por viaje cuando corresponda; venta de mostrador, recepción de proveedor, conteo físico; consulta de inventario previa a cotización y solicitud de validación de pedido (EV-14) | Movimientos por ubicación, salidas vinculadas al pedido y viaje cuando corresponda, cantidades despachadas y pendientes; stock apto, reservado y disponible, demanda no cubierta y diferencias registradas; información para respuesta ERP (EV-15) |

## 6. Flujo

La **consulta de disponibilidad no es un movimiento de inventario**: antes de emitir cada cotización o nueva versión, R-ATN consulta todos sus productos por ubicación, con apoyo de R-ALM cuando exista incertidumbre. Después de la aceptación, el ERP revalida existencias y condiciones para responder a EV-14 mediante EV-15; una respuesta condicionada no equivale a pedido confirmado. En el modelo funcional, **stock disponible = máx(0, stock físico apto − stock reservado físicamente)**; la demanda no cubierta se registra por separado. Ver [parámetros de inventario](../../datos/maestros/parametros-inventario.md) y [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md).

La programación de viajes y la conformidad del receptor se documentan en [O2C](../o2c/flujo.md). Inventario registra la **preparación y salida física** con evidencia; no declara una entrega conforme solo por generar el movimiento. El tratamiento posterior de una no conformidad continúa `[ND]`.

| Paso | Movimiento | Actividad | Actor | Regla | Evidencia |
|---|---|---|---|---|---|
| 1 | Recepción | Contar la mercadería recibida y registrar la cantidad real y apta en la ubicación de destino, distinguiendo daños | R-ALM | RN-19 | Documento de entrada con cantidad real, daños y ubicación |
| 2 | Salida por pedido | Preparar y registrar la salida de cada viaje programado desde la ubicación validada (y reservada, si existe reserva efectiva); registrar cantidades realmente despachadas y pendientes, sin confundir salida con entrega conforme | R-ALM | RN-09, RN-10 | Documento de salida vinculado al pedido y viaje, con ubicación, fecha, responsable y cantidades; referencia de traslado/guía cuando corresponda, sin presumir emisión automática |
| 3 | Salida por punto de venta | Se propone descontar automáticamente el stock de la Tienda al vender; el momento y efecto real requieren prueba en Odoo | Sistema | RN-10 | Movimiento por venta `[S]` por validar en la instancia |
| 4 | Transferencia | Mover mercadería entre Tienda, Almacén 1 y Almacén 2 | R-ALM | RN-09 | Documento de transferencia con origen y destino |
| 5 | Conteo y ajuste | Contar, reportar la diferencia y regularizar solo con autorización de un propietario | R-ALM / R-PRO | RN-21, SOD-03 | Conteo, diferencia, motivo, autorización y movimiento de ajuste |

## 7. Excepciones

| Situación | Tratamiento | Regla |
|---|---|---|
| Stock registrado distinto del físico | No comprometer el faltante; reportar y ajustar con autorización | EXC-02, SOD-03 |
| Recepción parcial o dañada | Registrar lo real y mantener el saldo pendiente con el proveedor; no ajustar para ocultarlo | RN-20 |
| Faltante conocido para cotización o pedido | R-PRO evalúa parcial, sustituto o reposición documental por P2P; cemento, fierro, agregados y ladrillos pueden cotizarse sujetos a abastecimiento, **sin reserva ficticia ni fecha firme**. Para otras categorías, revisar antes de comprometer. No hay compra automática; no confundir este faltante conocido con EXC-02. | RN-05, RN-09, RN-16 |
| Salida parcial respecto del viaje programado | Registrar cantidad efectivamente despachada, diferencia y pendiente; comunicar el impacto para revisar la programación en O2C, sin declarar entregadas unidades no despachadas | RN-09, RN-10 |

## 8. Indicadores

Por definir. Los indicadores de recepción están en [P2P](../p2p/indicadores.md) (P2P-K-02).

## 9. Pendientes de validación

* Representación de Tienda, Almacén 1 y Almacén 2 como almacenes o ubicaciones en Odoo.
* Momento efectivo del descuento del punto de venta (ver [VER-02](../../arquitectura/versiones/VER-02-crm.md), CA-06).
* Valoración contable del inventario con el módulo de la comunidad ([ADR-003](../../arquitectura/decisiones/ADR-003-contabilidad.md)); no se da por hecho el asiento automático.
* Verificar disponibilidad por ubicación, reservas reales y tratamiento de demanda no cubierta; la consulta previa y EV-15 son controles funcionales propuestos, no automatizaciones acreditadas.
* Validar cómo vincular pedido, viaje programado, movimiento de salida y documentos de traslado en Odoo; la conformidad/no conformidad corresponde a O2C y su tratamiento posterior permanece `[ND]`.

## 10. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | César Joaquín | Versión inicial; reemplaza el caso de uso de inventario y lo alinea con los perfiles, las tres ubicaciones y la exclusión de Compras |
| 0.2 | 23/10/2026 | Revisión documental | Alineación puntual de disponibilidad, consulta previa, respuesta ERP y faltantes; sin pruebas de implementación |
| 0.3 (propuesta) | 23/10/2026 | Revisión documental, pendiente de aprobación del equipo | Vincula salida física con pedido y viaje programado; diferencia despacho, entrega conforme y saldo pendiente, sin asumir automatización |

# Supuestos, vacíos, propuestas y límites — MIGAL

## 1. Clasificación

Este documento registra los hechos, propuestas y aspectos pendientes que condicionan el diseño de la solución CRM–ERP para Ferre & Inversiones MIGAL.

- `[E]` **Evidencia:** información respaldada por fuentes del negocio.
- `[I]` **Interpretación:** conclusión razonada a partir de la evidencia.
- `[S]` **Supuesto:** condición provisional pendiente de confirmación.
- `[ND]` **No determinado:** aspecto sin información o decisión suficiente.
- `[AS-IS]` **Situación actual:** funcionamiento del negocio.
- `[TO-BE]` **Propuesta:** funcionamiento previsto en la solución.
- `[SIM]` **Simulado:** datos ficticios utilizados en casos académicos.

Las propuestas funcionales representan decisiones de diseño del equipo, pero no acreditan configuración ni pruebas ejecutadas en Odoo.

## 2. Realidad confirmada relevante

Se incluyen únicamente características del negocio que deben considerarse al modelar procesos, datos y responsabilidades en Odoo.

| Realidad de MIGAL                                                                                      | Estado       | Implicación para el ERP–CRM                                                                                     |
| ------------------------------------------------------------------------------------------------------ | ------------ | --------------------------------------------------------------------------------------------------------------- |
| Opera una tienda física principal y dos almacenes.                                                     | `[E][AS-IS]` | Representar Tienda, Almacén 1 y Almacén 2 para consultar existencias y registrar movimientos por ubicación.     |
| Cuenta con dos propietarios y dos trabajadores.                                                        | `[E][AS-IS]` | Definir perfiles funcionales sin inventar puestos y controlar las autorizaciones sensibles.                     |
| Atiende ventas de mostrador y pedidos de materiales para obras.                                        | `[E][AS-IS]` | Diferenciar el flujo del Punto de Venta del recorrido comercial que requiere cotización y pedido.               |
| El dueño de obra y el maestro pueden participar en una misma operación comercial.                      | `[E][AS-IS]` | Relacionar cuenta, contactos, sus roles y dirección de obra sin duplicar clientes.                              |
| Recibe solicitudes presencialmente, por WhatsApp, llamadas y referidos.                                | `[E][AS-IS]` | Registrar el canal y la fuente de cada consulta, sin asumir integración automática con WhatsApp.                |
| Los precios y descuentos dependen del producto, los costos, la competencia y los propietarios.         | `[E][AS-IS]` | Mantener precios identificables y registrar autorizaciones de excepciones comerciales.                          |
| Las cotizaciones pueden modificarse por cambios de precios o condiciones antes de concretar la compra. | `[E][AS-IS]` | Conservar las versiones y condiciones aceptadas, evitando sobrescribir acuerdos previos.                        |
| El inventario y la información comercial se mantienen en registros no integrados.                      | `[E][AS-IS]` | Centralizar productos, existencias, clientes y operaciones para reducir inconsistencias.                        |
| Las entregas dependen de cantidades, ubicación, proveedores y recursos de transporte.                  | `[E][AS-IS]` | Representar programación, cantidades por despacho y pendientes, sin asumir automatización logística.            |
| KeyFacil es utilizado por los propietarios para emitir comprobantes electrónicos.                      | `[E][AS-IS]` | Conservar KeyFacil como emisor externo y vincular documentalmente sus comprobantes con las operaciones del ERP. |

## 3. Propuestas del equipo

Esta sección reúne las decisiones funcionales adoptadas por el equipo para el modelo futuro de MIGAL. Su inclusión no significa que hayan sido implementadas o probadas en Odoo.

| Propuesta funcional                                                                                    | Estado                            | Qué debe contemplar el modelo                                                                                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maestro único de cuentas, contactos y obras.                                                           | `[TO-BE]`                         | Evitar duplicidades y relacionar al cliente con los participantes de cada operación.                                                                                                                                                        |
| Gestión y seguimiento del ciclo comercial desde la necesidad del cliente hasta la cotización aceptada. | `[TO-BE]`                         | Registrar la necesidad comercial y dar seguimiento a lead, lead calificado, oportunidad y cotización, identificando etapa comercial, responsable, próxima acción con fecha y versiones de cotización cuando correspondan.                   |
| Consulta de disponibilidad antes de cotizar y al validar pedidos.                                      | `[TO-BE]`                         | Consultar existencias por ubicación, distinguiendo stock físico apto, reservado y disponible, sin generar movimientos por una simple consulta.                                                                                              |
| Solicitud, validación y confirmación del pedido.                                                       | `[TO-BE]`                         | Conservar EV-11, EV-14 y EV-15, con condiciones y decisiones documentadas. Solo una respuesta ERP aceptada habilita la confirmación operativa del pedido.                                                                                   |
| Reserva de inventario al confirmar pedidos.                                                            | `[TO-BE]`                         | Gestionar las reservas al confirmar el pedido, según la disponibilidad y configuración de Odoo, sin considerar una reserva como salida física ni presumir que siempre será completa.                                                        |
| Ingreso de mercadería mediante órdenes de recepción.                                                   | `[TO-BE]`                         | Registrar toda incorporación de mercadería por abastecimiento mediante una orden de recepción. El stock aumenta únicamente cuando se confirma y valida la recepción física, registrando producto, cantidad, ubicación, fecha y responsable. |
| Atención de pedidos con faltantes.                                                                     | `[TO-BE]`                         | Identificar la demanda no cubierta, gestionar el abastecimiento y actualizar existencias mediante la recepción validada de los productos faltantes antes de realizar las salidas correspondientes.                                          |
| Salidas de inventario por ventas.                                                                      | `[TO-BE]`                         | Reducir las existencias mediante el movimiento correspondiente a una orden de entrega validada o a una venta procesada en el Punto de Venta.                                                                                                |
| Ajustes por deterioro, retiro, merma o pérdida.                                                        | `[TO-BE]`                         | Registrar disminuciones de inventario mediante movimientos o ajustes sustentados, indicando motivo, cantidad, ubicación, responsable y autorización correspondiente, sin tratarlas como ventas.                                             |
| Transferencias internas de inventario.                                                                 | `[TO-BE]`                         | Registrar movimientos entre Tienda, Almacén 1 y Almacén 2, actualizando las existencias por ubicación sin modificar el stock total de MIGAL.                                                                                                |
| Programación logística de pedidos confirmados.                                                         | `[TO-BE]`                         | Programar despachos desde el día siguiente a la confirmación del pedido, considerando disponibilidad de materiales y recursos logísticos.                                                                                                   |
| Tiempo máximo de espera en obra.                                                                       | `[TO-BE]` Pendiente de aprobación | Proponer una espera máxima de 30 minutos durante la entrega en obra, sujeta a aprobación y definición de excepciones.                                                                                                                       |
| Registro de entregas vinculadas al pedido.                                                             | `[TO-BE]`                         | Identificar viajes, cantidades despachadas, cantidades pendientes y conformidad del cliente, distinguiendo la salida física de la recepción conforme.                                                                                       |
| Abastecimiento documental sin módulo de Compras.                                                       | `[TO-BE]`                         | Relacionar necesidad, autorización, proveedor, recepción y pago sin asumir generación automática de solicitudes de cotización al proveedor.                                                                                                 |
| Registro y conciliación de comprobantes de KeyFacil.                                                   | `[TO-BE]`                         | Conservar tipo, serie, número, importe y vínculo con el pedido.                                                                                                                                                                             |
| Control de cobros y créditos.                                                                          | `[TO-BE]`                         | Registrar condiciones autorizadas, pagos, vencimientos y saldos.                                                                                                                                                                            |
| Autorización y trazabilidad por función.                                                               | `[TO-BE]`                         | Identificar quién solicita, aprueba, ejecuta o modifica operaciones sensibles.                                                                                                                                                              |

## 4. Supuestos y vacíos abiertos

Esta sección incluye exclusivamente aspectos que todavía requieren información, definición o confirmación. No se repiten las decisiones funcionales ya establecidas en la sección 3.

| Aspecto pendiente                                | Estado | Decisión o verificación necesaria                                                                                                                                                                                   |
| ------------------------------------------------ | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Configuración de Tienda, Almacén 1 y Almacén 2.  | `[ND]` | Determinar si se representarán técnicamente como almacenes o ubicaciones de Odoo.                                                                                                                                   |
| Permisos efectivos por usuario.                  | `[ND]` | Determinar cómo se asignarán los grupos y autorizaciones de Odoo a los dos propietarios y dos trabajadores, respetando la segregación de funciones.                                                                 |
| Política de crédito y descuentos.                | `[ND]` | Confirmar las condiciones y los límites autorizados por los propietarios, sin inventar porcentajes ni plazos.                                                                                                       |
| Compromisos de entrega sujetos a abastecimiento. | `[ND]` | Definir cuándo puede asegurarse al cliente una fecha de entrega si parte de los productos aún depende de la recepción del proveedor.                                                                                |
| Excepciones al tiempo de espera en obra.         | `[ND]` | Definir, si se aprueba la propuesta de 30 minutos, cómo se gestionarán las demoras y quién autorizará las excepciones.                                                                                              |
| Entregas no conformes.                           | `[ND]` | Definir el tratamiento de diferencias de cantidades, rechazos, devoluciones y responsabilidades.                                                                                                                    |
| Uso de Facturación de Odoo frente a KeyFacil.    | `[ND]` | Definir los controles para evitar duplicidad de comprobantes y el procedimiento de conciliación.                                                                                                                    |
| Extensión contable comunitaria.                  | `[ND]` | Determinar si se requiere una extensión adicional y cómo se relacionaría con los módulos obligatorios de Odoo.                                                                                                      |
| Viabilidad técnica de las propuestas en Odoo.    | `[ND]` | Verificar cuáles de los eventos, reservas, movimientos, controles y autorizaciones previstos pueden realizarse con funcionalidades nativas y cuáles requieren configuración, procedimientos o desarrollo adicional. |

## 5. Correcciones a supuestos de documentos previos

No deben tratarse como características confirmadas de MIGAL ni como automatizaciones implementadas:

- Descuento máximo fijo del `5 %`.
- Vigencia universal de cotización de `3 días`.
- DNI/RUC obligatorio para cualquier consulta o cotización.
- Cargos ficticios ajenos a la estructura real de MIGAL.
- Cotización aceptada equivalente a pedido automáticamente confirmado.
- Consulta de stock equivalente a reserva efectiva.
- Confirmación del pedido equivalente a salida física o entrega conforme.
- Incremento de stock por registrar una compra sin recepción validada.
- Salidas por deterioro, retiro, merma o pérdida tratadas como ventas.
- Descuento instantáneo de stock en POS sin prueba funcional.
- Emisión fiscal automática desde Odoo o integración automática con KeyFacil.
- Solicitudes automáticas de compra sin el módulo correspondiente.
- Presentar la espera máxima de 30 minutos en obra como política aprobada.

## 6. Alcance

El alcance vigente está definido en [alcance de la solución](../arquitectura/base/alcance.md).

- **Plataforma propuesta:** Odoo 18 Community, con CRM y ERP en una misma instancia.
- **ERP:** Ventas, Punto de Venta, Inventario y Facturación, considerando sus dependencias obligatorias.
- **CRM:** gestión comercial desde el registro de la necesidad del cliente, pasando por lead, lead calificado, oportunidad y cotización, hasta la aceptación y solicitud de validación.
- **O2C (Order to Cash):** recorrido integral desde la identificación y registro de la necesidad comercial del cliente, su atención mediante CRM (lead, lead calificado, oportunidad, cotización y aceptación), solicitud de validación, confirmación del pedido en ERP, reserva de inventario, programación y ejecución de entregas, emisión y conciliación de comprobantes mediante KeyFacil, gestión de cobros y cierre de la operación.
- **Inventario:** recepción validada de mercadería, reservas al confirmar pedidos, salidas por venta, transferencias y ajustes sustentados por diferencias físicas, deterioros, retiros o pérdidas.
- **P2P:** abastecimiento documental, sin incluir el módulo de Compras.
- **KeyFacil:** emisión externa de comprobantes; sin integración automática aprobada.

## 7. Infraestructura operativa

`[E][AS-IS]` Los recursos físicos identificados son:

| Recurso            | Cantidad | Consideración en el modelo                                                   |
| ------------------ | -------: | ---------------------------------------------------------------------------- |
| Tienda principal   |        1 | Punto de atención, venta de mostrador y ubicación de existencias.            |
| Almacenes          |        2 | Ubicaciones físicas para entradas, salidas, transferencias y disponibilidad. |
| Camiones propios   |        2 | Recursos para programar entregas de materiales.                              |
| Montacargas        |        1 | Recurso operativo para manipulación y carga.                                 |
| Vehículo eléctrico |        1 | Recurso para entregas pequeñas.                                              |

La representación de los recursos de transporte y carga es funcional y logística; no implica que se haya instalado o configurado un módulo de flota.

Esta información debe mantenerse alineada con el contexto empresarial, el modelo de inventario y el recorrido O2C.

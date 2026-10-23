# INT-01: CRM y ERP

## 1. Metadatos

* **Fecha:** 23/10/2026
* **Versión:** 0.2
* **Autores:** Gabriel Martínez
* **Sistemas involucrados:** CRM (atención, oportunidades y ventas), ERP (almacén, abastecimiento y contabilidad), KeyFacil (externo)

## 2. Propósito

Este documento identifica los eventos del proceso comercial de Ferre & Inversiones MIGAL, desde la consulta inicial hasta la cotización aceptada, y el punto en que esa información pasa del CRM al ERP.

La regla principal es que el CRM conserva el contexto y el compromiso comercial, luego el ERP recibe las condiciones aceptadas y gestiona la ejecución operativa.

En este documento, "evento" significa un suceso del proceso de negocio, no un mensaje técnico, una API ni un sistema de mensajería.

## 3. Alcance

### 3.1. Incluido

* Gestión de campañas
* Consulta inicial del cliente
* Registro del lead
* Calificación de la oportunidad
* Elaboración de la cotización y excepciones de precio

### 3.2. Excluido

* Interfaz técnica con KeyFacil, será solo registro y conciliación manual.
* Un conector o API entre instancias: este documento define un contrato de negocio, no una interfaz técnica.

## 4. Eventos de negocio

| ID | Evento | Disparador | Actor que registra | Sistema emisor | Sistema receptor | Regla o política | Datos necesarios |
|---|---|---|---|---|---|---|---|
| EV-01 | Campaña iniciada | Iniciativa identificable de MIGAL o de un proveedor (p. ej. material de Progresol, actividad comunitaria, fidelización navideña) | R-PRO | CRM | CRM | Solo se registran campañas identificables; no se inventan campañas | Nombre, objetivo, periodo, responsable |
| EV-02 | Consulta recibida | Contacto del cliente o maestro de obra por mostrador, WhatsApp o teléfono | R-ATN | Canal de atención | CRM | La campaña se registra solo si causó la consulta | Canal, fuente (referido maestro, referido cliente, cliente frecuente, directo), campaña si aplica, necesidad inicial |
| EV-03 | Lead registrado | EV-02 | R-ATN | CRM | CRM | [RN-01](../../procesos/catalogo-reglas.md): no duplicar leads por celular o nombre; no se exige DNI/RUC | Nombre o alias, teléfono, persona que contactó, cliente responsable y maestro de obra si se conocen, responsable de seguimiento |
| EV-04 | Lead descartado | Contacto inválido | R-ATN | CRM | CRM | Se archiva sin crear cuenta de cliente | Motivo de descarte |
| EV-05 | Oportunidad calificada | Necesidad concreta, roles y cuenta/contacto identificados | R-ATN | CRM | CRM | [RN-02](../../procesos/catalogo-reglas.md): toda oportunidad tiene una próxima acción | Cuenta y contacto, rol (dueño o maestro de obra), obra y dirección, productos o necesidad material, condiciones de entrega y pago consultadas, probabilidad (baja, media, alta), referencia competitiva, próxima acción, fecha y responsable |
| EV-06 | Cotización elaborada | Oportunidad calificada que solicita precios | R-ATN | CRM | CRM | [RN-03](../../procesos/catalogo-reglas.md): precios de la lista vigente, sujetos a actualización hasta la confirmación o pago. [RN-06](../../procesos/catalogo-reglas.md): registrar comprobante previsto | Productos, unidades, cantidades, precio de referencia, moneda, versión, flete, medio de pago, lugar de entrega, vigencia y comprobante previsto (boleta, factura, por definir) |
| EV-07 | Disponibilidad prometida | Cliente consulta disponibilidad o fecha de entrega | R-ATN o personal autorizado según la promesa | CRM | CRM | [RN-05](../../procesos/catalogo-reglas.md): solo personal autorizado confirma disponibilidad de materiales críticos | Producto, cantidad, fecha o condición prometida, responsable que confirmó, fundamento, estado (firme, condicionado o pendiente) |
| EV-08 | Excepción de precio solicitada | Cliente o maestro presenta un precio menor de la competencia | R-ATN | CRM | CRM | [EXC-01](../../procesos/catalogo-reglas.md): la decisión recae en los propietarios | Línea afectada, cantidad, precio de la competencia, competidor si se conoce |
| EV-09 | Excepción de precio resuelta | EV-08 | R-PRO | CRM | CRM | No existe un porcentaje máximo formal; considera cantidad, relación con el cliente, mezcla de productos y reparto | Precio de referencia, precio ofrecido, diferencia, motivo, autorizador, fecha |
| EV-10 | Cotización versionada | Cambio de cantidades, productos o precios tras enviar la proforma | R-ATN | CRM | CRM | [RN-04](../../procesos/catalogo-reglas.md): se crea una nueva versión sin eliminar la anterior | Versión anterior, versión nueva, motivo del cambio |
| EV-11 | Cotización aceptada | Respuesta del cliente | R-ATN | CRM | ERP | [RN-08](../../procesos/catalogo-reglas.md): paso al ERP solo con identidad suficiente, oferta identificable y aceptación registrada | Versión aceptada, quién aceptó, fecha, soporte o evidencia, condiciones acordadas, DNI/RUC si el comprobante lo requiere |
| EV-12 | Oportunidad cerrada sin venta total | Resultado de la cotización | R-ATN | CRM | CRM | El motivo es obligatorio si se pierde | Resultado (ganada parcial o perdida), motivo de no adjudicación, competidor si se conoce |
| EV-13 | Campaña cerrada | Fin del periodo de la campaña | R-PRO | CRM | CRM | — | Fecha de fin, leads atribuidos, resultado |
| EV-14 | Solicitud de pedido | EV-11 y condiciones mínimas cumplidas | R-ATN | CRM | ERP | La aceptación del cliente habilita la solicitud de validación, pero no confirma automáticamente el pedido | ID de correlación, oportunidad y cotización/versión aceptada, cuenta/contacto, líneas, destino, fechas, medio de pago, aprobaciones, stock consultado y pendientes |
| EV-15 | Respuesta ERP | Verificación de EV-14 | Responsable autorizado del ERP | ERP | CRM | La respuesta debe indicar fundamento, responsable y fecha; no se asumen bloqueos nativos de Odoo sin prueba | ID de solicitud y pedido si existe, estado (aceptada, rechazada o condicionada), motivo, reserva o déficit, condición, responsable y fecha |

EV-05 puede desglosarse funcionalmente en dos momentos: **EV-05A Calificación**, cuando existe una lista o necesidad material concreta, y **EV-05B Oportunidad**, cuando ya se exploraron condiciones y existe continuidad comercial. Este desglose es una ampliación propuesta y no modifica la numeración base.

## 5. Fronteras de información

### F-01: Canales de atención a CRM

* **Eventos activadores:** EV-02
* **Descripción:** Los canales aportan la información inicial. Se registran solo los datos necesarios para identificar y dar seguimiento a la consulta.
* **Mecanismo actual:** Procedimiento manual
* **Validaciones requeridas:**
  * Buscar contactos existentes por teléfono, nombre o documento antes de crear uno.
  * No crear una ficha nueva por cada canal de atención.
  * Registrar la relación con el maestro de obra como dato opcional.

### F-02: CRM a ERP

* **Eventos activadores:** EV-11
* **Descripción:** La cotización aceptada deja de ser un compromiso solo comercial y pasa al ERP como pedido, donde se gestionan disponibilidad, reserva, entrega, comprobante y cobro.
* **Mecanismo actual:** Estándar del sistema (Odoo Community) — por validar
* **Validaciones requeridas:**
  * Debe conocerse a quién se atribuye la operación y quién es el contacto responsable.
  * Debe existir una cotización concreta con versión, productos, unidades, cantidades, precios y descuentos aplicables.
  * Toda promesa de disponibilidad o fecha debe indicar si está confirmada, pendiente o condicionada.
  * Debe registrarse quién aceptó, qué versión, cuándo y bajo qué condiciones.
  * Los descuentos y condiciones excepcionales deben tener un autorizador registrado.
  * Cualquier diferencia entre lo cotizado y lo pedido se registra como cambio, no se corrige en silencio.
  * La cotización y la versión aceptada deben ser identificables; sus líneas, moneda, cantidades, precios y condiciones no se alteran sin una nueva aceptación.
  * Los descuentos excepcionales y el crédito deben contar con autorización expresa. La fórmula del recargo a crédito queda pendiente de definición.
  * Deben verificarse destino, fecha comunicada, flete y viabilidad logística. El despacho programado considera como mínimo el día siguiente, sujeto a ratificar cómo se computa.
  * Debe consultarse el stock por ubicación y registrarse la demanda no cubierta cuando corresponda. Las categorías de faltante pueden tratarse mediante una compra vinculada, sin afirmar que exista una reserva.
  * La respuesta debe tener un estado claro, fundamento y responsable; no se genera un segundo pedido por diseño ni se inventa un endpoint.

## 6. Supuestos y restricciones

* El CRM incluye la gestión comercial hasta la cotización aceptada; el ERP incluye ventas (desde el pedido confirmado), almacén, abastecimiento y contabilidad.
* Los actores se identifican con los perfiles del [modelo de autorización](../seguridad/modelo-autorizacion.md): R-ATN (atención comercial) y R-PRO (propietario aprobador).
* La plataforma común es Odoo Community ([ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md)); KeyFacil se mantiene como sistema externo operado por los propietarios.
* No se exige DNI/RUC para registrar un lead ni para cotizar; se solicita solo cuando la operación o el comprobante lo requieren.
* No se afirma que Odoo bloquee automáticamente descuentos, precios o faltantes sin una prueba en la instancia concreta.
* El inventario está distribuido en tres puntos físicos: Tienda, Almacén 1 y Almacén 2.
* El CRM conserva la identidad y el contexto del cliente, la cotización con sus versiones, las promesas comunicadas y la aceptación. El ERP conserva el pedido, la reserva, la entrega, la referencia del comprobante y el cobro. El CRM no afirma que algo esté reservado, entregado o cobrado; el ERP no pierde la versión aceptada ni sus condiciones.
* La información crece con el compromiso: para una consulta o cotización no se exigen DNI/RUC, datos fiscales completos, reserva de stock, promesas de entrega sin confirmación ni capturas de cada interacción; tampoco se duplica en el ERP lo que ya tiene referencia en el CRM.

## 7. Riesgos

| ID | Riesgo | Impacto | Mitigación |
|---|---|---|---|
| R-01 | Contactos duplicados por atención multicanal | Medio | Búsqueda previa obligatoria ([RN-01](../../procesos/catalogo-reglas.md)) |
| R-02 | Promesas de disponibilidad basadas en stock registrado distinto del físico | Alto | Confirmación por personal autorizado ([RN-05](../../procesos/catalogo-reglas.md), [EXC-02](../../procesos/catalogo-reglas.md)) |
| R-03 | Suponer controles automáticos que Odoo no aplica | Alto | Probar en la instancia antes de declararlos |

Seguimiento en el [registro de riesgos](../base/riesgos-y-controles.md): R-01 es RSK-06, R-02 es RSK-02 y R-03 es RSK-07.

## 8. Pendientes de validación

| ID | Pendiente | Frontera |
|---|---|---|
| P-01 | Definir los campos obligatorios para convertir una cotización aceptada en pedido | F-02 |
| P-02 | Definir cómo se registra la aceptación del cliente y qué evidencia se conserva | F-02 |
| P-03 | Probar en Odoo la conversión de oportunidad a cotización y de cotización a pedido | F-02 |
| P-04 | Verificar el comportamiento ante stock insuficiente o entrega parcial | F-02 |
| P-05 | Revisar los permisos disponibles para precios y descuentos | F-02 |
| P-06 | Verificar el control de duplicados de contactos en Odoo | F-01 |
| P-07 | Verificar en Odoo 18 Community los estados reales de cotización y pedido, el enlace con la oportunidad (`sale_crm`), la confirmación y los permisos | F-02 |
| P-08 | Definir el registro de EV-14 y EV-15, incluyendo correlación, respuesta condicionada, déficit y referencia de pedido | F-02 |
| P-09 | Ratificar el cómputo del despacho programado mínimo al día siguiente y definir el recargo a crédito | F-02 |

## 9. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | Gabriel Martínez | Versión inicial |
| 0.2 | 22/10/2026 | Gabriel Martínez | Ampliación funcional: solicitud y respuesta ERP, validaciones y datos mínimos |

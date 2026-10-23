# INT-01: CRM y ERP

## 1. Metadatos

* **Fecha:** 23/10/2026
* **Versión:** 0.6 (propuesta de alineación; pendiente de revisión del equipo)
* **Sistemas involucrados:** Contactos (maestro compartido de personas y empresas), CRM (atención, oportunidades y ventas), ERP (almacén, abastecimiento y contabilidad), KeyFacil (externo)

## 2. Propósito

Este documento identifica los eventos del proceso comercial de Ferre & Inversiones MIGAL, desde la consulta inicial hasta la cotización aceptada, y el punto en que esa información pasa del CRM al ERP.

La regla principal es que el CRM conserva el contexto y el compromiso comercial, luego el ERP recibe las condiciones aceptadas y gestiona la ejecución operativa.

En este documento, "evento" significa un suceso del proceso de negocio, no un mensaje técnico, una API ni un sistema de mensajería.

## 3. Alcance

### 3.1. Incluido

* Gestión de contactos vinculada al CRM, reutilizando la ficha existente para nuevas necesidades comerciales
* Gestión propuesta de campañas propias de MIGAL; actualmente no se realizan campañas
* Consulta inicial del cliente
* Registro del lead
* Calificación del lead y conversión a oportunidad con criterios explícitos
* Elaboración de la cotización y excepciones de precio
* Consulta de disponibilidad de todos los productos antes de emitir cada cotización o versión
* Aceptación documentada, solicitud de pedido y respuesta operativa del ERP (EV-11, EV-14 y EV-15)

### 3.2. Excluido

* Interfaz técnica con KeyFacil, será solo registro y conciliación manual.
* Un conector o API entre instancias: este documento define un contrato de negocio, no una interfaz técnica.

## 4. Eventos de negocio

| ID | Evento | Disparador | Actor que registra | Sistema emisor | Sistema receptor | Regla o política | Datos necesarios |
|---|---|---|---|---|---|---|---|
| EV-01 | Campaña iniciada | Inicio efectivo de una campaña propia aprobada por MIGAL; funcionalidad propuesta, sin campañas actuales | R-PRO | CRM | CRM | Solo se registran campañas propias efectivamente iniciadas; una propuesta aún no ejecutada no activa EV-01 | Nombre, objetivo, periodo, responsable |
| EV-02 | Consulta recibida | Contacto del cliente o maestro de obra por mostrador, WhatsApp o teléfono | R-ATN | Canal de atención | CRM | La campaña se registra solo si causó la consulta | Canal, fuente (referido maestro, referido cliente, cliente frecuente, directo), campaña si aplica, necesidad inicial |
| EV-03 | Lead registrado | EV-02 | R-ATN | CRM | CRM | [RN-01](../../procesos/catalogo-reglas.md): buscar y reutilizar el contacto existente; evitar duplicar la misma necesidad comercial, no limitar los leads por contacto. Una necesidad nueva puede generar otro lead del mismo cliente; nombre o celular coincidente no basta para fusionar registros. No se exige DNI/RUC | Nombre o alias, teléfono, persona que contactó, cliente responsable y maestro de obra si se conocen, responsable de seguimiento |
| EV-04 | Lead descartado | Registro improcedente comprobado (p. ej., spam o consulta ficticia), o datos erróneos que impiden el seguimiento tras intentar aclararlos y sin otro canal disponible | R-ATN | CRM | CRM | Registrar el motivo y archivar el lead; no eliminar ni invalidar el contacto existente. La falta de DNI/RUC, un teléfono ausente o la falta temporal de respuesta no bastan para descartarlo | Motivo, comprobación realizada y responsable |
| EV-05 | Calificación del lead y conversión a oportunidad | EV-05A: necesidad material concreta identificada; EV-05B: condiciones exploradas y continuidad comercial identificada | R-ATN | CRM | CRM | [RN-02](../../procesos/catalogo-reglas.md): distinguir ambos momentos y registrar próxima acción; ver criterios de transición debajo de la tabla | EV-05A: necesidad o lista de materiales y datos disponibles. EV-05B: cuenta/contacto, roles pertinentes, obra o destino si aplica, condiciones consultadas, probabilidad, referencia competitiva si existe, próxima acción, fecha y responsable |
| EV-06 | Cotización elaborada | Cliente solicita una cotización o acepta que MIGAL se la prepare al ofrecérsela, con oportunidad identificada y datos suficientes para cotizar | R-ATN | CRM | CRM | [RN-03](../../procesos/catalogo-reglas.md): precios de la lista vigente, sujetos a actualización hasta la confirmación o pago. [RN-06](../../procesos/catalogo-reglas.md): registrar comprobante previsto. [POL-09](../../procesos/politicas-operativas.md): consultar disponibilidad de **todos los productos antes de emitir** la cotización, sin confundir consulta con reserva. | Solicitud del cliente o aceptación de la oferta de preparar cotización, contacto vinculado, productos, unidades, cantidades, precio de referencia, moneda, versión, flete, medio de pago, lugar de entrega si aplica, vigencia, comprobante previsto y referencia de la consulta de stock por ubicación |
| EV-07 | Disponibilidad prometida | Cliente consulta disponibilidad o fecha de entrega | R-ATN o personal autorizado según la promesa | CRM | CRM | [RN-05](../../procesos/catalogo-reglas.md): solo personal autorizado confirma disponibilidad de materiales críticos | Producto, cantidad, fecha o condición prometida, responsable que confirmó, fundamento, estado (firme, condicionado o pendiente) |
| EV-08 | Excepción de precio solicitada | Cliente o maestro presenta un precio menor de la competencia | R-ATN | CRM | CRM | [EXC-01](../../procesos/catalogo-reglas.md): la decisión recae en los propietarios | Línea afectada, cantidad, precio de la competencia, competidor si se conoce |
| EV-09 | Excepción de precio resuelta | EV-08 | R-PRO | CRM | CRM | No existe un porcentaje máximo formal; considera cantidad, relación con el cliente, mezcla de productos y reparto | Precio de referencia, precio ofrecido, diferencia, motivo, autorizador, fecha |
| EV-10 | Cotización versionada | Cambio de cantidades, productos o precios tras enviar la proforma | R-ATN | CRM | CRM | [RN-04](../../procesos/catalogo-reglas.md): se crea una nueva versión sin eliminar la anterior; [POL-09](../../procesos/politicas-operativas.md): consultar nuevamente el stock de todos los productos de la nueva versión antes de emitirla | Versión anterior, versión nueva, motivo del cambio y referencia de nueva consulta de stock |
| EV-11 | Cotización aceptada | Respuesta del cliente | R-ATN | CRM | CRM | [RN-08](../../procesos/catalogo-reglas.md): registrar la versión aceptada; habilita EV-14, pero no confirma por sí misma el pedido | Versión aceptada, quién aceptó, fecha, soporte o evidencia, condiciones acordadas, DNI/RUC si el comprobante lo requiere |
| EV-12 | Oportunidad cerrada sin venta total | Cierre de la oportunidad o resultado de la cotización | R-ATN | CRM | CRM | Al cerrar una oportunidad como perdida o registrar la no adjudicación total o parcial de una cotización, registrar el motivo cuando se conoce o indicar «Motivo desconocido». «Sin respuesta» describe el seguimiento observado y no permite atribuir otra causa sin evidencia | Resultado comercial y oportunidad/cotización o versión afectada, motivo conocido o «Motivo desconocido», competidor solo si se conoce |
| EV-13 | Campaña cerrada | Fin del periodo de una campaña propia de MIGAL efectivamente iniciada | R-PRO | CRM | CRM | — | Fecha de fin, leads atribuidos, resultado |
| EV-14 | Solicitud de pedido | EV-11 y condiciones mínimas cumplidas | R-ATN | CRM | ERP | La aceptación del cliente habilita la solicitud de validación, pero no confirma automáticamente el pedido | ID de correlación, oportunidad y cotización/versión aceptada, cuenta/contacto, líneas, destino, fechas, medio de pago, aprobaciones, stock consultado y pendientes |
| EV-15 | Respuesta ERP | Verificación de EV-14 | Responsable autorizado del ERP | ERP | CRM | La respuesta debe indicar fundamento, responsable y fecha; no se asumen bloqueos nativos de Odoo sin prueba | ID de solicitud y pedido si existe, estado (aceptada, rechazada o condicionada), motivo, reserva o déficit, condición, responsable y fecha |

### 4.1. Contactos y criterios de transición comercial

**Contactos** mantiene la identidad de personas y empresas; el **lead** registra una necesidad comercial, y la **oportunidad** su avance comercial. Para el diseño de MIGAL, la gestión de contactos forma parte necesaria del flujo integrado. Buscar y reutilizar una ficha existente no impide registrar nuevos leads u oportunidades del mismo cliente. Antes de emitir una cotización se vincula el cliente correspondiente, creando su ficha solo si no existe. Esto no implica exigir una ficha completa ni DNI/RUC para registrar una consulta inicial; tampoco afirma que Odoo exija un cliente vinculado para todo lead.

| Transición | Criterio de avance | Registro mínimo |
|---|---|---|
| Consulta → Lead (EV-03) | Se registra la nueva necesidad o interés recibido para su seguimiento, aunque provenga de un cliente antiguo | Necesidad inicial, canal, persona que consulta, contacto existente si se identifica y responsable |
| Lead → Lead calificado (EV-05A) | Se identifica una necesidad material concreta o una lista de productos; no basta con tener los datos del contacto | Necesidad o lista, cantidades aproximadas si se conocen y siguiente acción |
| Lead calificado → Oportunidad (EV-05B) | Se exploran las condiciones pertinentes (productos, entrega, pago y participantes) y existe continuidad comercial para atender la necesidad | Contacto/cuenta identificados, condiciones conocidas y pendientes, próxima acción y responsable; no se obliga a completar datos que no aplican |
| Oportunidad → Cotización (EV-06) | El cliente solicita que se le cotice o acepta la propuesta de MIGAL de prepararle una cotización; se dispone de productos y cantidades para elaborarla | Registrar esa solicitud o aceptación, vincular el cliente y consultar disponibilidad de todos los productos antes de emitir |

EV-05A y EV-05B desarrollan el evento EV-05 sin renumerar los demás eventos. **Lead, Lead calificado, Oportunidad y Cotización son las etapas comerciales propuestas por el equipo para MIGAL.** El avance entre ellas se determina por los criterios de la tabla y no exige crear un registro independiente por cada etapa. La elaboración de la cotización conserva su documento vinculado a la oportunidad.

**Ubicación según la información disponible.** Al recibir una consulta o retomar su atención, R-ATN revisa y mapea la información de la necesidad comercial: qué datos ya se tienen, cuáles faltan y qué criterios de etapa están cumplidos. Se aprovecha lo aportado por el cliente y la información vigente ya registrada, comprobando que corresponda a esta compra. Con ello se ubica la necesidad en la etapa que realmente le corresponde y se define la siguiente acción, sin exigir que permanezca en etapas cuyos criterios ya cumple. Tener una ficha de contacto previa facilita la identificación, pero la etapa depende de la información y del avance de la necesidad actual.

Por ejemplo, si un cliente conocido llega con productos y cantidades definidos y solicita una cotización, se reutiliza su contacto, se completan las condiciones pertinentes y se puede avanzar a Cotización en la misma atención, previa consulta de disponibilidad antes de emitirla. Se deja constancia de los criterios cumplidos, sin repetir preguntas ni crear leads duplicados.

Aceptar que se prepare una cotización **no es aceptar sus precios y condiciones**: esa segunda aceptación corresponde a EV-11, una vez emitida la versión que el cliente acepta.

Un mismo cliente puede tener varias necesidades, leads y oportunidades. Si vuelve por la misma cotización, se continúa el seguimiento existente; si solicita materiales para otra compra u obra, se registra la nueva necesidad vinculada al mismo contacto. Una coincidencia de nombre o teléfono solo motiva revisión de identidad y contexto, nunca una fusión automática.

La consulta universal de inventario se realiza antes de EV-06 y de cada EV-10; EV-07 registra una **promesa comunicada** al cliente, no sustituye aquella consulta.

## 5. Fronteras de información

### F-01: Canales de atención a CRM

* **Eventos activadores:** EV-02
* **Descripción:** Los canales aportan la información inicial. Se registran solo los datos necesarios para identificar y dar seguimiento a la consulta.
* **Mecanismo actual:** Procedimiento manual
* **Validaciones requeridas:**
  * Buscar contactos existentes por teléfono, nombre o documento disponible antes de crear uno; comprobar identidad y no asumir que nombres iguales son la misma persona.
  * No crear una ficha de contacto nueva por cada canal o nueva compra. Distinguir duplicados de una misma necesidad de nuevos leads del mismo contacto.
  * Registrar la relación con el maestro de obra como dato opcional.

### F-02: CRM a ERP

* **Eventos relacionados:** EV-11 (aceptación comercial), EV-14 (solicitud CRM → ERP) y EV-15 (respuesta ERP → CRM).
* **Descripción:** La aceptación documentada de una versión de cotización habilita la solicitud funcional de pedido (EV-14), pero **no confirma automáticamente un pedido**. El ERP revalida las condiciones y devuelve una respuesta aceptada, condicionada o rechazada (EV-15). Solo tras la validación operativa aceptada puede quedar confirmado el pedido y continuar la ejecución (reserva cuando proceda, entrega, comprobante y cobro).
* **Mecanismo actual:** Contrato funcional entre módulos de una misma instancia de Odoo Community; el mecanismo y los controles técnicos están por validar, sin presuponer una API ni una entidad nueva.
* **Validaciones requeridas:**
  * Debe conocerse a quién se atribuye la operación y quién es el contacto responsable.
  * Debe existir una cotización concreta con versión, productos, unidades, cantidades, precios y descuentos aplicables.
  * **Antes de emitir cada cotización o versión**, Comercial consulta la disponibilidad de **todos sus productos**, por ubicación, registra incertidumbres y no convierte esa consulta en una reserva.
  * Toda promesa de disponibilidad o fecha debe indicar si está confirmada, pendiente o condicionada.
  * Debe registrarse quién aceptó, qué versión, cuándo y bajo qué condiciones.
  * Los descuentos y condiciones excepcionales deben tener un autorizador registrado.
  * Cualquier diferencia entre lo cotizado y lo pedido se registra como cambio, no se corrige en silencio.
  * La cotización y la versión aceptada deben ser identificables; sus líneas, moneda, cantidades, precios y condiciones no se alteran sin una nueva aceptación.
  * Los descuentos excepcionales y el crédito deben contar con autorización expresa. La fórmula del recargo a crédito queda pendiente de definición.
  * Deben verificarse destino, fecha comunicada, flete y viabilidad logística. El despacho programado considera como mínimo el día siguiente, sujeto a ratificar cómo se computa.
  * **Tras EV-14**, el ERP vuelve a validar stock físico apto, reservas, disponibilidad por ubicación, autorizaciones y fecha logística; registra demanda no cubierta sin tratarla como existencias. Si faltan cemento, fierro, agregados o ladrillos, puede plantearse abastecimiento documental P2P y una promesa condicionada, nunca una reserva ficticia ni una entrega garantizada. Otros faltantes requieren revisión antes de comprometer una oferta.
  * EV-15 debe indicar respuesta **aceptada, condicionada o rechazada**, fundamento, responsable, fecha y próxima acción. La respuesta condicionada o rechazada no equivale a pedido confirmado; no se genera un segundo pedido por diseño ni se inventa un endpoint.

## 6. Supuestos y restricciones

* El CRM conserva la responsabilidad **comercial** hasta la cotización aceptada, aunque las cotizaciones se apoyen técnicamente en Ventas (`sale`/`sale_crm`) dentro de la misma instancia de Odoo. La responsabilidad **operativa** del ERP comienza con el pedido confirmado e incluye almacén, abastecimiento y contabilidad.
* Los actores se identifican con los perfiles del [modelo de autorización](../seguridad/modelo-autorizacion.md): R-ATN (atención comercial) y R-PRO (propietario aprobador).
* Contactos es el maestro compartido de identidad para el flujo propuesto, no un sistema externo ni una etapa comercial. La obligatoriedad funcional definida para MIGAL no implica que todo lead de Odoo requiera una ficha de cliente vinculada desde su creación.
* Actualmente MIGAL no realiza campañas; su gestión es una propuesta del equipo y comprende únicamente iniciativas propias.
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
| P-01 | Precisar los datos mínimos exigibles en EV-14 y para la confirmación posterior a EV-15, según el tipo de operación | F-02 |
| P-02 | Definir cómo se registra la aceptación del cliente y qué evidencia se conserva | F-02 |
| P-03 | Probar en Odoo la conversión de oportunidad a cotización y de cotización a pedido | F-02 |
| P-04 | Verificar el comportamiento ante stock insuficiente o entrega parcial | F-02 |
| P-05 | Revisar los permisos disponibles para precios y descuentos | F-02 |
| P-06 | Verificar la vinculación con Contactos y la revisión de duplicados, permitiendo múltiples leads/oportunidades por contacto para necesidades distintas | F-01 |
| P-07 | Verificar en Odoo 18 Community los estados reales de cotización y pedido, el enlace con la oportunidad (`sale_crm`), la confirmación y los permisos | F-02 |
| P-08 | Definir el registro de EV-14 y EV-15, incluyendo correlación, respuesta condicionada, déficit y referencia de pedido | F-02 |
| P-09 | Ratificar el cómputo del despacho programado mínimo al día siguiente y definir el recargo a crédito | F-02 |
| P-10 | Comprobar en una prueba del flujo el mapeo de información disponible, la ubicación en la etapa correspondiente y el registro de solicitud o aceptación de preparar cotización en EV-06, incluyendo clientes conocidos que llegan con datos suficientes para avanzar en la misma atención | F-01 / F-02 |

## 9. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | Gabriel Martínez | Versión inicial |
| 0.2 | 22/10/2026 | Gabriel Martínez | Ampliación funcional: solicitud y respuesta ERP, validaciones y datos mínimos |
| 0.3 | 23/10/2026 | Autor por registrar al aprobar | Propuesta de alineación puntual con POL-09, EV-14/EV-15, revalidación y frontera funcional CRM–ERP; pendiente de aprobación del equipo |
| 0.4 | 23/10/2026 | Autor por registrar al aprobar | Precisión de Contactos, múltiples leads por cliente, descarte justificado, campañas propias propuestas y criterios de transición Lead → Lead calificado → Oportunidad → Cotización; pendiente de revisión del equipo |
| 0.5 | 23/10/2026 | — | Se retira la autoría de los metadatos iniciales y se permite motivo desconocido en pérdida de oportunidades y no adjudicación de cotizaciones |
| 0.6 | 23/10/2026 | — | Se explicitan las etapas comerciales propuestas por MIGAL y el mapeo de información disponible para ubicar cada necesidad en su etapa correspondiente |

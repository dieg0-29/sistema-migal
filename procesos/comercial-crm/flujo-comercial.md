# Flujo comercial: de la necesidad comercial al pedido confirmado

## 1. Propósito

`[E][AS-IS]` MIGAL recibe consultas y solicitudes comerciales mediante atención presencial, WhatsApp, llamadas y referidos. La información se encuentra distribuida en registros no integrados, lo que dificulta el seguimiento de negociaciones, cotizaciones y decisiones comerciales. `[FN-01][FN-02]`

`[TO-BE]` El flujo propuesto permite gestionar una necesidad comercial desde su identificación hasta la confirmación del pedido, conservando la trazabilidad de los participantes, oportunidades, cotizaciones, condiciones aceptadas y validaciones operativas.

El CRM gestiona el recorrido comercial y el ERP valida la disponibilidad de stock, aplica las reglas de abastecimiento y permite confirmar los pedidos. La programación de entregas y el abastecimiento se coordinan después de la confirmación.

## 2. Inicio y fin

**Inicio:** identificación o recepción de una necesidad comercial del cliente mediante atención directa, WhatsApp, llamada, referido o campaña identificable, cuando corresponda.

**Fin:** confirmación del pedido después de la respuesta ERP (EV-15), ya sea aceptada o condicionada por abastecimiento. El recorrido también puede finalizar sin pedido cuando no existe aceptación comercial o la solicitud es rechazada.

La cotización aceptada (EV-11) no constituye por sí misma un pedido confirmado ni una oportunidad ganada.

El proceso O2C continúa posteriormente mediante reservas, abastecimiento cuando corresponda, programación y ejecución de entregas, emisión de comprobantes, cobros y cierre.

## 3. Sistemas, módulos y actores

### 3.1. Responsabilidades funcionales

| Sistema | Responsabilidad |
|---|---|
| CRM | Registrar la necesidad comercial, gestionar leads, calificación, oportunidades, cotizaciones, aceptación, solicitud de pedido y seguimiento de la respuesta ERP. |
| ERP | Consultar existencias, validar las solicitudes de pedido, aplicar las políticas de abastecimiento y gestionar la confirmación y ejecución operativa de pedidos. |

### 3.2. Módulos técnicos de apoyo

| Módulo Odoo | Intervención |
|---|---|
| CRM (`crm`) | Gestión de leads, oportunidades, etapas comerciales y seguimiento. |
| Contactos (`contacts`) | Identificación de clientes, contactos y participantes. |
| Ventas (`sale_management` / `sale`) | Elaboración de cotizaciones y gestión de pedidos. |
| Inventario (`stock`) | Consulta de disponibilidad, reservas y movimientos de inventario. |
| Punto de Venta (`point_of_sale`) | Ventas directas de mostrador mediante un recorrido simplificado. |

Los módulos pertenecen a una misma instancia de Odoo 18 Community. Las responsabilidades funcionales de CRM y ERP no equivalen a una separación física entre aplicaciones.

### 3.3. Actores y responsabilidades

| Actor | Responsabilidad |
|---|---|
| **Responsable de Atención y Ventas** | Atiende al cliente, registra y califica leads, gestiona oportunidades, consulta existencias, elabora cotizaciones, realiza seguimiento, registra aceptaciones y genera solicitudes de pedido. |
| **Propietario de MIGAL** | Autoriza excepciones comerciales y decide sobre solicitudes con faltantes de productos que no pertenezcan a las cuatro categorías contempladas por la política de abastecimiento. |
| **Responsable de Despacho y Transporte** | Después de confirmar el pedido, verifica los recursos de reparto y determina la fecha viable de entrega. |
| **Responsable de Abastecimiento** | Coordina la reposición de productos faltantes en pedidos condicionados e informa la fecha prevista de disponibilidad. |
| **Cliente o responsable de la compra** | Comunica su necesidad, solicita o acepta recibir una cotización y decide si acepta la propuesta. |
| **Maestro de obra o contacto de obra** | Puede comunicar requerimientos, preparar listas de materiales y coordinar aspectos de la entrega, sin asumir necesariamente la responsabilidad económica. |
| **ERP de Odoo** | Contrasta cantidades con existencias, identifica categorías de productos faltantes y determina la respuesta operativa o el escalamiento al propietario. |

Los actores humanos representan funciones asignables al personal existente de MIGAL, no nuevos puestos de trabajo. La validación ordinaria de stock se propone como automatizada; la intervención del propietario se limita a las excepciones definidas.

## 4. Entradas y salidas

Las entradas y salidas identifican las condiciones necesarias y los resultados de cada etapa. El avance comercial no se produce automáticamente por disponer de más información.

| Entrada | Salida |
|---|---|
| Necesidad o interés inicial del cliente y datos mínimos para continuar la atención. | **Lead registrado**, con necesidad, canal, fuente y responsable. |
| Lead con necesidad concreta de materiales o productos identificada. | **Lead calificado**, aunque todavía no exista negociación. |
| Lead calificado e intención del cliente de continuar evaluando una posible compra. | **Oportunidad comercial**, con responsable y próxima acción. |
| Oportunidad y solicitud del cliente para recibir una cotización, o conformidad con la propuesta de Ventas de elaborarla. | **Solicitud o conformidad para cotizar identificada**; se recopila cualquier información pendiente. |
| Solicitud o conformidad, datos suficientes y existencias consultadas. | **Cotización elaborada** mediante Ventas, con identificador propio y vinculada a la oportunidad. |
| Cotización presentada y solicitud de cambios del cliente. | **Otra cotización independiente**, vinculada a la misma oportunidad y con su propio identificador. |
| Cotización identificada y aceptación del cliente. | **Cotización aceptada (EV-11)**, con referencia y evidencia. |
| Cotización aceptada y condiciones necesarias para la validación. | **Solicitud de pedido (EV-14)** vinculada a la cotización. |
| Solicitud de pedido y verificación ERP de stock y categorías. | **Respuesta EV-15**: aceptada, condicionada o rechazada, previa decisión del propietario cuando corresponda. |
| Respuesta aceptada o condicionada. | **Pedido confirmado**, con compromiso de abastecimiento si existen faltantes. |
| Respuesta rechazada. | Solicitud sin pedido confirmado y motivo registrado. |
| Pedido confirmado. | Coordinación posterior de entrega y abastecimiento cuando corresponda. |

La consulta de stock durante la elaboración de la cotización no implica reserva. Tampoco se requiere verificar previamente la disponibilidad de transporte para elaborar la propuesta.

Los datos se relacionan con el [diccionario de datos](../../datos/diccionario-datos.md) y el [modelo de cuentas y canales](cuenta-contacto-canales.md).

## 5. Flujo comercial TO-BE

El flujo establece las actividades, condiciones de avance, responsables y evidencias esperadas desde la necesidad comercial hasta la confirmación del pedido.

| Paso | Etapa | Actividad y criterio de avance | Responsable | Regla o evento | Evidencia |
|---|---|---|---|---|---|
| 1 | Necesidad comercial | Identificar la necesidad inicial y el canal de atención. | Atención y Ventas | EV-02 | Necesidad, canal y fuente. |
| 2 | Lead | Buscar contactos existentes y registrar el interés comercial con datos mínimos para continuar la atención. | Atención y Ventas | EV-03, RN-01 | Lead registrado. |
| 3 | Lead calificado | Precisar la necesidad hasta identificar materiales o requerimientos concretos. | Atención y Ventas | Calificación funcional de EV-05 | Necesidad identificada. |
| 4 | Oportunidad | Comprobar la intención de continuar evaluando una compra y establecer responsable y próxima acción. | Atención y Ventas | EV-05, RN-02 | Oportunidad y seguimiento. |
| 5 | Cotización | Verificar que el cliente solicite o acepte recibir una cotización, completar datos, consultar stock y elaborar la propuesta mediante Ventas. | Atención y Ventas | EV-06, RN-03, RN-06 | Cotización identificada y vinculada a la oportunidad. |
| 6 | Revisión comercial | Gestionar negociaciones y excepciones de precio, solicitando autorización al propietario cuando corresponda. | Atención y Ventas / Propietario | EV-07, EV-08, EV-09 | Condiciones y autorizaciones registradas. |
| 7 | Modificaciones y seguimiento | Comunicar la propuesta y efectuar seguimiento. Si el cliente solicita cambios, crear otra cotización independiente vinculada a la misma oportunidad. | Atención y Ventas | EV-10, RN-04 | Cotizaciones identificadas y actividades registradas. |
| 8 | Aceptación | Registrar la cotización concreta aceptada por el cliente, sin marcar todavía la oportunidad como ganada. | Atención y Ventas | EV-11, RN-08 | Cotización aceptada y evidencia. |
| 9 | Solicitud de pedido | Generar EV-14 con las cantidades y condiciones aceptadas. | Atención y Ventas | EV-14 | Solicitud vinculada a la cotización. |
| 10 | Validación de stock y categorías | Verificar existencias e identificar las categorías de los productos faltantes. | ERP de Odoo (automatización propuesta) | EV-15 | Disponibilidad, faltantes y categorías. |
| 11 | Decisión excepcional | Si algún producto faltante no pertenece a cemento, fierro, agregados o ladrillos, escalar la decisión al propietario. | Propietario / ERP | Política de abastecimiento | Autorización o rechazo documentado. |
| 12 | Respuesta ERP y confirmación | Emitir EV-15 y confirmar el pedido si la respuesta es aceptada o condicionada. Ante rechazo, no confirmar. | ERP de Odoo (flujo propuesto) | EV-15, RN-08 | Pedido confirmado o rechazo registrado. |
| 13 | Cierre comercial | Marcar la oportunidad como **Ganada** únicamente cuando exista un pedido confirmado, aceptado o condicionado. | Atención y Ventas | Criterio TO-BE de cierre | Oportunidad ganada y pedido vinculado. |
| 14 | Coordinación de entrega | Después de confirmar el pedido, consultar a Despacho la fecha viable y, si existen faltantes, a Abastecimiento la disponibilidad prevista. | Despacho, Abastecimiento y Atención y Ventas | Coordinación operativa | Fecha y responsables registrados. |
| 15 | Continuidad operativa | ERP gestiona reservas, reposición pendiente, programación y entregas, manteniendo el seguimiento comercial correspondiente. | ERP / Atención y Ventas | Frontera F-02 | Pedido y compromisos pendientes. |

Los pasos 1 al 5 representan el avance comercial hasta la cotización. Los pasos 6 al 8 corresponden a negociación y aceptación. Los pasos 9 al 12 abarcan la solicitud, validación y confirmación del pedido. El paso 13 formaliza el cierre comercial como ganado y los pasos 14 y 15 representan la continuidad operativa.

El paso 11 solamente se ejecuta si existen productos faltantes fuera de las cuatro categorías establecidas. No se requiere la intervención del propietario para las validaciones ordinarias de stock.

La respuesta condicionada representa un pedido confirmado con compromiso de abastecimiento y no necesita convertirse posteriormente en aceptada.

### 5.1. Criterios de calificación y transición comercial

| Momento comercial | Criterio necesario | Qué todavía no implica |
|---|---|---|
| **Lead** | Existe un interés inicial y datos mínimos para continuar la atención. | No implica necesidad concreta ni negociación. |
| **Lead calificado** | Se identifica una necesidad concreta de materiales o productos. | No implica intención de negociar ni solicitud de cotización. |
| **Oportunidad** | El cliente manifiesta interés en continuar evaluando una compra; se asignan responsable y próxima acción. | No autoriza por sí sola a elaborar una cotización. |
| **Cotización** | El cliente solicita o acepta recibir una propuesta; se completan los datos, se consulta stock y se elabora efectivamente la cotización. | No implica aceptación ni confirmación del pedido. |
| **Ganada** | Existe un pedido confirmado después de una respuesta EV-15 aceptada o condicionada. | No significa que ya se haya entregado o cobrado. |

**Representación funcional prevista en Odoo CRM:**

Lead → Lead calificado → Oportunidad → Cotización → Ganada.

La gestión de leads y su conversión a oportunidades debe distinguirse de las etapas configurables del Kanban de oportunidades. Este recorrido expresa la secuencia funcional acordada, sin asumir que todas sus denominaciones serán columnas de un mismo tablero.

El criterio de **Ganada** corresponde a la confirmación efectiva del pedido, no únicamente a la aceptación de la cotización.

Las reglas específicas de negociación, cotizaciones y autorizaciones se desarrollan en [decisiones comerciales](decisiones-comerciales.md). La validación del pedido, la política de cuatro categorías y el tratamiento de faltantes corresponden a [pedido de cliente](../o2c/pedido.md) e [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md).

## 6. Excepciones y situaciones particulares

| Situación | Tratamiento | Referencia |
|---|---|---|
| Contacto duplicado | Revisar registros existentes antes de crear otro lead. | RN-01 |
| Contacto inválido o necesidad sin continuidad | Registrar descarte o finalización con motivo. | EV-04 |
| Lead sin necesidad concreta | Mantener seguimiento sin calificarlo automáticamente. | Criterio de calificación |
| Lead calificado sin continuidad comercial | No convertirlo automáticamente en oportunidad. | Criterio de oportunidad |
| Oportunidad sin solicitud de cotización | Consultar al cliente si desea recibir una propuesta. | Criterio de cotización |
| Cotización solicitada con datos incompletos | Recopilar información faltante antes de elaborarla. | EV-06 |
| Modificaciones solicitadas por el cliente | Crear otra cotización independiente vinculada a la oportunidad. | EV-10, RN-04 |
| Excepción de precio | Solicitar autorización del propietario y registrar la decisión. | EXC-01 |
| Diferencia entre stock registrado y físico | Comunicar la discrepancia para verificación y eventual ajuste. | EXC-02 |
| Faltantes de cemento, fierro, agregados o ladrillos | Confirmar el pedido condicionado por abastecimiento conforme a la política. | EV-15 |
| Faltantes de otras categorías | Escalar al propietario para autorizar el pedido condicionado o rechazar la solicitud. | EV-15 |
| Respuesta ERP rechazada | No confirmar el pedido y conservar el motivo. | EV-15 |
| Falta de transporte después de confirmar | Coordinar una fecha viable e informar al cliente. | Coordinación logística |
| Venta directa de mostrador | Utilizar POS sin exigir el recorrido completo de leads y oportunidades. | Alcance POS |

La respuesta final EV-15 mantiene tres resultados: aceptada, condicionada y rechazada. El escalamiento al propietario es una actividad de decisión previa, no un cuarto estado definitivo.

## 7. Indicadores

Los indicadores comerciales se encuentran en [indicadores O2C](../o2c/indicadores.md).

La medición debe distinguir leads registrados, leads calificados, oportunidades, cotizaciones elaboradas, cotizaciones aceptadas, pedidos confirmados y pedidos condicionados por abastecimiento.

La creación de varias cotizaciones para una misma oportunidad no debe contabilizarse como varias oportunidades comerciales.

## 8. Pendientes de validación técnica

- **Configuración del CRM:** habilitar la gestión de leads, definir su conversión a oportunidades y configurar las etapas correspondientes del Kanban, incluido el criterio de Ganada.
- **Módulos instalados:** comprobar en la instancia la disponibilidad de CRM, Ventas, Inventario y sus dependencias necesarias.
- **Cotizaciones independientes:** verificar la asociación de varias cotizaciones a una oportunidad y la conservación de la cotización finalmente aceptada, sin presuponer versionado automático.
- **Validación EV-14/EV-15:** comprobar o implementar la consulta automática de stock, identificación de categorías faltantes, escalamiento excepcional y respuestas de validación.
- **Reservas y abastecimiento:** verificar el comportamiento real de las reservas y la demanda pendiente al confirmar pedidos condicionados.
- **Coordinación operativa:** definir el registro de fechas de Despacho y Abastecimiento después de confirmar pedidos.
- **Asignación de responsabilidades:** identificar qué personas existentes asumirán las funciones comerciales y operativas.
- **Alineación documental:** actualizar el BPMN comercial, INT-01, el catálogo de reglas y los documentos O2C relacionados.

Las decisiones funcionales aquí descritas no constituyen evidencia de configuración o implementación técnica completada.

Fuentes: [fuentes del negocio](../../fuentes/fuentes-negocio.md), [alcance](../../arquitectura/base/alcance.md), [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md), [catálogo de reglas](../catalogo-reglas.md), [decisiones comerciales](decisiones-comerciales.md) y [pedido de cliente](../o2c/pedido.md).

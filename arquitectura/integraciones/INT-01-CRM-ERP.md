# INT-01: Contrato funcional CRM–ERP

## 1. Propósito y alcance

`[TO-BE]` Definir los eventos, datos y controles que permiten conservar la trazabilidad desde la recepción de una necesidad comercial hasta la confirmación del pedido y su transferencia a la ejecución operativa.

El contrato relaciona la identidad del cliente, la oportunidad, las cotizaciones, la aceptación comercial, la solicitud de validación y el resultado operativo.

En este documento:

- **Evento:** hecho del negocio que debe poder reconstruirse mediante registros o evidencias.
- **Actividad:** acción que una persona o el sistema realiza para avanzar en el proceso.
- **Estado técnico:** valor que Odoo utiliza para representar la situación de un registro.
- **Resultado funcional:** decisión del proceso de MIGAL, aunque no exista como estado estándar de Odoo.

Un evento no exige una pantalla, un campo, una API ni un objeto independiente. Varios eventos pueden quedar documentados en una misma operación.

### 1.1. Distribución del contenido

| Documento | Contenido que desarrolla |
|---|---|
| [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md) | Recorrido comercial, etapas y criterios de transición. |
| [Cuenta, contacto y canales](../../procesos/comercial-crm/cuenta-contacto-canales.md) | Identidad, participantes, canales y continuidad de la relación comercial. |
| [Decisiones comerciales](../../procesos/comercial-crm/decisiones-comerciales.md) | Cotizaciones, precios, autorizaciones y política de abastecimiento condicionado. |
| [Pedido de cliente](../../procesos/o2c/pedido.md) | Preparación, disponibilidad utilizable, confirmación, cambios y cancelaciones. |
| **INT-01** | Eventos, fronteras de información, correspondencia de registros y datos necesarios para reconstruir la transición. |
| [Flujo O2C](../../procesos/o2c/flujo.md) | Ejecución posterior: abastecimiento, entrega, comprobante, cobro y cierre. |

### 1.2. Límites

Este contrato incluye la atención comercial y la transición hacia un pedido confirmado. La venta directa de mostrador mediante Punto de Venta conserva su recorrido simplificado y no debe forzarse a pasar por todos estos eventos.

No define una integración automática con WhatsApp o KeyFacil ni una API entre instancias de Odoo.

## 2. Representación funcional en Odoo 18 Community

### 2.1. Aplicaciones y registros

CRM y ERP representan responsabilidades funcionales dentro de una misma instancia. Las cotizaciones y los pedidos se gestionan técnicamente en Ventas, aunque intervengan en ambos lados de la frontera funcional.

| Concepto del proyecto | Representación de referencia | Criterio para MIGAL |
|---|---|---|
| Persona, empresa o cliente | Contactos; registro `res.partner`. | Reutilizar la identidad existente y distinguir los participantes de cada operación. |
| Lead | CRM; registro `crm.lead` de tipo lead. | Registrar una necesidad para su evaluación. |
| Lead calificado | Hito funcional previo a la conversión. | Evidenciar que se identificó una necesidad concreta; no asumir un tercer tipo nativo. |
| Oportunidad | CRM; registro `crm.lead` de tipo oportunidad. | La conversión no exige duplicar el registro original. |
| Etapa comercial | Etapa configurable del CRM. | Definir su correspondencia con el flujo de MIGAL; no confundirla con el estado de una cotización. |
| Cotización | Ventas; registro `sale.order` sin confirmar. | Vincularla a la oportunidad mediante la relación correspondiente. |
| Pedido de venta | El mismo `sale.order` después de confirmar. | No crear otro pedido solo para representar el paso CRM–ERP. |
| Entrega y movimientos | Documentos operativos de Inventario. | Dar seguimiento independiente a reserva y ejecución física. |
| Seguimiento | Actividades asociadas al registro. | Registrar responsable, vencimiento y acción pendiente. |
| Evidencias | Notas, mensajes y adjuntos del *chatter*, o datos estructurados cuando se requieran. | Elegir el soporte según la necesidad de control y consulta. |

La documentación explica la conversión de leads en oportunidades. El código público identifica ambos mediante `crm.lead` y la cotización/pedido mediante `sale.order`. [OD-01][OC-01][OC-02]

### 2.2. Estados nativos y resultados propios

En el código estándar de Ventas revisado, los estados de `sale.order` son:

| Valor técnico | Significado |
|---|---|
| `draft` | Cotización. |
| `sent` | Cotización enviada. |
| `sale` | Pedido de venta confirmado. |
| `cancel` | Cancelado. |

**Aceptada, condicionada y rechazada son resultados funcionales de EV-15**, no valores estándar de ese campo. Deben registrarse mediante un mecanismo adicional sin sustituir indebidamente el estado nativo. [OC-02]

Del mismo modo, “cliente aceptó la propuesta”, “pendiente de autorización” y “pendiente de abastecimiento” describen situaciones del negocio; no se presume que Odoo las distinga automáticamente.

Una cotización comunicada por un canal externo requiere evidencia de presentación. No debe suponerse que enviarla manualmente por WhatsApp actualice su estado técnico.

### 2.3. Confirmación y aceptación

Odoo transforma la cotización en pedido mediante la confirmación. Para MIGAL se mantiene una aceptación comercial previa, EV-11, seguida de la validación definida por este contrato.

La firma o el pago en línea pueden intervenir en la confirmación estándar. Por ello, no deben habilitarse como mecanismo de aceptación separado de EV-14 sin comprobar que respeten el control previo de MIGAL. [OD-05][OD-06]

Hasta resolver esa implementación, la aceptación comercial puede conservarse como evidencia vinculada a la cotización, sin ejecutar todavía su confirmación.

## 3. Contactos, participantes y contexto comercial

### 3.1. Identidad y participación

Odoo permite gestionar personas, empresas y direcciones relacionadas. Para MIGAL, esa identidad debe distinguirse del papel que cada participante tiene en una compra. [OD-03]

| Información | Tratamiento |
|---|---|
| Cliente responsable de la compra | Vincular al cliente correspondiente en la cotización. |
| Persona que consulta | Registrar quién comunica la necesidad, aunque no sea quien paga. |
| Maestro de obra | Identificar su intervención cuando corresponda, sin atribuirle automáticamente la obligación económica. |
| Persona que acepta | Conservar quién expresa la conformidad y verificar su representación cuando sea pertinente. |
| Dirección de entrega | Identificar el destino de la operación. |
| Datos de facturación | Recopilar los necesarios para el comprobante, sin confundirlos con la dirección de entrega. |

Los papeles de dueño de obra, maestro, solicitante o autorizador no se deducen únicamente de un nombre o de una dirección. Si se requiere consultarlos de forma estructurada, su representación debe definirse en el modelo de datos.

No debe registrarse a un maestro independiente como empleado o contacto subordinado de una empresa solo para representar que participa en una compra.

La ficha de un cliente puede relacionarse con varias necesidades y oportunidades. Una compra nueva no exige crear otro contacto; retomar la misma negociación tampoco exige otro lead.

### 3.2. Medio, fuente y campaña

Odoo utiliza los conceptos **Medio, Fuente y Campaña** para atribución comercial. [OD-08]

Para MIGAL se propone la siguiente correspondencia, que debe mantenerse coherente con el documento de canales:

| Concepto | Uso propuesto |
|---|---|
| **Medio** | Canal por el que se originó la consulta: presencial, teléfono, WhatsApp u otro definido. |
| **Fuente** | Origen comercial conocido: referido de maestro, referido de cliente, contacto directo u otra fuente definida. |
| **Campaña** | Iniciativa propia que originó la consulta, únicamente cuando exista esa relación. |

Esta correspondencia es una decisión de configuración del proyecto. No supone que Odoo capture automáticamente la procedencia de una llamada o conversación externa.

“Cliente frecuente” describe la relación con el cliente y no necesariamente el origen de una consulta. Conviene conservar esa distinción para que los reportes no mezclen procedencia y recurrencia.

### 3.3. Información progresiva y seguimiento

La información se completa según el avance de la operación. El registro inicial no exige datos fiscales completos ni una fecha de entrega confirmada.

Se propone utilizar **actividades** para la próxima acción y el **chatter** para notas y soportes. No es necesario crear otro registro por cada llamada o aclaración. [OD-07][OD-09]

Las notas libres sirven como evidencia, pero no sustituyen datos estructurados cuando se necesita aplicar una regla automática, restringir una autorización o elaborar un indicador.

## 4. Catálogo de eventos

Los identificadores principales se conservan para mantener las referencias existentes. Los subidentificadores distinguen momentos relacionados sin alterar el significado de EV-11, EV-14 y EV-15.

La numeración no impone el orden técnico de ejecución. Los disparadores determinan cuándo ocurre cada evento.

### 4.1. Recepción y calificación

| ID | Evento | Disparador | Responsable | Evidencia mínima |
|---|---|---|---|---|
| **EV-01** | Campaña iniciada | Inicio efectivo de una campaña propia. | Propietario | Campaña, objetivo, periodo y responsable. |
| **EV-02** | Consulta recibida | Una persona comunica una necesidad comercial. | Atención y Ventas | Necesidad inicial, participante, medio y fuente; campaña si aplica. |
| **EV-03** | Lead registrado | La necesidad se registra para su seguimiento. | Atención y Ventas | Lead, contacto identificado cuando exista y responsable. |
| **EV-04** | Lead descartado | Se determina un descarte justificado. | Atención y Ventas | Motivo, comprobación, responsable y fecha. |
| **EV-05A** | Lead calificado | Se identifica una necesidad concreta. | Atención y Ventas | Productos o requerimientos conocidos e información pendiente. |
| **EV-05B** | Lead convertido en oportunidad | Existe continuidad comercial y se cumplen los criterios de conversión. | Atención y Ventas | Oportunidad, cliente o contacto, responsable y próxima acción. |

EV-05 agrupa los dos momentos identificados como EV-05A y EV-05B. La calificación puede conservarse mediante evidencia del análisis; no requiere obligatoriamente una columna adicional del Kanban.

Buscar contactos y revisar duplicados son controles de registro. No se exige un evento por cada búsqueda.

La ausencia de DNI/RUC, de un teléfono o de una respuesta temporal no basta por sí sola para descartar un lead si la atención puede continuar por otra vía.

### 4.2. Preparación y presentación de cotizaciones

| ID | Evento | Disparador | Responsable | Evidencia mínima |
|---|---|---|---|---|
| **EV-06A** | Solicitud o conformidad para cotizar registrada | El cliente solicita una propuesta o acepta que se prepare. | Atención y Ventas | Oportunidad, solicitud o conformidad y alcance conocido. |
| **EV-06B** | Información pendiente solicitada | Faltan datos necesarios para cotizar. | Atención y Ventas | Datos faltantes y solicitud de aclaración. |
| **EV-06C** | Información suficiente reunida | Se dispone de productos, cantidades y condiciones necesarias. | Atención y Ventas | Requerimiento y condiciones aplicables. |
| **EV-06D** | Disponibilidad consultada | Se consulta el inventario para preparar la propuesta. | Atención y Ventas mediante Inventario | Productos, cantidades, disponibilidad, ámbito de consulta y faltantes. |
| **EV-06** | Cotización elaborada | Se genera la propuesta con información suficiente y stock consultado. | Atención y Ventas | Cotización identificada y vinculada a la oportunidad. |
| **EV-06E** | Cotización presentada | Se entrega o comunica la propuesta al cliente. | Atención y Ventas | Cotización, destinatario, canal y fecha. |
| **EV-07** | Disponibilidad o fecha comunicada | Se informa disponibilidad o una fecha coordinada. | Atención y Ventas | Información comunicada, fundamento, condición y registro relacionado. |

EV-06B solo ocurre cuando faltan datos. EV-06C puede cumplirse desde la primera atención.

La consulta EV-06D no constituye reserva. EV-07 tampoco acredita por sí mismo disponibilidad física o una programación; conserva lo comunicado y su fundamento.

Aceptar la preparación de una propuesta en EV-06A es diferente de aceptar la cotización en EV-11.

### 4.3. Negociación y aceptación

| ID | Evento | Disparador | Responsable | Evidencia mínima |
|---|---|---|---|---|
| **EV-08** | Excepción de precio solicitada | Se plantea un ajuste que requiere autorización. | Atención y Ventas | Propuesta, producto, cantidad, precio solicitado y motivo. |
| **EV-09** | Excepción de precio resuelta | El propietario autoriza o rechaza. | Propietario; Ventas conserva la evidencia. | Decisión, precio autorizado si aplica, motivo, autorizador y fecha. |
| **EV-10A** | Modificación solicitada | Se solicitan cambios en una propuesta presentada. | Atención y Ventas | Cotización de origen, cambio y motivo. |
| **EV-10** | Nueva cotización por modificación elaborada | Se prepara otra propuesta independiente. | Atención y Ventas | Cotización anterior, nueva cotización y oportunidad común. |
| **EV-10B** | Seguimiento realizado | Se ejecuta una actividad de seguimiento. | Atención y Ventas | Actividad, resultado observado y siguiente acción. |
| **EV-11** | Cotización aceptada | El cliente expresa conformidad con una propuesta concreta. | Atención y Ventas | Cotización, persona que acepta, fecha y soporte. |
| **EV-12** | Resultado sin venta total registrado | Se registra pérdida de la oportunidad o no adjudicación total o parcial de una propuesta. | Atención y Ventas | Registro afectado, alcance del resultado y motivo conocido o desconocido. |
| **EV-13** | Campaña cerrada | Finaliza una campaña propia iniciada. | Propietario | Campaña, fecha y resultados. |

Cada nueva cotización requiere nuevamente EV-06D y EV-06E, asociados al nuevo documento.

El rechazo de una excepción de precio no equivale necesariamente al rechazo de la propuesta original. Una cotización no adjudicada tampoco implica automáticamente que toda la oportunidad esté perdida.

Si solo se acepta parte de una propuesta y cambian sus cantidades, debe identificarse la cotización que refleja lo finalmente aceptado.

### 4.4. Solicitud y validación

| ID | Evento | Disparador | Responsable | Evidencia mínima |
|---|---|---|---|---|
| **EV-14** | Solicitud de pedido registrada | Existe EV-11 y se cumplen los requisitos de preparación. | Atención y Ventas | Solicitud, cotización aceptada, condiciones y autorizaciones. |
| **EV-14A** | Solicitud disponible para validación | Se inicia su procesamiento operativo. | ERP — automatización propuesta. | Solicitud y referencia del procesamiento. |
| **EV-14B** | Disponibilidad revalidada | Se comparan cantidades solicitadas y disponibilidad utilizable. | ERP — automatización propuesta. | Cantidades solicitadas, disponibles y faltantes por producto. |
| **EV-14C** | Categorías de faltantes evaluadas | Se detectan faltantes. | ERP — automatización propuesta. | Categorías y determinación de escalamiento. |
| **EV-14D** | Autorización excepcional solicitada | Algún faltante requiere decisión del propietario. | ERP — automatización propuesta. | Solicitud, faltantes y destinatario de la decisión. |
| **EV-14E** | Autorización excepcional resuelta | El propietario decide. | Propietario | Autorización o rechazo, motivo y fecha. |

EV-14A no presupone una transmisión por API. EV-14C solo ocurre si hay faltantes; EV-14D y EV-14E, cuando corresponde autorización excepcional.

La política aplicable se desarrolla en [decisiones comerciales](../../procesos/comercial-crm/decisiones-comerciales.md#52-política-de-abastecimiento-condicionado).

### 4.5. Confirmación y cierre comercial

| ID | Evento | Disparador | Responsable | Evidencia mínima |
|---|---|---|---|---|
| **EV-15A** | Pedido confirmado | La validación permite formalizar la operación y la confirmación técnica se completa. | ERP — flujo propuesto. | Pedido, solicitud, cotización y faltantes cuando existan. |
| **EV-15** | Respuesta definitiva disponible | Se resuelve la solicitud; los resultados favorables cuentan con confirmación efectiva. | ERP — automatización propuesta. | Resultado, fundamento y pedido confirmado cuando corresponda. |
| **EV-15B** | Resultado incorporado al seguimiento | Comercial dispone de EV-15. | Atención y Ventas; registro técnico por definir. | Resultado relacionado con la oportunidad y acción siguiente. |
| **EV-15C** | Oportunidad marcada como ganada | Existe pedido confirmado con respuesta aceptada o condicionada. | Atención y Ventas, o automatización expresamente implementada. | Oportunidad y pedido relacionado. |
| **EV-15D** | Resultado comunicado al cliente | Se informa confirmación o rechazo. | Atención y Ventas | Resultado comunicado, destinatario, canal y fecha. |
| **EV-15E** | Coordinación operativa solicitada | El pedido confirmado requiere organizar su atención. | Atención y Ventas | Pedido y coordinación con Despacho y Abastecimiento cuando corresponda. |

La decisión favorable precede a la confirmación; EV-15 comunica el resultado definitivo una vez comprobada esta. EV-15A y EV-15 pueden quedar registrados en una misma operación.

Ante rechazo no ocurre EV-15A ni se marca Ganada. La oportunidad puede continuar en negociación.

Los eventos no requieren registros manuales separados cuando una misma evidencia permite reconstruir varios de ellos.

## 5. Fronteras de información

### 5.1. F-01: Canales de atención → CRM

**Evento de entrada:** EV-02.

Los canales aportan la necesidad inicial y la información disponible. Atención y Ventas la relaciona con el contacto y la necesidad comercial correctos.

Los controles de esta frontera son:

- Revisar identidad y contexto antes de crear o fusionar contactos.
- Distinguir una nueva necesidad de la continuación de una negociación existente.
- Registrar participantes conocidos sin convertirlos automáticamente en clientes responsables.
- Conservar medio, fuente y campaña con significados consistentes.
- Aprovechar los datos recibidos para ubicar la necesidad en el momento comercial correspondiente.

El mecanismo inicial es manual para canales externos. No se presume importación automática de conversaciones.

### 5.2. F-02: CRM ↔ ERP

| Momento | Información y responsabilidad |
|---|---|
| **EV-11: aceptación** | Comercial conserva la cotización aceptada y la evidencia del cliente. |
| **EV-14: solicitud** | Comercial pone las condiciones aceptadas a disposición de la validación operativa. |
| **Validación** | ERP comprueba disponibilidad y aplica la política; el propietario resuelve las excepciones. |
| **EV-15: respuesta** | ERP informa el resultado y el pedido confirmado, cuando corresponda. |
| **Seguimiento** | Comercial actualiza la oportunidad y comunica el resultado. |

La intervención operativa empieza al validar la solicitud, no recién después de confirmar.

La frontera es funcional: una cotización de Ventas participa primero en la negociación y luego, al confirmarse, representa el pedido. No se requiere copiarla a otra aplicación.

La programación logística no es condición previa de esta frontera. Continúa después de la confirmación conforme al [flujo O2C](../../procesos/o2c/flujo.md).

### 5.3. Responsabilidad sobre la información

| Información | Registro o ámbito de referencia |
|---|---|
| Identidad y direcciones | Contactos. |
| Necesidad, oportunidad y seguimiento | CRM. |
| Líneas, precios y condiciones de la propuesta | Cotización de Ventas. |
| Aceptación y autorizaciones | Evidencias vinculadas a la propuesta y a la solicitud. |
| Resultado de validación MIGAL | Mecanismo adicional asociado a la solicitud/cotización. |
| Confirmación de la venta | Pedido de Ventas. |
| Reservas, recepciones y entregas | Inventario. |
| Comprobante fiscal | KeyFacil, con referencia y conciliación según el documento especializado. |

Una referencia entre registros debe permitir recuperar la información necesaria sin duplicarla innecesariamente.

## 6. Datos del contrato EV-11, EV-14 y EV-15

### 6.1. EV-11: aceptación comercial

| Dato | Criterio |
|---|---|
| Cotización aceptada | Identificador inequívoco del documento. |
| Oportunidad y cliente | Referencias de la operación comercial. |
| Persona que acepta | Identidad del participante y representación cuando corresponda. |
| Fecha y canal | Momento y medio de la aceptación. |
| Evidencia | Soporte o registro de la conformidad. |
| Condiciones aceptadas | Contenido recuperable de productos, cantidades, precios y condiciones. |

`[TO-BE]` Se propone conservar el PDF presentado junto con la evidencia cuando sea necesario fijar el contenido aceptado. Un vínculo a un documento editable, por sí solo, no garantiza recuperar lo que el cliente vio.

### 6.2. EV-14: solicitud de validación y pedido

| Dato | Criterio |
|---|---|
| Referencia de solicitud | Permitir relacionar procesamiento y respuesta; puede apoyarse en registros existentes. |
| Cotización y EV-11 | Identificar la propuesta y aceptación que fundamentan la solicitud. |
| Oportunidad y cliente | Conservar el contexto comercial. |
| Líneas | Producto, unidad, cantidad y precio aceptados; descuentos e impuestos aplicables. |
| Condiciones | Moneda, condiciones de pago y flete cuando corresponda. |
| Destino | Dirección o modalidad de entrega pertinente. |
| Autorizaciones | Referencias a decisiones excepcionales exigibles. |
| Consulta de stock | Antecedente utilizado al cotizar; no sustituye la revalidación. |
| Responsable y fecha | Identificar quién solicita y cuándo. |

Las **condiciones de pago** expresan plazos o vencimientos; no equivalen al medio previsto, como efectivo o transferencia. Ambos deben diferenciarse si se necesitan.

No se exige una fecha logística ya confirmada. Tampoco debe utilizarse “Referencia del cliente” como identificador interno de EV-14 si ese campo se necesita para la referencia aportada por el comprador.

### 6.3. EV-15: respuesta definitiva

| Dato | Criterio |
|---|---|
| Solicitud atendida | Correlación inequívoca con EV-14. |
| Resultado funcional | Aceptada, condicionada o rechazada. |
| Fundamento | Resultado de la validación y motivo. |
| Cantidades | Disponibilidad y faltantes comprobados. |
| Categorías | Clasificación de los productos faltantes que sustentó la decisión. |
| Excepción | Autorización o rechazo del propietario, cuando corresponda. |
| Pedido confirmado | Referencia exigible para resultados aceptados o condicionados. |
| Fecha y autoría | Momento de resolución, ejecución automatizada e intervenciones humanas pertinentes. |

| Resultado | Efecto |
|---|---|
| **Aceptada** | Pedido confirmado con disponibilidad suficiente según la comprobación. |
| **Condicionada** | Pedido confirmado con compromiso de abastecimiento. |
| **Rechazada** | Solicitud resuelta sin confirmar el pedido. |

La condición de abastecimiento no altera el significado técnico de pedido confirmado. El seguimiento posterior de faltantes no requiere reescribir EV-15 como aceptada.

El rechazo de la solicitud tampoco equivale automáticamente al estado técnico Cancelado: el tratamiento de la cotización depende de si continúa la negociación.

## 7. Controles de consistencia

### 7.1. Correspondencia de condiciones

La propuesta aceptada, la solicitud y el pedido deben corresponder en cliente, productos, unidades, cantidades y condiciones.

Si cambia lo acordado, se aplica el procedimiento de [pedido de cliente](../../procesos/o2c/pedido.md#4-cambios-y-cancelaciones). No se reemplaza silenciosamente la información que sustentó la aceptación.

Las cotizaciones alternativas se mantienen distinguibles. Su relación con la misma oportunidad no constituye un sistema automático de versiones.

### 7.2. Disponibilidad y reservas

La existencia actual, la reserva y el pronóstico deben distinguirse. El pronóstico considera movimientos previstos y no acredita que la mercadería ya esté físicamente disponible. [OD-10]

Los productos sometidos al control de faltantes deben tener seguimiento de inventario adecuado. Odoo considera disponibles los bienes sin seguimiento, por lo que esa configuración impediría utilizar sus cantidades como base del control propuesto. [OD-11]

El método de reserva es configurable. Por ello, confirmar no acredita que todo el pedido haya quedado reservado. [OD-04]

La tienda y los dos almacenes físicos deben mapearse a una estructura de almacenes y ubicaciones validada. No debe suponerse que la cantidad global de todos los puntos puede utilizarse inmediatamente para cualquier pedido.

Tampoco debe descontarse dos veces una demanda ya considerada en el cálculo de disponibilidad. La fórmula y el ámbito de consulta deberán probarse en Inventario.

### 7.3. Autorizaciones y rutas de confirmación

La política de categorías de MIGAL constituye una regla adicional. El estándar puede confirmar ventas con faltantes; la consulta de disponibilidad no equivale a un bloqueo. [OD-12]

La implementación debe verificar las vías de confirmación habilitadas, incluyendo el botón de confirmación y, si se utilizan, firma o pago del portal. Todas deben respetar el control acordado.

Una nota o una actividad dirigida al propietario puede documentar una solicitud, pero no impide por sí sola que un usuario confirme la venta.

### 7.4. Repeticiones y fallas

Un reintento de la misma solicitud no debe crear otro pedido. Una nueva cotización o modificación debe distinguirse de un reprocesamiento.

Si falla la confirmación técnica, no se comunica un pedido confirmado ni se marca Ganada. La incidencia permanece pendiente de recuperación y no se convierte automáticamente en rechazo comercial.

La confirmación y el registro del resultado deben conservar coherencia aun cuando ocurra un error durante el procesamiento.

### 7.5. Cierre comercial

El criterio de Ganada es propio del flujo de MIGAL: requiere pedido confirmado.

La revisión de `sale_crm` muestra que su confirmación actualiza los ingresos esperados relacionados, pero no ejecuta por sí misma el marcado como Ganada. Se conserva la acción de Atención y Ventas o se implementa una automatización explícita. [OC-03]

La pérdida de una oportunidad debe registrarse como tal. Archivar un registro sin marcarlo Perdido no produce necesariamente el mismo efecto en los reportes comerciales. [OD-02]

## 8. Ajustes y funciones aprovechables

| Elemento | Tratamiento propuesto |
|---|---|
| Actividades de seguimiento | Usarlas para solicitar datos, efectuar llamadas y organizar próximas acciones. |
| Notas y adjuntos | Utilizarlos para evidencia contextual, sin suponer auditoría exhaustiva de todos los campos. |
| Relación oportunidad–cotizaciones | Aprovecharla para consultar propuestas de una misma negociación. |
| Direcciones diferenciadas | Separar facturación y entrega cuando la operación lo necesite. |
| Medio, fuente y campaña | Configurar valores consistentes para atribución comercial. |
| Lista de precios y vencimiento | Utilizar sus funciones cuando MIGAL defina las políticas; revisar los valores predeterminados para no imponer una vigencia no aprobada. |
| Motivos de pérdida | Utilizar un catálogo controlado y permitir motivo desconocido cuando no haya evidencia. |
| Resultado EV-15 | Definir un registro estructurado adicional si debe alimentar automatización e indicadores. |
| Participantes por operación | Definir su representación cuando sea necesario consultar separadamente solicitante, maestro y responsable de compra. |
| Autorizaciones excepcionales | Implementar evidencia y control de permisos coherentes; no limitarse a un comentario si se requiere bloqueo. |

Las capacidades de CRM para cotizaciones, vencimientos y motivos de pérdida están documentadas por Odoo; su utilización concreta depende de configuración y módulos instalados. [OD-02]

La documentación general de Odoo no implica que toda función mostrada esté disponible en Community. Las ampliaciones deberán verificarse para la edición y los módulos concretos de MIGAL.

## 9. Riesgos y validaciones pendientes

### 9.1. Riesgos del contrato

| Riesgo | Control propuesto |
|---|---|
| Confundir al solicitante con el cliente responsable | Registrar los participantes y su papel en la operación. |
| Duplicar contactos o necesidades | Revisar identidad y continuidad antes de crear registros. |
| Tratar resultados MIGAL como estados nativos | Mantener explícito el mapeo funcional y técnico. |
| Confirmar sin aplicar la política de faltantes | Controlar todas las vías de confirmación habilitadas. |
| Considerar disponible una cantidad futura o no rastreada | Validar seguimiento del producto y cálculo de disponibilidad. |
| Perder las condiciones aceptadas | Conservar evidencias y antecedentes de los cambios. |
| Duplicar pedidos al repetir una solicitud | Reconocer solicitudes ya procesadas y pedidos relacionados. |
| Comunicar éxito tras una falla técnica | Comprobar confirmación efectiva antes de emitir el resultado favorable. |

El seguimiento general corresponde al [registro de riesgos y controles](../base/riesgos-y-controles.md).

### 9.2. Pruebas específicas

| Prueba | Resultado esperado |
|---|---|
| **Identidad y conversión** | Reutilizar contactos y convertir leads sin duplicar innecesariamente registros. |
| **Calificación funcional** | Identificar cómo queda evidenciada EV-05A antes de EV-05B. |
| **Cotizaciones relacionadas** | Conservar propuestas independientes y distinguir la aceptada. |
| **Aceptación previa** | Registrar EV-11 sin confirmar anticipadamente la venta. |
| **Contrato EV-14/EV-15** | Recuperar solicitud, decisión, autorizaciones y pedido desde sus referencias. |
| **Casos de validación** | Resolver stock suficiente, faltantes permitidos, autorización excepcional y rechazo. |
| **Rutas de confirmación** | Evitar que una vía habilitada omita la validación de MIGAL. |
| **Reintentos y fallas** | No duplicar pedidos ni informar confirmaciones inexistentes. |
| **Ganada** | Aplicar el cierre únicamente después de confirmar. |
| **Historial** | Recuperar condiciones y decisiones originales después de cambios. |

La asignación nominal de funciones y permisos corresponde al [modelo de autorización](../seguridad/modelo-autorizacion.md).

Las pruebas detalladas de reservas, cambios y cancelaciones corresponden a [pedido de cliente](../../procesos/o2c/pedido.md); las de entrega y abastecimiento, al [flujo O2C](../../procesos/o2c/flujo.md).

## 10. Referencias oficiales y alcance de la revisión

Las referencias respaldan el comportamiento estándar. Las reglas propias de MIGAL continúan siendo decisiones TO-BE y requieren implementación y pruebas.

### Documentación de usuario de Odoo 18

- **OD-01:** [Conversión de leads en oportunidades](https://www.odoo.com/documentation/18.0/applications/sales/crm/acquire_leads/convert.html).
- **OD-02:** [Creación y envío de cotizaciones desde CRM](https://www.odoo.com/documentation/18.0/applications/sales/crm/acquire_leads/send_quotes.html).
- **OD-03:** [Contactos](https://www.odoo.com/documentation/18.0/applications/essentials/contacts.html).
- **OD-04:** [Métodos de reserva](https://www.odoo.com/documentation/18.0/applications/inventory_and_mrp/inventory/shipping_receiving/reservation_methods.html).
- **OD-05:** [Firma en línea para confirmar pedidos](https://www.odoo.com/documentation/18.0/es/applications/sales/sales/sales_quotations/get_signature_to_validate.html).
- **OD-06:** [Confirmación mediante pago en línea](https://www.odoo.com/documentation/18.0/applications/sales/sales/sales_quotations/get_paid_to_validate.html).
- **OD-07:** [Actividades para equipos de ventas](https://www.odoo.com/documentation/18.0/applications/sales/crm/optimize/utilize_activities.html).
- **OD-08:** [Atribución de marketing](https://www.odoo.com/documentation/18.0/applications/sales/crm/track_leads/marketing_attribution.html).
- **OD-09:** [Chatter](https://www.odoo.com/documentation/18.0/applications/productivity/discuss/chatter.html).
- **OD-10:** [Informe de pronóstico](https://www.odoo.com/documentation/18.0/applications/inventory_and_mrp/inventory/warehouses_storage/reporting/forecast.html).
- **OD-11:** [Tipo de producto y seguimiento de inventario](https://www.odoo.com/documentation/18.0/applications/inventory_and_mrp/inventory/product_management/configure/type.html).
- **OD-12:** [Reservas al confirmar y tratamiento de faltantes](https://www.odoo.com/documentation/18.0/applications/inventory_and_mrp/inventory/shipping_receiving/reservation_methods/at_confirmation.html).

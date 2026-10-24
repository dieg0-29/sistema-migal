# Decisiones comerciales y responsabilidades

## 1. Propósito

Establecer las decisiones comerciales relevantes de Ferre & Inversiones MIGAL, identificando sus responsables, criterios, controles y evidencias necesarias para conservar la trazabilidad del proceso.

El documento desarrolla las decisiones relacionadas con:

1. Solicitud, elaboración y modificación de cotizaciones.
2. Negociación de precios y descuentos excepcionales.
3. Consulta de disponibilidad de productos.
4. Validación de solicitudes de pedido y autorizaciones excepcionales.
5. Coordinación posterior de entregas y abastecimiento.
6. Condiciones comerciales de facturación y pago.

`[TO-BE]` Las decisiones se integran en una solución basada en Odoo 18 Community, diferenciando la gestión comercial funcional del CRM de la validación y ejecución operativa del ERP.

## 2. Matriz de decisiones comerciales

| Decisión                                | Criterio funcional                                                                 | Responsable                            | Evidencia esperada                                             |
| --------------------------------------- | ---------------------------------------------------------------------------------- | -------------------------------------- | -------------------------------------------------------------- |
| **Elaborar cotización**                 | El cliente la solicita o acepta que Atención y Ventas la prepare.                  | Atención y Ventas / Cliente            | Cotización elaborada y vinculada a la oportunidad.             |
| **Modificar cotización**                | El cliente solicita cambios en productos, cantidades, precios u otras condiciones. | Atención y Ventas                      | Nueva cotización independiente y referencia de la anterior.    |
| **Autorizar descuento excepcional**     | Se solicita un precio diferente al de referencia y requiere aprobación.            | Propietario de MIGAL                   | Precio original, precio aprobado, motivo, autorizador y fecha. |
| **Consultar disponibilidad**            | Se requiere conocer las existencias para elaborar la propuesta.                    | Atención y Ventas mediante Inventario  | Productos, cantidades y stock consultado.                      |
| **Aceptar cotización**                  | El cliente expresa conformidad con una cotización identificable.                   | Cliente / Atención y Ventas            | Cotización aceptada y evidencia de aceptación (EV-11).         |
| **Validar solicitud de pedido**         | Se recibe EV-14 y se verifica stock y categorías de productos faltantes.           | ERP, mediante automatización propuesta | Resultado de la validación EV-15.                              |
| **Autorizar faltantes excepcionales**   | Algún producto faltante no pertenece a cemento, fierro, agregados o ladrillos.     | Propietario de MIGAL                   | Decisión de autorización o rechazo y motivo.                   |
| **Confirmar pedido**                    | EV-15 obtiene respuesta aceptada o condicionada.                                   | ERP, según flujo propuesto             | Pedido confirmado y vinculado a la cotización aceptada.        |
| **Determinar fecha de entrega**         | El pedido ya está confirmado y se requiere programar el despacho.                  | Responsable de Despacho y Transporte   | Fecha viable, responsable y programación.                      |
| **Determinar abastecimiento pendiente** | El pedido confirmado está condicionado por productos faltantes.                    | Responsable de Abastecimiento          | Cantidades pendientes y fecha prevista de disponibilidad.      |
| **Definir comprobante previsto**        | El cliente comunica si requiere boleta o factura.                                  | Atención y Ventas / Cliente            | Tipo de comprobante previsto y datos pendientes.               |
| **Autorizar condiciones de crédito**    | Se propone una condición crediticia nueva o excepcional.                           | Propietario de MIGAL                   | Condición y autorización registrada.                           |

Las decisiones comerciales y operativas no deben atribuirse exclusivamente al módulo técnico utilizado. CRM y ERP son responsabilidades funcionales dentro de una misma instancia de Odoo.

## 3. Cotizaciones y negociación comercial

### 3.1. Decisión de elaborar una cotización

`[TO-BE]` La existencia de una oportunidad comercial y de información suficiente sobre los productos **no implica que deba elaborarse automáticamente una cotización**.

Para elaborar una propuesta se requiere una de las siguientes situaciones:

- **Solicitud del cliente:** el cliente pide expresamente una cotización.
- **Propuesta de Atención y Ventas:** el vendedor ofrece elaborar una cotización y el cliente acepta.

Si faltan datos sobre productos, cantidades o condiciones necesarias, Atención y Ventas los solicita antes de emitir la propuesta.

La consulta de existencias se realiza como parte de la preparación de la cotización. No se exige verificar la disponibilidad de transporte para elaborarla.

**Criterio de transición:** una oportunidad alcanza la etapa de cotización únicamente cuando se ha elaborado efectivamente la propuesta solicitada o aceptada por el cliente.

Una consulta sobre precios referenciales no constituye necesariamente una solicitud de cotización formal.

### 3.2. Modificación de cotizaciones

`[TO-BE]` Cuando el cliente solicita cambios en una cotización ya presentada, se crea **otra cotización independiente en Odoo**, con su propio identificador y vinculada a la misma oportunidad comercial.

No se presupone un mecanismo automático de versionado mediante códigos consecutivos derivados de la primera cotización.

La trazabilidad comercial debe conservar:

- Oportunidad de origen.
- Identificador de cada cotización.
- Productos, cantidades, precios y condiciones ofrecidas.
- Relación con las cotizaciones anteriores.
- Identificación de la cotización finalmente aceptada.

**Ejemplo funcional:** un cliente recibe una cotización por 100 bolsas de cemento. Posteriormente solicita cambiar la cantidad a 120 bolsas. Atención y Ventas elabora otra cotización con un identificador propio, asociada a la misma oportunidad, sin eliminar la propuesta anterior.

La relación comercial se mantiene aunque existan varias cotizaciones. Estas no deben contabilizarse como oportunidades diferentes.

El modelo requiere conservar documentos distinguibles, pero no afirma que Odoo bloquee automáticamente la modificación o eliminación de cotizaciones anteriores.

### 3.3. Aceptación y cierre comercial

`[TO-BE]` Cuando el cliente acepta una propuesta, Atención y Ventas identifica la cotización concreta y registra la aceptación (EV-11).

Esta aceptación permite generar la solicitud de pedido (EV-14), pero **no confirma automáticamente el pedido ni convierte la oportunidad en ganada**.

La oportunidad se marca **Ganada** únicamente cuando el ERP confirma el pedido mediante una respuesta EV-15 aceptada o condicionada.

El cierre comercial como ganado no significa que los productos ya se hayan entregado o que la operación esté cobrada.

## 4. Decisiones sobre precios y descuentos excepcionales

### 4.1. Evidencia AS-IS

- `[E][AS-IS]` Los precios consideran costos recientes, tipo de producto, condiciones del mercado y características de la operación. `[FN-01]`
- `[E][AS-IS]` Los descuentos pueden depender de cantidades, relación con el cliente, mezcla de productos y conveniencia comercial. `[FN-01][FN-02]`
- `[E][AS-IS]` No existe un porcentaje máximo de descuento formalizado. `[FN-01]`
- `[E][AS-IS]` Ambos propietarios pueden autorizar ajustes comerciales. `[FN-01][FN-02]`
- `[E][AS-IS]` Una rebaja puede reflejarse directamente en el precio unitario, sin registrarse como descuento separado. `[FN-02]`
- `[E][AS-IS]` Los clientes pueden presentar precios de otras ferreterías durante la negociación. `[FN-02]`

### 4.2. Mejora propuesta

`[TO-BE]` Cuando se solicite un precio excepcional, Atención y Ventas deberá consultar al propietario y conservar la decisión correspondiente.

La evidencia mínima propuesta comprende:

| Dato                    | Finalidad                                          |
| ----------------------- | -------------------------------------------------- |
| Cotización identificada | Relacionar la decisión con la propuesta comercial. |
| Producto y cantidad     | Identificar la línea afectada.                     |
| Precio de referencia    | Conocer el precio utilizado inicialmente.          |
| Precio ofrecido         | Registrar el precio autorizado para el cliente.    |
| Diferencia              | Conocer el ajuste concedido.                       |
| Motivo                  | Justificar la negociación o excepción.             |
| Propietario autorizador | Identificar quién aprobó el ajuste.                |
| Fecha                   | Conservar la trazabilidad de la decisión.          |

Si el ajuste modifica una cotización ya presentada, se elabora otra cotización independiente asociada a la misma oportunidad.

No se establece un límite fijo de descuento del 5 %, 10 % u otro porcentaje, porque no existe evidencia de una política formal aprobada.

La autorización excepcional se vincula con EXC-01 y los controles comerciales correspondientes.

### 4.3. Vigencia y actualización de precios

`[E][AS-IS]` No se ha identificado una vigencia universal fija para las cotizaciones de MIGAL. Los precios pueden variar antes de concretarse la operación. `[FN-01][FN-02]`

`[TO-BE]` Cuando se produzca una actualización relevante de precios antes de la confirmación o pago, la propuesta debe revisarse con el cliente cuando corresponda.

Si deben modificarse condiciones ya ofrecidas, se crea otra cotización y se identifica cuál fue aceptada.

No se presume una vigencia automática de tres días ni otro plazo general sin aprobación de los propietarios.

## 5. Disponibilidad de productos y validación de pedidos

### 5.1. Consulta comercial de existencias

`[TO-BE]` Antes de elaborar una cotización, Atención y Ventas consulta la disponibilidad registrada de los productos mediante el módulo Inventario de Odoo.

La consulta permite identificar:

- Producto y cantidad requerida.
- Existencias registradas.
- Disponibilidad por ubicación, cuando corresponda.
- Diferencias o faltantes identificados.

La consulta de existencias **no constituye una reserva de inventario** ni garantiza que el stock permanezca disponible hasta la confirmación del pedido.

Si se detecta una discrepancia entre las existencias registradas y las físicas, debe comunicarse para su verificación conforme a EXC-02.

La existencia de productos faltantes no impide necesariamente elaborar una cotización, porque el modelo contempla pedidos condicionados por abastecimiento.

### 5.2. Política de abastecimiento condicionado

`[TO-BE]` Una vez aceptada la cotización (EV-11), Atención y Ventas genera la solicitud de pedido (EV-14).

El ERP valida las cantidades solicitadas, consulta la disponibilidad registrada y revisa las categorías de los productos faltantes.

La política funcional acordada establece cuatro categorías que pueden originar directamente un pedido condicionado:

**Cemento, fierro, agregados y ladrillos.**

| Resultado de la comprobación                                                                               | Decisión                                                                            |
| ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Todas las cantidades solicitadas cuentan con stock suficiente.                                             | Respuesta **Aceptada**. El pedido se confirma.                                      |
| Existe stock insuficiente y todos los productos faltantes pertenecen a las cuatro categorías establecidas. | Respuesta **Condicionada**. El pedido se confirma con compromiso de abastecimiento. |
| Existe stock insuficiente y al menos un producto faltante pertenece a otra categoría.                      | La decisión se **escala al propietario** antes de confirmar.                        |
| El propietario autoriza la operación excepcional.                                                          | Respuesta **Condicionada**. El pedido se confirma y se registra la autorización.    |
| El propietario rechaza la operación excepcional.                                                           | Respuesta **Rechazada**. No se confirma el pedido.                                  |

El criterio depende de **las categorías de los productos faltantes**, no de todas las categorías presentes en el pedido.

Por ejemplo, si se solicitan cemento y una herramienta, pero solo falta cemento, corresponde la condición automática de abastecimiento. Si también falta la herramienta, la solicitud debe escalarse al propietario.

El escalamiento no representa una cuarta respuesta definitiva EV-15; es una decisión excepcional previa a la confirmación.

### 5.3. Confirmación y compromiso de abastecimiento

Las respuestas finales EV-15 son:

- **Aceptada:** pedido confirmado con disponibilidad suficiente.
- **Condicionada:** pedido confirmado con obligación de abastecer las cantidades faltantes.
- **Rechazada:** solicitud sin pedido confirmado.

La respuesta condicionada representa una aceptación definitiva sujeta a abastecimiento. **No requiere convertirse posteriormente en aceptada**.

La confirmación no implica que los productos faltantes existan físicamente ni que estén reservados.

El abastecimiento pendiente deberá coordinarse y documentarse. Las nuevas cantidades ingresarán al inventario únicamente cuando se valide su recepción física.

`[TO-BE]` La consulta de stock y la aplicación ordinaria de categorías se proponen como automatizadas en el ERP, sin aprobación manual adicional. La validación excepcional corresponde al propietario cuando se identifican faltantes fuera de las cuatro categorías.

La implementación técnica de esta lógica debe verificarse en Odoo 18 Community. No se afirma que sus controles personalizados estén disponibles automáticamente sin configuración o desarrollo.

El detalle operativo de EV-14 y EV-15 corresponde a [pedido de cliente](../o2c/pedido.md) y al [contrato de eventos INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md).

## 6. Coordinación de despacho y abastecimiento

### 6.1. Evidencia AS-IS

`[E][AS-IS]` La capacidad de entrega depende de factores como existencia de materiales, disponibilidad de vehículos y personal, características de la carga, ubicación del cliente y entregas previamente comprometidas. `[FN-01][FN-02]`

Los propietarios participan en las decisiones relacionadas con la operación y coordinación de entregas. `[FN-01][FN-02]`

### 6.2. Mejora propuesta

`[TO-BE]` La coordinación logística comienza **después de confirmar el pedido**, no durante la elaboración de la cotización.

Las responsabilidades son:

| Responsable funcional     | Decisión o información proporcionada                                                                                         |
| ------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Atención y Ventas**     | Solicita la programación y comunica al cliente la fecha coordinada.                                                          |
| **Despacho y Transporte** | Determina la fecha viable de despacho o entrega según vehículos, carga, capacidad y programación.                            |
| **Abastecimiento**        | Cuando existe un pedido condicionado, consulta a proveedores e informa la fecha prevista de disponibilidad de los faltantes. |

En pedidos aceptados con stock suficiente, Despacho determina la fecha viable según la disponibilidad logística.

En pedidos condicionados, Abastecimiento informa cuándo se espera contar con los productos faltantes. Despacho utiliza esa información para coordinar la entrega y Atención y Ventas comunica el resultado al cliente.

### 6.3. Condiciones de programación

`[TO-BE]` Los despachos se programan desde el día siguiente a la confirmación del pedido, considerando la disponibilidad de productos y transporte.

Esta regla no significa que todos los pedidos deban entregarse exactamente al día siguiente.

Tampoco se fija una fecha universal de reposición para pedidos condicionados. La fecha dependerá de la información proporcionada por Abastecimiento y de la programación posterior de Despacho.

La evidencia esperada comprende:

- Pedido confirmado.
- Productos y cantidades por entregar.
- Cantidades pendientes de abastecimiento, cuando corresponda.
- Fecha prevista de disponibilidad informada por Abastecimiento.
- Fecha viable definida por Despacho.
- Responsable de la coordinación.
- Comunicación de la fecha al cliente.

No se exige comprobar la disponibilidad de transporte antes de elaborar una cotización.

La programación y ejecución de entregas se desarrollan en el [flujo O2C](../o2c/flujo.md).

## 7. Condición de facturación y pago

### 7.1. Evidencia AS-IS

- `[E][AS-IS]` MIGAL utiliza KeyFacil para la emisión de comprobantes electrónicos. `[FN-02]`
- `[E][AS-IS]` Los propietarios realizan esta actividad. `[FN-02]`
- `[E][AS-IS]` El cliente comunica si necesita boleta o factura. `[FN-02]`
- `[E][AS-IS]` Se solicitan los datos de identificación que correspondan al comprobante requerido. `[FN-02]`

### 7.2. Mejora propuesta

`[TO-BE]` Durante la cotización, Atención y Ventas identifica la condición de facturación prevista, sin exigir anticipadamente información que todavía no resulte necesaria.

| Dato                 | Tratamiento                                                                       |
| -------------------- | --------------------------------------------------------------------------------- |
| Comprobante previsto | Boleta, factura o por definir.                                                    |
| Datos fiscales       | Solicitar cuando sean necesarios para emitir el comprobante correspondiente.      |
| Condición de pago    | Registrar la condición consultada o acordada.                                     |
| Crédito excepcional  | Requiere decisión expresa del propietario conforme a RN-15.                       |
| Comprobante emitido  | Registrar y conciliar la referencia de KeyFacil con la operación correspondiente. |

La emisión fiscal continúa realizándose mediante **KeyFacil**, como sistema externo.

El módulo Facturación de Odoo se considera para registros internos y conciliación, sin presumir integración automática con KeyFacil ni emisión fiscal equivalente.

No se establece una fórmula de recargo crediticio ni una política de crédito no aprobada.

El detalle de la emisión y conciliación corresponde a [facturación O2C](../o2c/facturacion.md).

## 8. Principio de responsabilidades y controles

`[I]` MIGAL es una empresa familiar cuya estructura comprende dos propietarios y dos trabajadores. No se deben inventar departamentos o cargos adicionales para describir su operación.

`[TO-BE]` Las denominaciones Atención y Ventas, Despacho y Transporte, y Abastecimiento representan **responsabilidades funcionales**, asignables a personas existentes.

Se adoptan los siguientes principios:

- Atención y Ventas gestiona la relación comercial y no requiere aprobación del propietario para cada consulta ordinaria de stock.
- El propietario autoriza excepciones de precio, decisiones crediticias y pedidos con faltantes fuera de las cuatro categorías de abastecimiento.
- El ERP realiza la validación automatizada propuesta de disponibilidad y categorías, siempre que sus reglas hayan sido configuradas y verificadas.
- Despacho y Abastecimiento definen las condiciones operativas necesarias después de confirmar el pedido.
- La aceptación comercial del cliente y la confirmación operativa del pedido son eventos diferentes.
- La oportunidad se marca Ganada únicamente cuando existe un pedido confirmado, sea aceptado o condicionado.
- Las cotizaciones independientes, autorizaciones y decisiones de pedido deben conservar referencias que permitan reconstruir la operación.

Los perfiles y permisos técnicos deben ser coherentes con el [modelo de autorización](../../arquitectura/seguridad/modelo-autorizacion.md).

## 9. Límites y validaciones técnicas pendientes

Las decisiones funcionales anteriores forman parte de la propuesta TO-BE. Su documentación no acredita que los controles hayan sido implementados en Odoo.

Permanecen pendientes:

- Verificar la creación de varias cotizaciones independientes asociadas a una oportunidad y la conservación de sus referencias.
- Comprobar los mecanismos efectivos para registrar y restringir autorizaciones de descuentos excepcionales.
- Verificar la consulta de existencias por ubicación y el tratamiento de discrepancias físicas.
- Configurar o implementar la clasificación de productos faltantes según las cuatro categorías establecidas.
- Configurar o implementar el escalamiento al propietario para solicitudes con faltantes fuera de esas categorías.
- Probar EV-14 y EV-15, incluida la confirmación de pedidos condicionados y la conservación de autorizaciones excepcionales.
- Verificar el comportamiento de reservas, demanda pendiente y recepciones de abastecimiento.
- Definir el registro de fechas proporcionadas por Despacho y Abastecimiento.
- Validar los permisos técnicos de los propietarios y responsables operativos.
- Alinear los documentos O2C, INT-01 y el catálogo de reglas para evitar contradicciones con estas decisiones.

No se presume automatización de WhatsApp, compras mediante el módulo `purchase`, integración automática con KeyFacil ni bloqueos de Odoo que no hayan sido configurados o probados.

## 10. Reglas y documentos relacionados

| Referencia                                                                    | Aplicación                                                    |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------- |
| [Flujo comercial](flujo-comercial.md)                                         | Etapas comerciales, responsables y transiciones.              |
| [Cuenta, contacto y canales](cuenta-contacto-canales.md)                      | Identificación de clientes, participantes y seguimiento.      |
| [Catálogo de reglas](../catalogo-reglas.md)                                   | RN-01 a RN-06, RN-08, RN-15, EXC-01 y EXC-02.                 |
| [Pedido de cliente](../o2c/pedido.md)                                         | Solicitud, validación y confirmación de pedidos.              |
| [INT-01 CRM–ERP](../../arquitectura/integraciones/INT-01-CRM-ERP.md)          | Eventos EV-11, EV-14 y EV-15 y frontera funcional.            |
| [Flujo O2C](../o2c/flujo.md)                                                  | Ejecución posterior del pedido, entrega, comprobante y cobro. |
| [Modelo de autorización](../../arquitectura/seguridad/modelo-autorizacion.md) | Responsabilidades, autorizaciones y segregación de funciones. |
| [Fuentes del negocio](../../fuentes/fuentes-negocio.md)                       | Evidencias AS-IS utilizadas como base.                        |

Las reglas comerciales y de abastecimiento deberán actualizarse en sus documentos especializados para mantener una única interpretación durante la revisión académica y las futuras pruebas de Odoo.

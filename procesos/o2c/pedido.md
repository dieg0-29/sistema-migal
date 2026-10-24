# Pedido de cliente

## 1. Propósito y alcance

`[TO-BE]` Formalizar una cotización aceptada como pedido confirmado, conservando las condiciones acordadas y distinguiendo las cantidades disponibles de las pendientes de abastecimiento.

Este documento desarrolla los controles propios de la solicitud, confirmación y modificación del pedido. Las etapas comerciales corresponden al [flujo comercial](../comercial-crm/flujo-comercial.md); las políticas de cotización, autorización y abastecimiento condicionado, a [decisiones comerciales](../comercial-crm/decisiones-comerciales.md).

El proceso comienza con una cotización aceptada identificable y concluye con la confirmación del pedido o el rechazo de la solicitud. Las solicitudes que requieren resolver información o autorizaciones permanecen pendientes antes de obtener una respuesta definitiva.

## 2. Preparación de la solicitud

Atención y Ventas comprueba que la solicitud corresponda a la cotización efectivamente aceptada: cliente, productos, unidades, cantidades, precios y condiciones comerciales deben conservar lo acordado.

Si existen varias cotizaciones vinculadas a la oportunidad, se identifica la aceptada mediante su propio identificador. La solicitud no debe basarse en una propuesta sustituida sin una nueva conformidad del cliente.

Antes de remitir la solicitud, deben estar resueltas las autorizaciones comerciales exigibles y los datos necesarios para la operación. Cuando un contacto actúe por cuenta del cliente, se verifica su facultad para comunicar la aceptación según corresponda.

La solicitud EV-14 y la respuesta EV-15 conservan las referencias y evidencias definidas en [INT-01 CRM–ERP](../../arquitectura/integraciones/INT-01-CRM-ERP.md). No se exige una fecha de entrega ya programada para generar la solicitud.

## 3. Validación y formalización del pedido

### 3.1. Disponibilidad para la solicitud

`[TO-BE]` El ERP revalida la disponibilidad al procesar la solicitud. La consulta realizada durante la cotización constituye un antecedente y no garantiza que las cantidades continúen disponibles.

La comprobación debe distinguir:

| Concepto | Tratamiento |
|---|---|
| Existencias registradas | Cantidades incorporadas al inventario mediante movimientos validados. |
| Cantidades reservadas o comprometidas | Cantidades cuya asignación a otras operaciones debe considerarse al determinar la disponibilidad utilizable. |
| Cantidades disponibles para la solicitud | Cantidades que pueden asignarse al pedido según las ubicaciones y reglas de disponibilidad aplicables. |
| Cantidades faltantes | Parte de lo solicitado que no puede cubrirse con la disponibilidad utilizable. |

La comparación debe utilizar unidades de medida compatibles. El cálculo técnico de disponibilidad y el tratamiento de compromisos sin reserva deberán verificarse en la instancia.

Cuando se detecte una discrepancia entre el registro y las existencias físicas, se aplica EXC-02 y se revisa su efecto sobre las cantidades utilizadas para decidir.

### 3.2. Decisión y confirmación

Con los faltantes identificados, el ERP aplica la política de abastecimiento y escalamiento definida en [decisiones comerciales](../comercial-crm/decisiones-comerciales.md#52-política-de-abastecimiento-condicionado).

La respuesta EV-15 aceptada o condicionada permite confirmar el pedido; la rechazada no lo confirma. Si corresponde intervención del propietario, su decisión debe registrarse antes de completar la confirmación.

La formalización debe conservar el vínculo entre la solicitud, la respuesta y el pedido resultante. Emitir una respuesta favorable sin completar la confirmación técnica no basta para considerar terminado el proceso.

No se exige crear un objeto separado para cada evento: la transición puede conservarse en el mismo registro si la implementación permite reconstruir sus condiciones y evidencias.

### 3.3. Cantidades después de confirmar

El pedido confirmado debe permitir distinguir lo que puede atenderse con disponibilidad existente y lo que queda pendiente de abastecimiento.

La confirmación no acredita por sí sola una reserva completa. Las cantidades faltantes no pueden registrarse como existencias físicas ni como reservas efectivas.

En pedidos condicionados, el cumplimiento se sigue mediante la reducción de cantidades pendientes y las operaciones de recepción y entrega correspondientes. No se requiere cambiar la respuesta original de condicionada a aceptada.

La coordinación de abastecimiento, reservas, programación y entregas continúa en el [flujo O2C](flujo.md). El resultado de la confirmación se comunica a Comercial para aplicar el cierre definido en el [flujo comercial](../comercial-crm/flujo-comercial.md).

## 4. Cambios y cancelaciones

Los cambios deben tratarse según el momento en que se solicitan y las operaciones que ya se hayan ejecutado.

| Momento o situación | Control requerido |
|---|---|
| **Cambio antes de confirmar** | Si modifica lo ofrecido, aplicar el procedimiento de nueva cotización de [decisiones comerciales](../comercial-crm/decisiones-comerciales.md#32-modificación-de-cotizaciones), conservar la aceptación correspondiente y actualizar la solicitud que se someterá a validación. |
| **Cambio después de confirmar** | Revisar el efecto sobre condiciones comerciales, disponibilidad, reservas, abastecimiento y entregas. Registrar motivo, situación anterior, cambio aprobado y conformidad del cliente cuando se modifique lo acordado. |
| **Cambio con operaciones ya ejecutadas** | Identificar movimientos, entregas, comprobantes y pagos afectados antes de modificar el pedido. Las regularizaciones deben conservar referencias a la operación original. |
| **Cancelación** | Revisar compromisos y operaciones existentes, liberar o regularizar lo que corresponda y conservar el motivo y la evidencia del cierre. No eliminar el historial del pedido. |

El procedimiento técnico de modificación o cancelación deberá validarse en Odoo. No se presupone que editar el pedido revierta automáticamente reservas, movimientos, comprobantes o pagos.

## 5. Validaciones técnicas pendientes

Las pruebas específicas de este documento deben demostrar:

- **Correspondencia:** el pedido conserva el cliente, productos, cantidades y condiciones de la cotización aceptada, junto con sus referencias de solicitud y respuesta.
- **Disponibilidad:** el cálculo considera unidades, ubicaciones y compromisos existentes sin asignar dos veces las mismas cantidades.
- **Confirmación efectiva:** las respuestas aceptada y condicionada producen un pedido confirmado; el rechazo o una autorización pendiente no lo confirman.
- **Cantidades pendientes:** la confirmación condicionada permite distinguir faltantes sin generar existencias o reservas ficticias.
- **Modificaciones y cancelaciones:** los cambios conservan la trazabilidad y consideran las operaciones previamente ejecutadas.

Las pruebas del contrato EV-14/EV-15 corresponden a [INT-01](../../arquitectura/integraciones/INT-01-CRM-ERP.md); las de programación y ejecución posterior, al [flujo O2C](flujo.md).

Este documento describe el diseño funcional propuesto y no acredita que los controles estén configurados o implementados.

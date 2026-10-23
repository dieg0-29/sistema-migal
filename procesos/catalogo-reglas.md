# Catálogo de reglas de negocio

## 1. Metadatos

* **Fecha de creación:** 17/10/2026
* **Última actualización:** 23/10/2026
* **Versión:** 0.3 (propuesta de alineación)

## 2. Propósito

Este catálogo es la **única numeración** de reglas de negocio (RN), sus controles (CT) y las excepciones (EXC) de todos los procesos. Los flujos, casos de prueba, la [matriz de trazabilidad](../datos/trazabilidad/plan-trazabilidad-auditoria.md) y las integraciones citan estos códigos. Cada control CT-nn verifica la regla RN-nn del mismo número.

Las reglas son propuestas funcionales: que una regla exista no significa que Odoo la haga cumplir automáticamente. Su aplicación se comprueba en los casos de prueba.

## 3. Reglas del proceso comercial (CRM)

Detalle de las decisiones que originan estas reglas: [decisiones comerciales](comercial-crm/decisiones-comerciales.md).

| ID | Regla | Etapa | Motivo | Sistema | Control | Consecuencia si se viola | Responsable |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RN-01** | **Contactos y necesidades sin duplicados:** Buscar y reutilizar el contacto existente. Evitar registrar dos veces la misma necesidad comercial; una nueva compra u obra puede generar otro lead u oportunidad del mismo contacto. Una coincidencia de nombre o celular requiere revisar identidad y contexto, sin fusionar automáticamente. | ETP-01: Nuevo (Lead) | Conservar una identidad compartida sin confundir nuevas necesidades con duplicados. | Contactos/CRM | CT-01 | Fichas duplicadas o necesidades distintas fusionadas indebidamente; R-ATN revisa las coincidencias antes de crear o fusionar. | R-ATN |
| **RN-02** | **Calificación y próxima acción:** Distinguir Lead calificado de Oportunidad según los criterios de INT-01, §4.1. Toda oportunidad activa debe tener una próxima acción con fecha y responsable. | Lead calificado / Oportunidad (referencia ETP-02) | Ubicar cada necesidad según su avance real y mantener el seguimiento. | CRM | CT-02 | Etapa sin fundamento o seguimiento pendiente; R-ATN revisa la información y programa la próxima acción. | R-ATN |
| **RN-03** | **Precios de Lista Vigente:** Los precios de referencia proceden de la lista vigente; cualquier precio ofrecido distinto conserva su justificación y autorización cuando corresponda. Los precios están sujetos a actualización mientras no exista pago o confirmación formal; un cambio se documenta mediante RN-04 y no altera silenciosamente condiciones ya aceptadas. | ETP-03: Propuesta (Cotización) | Proteger el margen y conservar las condiciones acordadas. | CRM/ERP (Ventas) | CT-03 | Precio desactualizado o cambio sin trazabilidad; R-ATN revisa la lista y R-PRO resuelve excepciones antes de comunicar nuevas condiciones. | R-ATN / R-PRO |
| **RN-04** | **Versionado de Cotizaciones:** Todo cambio relevante de cantidades, productos o precios tras emitir una cotización genera una nueva versión, conservando la anterior y el motivo. Si modifica condiciones ya aceptadas, requiere nueva aceptación del cliente. | ETP-03: Propuesta (Cotización) | Mantener un historial auditable de la negociación. | ERP/CRM | CT-04 | Pérdida de las condiciones anteriores; R-ATN conserva las versiones y sus referencias sin sobrescribir ni eliminar la historia. | R-ATN |
| **RN-05** | **Consulta y promesa de disponibilidad:** Antes de emitir cada cotización o versión, R-ATN consulta la disponibilidad de **todos los productos** por ubicación. Solo se comunica una promesa firme con verificación operativa y autorización correspondiente. Si falta cemento, fierro, agregados o ladrillos, se puede cotizar **condicionado a abastecimiento**, sin afirmar reserva física ni fecha garantizada. | ETP-03: Propuesta (Cotización) | Evitar cotizaciones sin respaldo y promesas de despacho imposibles de cumplir. | CRM/ERP | CT-05 | Cotización sin consulta previa o promesa sin fundamento; control técnico pendiente de prueba. | R-ATN / R-PRO |
| **RN-06** | **Condición de Facturación Prevista:** Antes de emitir la cotización, se debe consultar si la eventual compra requerirá Boleta o Factura (aunque no se exija RUC en ese instante). | ETP-03: Propuesta (Cotización) | Preparar administrativamente el cierre del pedido y anticipar si se tratará de una cuenta empresa o persona natural. | CRM | CT-06 | Comprobante previsto omitido; R-ATN registra `Boleta`, `Factura` o `Por Definir`, según la información disponible. | R-ATN |


**Etapas e información disponible.** Las etapas comerciales propuestas son **Lead → Lead calificado → Oportunidad → Cotización**, con los criterios de [INT-01, §4.1](../arquitectura/integraciones/INT-01-CRM-ERP.md). El lead se califica al identificar una necesidad material concreta; se trabaja como oportunidad al explorar las condiciones pertinentes y establecer continuidad comercial. R-ATN mapea la información disponible, los datos faltantes y los criterios cumplidos para ubicar la necesidad en su etapa correspondiente. Puede aprovechar datos vigentes del contacto y avanzar en la misma atención, sin repetir preguntas ni crear un registro por etapa. Las referencias ETP-01 a ETP-03 se conservan para mantener los vínculos existentes; no sustituyen esta distinción entre las cuatro etapas.

**Preparación de cotización.** Se elabora cuando el cliente la solicita o acepta que MIGAL se la prepare al ofrecérsela, con productos y cantidades suficientes, cliente vinculado en Contactos y consulta de disponibilidad de todos los productos antes de emitirla. Aceptar su preparación no equivale a aceptar sus precios y condiciones (EV-11).

**Resultado comercial.** Al cerrar una oportunidad como perdida o registrar la no adjudicación total o parcial de una cotización, se conserva la referencia afectada y el motivo conocido o «Motivo desconocido». «Sin respuesta» describe lo observado durante el seguimiento y no permite atribuir una causa adicional sin evidencia; el competidor se registra solo si se conoce.

Los controles descritos son verificaciones funcionales a cargo del responsable; sus avisos, bloqueos o automatizaciones deben comprobarse antes de presentarlos como configurados.

Si un producto fuera de las cuatro categorías indicadas en RN-05 presenta stock insuficiente, la oferta queda en revisión hasta resolver disponibilidad o alternativa; la consulta previa se realiza incluso si el cliente no pregunta por una fecha. El faltante conocido **no es EXC-02**, que corresponde a discrepancias entre el registro y la existencia física.

## 4. Reglas de Order to Cash (ERP)

Aplican a la transición desde la cotización aceptada: se registra la **solicitud de pedido (EV-14)**, el ERP revalida las condiciones y devuelve una **respuesta (EV-15)**. La aceptación comercial no confirma automáticamente el pedido ([INT-01](../arquitectura/integraciones/INT-01-CRM-ERP.md), F-02).

| ID | Regla | Etapa | Motivo | Sistema | Control | Consecuencia si se viola | Responsable |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RN-07** | **Identificación según la operación:** El cliente se identifica con los datos que exija la operación (entrega, comprobante) y se conserva el origen de cada dato. | Pedido | Evitar exigir datos innecesarios y, a la vez, emitir comprobantes con identidad correcta. | CRM/ERP | CT-07 | Pedido o comprobante con identidad incompleta o sin origen trazable. | R-ATN / R-PRO |
| **RN-08** | **Aceptación y validación vinculadas:** La versión aceptada origina la solicitud EV-14; el ERP revalida stock, autorizaciones y viabilidad de entrega y emite EV-15 (aceptada, rechazada o condicionada). **Solo la validación operativa aceptada habilita la confirmación del pedido**, conservando la versión y condiciones aceptadas. | Pedido | Asegurar que lo ejecutado coincide con lo acordado y con las condiciones operativas verificadas. | ERP | CT-08 | Pedido confirmado sin aceptación o sin revalidación; pérdida de trazabilidad entre versión, solicitud y respuesta. | R-ATN / R-PRO |
| **RN-09** | **Reserva por ubicación:** El pedido se relaciona con disponibilidad y reserva de existencia **física apta** en Tienda, Almacén 1 y Almacén 2. La **demanda no cubierta** se registra aparte y no equivale a reserva; un pedido confirmado no acredita por sí solo reserva física completa. | Reserva | Evitar comprometer stock inexistente o de otra ubicación. | ERP | CT-09 | Promesas incumplidas, reservas ficticias y descuadres entre ubicaciones. | R-ALM / R-PRO |
| **RN-10** | **Entrega real:** Se registran las cantidades realmente entregadas y las diferencias físicas. | Entrega | Mantener visible lo pendiente y la exactitud del inventario. | ERP | CT-10 | Saldos pendientes ocultos e inventario inexacto. | R-ALM |
| **RN-11** | **Conciliación con KeyFacil:** La referencia del comprobante emitido en KeyFacil se concilia con el pedido y sus importes. | Comprobante | Evitar doble emisión o diferencias entre lo vendido y lo facturado. | ERP / KeyFacil | CT-11 | Descuadres fiscales y comprobantes sin operación de origen. | R-FAC |
| **RN-12** | **Cobro documentado:** Cada cobro se registra con soporte verificable y saldo resultante. | Cobro | Conocer lo pendiente por cobrar. | ERP | CT-12 | Saldos incorrectos y cobros sin respaldo. | R-FAC |
| **RN-13** | **Posventa vinculada:** Todo caso de posventa se relaciona con el pedido de origen. | Posventa | Resolver reclamos con el contexto de la venta. | ERP | CT-13 | Devoluciones o notas sin sustento. | R-ATN / R-PRO |
| **RN-14** | **Cierre verificado:** El pedido se cierra solo con entrega, cobro e incidencias resueltos. | Cierre | Evitar cierres con pendientes. | ERP | CT-14 | Operaciones cerradas con saldos o entregas pendientes. | R-PRO |
| **RN-15** | **Decisión de crédito:** Toda condición de crédito nueva o modificada requiere decisión expresa de un propietario. | Pedido | Evitar financiación no autorizada. | ERP | CT-15 | Ventas a crédito sin respaldo (ver SOD-04). | R-PRO |

## 5. Reglas de Procure to Pay (ERP)

| ID | Regla | Etapa | Evidencia esperada | Sistema | Control | Consecuencia si se viola | Responsable |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RN-16** | **Toda reposición tiene necesidad identificable.** | P2P | Producto, unidad, cantidad y causa (stock mínimo, venta o programación) | ERP | CT-16 | Necesidad, recepción o pago sin respaldo verificable. | R-ALM / R-PRO |
| **RN-17** | **Revisar existencias y compras pendientes antes de repetir compra.** | P2P | Referencias consultadas o incertidumbre | ERP | CT-17 | Necesidad, recepción o pago sin respaldo verificable. | R-PRO / R-ALM |
| **RN-18** | **Confirmar proveedor, precio y condiciones antes del compromiso.** | P2P | Oferta y decisión; cambios requieren nueva constancia | ERP | CT-18 | Necesidad, recepción o pago sin respaldo verificable. | R-PRO |
| **RN-19** | **Registrar cantidad real recibida, no copiar cantidad comprada sin contar.** | P2P | Conteo, daños y ubicación | ERP | CT-19 | Necesidad, recepción o pago sin respaldo verificable. | R-ALM |
| **RN-20** | **Diferencias mantienen saldo físico o excepción.** | P2P | Cantidad pendiente y resolución | ERP | CT-20 | Necesidad, recepción o pago sin respaldo verificable. | R-ALM / R-PRO |
| **RN-21** | **Separar recepción de ajuste de inventario.** | P2P | Documento de entrada; ajuste con causa/autorización | ERP | CT-21 | Necesidad, recepción o pago sin respaldo verificable. | R-ALM / R-PRO |
| **RN-22** | **Cotejar compra, recepción y documento antes de cerrar obligación.** | P2P | Coincidencias y discrepancias | ERP | CT-22 | Necesidad, recepción o pago sin respaldo verificable. | R-FAC / R-PRO |
| **RN-23** | **Verificar pago y aplicación sin duplicidades.** | P2P | Soporte, monto, obligación y saldo | ERP | CT-23 | Necesidad, recepción o pago sin respaldo verificable. | R-PRO |
| **RN-24** | **Mantener trazabilidad de anulaciones/devoluciones.** | P2P | Motivo, decisión y efecto físico/económico | ERP | CT-24 | Necesidad, recepción o pago sin respaldo verificable. | R-PRO / R-ALM |
| **RN-25** | **Cerrar con saldo resuelto o excepción documentada.** | P2P | Estado físico y económico separados | ERP | CT-25 | Necesidad, recepción o pago sin respaldo verificable. | R-PRO |

## 6. Excepciones

| ID | Excepción | Tratamiento | Acción en el sistema |
| :--- | :--- | :--- | :--- |
| **EXC-01** | **Excepción de Descuento (Igualar competencia):** Cliente o maestro presenta una referencia de precio menor de otra ferretería. | R-PRO evalúa y aprueba o rechaza considerando cantidad, relación con el cliente, mezcla de productos y reparto; registra motivo y condiciones. No se fija un porcentaje máximo sin validación de los propietarios. | Registrar la solicitud y decisión con responsable y fecha; si se modifica una cotización ya emitida, conservar nueva versión según RN-04. La automatización de la aprobación está pendiente de prueba. |
| **EXC-02** | **Sin Stock por Desfase (Inventario Fantasma):** El sistema muestra stock para cotizar, pero el vendedor sabe (por conocimiento físico) que el producto está dañado o no hay la cantidad suficiente. | El vendedor no compromete el producto faltante, notifica la discrepancia a almacén y ofrece un sustituto o deja pendiente en la proforma. | Registrar la observación y notificar a almacén para comprobar la discrepancia. Cualquier ajuste requiere causa y autorización; no se presume una alerta ni un ajuste automático. |
| **EXC-03** | **Quiebre de margen por alza de costo (Fuerza mayor / MTO):** El costo de adquisición de un material volátil (ej. fierro) se incrementa abruptamente por factores externos después de emitida o aceptada la cotización, pero antes del pago del cliente, volviendo negativo el margen proyectado. | R-PRO evalúa la situación. Decide si asume la pérdida momentánea por estrategia de fidelización, o si se ampara en RN-03 para comunicar la fuerza mayor y renegociar el precio (RN-04). | El sistema alerta/bloquea el despacho por margen negativo. R-PRO autoriza excepcionalmente el margen (desbloqueo) o instruye generar una nueva versión de cotización (`COT-XXX-V02`) con los precios ajustados. |

## 7. Trazabilidad

| Reglas | Origen | Se verifican en |
| :--- | :--- | :--- |
| RN-01 a RN-06 | [Flujo comercial](comercial-crm/flujo-comercial.md) y [decisiones comerciales](comercial-crm/decisiones-comerciales.md) | [INT-01](../arquitectura/integraciones/INT-01-CRM-ERP.md), [plan de trazabilidad](../datos/trazabilidad/plan-trazabilidad-auditoria.md) (TR-01 a TR-03), [CP-01](../pruebas/casos/CP-01-caso-normal.md) |
| EXC-01 | [Decisiones comerciales](comercial-crm/decisiones-comerciales.md) | SOD-01 del [modelo de autorización](../arquitectura/seguridad/modelo-autorizacion.md), TR-04, [CP-02](../pruebas/casos/CP-02-caso-excepcion.md) |
| EXC-02 | [Decisiones comerciales](comercial-crm/decisiones-comerciales.md) | SOD-03 del [modelo de autorización](../arquitectura/seguridad/modelo-autorizacion.md), [flujo de inventario](inventario/flujo.md); caso de descuadre pendiente de definir |
| EXC-03 | [Decisiones comerciales](comercial-crm/decisiones-comerciales.md) (Caso de quiebre) | TR-04 (Aprobación comercial), CT-03 y CT-04. Caso de quiebre a exponer en PC2. |
| RN-07 a RN-15 | [Flujo O2C](o2c/flujo.md), [pedido](o2c/pedido.md), [facturación](o2c/facturacion.md) | [Plan de trazabilidad](../datos/trazabilidad/plan-trazabilidad-auditoria.md) (TR-05 a TR-11); casos por definir |
| RN-16 a RN-25 | [Flujo P2P](p2p/flujo.md) | [Eventos y controles P2P](p2p/eventos-controles.md); casos por definir |

## 8. Equivalencias con códigos retirados

Las reglas O2C-R y P2P-R se integraron en este catálogo y ya no se usan.

| Código retirado | Ahora |
| :--- | :--- |
| O2C-R-01 | RN-07 |
| O2C-R-02 | RN-03 y DQ-04 |
| O2C-R-03 | RN-04 |
| O2C-R-04 | EXC-01 |
| O2C-R-05 | RN-05 |
| O2C-R-06 | RN-08 |
| O2C-R-07 | RN-09 y RN-10 |
| O2C-R-08 | RN-11 |
| O2C-R-09 | RN-12 |
| O2C-R-10 | RN-14 |
| P2P-R-01 a P2P-R-10 | RN-16 a RN-25 |

## 9. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 17/10/2026 | Alejandro Flores | Versión inicial; reúne las reglas de CRM, O2C y P2P |
| 0.2 | 23/10/2026 | Revisión de alineación propuesta | Precisiones de RN-05, RN-08, RN-09 y RN-16, frontera EV-14/EV-15 y trazabilidad de EXC-02; sin renumerar reglas ni controles |
| 0.3 | 23/10/2026 | — | Alineación con INT-01 v0.6: Contactos y múltiples necesidades por cliente, etapas y mapeo de información, condición para cotizar, motivo desconocido, versionado por precios y controles funcionales; precisión de EXC-01/EXC-02 y retirada de autoría inicial |

# Políticas operativas y relación con las reglas de negocio — MIGAL

**Estado:** `[AC][TO-BE]` — Acuerdos del equipo pendientes de validación con los propietarios y de prueba técnica cuando corresponda.  
**Propósito:** Alinear las políticas de CRM, O2C y P2P con las reglas `RN`, controles `CT` y excepciones `EXC` del [catálogo vigente](catalogo-reglas.md).

## 1. Políticas operativas

| Código | Política | Definición funcional | Responsable propuesto |
|---|---|---|---|
| **POL-01** | Identificación del lead | Si no se conoce el nombre, registrar un alias y un identificador único; comprobar posibles duplicados. No exigir DNI/RUC al primer contacto. | R-ATN |
| **POL-02** | Venta a crédito | Registrar la condición de crédito y el recargo propuesto en la cotización, sujetos a aprobación de R-PRO. **La fórmula y el porcentaje del recargo están pendientes**: no calcular ni aplicar valores no definidos. | R-PRO |
| **POL-03** | Espera en obra | El transporte espera hasta **30 minutos desde su llegada**. Si no se concreta la recepción, registra la incidencia y coordina el retorno o la reprogramación; las excepciones y costos quedan pendientes. | Logística/Transporte (función por asignar) |
| **POL-04** | Programación del despacho | Para pedidos confirmados, programar el despacho **como mínimo desde el día siguiente**, sujeto a disponibilidad, capacidad y validación del calendario aplicable. | Logística/Transporte (función por asignar) |
| **POL-05** | Cotización con stock insuficiente | Para **cemento, fierro, agregados y ladrillos**, permitir cotización condicionada a abastecimiento cuando no exista stock suficiente. Registrar el faltante, evaluar P2P y **no prometer entrega firme** sin verificar suministro y logística. La cotización no confirma un pedido ni autoriza automáticamente una compra. | R-ATN / R-ALM / R-PRO |
| **POL-06** | Descuento extraordinario | Toda excepción al precio ofrecido requiere decisión documentada de R-PRO y nueva versión de la cotización, sin borrar la anterior. El iniciador no aprueba su propia excepción (SOD-01). | R-PRO |
| **POL-07** | Adelantos parciales O2C | **Inactiva.** El escenario ordinario modelado no contempla adelantos parciales de clientes. No supone prohibición general ni altera obligaciones fiscales; los pagos anticipados a proveedores se evalúan por separado en P2P. | No aplica |
| **POL-08** | Revisión de inventario | Revisar semanalmente el **20 % de productos más vendidos** para identificar posibles reposiciones; la métrica y ventana de ventas aún deben definirse. La revisión no genera compras automáticas. | R-ALM / R-PRO |
| **POL-09** | Consulta y verificación de existencias | **Antes de emitir cada cotización**, R-ATN registra productos/cantidades y consulta disponibilidad de **todos los productos**. R-ALM verifica existencias aptas, reservas y discrepancias cuando sea necesario. Consultar no reserva stock; tras la aceptación, el ERP vuelve a verificar disponibilidad y condiciones. | R-ATN / R-ALM; validación ERP por responsable autorizado |
| **POL-10** | Promesa de entrega | Logística/Transporte evalúa la fecha solicitada según stock, capacidad y viajes programados. Comercial comunica únicamente la fecha o condición autorizada; R-PRO decide excepciones cuando corresponda. | Logística/Transporte; R-ATN / R-PRO según decisión |

**Precisiones:** «Logística/Transporte» es una función, **no un perfil nuevo**; responsables y facultades requieren validación `[ND]`. POL-09 aplica a todo producto y POL-05 a las cuatro categorías especiales.

## 2. Relación con reglas y controles vigentes

| Política | Referencias existentes | Alcance de la relación |
|---|---|---|
| POL-01 | RN-01 / CT-01 | Identificación y prevención de duplicados. |
| POL-02 | RN-03, RN-15 / CT-03, CT-15 | Precio y aprobación de crédito; fórmula del recargo pendiente. |
| POL-03 | RN-10, RN-14 | Registro de entrega real e incidencias/cierre; **los 30 minutos no están regulados expresamente por estas RN**. |
| POL-04 | RN-05, RN-10 | Promesa de disponibilidad y entrega real; **la programación al día siguiente requiere regla/control específico por alinear**. |
| POL-05 | RN-05, RN-08, RN-09, RN-16, RN-17 | Oferta condicionada, transición a pedido y necesidad P2P; sin reserva ficticia. |
| POL-06 | RN-03, RN-04, EXC-01 / SOD-01 | Precio, versionado y autorización independiente de excepciones. |
| POL-07 | — | Política inactiva en el escenario O2C acordado. |
| POL-08 | RN-16, RN-17 | Necesidad identificada y revisión de existencias/compras pendientes. |
| POL-09 | RN-05, RN-09; INT-01/EV-14 y EV-15 | Consulta previa, reserva diferenciada y revalidación ERP. La consulta obligatoria para **todos** los productos debe formalizarse en el catálogo. |
| POL-10 | RN-05, RN-10 | Promesa autorizada y entrega física; autoridad y programación específicas pendientes. |

**Uso correcto de EXC-02:** corresponde a la **discrepancia entre stock registrado y físico o apto** («inventario fantasma»), **no** a todo faltante conocido de productos especiales. Solo se relaciona con POL-05 o POL-09 cuando efectivamente exista ese descuadre.

**Transversales:** RN-08 (aceptación distinta de pedido confirmado), RN-11 y RN-12 (comprobante y cobro), RN-18 a RN-25 (P2P). Ningún control se considera automatizado sin prueba en Odoo.

## 3. Definiciones pendientes

| Tema | Por definir o validar |
|---|---|
| Crédito | Fórmula, porcentaje, condiciones y aprobación del recargo. |
| Espera y despacho | Excepciones, costos, calendario hábil/natural y tratamiento de urgencias. |
| Faltantes | Verificación de proveedor, viabilidad logística y condiciones para una promesa firme. |
| Descuentos | Criterios de autorización y segregación aplicable al caso real. |
| Inventario | Métrica/periodo del 20 %, parámetros por SKU, evidencia de consulta previa y tratamiento de inconsistencias. |
| Transporte | Responsable de evaluar, comunicar y aprobar fechas; correspondencia con los perfiles existentes. |
| Reglas/controles | Incorporar explícitamente consulta universal antes de cotizar y políticas de programación sin reutilizar RN-10 como si ya las regulara. |

## 4. Implicaciones de integración

- **CRM:** administra cotizaciones, versiones y condiciones comerciales. La consulta previa de inventario no supone reserva.
- **ERP:** cuando el cliente acepta, recibe una solicitud funcional y **revalida** datos, autorizaciones, disponibilidad y fecha. Responde **aceptada, condicionada o rechazada**; únicamente la aceptación operativa habilita confirmar el pedido (INT-01/EV-14 y EV-15).
- **Inventario y P2P:** registran existencias físicas/aptas/reservadas, demanda no cubierta y necesidades documentales de abastecimiento; no se presupone módulo Compras ni aprobación automática.
- **Facturación:** para el O2C ordinario se modela la emisión del comprobante cuando ocurra primero el pago total o la entrega; su aplicación requiere validación fiscal. En P2P, la factura del proveedor puede recibirse antes o después de la mercadería, conservando las incidencias.

**Documentos relacionados:** [Flujo CRM](comercial-crm/flujo-comercial.md), [decisiones comerciales](comercial-crm/decisiones-comerciales.md), [O2C](o2c/flujo.md), [P2P](p2p/flujo.md), [parámetros de inventario](../datos/maestros/parametros-inventario.md), [INT-01](../arquitectura/integraciones/INT-01-CRM-ERP.md) y [modelo de autorización](../arquitectura/seguridad/modelo-autorizacion.md).

**Estado:** propuesta alineada conceptualmente con el `main` revisado; validación empresarial y pruebas técnicas pendientes.

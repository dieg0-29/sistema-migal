# VER-03: Integración consolidada del CRM con el ERP

## 1. Metadatos

* **Fecha:** 23/10/2026
* **Versión:** 0.2
* **Autores:** Luis Núñez
* **Sistema incorporado:** CRM (Odoo 18 Community, misma instancia que el ERP; incorporación original en VER-02 y consolidación funcional en VER-03).
* **Sistemas existentes afectados:** ERP (Ventas, Punto de Venta, Inventario y registro administrativo); KeyFacil se mantiene como sistema externo, sin integración técnica acreditada.

## 2. Propósito

Consolidar la integración de **CRM y ERP** en una sola arquitectura funcional para Ferre & Inversiones MIGAL, conservando las decisiones vigentes de [VER-01](VER-01-erp-base.md) y [VER-02](VER-02-crm.md) y haciendo explícito su vínculo con **Order to Cash (O2C)**, **Procure to Pay (P2P)** e Inventario.

La necesidad del negocio es mantener la trazabilidad desde la consulta y la cotización comercial hasta la solicitud de pedido, su validación operativa y la ejecución posterior, sin confundir aceptación del cliente con pedido confirmado. Esta versión representa el **estado conceptual consolidado** del CRM ya incorporado; no agrega un tercer sistema ni presupone otra instancia, una API o controles implementados.

**Estado general:** `[AC][TO-BE]` — acuerdo de diseño del equipo, sujeto a ratificación por MIGAL. Los comportamientos específicos de Odoo y las pruebas funcionales continúan pendientes cuando así se indica.

## 3. Elementos que se mantienen

| Elemento | Observación |
|---|---|
| Plataforma | Odoo 18 Community para CRM, Ventas, Punto de Venta e Inventario, en **una misma instancia**; continúan las decisiones [ADR-001](../decisiones/ADR-001-seleccion-erp.md) y [ADR-002](../decisiones/ADR-002-version-y-edicion.md). |
| Responsabilidad comercial y operativa | El CRM gestiona leads, oportunidades, **cotizaciones y sus versiones hasta la aceptación**; el ERP ejecuta desde el pedido operativamente confirmado. El módulo técnico `sale` puede soportar la cotización vinculada al CRM mediante `sale_crm`, sin crear registros duplicados por diseño. |
| KeyFacil | Mantiene la **emisión fiscal externa**; Odoo conserva referencias operativas para conciliación, sin asumir conectores. |
| Canales de atención | WhatsApp, teléfono y atención presencial se registran como origen de la consulta; no se automatizan. |
| Compras | El módulo Compras no forma parte del alcance. El abastecimiento **P2P es documental**, sin solicitud de cotización a proveedores generada automáticamente. |
| Ubicaciones físicas | Tienda, Almacén 1 y Almacén 2; su representación y comportamiento exacto en Odoo siguen sujetos a validación. |
| Autorizaciones | Se mantienen los perfiles R-ATN, R-PRO, R-ALM y R-FAC, los controles PR y la segregación SOD definidos en el [modelo vigente](../seguridad/modelo-autorizacion.md). No se crea un perfil formal de Logística. |
| Datos y evidencia | IDs trazables y catálogos `[SIM]`; documentar un control **no** equivale a haberlo configurado o probado. |

## 4. Elementos que se agregan

Esta tabla presenta los elementos **acumulados en el estado consolidado**: tanto los incorporados originalmente con el CRM en VER-02 como las precisiones actuales. No supone que cada elemento se haya creado por primera vez en VER-03.

| Elemento | Descripción | Respaldo |
|---|---|---|
| CRM y seguimiento | Leads, contactos, oportunidades, actividades, origen comercial y motivos de cierre; campañas solo cuando exista una fuente atribuible. | [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md), [cuentas y canales](../../procesos/comercial-crm/cuenta-contacto-canales.md) |
| Etapas y cotización comercial | Lead → Lead calificado → Oportunidad → Cotización → Negociación; historial de versiones y aceptación. Son **etapas funcionales** cuya representación en Odoo se verificará. | [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md), [glosario](../../datos/glosario-negocio.md) |
| Consulta previa de inventario | Comercial consulta disponibilidad de **todos los productos antes de emitir cada cotización**. Almacén verifica stock apto, reservas y diferencias cuando corresponde; consultar no equivale a reservar. | [Políticas operativas](../../procesos/politicas-operativas.md), [parámetros de inventario](../../datos/maestros/parametros-inventario.md) |
| Transición CRM–ERP | Cotización aceptada → solicitud funcional de pedido (**EV-14**) → revalidación de datos, autorizaciones, stock y fecha → respuesta del ERP (**EV-15**): aceptada, rechazada o condicionada. Solo la aceptada permite confirmar el pedido. | [INT-01](../integraciones/INT-01-CRM-ERP.md), [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md) |
| Inventario y reposición | Stock físico, apto, reservado, disponible y demanda no cubierta, sin confundir mercadería inexistente con reservas. Parámetros mínimo/óptimo y revisión semanal propuesta del 20 % de productos más vendidos. | [Glosario](../../datos/glosario-negocio.md), [parámetros de inventario](../../datos/maestros/parametros-inventario.md) |
| Cotización con faltante especial | Cemento, fierro, agregados y ladrillos pueden cotizarse **condicionados a abastecimiento**, sin fecha firme hasta verificar suministro y logística. Para otros faltantes se revisa alternativa o condición antes de emitir. | [Políticas operativas](../../procesos/politicas-operativas.md), [P2P](../../procesos/p2p/flujo.md) |
| Abastecimiento P2P documental | Necesidad por mínimo, demanda de venta o revisión programada; evaluación de proveedor, autorización, orden de compra documental, recepción real, factura y pago, sin módulo Compras. | [P2P](../../procesos/p2p/flujo.md), [reglas de negocio](../../procesos/catalogo-reglas.md) |
| Condiciones y logística | Crédito con recargo aún sin fórmula; descuentos extraordinarios aprobados con segregación; despacho programado desde el día siguiente y espera máxima propuesta de 30 minutos en obra, sujetos a validación. | [Políticas operativas](../../procesos/politicas-operativas.md), [revisión de roles](../seguridad/revision-roles-funcionales.md) |
| Documentación fiscal y cobro | KeyFacil emite el comprobante; Odoo conserva referencia y conciliación. En O2C ordinario el equipo modela la emisión al primer evento entre pago total y entrega, **pendiente de confirmación tributaria**. En P2P, la factura del proveedor puede llegar antes o después de la recepción. | [Facturación O2C](../../procesos/o2c/facturacion.md), [P2P](../../procesos/p2p/flujo.md) |
| Vista y validación funcional | Diagramas editables PlantUML y definición del contrato funcional. Casos normales y con faltantes previstos para la integración; **no** se declaran ejecutados. | [Vista funcional](../vistas/contrato-crm-erp.md), [casos de prueba](../../pruebas/casos/) |

## 5. Ajustes en los sistemas existentes

Los **CA-01 a CA-08** se reproducen **como ajustes heredados de VER-02**, con sus estados aún vigentes o pendientes; **no se renumeran ni constituyen nuevos ajustes**. Los **CA-09 a CA-17** registran la evolución conceptual de esta consolidación. Así, el documento puede leerse de forma autónoma sin perder la trazabilidad histórica. El índice de versiones debe diferenciar los ajustes heredados de los incorporados en VER-03.

| ID | Sistema / componente | Antes | Después | Motivo de la integración | Documentos afectados | Estado |
|---|---|---|---|---|---|---|
| CA-01 | Arquitectura C4 | Vistas centradas en ERP e Inventario. | Se incluye CRM y su relación con Ventas en una instancia Odoo. | Representar la frontera entre ámbitos comercial y operativo. | [Vistas C4](../vistas/consolidado-diagramas.md) | Documentado en VER-02; renders por alinear. |
| CA-02 | Ventas | Cotización originada directamente en Ventas. | Cotización vinculada a oportunidad (`sale_crm`), gestionada comercialmente hasta aceptación; pedido **solo tras validación ERP**. | Preservar origen comercial sin confirmar prematuramente. | [INT-01](../integraciones/INT-01-CRM-ERP.md), [O2C](../../procesos/o2c/flujo.md) | Diseño ajustado; pendiente de prueba. |
| CA-03 | Registros internos / KeyFacil | Documentos operativos y fiscales insuficientemente diferenciados. | KeyFacil emite el comprobante; Odoo registra referencia y conciliación. | Evitar duplicidad de emisión y mantener trazabilidad. | [ADR-004](../decisiones/ADR-004-odoo-community-fronteras-crm-erp.md), [facturación](../../procesos/o2c/facturacion.md) | Frontera documentada; flujo y prueba pendientes. |
| CA-04 | Abastecimiento | Se presumía RFQ automática por falta de stock. | P2P documental, sin automatismo ni módulo Compras. | Respetar alcance y capacidades no verificadas. | [O2C](../../procesos/o2c/flujo.md), [P2P](../../procesos/p2p/flujo.md), [inventario](../../procesos/inventario/flujo.md) | Criterio documental establecido; ejecución pendiente. |
| CA-05 | Precios | Ajustes de precio sin contexto uniforme. | Se conserva versión, referencia, precio ofrecido, motivo y autorizador. | Reconstruir negociaciones y excepciones. | [Decisiones comerciales](../../procesos/comercial-crm/decisiones-comerciales.md) | Documentado; pendiente de prueba. |
| CA-06 | Punto de Venta | Se mezclaban descuento inmediato y cierre de sesión. | Se propone descontar el stock de Tienda con cada venta POS. | Evitar disponibilidad desactualizada. | [O2C](../../procesos/o2c/flujo.md), [inventario](../../procesos/inventario/flujo.md) | Decidido en diseño; pendiente de prueba Odoo. |
| CA-07 | Contabilidad | Complemento de terceros tratado como requisito universal. | Verificar alcance estándar frente a módulos adicionales y convivencia con KeyFacil. | Delimitar dependencias reales. | [ADR-003](../decisiones/ADR-003-contabilidad.md) | Revisión del ADR pendiente. |
| CA-08 | Datos de prueba | Sin guía uniforme de códigos. | Distinguir datos sintéticos, IDs documentales y referencias propias de Odoo. | Reproducibilidad y auditoría. | [Datos de prueba](../../datos/pruebas/README.md) | Guía documentada; ejecución pendiente. |
| CA-09 | CRM / etapas | Calificación y oportunidad sin separación suficiente. | Lead, Lead calificado, Oportunidad, Cotización y Negociación como momentos funcionales; mantener versionado. | Mayor precisión comercial sin suponer estados nativos. | [Flujo comercial](../../procesos/comercial-crm/flujo-comercial.md), [glosario](../../datos/glosario-negocio.md) | `[AC][TO-BE]`; alineación del flujo pendiente. |
| CA-10 | CRM–ERP / pedidos | La aceptación se describía como paso directo a pedido. | Aceptación → EV-14 solicitud → revalidación ERP → EV-15 resultado aceptado, condicionado o rechazado; solo entonces confirmar si procede. | Evitar compromiso operativo no validado. | [INT-01](../integraciones/INT-01-CRM-ERP.md), [pedido](../../procesos/o2c/pedido.md), [vista funcional](../vistas/contrato-crm-erp.md) | EV-14/15 documentados; F-02 por armonizar; prueba pendiente. |
| CA-11 | Inventario | Existencias, reservas y promesas confundidas; consulta previa no uniforme. | Stock físico apto, reservado, disponible y demanda no cubierta; consulta de **todos los productos antes de cotizar** y revalidación después de aceptar. | Disminuir promesas y reposiciones incorrectas. | [Parámetros](../../datos/maestros/parametros-inventario.md), [inventario](../../procesos/inventario/flujo.md), [políticas](../../procesos/politicas-operativas.md) | Modelo documental alineado; parámetros y sistema pendientes. |
| CA-12 | Comercial / O2C–P2P | Faltantes como excepción general. | Cotizar cemento, fierro, agregados y ladrillos con condición de abastecimiento; no comprometer fecha firme ni reservar stock inexistente. | Vincular oferta con viabilidad operativa y P2P. | [Políticas](../../procesos/politicas-operativas.md), [O2C](../../procesos/o2c/flujo.md), [P2P](../../procesos/p2p/flujo.md) | Acuerdo del equipo; validación pendiente. |
| CA-13 | P2P | Reposición general sin causas diferenciadas. | Necesidades por stock mínimo, demanda comercial y revisión semanal de productos prioritarios; compra autorizada, recepción y conciliación documentales. | Formalizar abastecimiento y evitar duplicidad. | [P2P](../../procesos/p2p/flujo.md), [parámetros](../../datos/maestros/parametros-inventario.md) | Diseño funcional; documentos operativos por alinear. |
| CA-14 | O2C / P2P y comprobantes | Entrega, facturación y cobro descritos como secuencia fija. | En O2C se propone emitir en KeyFacil al primer evento entre pago total y entrega; en P2P la factura del proveedor puede preceder o seguir a la recepción. | Separar eventos físicos, fiscales y económicos. | [Facturación](../../procesos/o2c/facturacion.md), [O2C](../../procesos/o2c/flujo.md), [P2P](../../procesos/p2p/flujo.md) | Propuesta; verificación fiscal y documental pendiente. |
| CA-15 | Logística / O2C | Programación y espera insuficientemente descritas. | Viajes y conformidad de entrega; despacho previsto desde el día siguiente y espera máxima propuesta de 30 minutos en obra. | Controlar compromisos de entrega e incidencias. | [O2C](../../procesos/o2c/flujo.md), [políticas](../../procesos/politicas-operativas.md), [roles](../seguridad/revision-roles-funcionales.md) | Responsables y excepciones pendientes. |
| CA-16 | Condiciones comerciales y permisos | Descuentos y crédito parcialmente definidos. | Descuento excepcional con decisión de propietario independiente según SOD-01; recargo por crédito identificado **sin fórmula aprobada**. | Trazabilidad de decisión y segregación. | [Decisiones](../../procesos/comercial-crm/decisiones-comerciales.md), [seguridad](../seguridad/modelo-autorizacion.md), [políticas](../../procesos/politicas-operativas.md) | Definición parcial; fórmula y configuración pendientes. |
| CA-17 | Pruebas y trazabilidad | Casos principales enfocados al CRM. | Proponer CP-03/CP-04 para transición normal y faltante, con EV-14/15, resultados esperados, controles y referencias. | Evaluar continuidad CRM–ERP sin pruebas ficticias. | [Casos](../../pruebas/casos/), [plan de trazabilidad](../../datos/trazabilidad/plan-trazabilidad-auditoria.md) | Casos por incorporar; **no ejecutados**. |

## 6. Decisiones pendientes

* Ratificar con propietarios las políticas de inventario, abastecimiento, descuentos, crédito y logística; definir fórmula del recargo, parámetros mínimos/óptimos y criterio de revisión semanal del 20 %.
* Precisar responsables de fecha y transporte, cómputo del despacho desde el día siguiente, espera de 30 minutos y documentación del traslado.
* Alinear [INT-01/F-02](../integraciones/INT-01-CRM-ERP.md) para que no describa la aceptación como pedido directo, y formalizar consulta previa universal y revalidación en el [catálogo RN/CT](../../procesos/catalogo-reglas.md).
* Revisar [ADR-003](../decisiones/ADR-003-contabilidad.md) con su responsable; verificar módulos, grupos, POS, reservas, `sale_crm`, versiones de oferta y confirmación de `sale.order` en la instancia real.
* Actualizar los procesos CRM/O2C/P2P, el [diccionario de datos](../../datos/diccionario-datos.md), las [reglas de calidad](../../datos/reglas-calidad.md), la [matriz de permisos](../seguridad/matriz-permisos.xlsx) y diagramas C4/BPMN con sus renders.
* Incorporar y ejecutar los casos de prueba cuando corresponda, diferenciando resultado esperado, obtenido y evidencia. Completar el [índice de versiones](README.md) **sin modificar la plantilla**; el índice debe aclarar que CA-01 a CA-08 son heredados y CA-09 a CA-17 son los nuevos.

## 7. Historial de cambios

| Versión | Fecha | Autor | Cambio |
|---|---|---|---|
| 0.1 | 22/10/2026 | Luis Núñez | Borrador inicial de VER-03 consolidada; recuperación de CA-01 a CA-08 y propuesta CA-09 a CA-17. |
| 0.2 | 23/10/2026 | Luis Núñez | Alineación con el último `main`, el glosario y los documentos revisados de Fase 2; consulta previa universal, revalidación ERP, enlaces relativos y distinción entre ajustes heredados y nuevos. |

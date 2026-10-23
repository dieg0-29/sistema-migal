# Parámetros de inventario y necesidades — definición funcional

**Estado:** `[AC][TO-BE]`. Definición funcional del equipo; parámetros, existencias y cantidades reales pendientes de validación.  
**Unidad de referencia:** Cada producto (SKU) y ubicación, cuando corresponda.  
**Propósito:** Distinguir los parámetros que orientan la gestión del inventario de los datos operativos que permiten identificar necesidades de abastecimiento.

## 1. Parámetros de inventario

Son los datos maestros y valores de referencia propuestos para cada producto. Sirven para evaluar las existencias, detectar faltantes y orientar las decisiones de reposición.

| Parámetro | Definición funcional | Responsable y validación |
|---|---|---|
| `Producto_ID` / `Unidad_medida` | Identificador único del producto y unidad de medida utilizada para controlar sus existencias. | R-PRO; maestro de productos. |
| `Ubicación` | Punto físico donde se controla el inventario: Tienda, Almacén 1 o Almacén 2. | R-ALM / R-PRO; representación en Odoo pendiente de validación. |
| `Stock_mínimo` | Umbral definido para identificar cuándo un producto requiere revisión o reposición. | R-PRO; valor empírico por producto, con fecha y fundamento. |
| `Stock_óptimo` | Cantidad objetivo que se busca alcanzar mediante el abastecimiento. | R-PRO; valor empírico por producto, con fecha y fundamento. |
| `Revisión_20pct` | Revisión semanal del 20 % de productos más vendidos para evaluar su disponibilidad y posibles necesidades de reposición. | R-PRO / R-ALM; métrica de ventas, ventana histórica y excepciones pendientes `[ND]`. |

**Consideraciones:**
- Los valores de stock mínimo y óptimo serán determinados inicialmente mediante el criterio de los propietarios.
- Los parámetros pueden revisarse cuando cambien las ventas, la rotación, los tiempos de abastecimiento o las condiciones del negocio.
- La revisión semanal del 20 % permite priorizar productos, pero no determina automáticamente cuánto comprar.
- Los valores deben conservar su fecha de actualización, responsable y fundamento.

## 2. Existencias operativas y necesidades de abastecimiento

Son los datos que describen la situación actual del inventario y permiten identificar cuánto se necesita abastecer y por qué motivo.

| Dato o necesidad | Definición / criterio de activación | Registro y responsable |
|---|---|---|
| `Stock_físico` | Existencias observadas en el conteo por ubicación; pueden incluir unidades dañadas o no aptas. | R-ALM; fecha, ubicación y evidencia del conteo. |
| `Stock_apto` | Parte del stock físico utilizable para atender ventas; excluye unidades deterioradas o no aptas. | R-ALM verifica; registrar diferencias y condición física. |
| `Stock_reservado` | Unidades de stock apto asignadas físicamente a pedidos y aún no entregadas; no incluye promesas sin existencia. | R-ALM; referencia del pedido y cantidad asignada. |
| `Stock_disponible` | Stock apto que no está reservado y puede evaluarse para nuevas solicitudes. | Cálculo funcional con stock apto y reservado, por producto y ubicación. |
| `Demanda_no_cubierta` | Cantidad comprometida o prometida sin existencias asignables; distinguir cotización condicionada, solicitud pendiente y pedido confirmado. | Comercial registra la necesidad; R-ALM verifica el faltante; referencia y estado de la operación. |
| `Compras_pendientes` | Cantidades de compras documentales aprobadas que aún no se reciben; no son stock físico. | R-PRO; referencia de compra, cantidad, proveedor y estado. |
| `Entradas_confirmadas` | Parte de las compras pendientes cuya cantidad y recepción prevista están confirmadas para el horizonte evaluado; no es existencia disponible. | R-PRO / R-ALM; referencia, cantidad, fecha y ubicación previstas. |
| **Necesidad por stock bajo** | Se identifica cuando el stock disponible cae por debajo del mínimo establecido. Se propone reponer hasta alcanzar el stock óptimo. | R-ALM identifica; R-PRO evalúa y aprueba. |
| **Necesidad por venta comprometida** | Se identifica cuando un pedido requiere productos que no pueden cubrirse con las existencias disponibles. Aplica especialmente a cemento, fierro, agregados y ladrillos. | Comercial comunica la demanda; R-ALM verifica el faltante; R-PRO autoriza el abastecimiento. |
| **Necesidad programada** | Se identifica mediante revisiones periódicas de productos prioritarios y tendencias de venta. La cantidad se determina según el comportamiento observado y las condiciones de abastecimiento. | R-ALM / R-PRO; revisión de rotación, existencias y necesidades previstas. |

### 2.1. Cálculos funcionales recomendados

Los siguientes cálculos sirven para el análisis y la planificación. **No representan fórmulas ni automatizaciones verificadas en Odoo.**

**A. Stock disponible**

`Stock_disponible = max(0, Stock_apto - Stock_reservado)`

Si el stock reservado supera al **apto**, se registra una inconsistencia para revisión; el `max(0, ...)` no la corrige ni la oculta. No se contabilizan como reservadas las cantidades prometidas sin stock físico asignado.

**B. Reposición candidata por stock bajo**

`Cantidad_reposición = max(0, Stock_óptimo - Stock_disponible - Entradas_confirmadas)`

Esta estimación solo aplica si las entradas confirmadas corresponden al mismo producto, ubicación y horizonte de reposición. Antes de aprobar, evaluar por separado la demanda no cubierta y descontar las compras pendientes que ya la atienden para evitar duplicidades. No usar entradas previstas como stock disponible.

**C. Abastecimiento por venta específica**

`Déficit_venta = max(0, Cantidad_requerida - Cantidad_disponible)`

El déficit representa la cantidad no cubierta al momento de la consulta; puede identificarse antes de cotizar o al revalidar el pedido. No implica automáticamente una compra aprobada ni una fecha de entrega confirmada.

**D. Abastecimiento programado**

La cantidad se determinará considerando las ventas históricas, existencias disponibles, compras pendientes, demanda prevista, tiempo de suministro y presupuesto.

No se establece una fórmula única hasta contar con criterios y datos suficientes.

### 2.2. Relación con CRM, ERP y P2P

- **Antes de cada cotización:** Comercial consulta la disponibilidad de **todos los productos** solicitados; R-ALM verifica existencias físicas cuando sea necesario. Consultar no significa reservar.
- **Faltantes especiales:** cemento, fierro, agregados y ladrillos pueden cotizarse con condición explícita de abastecimiento, sin prometer una fecha firme no verificada. La cotización condicionada no es un pedido confirmado ni una compra aprobada.
- **Después de la aceptación:** el ERP revalida stock, autorizaciones y viabilidad logística antes de confirmar el pedido. La solicitud puede resultar aceptada, condicionada o rechazada (INT-01/EV-14 y EV-15).
- **Abastecimiento:** toda necesidad de reposición se tramita documentalmente en P2P, sin asumir el módulo Compras ni automatismos de Odoo.

### 2.3. Validaciones y decisiones pendientes

- Definir valores de stock mínimo y óptimo por producto.
- Establecer la periodicidad de revisión y actualización de estos parámetros.
- Determinar el tiempo de abastecimiento de los productos y proveedores.
- Precisar la métrica y el período utilizados para seleccionar el 20 % de productos más vendidos.
- Definir presupuestos, prioridades y responsables de aprobación.
- Evitar duplicidades entre necesidades por stock bajo, ventas comprometidas y abastecimiento programado.
- Formalizar la política de abastecimiento para cemento, fierro, agregados y ladrillos, diferenciando la aceptación de una solicitud de la confirmación de su entrega.

**Restricción:** No se deben inventar existencias, parámetros, cantidades de compra ni valores históricos de MIGAL.

**Fuentes de referencia:** acuerdos del equipo, [glosario de negocio](../glosario-negocio.md), [integración CRM–ERP](../../arquitectura/integraciones/INT-01-CRM-ERP.md), [inventario](../../procesos/inventario/flujo.md), [P2P](../../procesos/p2p/flujo.md) y FN-01/FN-02.
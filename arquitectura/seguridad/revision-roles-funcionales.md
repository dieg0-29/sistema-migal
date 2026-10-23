# Revisión de roles y responsabilidades funcionales — MIGAL

**Estado:** `[AC][TO-BE]` — Propuesta de alineación de responsabilidades para CRM, O2C y P2P, pendiente de validación.

**Propósito:** Definir las funciones y responsabilidades que intervienen en los procesos empresariales, manteniendo coherencia con el modelo de autorización vigente.

## 1. Roles y responsabilidades funcionales

| Rol / Función | Responsabilidades principales | Restricciones y controles |
|---|---|---|
| **Atención comercial (R-ATN)** | Registra leads, califica oportunidades, gestiona cotizaciones y comunica condiciones comerciales aprobadas. | No autoriza descuentos extraordinarios ni condiciones de crédito. |
| **Propietario (R-PRO)** | Aprueba descuentos excepcionales, condiciones de crédito, compras y decisiones comerciales sensibles. | Debe respetar la segregación de funciones (SOD-01 y SOD-04). |
| **Inventario / Almacén (R-ALM)** | Verifica existencias, reservas y disponibilidad por ubicación; registra recepciones, despachos y diferencias. | No aprueba sus propios ajustes de inventario. |
| **Transporte / Logística** | Evalúa fechas de entrega, capacidad de transporte, programación de viajes y conformidad de recepción. | Función pendiente de formalización; no implica un nuevo rol técnico en Odoo. |
| **Facturación y cobro (R-FAC)** | Registra comprobantes, cobros, cuotas, facturas de proveedores y saldos; realiza conciliaciones. | No autoriza condiciones crediticias ni emite comprobantes fiscales desde Odoo. |
| **KeyFacil (sistema externo)** | Permite la emisión de comprobantes electrónicos por personal autorizado. | No existe integración técnica automatizada acreditada con Odoo. |

## 2. Matriz de responsabilidades por actividad

**Convenciones:** **P:** Prepara o registra información. **A:** Aprueba o autoriza. **E:** Ejecuta o comunica. **V:** Verifica.

| Actividad | Comercial | Almacén | Transporte | Propietario | Facturación / Cobro |
|---|---|---|---|---|---|
| Cotización | P/E | V disponibilidad | V condiciones logísticas | A excepciones | V datos fiscales |
| Promesa de entrega | E al cliente | V existencias | V viabilidad | A excepciones | — |
| Pedido con stock insuficiente | P | V déficit | V fecha viable | A decisión | — |
| Programación y entrega | Coordina con cliente | P/E despacho | P/E transporte | A excepciones | — |
| Facturación y cobro | Consulta estado | — | — | Emite en KeyFacil | P/E registro y conciliación |
| Abastecimiento y compra | Informa requerimiento | P necesidad | Coordina recepción | A compra | V documentos y pagos |

La matriz representa **responsabilidades funcionales**, no departamentos necesariamente independientes. Una misma persona puede desempeñar varias funciones, respetando los controles de autorización establecidos.

## 3. Consideraciones pendientes

| Aspecto | Validación requerida |
|---|---|
| Organización | Confirmar los responsables reales de cada función en MIGAL. |
| Transporte | Precisar quién programa, conduce y confirma las entregas. |
| Guías de remisión | Determinar el responsable de emisión y el tipo de guía aplicable. |
| Autorizaciones | Verificar sustituciones y segregación de funciones en decisiones sensibles. |
| Permisos de Odoo | Contrastar las responsabilidades propuestas con los accesos efectivamente disponibles. |

## 4. Vinculación documental

Las responsabilidades deberán mantenerse alineadas con:

- `arquitectura/seguridad/modelo-autorizacion.md`
- `procesos/comercial-crm/flujo-comercial.md`
- `procesos/o2c/flujo.md`
- `procesos/p2p/flujo.md`
- `procesos/catalogo-reglas.md`

**Estado de cierre:** Propuesta preparada para validación. No modifica la matriz PR/SOD vigente ni acredita permisos técnicos configurados en Odoo.
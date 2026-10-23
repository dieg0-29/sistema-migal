# Glosario de negocio y estados — MIGAL

- **Estado:** propuesta del equipo pendiente de ratificación por MIGAL.
- **Uso:** términos comunes para arquitectura, CRM, O2C, P2P y pruebas.
- **Fecha:** 22/10/2026.

## Convención de evidencia
- `[E]`: consta en FN-01/FN-02 u otra fuente del negocio; no extender más allá de lo que conste.
- `[AC]`: acuerdo comunicado por el equipo el 22/10/2026, aún no necesariamente validado por los propietarios.
- `[P]`: recomendación de diseño pendiente de aprobación.
- `[ND]`: no definido/verificado. `[SIM]`: dato artificial. `[PRUEBA]`: solo si existe ejecución y evidencia.

| Término | Significado funcional | Regla de uso |
|---|---|---|
| Necesidad comercial | Requerimiento de material de un comprador potencial o habitual | Inicio del O2C completo; no equivale a pedido |
| Lead | Persona con interés identificable en MIGAL o un producto | Nombre o alias y responsable; no exigir DNI/RUC |
| Lead calificado | Lead con lista o necesidad material concreta | Puede existir lista parcial y cantidades aproximadas; no exigir lista perfecta |
| Oportunidad | Posibilidad comercial activa con condiciones consultadas/ofrecidas y siguiente acción | Despacho/pago/fecha son señales, no preguntas obligatorias del cliente |
| Cuenta | Cliente responsable de la relación comercial | No se confunde con maestro de obra o contacto |
| Contacto | Interlocutor asociado a una cuenta (dueño, maestro, receptor) | Un mismo contacto puede participar en distintas interacciones |
| Cotización | Oferta con identificador/versión, productos, cantidades, precios y condiciones | Su emisión no confirma el pedido |
| Negociación | Ajustes solicitados o propuestos a una oferta | Se conserva versión anterior y motivo del cambio |
| Cotización aceptada | Versión y condiciones aceptadas por persona pertinente | No equivale a pedido confirmado automáticamente |
| Solicitud de pedido | Evento funcional que pide al ERP validar/confirmar lo aceptado | No presupone API ni tabla adicional en Odoo |
| Respuesta ERP | Aceptada, rechazada o promesa condicionada/pendiente | Debe expresar motivo, responsable, próxima acción y referencia |
| Pedido confirmado | Compromiso operativo registrado tras verificaciones | No necesariamente acredita reserva física completa |
| Stock físico de almacén | Existencia física por ubicación | Diferenciar stock apto/deteriorado y diferencias de conteo |
| Stock comprometido físicamente | Existencia apta asignada/reservada a pedidos | No incluye unidades prometidas pero inexistentes |
| Stock disponible | Stock físico apto menos stock reservado/comprometido físicamente | No negativo en la definición física; revisar disponibilidad por ubicación |
| Demanda no cubierta | Cantidad comprometida/prometida aún sin existencias asignables | Puede originar necesidad de P2P |
| Stock mínimo | Umbral empírico de control por producto | Aprobación y revisión por propietarios |
| Stock óptimo | Nivel objetivo empírico de reposición por producto | No es pronóstico comprobado |
| Necesidad de abastecimiento | Registro con causa, artículo, cantidad, fecha y prioridad | Origen: stock bajo, venta o programación |
| Orden de compra | Compromiso documental autorizado con proveedor | Proceso P2P; no afirmar que el módulo Compras está implementado |
| Programación de entrega | Plan de uno o varios viajes por pedido | Distinguir compromiso y ejecución real |
| Conformidad de recepción/entrega | Registro de cantidades efectivas y observaciones | No sustituye automáticamente documento tributario |
| Comprobante fiscal | Boleta/factura válida emitida/recibida según norma y proceso | MIGAL: KeyFacil emite comprobantes de venta, Odoo guarda referencia propuesta |
| Cobro/pago | Movimiento económico efectivamente verificado | Distinto de facturación y de deuda/vencimiento |


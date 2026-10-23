# ADR 004: Adoptar Odoo Community con fronteras funcionales claras para CRM y ERP

## 1. Metadatos

* **Fecha:** 22/10/2026
* **Version:** V2
* **Estado:** **Propuesto**
* **Autores:** Gabriel Martínez

## 2. Contexto y Problema

Ferre & Inversiones MIGAL recibe consultas de clientes y maestros de obra por WhatsApp, teléfono y mostrador. Actualmente, la información comercial puede quedar distribuida entre conversaciones, apuntes y conocimiento informal de los propietarios.

La empresa también mantiene materiales en tres puntos físicos de inventario: Tienda, Almacén 1 y Almacén 2. Además, la emisión de comprobantes se realiza actualmente mediante KeyFacil, por lo que cualquier solución propuesta debe convivir con ese sistema mientras no se defina una integración técnica.

El problema arquitectónico consiste en organizar el recorrido de la información desde la consulta inicial hasta la cotización, la confirmación del pedido, la entrega, la facturación y el cobro, sin introducir una solución técnica más compleja que la capacidad operativa y económica de la empresa.

## 3. Restricciones de la Empresa

* La solución debe ser sostenible para una pequeña empresa.
* Se debe evitar una arquitectura con múltiples sistemas y conectores técnicos innecesarios.
* La información de clientes, productos, precios y pedidos debe registrarse de forma trazable.
* Los precios y descuentos especiales requieren revisión de los propietarios.
* La existencia física está distribuida entre Tienda, Almacén 1 y Almacén 2.
* KeyFacil continúa siendo el sistema utilizado para emitir comprobantes.
* En el alcance actual no se exige desarrollar una integración técnica con KeyFacil, WhatsApp u otros sistemas externos.
* Las reglas definitivas de descuentos, créditos, vigencia de cotizaciones y reservas de stock todavía deben validarse con MIGAL.
* El comportamiento exacto de Odoo debe comprobarse en la versión y configuración utilizada por el equipo.
* La decisión distingue interés del cliente, cotización aceptada, solicitud de pedido, respuesta ERP y confirmación operativa. 

## 4. Opciones Consideradas

| Opción | Ventaja | Desventaja |
|---|---|---|
| A. CRM y ERP separados | Fronteras técnicas independientes | Integra datos y costes adicionales no sustentados |
| B. Un Odoo sin responsabilidades | Menor esfuerzo documental inicial | Confunde precios, reservas, aprobaciones y resultados |
| **C. Un Odoo con fronteras funcionales y evidencia** | Información y estados trazables, menor complejidad relativa | Requiere procesos, responsables, pruebas y disciplina documental |

### Opción A: CRM externo y ERP separado

Consiste en utilizar una herramienta externa para la gestión comercial y otra para inventario y ventas, conectándolas mediante interfaces técnicas.

**Ventajas:**

* Permite seleccionar herramientas especializadas.
* Puede ofrecer mayor flexibilidad en cada área.

**Desventajas:**

* Mayor costo de licencias, configuración y mantenimiento.
* Mayor riesgo de pérdida o duplicación de información.
* Dependencia de conectores técnicos.
* Complejidad superior al alcance actual del proyecto.

**Evaluación:** Descartada para el alcance actual.

### Opción B: Utilizar Odoo sin fronteras funcionales

Consiste en utilizar Odoo Community como plataforma única, pero sin definir responsabilidades, datos mínimos ni controles por etapa.

**Ventajas:**

* Bajo costo inicial.
* Implementación conceptual sencilla.

**Desventajas:**

* Puede permitir duplicidad de contactos.
* Puede generar modificaciones no controladas de precios o pedidos.
* No distingue claramente las responsabilidades de Ventas, Almacén y Administración.
* Traslada el desorden operativo al sistema.

**Evaluación:** Descartada.

### Opción C: Odoo Community con fronteras funcionales claras

Consiste en utilizar Odoo Community como plataforma integrada para CRM, Ventas e Inventario, definiendo responsabilidades y datos mínimos por etapa.

La relación con KeyFacil se manejará inicialmente mediante el registro y conciliación de la referencia del comprobante, sin asumir una integración técnica hasta que sea evaluada y aprobada.

**Evaluación:** Seleccionada como propuesta base para evaluación.

## 5. Decisión

Se propone adoptar la **Opción C: Odoo Community con fronteras funcionales claras para CRM y ERP** como base de evaluación de la arquitectura.

La propuesta establece las siguientes fronteras:

1. **CRM:** registro de contactos, consultas y oportunidades.
2. **Ventas:** elaboración y seguimiento de cotizaciones y pedidos.
3. **Inventario:** consulta de existencias y preparación de entregas en los tres puntos físicos.
4. **Administración:** registro de comprobantes, pagos y saldos.
5. **KeyFacil:** emisión de boletas y facturas, manteniendo una referencia trazable en Odoo.

Esta decisión no implica que todos los controles estén implementados ni que exista una integración técnica con KeyFacil.

Cuando cliente acepta cotización, se solicita validación del pedido. ERP responde aceptada, rechazada o condicionada con motivo, autor y siguiente acción. Pedidos con faltante de cemento, fierro, agregados y ladrillos originan evaluación de abastecimiento; fecha prometida puede mantenerse condicionada hasta verificación. Comercial comunica, logística evalúa fecha y propietario aprueba excepciones, con asignación real aún pendiente.

## 6. Justificación

La propuesta se alinea con las restricciones conocidas de MIGAL porque:

* evita introducir varios sistemas comerciales y operativos desde el inicio;
* permite evaluar el uso de CRM, Ventas e Inventario en una misma plataforma;
* reduce la duplicación de datos entre las etapas comerciales;
* facilita la consulta de productos y precios desde un catálogo central;
* permite organizar la visibilidad de los tres puntos físicos de inventario;
* mantiene la convivencia con KeyFacil sin forzar una integración técnica no evaluada;
* permite separar los controles que realiza el sistema de los controles que dependen del procedimiento del personal.

La decisión debe revisarse si las pruebas de Odoo muestran limitaciones importantes o si MIGAL define requisitos que necesiten personalización o integración externa.

## 7. Consecuencias

### Positivas

* Menor complejidad inicial.
* Menor dependencia de conectores técnicos.
* Mayor trazabilidad entre consulta, cotización y pedido.
* Catálogo de productos y precios centralizado.
* Posibilidad de organizar el inventario por puntos físicos.
* Separación clara entre actividades comerciales, operativas y administrativas.

### Negativas y riesgos

* La conciliación con KeyFacil será inicialmente manual.
* Los controles de descuentos y créditos pueden requerir permisos, configuración o revisión humana.
* El stock registrado puede diferir del stock físico.
* El equipo debe respetar las fronteras funcionales definidas.
* El comportamiento exacto de reservas, entregas parciales y backorders debe validarse en la instancia de Odoo.
* La decisión no resuelve por sí misma la integración con WhatsApp ni la validación tributaria de documentos.

## 8. Validaciones Pendientes

* Confirmar la versión de Odoo Community utilizada.
* Probar la conversión de una oportunidad en cotización.
* Probar la confirmación de una cotización y la generación de una entrega.
* Verificar cómo se representan Tienda, Almacén 1 y Almacén 2.
* Probar un pedido con stock insuficiente.
* Verificar el comportamiento de una entrega parcial o backorder.
* Revisar los permisos disponibles para precios y descuentos.
* Definir el procedimiento para registrar la referencia de KeyFacil.
* Validar los requisitos tributarios con Administración y el responsable correspondiente.

<!-- Path: arquitectura_y_sistemas/23_odoo19_compras_novedades.md -->

# 🛒 Manual Operativo 23: Novedades y Gestión Avanzada del Módulo de Compras en Odoo 19

**Módulo / Área:** Financiero y Logística / Compras y Abastecimiento  
**Rol Requerido:** Compradores, Analistas de Abastecimiento, Director Financiero, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para conocer y aplicar las nuevas capacidades del ciclo de compras en Odoo 19, abarcando desde el control estricto de datos maestros y la confirmación telemática de órdenes por parte de los proveedores hasta la causación anticipada de facturas y el abastecimiento guiado por sugerencias estadísticas.

---

## 1. Control de Datos Maestros y Trazabilidad de Productos

Para garantizar la integridad del inventario corporativo y evitar la creación duplicada o errónea de suministros (cables, conectores, herrajes y equipos):
* **Restricción de Usuarios:** A través de la configuración de usuarios en la sección de *Administración de datos maestros*, es posible limitar qué perfiles tienen permisos para crear, editar o eliminar productos en el catálogo.
* **Trazabilidad:** Cualquier modificación en campos críticos (como unidades de medida o tipos de control de series/lotes) queda registrada automáticamente en el historial (*chatter*) del producto, permitiendo auditar responsabilidades.

---

## 2. Tablero de Control y Seguimiento Operativo

El módulo de Compras incorpora un tablero ejecutivo que segmenta las órdenes mediante indicadores de estado clave:
* **Borradores y Enviadas:** Solicitudes de cotización (RFQ) pendientes de respuesta.
* **Atrasadas:** Órdenes que han superado la fecha límite estipulada internamente por la compañía.
* **Sin Confirmar:** Pedidos enviados que aún no han recibido validación formal de recepción por parte del proveedor.

---

## 3. Confirmación de Órdenes y Recepción Logística

* **Validación del Proveedor:** El proveedor cuenta con mecanismos de confirmación telemática (vía enlace o portal) para certificar que ha recibido y aceptado las condiciones de la orden de compra, retirando el pedido del estado de alerta en el tablero interno.
* **Monitoreo de Recepciones Atrasadas:** Un botón directo en el módulo de Compras permite supervisar el estado de las entregas de mercancía pendientes sin necesidad de navegar hacia el módulo de Inventario, permitiendo anticipar cuellos de botella en proyectos de infraestructura.

---

## 4. Causación Anticipada de Facturas de Proveedor

* **Flexibilidad Operativa:** En versiones anteriores, la política de control impedía registrar facturas hasta que se completara la recepción física en inventario. Odoo 19 permite adjuntar y **subir la factura del proveedor directamente en la orden de compra**, aun cuando la mercancía se encuentre en tránsito o pendiente de entrega.
* **Ventaja Financiera:** Permite que el departamento contable registre las cuentas por pagar a tiempo, integrándose de forma directa con los flujos de *Causación 2.0* y agentes de pago sin demorar los procesos administrativos por retrasos logísticos.

---

## 5. Abastecimiento Inteligente (Catálogo y Sugerencias)

* **Catálogo por Proveedor:** Al generar una solicitud de cotización, el botón *Catálogo* despliega de manera inmediata todos los artículos comprados históricamente a dicho proveedor.
* **Cálculo de Cantidades Sugeridas:** Mediante el asistente de sugerencia, el sistema calcula volúmenes de reposición basándose en:
  * Días de abastecimiento objetivo (ej. 7, 15, 30 días).
  * Historial de ventas y consumo (semanal, mensual, anual o comparativos estacionales del mismo periodo en años anteriores).
  * Factores porcentuales de crecimiento o sobrestock.

---

## 6. Relevancia y Directrices Operativas para Telcobras

| Función Odoo 19 | Aplicación en Telcobras | Prioridad |
| :--- | :--- | :--- |
| **Control de Datos Maestros** | Evitar la alteración no autorizada de catálogos de materiales de red y equipos | Alta |
| **Tablero y Confirmación de Proveedores** | Garantizar certeza en la entrega de materiales críticos para obras de infraestructura | Alta |
| **Subir Factura sin Esperar Recepción** | Agilizar el registro contable y las cuentas por pagar (*Causación 2.0*) | Alta |
| **Seguimiento de Recepciones Atrasadas** | Prevenir retrasos en cronogramas de instalación y despliegue técnico | Alta |
| **Catálogo con Cantidades Sugeridas** | Optimizar el reabastecimiento de repuestos e insumos de alta rotación | Media |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Novedades del Módulo de Compras Odoo 19 (Control de datos maestros, tablero de control, confirmación de órdenes, causación anticipada de facturas y abastecimiento asistido): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/23_odoo19_compras_novedades.md

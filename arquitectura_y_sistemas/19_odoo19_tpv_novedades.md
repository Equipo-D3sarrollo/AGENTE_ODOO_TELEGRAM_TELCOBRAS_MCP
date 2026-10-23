<!-- Path: comercial/19_odoo19_tpv_novedades.md -->

# 💳 Manual Operativo 19: Novedades y Capacidades Avanzadas del Punto de Venta (TPV) en Odoo 19

**Módulo / Área:** Comercial / Punto de Venta (TPV)  
**Rol Requerido:** Cajeros, Vendedores de Mostrador, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para conocer y configurar las nuevas funcionalidades incorporadas en el Punto de Venta de Odoo 19, abarcando desde la restricción de privilegios por niveles y métodos de cobro rápido hasta la consolidación de facturación periódica y el manejo de cuentas pendientes.

---

## 1. Niveles de Permiso y Seguridad por Empleado

Odoo 19 reestructura los perfiles de acceso en el TPV para mitigar riesgos operativos en caja, dividiendo a los operarios en tres categorías claras:

| Nivel | Abrir / Cerrar Caja | Entradas / Salidas de Efectivo | Reembolsos y Cancelaciones | Transferencia de Mesas | Acceso al Backend |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Avanzado** | Permitido | Permitido | Permitido | Permitido | Permitido |
| **Básico** | Limitado | Limitado | Limitado | Limitado | Restringido |
| **Mínimo** | **Bloqueado** | **Bloqueado** | **Bloqueado** | **Bloqueado** | **Restringido** |

* **Configuración:** Ir a **TPV > Configuración > Ajustes** de la tienda correspondiente, buscar la sección de inicio de sesión de empleados y asignar el nivel de privilegios a cada colaborador.
* **Buenas Prácticas:** El rol *Mínimo* se recomienda para personal de apoyo o ventas puras que no deben manipular efectivo, realizar arqueos, procesar devoluciones ni ver reportes sensibles.

---

## 2. Métodos de Pago Rápido ("Pago con un Clic")

* **Funcionamiento:** Permite configurar métodos de pago específicos (ej. tarjetas débito/crédito o pasarelas) como accesos directos en la interfaz inferior de cobro.
* **Utilidad:** Al pulsar el botón rápido, la transacción se procesa instantáneamente sin necesidad de abrir la pantalla emergente de pagos, optimizando los tiempos de atención en transacciones rápidas donde no se requiere calcular devueltas de efectivo.

---

## 3. Cierre y Liquidación de Cuentas Pendientes

Permite asociar pagos diferidos directamente a las órdenes de origen para clientes con líneas de crédito autorizadas.

* **Procedimiento:** 
  1. Seleccionar al cliente con saldo adeudado en la interfaz del TPV.
  2. Desplegar el menú de acciones (tres líneas) y hacer clic en **Liquidar órdenes**.
  3. El sistema listará todas las facturas o tickets pendientes de ese tercero; seleccionar la orden específica a saldar y procesar el método de pago para garantizar una trazabilidad contable impecable.

---

## 4. Estructuración y Subcategorías de Productos

* **Navegación en Cascada:** Se incorporan subcategorías jerárquicas (ej. la categoría principal *Fibra Óptica* puede contener subcategorías como *Conectores*, *Patch Cords* y *Herramientas*).
* **Experiencia de Usuario:** La interfaz del TPV actúa como un embudo visual, permitiendo al cajero ubicar los inventarios de forma más rápida mediante clics sucesivos en los grupos y subgrupos configurados.

---

## 5. Emisión de Facturas Consolidadas

Para clientes corporativos o recurrentes que realizan múltiples compras pequeñas a lo largo de un ciclo mensual:

* **Procedimiento (Backend):** 
  1. Ir a **TPV > Órdenes**.
  2. Seleccionar las órdenes independientes del mismo cliente (deben compartir el mismo método de pago).
  3. Hacer clic en **Crear factura**, asegurándose de marcar la casilla de **Factura consolidada**.
  4. Procesar el documento resultante para cumplir con los requerimientos de facturación electrónica (DIAN en Colombia).

---

## 6. Integración con Pasarelas de Pago y Comentarios Adicionales

* **Terminales de Pago:** Soporte nativo para pasarelas de cobro electrónico (ej. Mercado Pago), donde al enviar el monto desde Odoo la terminal física procesa la transacción e imprime el recibo, confirmando el cobro en el ERP de manera automatizada.
* **Gestión de Combos Flexibles:** Permite configurar paquetes de productos (kits) permitiendo modificar o seleccionar componentes adicionales o gratuitos directamente desde la línea de la orden en el TPV.

---

## 7. Relevancia Operativa para Telcobras

Aunque gran parte de las novedades de Odoo 19 en el TPV están orientadas a la hostelería y restaurantes, Telcobras puede aprovechar las siguientes capacidades en sus puntos de atención física o mostrador de accesorios:

| Función Odoo 19 | Aplicación en Telcobras | Prioridad |
| :--- | :--- | :--- |
| **Permisos por Niveles y PIN** | Separar al personal de ventas de cajeros autorizados para retiros o cierres | Alta |
| **Cuentas Pendientes** | Control de créditos otorgados a clientes corporativos frecuentes | Media |
| **Facturas Consolidadas** | Agrupar consumos o compras menores mensuales en una sola factura fiscal | Media / Alta |
| **Subcategorías** | Organizar el catálogo físico de componentes y suministros de red | Media |

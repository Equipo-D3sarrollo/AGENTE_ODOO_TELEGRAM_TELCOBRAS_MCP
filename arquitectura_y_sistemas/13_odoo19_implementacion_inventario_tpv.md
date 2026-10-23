<!-- Path: arquitectura_y_sistemas/13_odoo19_implementacion_inventario_tpv.md -->

# 📦 Manual Operativo 13: Implementación Básica — Almacenes, Productos, Inventario y Punto de Venta (Odoo 19)

**Módulo / Área:** Comercial / Inventario y Punto de Venta (TPV)  
**Rol Requerido:** Administrador del Sistema, Encargado de Logística, Cajeros  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para realizar el recorrido fundamental de configuración de una nueva implementación en Odoo 19 Online: creación de usuarios, estructuración de almacenes, gestión y costeo de productos, carga de stock físico, apertura de tiendas TPV con asignación de cajeros y control de traslados internos.

---

## ⚠️ Advertencia Operativa e Inicial

* **Alcance:** Este manual describe el flujo estándar de implementación comercial en Odoo 19. Para el contexto de Telcobras, los identificadores fiscales (como el RUC mostrado en referencias de Perú) deben sustituirse por el **NIT con dígito de verificación** y la moneda por **Pesos Colombianos (COP)**.
* **Control de Stock:** El sistema permite por defecto operar con saldos negativos si no se configuran restricciones estrictas. Se recomienda aplicar las buenas prácticas de reposición descritas al final para evitar distorsiones en la valoración del inventario.

---

## 1. Gestión de Usuarios y Permisos

Para dar acceso a nuevos operarios al sistema, se debe evitar otorgar el rol de Administrador general por motivos de seguridad corporativa.

**Procedimiento de Alta:**
1. Dirigirse a **Ajustes > Usuarios > Nuevo**.
2. Ingresar el nombre y el **correo electrónico** (campo obligatorio, ya que el sistema enviará la invitación por este medio).
3. Asignar los permisos específicos por aplicación (ej. *Ventas: Usuario/Administrador*, *Punto de Venta: Usuario*).
4. Guardar. El usuario recibirá un enlace para configurar su contraseña y activar su cuenta. El perfil permanecerá inactivo hasta que el usuario complete este paso.

---

## 2. Configuración de Almacenes y Tipos de Operación

Cada bodega física de la compañía debe estructurarse de forma independiente en el sistema.

**Procedimiento:**
1. Ir a **Inventario > Configuración > Almacenes > Nuevo**.
2. Asignar un nombre representativo (ej. *Bodega Central*, *Almacén Sede*).
3. Guardar. Odoo generará automáticamente su ubicación interna principal (`WH/Stock`) y sus cuatro tipos de operación predeterminados:
   * **Recepciones** (Entradas de proveedores).
   * **Órdenes de entrega** (Salidas a clientes).
   * **Traslados internos** (Movimientos entre bodegas propias).
   * **Pedidos del punto de venta (PoS Orders)**.

---

## 3. Estructura de Categorías y Creación de Productos

**Categorías de Productos:**
* Ir a **Inventario > Configuración > Categorías de producto > Nuevo**.
* Definir categorías principales y jerarquías (usando la opción *Categoría padre*) para ordenar el catálogo corporativo.

**Creación de Productos:**
1. Ir a **Inventario > Productos > Nuevo**.
2. Marcar la casilla **Disponible en Punto de Venta**.
3. Seleccionar el tipo de producto como **Bienes** (indispensable para que el sistema controle existencias físicas).
4. Configurar el *Precio de venta*, *Costo* y código de barras. 
5. Guardar y verificar que los precios queden debidamente registrados en el sistema.

---

## 4. Carga de Stock Inicial (Ajuste de Inventario)

Todo producto nuevo nace con inventario en cero. Para incorporar existencias iniciales sin realizar una compra:
1. Ir a **Inventario > Operaciones > Inventario físico > Nuevo**.
2. Seleccionar la ubicación de destino exacta (ej. `WH/Stock`).
3. Agregar el producto y la cantidad real contada físicamente.
4. Pulsar en **Aplicar todo** e ingresar un motivo o referencia de control (ej. *"Inventario inicial"*) junto con la fecha.

---

## 5. Configuración y Operación del Punto de Venta (TPV)

### 5.1. Apertura de Tiendas y Vínculo con Almacenes
1. Ir a **Punto de Venta > Configuración > Ajustes**.
2. Crear una tienda nueva (ej. *Tienda Principal*).
3. En la sección de **Inventario** de la configuración de la tienda, enlazarla estrictamente con el tipo de operación de TPV correspondiente a su almacén físico. Esto garantiza que cada venta descuente stock de la bodega correcta.

### 5.2. Asignación de Empleados y PIN de Seguridad
1. Ir al módulo de **Empleados > Nuevo**, registrar al colaborador y asignarle un **PIN numérico** de seguridad en su pestaña de configuración de RR. HH.
2. En el TPV, configurar los accesos para que cada cajero abra su sesión de caja utilizando exclusivamente su PIN personal, asegurando la trazabilidad de las operaciones.

### 5.3. Apertura de Caja y Ejecución de Ventas
1. Ingresar a la tienda TPV asignada, desbloquear la caja introduciendo el PIN del cajero.
2. Seleccionar los productos solicitados por el cliente, pulsar **Pagar** y **Validar** para emitir el comprobante y descontar automáticamente las unidades del almacén vinculado.

---

## 6. Traslados Internos y Control de Stock Negativo

* **Prevención de Negativos:** Si una tienda vende unidades sin stock disponible, el sistema permitirá la transacción dejando la existencia en negativo. Para corregirlo, se deben ejecutar traslados internos de reposición antes de abrir las cajas.
* **Procedimiento de Traslado:**
  1. Ir a **Inventario > Operaciones > Transferencias > Nuevo**.
  2. Seleccionar el tipo de operación **Traslado interno**.
  3. Indicar la *Ubicación de origen* y la *Ubicación de destino*.
  4. Agregar el producto y la cantidad a transferir.
  5. Guardar y **Validar** para oficializar el movimiento de stock entre bodegas.

---

## 7. Identidad Corporativa y Datos de Compañía

Para garantizar que los comprobantes del TPV y los documentos de inventario reflejen la imagen oficial de Telcobras:
1. Ir a **Ajustes > Compañías > Actualizar información**.
2. Subir el logotipo corporativo.
3. Rellenar los campos legales: Dirección, **NIT con dígito de verificación**, moneda oficial (COP) y sitio web.
4. Guardar cambios para aplicarlos de forma inmediata en las impresiones y tickets del sistema.

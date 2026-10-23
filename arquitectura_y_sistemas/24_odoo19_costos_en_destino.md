<!-- Path: financiero/24_odoo19_costos_en_destino.md -->

# 📦 Manual Operativo 24: Automatización y Gestión de Costos en Destino (Landed Costs) en Odoo 19

**Módulo / Área:** Financiero y Logística / Contabilidad e Inventarios  
**Rol Requerido:** Director Financiero, Contador, Analistas de Compras y Logística  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para configurar e incorporar de forma automatizada los gastos adicionales de adquisición (fletes, seguros, aranceles, transporte) al valor real de los inventarios en Odoo 19, garantizando la precisión en la valoración de stock y el cálculo de márgenes comerciales en el estado de resultados.

---

## 1. Fundamentos Contables y Logísticos

* **Definición:** El costo real de un producto no se limita a su precio de factura; comprende la sumatoria de la adquisición más los gastos de traslado y nacionalización hasta las bodegas de la compañía (*Costos en Destino* o *Landed Costs*).
* **Impacto Financiero:** No registrar estos costos distorsiona la valoración del inventario y arroja márgenes de utilidad irreales, afectando directamente el balance general y el estado de resultados.

---

## 2. Requisitos y Activación de la Función

**Requisito Base:** Tener instaladas las aplicaciones de **Contabilidad** e **Inventario**.

1. Ir a **Inventario > Configuración > Ajustes**, localizar la sección de *Valoración* y activar la casilla **Costos en destino**.
2. **Configuración de Categoría:** Asegurarse de que las categorías de los productos afectados utilicen métodos de costeo **FIFO** o **Costo Promedio** (el precio estándar no recalcula variaciones) y tengan configurada la **Valoración de inventario permanente (automatizada)** con sus respectivas cuentas contables.

---

## 3. Parametrización del Producto de Gasto (Servicio)

Para imputar los fletes o gastos, debe crearse o configurarse un producto dedicado:
1. Ir a **Inventario > Productos** y crear un ítem con tipo de producto **Servicio** (el flete no se almacena físicamente).
2. Marcar únicamente la casilla **Se puede comprar** y asignar el costo base en `0` (para que se ajuste dinámicamente en cada orden).
3. En la pestaña de *Compras*, marcar la opción **¿Es un costo en destino?** (esta casilla solo se habilita si el producto es de tipo *Servicio*).
4. Definir el **método de división predeterminado** según la naturaleza del gasto:
   * **Igual:** Reparte el valor en partes iguales por línea de producto.
   * **Por cantidad:** Divide el costo proporcionalmente al número de unidades físicas.
   * **Por costo actual:** Prorratea el gasto en función del valor monetario de cada línea.
   * **Por peso / volumen:** Reparte el costo de acuerdo con las especificaciones físicas de la carga.

---

## 4. Flujo Operativo y Aplicación en una Compra

1. **Orden de Compra:** Crear la orden incluyendo el producto adquirido y una línea adicional con el producto de servicio configurado como *Costo en destino* (ej. Flete) y su valor cotizado.
2. **Recepción Logística:** Confirmar la compra, ingresar al módulo de Inventario y **validar la recepción** de la mercancía. Copiar la referencia del documento (ej. `WH/IN/000XX`).
3. **Facturación y Costos en Destino:** 
   * Volver a la orden de compra y hacer clic en **Subir factura** ingresando la fecha correspondiente.
   * Desde el documento de factura, seleccionar la opción **Crear costos en destino**.
   * En el formulario generado, enlazar la recepción previamente validada en el campo **Traslados**.
   * Ajustar el valor final si la factura del transportista difiere del estimado, pulsar **Calcular** y finalmente **Validar**.

---

## 5. Verificación y Impacto en la Valoración

* **Actualización Automática:** En la pestaña de *Ajustes de valoración* del costo en destino validado, el sistema desglosará el valor original de adquisición, el monto del costo en destino asignado y el nuevo costo unitario ponderado de cada artículo.
* **Margen Comercial:** Al realizar futuras ventas de estos ítems, Odoo deducirá el costo real integrado, reflejando una utilidad operativa fidedigna.

---

## 6. Directrices y Consideraciones para Telcobras

| Caso de Aplicación | Gasto Asociado | Método de División Sugerido |
| :--- | :--- | :--- |
| **Transporte de Bobinas y Cables** | Flete de traslado a bodega | **Por peso** |
| **Adquisición de Equipos (ONT, Routers)** | Flete y seguros de trayecto | **Por costo actual** |
| **Importaciones de Infraestructura** | Fletes internacionales, aranceles y aduanas | **Por costo actual** o **Por peso** (creando un producto de servicio por cada concepto) |

* **Punto de Control Contable:** Los fletes asociados a compras destinadas directamente a obras de instalación deben evaluarse junto con el departamento financiero para determinar si se capitalizan al inventario o se imputan directamente al costo del proyecto, asegurando la coherencia con los flujos de causación de la compañía.

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Automatización de Costos en Destino en Odoo 19 (Activación de landed costs, configuración de categorías de inventario FIFO/Promedio, parametrización de servicios de flete, métodos de división y aplicación contable en compras): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/financiero/24_odoo19_costos_en_destino.md

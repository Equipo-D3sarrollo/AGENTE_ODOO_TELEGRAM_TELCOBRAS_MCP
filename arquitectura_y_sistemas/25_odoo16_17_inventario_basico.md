````markdown
<!-- Path: arquitectura_y_sistemas/25_odoo16_17_inventario_basico.md -->

# 📦 Manual Operativo 25: Fundamentos Históricos del Módulo de Inventario en Odoo (Versiones Previas)

**Módulo / Área:** Arquitectura y Sistemas / Gestión Logística (Histórico)  
**Clasificación:** SOPORTE DE CONSULTA / NO PRODUCCIÓN (Aplica como referencia conceptual para estructuras v.16-17)  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este documento exclusivamente como referencia conceptual de los principios base del módulo de Inventario en versiones anteriores (16-17), recordando que para la operación oficial en producción de Telcobras se rige bajo los estándares de Odoo 19.

---

## 1. Principios Generales del Almacén y Tablero

El tablero principal de la aplicación de Inventario agrupa las operaciones por almacenes individuales, mostrando de manera directa el flujo operativo de:
* **Recepciones:** Entradas de mercancía de proveedores.
* **Traslados internos:** Movimientos de stock entre ubicaciones o bodegas propias.
* **Órdenes de entrega:** Despachos a clientes finales.
* **Órdenes de TPV:** Salidas automáticas de punto de venta.

---

## 2. Creación y Configuración de Almacenes

**Ruta:** Inventario > Configuración > Almacenes > Nuevo

* **Identificadores Cortos:** Al crear un almacén (ej. *Sucursal 2*), se define un código corto (prefijo) que el sistema utilizará de forma automática para nombrar las ubicaciones internas y numerar los documentos logísticos asociados (ej. prefijo `SU2`).
* **Buenas Prácticas de Nomenclatura:** Se recomienda definir códigos limpios y estandarizados antes de iniciar operaciones (ej. `BOG` para bodega principal o `OBR` para bodegas de obra), ya que modificar los prefijos posteriormente genera inconsistencias en los consecutivos de documentos históricos.

---

## 3. Control de Existencias y Diferencia Operativa (Ajustes vs. Compras)

El sistema contempla dos vías principales para incorporar existencias a las ubicaciones de stock, cada una con implicaciones operativas y de trazabilidad distintas:

| Método de Entrada | Propósito / Cuándo Usarlo | Implicación Contable y Trazabilidad |
| :--- | :--- | :--- |
| **Ajuste de Inventario (Inventario Físico)** | Carga de **inventario inicial** al arrancar con Odoo o correcciones por conteos periódicos / mermas. | Modifica las existencias físicas de inmediato pero carece de un documento comercial de origen (orden de compra). |
| **Orden de Compra y Recepción** | Operación estándar para el abastecimiento normal de mercancía a proveedores. | Vincula el stock recibido con el documento de compra, el costo real del proveedor y la factura contable. |

* **Regla Operativa:** Salvo el cargue inicial del sistema y ajustes extraordinarios por conteo físico o daño, **todo el stock de Telcobras debe ingresar obligatoriamente a través del flujo de compras y recepciones** para preservar la trazabilidad del costo.

---

## 4. Reportes de Existencias

**Ruta:** Inventario > Reportes > Existencias

Permite auditar en tiempo real la situación del inventario corporativo, mostrando por cada producto la cantidad disponible, el costo unitario y el valor total valorizado, con filtros avanzados por categorías y tipos de artículo.

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Fundamentos Históricos del Módulo de Inventario en Odoo (Versiones 16-17): Estructura de almacenes, prefijos de ubicación, reportes de existencias y diferenciación metodológica entre ajustes de inventario físico y recepciones por compras: [https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/25_odoo16_17_inventario_basico.md](https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/25_odoo16_17_inventario_basico.md)

<!-- Path: comercial/16_odoo19_crm_ventas_novedades.md -->

# 📈 Manual Operativo 16: Novedades del Ciclo Comercial — CRM y Ventas en Odoo 19

**Módulo / Área:** Comercial / CRM y Ventas  
**Rol Requerido:** Ejecutivos de Ventas, Directores Comerciales, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para conocer y aplicar las nuevas capacidades de gestión del embudo comercial en Odoo 19, abarcando desde la captura ágil de leads y la asignación inteligente por prioridades hasta la creación de cotizaciones estructuradas, cobro de anticipos por portal y cálculo de comisiones.

---

## 1. Captura de Leads: Escáner de Tarjetas de Presentación

Permite digitalizar el contacto de forma inmediata desde ferias o visitas a terreno, eliminando la carga manual.

* **Procedimiento (Móvil):** Utilizar la aplicación móvil oficial de Odoo, abrir el módulo de CRM y activar la función de escaneo sobre la tarjeta física. El sistema procesará la imagen y creará el registro del lead de manera automática.
* **Procedimiento (Escritorio):** Ir al pipeline de **CRM > Acciones (engranaje) > Importar / escanear tarjeta de presentación**, adjuntar la imagen y verificar que campos clave como nombre, empresa, correo y teléfono queden correctamente indexados. La imagen original quedará guardada en el historial de la oportunidad.

---

## 2. Asignación Inteligente de Leads por Prioridad

Odoo 19 introduce reglas nativas para distribuir oportunidades de manera eficiente sin requerir scripts complejos de IA.

* **Configuración:** En los ajustes del **Equipo de Ventas**, definir reglas de prioridad y filtros personalizados vinculados a las características de los prospectos (ej. tipo de cliente B2B, región o servicio requerido).
* **Comportamiento:** Los leads que cumplan con los criterios de alta prioridad se enrutarán primero al colaborador asignado (ej. comerciales senior), manteniendo abierta la distribución general para el resto del equipo si se superan ciertos umbrales.

---

## 3. Predicción de Cierre mediante Inteligencia Artificial

* **Funcionamiento:** En la vista de cada oportunidad, el campo de **Probabilidad** incorpora un asistente de IA impulsado por *machine learning*.
* **Utilidad Comercial:** Analiza el historial de conversaciones, la validez de los datos de contacto y el rendimiento histórico del embudo para calcular un porcentaje de éxito estimado, ayudando al equipo de ventas a enfocar sus esfuerzos en los negocios con mayor potencial de cierre.

---

## 4. Estructuración Avanzada de Cotizaciones

### 4.1. Catálogo con Secciones y Subsecciones
* Al elaborar presupuestos desde el CRM o Ventas, la vista de **Catálogo** permite agrupar los ítems directamente en secciones con nombre (ej. por frentes de obra o zonas geográficas). 
* Es posible ocultar precios de componentes menores (como tornillería o insumos de bajo valor) o condensar detalles complejos detrás de notas descriptivas para facilitar la lectura del cliente.

### 4.2. Edición Dinámica de Combos
* Si se añaden productos compuestos (*combos* o kits de servicios), Odoo 19 permite modificar y personalizar sus componentes internos directamente desde la línea de la cotización sin necesidad de reconfigurar el producto original en el inventario.

### 4.3. Opciones de Pago Único o Suscripción
* Permite ofrecer alternativas de comercialización mixtas en un mismo presupuesto (ej. adquisición de equipos mediante pago único frente a una modalidad de soporte o servicio recurrente mensual).

---

## 5. Gestión de Anticipos y Pagos por el Portal del Cliente

* **Flujo Operativo:** Al enviar el enlace del portal web al cliente, este puede visualizar el presupuesto y seleccionar autónomamente si desea cancelar un **porcentaje de anticipo** (definido previamente por la política de Telcobras) o el pago total.
* **Conciliación Automática:** Al procesar el pago en línea, la cotización se convierte automáticamente en orden de venta, y la factura generada posterior registra el abono parcial de forma directa, sincronizando la tesorería sin intervención manual.

---

## 6. Planes de Comisiones Comerciales

**Ruta:** Ventas > Comisiones > Planes de comisiones > Nuevo

* **Criterios Soportados:** Permite configurar esquemas de incentivos basados no solo en volumen de venta, sino también en el **margen de ganancia** de los productos o en el **MRR** (ingreso recurrente mensual) generado por servicios de suscripción.
* **Parametrización:** Se definen las tasas porcentuales, los periodos de vigencia y los colaboradores o equipos a los que aplica cada plan de compensación.

---

## 7. Priorización e Impacto en Telcobras

| Función Odoo 19 | Aplicación Operativa en Telcobras | Prioridad |
| :--- | :--- | :--- |
| **Secciones en Cotizaciones** | Cotizar proyectos de infraestructura de red segregados por tramos u obra civil | Alta |
| **Anticipo por Portal** | Recaudar anticipos obligatorios antes de iniciar la ejecución de proyectos | Alta |
| **Asignación por Prioridad** | Derivar prospectos corporativos (B2B) a ejecutivos especializados | Alta |
| **Escáner de Tarjetas** | Captura rápida de contactos en eventos y visitas comerciales a terreno | Media |
| **Planes por Margen** | Incentivar al equipo comercial midiendo rentabilidad real y no solo facturación | Media |

<!-- Path: arquitectura_y_sistemas/09_conexion_mcp_claude.md -->

# ⚙️ Manual Operativo: Conexión Odoo 19 con Claude vía MCP (Model Context Protocol)

**Módulo / Área:** Arquitectura y Sistemas / Inteligencia Artificial  
**Rol Requerido:** Administrador de Sistemas, Arquitecto de Software, Desarrollador Integrador  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para entender la arquitectura de integración entre asistentes de IA externos (como Claude o ChatGPT) y Odoo 19 utilizando servidores MCP, así como las restricciones y adaptaciones necesarias para el entorno SaaS de Telcobras.

---

## 1. Arquitectura de Integración (Restricción Odoo Online)

El estándar de la industria (mostrado por Gemastic) utiliza módulos personalizados instalados dentro de Odoo Community para exponer los datos al LLM. 
📌 **RESTRICCIÓN CRÍTICA TELCOBRAS:** Telcobras opera sobre **Odoo 19 Online (SaaS)**, entorno que prohíbe la instalación de módulos de terceros.

🔹 **Solución Arquitectónica (Desacoplada):** 
Para lograr la misma integración sin instalar módulos propios, Telcobras debe desplegar un **Servidor MCP Externo** (ej. alojado en la infraestructura cloud de la empresa). 
* Este servidor actúa como puente: se conecta a Claude/ChatGPT mediante el estándar MCP, y se comunica con Odoo 19 Online utilizando la **API externa nativa (JSON-2 o XML-RPC)**.

---

## 2. Capacidades de Consulta en Lenguaje Natural (NLP)

Una vez establecido el puente MCP, el agente de IA puede consumir datos del ERP en tiempo real y responder en lenguaje natural. Las capacidades probadas incluyen:

🔹 **Módulo de Ventas:**
* Solicitar resúmenes mensuales de ventas.
* Diferenciar automáticamente entre cotizaciones (estado borrador) y ventas confirmadas.

🔹 **Módulo de Compras:**
* Listar comprobantes de gastos.
* Calcular montos ejecutados frente a montos pendientes (borradores).

🔹 **Consultas Transversales (Soportadas por el modelo de datos):**
* Consultar clientes y oportunidades del CRM.
* Revisar el estado de pedidos.
* Extraer niveles de inventario y fichas de productos.

---

## 3. Seguridad y Permisos por Roles

La seguridad de la integración no depende de la IA, sino de las credenciales de acceso a Odoo.

📌 **Regla de Acceso:** El servidor MCP no debe conectarse a Odoo con credenciales de administrador (Superuser).
* Se deben generar **API Keys por usuario/rol**.
* Si el asistente es consultado por alguien de Recursos Humanos, el MCP usará la API Key de RR.HH., limitando la visibilidad del LLM exclusivamente a esos módulos e impidiendo que acceda a datos contables o de ventas.

---

## 4. Advertencias Financieras y Operativas

El uso de asistentes LLM para calcular métricas financieras requiere extrema precaución:

⚠️ **Cálculo de "Utilidad":** Un LLM estándar tiende a calcular la utilidad restando las Órdenes de Compra a las Órdenes de Venta. 
📌 **Política Telcobras:** Este cálculo es financieramente incorrecto, ya que ignora nómina, caja menor, impuestos y causaciones. El Agente de IA tiene **prohibido** calcular márgenes o utilidades cruzando pedidos. Para datos de rentabilidad, el servidor MCP debe programarse para leer únicamente los **Informes Contables oficiales (Estado de Resultados)** del módulo de Contabilidad.

<!-- Path: arquitectura_y_sistemas/08_ia_nativa_odoo19.md -->

# Manual Operativo: Funciones Nativas de Inteligencia Artificial en Odoo 19

**Módulo / Área:** Arquitectura y Sistemas / Inteligencia Artificial  
**Rol Requerido:** Administrador del Sistema, Líder de Desarrollo, Equipo Comercial y Finanzas  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para configurar, gestionar y desplegar las capacidades nativas de Inteligencia Artificial que incluye Odoo 19, abarcando desde agentes de atención al cliente hasta automatizaciones complejas en CRM, Documentos y reportes.

---

## 1. Preparación del Entorno

Para que las herramientas de IA funcionen correctamente y tengan contexto, el entorno debe cumplir con un requisito fundamental:
* **Datos existentes:** La base de datos de Odoo 19 debe contener transacciones previas (ventas, compras, contactos, documentos) para que los agentes puedan realizar consultas y analíticas reales.
* **Instalación:** Acceder al módulo de **Aplicaciones**, buscar **"IA"** o **"AI"** e instalar la aplicación principal de Inteligencia Artificial. Esto desplegará el panel de control central de agentes.

---

## 2. Configuración Core de Agentes de IA

Desde la aplicación de IA, es posible editar agentes preconfigurados o crear nuevos. Todo agente se configura definiendo 5 parámetros estructurales:

1. **Modelo LLM:** Selección del motor de procesamiento (ej. versiones específicas de ChatGPT o Gemini).
2. **Estilo de respuesta:** Tono comunicacional del agente (Analítico, Equilibrado, Creativo).
3. **Fuentes Limitadas (RAG):** Restricción estricta de conocimiento. Si se carga un documento (ej. PDF de Políticas de Reembolso), el agente responderá *exclusivamente* basándose en ese archivo, ignorando su conocimiento general.
4. **Temas / Capacidades:** Permisos operativos otorgados al agente:
   * Búsqueda e interacción en lenguaje natural.
   * Consulta directa de información transaccional en la base de datos de Odoo.
   * Capacidad de crear registros (ej. creación de leads).
5. **Prompt (Instrucción Maestra):** Reglas de comportamiento y directrices de cómo debe reaccionar ante el usuario.

---

## 3. Casos de Uso y Automatizaciones Operativas

La IA de Odoo 19 se integra transversalmente en diferentes módulos de Telcobras. A continuación, los procedimientos para habilitar cada función:

### 3.1. Asistente Web para Preguntas Frecuentes (Módulo Sitio Web)
Permite a los clientes consultar información corporativa (términos, condiciones, portafolio) mediante una barra de búsqueda inteligente.
* **Procedimiento:** Entrar al editor del Sitio Web, arrastrar el bloque de "Inteligencia Artificial" y vincularlo a un agente configurado con **Fuentes Limitadas** (ej. un PDF corporativo).
* **Comportamiento:** El agente lee la consulta del cliente y extrae la respuesta del PDF cargado, citando incluso la sección del documento.

### 3.2. Captación Automática de Leads (Módulo CRM / Live Chat)
Se utiliza el agente preconfigurado *"Live Chat AI agent"*.
* **Procedimiento:** El agente interactúa con el visitante del sitio web, califica su interés mediante preguntas segmentadas y solicita sus datos de contacto.
* **Resultado automático:** Odoo crea inmediatamente un Lead en el CRM, inyectando el correo, teléfono y asignando una etiqueta automática basada en la intención de compra detectada (ej. "Interés en Conectividad").

### 3.3. Enrutamiento Inteligente de Oportunidades (Módulo CRM)
Evita la asignación manual de vendedores analizando el nombre o contenido del lead.
* **Procedimiento:** 
  1. Ir a CRM > Automatizaciones.
  2. **Modelo:** Lead/Oportunidad.
  3. **Disparador:** Al actualizar la Oportunidad.
  4. **Acción:** Seleccionar *"Actualizar con inteligencia artificial"*.
  5. **Campo a actualizar:** Vendedor.
  6. **Prompt:** Definir las reglas lógicas escribiendo "/" para referenciar campos. (Ej. *Si la industria es Automotriz = Asignar a Regina; Si es Alimentaria = Asignar a Valentina*).

### 3.4. Análisis y Resumen de Documentos Adjuntos (Módulo CRM)
La IA lee pliegos o requisiciones extensas enviadas por clientes y extrae los datos vitales.
* **Procedimiento:** 
  1. En las automatizaciones del CRM, configurar un disparador al cargar un documento en un campo de archivo adjunto.
  2. **Acción:** *"Actualizar con IA"*.
  3. **Campo destino:** Seleccionar un campo HTML (como "Notas").
  4. **Prompt:** Solicitar a la IA que lea el adjunto y genere una lista de *Puntos importantes* y *Riesgos potenciales*, utilizando formato HTML para estructurar la visual.

### 3.5. Causación y Organización Automática (Módulo Documentos)
Elimina el trabajo manual de clasificar archivos y crear facturas.
* **Procedimiento:** Configurar la función de *"Ordenado automático"* con un prompt de reglas condicionales:
  * Si es un contrato -> Mover a carpeta **Legal**.
  * Si es una credencial -> Mover a carpeta **Empleados**.
  * Si es una factura con el NIT de Telcobras -> Ejecutar acción *"Crear factura de proveedor"* y mover a carpeta **Finanzas**.
  * Si es una factura externa -> Ejecutar acción *"Crear factura de cliente"*.
* **Resultado:** Odoo extrae el importe, asocia el PDF y genera el registro contable en segundos.

### 3.6. Reportes en Lenguaje Natural (Transversal)
Mediante el chat interno, los usuarios de Telcobras pueden consultar el estado de la empresa sin navegar por menús complejos.
* **Ejemplos de Prompts soportados:**
  * *"Genera una gráfica de pastel con las ventas realizadas por cada vendedor."*
  * *"Muéstrame las facturas por cobrar que vencen esta semana."*
  * *"Lista las facturas por pagar urgentes."*

---

## 4. Tabla de Resumen Técnico

| Módulo Afectado | Herramienta / Función | Uso Principal en Telcobras |
| :--- | :--- | :--- |
| **Aplicación IA** | Panel de Agentes | Base para configurar modelos, prompts y fuentes de conocimiento (RAG). |
| **Sitio Web** | Bloque IA (Fuentes limitadas) | Resolución de dudas de clientes basándose estrictamente en PDFs corporativos. |
| **Live Chat / CRM** | Agente Captador | Calificación de visitantes, solicitud de datos y creación automática de Leads. |
| **CRM** | Actualizar con IA (Automatización) | Enrutamiento automático de vendedores y resumen automático de PDFs de clientes. |
| **Documentos** | Ordenado automático | Clasificación de archivos y generación automática de facturas de proveedor/cliente. |
| **Base de Datos** | Consultas en lenguaje natural | Generación de gráficos y reportes financieros o de ventas mediante chat. |

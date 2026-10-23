<!-- Path: arquitectura_y_sistemas/14_odoo19_configuracion_inicial.md -->

# ⚙️ Manual Operativo 14: Configuración Inicial de Odoo 19 — Idioma, Compañía, Impresión, Correos y Facturación Electrónica

**Módulo / Área:** Arquitectura y Sistemas / Configuración General y Facturación  
**Rol Requerido:** Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para realizar la parametrización base de una nueva instancia de Odoo 19 Online, abarcando desde la localización regional y el diseño de documentos comerciales hasta la gestión de correos de resumen y los requisitos de facturación electrónica.

---

## 1. Localización Regional e Idioma

Al inicializar una instancia, la interfaz suele presentarse en inglés. Es necesario configurar el idioma y la localización para asegurar el formato correcto de fechas, números y normativas.

**Procedimiento:**
1. Dirigirse a **Settings > Languages > Add Languages**[cite: 26].
2. Buscar **Spanish**, seleccionar la variante correspondiente al país de operación (para el caso de Telcobras en Colombia, elegir **Español (CO)**) y hacer clic en **Add**[cite: 26].
3. Aplicar los cambios aceptando el cambio de interfaz o estableciendo el idioma como predeterminado en las preferencias del usuario de Odoo[cite: 26].

---

## 2. Datos Generales de la Compañía

**Ruta:** Ajustes > Compañías > Actualizar información[cite: 26]

Se deben configurar con exactitud los datos fiscales y de contacto corporativo:
* **Razón Social y Identificación:** Registrar el nombre legal y el **NIT con dígito de verificación**[cite: 26].
* **Moneda:** Configurar la moneda oficial de operación (Pesos Colombianos - COP)[cite: 26].
* **Canal de Correo:** Ingresar el correo corporativo con **dominio propio** (ej. `@telcobras.com`). Este será el remitente oficial desde el cual el ERP enviará cotizaciones, facturas y alertas a los clientes[cite: 26].
* **Logotipo e Identidad:** Cargar el archivo del logotipo institucional[cite: 26].

---

## 3. Diseño y Formato de Documentos Impresos

Odoo 19 permite personalizar la apariencia visual de presupuestos, facturas y albaranes mediante una herramienta de vista previa en tiempo real.

**Ruta:** Ajustes > Compañías > Configurar diseño del documento[cite: 26]

* **Plantilla y Estilos:** Seleccionar el diseño general (*Light* por defecto) y el formato de tablas acorde a la línea gráfica institucional[cite: 26].
* **Formato de Papel:** Asegurarse de configurar el tamaño de hoja correcto (**Carta** para Colombia, reemplazando el estándar A4)[cite: 26].
* **Elementos Informativos:** Incorporar el lema corporativo, las cuentas bancarias autorizadas en el pie de página y verificar la correcta visualización de los datos fiscales y el código QR exigido por normativas de facturación[cite: 26].

---

## 4. Gestión de Servidores de Correo

* **Opción por Defecto:** Odoo permite utilizar sus propios servidores integrados para la salida de correos electrónicos corporativos, lo cual simplifica la puesta en marcha inicial[cite: 26].
* **Dominio Propio (Recomendado):** Para evitar que las notificaciones de facturación o cobro sean clasificadas como *spam*, se deben configurar servidores de correo saliente y entrante personalizados (SMTP/IMAP) asegurando la correcta implementación de registros DNS corporativos (SPF, DKIM y DMARC).

---

## 5. Correos de Resumen Automático (Informes Periódicos)

Esta función permite programar envíos automáticos a perfiles directivos con un resumen analítico del desempeño del negocio.

**Ruta:** Ajustes > Correo electrónico de resumen > Configurar correos de resumen[cite: 26]

* **Configuración:** Permite definir reportes con frecuencia diaria, semanal, mensual o trimestral[cite: 26].
* **Indicadores Clave:** Es posible seleccionar qué módulos incluir para evitar la saturación de la bandeja de entrada, priorizando métricas vitales como *nuevos leads del CRM*, *oportunidades ganadas*, *visitas al sitio web* y *ventas del punto de venta*[cite: 26].

---

## 6. Seguridad y Restricción de Plantillas

**Ruta:** Ajustes > Restringir la creación de plantillas[cite: 26]

* **Política Operativa:** Se recomienda mantener **activada** esta directiva de seguridad. Su propósito es impedir que usuarios sin perfil de administrador modifiquen la estructura, el código o el diseño corporativo de las plantillas de correo y los documentos legales, evitando alteraciones accidentales de los textos reglamentarios o logotipos institucionales[cite: 26].

---

## 7. Facturación Electrónica y Certificado Digital

* **Requisito Legal:** Para la emisión de documentos electrónicos tributarios, el sistema debe enlazarse con la autoridad fiscal correspondiente (DIAN en Colombia)[cite: 26].
* **Gestión del Certificado:** Dependiendo del proveedor tecnológico de facturación electrónica contratado por Telcobras, la firma digital puede gestionarse directamente a través de las pasarelas del proveedor o cargando los archivos del certificado digital (clave pública y privada) desde el menú de ajustes de Odoo[cite: 26].

---

## 8. Activación del Modo Desarrollador

**Ruta:** Ajustes > Activar el modo desarrollador (ubicado al final de la página)[cite: 26]

* **Uso Restringido:** Este modo habilita opciones avanzadas de personalización, campos técnicos y vistas de depuración. Se identifica visualmente por el icono de un escarabajo (bug) en la barra superior[cite: 26]. 
* **Buena Práctica:** Debe mantenerse activo exclusivamente durante las tareas de parametrización técnica y desactivarse al concluir para proteger la integridad de la interfaz ante operarios estándar.

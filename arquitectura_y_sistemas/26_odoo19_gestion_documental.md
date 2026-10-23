<!-- Path: arquitectura_y_sistemas/26_odoo19_gestion_documental.md -->

# 📄 Manual Operativo 26: Gestión Documental Centralizada y Digitalización en Odoo 19

**Módulo / Área:** Arquitectura y Sistemas / Transversal (Aplicación Documentos)  
**Rol Requerido:** Administrador del Sistema, Jefes de Área, Equipo Contable y RR. HH.  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para configurar el repositorio digital de la empresa, establecer políticas de permisos, automatizar la recepción de facturas mediante OCR y gestionar archivos PDF de manera colaborativa y segura sin depender del papel físico.

---

## 1. Arquitectura de Carpetas y Seguridad de Acceso

La aplicación de Documentos centraliza todos los archivos corporativos en un único repositorio organizado por carpetas y subcarpetas[cite: 20]. 

### 1.1. Niveles de Permiso
* **Usuarios Internos:** Los privilegios se dividen en **Ninguno** (solo el propietario ve y edita), **Lector** (solo visualización) y **Editor** (visualización y modificación)[cite: 20].
* **Accesos Externos:** Al compartir un enlace público, se debe otorgar nivel de **Lector** o **Editor**, aplicando obligatoriamente una **fecha de vencimiento** al enlace por políticas de seguridad[cite: 20].
* **Accesos Directos:** Para visualizar un archivo en múltiples carpetas, se debe crear un "acceso directo", evitando duplicar el archivo original en el sistema[cite: 20].

### 1.2. Estructura Sugerida para Telcobras
| Área / Carpeta principal | Subcarpetas | Permisos y Seguridad |
| :--- | :--- | :--- |
| **Contabilidad** | Facturas de proveedor, Soportes de caja menor, Extractos bancarios | Contabilidad (Editor), Gerencia (Lector)[cite: 20]. |
| **Recursos Humanos** | Hojas de vida, Contratos, Certificaciones (Alturas), Incapacidades | Estrictamente RR. HH.[cite: 20] Contiene datos sensibles. |
| **Comercial** | Propuestas técnicas, Cotizaciones firmadas, Contratos de clientes | Comercial (Editor), Gerencia (Lector)[cite: 20]. |
| **Operaciones / Proyectos** | Actas de entrega, Planos, Registros fotográficos de obra | Miembros específicos del proyecto (Editor)[cite: 20]. |
| **Legal** | Acuerdos (NDA), Pólizas, Representación legal | Gerencia (Editor)[cite: 20]. |

---

## 2. Métodos de Ingreso y Recolección de Archivos

Odoo 19 permite alimentar el repositorio documental mediante tres vías principales[cite: 20]:
1. **Subida Directa:** Carga manual desde el ordenador por parte de usuarios con permisos[cite: 20].
2. **Correo Electrónico Dedicado (Alias):** Se puede configurar una dirección de correo para una carpeta específica. Todo archivo adjunto enviado a esa dirección ingresa automáticamente a la carpeta (ej. recepción automatizada de facturas de proveedores)[cite: 20].
3. **Solicitud de Documentos:** Permite crear una solicitud a un empleado o cliente asignando un responsable. El sistema crea un "marcador de posición" (placeholder) en la carpeta que será reemplazado automáticamente por el archivo real una vez la persona lo suba a través de su portal[cite: 20].

---

## 3. Herramientas de Edición y Manipulación de PDF

Odoo elimina la necesidad de software externo para modificar archivos escaneados:
* **Escaneo por Lotes:** Una impresora física puede enviar un bloque completo de hojas escaneadas al correo de la carpeta[cite: 20].
* **Dividir (Split):** Al visualizar el PDF, la interfaz muestra marcadores de corte entre páginas. Se pueden activar estos cortes para separar un archivo extenso (ej. un PDF de 6 páginas se convierte en dos PDF de 3 páginas)[cite: 20].
* **Unir (Merge):** Seleccionando múltiples documentos en la carpeta, se puede ejecutar la opción de unirlos, retirando los cortes para fusionarlos en un único archivo[cite: 20].

---

## 4. Automatización con OCR y Flujos de Trabajo (Acciones de un clic)

El sistema soporta acciones personalizadas que agilizan las tareas administrativas repetitivas mediante Reconocimiento Óptico de Caracteres (OCR)[cite: 20]:
* **Causación 2.0 (Facturas de Proveedor):** Al recibir el PDF de una factura en la carpeta de Contabilidad, el usuario puede presionar la acción de un clic **"Crear factura de proveedor"**. La IA (OCR) extraerá los datos del documento y generará el borrador contable de la factura sin necesidad de digitación manual[cite: 20].

---

## 5. Herramientas de Colaboración y Portal de Clientes

* **Comunicación Interna:** Cada documento posee un *chatter* (historial de notas) para mencionar usuarios, dejar comentarios (ej. notificar errores en una factura) o programar actividades (llamadas, reuniones)[cite: 20].
* **Integración Omnicanal:** Es posible iniciar una conversación de WhatsApp con el contacto vinculado directamente desde la vista del documento[cite: 20].
* **Portal y Firma Electrónica:** Mediante el portal web, los clientes pueden revisar sus documentos asignados y utilizar la app de Firma (*Sign*) para firmar digitalmente contratos o actas, sin imprimir ni escanear documentos físicos[cite: 20].

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Gestión Documental Odoo 19 (Estructura de carpetas, permisos, recolección por alias de correo, manipulación de PDF por lotes, extracción OCR para facturas de proveedores y colaboración): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/26_odoo19_gestion_documental.md

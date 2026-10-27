<!-- Path: arquitectura_y_sistemas/README.md -->

# 🏛️ Categoría: Arquitectura y Sistemas - Telcobras S.A.S.

## 📖 ¿Qué es y para qué se usa esta categoría?
La categoría **Arquitectura y Sistemas** concentra toda la documentación técnica de infraestructura cloud, arquitectura de software, integración de sistemas de IA y los manuales operativos de implementación y soporte para las soluciones digitales de Telcobras S.A.S.

**¿Para qué se usa?**
* **Operación y Monitoreo:** Guía a operadores, administradores y equipos técnicos en la lectura de estados, gestión de cuentas, operación de válvulas y soporte de infraestructura crítica.
* **Soporte Técnico y Servidor:** Proporciona runbooks, protocolos de despliegue, solución de errores y gestión de la máquina virtual de producción.
* **Arquitectura y Desarrollo:** Documenta la integración de agentes de IA, patrones desacoplados, esquemas de notificaciones y la planeación tecnológica del producto y del ecosistema digital.

---

## 📑 Índice de Documentos Actualizados

| # | Nombre del Archivo | Descripción General |
| :--- | :--- | :--- |
| **01** | `01_integracion_mcp_github.md` | Integración de MCP con GitHub para recuperación y consulta de documentación técnica desde repositorios. |
| **02** | `02_frescura_conexion_aquora.md` | Diagnóstico y monitoreo de la frescura y conexión de Aquora para plantas y servicios críticos. |
| **03** | `03_evaluacion_hosting_cloud.md` | Evaluación de alternativas de hosting y plataformas cloud para la solución tecnológica. |
| **04** | `04_manual_centro_de_gestion_aquora.md` | Manual operativo del Centro de Gestión Aquora con roles, permisos, válvulas y mantenimiento de servidor. |
| **05** | `05_propuesta_tecnica_asistente_ia.md` | Propuesta técnica y arquitectónica del asistente virtual inteligente para la web y el ecosistema digital. |
| **06** | `06_sistema_notificaciones_aquora.md` | Diseño del subsistema de notificaciones de Aquora, avisos e incidencias con viabilidad de IA. |
| **07** | `07_roadmap_aquora_sept_nov_2026.md` | Roadmap de Aquora entre septiembre y noviembre de 2026 con hitos de producto e infraestructura. |
| **08** | `08_ia_nativa_odoo19.md` | Documento técnico sobre IA nativa en Odoo 19 y su aplicación dentro del ecosistema del negocio. |
| **09** | `09_conexion_mcp_claude.md` | Documento técnico sobre la conexión de MCP con Claude y su integración al flujo de trabajo. |
| **10** | `10_api_json2_odoo19.md` | Descripción de la API JSON2 y su uso para integración y automatización en Odoo 19. |
| **11** | `11_controllers_odoo16_18_historico.md` | Revisión histórica y técnica de controllers en Odoo 16 y 18. |
| **12** | `12_entorno_docker_local_odoo19.md` | Configuración del entorno Docker local para Odoo 19 en desarrollo y pruebas. |
| **13** | `13_odoo19_implementacion_inventario_tpv.md` | Implementación de inventario y TPV en Odoo 19 con ajustes funcionales y técnicos. |
| **14** | `14_odoo19_configuracion_inicial.md` | Configuración inicial de Odoo 19 para despliegue y arranque del sistema. |
| **15** | `15_odoo19_mapa_nuevas_funcionalidades.md` | Mapa de nuevas funcionalidades y capacidades relevantes de Odoo 19. |
| **16** | `16_odoo19_crm_ventas_novedades.md` | Novedades de CRM y ventas en Odoo 19 para gestión comercial y seguimiento. |
| **17** | `17_odoo19_crm_agendar_reunion.md` | Procedimiento para agendar reuniones desde CRM en Odoo 19. |
| **18** | `18_odoo19_lead_desde_whatsapp.md` | Proceso para la captura y gestión de leads desde WhatsApp en Odoo 19. |
| **19** | `19_odoo19_tpv_novedades.md` | Novedades y mejoras del TPV en Odoo 19 para operación comercial. |
| **20** | `20_odoo19_sitio_web_novedades.md` | Novedades del módulo de sitio web en Odoo 19 para experiencia digital. |
| **21** | `21_odoo_pasarelas_de_pago.md` | Guía técnica sobre pasarelas de pago y su integración con Odoo. |
| **22** | `22_odoo19_gestion_de_eventos.md` | Gestión de eventos y programación dentro del ecosistema Odoo 19. |
| **23** | `23_odoo19_compras_novedades.md` | Novedades y ajustes del módulo de compras en Odoo 19. |
| **24** | `24_odoo19_costos_en_destino.md` | Configuración y uso de costos en destino en el modelo operativo de Odoo 19. |
| **25** | `25_odoo16_17_inventario_basico.md` | Resumen operativo del inventario básico en Odoo 16 y 17. |
| **26** | `26_odoo19_gestion_documental.md` | Procedimientos y configuración para la gestión documental en Odoo 19. |

---

## 🚀 Guía: ¿Cómo agregar un nuevo manual correctamente?

Para mantener la base de conocimiento organizada, auditable y funcional para los agentes de IA (Make.com), cualquier miembro del equipo que necesite incorporar un nuevo manual técnico debe seguir estrictamente estos 4 pasos.

### 1. Naming y Ubicación del Archivo
* **Ruta:** El archivo debe guardarse dentro de la carpeta `arquitectura_y_sistemas/`.
* **Nomenclatura:** Utilice un número secuencial de dos dígitos seguido de un nombre descriptivo en minúsculas separado por guiones bajos (ej. `08_nuevo_procedimiento_respaldo.md`).

### 2. Estructura y Estándar de Contenido (Semantic Markdown)
Todo manual nuevo debe incluir obligatoriamente al inicio los metadatos de contexto y seguir una estructura jerárquica clara:
```markdown
<!-- Path: arquitectura_y_sistemas/08_nuevo_procedimiento_respaldo.md -->

# Título Descriptivo del Documento

**Módulo / Área:** [Área correspondiente]  
**Rol Requerido:** [Roles con acceso]  
**Empresa / Proyecto:** Telcobras S.A.S. / Xpertic  
**Cuándo usar este documento:** [Breve descripción de utilidad]

---

## 1. Sección Principal
...
```

### 3. Reglas de Integridad y Trazabilidad
* **No renombrar archivos existentes** sin coordinación previa.
* **No eliminar manuales** que estén referenciados por enlaces o automatizaciones.
* **Mantener un único origen de verdad** para cada procedimiento o integración.
* **Usar títulos y descripciones homogéneas** para facilitar la búsqueda y la recuperación semántica.

### 4. Validación final antes de publicar
* Revisar que el nombre del archivo y el contenido reflejen exactamente el propósito del procedimiento.
* Confirmar que el documento respeta el formato `NN_nombre_descriptivo.md`.
* Verificar que el documento esté incluido en este índice si corresponde a la categoría actual.

---

> Este índice se actualiza de forma manual para reflejar los documentos existentes y mantener la trazabilidad documental del repositorio.



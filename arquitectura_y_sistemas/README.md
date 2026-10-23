<!-- Path: arquitectura_y_sistemas/README.md -->

# 🏛️ Categoría: Arquitectura y Sistemas - Telcobras S.A.S.

## 📖 ¿Qué es y para qué se usa esta categoría?
La categoría **Arquitectura y Sistemas** concentra toda la documentación técnica de infraestructura cloud, arquitectura de software, integración de sistemas de IA y los manuales operativos/de monitoreo vinculados a la operación de Aquora, Odoo y el ecosistema digital de Telcobras.

**¿Para qué se usa?**
* **Operación y Monitoreo:** Guía a operadores, administradores y equipos técnicos en la lectura de estados, gestión de cuentas, operación de válvulas y soporte de infraestructura crítica.
* **Soporte Técnico y Servidor:** Proporciona runbooks, protocolos de despliegue, solución de errores y gestión de la máquina virtual de producción.
* **Arquitectura y Desarrollo:** Documenta la integración de agentes de IA, patrones desacoplados, esquemas de notificaciones y la planeación tecnológica del producto y del ecosistema digital.

---

## 📑 Índice de Documentos Actualizados

| # | Nombre del Archivo | Descripción General |
| :--- | :--- | :--- |
| **01** | `01_integracion_mcp_github.md` | Documento técnico sobre la integración de MCP con GitHub y la recuperación de documentación desde repositorios. |
| **02** | `02_frescura_conexion_aquora.md` | Documento técnico sobre la frescura y conexión de Aquora, incluyendo diagnóstico y monitoreo de plantas PTAP. |
| **03** | `03_evaluacion_hosting_cloud.md` | Informe técnico de evaluación de proveedores de hosting y plataformas cloud para la solución. |
| **04** | `04_manual_centro_de_gestion_aquora.md` | Manual operativo del Centro de Gestión Aquora, roles, permisos, cuentas, válvulas y mantenimiento de servidor. |
| **05** | `05_propuesta_tecnica_asistente_ia.md` | Propuesta técnica y arquitectónica del asistente virtual inteligente para la landing page y el ecosistema digital. |
| **06** | `06_sistema_notificaciones_aquora.md` | Documento técnico del subsistema de notificaciones Aquora, avisos, incidencias y viabilidad de IA por niveles. |
| **07** | `07_roadmap_aquora_sept_nov_2026.md` | Roadmap de Aquora para septiembre-noviembre de 2026 con fases de producto, funciones internas e infraestructura. |
| **08** | `08_ia_nativa_odoo19.md` | Documento técnico sobre IA nativa en Odoo 19 y su aplicación en el ecosistema de negocio. |
| **09** | `09_conexion_mcp_claude.md` | Documento técnico sobre la conexión de MCP con Claude y su integración con el flujo de trabajo. |
| **10** | `10_api_json2_odoo19.md` | Documento técnico sobre la API JSON2 y su uso en Odoo 19. |
| **11** | `11_controllers_odoo16_18_historico.md` | Documento técnico histórico sobre controllers en Odoo 16 y 18. |
| **12** | `12_entorno_docker_local_odoo19.md` | Documento técnico sobre el entorno Docker local para Odoo 19. |
| **13** | `13_odoo19_implementacion_inventario_tpv.md` | Documento técnico sobre la implementación de inventario y TPV en Odoo 19. |
| **14** | `14_odoo19_configuracion_inicial.md` | Documento técnico sobre la configuración inicial de Odoo 19. |
| **15** | `15_odoo19_mapa_nuevas_funcionalidades.md` | Documento técnico con el mapa de nuevas funcionalidades de Odoo 19. |
| **16** | `16_odoo19_crm_ventas_novedades.md` | Documento técnico sobre CRM y novedades de ventas en Odoo 19. |
| **17** | `17_odoo19_crm_agendar_reunion.md` | Documento técnico para agendar reuniones desde CRM en Odoo 19. |
| **18** | `18_odoo19_lead_desde_whatsapp.md` | Documento técnico sobre la gestión de leads desde WhatsApp en Odoo 19. |
| **19** | `19_odoo19_tpv_novedades.md` | Documento técnico sobre novedades del TPV en Odoo 19. |
| **20** | `20_odoo19_sitio_web_novedades.md` | Documento técnico sobre novedades del sitio web en Odoo 19. |
| **21** | `21_odoo_pasarelas_de_pago.md` | Documento técnico sobre pasarelas de pago en Odoo. |
| **22** | `22_odoo19_gestion_de_eventos.md` | Documento técnico sobre la gestión de eventos en Odoo 19. |
| **23** | `23_odoo19_compras_novedades.md` | Documento técnico sobre novedades de compras en Odoo 19. |
| **24** | `24_odoo19_costos_en_destino.md` | Documento técnico sobre costos en destino en Odoo 19. |
| **25** | `25_odoo16_17_inventario_basico.md` | Documento técnico sobre inventario básico en Odoo 16 y 17. |
| **26** | `26_odoo19_gestion_documental.md` | Documento técnico sobre gestión documental en Odoo 19. |

---

## 🚀 Guía: ¿Cómo agregar un nuevo manual correctamente?

Para mantener la base de conocimiento organizada, auditable y funcional para los agentes de IA (Make.com), cualquier miembro del equipo que necesite incorporar un nuevo manual técnico debe seguir estos cuatro pasos y respetar la nomenclatura establecida.

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


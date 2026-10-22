<!-- Path: arquitectura_y_sistemas/README.md -->

# 🏛️ Categoría: Arquitectura y Sistemas - Telcobras S.A.S.

## 📖 ¿Qué es y para qué se usa esta categoría?
La categoría **Arquitectura y Sistemas** concentra toda la documentación técnica de infraestructura cloud, arquitectura de software, integración de sistemas de IA y los manuales operativos/de monitoreo industrial de la plataforma **Aquora** (monitoreo de PTAP). 

**¿Para qué se usa?**
* **Operación y Monitoreo:** Guía a operadores, jefes de planta y administradores en la lectura de estados (`LIVE`, `STABLE`, `FROZEN`), gestión de cuentas y operación de válvulas.
* **Soporte Técnico y Servidor:** Proporciona runbooks, protocolos de despliegue mediante PM2/Git, solución de errores y gestión de la máquina virtual de producción.
* **Arquitectura y Desarrollo:** Documenta la integración de agentes de IA, patrones desacoplados (React - Make.com), esquemas de notificaciones y la planeación tecnológica (Roadmap).

---

## 📑 Índice de Documentos Actualizados

| # | Nombre del Archivo | Descripción General |
| :--- | :--- | :--- |
| **01** | `01_integracion_mcp_github.md` | Conexión de agentes en Make.com a repositorios de GitHub mediante GitMCP y URLs Raw para la recuperación de documentación. |
| **02** | `02_frescura_conexion_aquora.md` | Guía técnica de frescura y conexión en el monitoreo de plantas PTAP (capas de estado, máquina del puente OPC UA y diagnóstico). |
| **03** | `03_evaluacion_hosting_cloud.md` | Informe comparativo de proveedores de hosting y plataformas cloud (Latinoamérica Hosting, Hostinger, DigitalOcean, AWS, Azure y Google Cloud). |
| **04** | `04_manual_centro_de_gestion_aquora.md` | Manual operativo del Centro de Gestión Aquora, roles, permisos, gestión de cuentas, operación de válvulas y mantenimiento en servidor. |
| **05** | `05_propuesta_tecnica_asistente_ia.md` | Propuesta técnica y arquitectónica del asistente virtual inteligente para la landing page (Fase 1, React - Make.com). |
| **06** | `06_sistema_notificaciones_aquora.md` | Informe técnico del subsistema de notificaciones Aquora, catálogo de avisos, incidencias históricas y viabilidad de IA por niveles. |
| **07** | `07_roadmap_aquora_sept_nov_2026.md` | Plan de actualizaciones a 10 semanas (Sep - Nov 2026), fases de producto visible, funciones de fondo e infraestructura. |

---

## 🚀 Guía: ¿Cómo agregar un nuevo manual correctamente?

Para mantener la base de conocimiento organizada, auditable y funcional para los agentes de IA (Make.com), cualquier miembro del equipo que necesite incorporar un nuevo manual técnico debe seguir estrictamente estos 4 pasos:

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

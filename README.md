# 🏢 Base de Conocimiento y Arquitectura Agente IA - Telcobras S.A.S.

> **Creado por:** Cristian Bedoya 👨‍💻  
> **Propósito:** Repositorio oficial centralizado de Procedimientos Operativos Estándar (SOP) en Markdown Semántico y documentación técnica para la integración del Agente de IA de Operaciones y la base documental corporativa de Telcobras S.A.S.

---

## 📋 ¿Qué es este repositorio y para qué sirve?

Este repositorio funciona como el **cerebro documental y la fuente única de verdad** para el Agente de Inteligencia Artificial de Telcobras S.A.S. Contiene guías modulares estructuradas que parametrizan procedimientos, políticas, arquitectura técnica y estándares de operación para cada área funcional de la empresa.

Sirve como base documental para:
* consulta operativa por parte de usuarios y equipos.
* soporte técnico para soluciones Odoo, IA, integraciones y automatizaciones.
* mantenimiento de la trazabilidad de procedimientos y la estandarización de contenidos.
* respuesta inteligente del agente mediante lectura directa de archivos Markdown.

---

## 📁 Estructura del Repositorio

La documentación está organizada por áreas operativas y técnicas en las siguientes carpetas:

* **`arquitectura_y_sistemas/`** ⚙️ — Standars técnicos, infraestructura, integración MCP, conectividad con GitHub, IA y arquitectura del ecosistema digital.
* **`comercial/`** 💼 — Portafolio de servicios, propuestas comerciales y documentación de oferta de valor corporativa.
* **`compras_y_pagos/`** 💳 — Flujos de aprobación, desembolsos y control de pagos.
* **`finanzas_y_contabilidad/`** 📊 — Facturación electrónica, notas, caja menor, nómina, tributaria y gestión documental contable.
* **`inventario_y_logistica/`** 📦 — Gestión de inventario, nomenclatura, activos y control de flota.
* **`rrhh_y_proyectos/`** 👥 — Evaluaciones de desempeño, estructura del equipo, marca y proyectos.

---

## 📚 Índice General Consolidado

| Área | Carpeta | Documentos principales | Estado |
| :--- | :--- | :--- | :--- |
| Arquitectura y Sistemas | `arquitectura_y_sistemas/` | 26 manuales técnicos | Actualizado |
| Comercial | `comercial/` | 2 documentos | Actualizado |
| Compras y Pagos | `compras_y_pagos/` | 1 documento | Actualizado |
| Finanzas y Contabilidad | `finanzas_y_contabilidad/` | 19 documentos | Actualizado |
| Inventario y Logística | `inventario_y_logistica/` | 2 documentos | Actualizado |
| RRHH y Proyectos | `rrhh_y_proyectos/` | 6 documentos | Actualizado |

### Documentos por categoría

#### 1) Arquitectura y Sistemas (`arquitectura_y_sistemas/`)
- `01_integracion_mcp_github.md`
- `02_frescura_conexion_aquora.md`
- `03_evaluacion_hosting_cloud.md`
- `04_manual_centro_de_gestion_aquora.md`
- `05_propuesta_tecnica_asistente_ia.md`
- `06_sistema_notificaciones_aquora.md`
- `07_roadmap_aquora_sept_nov_2026.md`
- `08_ia_nativa_odoo19.md`
- `09_conexion_mcp_claude.md`
- `10_api_json2_odoo19.md`
- `11_controllers_odoo16_18_historico.md`
- `12_entorno_docker_local_odoo19.md`
- `13_odoo19_implementacion_inventario_tpv.md`
- `14_odoo19_configuracion_inicial.md`
- `15_odoo19_mapa_nuevas_funcionalidades.md`
- `16_odoo19_crm_ventas_novedades.md`
- `17_odoo19_crm_agendar_reunion.md`
- `18_odoo19_lead_desde_whatsapp.md`
- `19_odoo19_tpv_novedades.md`
- `20_odoo19_sitio_web_novedades.md`
- `21_odoo_pasarelas_de_pago.md`
- `22_odoo19_gestion_de_eventos.md`
- `23_odoo19_compras_novedades.md`
- `24_odoo19_costos_en_destino.md`
- `25_odoo16_17_inventario_basico.md`
- `26_odoo19_gestion_documental.md`

#### 2) Comercial (`comercial/`)
- `01_portafolio_servicios.md`
- `02_odoo19_2_novedades_alquiler.md`

#### 3) Compras y Pagos (`compras_y_pagos/`)
- `01_aprobacion_pagos.md`

#### 4) Finanzas y Contabilidad (`finanzas_y_contabilidad/`)
- `01_facturacion_electronica.md`
- `02_documento_soporte_electronico.md`
- `03_caja_menor_xpertic.md`
- `04_agente_ia_causacion.md`
- `05_notas_credito_debito_dian.md`
- `06_odoo16_17_resumen_financiero.md`
- `07_odoo19_nomina_overview.md`
- `08_manual_factura_electronica_venta.md`
- `09_manual_nota_credito.md`
- `10_manual_nota_debito.md`
- `11_manual_contenedor_electronico.md`
- `12_manual_registro_eventos_radian.md`
- `13_manual_transmision_dian.md`
- `14_manual_reglas_validacion_rechazos.md`
- `15_manual_mecanismos_control.md`
- `16_manual_contingencias_tecnologicas.md`
- `17_manual_entrega_recepcion.md`
- `18_manual_gestion_catalogos.md`
- `19_manual_extensiones_sectoriales.md`

#### 5) Inventario y Logística (`inventario_y_logistica/`)
- `01_control_flota.md`
- `02_creacion_productos_nomenclatura.md`

#### 6) RRHH y Proyectos (`rrhh_y_proyectos/`)
- `01_evaluaciones_desempeno.md`
- `02_odoo_equipo_desarrollo.md`
- `03_creacion_empleados_odoo19.md`
- `04_manual_de_marca.md`
- `05_configuracion_evaluaciones_odoo19.md`
- `06_odoo19_recursos_humanos_novedades.md`

---

## ⚙️ ¿Cómo funciona y cómo se usa?

1. **Lectura en Tiempo Real (*Raw URLs*):** El agente implementa el protocolo MCP y utiliza la herramienta `fetch_generic_url_content` en Make.com para leer directamente el contenido en texto plano de cada archivo Markdown desde GitHub.
2. **Actualización Inmediata:** Cualquier cambio aprobado en los archivos Markdown se refleja automáticamente en las respuestas del agente en producción, sin necesidad de reentrenar modelos.
3. **Citación Obligatoria:** Todas las respuestas del agente incluyen estrictamente la fuente consultada al final, por ejemplo: `*Fuente consultada: 03_caja_menor_xpertic.md*`.
4. **Trazabilidad documental:** Cada carpeta representa una categoría operativa o técnica y su índice refleja el estado real de los documentos disponibles en el repositorio.

---

## 🛑 REGLA ESTRICTAMENTE OBLIGATORIA: PROHIBIDO MODIFICAR SIN APROBACIÓN

> **¡Atención equipo de desarrollo y colaboradores!**  
> Ningún archivo, ruta, nombre de manual o contenido dentro de este repositorio debe ser modificado, renombrado, reestructurado o eliminado **sin la autorización formal y aprobación previa del responsable del repositorio**.
>
> *Cualquier cambio no autorizado altera los enlaces oficiales (*Raw URLs*) configurados en el System Prompt de Make, lo que provocará fallos inmediatos en las respuestas del asistente virtual en producción.*

---

## 📌 Estado Actual del Repositorio

- Índices actualizados por categoría: ✅
- Documentos alineados con archivos reales: ✅
- Numeración corregida en todas las secciones: ✅
- README principal con consolidado general: ✅

---

© 2026 Telcobras S.A.S. — Todos los derechos reservados.

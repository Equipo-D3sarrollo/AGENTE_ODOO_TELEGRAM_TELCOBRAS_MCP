<!-- Path: arquitectura_y_sistemas/01_integracion_mcp_github.md -->

# Arquitectura de Integración MCP & GitHub para Agentes IA

**Módulo / Área:** Make.com / Infraestructura Técnica / MCP[cite: 97]
**Rol Requerido:** Equipo de Desarrollo[cite: 97]
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 97]
**Presentado por:** Cristian Bedoya[cite: 97]
**Cuándo usar este documento:** Utiliza este manual cuando necesites comprender, replicar o mantener la arquitectura técnica que conecta el Agente de IA de Soporte Técnico en Make.com con los manuales operativos alojados en repositorios Git mediante el protocolo MCP[cite: 97].

---

## 1. Resumen Ejecutivo
Este documento formaliza la arquitectura técnica diseñada para conectar el Agente de IA de Soporte Técnico en Make.com con los manuales operativos alojados en repositorios Git[cite: 97]. Se evaluaron diferentes alternativas de integración hasta consolidar un modelo híbrido ultraeficiente basado en el protocolo **MCP (Model Context Protocol)** y lectura en formato crudo (*Raw*)[cite: 97].

---

## 2. Evaluación de Enfoques Técnicos
Durante la fase de validación técnica se descartaron enfoques tradicionales debido a limitaciones estructurales[cite: 97]:

| Enfoque / Servidor | Comportamiento Observado | Estado |
| :--- | :--- | :--- |
| **GitMCP General** (`gitmcp.io/docs`)[cite: 97] | Exige un archivo `README.md` global; falla al intentar consultar archivos individuales o rutas relativas específicas[cite: 97]. | **Descartado**[cite: 97] |
| **GitHub Oficial MCP** (`api.githubcopilot.com`)[cite: 97] | Orientado a entornos IDE (VS Code). Devuelve confirmaciones con códigos SHA pero oculta el cuerpo de texto plano al LLM[cite: 97]. | **Descartado**[cite: 97] |
| **GitMCP + URLs Raw** (`raw.githubusercontent.com`)[cite: 97] | Utiliza el cliente MCP para lectura genérica web apuntando directamente al contenido crudo del repositorio[cite: 97]. | **Estándar Aprobado**[cite: 97] |

---

## 3. Principio de Funcionamiento
En lugar de depender de motores de búsqueda intermedios propensos a fallos de indexación, el Agente utiliza la herramienta `fetch_generic_url_content` para consultar de manera directa y sincrónica el texto plano de cada manual Markdown, garantizando un **cero por ciento de alucinación técnica**[cite: 97, 98].

* **Seguridad e Integridad:** El sistema implementa políticas estrictas de degradación: si la URL no responde o el contenido no contiene la política requerida, el bot se abstiene de inventar datos.
* **Mantenibilidad:** Cualquier actualización en los procedimientos de Odoo 19 realizada en el repositorio se refleja de forma inmediata sin necesidad de reentrenar modelos.

---

## 4. Guía de Replicación para Entornos de Producción
Para replicar esta arquitectura en futuras bases de conocimiento corporativas dentro de Telcobras, el equipo de desarrollo debe seguir esta directriz de implementación en Make.com[cite: 98]:

### Configuración del Escenario y Endpoints
* **Paso 1 (Capa de Conexión):** Configurar el módulo *MCP Client* utilizando el servidor verificado GitMCP (`https://gitmcp.io/docs`)[cite: 98].
* **Paso 2 (Mapeo de Enlaces):** Exponer los archivos `.md` de la base de conocimiento utilizando la estructura de URLs estables de GitHub Raw[cite: 98]:
  `https://raw.githubusercontent.com/<org>/<repo>/main/<archivo>.md`[cite: 98]

---

## 5. Estructura Oficial del System Prompt
El Agente de IA debe ser programado bajo el siguiente estándar de enrutamiento para asegurar la selección correcta de la herramienta[cite: 98]:

```markdown
# SYSTEM PROMPT SOPORTE TÉCNICO ODOO 19
ROL: Asistente oficial de Telcobras. Español técnico. 
MAPA DE URLS RAW: 
- Clientes: [https://raw.githubusercontent.com/.../01_clientes.md](https://raw.githubusercontent.com/.../01_clientes.md) 
- Inventario: [https://raw.githubusercontent.com/.../02_inventario.md](https://raw.githubusercontent.com/.../02_inventario.md)
- Descuentos: [https://raw.githubusercontent.com/.../03_politicas.md](https://raw.githubusercontent.com/.../03_politicas.md) 
PROTOCOLO: Invocar obligatoriamente 'fetch_generic_url_content' pasando la URL exacta. Incluir al final: [Fuente consultada].

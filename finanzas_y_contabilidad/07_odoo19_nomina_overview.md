<!-- Path: finanzas_y_contabilidad/07_odoo19_nomina_overview.md -->

# 💵 Manual Operativo 07: Gestión Integral y Configuración del Módulo de Nómina en Odoo 19

**Módulo / Área:** Finanzas y Contabilidad / Nómina y Capital Humano  
**Rol Requerido:** Director Financiero, Contador, Analista de Nómina, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para comprender la arquitectura, configuración y el ciclo operativo del módulo de Nómina en Odoo 19, abarcando desde la gestión de fichas de empleados, entradas de trabajo e historial de contratos hasta el diseño de estructuras salariales, reglas y la ejecución de lotes de pago, adaptando estos flujos al marco legal de Colombia.

---

## 1. Arquitectura y Ciclo Operativo del Módulo

El módulo de Nómina automatiza el cálculo y procesamiento de las retribuciones salariales, integrándose directamente con las aplicaciones de **Empleados, Asistencias, Ausencias (Tiempo personal) y Contabilidad** (para la contabilización automática del gasto).

**Flujo General:**
1. **Ficha y Contrato:** Expediente unificado del colaborador con su historial de contrataciones y condiciones salariales.
2. **Entradas de Trabajo (*Work Entries*):** Consolidación de asistencias, horas laboradas e incapacidades o permisos aprobados.
3. **Estructuras y Reglas:** Aplicación de fórmulas matemáticas, devengos, deducciones legales y aportes de ley.
4. **Lotes de Pago y Recibos:** Generación de volantes de pago individuales o masivos, validación y registro contable.

---

## 2. Fichas de Empleados y Control de Contratos (Odoo 19)

### 2.1. Estructura del Expediente
* **Pestaña de Trabajo:** Departamentos (con soporte de organigrama jerárquico), cargos, jefes directos y modalidades de trabajo híbrido/presencial.
* **Información Personal:** Documento de identidad nacional (cédula), datos de contacto, cuentas bancarias de nómina, cargas familiares y datos de ciudadanía.
* **Historial de Contratos:** En Odoo 19 se gestionan las condiciones contractuales mediante un **Historial de versiones** integrado directamente en la ficha del empleado, permitiendo manejar tipos de salario (fijo o por hora), tipos de contrato y vinculaciones periódicas con fechas de inicio y fin claras.

---

## 3. Entradas de Trabajo y Horarios

* **Tipos de Entrada:** Clasificación de los eventos que afectan la liquidación (jornada ordinaria, horas extra, licencias remuneradas, incapacidades).
* **Entradas de Trabajo:** Representan la conversión efectiva de las asistencias y ausencias validadas en datos contables de nómina. **Ningún recibo de nómina puede calcularse sin entradas de trabajo válidas ni contratos activos en el periodo.**
* **Validación de Conflictos:** El sistema detecta superposiciones o ausencias no cubiertas por contratos vigentes, permitiendo visualizaciones en formato de lista, Gantt o tabla dinámica.

---

## 4. Estructuras Salariales y Reglas

* **Tipos de Estructura:** Plantillas marco que agrupan las condiciones de pago (ej. régimen general, empleados fijos, trabajadores por hora).
* **Reglas Salariales:** Bloques lógicos o fórmulas matemáticas que determinan cada concepto del recibo. Sus componentes principales son:
  * **Secuencia:** Orden estricto de ejecución en el cálculo.
  * **Condiciones:** Restricciones de aplicación (ej. reglas que solo se activan si existe una entrada manual de embargo o libranza en el periodo).
  * **Cálculo:** Asignación de montos fijos, porcentajes o sentencias personalizadas en código Python.
* **Parámetros de Reglas (*Rule Parameters*):** Valores fijos reutilizables (ej. topes legales, salarios mínimos, tasas de retención o auxilios) que cambian periódicamente por ley, evitando modificar las fórmulas de las reglas y permitiendo configurar fechas de vigencia.

---

## 5. Recibos y Lotes de Nómina (*Pay Runs*)

1. **Creación del Lote:** Desde el módulo de Nómina, se define el periodo (mensual o quincenal), la estructura aplicable y se seleccionan los empleados.
2. **Cálculo y Validación:** Odoo procesa las entradas de trabajo y aplica las reglas salariales generando los recibos en estado borrador.
3. **Revisión de Alertas:** El sistema advierte sobre incidencias (ej. cuentas bancarias faltantes o ausencias diferidas pendientes).
4. **Confirmación y Pago:** Una vez validados los recibos, se confirma el lote y se procede a marcar los pagos como ejecutados, generando el asiento contable correspondiente.
5. **Ajustes Salariales:** Permite registrar deducciones recurrentes (ej. embargos judiciales o libranzas con montos fijos o negativos) que se aplican automáticamente en los siguientes ciclos de cálculo.

---

## 6. Consideraciones y Adaptación Crítica para Colombia

Dado que las demostraciones internacionales base operan bajo legislaciones extranjeras (como EE. UU. o India), Telcobras debe configurar el módulo de acuerdo con la normatividad laboral y fiscal colombiana:

| Componente Odoo | Homologación / Requerimiento en Colombia |
| :--- | :--- |
| **Salario Básico** | Liquidado proporcionalmente a los días efectivamente laborados según las entradas de trabajo. |
| **Auxilio de Transporte** | Configurado como regla con condición de tope salarial (vinculado a un parámetro de ley anual). |
| **Horas Extra y Recargos** | Mapeadas desde el módulo de *Asistencias* aplicando porcentajes legales vigentes (extra diurna, nocturna, dominical y festiva). |
| **Deducciones de Ley** | Aportes obligatorios del empleado a Salud y Pensión (porcentajes fijos), Fondo de Solidaridad Pensional y Retención en la Fuente (mediante tabla UVT en parámetros). |
| **Aportes del Empleador** | Configurar las reglas de costos patronales (salud, pensión, ARL y parafiscales) marcando la opción *"Suma al costo del empleador"*. |
| **Nómina Electrónica (DIAN)** | **Validación Crítica:** Odoo por defecto no incluye la localización nativa de nómina electrónica DIAN para Colombia. Es indispensable confirmar si la instancia cuenta con un desarrollo a medida o un conector certificado, ya que en Odoo Online no está permitida la instalación de módulos externos de terceros. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Módulo de Nómina en Odoo 19 (Gestión de empleados, contratos, entradas de trabajo, estructuras salariales, reglas y lotes de pago, con consideraciones específicas para la normatividad laboral de Colombia): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/07_odoo19_nomina_overview.md

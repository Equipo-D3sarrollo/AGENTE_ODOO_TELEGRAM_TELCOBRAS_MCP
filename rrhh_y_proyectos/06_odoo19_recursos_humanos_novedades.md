````markdown
<!-- Path: rrhh_y_proyectos/06_odoo19_recursos_humanos_novedades.md -->

# 👥 Manual Operativo 26: Novedades y Gestión Integral de Recursos Humanos en Odoo 19

**Módulo / Área:** RR. HH. y Proyectos / Gestión del Talento, Asistencias y Nómina  
**Rol Requerido:** Director de Recursos Humanos, Analistas de Nómina, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para conocer y aplicar las capacidades introducidas en el ecosistema de Recursos Humanos de Odoo 19, abarcando desde la agilización de procesos en reclutamiento, la ficha unificada de empleados, el control de asistencias con reglas de horas extra y ausencias, hasta la ejecución de evaluaciones de desempeño y lotes de nómina.

---

## 1. Gestión de Reclutamiento y Bolsa de Talento

El módulo de Reclutamiento optimiza el seguimiento de candidatos desde la vacante inicial hasta su contratación final:
* **Vista General y Requisitos:** Paneles en tiempo real con métricas de postulaciones y botones para registrar los perfiles requeridos por puesto.
* **Bolsa de Talento:** Permite almacenar y clasificar en reservas a los candidatos que no fueron seleccionados en el proceso inmediato pero cuyo perfil es valioso para futuras vacantes, agilizando búsquedas posteriores mediante etiquetas.
* **Transición a Contrato:** Con un solo clic desde la postulación en etapa de propuesta, se genera el contrato para su firma electrónica y se da de alta automáticamente al nuevo colaborador en el expediente de empleados.

---

## 2. Ficha Unificada de Empleados y Versiones de Contrato

* **Expediente Centralizado (Odoo 19):** Se unifica la gestión del colaborador eliminando módulos separados para contratos. Toda la información personal, puestos, departamentos, gerentes (organigrama), certificaciones y datos salariales reside en una sola ficha.
* **Control de Versiones de Contrato:** Permite registrar modificaciones contractuales (ej. aumentos salariales o cambios de condiciones) mediante nuevas versiones ligadas a una fecha de inicio, facilitando la navegación histórica con flechas de control.
* **Certificaciones:** El módulo de Aprendizaje permite administrar masivamente capacitaciones y certificaciones de personal (clave para el control de licencias o trabajos especializados de campo en Telcobras).

---

## 3. Control de Asistencias y Motor de Reglas de Horas Extra

* **Modo Kiosco:** Los colaboradores registran sus entradas y salidas de forma autónoma mediante la introducción de un PIN de seguridad.
* **Reglas Automatizadas de Horas Extra:** Permite configurar parámetros personalizados de compensación y tolerancias (ej. cálculo de recargos extraordinarios, jornadas dobles o nocturnas superados los márgenes de tolerancia de la empresa).
* **Adaptación a Colombia:** Es indispensable configurar estas reglas en concordancia con la legislación laboral colombiana (recargos nocturnos, horas extra diurnas/nocturnas, dominicales y festivos) en lugar de normativas extranjeras.

---

## 4. Gestión de Tiempo Personal y Ausencias

* **Tipos de Ausencia:** Administración estructurada de vacaciones, días compensatorios, permisos y días festivos oficiales.
* **Festivos Corporativos:** Carga de los días festivos nacionales (incluyendo traslados de ley para Colombia), los cuales se reflejan automáticamente en el calendario de todo el personal.
* **Fraccionamiento:** Solicitud de ausencias por medio día (mañana o tarde) o por horas específicas, impactando directamente las entradas de trabajo (*work entries*).

---

## 5. Lotes de Nómina y Entradas de Trabajo

* **Tablero Financiero y de Advertencias:** Resumen ejecutivo de costos de personal (mensual/anual), alertas de recibos mediante códigos de color y control de periodos de pago.
* **Aprobación Cruzada:** Los permisos o ausencias sin goce de sueldo solicitados por el personal pueden aprobarse directamente desde el entorno de nómina sin cambiar de aplicación.
* **Lotes de Nómina (Pay Runs):** Consolidación de todos los recibos del periodo, permitiendo procesar lotes masivos, verificar entradas de trabajo (asistencias, horas extra y permisos) y ejecutar el ciclo de pago.
* **Consideración de Nómina Electrónica (DIAN):** Validar la integración del operador de pago y los requerimientos de nómina electrónica en Colombia frente a funciones nativas de timbrado internacional.

---

## 6. Evaluaciones de Desempeño

* **Ciclos de Evaluación:** Creación de evaluaciones dirigidas a empleados o gerencias basadas en plantillas personalizables.
* **Autoevaluación y Objetivos:** Asignación de metas anuales, registro de notas privadas de retroalimentación por parte de los supervisores y publicación de autoevaluaciones por parte del colaborador.
* **Informes de Habilidades:** Reportes analíticos por departamento para medir la evolución de competencias y desempeño a lo largo del tiempo.

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Novedades y Gestión de Recursos Humanos en Odoo 19 (Reclutamiento, ficha unificada, versiones de contrato, reglas de horas extra, ausencias, lotes de nómina y evaluaciones de desempeño): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/rrhh_y_proyectos/06_odoo19_recursos_humanos_novedades.md

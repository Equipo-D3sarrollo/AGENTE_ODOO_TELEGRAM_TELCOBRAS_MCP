<!-- Path: rrhh_y_proyectos/05_configuracion_evaluaciones_odoo19.md -->

# Manual de configuración del módulo Evaluaciones en Odoo 19 Online

**Módulo / Área:** Evaluaciones / RRHH y Proyectos[cite: 78]  
**Rol Requerido:** Talento Humano y Administradores[cite: 91]  
**Empresa:** Telcobras S.A.S.[cite: 78]  
**Cuándo usar este documento:** Utilice este manual para configurar el módulo Evaluaciones de Odoo 19 Online (suscripción Custom), estableciendo la evaluación mensual de desempeño con seguimiento semanal por área[cite: 78].

---

## 1. Objetivo y Modelo
El sistema permite que cada área de Telcobras evalúe a su personal mensualmente con base en datos y mantenga conversaciones cortas de seguimiento semanal[cite: 78]. El piloto inicial se ejecuta con el equipo de Desarrollo antes de replicarlo en las demás áreas[cite: 78].

* **Evaluación mensual:** Odoo la genera automáticamente el segundo día hábil del mes empleando la plantilla del área[cite: 78].
* **Seguimiento semanal:** Cada lunes, Odoo crea al jefe una actividad por persona con vencimiento el viernes[cite: 78].
* **Objetivos del mes:** Máximo 3 por persona, con indicador de éxito y progreso de 0% a 100%[cite: 78].
* **Habilidades:** Niveles por persona que ascienden únicamente mediante evidencia[cite: 78].
* **Calificación:** Rúbrica ponderada de 1 a 5 por áreas alimentada con datos directos de Odoo[cite: 78].
* **Indicadores:** Hojas de cálculo de Odoo filtradas por mes y publicadas en Tableros[cite: 78].

### 1.1 Requisitos de personal y datos previos
* **Empleados y practicantes activos:** Creados desde el menú *Empleados > Nuevo*[cite: 79].
* **Retirados:** Archivados obligatoriamente para evitar que Odoo les genere evaluaciones[cite: 79].
* **Departamento y puesto:** Asignados en la ficha, pestaña *Trabajo*[cite: 79].
* **Gerente (jefe directo):** Definido en la ficha, pestaña *Trabajo*[cite: 79].
* **Tipo de empleado:** Configurado como Empleado o Practicante (con detalle de SENA o convenio universitario) en la pestaña *Ajustes*[cite: 79].
* **Usuario personal de Odoo:** Vinculado a la ficha para autoevaluaciones, recepción de actividades y registro de horas[cite: 79].

---

## 2. Preparar el Entorno y Aplicaciones
* **Paso 1 - Duplicar base:** Crear una copia de pruebas (ej. `telcobras-pruebas-evaluaciones`) desde *Mi cuenta > Mis bases de datos*[cite: 81]. Las acciones planificadas y correos se desactivan en la copia; las acciones se prueban manualmente[cite: 81].
* **Paso 2 - Modo desarrollador:** Activar desde *Ajustes > Herramientas para desarrolladores*[cite: 81].
* **Paso 3 - Aplicaciones:** Instalar *Evaluaciones*, *Encuestas*, *Documentos*, *Tableros*, y para medición de tiempo/tareas, *Proyecto* y *Hojas de horas*[cite: 81].

---

## 3. Ajustes y Plan Mensual
* **Paso 4 - Configuración del módulo:** En *Evaluaciones > Configuración > Ajustes*, configurar los planes de evaluación (primera evaluación, segunda, y siguientes a 1 mes)[cite: 81]. 
* *Nota sobre el error de fecha:* Para evitar el error *"La siguiente fecha de evaluación no puede ser una fecha que ya pasó"*, se debe asegurar archivar retirados y actualizar la próxima fecha de evaluación antes de guardar el plan[cite: 81, 82].

---

## 4. Escala, Departamentos, Habilidades y Etiquetas
* **Paso 5 - Escala de evaluación (1 a 5):** Configurar en *Evaluaciones > Configuración > Escala de evaluación*[cite: 82]:
  * 5 - Excepcional ($\ge$100%)[cite: 82]
  * 4 - Supera (80-99%)[cite: 82]
  * 3 - Cumple con revisión (70-79%)[cite: 82]
  * 2 - Necesita mejorar (60-69%)[cite: 82]
  * 1 - No cumple ($<$60%)[cite: 82]
* **Paso 6 - Departamentos:** Confirmar la existencia de los departamentos con sus respectivos gerentes en *Empleados > Configuración > Departamentos*[cite: 82].
* **Paso 7 - Tipos de habilidad:** Configurar los tipos de habilidad y sus niveles (*Principiante, Básico, Intermedio, Avanzado, Experto* con progresos de 20 a 100) en *Evaluaciones > Configuración > Habilidades*[cite: 82, 83].
* **Paso 8 - Habilidades por persona:** Asignar en la pestaña *Currículum* de la ficha de empleado[cite: 83].
* **Paso 9 - Etiquetas de objetivos:** Crear los frentes de trabajo en *Evaluaciones > Configuración > Etiquetas*[cite: 84].

---

## 5. Plantillas de Evaluación
* **Paso 10 - Plantilla del área:** Crear en *Evaluaciones > Configuración > Plantillas de evaluación > Nuevo*, asignando el departamento correspondiente y redactando las preguntas de autoevaluación del empleado y datos/calificación del gerente[cite: 84, 85].
* **Paso 11 - Plantilla Gerencia y jefes:** Diseñada para roles de coordinación o PMO sin tareas de horas medidas[cite: 85, 86].

---

## 6. Rúbrica de Calificación
La rúbrica convierte los datos operativos mensuales en una nota de 1 a 5 basada en porcentajes ponderados que suman 100%[cite: 86, 87].
* **Rúbrica de Desarrollo (Ejemplo):**
  * A. Productividad: 30%[cite: 87]
  * B. Cumplimiento del plan: 20%[cite: 87]
  * C. Calidad: 20%[cite: 87]
  * D. Objetivos del mes: 20%[cite: 87]
  * E. Registro y colaboración: 10%[cite: 87]
* Nota final calculada mediante la suma ponderada y redondeo al entero más cercano[cite: 86, 87].

---

## 7. Objetivos del Mes y Seguimiento Semanal
* **Paso 12 - Objetivos:** Máximo 3 por persona con indicador de éxito y fecha límite en el último día hábil del mes (*Evaluaciones > Objetivos > Nuevo*)[cite: 88, 89].
* **Paso 13 - Acción planificada (Seguimiento semanal):** Configurar una acción planificada de tipo *Ejecutar código* en *Ajustes Técnico > Automatización > Acciones planificadas* que genere actividades semanales para los jefes de área[cite: 89, 90].

---

## 8. Permisos, Indicadores y Puesta en Marcha
* **Paso 14 - Roles:** Asignar *Evaluaciones: Administrador* exclusivamente a Talento Humano y *Evaluaciones: Oficial* a los jefes de área[cite: 91, 92].
* **Pasos 15 a 22 - Indicadores y Hojas de Cálculo:** Integración de datos desde *Hojas de horas*, *Proyecto*, *Errores* e *Incidencias* hacia hojas de cálculo de Odoo con filtros de mes y publicación en la app *Tableros*[cite: 92, 94, 95, 96].
* **Pasos 23 a 31 - Encendido y Piloto:** Configuración de la próxima fecha de evaluación unificada, activación de la acción semanal y automatización, ejecución de piloto con el mes anterior y calibración de la rúbrica[cite: 97, 98].

<!-- Path: rrhh_y_proyectos/01_evaluaciones_desempeno.md -->

# Evaluaciones de Desempeño en Odoo (Piloto: Equipo de Desarrollo)

**Módulo Odoo:** Evaluaciones / Empleados[cite: 43]
**Rol Requerido:** Responsable de la implementación, Jefe de Desarrollo y Talento Humano[cite: 43]
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 43]
**Elaborado por:** Joshua Lores Florez (Versión 1.0)[cite: 43]
**Cuándo usar este documento:** Utiliza esta guía para configurar y ejecutar el módulo de Evaluaciones en Odoo 19, implementando inicialmente un piloto con el equipo de Desarrollo y conectando los datos del Cronograma de Gestión con las valoraciones periódicas y rúbricas de desempeño[cite: 43, 44].

---

## 1. Principio Metodológico y Ruta de Ejecución
* **La regla de fondo:** El *Cronograma de gestión* mide y Odoo registra[cite: 44]. Odoo no calcula valor ganado, ciclos de error ni metas diarias (dichos cálculos se realizan en el Excel de gestión)[cite: 44]. Mensualmente, el jefe extrae los valores del Excel, los consigna en la evaluación de Odoo y califica bajo reglas fijas[cite: 44]. Odoo almacena el registro histórico, las metas, la retroalimentación semanal y las consecuencias firmadas[cite: 44].

### Fases de Implementación
1. **Fase 0: Preparar** $\rightarrow$ Correo de datos, copia de respaldo de la base de datos y ajustes previos al Excel de gestión[cite: 44, 46, 47].
2. **Fase 1: Configurar** $\rightarrow$ Creación de escala de evaluación (1 a 5), plantillas, metas, habilidades y acción semanal (mantenida apagada temporalmente)[cite: 44, 48, 49].
3. **Fase 2: Cargar personal y encender** $\rightarrow$ Verificación de fichas, usuarios, permisos y activación de automatismos tras recibir la información de Talento Humano[cite: 44, 51, 52].
4. **Fase 3: Piloto (Septiembre)** $\rightarrow$ Primera ejecución de evaluación con datos reales dentro de la base de pruebas[cite: 44, 54].
5. **Fase 4: Producción y rutina** $\rightarrow$ Réplica de la configuración en la base de datos real y operación regular mensual/semanal[cite: 44, 56].

---

## 2. Fase 0: Preparar
1. **Solicitud de datos a Talento Humano:** Envíe el complemento para ubicar al equipo de Desarrollo en el departamento `Administrativo / Desarrollo`, indicando su jefe directo y utilizando los nombres exactos del Cronograma de gestión[cite: 46].
2. **Duplicar la base para pruebas:** En *Odoo.com* > *Mis bases de datos*, duplique la base de Telcobras (ej. `telcobras-pruebas-evaluaciones`) para realizar allí todas las pruebas de las Fases 1 a 3[cite: 46].
3. **Activar el modo desarrollador:** En *Ajustes* > *Herramientas para desarrolladores* > *Activar el modo desarrollador*[cite: 46].
4. **Ajustes en el Cronograma de Gestión (`.xlsm`):**
   * Corrija la fórmula de *Metas diarias cumplidas* (hoja 08, celda 16) para evitar sesgos por celdas vacías[cite: 47].
   * Asegúrese de que no queden metas sin marcar entre el 7/09 y el cierre de mes[cite: 47].
   * Verifique el mes del sprint S5 (cambiar a `2026-10 Octubre` si corresponde)[cite: 47].

---

## 3. Fase 1: Configuración Inicial (Sin Dependencias de Personal)
*Todo se realiza en la base de pruebas (copia)*[cite: 48].

### 5. Instalación de Aplicaciones
Instale los módulos **Evaluaciones** y **Encuestas** (este último se usará en la Fase 2 para retroalimentación 360° entre pares)[cite: 48].

### 6. Plan de Evaluación Mensual
En *Evaluaciones* > *Configuración* > *Ajustes*[cite: 48]:
* Primera evaluación: 1 mes después del ingreso[cite: 48].
* Siguientes: Cada 1 mes[cite: 48].
* **Automatización de evaluaciones:** **APAGADA** por ahora (se encenderá en el paso 24 cuando el personal esté depurado)[cite: 48].

### 7. Escala de Evaluación (1 a 5)
Configure la escala en *Evaluaciones* > *Configuración* > *Escala de evaluación* alineada con los umbrales del semáforo del Excel[cite: 48]:
* **5 - Excepcional:** Eficiencia $\ge$ 100% con estimaciones validadas por el jefe[cite: 48].
* **4 - Supera:** Excelente (80% - 99%), jornada normal de 6 horas[cite: 48].
* **3 - Cumple con revisión:** Revisión (70% - 79%); se analiza la causa, la jornada no cambia[cite: 48].
* **2 - Necesita mejorar:** Intervención (60% - 69%), jornada de 7 horas[cite: 48].
* **1 - No cumple:** Crítico ($<$ 60%), jornada de 8 horas y advertencia contractual[cite: 48].

### 8. Departamentos y Estructura
* Confirme la existencia de *Gerencia*, *Administrativo* y *Operativo*[cite: 48].
* Cree el departamento **Desarrollo** con departamento padre *Administrativo* (Odoo lo mostrará como `Administrativo / Desarrollo`)[cite: 48].

### 9. Etiquetas y Habilidades
* **Etiquetas de metas:** Cree los frentes del Cronograma (*Facturación Electrónica, Chatbot, PayPal, Contactos, Contabilidad, Calidad / Errores, Automatización general, Gestión / PMO, Sitio web*)[cite: 49].
* **Habilidades (Desarrollo):** Asigne niveles (*Básico 25, Intermedio 50, Avanzado 75, Experto 100*) para habilidades clave como *Odoo, Integración, Inteligencia artificial* y *Gestión*[cite: 49].

### 11 y 12. Plantillas de Evaluación
* **Plantilla "Desarrollo":** Asigne el cuestionario de autoevaluación (empleado) y evaluación gerencial (ver textos detallados en los Anexos B1 y B2 del manual original)[cite: 49, 60].
* **Plantilla "Gerencia y jefes":** Diseñada para roles de coordinación sin horas registradas en el cronograma (ej. rol PMO) (ver Anexo B3)[cite: 49, 60].

### 13. Acción Semanal de Cierre de Sprint (Apagada)
1. En *Ajustes* > *Técnico* > *Automatización* > *Acciones planificadas* > *Nuevo*[cite: 49].
2. Nombre: `Cierre de sprint - Desarrollo` (Modelo: Empleado, Ejecutar cada: 1 semana)[cite: 49].
3. Acción: *Ejecutar código* (utilizando el script del Anexo C del manual original para filtrar el departamento Desarrollo y generar actividades al jefe directo)[cite: 49, 61].
4. **Deje la opción Activo desmarcada** hasta la Fase 2[cite: 49].

---

## 4. Fase 2: Carga de Personal y Activación de Automatismos
*Inicia al recibir la respuesta formal de Talento Humano*[cite: 51].

1. **Verificación de fichas (`Empleados`):** Revise que ningún empleado quede en el grupo "Ninguno" (sin jefe asignado) y corrobore concordancia de nombres con el Cronograma[cite: 51].
2. **Vínculo de Usuarios:** En la ficha de cada empleado (*Configuración de RR. HH.*), llene el campo **Usuario relacionado** para habilitar notificaciones y autoevaluaciones[cite: 51].
3. **Fecha de Evaluación Estándar:** Establezca como fecha de próxima evaluación para todo el equipo de Desarrollo el **miércoles 4 de noviembre de 2026** (2.° día hábil de noviembre, cubriendo el ciclo de octubre)[cite: 52].
4. **Permisos y Roles:** Otorgue el rol *Administrador* exclusivamente a Talento Humano; el rol *Oficial* al Jefe de Desarrollo; y mantenga a los empleados con permisos estándar (usuario interno)[cite: 52].
5. **Encendido de la acción semanal y automatización:** Tras realizar pruebas manuales, marque la acción planificada como *Activa* y encienda la *Automatización de evaluaciones* en los ajustes del módulo[cite: 52].
6. **Carga de Metas del Mes:** En *Evaluaciones* > *Metas* > *Nuevo*, cargue el objetivo principal, indicador de éxito, fecha límite y etiqueta para cada colaborador (máximo 3 metas mensuales por persona)[cite: 53].

---

## 5. Fase 3 y 4: Piloto, Calibración y Rutina Operativa

### Calibración de la Rúbrica (Fase 3)
Antes de entregar notas, el jefe realiza una calibración ciega comparando su criterio intuitivo con el resultado de la rúbrica ponderada[cite: 55]. Si hay desviaciones mayores a 2 puntos, se ajustan los criterios antes de llevar el modelo a producción[cite: 55].

### Rutina Operativa en Producción (Fase 4)
* **Diario:** Cada empleado registra horas, subtareas, metas diarias y errores/incidencias[cite: 56].
* **Lunes (7:00 a. m.):** Odoo genera automáticamente la actividad de *Cierre de sprint* para el jefe con vencimiento al viernes[cite: 56].
* **Lunes a Viernes:** Conversaciones breves de seguimiento (10 minutos) basadas en el semáforo semanal[cite: 56].
* **Último día hábil del mes:** Cierre de registros diarios y validación de metas sin dejar celdas vacías[cite: 56].
* **2.° día hábil del mes:** Generación de evaluaciones, cruce con la hoja de corte mensual y ejecución de reuniones de retroalimentación[cite: 56].

---

## 6. Anexo: Ponderación de la Rúbrica de Calificación (A-E)

| Área Evaluada | Peso | Criterios principales de calificación (1 a 5) |
| :--- | :--- | :--- |
| **A. Productividad**[cite: 59] | **30%**[cite: 59] | Basado en la **eficiencia ajustada** del mes (Nota 5 $\ge$ 100% con estimaciones validadas; Nota 1 $<$ 60%)[cite: 59]. |
| **B. Cumplimiento del plan**[cite: 59] | **20%**[cite: 59] | Basado en el porcentaje de **metas diarias cumplidas** ($Si \div [Si + No]$)[cite: 59]. |
| **C. Calidad**[cite: 59] | **20%**[cite: 59] | Evaluación de **errores** donde es responsable de corrección (Severidade, reincidencia y días abierto)[cite: 59]. |
| **D. Metas del mes**[cite: 59] | **20%**[cite: 59] | **Progreso y cumplimiento** de las metas estratégicas registradas en Odoo[cite: 59]. |
| **E. Registro y colaboración**[cite: 59] | **10%**[cite: 59] | Consistencia en los **días con registro**, ausencia de metas vacías e incidencias causadas[cite: 59]. |

* **Nota Final:** Cálculo ponderado $0,30\cdot A + 0,20\cdot B + 0,20\cdot C + 0,20\cdot D + 0,10\cdot E$ (redondeado al entero más cercano)[cite: 59]. 
* **Regla de Consecuencia:** La jornada laboral aplicable (6, 7 u 8 horas) se determina estrictamente por el **Área A (Productividad)**, garantizando que la nota global y la sanción operativa mantengan criterios independientes y transparentes[cite: 56, 59].

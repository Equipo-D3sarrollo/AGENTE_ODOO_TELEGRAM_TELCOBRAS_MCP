<!-- Path: rrhh_y_proyectos/10_creacion_empleados_odoo19.md -->

# Creación de Empleados en Odoo 19: Talento Humano y Gerente

**Módulo Odoo:** Empleados / Evaluaciones / Hojas de horas / Encuestas[cite: 89]  
**Rol Requerido:** Talento Humano y Gerentes de Área[cite: 89]  
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 89]  
**Cuándo usar este documento:** Utilice este manual para conocer el reparto de responsabilidades, pasos obligatorios y validaciones conjuntas necesarios al crear o actualizar la ficha de un empleado o practicante en Odoo 19, garantizando su correcta integración en los módulos de evaluación, control de tiempo y encuestas[cite: 89].

---

## 1. Objetivo y Repartición de Responsabilidades

Para que un colaborador funcione correctamente en Evaluaciones, Hojas de horas y Encuestas, su ficha de empleado requiere tanto datos administrativos como datos operativos del día a día[cite: 89]. La carga de información se divide de la siguiente manera[cite: 89]:

| Quién | Qué llena | Por qué |
| :--- | :--- | :--- |
| **Talento Humano**[cite: 89] | Identificación, contacto, departamento, puesto, gerente, fecha de ingreso, tipo de vinculación, usuario de Odoo y gestión de retirados[cite: 89]. | Corresponde a los datos del contrato y de la estructura organizacional de la empresa[cite: 89]. |
| **Gerente del Área**[cite: 89] | Horario, turnos, aprobadores, próxima evaluación, habilidades y objetivos[cite: 89]. | Conoce de forma directa cómo trabaja cada persona en el día a día[cite: 89]. |
| **Ambos**[cite: 89] | Verificación conjunta final[cite: 89]. | Confirmar que la ficha quedó completa y funcional[cite: 89]. |

### Orden de Trabajo y Plazos
1. **Paso 1:** Talento Humano crea fichas, usuarios y archiva retirados (Parte A) dentro de los **5 días hábiles** desde la recepción de la directriz[cite: 89].
2. **Paso 2:** Talento Humano avisa al gerente que la Parte A está lista el mismo día[cite: 89]. *(Nota: El gerente no inicia la Parte B hasta que la ficha exista y tenga departamento asignado; registrar horas sin departamento hace que queden huérfanas de área)[cite: 90].*
3. **Paso 3:** El gerente completa horarios, aprobadores, habilidades y objetivos (Parte B) en **3 días hábiles** tras el aviso[cite: 89].
4. **Paso 4:** Verificación conjunta entre Talento Humano y el gerente al finalizar la Parte B[cite: 89].

### Permisos Requeridos en Odoo
* **Talento Humano:** `Empleados: Administrador`, `Evaluaciones: Administrador`, permiso para crear usuarios[cite: 90].
* **Gerente del Área:** `Empleados: Oficial` (para editar fichas de su equipo), `Evaluaciones: Oficial`, `Hojas de horas: Aprobador`, `Proyecto: Administrador`[cite: 90]. *(Sin el permiso de Oficial en Empleados, el gerente verá las fichas pero no podrá guardar los cambios de la Parte B)[cite: 90].*

---

## 2. Parte A: Talento Humano (Datos Contractuales)

### A1. Creación de la Ficha
En **Empleados > Nuevo**, cree una ficha por cada persona activa (empleados y practicantes)[cite: 90]:
* **Nombre completo:** En el encabezado, idéntico al documento de identidad, sin abreviaturas, para permitir el cruce con nómina[cite: 90].
* **Correo laboral:** En el encabezado, único por persona; será su inicio de sesión y el canal para recibir encuestas[cite: 90].
* **Celular de trabajo:** En el encabezado[cite: 90].
* **Departamento:** Pestaña *Trabajo*, seleccionado de la lista (ej. `Gerencia / Desarrollo Software`)[cite: 90].
* **Puesto de trabajo:** Pestaña *Trabajo*, elegido de la lista predefinida[cite: 90].
* **Gerente:** Pestaña *Trabajo*, jefe directo que debe existir previamente como empleado con usuario personal[cite: 91].
* **Número de documento:** Pestaña *Personal*[cite: 91].
* **Fecha de ingreso:** Pestaña *Nómina*[cite: 91].
* **Tipo de empleado:** Pestaña *Ajustes*, configurado como `Empleado` o `Practicante` (anotando su tipo de vinculación: contrato SENA o convenio universitario)[cite: 91].
* **Responsable de RR. HH.:** Pestaña *Ajustes*, un colaborador de Talento Humano con usuario personal (nunca cuenta genérica)[cite: 91].

*Guarde la ficha haciendo clic en el icono de la nube antes de crear el usuario[cite: 91].*

### A2. Creación del Usuario
1. En la ficha guardada, pulse el botón **Crear usuario**[cite: 91]. Odoo lo creará usando el correo laboral y lo dejará vinculado automáticamente[cite: 91].
2. Verifique en la pestaña *Ajustes* que el campo *Usuario* muestre el nombre correcto[cite: 91].
3. Asigne los permisos correspondientes en **Ajustes > Usuarios y compañías > Usuarios** (Integrantes con evaluaciones sin rol, hojas de horas solo propias y proyecto como usuario; gerentes con roles oficiales)[cite: 91].
4. La persona recibirá un correo electrónico automatizado para establecer su contraseña[cite: 91]. *(Si Gerencia determina que un practicante no tendrá usuario por costos de licencia, se crea únicamente la ficha)[cite: 91].*

### A3. Restricción sobre Gerentes
Los gerentes se crean como cualquier empleado con su usuario propio[cite: 91]. **Las cuentas compartidas** (como `Area Gerencia`, `Area Administrativa`, etc.) tienen prohibido actuar como gerentes, aprobadores o evaluadores[cite: 91]. Asigne los gerentes a cada departamento desde **Empleados > Configuración > Departamentos**[cite: 91, 92].

### A4. Manejo de Retirados
Para los empleados retirados, abra su ficha, despliegue el menú de acciones (*engranaje*) y seleccione **Archivar**, adjuntando el motivo de salida[cite: 92]. Nunca se deben eliminar registros para preservar la integridad histórica y contable[cite: 92].

---

## 3. Parte B: Gerente del Área (Gestión Operativa)

Inicia una vez Talento Humano notifica la culminación de la Parte A[cite: 93].

### B1. Horarios y Turnos
En la ficha del empleado (`Empleados > [Nombre]`)[cite: 93]:
* **Horario laboral:** Pestaña *Trabajo* o *Nómina*. Define las horas reales de la jornada (ej. 6 horas de lunes a viernes), base para días laborables y horas esperadas[cite: 93]. Si no existe, créelo en *Configuración > Horarios de trabajo*[cite: 93].
* **Funciones (Planeación):** Pestaña *Ajustes > Planeación*. Seleccione los turnos aplicables, incluidos remotos; si no existen, créalos en *Planeación > Configuración > Funciones*[cite: 94].
* **Función predeterminada:** Indique el turno más habitual de la persona[cite: 94].

### B2. Aprobadores
En la pestaña *Ajustes > Aprobadores*, asigne al gerente con su **usuario personal** para[cite: 94]:
* Hoja de horas[cite: 94].
* Tiempo personal[cite: 94].
* Asistencia (si aplica en el área)[cite: 94].
* Gasto[cite: 94].

### B3. Próxima Evaluación
En la pestaña *Ajustes > Evaluación > Próxima evaluación*, establezca la fecha unificada del equipo[cite: 94]. Para el equipo de Desarrollo se fija el **04/11/2026** (segundo día hábil de noviembre, cubriendo el ciclo de octubre)[cite: 94]. Sobrescriba cualquier fecha errónea generada por defecto[cite: 94].

### B4. Habilidades
En la pestaña *Currículum > Habilidades*, agregue únicamente las competencias utilizadas en el puesto real[cite: 94]. El nivel inicial actúa como punto de partida y solo asciende mediante evaluaciones mensuales respaldadas por evidencia[cite: 94]:
* **Principiante:** Comienza a usarla con acompañamiento[cite: 94].
* **Básico:** La utiliza con guía o siguiendo ejemplos[cite: 95].
* **Intermedio:** La opera de forma autónoma en casos comunes[cite: 95].
* **Avanzado:** Resuelve casos complejos y corrige errores sin ayuda[cite: 95].
* **Experto:** Enseña y define estándares de ejecución en el equipo[cite: 95].
*(Nota para Desarrollo: Tipos habilitados: Odoo, Integración, Programación, Inteligencia artificial, Gestión, Web y Chatbots)[cite: 95].*

### B5. Objetivos del mes
En **Evaluaciones > Objetivos > Nuevo**, configure un **máximo de 3 objetivos por persona**, indicando indicador de éxito claro, fecha límite (último día hábil del mes) y etiqueta del frente correspondiente[cite: 95].

---

## 4. Verificación Conjunta y Resolución de Errores

Talento Humano y el gerente realizan una revisión cruzada de 15 minutos para validar[cite: 96]:
1. **Ausencia de huérfanos:** En *Empleados*, agrupe por Departamento y Gerente; nadie debe quedar en el grupo "Ninguno"[cite: 96].
2. **Control de usuarios:** Verifique mediante filtro personalizado que solo carezcan de usuario aquellos practicantes exentos por directriz gerencial[cite: 96].
3. **Historial de retirados:** Confirme en el filtro *Archivado* la presencia de todas las salidas[cite: 96].
4. **Prueba de plantilla:** En *Evaluaciones > Nuevo*, seleccione un empleado y confirme que el sistema asigne automáticamente la plantilla de su área y al gerente como encargado (descarte la prueba al terminar)[cite: 96].

### Matriz de Resolución de Incidencias Frecuentes

| Anomalía detectada | Causa probable | Responsable de corrección |
| :--- | :--- | :--- |
| **La persona no puede entrar a Odoo**[cite: 97] | No se creó el usuario o no se aceptó la invitación por correo[cite: 97]. | Talento Humano[cite: 97] |
| **No aparece en los reportes del área**[cite: 97] | La ficha de empleado carece de departamento asignado[cite: 97]. | Talento Humano[cite: 97] |
| **La evaluación no tiene evaluador**[cite: 97] | La ficha no tiene un gerente vinculado[cite: 97]. | Talento Humano[cite: 97] |
| **El gerente no puede guardar cambios**[cite: 97] | Carece de permisos de edición en la aplicación Empleados[cite: 97]. | Administrador de Odoo[cite: 97] |
| **Las horas no llegan para aprobación**[cite: 97] | El aprobador de hojas de horas está vacío o usa una cuenta genérica[cite: 97]. | Gerente del área[cite: 97] |
| **La evaluación cae en una fecha distinta**[cite: 97] | El campo de próxima evaluación no se sincronizó[cite: 97]. | Gerente del área[cite: 97] |
| **La persona no recibe encuestas**[cite: 97] | El correo laboral está vacío o mal escrito[cite: 97]. | Talento Humano[cite: 97] |

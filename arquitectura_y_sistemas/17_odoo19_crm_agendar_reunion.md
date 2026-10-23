<!-- Path: arquitectura_y_sistemas/17_odoo19_crm_agendar_reunion.md -->

# 📅 Manual Operativo 17: Gestión de Citas y Reuniones Comerciales desde el CRM (Odoo 19)

**Módulo / Área:** Comercial / CRM y Calendario  
**Rol Requerido:** Ejecutivos de Ventas, Asesores Comerciales, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para programar reuniones presenciales o videollamadas directamente desde las oportunidades del CRM en Odoo 19, enviar invitaciones automáticas por correo electrónico, evitar choques de horario y sincronizar las citas con el embudo comercial.

---

## 1. Requisitos Previos

* **Aplicaciones Instaladas:** Tener activas las apps de **CRM** y **Calendario** en la instancia de Odoo 19.
* **Datos del Contacto:** La oportunidad comercial debe contar obligatoriamente con un **correo electrónico** registrado en su ficha de contacto; de lo contrario, el sistema no podrá despachar la invitación telemática.

---

## 2. Procedimiento para Agendar una Reunión

1. **Acceso al Calendario:** Ingresar al módulo de **CRM**, abrir la oportunidad comercial y hacer clic en el botón inteligente superior (que indicará *"Sin reuniones"* o mostrará la fecha de la próxima cita programada).
2. **Selección de Horario:** Se desplegará la vista de calendario basada en la disponibilidad del usuario. Seleccionar un espacio libre en la agenda y hacer clic en **Más opciones** para configurar el evento completo.
3. **Parametrización del Evento:**
   * **Nombre de la Reunión:** Asignar un título definitivo y descriptivo antes de guardar (ej. `[Cliente] – Presentación de Propuesta`), evitando que el cliente reciba invitaciones con nombres genéricos por defecto.
   * **Fecha, Hora y Duración:** Definir el bloque temporal previsto.
   * **Ubicación / Videollamada:** Marcar si es un evento presencial (registrando la dirección física) o virtual. Para videollamadas, hacer clic en el botón **"Reunión de Odoo"**, el cual generará automáticamente un enlace integrado de *Odoo Discuss*.
   * **Asistentes:** Verificar que el correo del cliente y los participantes internos estén incluidos correctamente.
4. **Guardar y Enviar:** Al pulsar en **Guardar**, Odoo despachará de forma automática un correo electrónico de invitación con opciones interactivas para que el cliente acepte o rechace la convocatoria.

---

## 3. Experiencia del Cliente y Videollamada

* **Invitación por Correo:** El cliente recibe un correo con los datos de la cita y botones de confirmación. Para unirse a la sesión virtual, solo debe hacer clic en el enlace de *Odoo Discuss* (sin requerir credenciales ni usuarios en el ERP).
* **Herramientas de Colaboración:** La sala virtual de Odoo Discuss permite activar cámara y micrófono, compartir pantalla, desenfocar el fondo, levantar la mano e intercambiar archivos o notas en el chat interno lateral.

---

## 4. Trazabilidad en el CRM y el Calendario

* **Creación de Actividades:** Al programar la reunión, el sistema genera automáticamente una actividad de seguimiento asociada a la oportunidad, visible en color verde dentro del pipeline en vista Kanban.
* **Prevención de Cruces:** El evento queda bloqueado en la app de **Calendario**, inhabilitando ese espacio temporal para otras citas y garantizando que el equipo comercial mantenga una agenda organizada.

---

## 5. Recomendaciones Operativas para Telcobras

* **Convenciones de Títulos:** Estandarizar la nomenclatura de las reuniones para facilitar auditorías y seguimientos (ej. *"Telcobras – Levantamiento Técnico"* o *"Telcobras – Cierre de Contrato"*).
* **Plantillas de Correo Corporativo:** Utilizar plantillas de confirmación y recordatorio alineadas con el manual de identidad visual de la empresa.
* **Sincronización de Calendarios:** Se recomienda parametrizar la sincronización de Odoo con las herramientas de correo corporativo (Google Calendar o Microsoft Outlook) para centralizar la agenda de los asesores.

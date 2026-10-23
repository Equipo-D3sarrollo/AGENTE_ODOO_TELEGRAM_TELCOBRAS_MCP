<!-- Path: arquitectura_y_sistemas/22_odoo19_gestion_de_eventos.md -->

# 🎟️ Manual Operativo 22: Creación, Configuración y Gestión Integral de Eventos en Odoo 19

**Módulo / Área:** Comercial y Marketing / Eventos y Sitio Web  
**Rol Requerido:** Organizadores de Eventos, Especialistas de Marketing, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para configurar, publicar y administrar seminarios, capacitaciones, lanzamientos o talleres corporativos en Odoo 19, abarcando desde la definición de aforos y tipos de boletería hasta la captura de datos de asistentes mediante preguntas personalizadas y la emisión de entradas con código QR.

---

## 1. Requisitos Previos e Infraestructura

* **Aplicaciones Instaladas:** Contar con las apps de **Eventos** y **Sitio web** activas en la plataforma Odoo 19.
* **Pasarelas de Pago:** Si se comercializan entradas con costo monetario, debe estar configurado y activo un proveedor de pago compatible (ej. Mercado Pago).

---

## 2. Creación y Parametrización del Evento

1. **Alta del Registro:** Ir al módulo de **Eventos > Nuevo**. Introducir el nombre representativo del evento (ej. *Seminario de Infraestructura y Conectividad*).
2. **Definición Temporal y Espacial:**
   * **Fechas y Horario:** Configurar los bloques de inicio y cierre asegurando que la **zona horaria** coincida con la región de operación (ej. `America/Bogota` para Colombia).
   * **Sede y Organizador:** Asignar la dirección física exacta o marcarlo como virtual, indicando a la organización y al usuario interno responsable.
3. **Control de Aforo:** Activar la casilla de **Limitar registros** y especificar la capacidad máxima de asistentes (indispensable para eventos presenciales).

---

## 3. Configuración de Tipos de Entrada y Boletería

**Pestaña:** Boletos > Agregar una línea

Un mismo evento puede estructurarse con múltiples categorías de acceso:
* **Entrada General / de Pago:** Definir el valor comercial (en **Pesos Colombianos - COP**), el cupo disponible asignado y las fechas límite de preventa y cierre de inscripciones.
* **Acceso Gratuito:** Asignar boletos con costo cero para capacitaciones internas o eventos abiertos.
* **Condición de Cupos:** La sumatoria de las capacidades de todos los tipos de entrada configurados debe igualar exactamente el límite total de aforo del evento.

---

## 4. Captura de Datos y Preguntas Personalizadas

**Pestaña:** Preguntas

* **Campos Estándar:** El sistema captura por defecto nombre y correo electrónico, permitiendo opcionalmente habilitar el teléfono de contacto.
* **Preguntas Adicionales:** Es posible añadir preguntas preexistentes (ej. *Empresa*) o crear cuestionarios nuevos (ej. tipo selección múltiple para identificar el interés principal del asistente en servicios de Telcobras), estableciendo cuáles son de carácter obligatorio para completar el registro.

---

## 5. Publicación y Experiencia del Asistente

1. **Publicación Web:** Hacer clic en el botón **Ir al sitio web** para previsualizar la landing page del evento y, al validar el diseño, activar la opción de **Publicar**.
2. **Registro Público:** El usuario accede a la página, selecciona su tipo de entrada y diligencia el formulario de inscripción junto con las preguntas definidas.
3. **Confirmación y Accesos:** Tras el registro, el sistema genera de forma automatizada un correo electrónico de confirmación que incluye un **código QR**, enlaces directos para descargar los boletos y accesos para sincronizar la cita con calendarios digitales (Google Calendar, Outlook o Apple).

---

## 6. Control de Asistencia y Seguimiento Comercial

* **Panel de Asistentes:** Desde la opción de *Asistentes* en el evento, el equipo organizador puede auditar las respuestas y cambiar los estados el día de la ejecución (ej. marcar quiénes asistieron o cancelar registros inasistentes).
* **Derivación al CRM:** Los datos de los asistentes capturados durante el evento pueden ser exportados o convertidos en oportunidades comerciales (*leads*) para que el equipo de ventas ejecute campañas de seguimiento segmentadas.

---

## 7. Directrices Operativas para Telcobras

| Tipo de Evento | Aplicación en Telcobras | Configuración Recomendada |
| :--- | :--- | :--- |
| **Capacitación a Clientes** | Talleres de uso y buenas prácticas de redes de fibra | Entrada gratuita con control de cupo y preguntas de cargo y empresa |
| **Lanzamiento Comercial** | Presentación de nuevos servicios del portafolio | Registro abierto con preguntas de segmentación de interés principal |
| **Capacitación Interna** | Inducción y normativas de seguridad para técnicos y personal de campo | Evento no publicado (privado), con control estricto de asistencia |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Creación y Gestión de Eventos en Odoo 19 (Configuración de sedes, aforos, tipos de boletos con moneda local, preguntas personalizadas, publicación web y emisión de códigos QR): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/22_odoo19_gestion_de_eventos.md

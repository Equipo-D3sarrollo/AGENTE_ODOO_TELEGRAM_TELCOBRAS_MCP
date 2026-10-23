````markdown
<!-- Path: arquitectura_y_sistemas/18_odoo19_lead_desde_whatsapp.md -->

# 💬 Manual Operativo 18: Creación de Oportunidades (Leads) desde WhatsApp en Odoo 19

**Módulo / Área:** Comercial / CRM y Comunicaciones  
**Rol Requerido:** Ejecutivos de Ventas, Agentes de Soporte Comercial, Administrador del Sistema  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para convertir conversaciones de clientes entrantes a través de canales de mensajería instantánea en oportunidades comerciales formales dentro del CRM de Odoo 19, asegurando la trazabilidad y el seguimiento en el embudo de ventas.

---

## 1. Requisitos Previos e Infraestructura

* **Integración Activa:** Tener configurada y enlazada la cuenta oficial de WhatsApp con la plataforma de Odoo 19 (requiere app Enterprise y pasarela de WhatsApp Business Platform / API de Meta)[cite: 10].
* **Consola de Comunicaciones:** Los chats de los clientes deben centralizarse dentro de la aplicación **Conversaciones (Discuss)** en el apartado dedicado a WhatsApp[cite: 10].

---

## 2. Procedimiento para la Creación de Leads

1. **Ubicación del Chat:** Ingresar al módulo de **Conversaciones**, seleccionar la sección de **WhatsApp** y abrir la ventana de chat correspondiente al cliente que muestra interés comercial[cite: 10].
2. **Ejecución del Comando Rápido:** En la caja de redacción del mensaje del chat, introducir el comando de sistema acompañado de una nomenclatura descriptiva[cite: 10]:
   
   `/lead [Nombre del Cliente o Empresa] – [Servicio de Interés]`
   
   *(Ejemplo: `/lead Ferretería López – Instalación de Fibra`)*[cite: 10]
3. **Generación del Registro:** Presionar la tecla **Enter**. Odoo creará de forma inmediata la oportunidad en el CRM y arrojará un enlace directo de acceso dentro del mismo historial del chat[cite: 10].

---

## 3. Post-Creación y Seguimiento Comercial

* **Acceso a la Oportunidad:** Al hacer clic en el enlace generado en el chat, se abrirá la ficha del lead en el CRM. La información de contacto, número de teléfono y el contexto previo de la conversación quedarán enlazados automáticamente[cite: 10].
* **Flujo Continuo:** Desde la oportunidad creada, el equipo comercial podrá ejecutar acciones subsiguientes como[cite: 10]:
  * Agendar reuniones técnicas o comerciales (vinculadas al calendario)[cite: 9, 10].
  * Enviar cotizaciones estructuradas con secciones y anticipos[cite: 7, 8, 10].
  * Mover el lead a través de las etapas del embudo en la vista Kanban[cite: 8, 10].

---

## 4. Consideraciones Operativas para Telcobras

* **Estrategia de Canales (Chatbot Teli vs. Asesores):** 
  * Si el número principal de atención automática de Telcobras es gestionado por un bot externo (Teli), dicho canal no podrá utilizar simultáneamente la integración nativa de WhatsApp en Odoo a menos que se centralice todo mediante desarrollo de API o se empleen líneas telefónicas independientes (una para atención automatizada y otra exclusiva para el equipo comercial en Odoo)[cite: 10].
* **Nomenclatura Estándar:** Mantener la convención de nombres `[Cliente] – [Servicio]` al utilizar el comando `/lead` para facilitar la auditoría y evitar duplicidad de registros en el CRM[cite: 10].

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Creación de Leads desde WhatsApp Odoo 19 (Integración con Discuss, comando rápido /lead, conversión de chats en oportunidades de CRM): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/arquitectura_y_sistemas/18_odoo19_lead_desde_whatsapp.md

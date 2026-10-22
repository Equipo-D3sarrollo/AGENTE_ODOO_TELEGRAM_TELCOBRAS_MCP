<!-- Path: arquitectura_y_sistemas/05_propuesta_tecnica_asistente_ia.md -->

# Propuesta Técnica y Arquitectónica: Asistente Virtual Inteligente - Landing Page TelcoBras (Fase 1)

**Módulo / Área:** Arquitectura de Software e Inteligencia Artificial[cite: 134]  
**Rol Requerido:** Líder de Desarrollo y Equipo Técnico[cite: 134]  
**Elaborado por:** Cristian Bedoya (Ingeniería de Sistemas)[cite: 134]  
**Fecha:** Octubre de 2026[cite: 134]  
**Empresa:** TelcoBras S.A.S.[cite: 134]  
**Cuándo usar este documento:** Utilice este manual para conocer los lineamientos técnicos, diseño de flujo conversacional, selección de modelo de IA y arquitectura de integración desacoplada (React - Make.com) para el asistente virtual de la landing page corporativa en su Fase 1[cite: 134, 135].

---

## 1. Resumen Ejecutivo y Alcance de la Fase 1

El presente documento define la propuesta técnica para la implementación del nuevo asistente virtual basado en Inteligencia Artificial en la landing page de TelcoBras[cite: 134].

### Alcance Definido
* Atención interactiva 24/7 a los visitantes de la web corporativa[cite: 134].
* Resolución de preguntas frecuentes (FAQs), difusión del portafolio y énfasis corporativo en la trayectoria y casos de éxito de la empresa[cite: 134].

### Exclusiones Temporales (Fase 1)
No incluye la generación automatizada de cotizaciones ni la conexión directa con el ERP Odoo, posponiendo estos módulos para fases posteriores según directriz comercial[cite: 134].

---

## 2. Diseño del Flujo de Conversación

El comportamiento del agente está diseñado para minimizar la fricción del usuario y optimizar la captura de prospectos[cite: 134]:

* **Apertura Proactiva:** El widget flotante aparecerá de forma proactiva a los 3 segundos de carga con un saludo inicial (*"¡Hola! Soy el asistente virtual de TelcoBras. ¿En qué te puedo ayudar hoy?"*), evitando búsquedas manuales[cite: 134].
* **Filtro Rompehielos (Botones Rápidos):** Hasta 4 opciones predefinidas al abrir el chat (Portafolio, Casos de éxito, Soporte técnico, Hablar con asesor). Si se omiten, el agente pasa a modo conversacional libre[cite: 134].
* **Restricción Estricta de Conocimiento (RAG):** Respuestas basadas exclusivamente en el documento corporativo oficial cargado en su base de conocimiento, bloqueando fuentes web abiertas para prevenir alucinaciones[cite: 134].
* **Personalidad y Tono:** Formal pero amable ("Usted"), con emojis técnicos estratégicos y priorización de casos reales (ej. proyecto de alumbrado de 2009)[cite: 134].
* **Manejo de Trolls / Excepciones:** Respuesta diplomática predefinida ante situaciones ajenas al negocio, bromas o expresiones groseras para reencarrilar el propósito corporativo[cite: 134].
* **Protocolo de Transferencia (Handoff):** Recopilación de datos básicos de contacto ante intención clara de compra y provisión de enlace directo a WhatsApp[cite: 134].

---

## 3. Justificación de la Inteligencia Artificial (Modelo y Costos)

Tras evaluar las alternativas tecnológicas exigidas por el plan de desarrollo, se ha seleccionado la siguiente configuración[cite: 134]:

* **Modelo Seleccionado:** `gpt-4o-mini` de OpenAI, integrado mediante el orquestador Make.com (Make AI Agent)[cite: 134].
* **Eficiencia y Costos:** Excelente relación costo-beneficio frente a modelos de mayor envergadura, reduciendo drásticamente el costo por token sin sacrificar fluidez ni comprensión[cite: 134].
* **Centralización Operativa:** Evita plataformas de chat de terceros; todo el procesamiento y seguridad se centralizan en Make.com, facilitando la escalabilidad hacia Odoo[cite: 134].

---

## 4. Arquitectura de Integración (Patrón Desacoplado React - Make)

El sistema operará bajo una arquitectura API REST síncrona, manteniendo completamente separada la interfaz visual de la lógica de procesamiento y asegurando un bajo acoplamiento[cite: 135]:

### Frontend (React 19 + Vite + Tailwind CSS)
Implementado mediante un componente modular `<ChatWidget/>` que gestiona el estado visual de la ventana de chat, animaciones de carga ("Escribiendo...") y la persistencia de la memoria de sesión mediante `localStorage` (`session_id`), garantizando que el bot recuerde al usuario ante recargas de página[cite: 135].

### Backend (Make.com)
Recibe las peticiones mediante Custom Webhooks, procesa el lenguaje a través del Agente de IA validando la base de documentos interna, y responde al cliente de forma estructurada a través de un módulo de Webhook Response con cabeceras CORS configuradas (`Access-Control-Allow-Origin: *`) para permitir la comunicación fluida con el navegador[cite: 135].

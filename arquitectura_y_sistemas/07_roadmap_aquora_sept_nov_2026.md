<!-- Path: arquitectura_y_sistemas/07_roadmap_aquora_sept_nov_2026.md -->

# Plan de Actualizaciones (Roadmap): Aquora Monitoreo PTAP (Sep - Nov 2026)

**Módulo / Área:** Operaciones, Monitoreo PTAP y Planificación Tecnológica[cite: 164]  
**Rol Requerido:** Administradores, Equipo de Desarrollo y Dirección Técnica[cite: 164]  
**Empresa / Proyecto:** Telcobras S.A.S. / Xpertic (Aquora v1.3.0 en producción, versión de planificación: 4 de septiembre de 2026)[cite: 164]  
**Cuándo usar este documento:** Utilice este roadmap para conocer el plan de entregas a 10 semanas (divididas en tres fases) que abarca desde las mejoras visuales en la consola y la gestión de alarmas hasta la infraestructura crítica y la deuda técnica[cite: 164].

---

## 0. Vía Rápida (Acciones previas de infraestructura)
Tres acciones de minutos con acceso de administrador en el servidor que no compiten con el desarrollo pero tienen impacto directo[cite: 166]:
1. **Cerrar la ruta `/hmi/`:** Evita que el HMI de planta siga expuesto públicamente sin autenticación (`sudo bash ~/deploy-scripts/hmi-retirar.sh`)[cite: 166].
2. **Abrir el puerto 80 en el cortafuegos:** Indispensable para la renovación automática del certificado TLS que vence el **29 de octubre de 2026**[cite: 166].
3. **Activar la sincronización horaria (NTP):** `sudo timedatectl set-ntp true` para garantizar la fiabilidad del indicador de latencia contra el PLC[cite: 166].

---

## 1. Estructura General del Plan (10 Semanas / 3 Fases)

| Fase / Alcance | Semanas | Objetivo Principal y Entregables | Hito de Publicación |
| :--- | :--- | :--- | :--- |
| **Fase I: Producto Visible**[cite: 167, 169] | Semanas 1 a 6 (07 Sep - 16 Oct)[cite: 169] | Diagnóstico por planta, filtros en bandeja y tablero, ayuda integrada, tutorial guiado y unificación de estilos visuales[cite: 169, 170, 171, 172]. | **Versión 1.4.0** (Semana 6)[cite: 167, 172] |
| **Fase II: Funciones de Fondo**[cite: 167, 173] | Semanas 7 a 8 (19 Oct - 30 Oct)[cite: 173, 174] | Habilitación de alarmas configurables desde la app (con auditoría) e historial acotado de telemetría (sujeto a decisión del cliente)[cite: 173, 174]. | **Versión 1.5.0** (Semana 8)[cite: 167, 174] |
| **Fase III: Técnico e Infraestructura**[cite: 167, 175] | Semanas 9 a 10 (02 Nov - 13 Nov)[cite: 175, 176] | Resolución de deuda técnica, verificación de tipos estricta, cerramiento de perímetro de red, respaldos externos y rotación de tokens[cite: 175, 176]. | **Cierre del Plan** (Semana 10)[cite: 167, 176] |

---

## 2. Detalle de las 10 Semanas de Desarrollo

* **Semana 1 (07 - 11 Sep):** *Revisión por planta.* Pantalla de diagnóstico administrativo para verificar el estado de la cadena (red, puente OPC, buffers y señales) sin abrir terminal[cite: 169].
* **Semana 2 (14 - 18 Sep):** *Filtros y orden en la bandeja.* Incorporación de rangos de fecha, filtros por planta y gravedad, opciones de ordenamiento y chips de no vistos[cite: 170].
* **Semana 3 (21 - 25 Sep):** *Filtros y búsqueda en el tablero.* Buscador de señales, chips de anomalías, favoritos fijados y rejilla adaptativa para tabletas y web[cite: 170].
* **Semana 4 (28 Sep - 02 Oct):** *Ayuda y preguntas frecuentes.* Catálogo indexado de códigos de error y soluciones integrado a la app con prueba de cobertura automática[cite: 171].
* **Semana 5 (05 - 09 Oct):** *Tutorial guiado.* Recorrido interactivo con anclaje visual en las pantallas principales y degradación segura a tarjeta centrada[cite: 171].
* **Semana 6 (12 - 16 Oct):** *Estilos, pulido y publicación v1.4.0.* Unificación de tokens visuales, accesibilidad por teclado y empaquetado del APK 1.4.0 junto con la actualización web[cite: 172].
* **Semana 7 (19 - 23 Oct):** *Alarmas configurables.* Edición de umbrales en caliente desde la app con pistas de auditoría y desbloqueo de notificaciones web mediante DNS interno[cite: 173].
* **Semana 8 (26 - 30 Oct):** *Historial acotado y publicación v1.5.0.* Implementación de persistencia selectiva de señales, gráficas de tendencia y publicación de la versión 1.5.0 (depende de validación con el cliente)[cite: 174].
* **Semana 9 (02 - 06 Nov):** *Deuda técnica y calidad.* Extensión de verificación de tipos a scripts, corrección de comandos de prueba en Linux y cierre de defectos abiertos[cite: 175].
* **Semana 10 (09 - 13 Nov):** *Infraestructura, seguridad y entrega.* Automatización del volcado del mapeo a Git, amarre del servicio a la interfaz local, respaldos de base de datos fuera de la VM y rotación de credenciales[cite: 176].

---

## 3. Decisiones Externas y Limitaciones del Entorno
* **Dependencias del Cliente:** El alcance del historial en base de datos (Semana 8) y el esquema de alarmas (Semana 7) requieren ratificación gerencial previa[cite: 177].
* **Factores Externos (Integrador):** Se documentan sensores congelados en planta entera (KM 18, Pichindé y Cascajal con más de 22-25 días sin cambio), los cuales obedecen a problemas de campo o infraestructura del integrador y no al software de la plataforma[cite: 178].

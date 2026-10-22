<!-- Path: arquitectura_y_sistemas/04_manual_centro_de_gestion_aquora.md -->

# Manual del Centro de Gestión Aquora: Monitoreo de Plantas PTAP

**Módulo / Área:** Operaciones, Monitoreo PTAP y Arquitectura de Servidor[cite: 102]  
**Rol Requerido:** Administradores, Jefes de Planta, Operadores y Equipo Técnico[cite: 102]  
**Empresa:** Telcobras S.A.S. / Xpertic (12 plantas en producción, v1.3)[cite: 102]  
**Cuándo usar este documento:** Utilice este manual para comprender la operación general de la app Aquora, la gestión de cuentas, la interpretación de estados de planta, el manejo de válvulas, las herramientas de diagnóstico y los procedimientos técnicos de servidor[cite: 102].

---

## 0. Guía de Lectura Rápida
* **Si atiendes operadores o jefes de planta:** Consulta las secciones de *Estados de una planta* y *Problemas comunes*[cite: 102].
* **Si gestionas cuentas de usuario:** Consulta las secciones de *Roles* y *Gestión de cuentas* (creación, aprobación, roles y reseteo)[cite: 102].
* **Si eres el responsable técnico (Servidor/Código):** Consulta la sección de *Acceso técnico, contexto y servidor* (requiere VPN y SSH)[cite: 102, 114].

---

## 1. Arquitectura y Eslabones del Sistema
Aquora supervisa en tiempo real 12 plantas de tratamiento de agua potable (PTAP)[cite: 103]. La cadena del dato permite ubicar el origen de cualquier fallo[cite: 103]:

| Eslabón | Descripción | Si falla aquí... |
| :--- | :--- | :--- |
| **Sensores y PLC local**[cite: 103] | Miden caudal, nivel, presión, pH y turbidez en campo[cite: 103]. | Es un problema de campo; se escala al integrador[cite: 103]. |
| **PLC maestro (OPC UA) + Backend**[cite: 103] | Servidor OPC UA que centraliza las 12 plantas y servicio en la VM que valida y sirve por API[cite: 103]. | Corte de ruta (caen todas las plantas) o problema interno (reciclar sesión / reiniciar)[cite: 103]. |
| **App Móvil (Expo)**[cite: 103] | Interfaz utilizada por operadores, jefes, admins y rol civil[cite: 103]. | Problema de red o dispositivo del usuario (WiFi, datos, versión desactualizada)[cite: 103]. |

---

## 2. Roles y Permisos
El sistema cuenta con cuatro roles estrictamente validados en backend y frontend[cite: 102, 103]:
* **Civil:** Solo verifica si el sistema funciona y si hay suministro de agua; no visualiza datos ni pestañas operativas[cite: 103, 104].
* **Operador:** Visualiza el tablero en tiempo real y opera válvulas de su planta asignada[cite: 104].
* **Jefe:** Mismas capacidades de visualización que el operador, pero **sin permisos para operar válvulas**[cite: 104].
* **Admin:** Control total de las 12 plantas, gestión de usuarios, generación de informes y diagnósticos avanzados[cite: 104].

---

## 3. Estados de una Planta
El indicador de estado (*badge*) en el tablero clasifica la salud del enlace y el proceso[cite: 105]:
* **LIVE (En vivo):** Sesión con el PLC sana y valores actualizados recientemente[cite: 106].
* **STABLE (Estable):** Sesión sana, pero el proceso se encuentra en régimen estable (sin cambios en los últimos 5 minutos). Operación normal; el dato es totalmente válido[cite: 106].
* **FROZEN (Congelado):** Se perdió la sesión con el PLC o transcurrieron más de 5 minutos sin cambios. Las señales se marcan como no usables (`BRIDGE_STALE`)[cite: 106].

---

## 4. Gestión de Cuentas
* **Auto-registro:** Todo usuario que se registra por su cuenta ingresa con rol **Civil** e inactivo hasta que un administrador apruebe la cuenta[cite: 106].
* **Aprovisionamiento técnico:** Creación mediante el script de servidor `db:credenciales`[cite: 106].
* **Recuperación de contraseña:** No existe autoservicio ni recuperación por correo en la app[cite: 107]. La contraseña debe ser regenerada por el técnico en el servidor mediante comandos de consola y entregada por un canal seguro[cite: 107].

---

## 5. Operación de Válvulas
La ejecución de comandos sobre equipos físicos requiere doble confirmación y muestra tres posibles acuses[cite: 107, 108]:
1. **Verde (Confirmada):** El equipo confirmó el cambio de estado[cite: 108].
2. **Ámbar (Verifique en sitio):** La orden se transmitió y registró, pero la planta carece de sensor de confirmación de estado (caso habitual en 10 plantas)[cite: 108].
3. **Rojo (No se ejecutó / Interlock):** Rechazada, sin energía, trabada o sin datos frescos (planta congelada)[cite: 108].

---

## 6. Herramientas de Diagnóstico Integradas
* **Salud del PLC (`GET /api/health/opc`):** Devuelve `200` si está conectado o `503` en caso contrario. Compara `lastNotificationAt` con `lastFrameEmittedAt` para detectar atascos[cite: 110].
* **Prueba de ruta (`GET /api/diagnostics/route-check`):** Ejecuta cuatro sondas (internet, ping al PLC, puerto OPC y puerto alternativo) para emitir códigos de diagnóstico (`SRV-07`, `PLC-01`, `PLC-11`, `PLC-12`, `PLC-13`)[cite: 111].
* **Diagnóstico de frescura (`diagnose-freshness.ts`):** Script de consola para aislar si un retraso de datos proviene de campo (integrador) o del motor de suscripción interno[cite: 111].

---

## 7. Acceso Técnico y Servidor (Solo Administradores / Técnico)
* **Infraestructura:** La máquina virtual de producción se ubica en `192.168.30.50` (o IP externa de respaldo), accesible mediante **VPN** y **SSH** utilizando llaves criptográficas Ed25519[cite: 115].
* **Ruta del repositorio:** `~/monitor-ptap` en la rama de despliegue `yosh`[cite: 115].
* **Procedimiento estándar de despliegue:**
  ```bash
  cd ~/monitor-ptap
  git fetch origin && git reset --hard origin/yosh
  npm ci
  npm run db:migrate -w @ptap/api
  npm run build
  pm2 restart ptap-api && pm2 save

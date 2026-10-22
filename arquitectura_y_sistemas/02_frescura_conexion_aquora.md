<!-- Path: arquitectura_y_sistemas/02_frescura_conexion_aquora.md -->

# Monitoreo de Plantas PTAP: Frescura y Conexión (Aquora)

**Módulo / Área:** Plataforma Aquora / Arquitectura OPC UA / Backend y Monitoreo[cite: 87, 88]  
**Rol Requerido:** Equipo de Desarrollo y Arquitectura[cite: 87, 88]  
**Empresa:** Telcobras S.A.S. / Xpertic (NIT 900.281.488-1)[cite: 87]  
**Cuándo usar este documento:** Utilice este manual para comprender los invariantes, umbrales, el flujo de las tres capas de estado (Puente, Frescura y Calidad), la estrategia de diagnóstico en campo y las lecciones de diseño derivadas de incidentes críticos en el monitoreo de plantas PTAP[cite: 87, 88].

---

## 1. Resumen Ejecutivo y Principio Fundamental
Este manual documenta el subsistema de frescura y conectividad OPC UA de la plataforma Aquora (`aquora.xpertic.co`)[cite: 87]. Explica cómo el sistema determina si un valor en pantalla es confiable, diferenciando estrictamente entre la conectividad de transporte y la dinámica del proceso[cite: 87, 88].

> 💡 **La lección arquitectónica fundamental:**  
> *"Vigilar solo la entrada de una tubería no dice nada sobre si algo sale por el otro lado. Todo indicador de salud debe medirse lo más cerca posible del resultado que le importa al usuario, no de la primera etapa."*[cite: 88]

---

## 2. Las Tres Capas de Estado
Para evaluar si un dato sirve, el sistema procesa tres capas independientes que nunca deben confundirse[cite: 89]:

| Capa | Valores posibles | Granularidad | Quién lo calcula | Significado de fallo |
| :--- | :--- | :--- | :--- | :--- |
| **Puente** (`bridgeStatus`)[cite: 89] | `Connecting`, `Connected`, `Stale`, `Recovering`, `Disconnected`, `Faulted`[cite: 89] | Global (una sesión para las 12 plantas)[cite: 89] | `bridge/bridge-state-machine.ts`[cite: 89] | El transporte. Red, VPN, firewall o PC del servidor OPC UA apagado[cite: 89]. |
| **Frescura** (`liveness`)[cite: 89] | `live`, `stable`, `frozen`[cite: 89] | Por planta (`pipeline/liveness.tracker.ts`)[cite: 89] | Rastreador de frescura[cite: 89] | El proceso o la propagación (campo o lectura interna)[cite: 89]. |
| **Calidad** (`quality`)[cite: 89] | `usable: true/false` (con motivos como `BAD QUALITY`, `INVALID_NUMBER`, `BRIDGE_STALE`)[cite: 89] | Por señal (`pipeline/quality.evaluator.ts`)[cite: 89] | Evaluador de calidad[cite: 89] | Una lectura concreta afectada sin dañar toda la planta[cite: 89]. |

* **Dependencia unidireccional:** La frescura consume el estado del puente (`sourceHealthy = bridgeStatus === 'Connected'`), y la calidad consume la frescura (`frozen` $\rightarrow$ `usable: false`). Jamás a la inversa[cite: 89].

---

## 3. El Puente OPC UA (Máquina de 6 Estados)
El puente gestiona la sesión con el servidor OPC UA de las plantas bajo umbrales configurables por variables de entorno[cite: 90]:
* **`OPCUA_WATCHDOG_TIMEOUT_MS` (Default: 30,000 ms):** Sin notificaciones durante este tiempo, pasa a `Stale` y dispara reciclaje[cite: 90].
* **`OPCUA_HEARTBEAT_INTERVAL_MS` (Default: 10,000 ms):** Sondeo del servidor (lee `ServerCurrentTime` exigiendo severidad *Good*)[cite: 90].
* **Niveles de recuperación automática:**
  * *Nivel 1:* Reciclar solo la suscripción (hasta 3 intentos)[cite: 90].
  * *Nivel 2:* Reciclar la sesión completa (serializado para evitar sesiones huérfanas)[cite: 90].

---

## 4. Reglas de Frescura (Liveness Tracker)
La frescura evalúa por planta cuánto hace que un valor cambió y si la sesión está sana, aplicando cinco reglas estrictas[cite: 91]:
1. **Sesión no sana** (`bridgeStatus !== Connected`) $\rightarrow$ **`frozen`** (el dato ya no está respaldado)[cite: 91].
2. **Arranque sin cambios previos** $\rightarrow$ **`stable`** (dentro de la ventana pasada, beneficio de la duda temporal)[cite: 91].
3. **Último cambio $\le$ 10 s** $\rightarrow$ **`live`** (proceso en movimiento)[cite: 91].
4. **Último cambio $>$ 300 s (5 min)** $\rightarrow$ **`frozen`** (distingue proceso quieto de datos muertos)[cite: 91].
5. **Todo lo demás** $\rightarrow$ **`stable`** (operación normal, valores válidos en régimen estable)[cite: 91].

---

## 5. Diagnóstico en Campo: Guía de 3 Pasos ("La planta no se actualiza")
Ante incidencias de actualización, ejecute el siguiente orden de comprobación (de lo más rápido a lo más profundo)[cite: 97]:

* **Paso 1 (5 segundos) — Estado del Puente:**  
  Ejecute `curl -s http://127.0.0.1:4000/api/health/opc | jq`[cite: 97]. 
  * Si `lastNotificationAt` avanza pero `lastFrameEmittedAt` se queda atascado, existe un atasco aguas abajo (Cura inmediata: `pm2 restart ptap-api`)[cite: 97].
* **Paso 2 (15 segundos) — Veredictos de Ruta:**  
  Consulte `/api/diagnostics/route-check` (ejecuta 4 sondas: internet, ping al host, puerto OPC UA y puerto de control alternativo para descartar filtros de firewall o VPN)[cite: 97].
* **Paso 3 (30 segundos en la VM) — Script de Campo:**  
  Ejecute `npx tsx scripts/diagnose-freshness.ts` para leer directamente del reloj del PLC sin pasar por la suscripción de la app y aislar si el fallo es interno o de campo[cite: 98].

---

## 6. Invariantes Críticos (Antes de Tocar Código)
1. **La frescura no se decide en el cliente:** Un socket caído solo significa "sin empuje"; el REST es la fuente de verdad[cite: 102].
2. **Ningún estado de "todo bien" es el destino por defecto:** Evita que condiciones indefinidas queden ocultas en estados estables (`stable` no puede ser un *else*)[cite: 91, 102].
3. **Todo indicador nuevo se mide cerca del usuario:** Vigilar solo la entrada ignora si el agregador de tramas se detuvo[cite: 88, 102].
4. **Criterios de frescura unificados:** Duplicar umbrales genera bugs latentes por divergencia[cite: 92, 102].
5. **El agregador se apaga y se enciende:** Todo componente con estado en reconexión debe implementar un ciclo `start()` robusto tras el `stop()`[cite: 102].
6. **Manejo de PM2:** Nunca use `pm2 restart --update-env` en producción (deja el proceso sin escuchar puertos); emplee siempre `pm2 restart ptap-api` a secas[cite: 98, 102].

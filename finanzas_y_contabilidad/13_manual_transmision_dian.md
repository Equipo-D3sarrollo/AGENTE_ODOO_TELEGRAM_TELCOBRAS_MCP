<!-- Path: finanzas_y_contabilidad/13_manual_transmision_dian.md -->

# 🌐 Manual Operativo 13: Transmisión a la DIAN (Web Services y Lotes) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Administrador de Infraestructura  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema automatizado necesite autenticarse, empaquetar y transmitir documentos electrónicos (Facturas, Notas o Eventos) hacia la plataforma de la DIAN, gestionando la comunicación mediante servicios web SOAP (Web Services), el envío individual o por lotes, y la recuperación del estado de validación.

---

# MANUAL DE TRANSMISIÓN A LA DIAN (WEB SERVICES Y LOTES)

**ID del Manual:** DIAN-UBL2.1-M06  
**Versión de API:** SOAP 1.2 / WS-Security  
**Formato de Salida:** Peticiones SOAP (XML) comprimidas en ZIP codificado en Base64  
**Propósito:** Detallar el procedimiento técnico de red, protocolos de seguridad y orquestación de consumo de los Web Services de la DIAN para la entrega de documentos electrónicos, así como el manejo de las respuestas asíncronas y sincrónicas que garantizan el cierre del ciclo fiscal.

---

## 1. PROCEDIMIENTO GENERAL

El proceso de transmisión actúa como la capa de transporte entre el sistema del contribuyente y la plataforma gubernamental. Inicia tomando el archivo XML (UBL 2.1) debidamente firmado y validado estructuralmente, comprimiéndolo en un archivo `.zip`, y codificándolo en formato `Base64`. Posteriormente, se construye un sobre SOAP asegurado con `WS-Security` que se transmite vía HTTPS. Finalmente, el sistema debe procesar la respuesta inmediata (que puede incluir un `TrackId` para envíos asíncronos/lotes) y ejecutar las consultas de estado subsecuentes para obtener el `ApplicationResponse` definitivo de validación.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Documento XML Preparado:** El documento (Invoice, CreditNote, DebitNote o ApplicationResponse) debe estar estructurado y firmado digitalmente (Xades-EPES) en un nodo `ds:Signature` válido.
2. **Certificado Digital de Transmisión:** Contar con el certificado digital X.509 instalado en el servidor para firmar la petición SOAP a nivel de cabecera (`WS-Security`).
3. **Credenciales Web Service:** Conocer la clave técnica y los identificadores (NIT del emisor) habilitados en el portal de la DIAN.
4. **Habilitación de Red:** El servidor debe tener salida HTTPS (puerto 443) autorizada hacia las URLs oficiales de los Web Services de la DIAN (ambientes de pruebas y producción).
5. **Generación de TrackId:** Capacidad del sistema para almacenar y rastrear de forma asíncrona identificadores de transacción (TrackId) entregados por la DIAN.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Compresión:** El archivo XML debe comprimirse dentro de un archivo `.zip`. El nombre del XML interno debe coincidir con la nomenclatura oficial exigida por la DIAN.
- **Regla de Tamaño Máximo:** La DIAN establece restricciones en el tamaño de los lotes; los envíos masivos deben fraccionarse según el límite en MB establecido en el anexo técnico vigente.
- **Regla de Seguridad de Transporte:** Es estrictamente obligatorio que la petición al Web Service utilice `WS-Security` (Token de firma con el certificado X.509) con vigencia temporal estricta (`Timestamp`). Peticiones sin firma o con el *timestamp* expirado serán rechazadas (`HTTP 500 / Authentication Failed`).
- **Regla de Sincronía/Asincronía:** El consumo de validación de facturas se maneja frecuentemente por método asíncrono (`SendBillAsync`), retornando un `ZipKey` o `TrackId` que requiere una segunda petición de consulta de estado para obtener la respuesta final.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL SOAP PASO A PASO)

### Paso 4.1: Compresión y Codificación (ZIP a Base64)
- **Procedimiento:**
  1. Tomar el documento XML cerrado y firmado.
  2. Nombrarlo bajo el estándar (Ej. `face_f89030021443_00000001.xml`).
  3. Comprimir el archivo en formato `.zip` con el mismo nombre (Ej. `face_f89030021443_00000001.zip`).
  4. Convertir los datos binarios del `.zip` a una cadena de texto en codificación `Base64`.

### Paso 4.2: Autenticación y Cabecera de Seguridad (WS-Security)
- **Ruta:** `SOAP-ENV:Header/wsse:Security`
- **Procedimiento:**
  1. Incluir el nodo `wsu:Timestamp` definiendo el `<wsu:Created>` y `<wsu:Expires>` para mitigar ataques de repetición.
  2. Incluir el nodo `wsse:BinarySecurityToken` con la llave pública del certificado X.509.
  3. Firmar los nodos críticos (Timestamp y Body) generando el `<ds:Signature>` correspondiente al estándar de seguridad web (SOAP Message Security).

### Paso 4.3: Construcción del Cuerpo de la Petición (`SOAP Body`)
Dependiendo de la operación a ejecutar (Envío de documento o Consulta de estado).

- **Ruta:** `SOAP-ENV:Body`
- **Procedimiento para Transmisión (`SendBillAsync`):**
  1. Declarar la operación: `<dian:SendBillAsync>`.
  2. Insertar el nombre del archivo en `<dian:fileName>`.
  3. Inyectar la cadena de texto Base64 obtenida en el Paso 4.1 dentro de `<dian:contentFile>`.
- **Procedimiento para Consulta (`GetStatusZip` o `GetStatus`):**
  1. Declarar la operación (Ej. `<dian:GetStatusZip>`).
  2. Inyectar el `TrackId` retornado en la transmisión asíncrona dentro de `<dian:trackId>`.

### Paso 4.4: Invocación del Servicio HTTPS (Ejecución)
- **Procedimiento:**
  1. Establecer conexión TCP cifrada hacia la URL del Web Service DIAN (Ej. `https://vpfe.dian.gov.co/WcfDianCustomerServices.svc`).
  2. Enviar el HTTP POST asegurando que las cabeceras contengan `Content-Type: application/soap+xml; charset=utf-8` o el `SOAPAction` correspondiente.
  3. Capturar la respuesta HTTP (código 200 OK es mandatorio para continuidad lógica).

### Paso 4.5: Desempaquetado de la Respuesta (`ApplicationResponse`)
- **Procedimiento:**
  1. Si la operación es asíncrona, capturar el nodo `<b:ZipKey>` (TrackId) y almacenarlo en la base de datos asociado a la factura. Activar tarea programada para la consulta `GetStatusZip`.
  2. Si la consulta `GetStatus` es exitosa o el método es sincrónico, extraer la cadena en Base64 contenida en la respuesta.
  3. Decodificar el Base64 y descomprimir el `.zip` devuelto por la DIAN.
  4. Extraer el XML resultante (`ApplicationResponse`) y verificar el `ResponseCode` (`02` = Aceptado, o códigos de rechazo).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado En Transmisión:** El paquete ha sido enviado y se ha recibido un `TrackId`. El documento entra en estado de espera asíncrona (Pendiente de Respuesta).
2. **Estado Validado (Éxito):** El motor lee un `ApplicationResponse` extraído de los servicios de la DIAN donde la operación es exitosa (`ResponseCode = 02`). El estado de la factura en el sistema ERP/CRM cambia a "Aceptada".
3. **Estado Rechazado (Error de Reglas):** La DIAN devuelve un XML detallando las reglas de negocio incumplidas en el grupo `LineResponse/Response/Description`. El documento queda anulado a nivel lógico y debe ser corregido para una nueva emisión.
4. **Post-Procesamiento:** Con la validación exitosa, el sistema invoca automáticamente el Manual de *Contenedor Electrónico (Attached Document)* para despachar el correo al adquiriente.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES)

- Si ocurre el error `HTTP 500 / The security token could not be authenticated`, la solución es validar que el certificado X.509 adjunto en el `<wsse:BinarySecurityToken>` no esté vencido, pertenezca a la empresa emisora autorizada, y que la firma del SOAP esté calculada correctamente.
- Si ocurre el error `Regla 90 - Documento procesado anteriormente`, la solución es verificar en base de datos si el documento ya había sido transmitido por timeout de red. Se debe utilizar la operación de consulta `GetStatus` con el CUFE/CUDE para recuperar la respuesta original de la DIAN.
- Si ocurre un `Timeout Exception / No Response`, la solución es marcar el envío como "Pendiente" y ejecutar reintentos programados (Delay Queue) que utilicen primero métodos de consulta de estado antes de intentar retransmitir el mismo documento.
- Si el método asíncrono devuelve `ZipKey no encontrado o Procesando`, la solución es espaciar el tiempo de polling (ej. reintento cada 30 segundos, luego cada 2 minutos) ya que los servidores de la DIAN pueden estar bajo alta concurrencia o en cola de validación.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Principales Métodos del Web Service DIAN (WcfDianCustomerServices)**
| Método SOAP | Uso / Operación | Modalidad | Entregable Principal |
| :--- | :--- | :--- | :--- |
| `SendTestSetAsync` | Envío de documentos para fase de habilitación/pruebas. | Asíncrono | `ZipKey` (TrackId) |
| `SendBillAsync` | Transmisión oficial de facturas y notas en Producción. | Asíncrono | `ZipKey` (TrackId) |
| `SendBillSync` | Transmisión sincrónica (Uso limitado por carga). | Sincrónico | `ApplicationResponse` en Base64 |
| `GetStatusZip` | Consulta del resultado final de un envío asíncrono. | Sincrónico | `ApplicationResponse` y Reglas |
| `GetStatus` | Consulta del estado histórico utilizando el CUFE o CUDE. | Sincrónico | `ApplicationResponse` (Copia) |
| `SendEventUpdateStatus` | Transmisión de Eventos RADIAN y Acuses de Recibo. | Sincrónico | `ApplicationResponse` de Aceptación |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Transmisión a la DIAN (Web Services y Lotes), Peticiones SOAP, WS-Security, SendBillAsync, GetStatus, y compresión ZIP a Base64: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/13_manual_transmision_dian.md

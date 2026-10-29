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
2. **Certificado Digital de Transmisión:** Contar con el certificado digital X.509 instalado en el servidor para firmar la petición SOAP a nivel de cabecera (`WS-Security`). El certificado debe estar vigente, no revocado, con cadena de confianza verificable y con los atributos para realizar conexión de transmisión (Anexo 1.9, reglas ZC01 a ZC06).
3. **Credenciales Web Service:** Conocer la clave técnica y los identificadores (NIT del emisor) habilitados en el portal de la DIAN. La clave técnica se obtiene con el método `GetNumberingRange`, disponible únicamente en el ambiente de producción en operación; en el ambiente de habilitación el catálogo de participantes genera el rango y la clave técnica para las pruebas (Anexo 1.9, num. 7.15 y 11.1.6).
4. **Habilitación de Red:** El servidor debe tener salida HTTPS (puerto 443) autorizada hacia las URLs oficiales de los Web Services de la DIAN (ambientes de pruebas y producción). La comunicación usa el protocolo TLS versión 1.2 con autenticación mutua a través de certificados digitales (Anexo 1.9, num. 7.5).
5. **Generación de TrackId:** Capacidad del sistema para almacenar y rastrear de forma asíncrona identificadores de transacción (TrackId) entregados por la DIAN.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Compresión:** El archivo XML debe comprimirse dentro de un archivo `.zip`. El nombre del XML interno debe coincidir con la nomenclatura oficial exigida por la DIAN.
- **Regla de Tamaño Máximo:** La DIAN establece restricciones en el tamaño de los lotes; los envíos masivos deben fraccionarse según el límite en MB establecido en el anexo técnico vigente. `SendBillAsync` recibe un ZIP con uno o más documentos, máximo 50; `SendBillSync` y `SendEventUpdateStatus` reciben un ZIP con un solo documento; el `contentFile` admite hasta 50 MB y cada archivo XML no puede superar 500 KB (Anexo 1.9, num. 6.8, 7.8, 7.10 y 7.13; regla ZA01).
- **Regla de Seguridad de Transporte:** Es estrictamente obligatorio que la petición al Web Service utilice `WS-Security` (Token de firma con el certificado X.509) con vigencia temporal estricta (`Timestamp`). El estándar es WS-Security 1.0 de OASIS con autenticación X.509 Certificate Token Profile 1.1, y los mensajes usan SOAP 1.2 con estilo Document/Literal (Anexo 1.9, num. 7.5). Peticiones sin firma o con el *timestamp* expirado serán rechazadas (`HTTP 500 / Authentication Failed`).
- **Regla de Sincronía/Asincronía:** El consumo de validación de facturas se maneja frecuentemente por método asíncrono (`SendBillAsync`), retornando un `ZipKey` o `TrackId` que requiere una segunda petición de consulta de estado para obtener la respuesta final. Los documentos expedidos en contingencia (tipo 03 y tipo 04) deben enviarse por el método `SendBillSync` (Anexo 1.9, num. 12.1 y 12.2).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL SOAP PASO A PASO)

### Paso 4.1: Compresión y Codificación (ZIP a Base64)
- **Procedimiento:**
  1. Tomar el documento XML cerrado y firmado.
  2. Nombrarlo bajo el estándar: prefijo del tipo de documento (`fv` factura, `nc` nota crédito, `nd` nota débito, `ar` ApplicationResponse, `ad` AttachedDocument) + NIT del facturador sin DV de 10 dígitos con ceros a la izquierda + código `ppp` del PT (`000` Software Propio, `001` Facturación Gratuita DIAN) + 2 últimos dígitos del año + consecutivo de 8 dígitos hexadecimales (Ej. `fv08001972680001900000011.xml`). El consecutivo se reinicia en `00000001` cada 1 de enero (Anexo 1.9, num. 6.7).
  3. Comprimir el archivo en formato `.zip` con el prefijo `z` + NIT de 10 dígitos + `ppp` + año + consecutivo de 8 dígitos hexadecimales del paquete enviado (Ej. `z08001972680001900000011.zip`). El ZIP puede combinar documentos `fv`, `nc`, `nd` y `ar` cuando se envía por el servicio asincrónico (Anexo 1.9, num. 6.8).
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
  4. En los ejemplos del anexo el prefijo usado es `wcf`, con el namespace `http://wcf.dian.colombia` (Anexo 1.9, num. 7.6 y 7.8.2).
- **Procedimiento para Habilitación (`SendTestSetAsync`):** Igual que `SendBillAsync`, agregando el parámetro `testSetId` (identificador del set de pruebas para el proceso de habilitación). El ZIP puede contener uno o todos los documentos del set de pruebas y el resultado se consulta con `GetStatusZip` (Anexo 1.9, num. 7.9).
- **Procedimiento para Consulta (`GetStatusZip` o `GetStatus`):**
  1. Declarar la operación (Ej. `<dian:GetStatusZip>`).
  2. Inyectar el `TrackId` retornado en la transmisión asíncrona dentro de `<dian:trackId>`. Para `GetStatus`, el `trackId` corresponde al valor del CUFE o del TrackId del documento (Anexo 1.9, num. 7.11 y 7.12).

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
3. **Estado Rechazado (Error de Reglas):** La DIAN devuelve un XML detallando las reglas de negocio incumplidas en el grupo `LineResponse/Response/Description`. El documento queda anulado a nivel lógico y debe ser corregido para una nueva emisión. El `ResponseCode` del documento rechazado es `04` (Anexo 1.9, num. 6.5.3).
4. **Post-Procesamiento:** Con la validación exitosa, el sistema invoca automáticamente el Manual de *Contenedor Electrónico (Attached Document)* para despachar el correo al adquiriente.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES)

- Si ocurre el error `HTTP 500 / The security token could not be authenticated`, la solución es validar que el certificado X.509 adjunto en el `<wsse:BinarySecurityToken>` no esté vencido, pertenezca a la empresa emisora autorizada, y que la firma del SOAP esté calculada correctamente (Anexo 1.9, reglas ZC01 a ZC06).
- Si ocurre el error `Regla 90 - Documento procesado anteriormente` (código no listado en el PDF del Anexo 1.9), la solución es verificar en base de datos si el documento ya había sido transmitido por timeout de red. Se debe utilizar la operación de consulta `GetStatus` con el CUFE/CUDE para recuperar la respuesta original de la DIAN.
- Si ocurre un `Timeout Exception / No Response`, la solución es marcar el envío como "Pendiente" y ejecutar reintentos programados (Delay Queue) que utilicen primero métodos de consulta de estado antes de intentar retransmitir el mismo documento. Para facturas, el anexo define como demora una respuesta que toma más de 1 minuto o el mensaje "Time out": se notifica a la DIAN a manera informativa, se transmite de nuevo a los 2 minutos y se hacen cuatro (4) intentos más con intervalo de 2 minutos; si persiste tras los 5 intentos, se declara la contingencia tipo 04 (Anexo 1.9, num. 12.3.1).
- Si se recibe uno de los mensajes de error de la tabla 12.2.1 (Caja de Herramientas), se transmite de nuevo a los 5 segundos y se hacen dos (2) intentos más con intervalo de 5 segundos; si persiste, se aplica la contingencia tipo 04 (Anexo 1.9, num. 12.2).
- Si el método asíncrono devuelve `ZipKey no encontrado o Procesando`, la solución es espaciar el tiempo de polling (ej. reintento cada 30 segundos, luego cada 2 minutos) ya que los servidores de la DIAN pueden estar bajo alta concurrencia o en cola de validación.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Principales Métodos del Web Service DIAN (WcfDianCustomerServices)**
| Método SOAP | Uso / Operación | Modalidad | Entregable Principal |
| :--- | :--- | :--- | :--- |
| `SendTestSetAsync` | Envío de documentos para fase de habilitación/pruebas, con el parámetro `testSetId` (Anexo 1.9, num. 7.9). | Asíncrono | `ZipKey` (TrackId) |
| `SendBillAsync` | Transmisión oficial de facturas y notas en Producción; ZIP con máximo 50 documentos (Anexo 1.9, num. 7.8). | Asíncrono | `ZipKey` (TrackId) |
| `SendBillSync` | Transmisión sincrónica de un solo documento por ZIP; disponible en habilitación y producción; método obligatorio para documentos de contingencia tipo 03 y tipo 04 (Anexo 1.9, num. 7.10, 12.1 y 12.2). | Sincrónico | `ApplicationResponse` en Base64 |
| `GetStatusZip` | Consulta del resultado final de un envío asíncrono. | Sincrónico | `ApplicationResponse` y Reglas |
| `GetStatus` | Consulta del estado histórico utilizando el CUFE o el TrackId (Anexo 1.9, num. 7.11). | Sincrónico | `ApplicationResponse` (Copia) |
| `SendEventUpdateStatus` | Transmisión de Eventos RADIAN y Acuses de Recibo; ZIP con un solo ApplicationResponse (Anexo 1.9, num. 7.13). | Sincrónico | `ApplicationResponse` de Aceptación |
| `GetXmlByDocumentKey` | Descarga del XML del documento por CUFE o TrackId; el certificado autenticado debe corresponder al NIT emisor o receptor (Anexo 1.9, num. 7.14). | Sincrónico | UBL del documento en Base64 |
| `GetNumberingRange` | Consulta de los rangos de numeración registrados en la DIAN (incluida la clave técnica); solo en producción en operación (Anexo 1.9, num. 7.15). | Sincrónico | Lista de rangos de numeración |
| `GetExchangeEmails` | Consulta de los correos electrónicos de recepción registrados por los facturadores en la habilitación; no requiere parámetros (Anexo 1.9, num. 7.16). | Sincrónico | Lista de correos en Base64 |
| `GetStatusEvent` | Consulta de los eventos asociados a una factura electrónica de venta como título valor, con su CUFE (Anexo 1.9, num. 7.17). | Sincrónico | Eventos de la factura |
| `GetReferenceNotes` | Consulta de las notas crédito asociadas a una factura electrónica de venta como título valor, con su CUFE (Anexo 1.9, num. 7.18). | Sincrónico | Notas crédito de la factura |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Transmisión a la DIAN (Web Services y Lotes), Peticiones SOAP, WS-Security, SendBillAsync, GetStatus, y compresión ZIP a Base64: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/13_manual_transmision_dian.md

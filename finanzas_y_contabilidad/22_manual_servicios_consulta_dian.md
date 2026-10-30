<!-- Path: finanzas_y_contabilidad/22_manual_servicios_consulta_dian.md -->

# 🔎 Manual Operativo 22: Servicios de Consulta DIAN (Rangos, XML, Correos, Eventos y Notas) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema necesite consultar a la DIAN, sin transmitir documentos, los rangos de numeración autorizados y su clave técnica, descargar el XML de un documento electrónico por su CUFE, obtener los correos de recepción registrados por los facturadores, o consultar los eventos y las notas crédito asociados a una factura electrónica de venta como título valor.

---

# MANUAL DE SERVICIOS DE CONSULTA DIAN

**ID del Manual:** DIAN-UBL2.1-M22  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Peticiones SOAP 1.2 y respuestas XML de los web services de la DIAN  
**Propósito:** Detallar los métodos de consulta del web service de la DIAN descritos en los numerales 7.14 a 7.18 del Anexo Técnico 1.9: `GetXmlByDocumentKey`, `GetNumberingRange`, `GetExchangeEmails`, `GetStatusEvent` y `GetReferenceNotes`.

---

## 1. PROCEDIMIENTO GENERAL

El Sistema de Validación Previa de la DIAN dispone de un web service con diferentes métodos (Anexo 1.9, num. 7.1). Los métodos de este manual son sincrónicos: el procesamiento y la respuesta se realizan en la misma conexión de consumo (Anexo 1.9, num. 7.2). La secuencia es (Anexo 1.9, num. 7.2.1):
1. El software cliente realiza la conexión, autenticándose por medio de certificado digital, y envía la solicitud (Request) al servicio de la DIAN.
2. El web service de la DIAN recibe el Request y llama al software encargado del procesamiento.
3. Al culminar el proceso, devuelve el resultado y responde al cliente.
4. El software cliente, al obtener la respuesta, cierra la conexión.

*La consulta del resultado de los envíos (`GetStatus`, `GetStatusZip`) se detalla en el Manual 13.*

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Participante Registrado:** Los participantes (facturadores, proveedores tecnológicos y adquirientes) registrados para operar con la plataforma de validación previa de la DIAN pueden hacer uso de los métodos del web service (Anexo 1.9, num. 7.4).
2. **Conexión y Software Cliente:** Poseer conexión a Internet de banda ancha y desarrollar el software cliente según el anexo, independientemente del lenguaje de programación (Anexo 1.9, num. 7.4).
3. **Autenticación:** El software cliente debe autenticarse ante la DIAN utilizando certificado digital (Anexo 1.9, num. 7.4).
4. **Estándar de Comunicación:** Protocolo TLS versión 1.2 con autenticación mutua a través de certificados digitales; WS-Security 1.0 de Oasis con autenticación X.509 Certificate Token Profile 1.1; mensajes SOAP versión 1.2 con Style/Encoding Document/Literal (Anexo 1.9, num. 7.5).
5. **Estructura del Mensaje:** La información de control de las llamadas se almacena en el elemento Header del SOAP para identificar y autenticar por medio del certificado digital; el área de datos es un documento XML con el formato definido para cada web service (Anexo 1.9, num. 7.6). En los ejemplos del anexo el namespace de los métodos es `http://wcf.dian.colombia` con el prefijo `wcf`.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Autorización (`GetXmlByDocumentKey`):** Se valida que el usuario autenticado por certificado digital corresponda al NIT de la empresa emisora o receptora del UBL consultado (Anexo 1.9, num. 7.14.1).
- **Regla de Ambiente (`GetNumberingRange`):** El servicio está disponible únicamente en el ambiente de producción en operación. En el ambiente de producción en habilitación, el catálogo de participantes genera el rango a utilizar (autorización, prefijo, rango desde y hasta, fechas desde y hasta y la clave técnica) para realizar las pruebas o el proceso de habilitación (Anexo 1.9, num. 7.15.1).
- **Regla de Parámetros (`GetNumberingRange`):** Los NIT se envían en formato numérico, sin puntos, sin separadores y sin dígito de verificación (Anexo 1.9, num. 7.15.2).
- **Regla de Título Valor (`GetStatusEvent`, `GetReferenceNotes`):** La petición requiere el CUFE de la factura electrónica de venta como título valor (Anexo 1.9, num. 7.17.2 y 7.18.2).
- **Regla sin Parámetros (`GetExchangeEmails`):** No se requieren parámetros (Anexo 1.9, num. 7.16.2).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSULTAS PASO A PASO)

### Paso 4.1: Descarga del XML de un Documento Electrónico (`GetXmlByDocumentKey`)
(Anexo 1.9, num. 7.14)

- **Función:** Descarga de XML. **Proceso:** Sincrónico.
- **Descripción:** Permite descargar el UBL del documento electrónico a través de la consulta del CUFE.
- **Petición:**

| Elemento | Y | Descripción | Tipo | Ocu |
| :--- | :--- | :--- | :--- | :--- |
| `trackId` | R | TrackId o CUFE del documento electrónico | String | 1 |

```xml
<wcf:GetXmlByDocumentKey>
  <wcf:trackId>f3be1a2f832c10564a18e5044e16891739f77631</wcf:trackId>
</wcf:GetXmlByDocumentKey>
```

- **Respuesta:** Contiene como resultado el UBL del documento electrónico en base64.

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `Code` | R | Código que identifica el proceso: `100` Acción completada OK; `205` XML no encontrado; `206` XML con errores en los campos número documento Emisor o número documento Receptor; `401` No autorizado. |
| `Message` | R | Descripción que identifica el proceso, con los mismos códigos de `Code`. |
| `XmlBytesBase64` | R | Archivo UBL del documento electrónico en base 64. |

*En el ejemplo de respuesta del anexo, el elemento aparece como `<b:Code>Ok</b:Code>` con el mensaje "El XML para el trackId: ... fue encontrado".*

### Paso 4.2: Consulta de Rangos de Numeración (`GetNumberingRange`)
(Anexo 1.9, num. 7.15)

- **Función:** Consulta de los rangos de numeración registrados en la DIAN, entregando la información relacionada con ellos. **Proceso:** Sincrónico.
- **Descripción:** Devuelve la lista de rangos de numeración y su información complementaria. Requiere como parámetros el NIT de la empresa, el NIT del proveedor tecnológico (o el NIT de la empresa cuando maneja el modo de operación Software Propio) y el identificador del software.
- **Petición:**

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `accountCode` | R | Número de identificación tributaria del obligado a facturar electrónicamente, en formato numérico sin puntos, ni separadores y sin dígito de verificación. |
| `accountCodeT` | R | Número de identificación tributaria del dueño del software, en formato numérico sin puntos, ni separadores y sin dígito de verificación. |
| `softwareCode` | R | Número de identificación del software que genera las facturas electrónicas en el consumidor del servicio web; debe incluir los guiones, letras y números tal como lo especifica la DIAN. |

```xml
<wcf:GetNumberingRange>
  <wcf:accountCode>999690829</wcf:accountCode>
  <wcf:accountCodeT>999690829</wcf:accountCodeT>
  <wcf:softwareCode>e26828e4-f284-4ed8-89c0-003f68396205</wcf:softwareCode>
</wcf:GetNumberingRange>
```

*Nota: la tabla del anexo marca los tres elementos como `R`, mientras que el ejemplo de petición los muestra como `<!--Optional:-->`. La tabla describe `softwareCode` como una cadena alfanumérica de 16 caracteres, pero el ejemplo usa un identificador con guiones de 36 caracteres.*

- **Respuesta:** Contiene el resultado con la lista de numeración.

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `OperationCode` | R | `100` Acción completada OK; `401` No autorizado; `301` No fue encontrado ningún rango de numeración para el NIT `accountCode`; `302` No registra prefijos asociados al código de software `softwareCode`; `303` El código del software no corresponde al NIT `accountCodeT`; `401` NIT `accountCodeT` no autorizado para consultar rangos de numeración del NIT `accountCode`; `500` Ha ocurrido un error con el servicio solicitado. Favor intente más tarde. |
| `OperationDescription` | R | Descripción del proceso, con los mismos códigos de `OperationCode`. |
| `NumberRangeResponse` | R | Arreglo estructurado con el o los rangos. |

- **Datos de cada rango en el ejemplo de respuesta:** `ResolutionNumber`, `ResolutionDate`, `Prefix`, `FromNumber`, `ToNumber`, `ValidDateFrom`, `ValidDateTo` y `TechnicalKey` (clave técnica).
- **Uso de la clave técnica:** La clave técnica se usa en el cálculo del CUFE; cuando se autoriza un nuevo rango, la DIAN entrega una nueva clave técnica, diferente a la del rango anterior (Anexo 1.9, num. 11.1.6; ver Manual 15).

### Paso 4.3: Consulta de Correos de Recepción (`GetExchangeEmails`)
(Anexo 1.9, num. 7.16)

- **Función:** Consultar el correo electrónico suministrado por el adquiriente registrado en el procedimiento de habilitación como facturador electrónico. **Proceso:** Sincrónico.
- **Descripción:** Devuelve una lista en base64 de los correos electrónicos de los facturadores que lo registraron en el ambiente de habilitación o producción para la recepción de facturas electrónicas.
- **Petición:** No se requieren parámetros.

```xml
<wcf:GetExchangeEmails/>
```

- **Respuesta:**

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `CsvBase64Bytes` | R | Archivo con la información en base 64. |
| `StatusCode` | R | Código de estado de validación: `0` Procesado Correctamente. |
| `Success` | R | `true` (válido) o `false` (no válido). |

*Este correo es el correo electrónico único de recepción de documentos electrónicos registrado en la habilitación (Anexo 1.9, num. 9; ver Manual 17).*

### Paso 4.4: Consulta de Eventos de una Factura (`GetStatusEvent`)
(Anexo 1.9, num. 7.17)

- **Función:** Obtener información de los eventos asociados a una factura electrónica de venta como título valor. **Proceso:** Sincrónico.
- **Petición:**

| Elemento | Y | Descripción | Tipo | Ocu |
| :--- | :--- | :--- | :--- | :--- |
| `trackId` | R | Valor del CUFE de la factura electrónica de venta consultada | String | 1 |

```xml
<wcf:GetStatusEvent>
  <wcf:trackId>681df61f3b0be667913177e0a866d2f72cb8376c4f82592ce371778478db8a7a0ee2d8ac84d37d603ca89369e4f21dda</wcf:trackId>
</wcf:GetStatusEvent>
```

- **Respuesta:** Contiene el resultado de los eventos asociados a la factura electrónica de venta como título valor (estructura en el Paso 4.6).

### Paso 4.5: Consulta de Notas Crédito de una Factura (`GetReferenceNotes`)
(Anexo 1.9, num. 7.18)

- **Función:** Obtener información de las notas asociadas a una factura electrónica de venta como título valor. **Proceso:** Sincrónico.
- **Descripción:** Consulta las notas crédito asociadas a una factura electrónica de venta como título valor.
- **Petición:**

| Elemento | Y | Descripción | Tipo | Ocu |
| :--- | :--- | :--- | :--- | :--- |
| `trackId` | R | Valor del CUFE de la factura electrónica de venta consultada | String | 1 |

```xml
<wcf:GetReferenceNotes>
  <wcf:trackId>681df61f3b0be667913177e0a866d2f72cb8376c4f82592ce371778478db8a7a0ee2d8ac84d37d603ca89369e4f21dda</wcf:trackId>
</wcf:GetReferenceNotes>
```

- **Respuesta:** Contiene el resultado de las notas crédito asociadas a la factura electrónica de venta como título valor (estructura en el Paso 4.6).

### Paso 4.6: Estructura de Respuesta de `GetStatusEvent` y `GetReferenceNotes`
(Anexo 1.9, num. 7.17.3 y 7.18.3)

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `ErrorMessage/string` | R | Mensaje que se genera con la validación del documento ante las reglas de la DIAN: código y descripción de las reglas fallidas (rechazo o notificación). Longitud hasta 1000. |
| `IsValid` | R | `true` si es válida y `false` si no lo es. |
| `StatusCode` | R | `00` Procesado Correctamente; `90` TrackId no encontrado; `99` Validaciones contienen errores en campos mandatorios. |
| `StatusDescription` | R | `00` Procesado Correctamente; `66` NSU no encontrado; `90` TrackId no encontrado; `99` Validaciones contienen errores en campos mandatorios. Longitud hasta 1000. |
| `StatusMessage` | O | Descripción del error de cada una de las validaciones iniciales; si no hay errores no entrega descripción. |
| `XmlBase64Bytes` | R | UBL del ApplicationResponse con la respuesta oficial de la DIAN, en base64. |
| `XmlBytes` | R | Corresponde al procesamiento: `true`. |
| `XmlDocumentKey` | R | TrackId o CUFE del instrumento procesado. |
| `XmlFileName` | R | Nombre del archivo UBL procesado. Longitud hasta 100. |

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **XML Recuperado:** Con `GetXmlByDocumentKey` el sistema obtiene el UBL del documento en base64, siempre que el certificado autenticado corresponda al NIT emisor o receptor.
2. **Rangos y Clave Técnica Actualizados:** Con `GetNumberingRange` (en producción en operación) el sistema obtiene los rangos de numeración vigentes y su clave técnica para el cálculo del CUFE.
3. **Directorio de Correos:** Con `GetExchangeEmails` el sistema obtiene la lista de correos de recepción registrados por los facturadores.
4. **Trazabilidad del Título Valor:** Con `GetStatusEvent` y `GetReferenceNotes` el sistema obtiene, por CUFE, los eventos y las notas crédito asociados a la factura electrónica de venta como título valor.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si `GetXmlByDocumentKey` responde `205` (XML no encontrado), la solución es verificar el TrackId o CUFE consultado.
- Si `GetXmlByDocumentKey` responde `401` (No autorizado), la solución es verificar que el certificado con el que se autentica corresponda al NIT de la empresa emisora o receptora del documento (Anexo 1.9, num. 7.14.1).
- Si `GetNumberingRange` responde `301`, no hay rangos de numeración para el NIT `accountCode`; si responde `302`, no hay prefijos asociados al `softwareCode`; si responde `303`, el código del software no corresponde al NIT `accountCodeT`; si responde `401`, el NIT `accountCodeT` no está autorizado para consultar los rangos del NIT `accountCode`; si responde `500`, se debe intentar más tarde (Anexo 1.9, num. 7.15.3).
- Si `GetNumberingRange` no está disponible en habilitación, es el comportamiento esperado: en ese ambiente el rango y la clave técnica los genera el catálogo de participantes (Anexo 1.9, num. 7.15.1).
- Si `GetStatusEvent` o `GetReferenceNotes` responden `90` (TrackId no encontrado), la solución es verificar el CUFE de la factura consultada; si responden `99`, las validaciones contienen errores en campos mandatorios (Anexo 1.9, num. 7.17.3 y 7.18.3).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Métodos de Consulta del Web Service DIAN**
| Método | Parámetro | Resultado | Referencia |
| :--- | :--- | :--- | :--- |
| `GetXmlByDocumentKey` | `trackId` (TrackId o CUFE) | UBL del documento en base64 | Num. 7.14 |
| `GetNumberingRange` | `accountCode`, `accountCodeT`, `softwareCode` | Rangos de numeración y clave técnica (solo producción en operación) | Num. 7.15 |
| `GetExchangeEmails` | Ninguno | Lista de correos de recepción en base64 | Num. 7.16 |
| `GetStatusEvent` | `trackId` (CUFE) | Eventos de la factura como título valor | Num. 7.17 |
| `GetReferenceNotes` | `trackId` (CUFE) | Notas crédito de la factura como título valor | Num. 7.18 |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Servicios de consulta DIAN: GetXmlByDocumentKey (descarga de XML por CUFE), GetNumberingRange (rangos de numeración y clave técnica), GetExchangeEmails (correos de recepción), GetStatusEvent (eventos de una factura) y GetReferenceNotes (notas crédito de una factura): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/22_manual_servicios_consulta_dian.md

<!-- Path: finanzas_y_contabilidad/11_manual_contenedor_electronico.md -->

# 📦 Manual Operativo 11: Generación de Contenedor Electrónico (Attached Document) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el usuario o el sistema automatizado necesite ensamblar el archivo XML final (Contenedor Electrónico) que agrupa un documento electrónico de origen (Factura, Nota Crédito o Débito) junto con su respectiva prueba de validación oficial (ApplicationResponse de la DIAN), para su entrega legal y distribución por correo electrónico al adquiriente.

---

# MANUAL DE GENERACIÓN: CONTENEDOR ELECTRÓNICO (ATTACHED DOCUMENT)

**ID del Manual:** DIAN-UBL2.1-M04  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (Extensible Markup Language)  
**Propósito:** Detallar el procedimiento algorítmico y técnico para la encapsulación de un documento electrónico firmado y su aval fiscal (respuesta de la DIAN) en un único archivo XML estructurado, garantizando el cumplimiento normativo para la entrega B2B/B2C de la factura electrónica en Colombia.

---

## 1. PROCEDIMIENTO GENERAL

La construcción del Contenedor Electrónico (`AttachedDocument`) no es un proceso de transmisión hacia la DIAN, sino un proceso de **distribución al adquiriente**. Consiste en orquestar el nodo raíz, identificar al emisor (`SenderParty`) y al receptor (`ReceiverParty`), e incrustar de forma segura (usando secciones `<![CDATA[ ... ]]>`) los dos archivos XML previamente validados: el documento comercial origen (ej. Factura Electrónica) y el mensaje de respuesta de la DIAN (`ApplicationResponse`). Todo esto estructurado bajo el estándar UBL 2.1. La estructura `AttachedDocument` tiene por objetivo transmitir en un único archivo XML un documento electrónico y todos los eventos registrados hasta la fecha; siempre que un documento es validado, el emisor debe transmitir al adquiriente el respectivo contenedor (Anexo 1.9, num. 6.4).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Documento Comercial Generado y Firmado:** Se debe tener el archivo XML original de la Factura, Nota Crédito o Nota Débito, con su firma digital (`ds:Signature`) y código identificador (CUFE/CUDE) intactos.
2. **Validación Exitosa DIAN:** Se debe haber consumido el Web Service de la DIAN y tener almacenado el archivo XML de respuesta (`ApplicationResponse`) donde el `ResponseCode` sea igual a `02` (Documento validado). Excepción: en contingencia tipo 04 (inconvenientes tecnológicos de la DIAN), la factura con `InvoiceTypeCode` `04` se incluye en el `AttachedDocument` sin `ApplicationResponse` de la DIAN y se entrega al adquiriente (Anexo 1.9, num. 12.2 y 12.3.1).
3. **Mecanismo de Envío:** Disponer del motor de correo electrónico (SMTP) configurado para enviar el `.zip` que contendrá este XML y el PDF de representación gráfica al cliente. El envío se hace al correo electrónico único de recepción del adquiriente; el PDF es opcional dentro del mismo `.zip` y el peso máximo por envío es de 2 Megas (Anexo 1.9, num. 9 y 9.1).
4. **Ausencia de Modificación:** Los XML a embeber no pueden sufrir ninguna alteración en su codificación ni contenido, ya que invalidarían la firma o el hash (CUFE/CUDE) validado.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Firmas:** El `AttachedDocument` **SÍ** debe llevar firma digital: requiere como mínimo una extensión `ext:UBLExtension` con `ds:Signature`, y solamente puede haber una ocurrencia de un grupo `UBLExtension` con `ds:Signature` (Anexo 1.9, num. 6.4, FAC01 a FAC03). La política de firma incluye el `AttachedDocument` en la ubicación de la firma (Anexo 1.9, num. 10.5.5). Los documentos embebidos conservan además su propia firma Xades-EPES.
- **Regla de Identificadores:** No se calcula un nuevo CUFE o CUDE para este contenedor; hereda la trazabilidad del documento principal a través de los nodos de referencia.
- **Regla de Inyección CDATA:** Para evitar corromper la estructura XML del contenedor, los documentos incrustados deben ir estrictamente envueltos en etiquetas `<![CDATA[ ... ]]>`.
- **Destino Exclusivo:** Este documento se genera exclusivamente para el buzón de recepción de facturación del cliente (Adquiriente), **nunca** se transmite de vuelta a los Web Services de la DIAN. El contenedor es un tipo de documento no validado por la DIAN (Anexo 1.9, num. 8.5).
- **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8`.
- **Regla de Fechas (AE05):** La fecha de generación del contenedor no puede ser anterior a ninguno de los documentos contenidos (`IssueDate` del contenedor >= `IssueDate` de los documentos contenidos).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<AttachedDocument>` con los *namespaces* correspondientes de UBL 2.1.

- **Ruta:** `/AttachedDocument`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Literal `Documentos adjuntos` (Anexo 1.9, AE03).
  * `cbc:ProfileID`: Literal `Factura Electrónica de Venta` (Anexo 1.9, AE04).
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas) (Anexo 1.9, AE04a).
  * `cbc:ID`: Consecutivo propio del generador del documento (Anexo 1.9, AE04b).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de generación de este contenedor (Anexo 1.9, AE05 y AE06).
  * `cbc:DocumentType`: Literal `Contenedor de Factura Electrónica` (Anexo 1.9, AE08).
  * `cbc:ParentDocumentID`: ID de la factura electrónica que origina el contenedor (Ej. `SETP990000000`) (Anexo 1.9, AE08a).

### Paso 4.2: Identificación del Emisor (`SenderParty`)
- **Ruta:** `/AttachedDocument/cac:SenderParty`
- **Nodos Obligatorios:**
  * `cac:PartyTaxScheme / cbc:CompanyID`: NIT del emisor (proveedor) con `@schemeAgencyID="195"`, el DV en `@schemeID` (si `@schemeName` = `31`) y el tipo de documento en `@schemeName` (Anexo 1.9, AE12 a AE15).
  * `cac:PartyTaxScheme`: también `cbc:RegistrationName` (nombre o razón social), `cbc:TaxLevelCode` y `cac:TaxScheme` con `cbc:ID` y `cbc:Name` (Anexo 1.9, AE10, AE11 y AE16 a AE20).
  * Debe coincidir con la información del `AccountingSupplierParty` de la factura original.

### Paso 4.3: Identificación del Adquiriente (`ReceiverParty`)
- **Ruta:** `/AttachedDocument/cac:ReceiverParty`
- **Nodos Obligatorios:**
  * `cac:PartyTaxScheme / cbc:CompanyID`: Identificación del cliente, ya sea NIT, Cédula o Consumidor Final, con `@schemeAgencyID="195"`, el DV en `@schemeID` y el tipo de documento en `@schemeName` (Anexo 1.9, AE24 a AE27).
  * `cac:PartyTaxScheme`: también `cbc:RegistrationName`, `cbc:TaxLevelCode` y `cac:TaxScheme` con `cbc:ID` y `cbc:Name` (Anexo 1.9, AE22, AE23 y AE28 a AE32).
  * Debe coincidir con la información del `AccountingCustomerParty` de la factura original.

### Paso 4.4: Documento Adjunto Principal (`Attachment` de la Factura/Nota)
En este paso se incrusta el documento comercial firmado (XML origen).

- **Ruta:** `/AttachedDocument/cac:Attachment/cac:ExternalReference`
- **Nodos Obligatorios:**
  * `cbc:MimeCode`: `text/xml`.
  * `cbc:EncodingCode`: `UTF-8`.
  * `cbc:Description`: Aquí se debe inyectar de forma íntegra el código XML de la factura, envuelto en una estructura CDATA.
    * *Ejemplo:* `<cbc:Description><![CDATA[ <?xml version="1.0" ... <Invoice>...</Invoice> ]]></cbc:Description>`

### Paso 4.5: Documento de Referencia DIAN (`ParentDocumentLineReference` / ApplicationResponse)
En este paso se incrusta la respuesta de validación oficial emitida por la DIAN.

- **Ruta:** `/AttachedDocument/cac:ParentDocumentLineReference/cac:DocumentReference/cac:Attachment/cac:ExternalReference`
- **Nodos del grupo `cac:ParentDocumentLineReference`** (puede referenciar uno o más documentos electrónicos) (Anexo 1.9, AE38 a AE45):
  * `cbc:LineID`: Consecutivo para informar el documento.
  * `cac:DocumentReference/cbc:ID`: Número del documento electrónico.
  * `cac:DocumentReference/cbc:UUID`: CUFE de la factura o CUDE de otro documento, con `@schemeName` `CUFE-SHA384` o `CUDE-SHA384`.
  * `cac:DocumentReference/cbc:IssueDate`: Fecha de generación de la respuesta.
  * `cac:DocumentReference/cbc:DocumentType`: Literal `ApplicationResponse`.
- **Nodos Obligatorios:**
  * `cbc:MimeCode`: `text/xml`.
  * `cbc:EncodingCode`: `UTF-8`.
  * `cbc:Description`: Aquí se inyecta de forma íntegra el código XML del `ApplicationResponse` recibido desde la DIAN, también envuelto en CDATA.
    * *Ejemplo:* `<cbc:Description><![CDATA[ <?xml version="1.0" ... <ApplicationResponse>...</ApplicationResponse> ]]></cbc:Description>`
- **Resultado de la validación (`cac:DocumentReference/cac:ResultOfVerification`)** (Anexo 1.9, AE51 a AE55):
  * `cbc:ValidatorID`: `Unidad Especial Dirección de Impuestos y Aduanas Nacionales`.
  * `cbc:ValidationResultCode`: Código del resultado de la validación (Ej. `02`).
  * `cbc:ValidationDate` y `cbc:ValidationTime`: Fecha y hora de la validación.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML del contenedor ha sido cerrado en memoria asegurando que ambos documentos (origen y validación) están correctamente incrustados en sus respectivos `CDATA`.
2. **Estado Empaquetado (Zipeado):** El archivo `AttachedDocument` se comprime en un formato `.zip` estándar, opcionalmente junto a su Representación Gráfica (PDF). En el mismo único `.zip` también se pueden incluir documentos complementarios, estos dentro de un archivo `.zip`. Los nombres de los archivos que se envían al adquiriente no los reglamenta la DIAN (acuerdo entre las partes) (Anexo 1.9, num. 9.1); la guía de nombres de archivos XML de la DIAN usa el prefijo `ad` para el Attached Document (Anexo 1.9, num. 6.7).
3. **Estado Distribuido:** El motor de comunicaciones despacha el correo electrónico hacia la dirección registrada del adquiriente, con el asunto `NIT del Facturador Electrónico;Nombre del Facturador Electrónico;Número del Documento Electrónico;Código del tipo de documento;Nombre comercial del facturador;Línea de negocio` (la línea de negocio es opcional) (Anexo 1.9, num. 9.1). Al recibir este contenedor, el adquiriente tiene las garantías legales para proceder con eventos del RADIAN o deducciones fiscales.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES)

- Si ocurre el error `XML Parsing Error / Malformed CDATA`, la solución es verificar que el documento XML embebido no contenga secuencias de cierre `]]>` dentro de sus propios valores comerciales que puedan romper el envoltorio CDATA del contenedor.
- Si el cliente reporta `Firma invalida al revisar el XML`, la solución es garantizar que al insertar el XML original dentro del `<cbc:Description>`, no se alteró la codificación (ej. de UTF-8 a ANSI), ni se removieron/agregaron espacios o saltos de línea al documento original previamente firmado.
- Si ocurre el error `ApplicationResponse ausente`, la solución es verificar la base de datos transaccional; el proceso del `AttachedDocument` **debe detenerse y esperar** hasta que la DIAN retorne un `ApplicationResponse` válido con `ResponseCode="02"`. No se puede entregar una factura sin su validación, salvo la factura con `InvoiceTypeCode` `04` expedida en contingencia por inconvenientes tecnológicos de la DIAN (Anexo 1.9, num. 12.2).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Estructura de Nodos de Adjuntos (Attachments)**
| Tipo de Documento Embebido | Ubicación en el UBL 2.1 | Formato Requerido | Condición DIAN |
| :--- | :--- | :--- | :--- |
| Factura Venta, NC, ND, Evento (Anexo 1.9, AE33) | `/AttachedDocument/cac:Attachment/cac:ExternalReference/cbc:Description` | Texto plano (XML) incrustado en `CDATA` | Obligatorio (Documento Base) |
| ApplicationResponse DIAN | `/AttachedDocument/cac:ParentDocumentLineReference/cac:DocumentReference/cac:Attachment/.../cbc:Description` | Texto plano (XML) incrustado en `CDATA` | Obligatorio (Prueba de Validación) |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Contenedor Electrónico (Attached Document) UBL 2.1, embebido de Factura y ApplicationResponse con CDATA para distribución B2B/B2C: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/11_manual_contenedor_electronico.md

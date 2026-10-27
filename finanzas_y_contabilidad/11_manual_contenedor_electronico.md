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

La construcción del Contenedor Electrónico (`AttachedDocument`) no es un proceso de transmisión hacia la DIAN, sino un proceso de **distribución al adquiriente**. Consiste en orquestar el nodo raíz, identificar al emisor (`SenderParty`) y al receptor (`ReceiverParty`), e incrustar de forma segura (usando secciones `<![CDATA[ ... ]]>`) los dos archivos XML previamente validados: el documento comercial origen (ej. Factura Electrónica) y el mensaje de respuesta de la DIAN (`ApplicationResponse`). Todo esto estructurado bajo el estándar UBL 2.1.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Documento Comercial Generado y Firmado:** Se debe tener el archivo XML original de la Factura, Nota Crédito o Nota Débito, con su firma digital (`ds:Signature`) y código identificador (CUFE/CUDE) intactos.
2. **Validación Exitosa DIAN:** Se debe haber consumido el Web Service de la DIAN y tener almacenado el archivo XML de respuesta (`ApplicationResponse`) donde el `ResponseCode` sea igual a `02` (Documento validado).
3. **Mecanismo de Envío:** Disponer del motor de correo electrónico (SMTP) configurado para enviar el `.zip` que contendrá este XML y el PDF de representación gráfica al cliente.
4. **Ausencia de Modificación:** Los XML a embeber no pueden sufrir ninguna alteración en su codificación ni contenido, ya que invalidarían la firma o el hash (CUFE/CUDE) validado.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Firmas:** El `AttachedDocument` **NO** requiere ser firmado digitalmente de nuevo. Su validez jurídica proviene de los documentos que lleva embebidos, los cuales ya poseen su propia firma Xades-EPES.
- **Regla de Identificadores:** No se calcula un nuevo CUFE o CUDE para este contenedor; hereda la trazabilidad del documento principal a través de los nodos de referencia.
- **Regla de Inyección CDATA:** Para evitar corromper la estructura XML del contenedor, los documentos incrustados deben ir estrictamente envueltos en etiquetas `<![CDATA[ ... ]]>`.
- **Destino Exclusivo:** Este documento se genera exclusivamente para el buzón de recepción de facturación del cliente (Adquiriente), **nunca** se transmite de vuelta a los Web Services de la DIAN.
- **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<AttachedDocument>` con los *namespaces* correspondientes de UBL 2.1.

- **Ruta:** `/AttachedDocument`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: El mismo del documento origen (Ej. `10` para Factura Estándar, `20` para Nota Crédito).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Contenedor de Factura Electrónica de Venta` (o el equivalente según el tipo de documento encapsulado).
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Debe ser exactamente el mismo número del documento comercial encapsulado (Ej. `SETP990000000`).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de generación de este contenedor.

### Paso 4.2: Identificación del Emisor (`SenderParty`)
- **Ruta:** `/AttachedDocument/cac:SenderParty`
- **Nodos Obligatorios:**
  * `cac:PartyTaxScheme / cbc:CompanyID`: NIT del emisor (proveedor) con `@schemeID="31"`.
  * Debe coincidir con la información del `AccountingSupplierParty` de la factura original.

### Paso 4.3: Identificación del Adquiriente (`ReceiverParty`)
- **Ruta:** `/AttachedDocument/cac:ReceiverParty`
- **Nodos Obligatorios:**
  * `cac:PartyTaxScheme / cbc:CompanyID`: Identificación del cliente, ya sea NIT, Cédula o Consumidor Final.
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
- **Nodos Obligatorios:**
  * `cbc:MimeCode`: `text/xml`.
  * `cbc:EncodingCode`: `UTF-8`.
  * `cbc:Description`: Aquí se inyecta de forma íntegra el código XML del `ApplicationResponse` recibido desde la DIAN, también envuelto en CDATA.
    * *Ejemplo:* `<cbc:Description><![CDATA[ <?xml version="1.0" ... <ApplicationResponse>...</ApplicationResponse> ]]></cbc:Description>`

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML del contenedor ha sido cerrado en memoria asegurando que ambos documentos (origen y validación) están correctamente incrustados en sus respectivos `CDATA`.
2. **Estado Empaquetado (Zipeado):** El archivo `AttachedDocument` se comprime en un formato `.zip` estándar, opcionalmente junto a su Representación Gráfica (PDF).
3. **Estado Distribuido:** El motor de comunicaciones despacha el correo electrónico hacia la dirección registrada del adquiriente. Al recibir este contenedor, el adquiriente tiene las garantías legales para proceder con eventos del RADIAN o deducciones fiscales.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES)

- Si ocurre el error `XML Parsing Error / Malformed CDATA`, la solución es verificar que el documento XML embebido no contenga secuencias de cierre `]]>` dentro de sus propios valores comerciales que puedan romper el envoltorio CDATA del contenedor.
- Si el cliente reporta `Firma invalida al revisar el XML`, la solución es garantizar que al insertar el XML original dentro del `<cbc:Description>`, no se alteró la codificación (ej. de UTF-8 a ANSI), ni se removieron/agregaron espacios o saltos de línea al documento original previamente firmado.
- Si ocurre el error `ApplicationResponse ausente`, la solución es verificar la base de datos transaccional; el proceso del `AttachedDocument` **debe detenerse y esperar** hasta que la DIAN retorne un `ApplicationResponse` válido con `ResponseCode="02"`. No se puede entregar una factura sin su validación.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Estructura de Nodos de Adjuntos (Attachments)**
| Tipo de Documento Embebido | Ubicación en el UBL 2.1 | Formato Requerido | Condición DIAN |
| :--- | :--- | :--- | :--- |
| Factura Venta, NC, ND | `/AttachedDocument/cac:Attachment/cac:ExternalReference/cbc:Description` | Texto plano (XML) incrustado en `CDATA` | Obligatorio (Documento Base) |
| ApplicationResponse DIAN | `/AttachedDocument/cac:ParentDocumentLineReference/cac:DocumentReference/cac:Attachment/.../cbc:Description` | Texto plano (XML) incrustado en `CDATA` | Obligatorio (Prueba de Validación) |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Contenedor Electrónico (Attached Document) UBL 2.1, embebido de Factura y ApplicationResponse con CDATA para distribución B2B/B2C: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/11_manual_contenedor_electronico.md

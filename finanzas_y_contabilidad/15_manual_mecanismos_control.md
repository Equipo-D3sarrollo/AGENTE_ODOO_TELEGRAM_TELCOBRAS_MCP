<!-- Path: finanzas_y_contabilidad/15_manual_mecanismos_control.md -->

# 🔒 Manual Operativo 15: Mecanismos de Control (CUFE, CUDE, Firma Digital y QR) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Especialista en Criptografía, Analista de Seguridad  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema necesite implementar, generar o auditar los mecanismos de seguridad criptográfica y autenticidad fiscal que respaldan los documentos electrónicos en Colombia: el Código Único de Factura Electrónica (CUFE), el Código Único de Documento Electrónico (CUDE), el Código de Seguridad del Software, la Firma Digital Xades-EPES y el Código QR.

---

# MANUAL DE MECANISMOS DE CONTROL (CUFE, CUDE, FIRMA DIGITAL Y QR)

**ID del Manual:** DIAN-UBL2.1-M08  
**Versión de UBL:** 2.1 / Algoritmos Criptográficos (SHA-384, Xades-EPES)  
**Formato de Salida:** Cadenas Hash (Hexadecimal), Nodos XML (`ds:Signature`) y URL de Código QR  
**Propósito:** Detallar la especificación matemática, algorítmica y técnica para la construcción de los identificadores únicos, el estampado de tiempo, el cifrado de firmas digitales y la codificación de código QR en cumplimiento estricto con el Anexo Técnico 1.9 de la DIAN[cite: 9, 10, 16, 20, 33, 153].

---

## 1. PROCEDIMIENTO GENERAL

Los mecanismos de control constituyen la capa de seguridad, integridad y no repudio de la facturación electrónica en Colombia[cite: 16, 33]. El procedimiento consiste en recopilar los metadatos comerciales y tributarios clave en un orden de concatenación vectorial estricto para generar la huella digital criptográfica HASH SHA-384 (CUFE para facturas o CUDE para notas y eventos)[cite: 16, 33, 153]. Posteriormente, se calcula el `SoftwareSecurityCode`, se estructura la URL del código QR y se somete el archivo XML canónico a un proceso de firma digital Xades-EPES mediante un certificado digital X.509 vigente[cite: 20].

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Biblioteca Criptográfica Habilitada:** Disponer de librerías en el motor backend para el cálculo de funciones Hash SHA-384, canonicalización XML (`http://www.w3.org/TR/2001/REC-xml-c14n-20010315`, "Canonical XML (omits comments)") y firma XMLDSig / Xades-EPES[cite: 20] (Anexo 1.9, num. 10.5.4; regla DC03).
2. **Certificado Digital Válido:** Tener instalado el certificado digital de firma (X.509 v3) emitido por una entidad de certificación abierta aprobada por la ONAC[cite: 20]. El certificado debe estar vigente y no revocado al momento de la firma, y tener los atributos de firma digital y no repudio (Anexo 1.9, num. 10.1, 10.5.6 y 10.5.11; regla ZD07).
3. **Parámetros de Software Configurados:** Tener cargados en el sistema el `SoftwareID`, el `SoftwarePIN` y la `Clave Técnica` proporcionada por la DIAN en el servicio de numeración. La clave técnica no está expuesta en el XML, y cuando se autoriza un rango nuevo la DIAN entrega una clave técnica diferente a la del rango anterior (Anexo 1.9, num. 11.1.6).
4. **Sincronización con la Hora Legal (Sellado de Tiempo):** El reloj del servidor debe estar sincronizado con la hora legal colombiana (UTC-05:00)[cite: 20], que determina el reloj de la Superintendencia de Industria y Comercio (http://horalegal.inm.gov.co/) (Anexo 1.9, num. 10.5.8).
5. **Inmutabilidad del XML:** Comprender que cualquier cambio en un solo caracter o espacio en blanco dentro del XML después de firmado anulará la firma digital y el CUFE/CUDE.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Algoritmo:** El único algoritmo autorizado para el cálculo del CUFE, CUDE y `SoftwareSecurityCode` es **SHA-384** (Secure Hash Algorithm, 384 bits)[cite: 16, 153].
- **Regla de Formato del Hash:** El resultado del SHA-384 debe expresarse obligatoriamente como una cadena en minúsculas de exactamente 96 caracteres hexadecimales[cite: 16, 153].
- **Regla de Concatenación de Valores:** Los importes monetarios utilizados dentro de la cadena fuente del CUFE/CUDE deben formatearse obligatoriamente con exactamente dos (2) decimales, utilizando el punto (`.`) como separador decimal y sin separadores de miles (ej. `10000.00`)[cite: 20]. Los decimales se toman **truncados** a dos dígitos, sin símbolo de pesos (Anexo 1.9, num. 11.1.2).
- **Regla de Firma Única:** En la estructura `/Invoice/ext:UBLExtensions` o equivalente, sólo debe existir una única extensión que contenga el nodo `ds:Signature`[cite: 23, 31].
- **Codificación:** Toda la cadena fuente debe ser procesada en codificación de texto `UTF-8` antes de ser introducida a la función HASH.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CÁLCULOS CRIPTOGRÁFICOS PASO A PASO)

### Paso 4.1: Cálculo Algorítmico del CUFE (Facturas de Venta)
El Código Único de Factura Electrónica (CUFE) es la huella digital que identifica de manera unívoca a una factura de venta[cite: 16, 33].

- **Ruta XML:** `/Invoice/cbc:UUID` (Atributo `@schemeName="CUFE-SHA384"`; el atributo `@schemeID` indica el ambiente)[cite: 16, 33] (Anexo 1.9, FAD07 y FAD08)
- **Orden Estricto de Concatenación de Cadenas:**
  `CUFE = SHA-384( NumFac + FecFac + HorFac + ValFac + CodImp1 + ValImp1 + CodImp2 + ValImp2 + CodImp3 + ValImp3 + ValTot + NitOFE + NumAdq + ClaveTecnica + TipoAmb )`
- **Componentes:**
  1. `NumFac`: Número de factura con prefijo (Ej. `SETP990000001`).
  2. `FecFac`: Fecha en formato `YYYY-MM-DD`.
  3. `HorFac`: Hora en formato `HH:MM:SS-05:00`.
  4. `ValFac`: Subtotal de la factura (`LineExtensionAmount`) formateado a 2 decimales.
  5. `CodImp1` + `ValImp1`: Código de impuesto (Ej. `01` para IVA) y su valor (`TaxAmount`). Se repite para INC (`04`) e ICA (`03`). Los códigos `01`, `04` y `03` son fijos y siempre se incluyen; si el impuesto no está referenciado, su valor se representa con `0.00` (Anexo 1.9, num. 11.1.2).
  6. `ValTot`: Total a pagar (`PayableAmount`).
  7. `NitOFE`: NIT del emisor (sin dígito de verificación).
  8. `NumAdq`: Número de identificación del cliente (sin DV).
  9. `ClaveTecnica`: Clave asignada por la DIAN a la resolución.
  10. `TipoAmb`: `1` (Producción) o `2` (Pruebas), tomado de `/Invoice/cbc:ProfileExecutionID`.
- **Ejemplo del anexo (num. 11.1.2):** cadena `3232000001292019-01-1610:53:10-05:001500000.0001285000.00040.00030.001785000.00700085371800199436693ff6f2a553c3646a063436fd4dd9ded03114711`.

### Paso 4.2: Cálculo Algorítmico del CUDE (Notas y Eventos)
El Código Único de Documento Electrónico (CUDE) se utiliza para Notas Crédito, Notas Débito, Documento Soporte y Registro de Eventos[cite: 16, 153]. En el Anexo 1.9 de factura electrónica de venta, el CUDE se especifica para: documento electrónico de transmisión (contingencia tipo 03), notas crédito, notas débito y ApplicationResponse (Anexo 1.9, num. 11.1.3); el documento soporte no está regulado en este anexo.

- **Ruta XML:** `/CreditNote/cbc:UUID` o `/DebitNote/cbc:UUID` (Atributo `@schemeName="CUDE-SHA384"`)[cite: 16, 153] (Anexo 1.9, CAD08 y DAD08)
- **Orden Estricto de Concatenación de Cadenas:**
  `CUDE = SHA-384( NumDoc + FecDoc + HorDoc + ValDoc + CodImp1 + ValImp1 + ... + ValTot + NitEmisor + NumAdq + PINSoftware + TipoAmb )`
- **Diferencia Clave:** Sustituye la `ClaveTecnica` por el `PINSoftware` asignado en el portal DIAN. La transcripción de una factura de contingencia tipo 03 (`InvoiceTypeCode` = `03`) también usa el PIN del software, porque a ese rango no se le asigna clave técnica (Anexo 1.9, num. 11.1.4.1).
- **CUDE del Evento (ApplicationResponse):** Tiene una fórmula propia: `CUDE = SHA-384( Num_DE + Fec_Emi + Hor_Emi + NitFE + DocAdq + ResponseCode + ID + DocumentTypeCode + Software-PIN )`, donde `NitFE` es el documento de quien genera el evento, `DocAdq` el de quien lo recibe, `ID` el prefijo y número del documento referenciado y `DocumentTypeCode` su tipo de documento (Anexo 1.9, num. 11.1.5).

### Paso 4.3: Código de Seguridad del Software (`SoftwareSecurityCode`)
Identificador que valida la procedencia del software emisor autorizado.

- **Ruta XML:** `/Invoice/ext:UBLExtensions/.../sts:DianExtensions/sts:SoftwareSecurityCode`
- **Fórmula:**
  `SoftwareSecurityCode = SHA-384( SoftwareID + SoftwarePIN + NumDoc )`
- **NumDoc:** `cbc:ID` del Invoice, DebitNote, CreditNote o ApplicationResponse (Anexo 1.9, num. 11.1.8).

### Paso 4.4: Estructuración y Generación del Código QR
Cadena de texto estructurada que se encapsula en el XML y se procesa para renderizar la matriz de puntos del código QR en las representaciones gráficas (PDF).

- **Ruta XML:** `/Invoice/ext:UBLExtensions/.../sts:DianExtensions/sts:QRCode`
- **Estructura de la URL Contenida:**
  `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=` + `CUFE_o_CUDE` (ambiente de producción). En ambiente de habilitación: `https://catalogo-vpfe-hab.dian.gov.co/document/searchqr?documentkey=` + `CUFE_o_CUDE` (Anexo 1.9, num. 11.1.7.1).
- **Atributos adicionales en la representación QR:** Contiene los metadatos clave `NumFac`, `FecFac`, `HorFac`, `NitFac`, `DocAdq`, `ValFac`, `ValIva`, `ValOtroIm`, `ValTolFac`, `CUFE` y la URL del `QRCode`. Los NIT sin puntos ni guiones, y los valores con punto decimal, dos decimales, sin separadores de miles ni símbolo de pesos (Anexo 1.9, num. 11.1.7).
- **Representación gráfica:** El código QR debe tener un tamaño mínimo de 2 cm e ir en todas las páginas de la representación gráfica, digital o impresa (Anexo 1.9, num. 11.1.7).

### Paso 4.5: Implementación de la Firma Digital Xades-EPES
Procedimiento para estampar la firma electrónica sobre el documento canónico XML[cite: 20].

- **Ruta XML:** `/Invoice/ext:UBLExtensions/ext:UBLExtension/ext:ExtensionContent/ds:Signature`[cite: 22, 23]
- **Pasos Criptográficos:**
  1. **Canonicalización:** Aplicar el algoritmo `http://www.w3.org/TR/2001/REC-xml-c14n-20010315` sobre el elemento `SignedInfo` (Anexo 1.9, num. 10.5.4; regla DC03).
  2. **DigestValue:** Calcular el SHA-256 / SHA-384 del documento canónico para verificar la integridad. `SignedInfo` debe contener tres (3) grupos `Reference`: el documento completo (`URI=""`, con la transformación `enveloped-signature`), el `KeyInfo` y las `SignedProperties` (Anexo 1.9, num. 10.5.6; reglas DC02, DC05, DC07, DC10 y DC13).
  3. **SignedProperties:** Incluir las propiedades firmadas de Xades-EPES: momento de la firma (`SigningTime`), certificado de firma (`SigningCertificate`) y la política de firma de la DIAN (`SignaturePolicyIdentifier`). La política se identifica con `https://facturaelectronica.dian.gov.co/politicadefirma/v2/politicadefirmav2.pdf`, su hash (`SigPolicyHash`) con `sha256` o `sha512`, y la descripción "Política de firma para facturas electrónicas de la República de Colombia". El elemento `xades:SignerRole` contiene `supplier` cuando firma el obligado a facturar o `third party` cuando firma un proveedor tecnológico (Anexo 1.9, num. 10.5.7 a 10.5.9).
  4. **Cifrado Asimétrico:** Cifrar el valor Digest utilizando la llave privada del certificado digital (.pfx / .p12). El algoritmo de firma recomendado es RSAwithSHA256, RSAwithSHA384 o RSAwithSHA512 (Anexo 1.9, num. 10.5.3; regla DC04).
  5. **Inyección:** Inyectar el nodo `<ds:Signature>` completo dentro de una extensión UBL (la factura debe tener como mínimo dos: `sts:DianExtensions` y `ds:Signature`) (Anexo 1.9, FAC01). El certificado público se incluye en base64 en `ds:KeyInfo/ds:X509Data/ds:X509Certificate`, con toda la cadena de certificación (desde el certificado raíz) en `ds:X509Data` y `ds:Object` (Anexo 1.9, num. 10.5.2 y 10.5.6).
  6. **Formato:** Se usa XMLDSig enveloped con formato XAdES-EPES (ETSI TS 101 903 v1.2.2, v1.3.2 y v1.4.1), indicando la versión adoptada en las etiquetas XML (Anexo 1.9, num. 10.5.2).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Documento Blindado:** El archivo XML posee un CUFE/CUDE válido de 96 caracteres de longitud en minúsculas y un nodo de firma `ds:Signature` intacto[cite: 16, 20, 153].
2. **Inmutabilidad:** El archivo firmado digitalmente entra en estado "Bloqueado para edición". Si un usuario intenta modificar una sola letra, la firma se romperá al ser validada por la DIAN.
3. **Apto para Transmisión:** El documento cuenta con los metadatos criptográficos requeridos para ser procesado exitosamente por el *Manual de Transmisión a la DIAN*.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `CUFE Invalido / Error de Cálculo`, la solución es revisar la cadena fuente concatenada. El error más común es enviar importes monetarios con más o menos decimales (deben ser estrictamente 2), incluir el dígito de verificación en el NIT, o utilizar espacios en blanco antes o después de los valores.
- Si ocurre el error `SignatureValidationException / Firma Invalida` (reglas `DC01` a `DC56` o `ZE01`/`ZE02`), la solución es verificar que la librería de firma esté aplicando la canonicalización adecuada (`http://www.w3.org/TR/2001/REC-xml-c14n-20010315`) y que no se esté alterando el formato de codificación de caracteres de `UTF-8` a `Windows-1252` o `ISO-8859-1`.
- Si ocurre el error `Certificado Vencido o No Confiable`, la solución es actualizar el archivo del certificado digital (`.pfx`) en el servidor backend e importar la cadena de certificación de la entidad emisora (CA). La fecha de firma (`SigningTime`) debe estar dentro del lapso de vigencia del certificado, y el NIT del emisor debe corresponder al propietario del certificado digital (Anexo 1.9, num. 10.5.11; reglas ZD01 a ZD07 y ZE03).
- Si ocurre el error `SoftwareSecurityCode Incorregible`, la solución es verificar que el `SoftwarePIN` ingresado corresponda al entorno activo (Pruebas o Producción) en la plataforma de la DIAN.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Mecanismos Criptográficos y Especificaciones Téxcnicas DIAN**
| Mecanismo | Algoritmo Criptográfico | Longitud / Salida | Ubicación en el XML UBL 2.1 |
| :--- | :--- | :--- | :--- |
| **CUFE** | SHA-384 | 96 hex minúsculas | `/Invoice/cbc:UUID`[cite: 16, 33] |
| **CUDE** | SHA-384 | 96 hex minúsculas | `/CreditNote/cbc:UUID`, `/DebitNote/cbc:UUID` o `/ApplicationResponse/cbc:UUID`[cite: 16, 153] |
| **SoftwareSecurityCode** | SHA-384 | 96 hex minúsculas | `/Invoice/.../sts:SoftwareSecurityCode` |
| **Firma Digital** | RSA / Xades-EPES | Cifrado asimétrico | `/Invoice/ext:UBLExtensions/.../ds:Signature`[cite: 20, 22] |
| **Código QR** | URL + Token HASH | Cadena URL Texto | `/Invoice/.../sts:DianExtensions/sts:QRCode` |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Mecanismos de Control DIAN, Cálculos de CUFE y CUDE SHA-384, Firma Digital Xades-EPES, Código QR y SoftwareSecurityCode: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/15_manual_mecanismos_control.md

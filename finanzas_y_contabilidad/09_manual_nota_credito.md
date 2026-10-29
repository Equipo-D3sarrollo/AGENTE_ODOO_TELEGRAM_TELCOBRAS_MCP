<!-- Path: finanzas_y_contabilidad/09_manual_nota_credito.md -->

# 💳 Manual Operativo 09: Generación de Nota Crédito (CreditNote) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el usuario o el sistema automatizado necesite construir, validar y empaquetar algorítmicamente el archivo XML correspondiente a una Nota Crédito Electrónica, garantizando la interoperabilidad técnica y fiscal con los Web Services de la DIAN para anular, corregir o ajustar saldos de facturas electrónicas de venta previamente transmitidas y aceptadas.

---

# MANUAL DE GENERACIÓN: NOTA CRÉDITO (CREDITNOTE)

**ID del Manual:** DIAN-UBL2.1-M02  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (Extensible Markup Language)  
**Propósito:** Detallar el procedimiento algorítmico, técnico y normativo para la construcción, validación y ensamblaje de la Nota Crédito Electrónica, garantizando la interoperabilidad con la DIAN y la correcta interpretación tanto humana como de sistemas automatizados (IA)[cite: 148, 152, 154].

---

## 1. PROCEDIMIENTO GENERAL

La construcción de una Nota Crédito Electrónica (`CreditNote`) se divide en la orquestación secuencial del nodo raíz, la declaración del tipo de operación y concepto de corrección (`DiscrepancyResponse`), la vinculación obligatoria a la factura electrónica de origen (`BillingReference`), las extensiones de control DIAN (firma digital y proveedor tecnológico), la identificación de participantes (emisor/receptor), el desglose de ítems ajustados o devueltos (`CreditNoteLine`), la recalculación tributaria y el cierre aritmético de totales monetarios bajo el estándar internacional UBL 2.1 y el Anexo Técnico 1.9 (Resolución 165 de 2023)[cite: 148, 153, 154, 155].

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura de Origen Aceptada:** La factura electrónica de venta objeto de ajuste debe haber sido transmitida previamente y contar con estado "Aceptada" (ApplicationResponse `02`) en la plataforma DIAN[cite: 13, 148, 154]. Si el adquiriente ya aceptó la factura de manera expresa o tácita (eventos `033` o `034`), la DIAN indica que no es posible la transmisión de una nota crédito por cuanto la factura ya se encuentra aceptada (Anexo 1.9, regla LGC15); en ese caso se usa la nota de ajuste para factura aceptada, `CustomizationID` `24` (Anexo 1.9, num. 14.3).
2. **Disponibilidad del CUFE Original:** Se debe contar con el CUFE exacto de la factura afectada y su fecha de expedición para establecer la trazabilidad jurídica[cite: 154, 155].
3. **Sincronización con la hora legal:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo y la firma digital (Xades-EPES)[cite: 20]. La hora legal la determina el reloj de la Superintendencia de Industria y Comercio (http://horalegal.inm.gov.co/) (Anexo 1.9, num. 10.5.8).
4. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación digital abierta autorizada por la ONAC[cite: 20]; un documento firmado que no cumpla esta condición se entiende inválido (Anexo 1.9, num. 10.1).
5. **Algoritmo CUDE Configurado:** Disponer del PIN de software para el cálculo de la huella digital hash CUDE mediante el algoritmo SHA-384[cite: 16, 153]. El CUDE de la nota crédito usa el `Software-PIN` registrado en el catálogo del participante, no la clave técnica (Anexo 1.9, num. 11.1.4.3).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Perfil (CAD03):** El elemento `ProfileID` debe indicar exactamente el literal "DIAN 2.1: Nota Crédito de Factura Electrónica de Venta" o "DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada" según el código de operación (`CustomizationID`) utilizado[cite: 153].
- **Regla de Identificadores (CUDE):** A diferencia de la factura de venta (que utiliza CUFE), la Nota Crédito genera y transmite obligatoriamente el Código Único de Documento Electrónico (CUDE) calculado mediante la función Hash SHA-384[cite: 16, 153].
- **Regla de Coherencia Temporal (CBG06):** La fecha de emisión de la Nota Crédito (`CreditNote/cbc:IssueDate`) no puede ser anterior a la fecha de emisión de la factura electrónica referenciada[cite: 155]. Además, la fecha de generación de la nota debe ser igual a su fecha de firma (Anexo 1.9, regla CAD09e).
- **Regla de Discrepancia Obligatoria (CBF01-CBF04):** Si se ajusta o anula una factura, es obligatorio declarar el grupo `cac:DiscrepancyResponse` indicando el código de respuesta (`cbc:ResponseCode`) y la justificación textual del ajuste[cite: 154, 155]. El grupo es obligatorio cuando `CustomizationID` es `20`, y la descripción debe corresponder al código informado (Anexo 1.9, CBF01 y CBF04).
- **Regla de Anulación (CAD02a, CBF03a):** Si el concepto es `2` (anulación), el `CustomizationID` debe ser `20`; una nota tipo `22` no puede anular facturas (Anexo 1.9, reglas CAD02a y CBF03a).
- **Regla de Signos (VLR01):** Todos los valores monetarios de ajuste o devolución deben expresarse en valores positivos[cite: 21]. El único campo que admite signo negativo es el ajuste por redondeo (`PayableRoundingAmount`)[cite: 21].
- **Codificación:** El documento XML debe estar estructurado en codificación estandarizada `UTF-8` (Anexo 1.9, regla ZB03).
- **Contingencia:** La nota crédito no tiene esquema de contingencia; se genera y transmite cuando los servicios de la DIAN funcionen normalmente (Anexo 1.9, num. 12.1, 12.2 y 12.3.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
Declara el elemento raíz `<CreditNote>` incorporando los espacios de nombres (*namespaces*) estándar de UBL 2.1 y la DIAN[cite: 152].

- **Ruta:** `/CreditNote`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`[cite: 152].
  * `cbc:CustomizationID`: `20` (Nota Crédito que referencia Factura) o `24` (Nota de Ajuste para Factura Aceptada)[cite: 153]. El valor por defecto es `20`; el tipo `22` se usa para notas crédito sin referencia a una factura, informando el periodo que se afecta en `cac:InvoicePeriod` (Anexo 1.9, num. 14.3; CAE01).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Nota Crédito de Factura Electrónica de Venta` o `DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada`[cite: 153].
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas)[cite: 153].
  * `cbc:ID`: Prefijo y número consecutivo asignado a la nota crédito (Ej. `NC990000001`), sin espacios ni guiones (Anexo 1.9, CAD05).
  * `cbc:UUID`: CUDE de 96 caracteres, calculado con SHA-384. El algoritmo se indica en el atributo `@schemeName="CUDE-SHA384"` y el ambiente de destino en `@schemeID` (`1` Producción, `2` Pruebas)[cite: 16, 153] (Anexo 1.9, CAD06, CAD07 y CAD08).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición de la nota[cite: 155], con la hora en zona horaria -5 (Anexo 1.9, CAD09 y CAD10).
  * `cbc:CreditNoteTypeCode`: Código del tipo de nota (Fijo en `91` para Nota Crédito). La lista de valores está en la Caja de Herramientas (Anexo 1.9, CAD12).
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción (Ej. `COP`).
  * `cbc:LineCountNumeric`: Número de líneas; debe coincidir con el número de grupos `cac:CreditNoteLine` (Anexo 1.9, CAD16).

### Paso 4.2: Referencia al Documento de Origen y Discrepancia (`DiscrepancyResponse` y `BillingReference`)
Paso exclusivo que vincula jurídicamente la Nota Crédito a la factura afectada y justifica la razón comercial del ajuste[cite: 154, 155].

- **Ruta:** `/CreditNote/cac:DiscrepancyResponse` y `/CreditNote/cac:BillingReference`
- **Procedimiento:**
  1. **Grupo `DiscrepancyResponse`:**
     * `cbc:ReferenceID`: Identifica la sección de la factura original a la cual se aplica la corrección (Anexo 1.9, CBF02).
     * `cbc:ResponseCode`: Código de concepto del ajuste según la tabla oficial (Ej. `1` Devolución parcial, `2` Anulación de factura, `3` Rebaja/descuento)[cite: 154]. La tabla 13.2.4 está en la Caja de Herramientas (Anexo 1.9, CBF03).
     * `cbc:Description`: Explicación detallada del motivo de la nota[cite: 154, 155]. Debe corresponder al código informado en `cbc:ResponseCode` (Anexo 1.9, CBF04).
  2. **Grupo `BillingReference / cac:InvoiceDocumentReference`:**
     * `cbc:ID`: Número de la factura afectada[cite: 155].
     * `cbc:UUID`: CUFE de la factura afectada, especificando el atributo `@schemeName="CUFE-SHA384"`[cite: 155].
     * `cbc:IssueDate`: Fecha original de emisión de la factura[cite: 155].
     * Solo se pueden referenciar facturas electrónicas de un mismo adquiriente. Si `CustomizationID` es `20`, el grupo debe aparecer una vez; si es `22`, no se valida (Anexo 1.9, CBG01).

### Paso 4.3: Construcción de Extensiones DIAN (`UBLExtensions`)
Estructura reservada para alojar los metadatos de control fiscal, el código QR y la firma digital.

- **Ruta:** `/CreditNote/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):**
     * `sts:InvoiceSource`: `cbc:IdentificationCode` con el literal `CO` y atributos `@listAgencyID="6"` y `@listSchemeURI` (Anexo 1.9, CAB13 a CAB17).
     * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT o Emisor) y `sts:softwareID`, con `@schemeAgencyID="195"` (Anexo 1.9, CAB18 a CAB26).
     * `sts:SoftwareSecurityCode`: SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Nota Crédito) (Anexo 1.9, num. 11.1.8).
     * `sts:AuthorizationProvider`: NIT de la DIAN en `sts:AuthorizationProviderID` (Anexo 1.9, CAB30 a CAB35).
     * La nota crédito no lleva grupo `sts:InvoiceControl` (resolución de numeración).
  2. **Generación de Código QR (`sts:QRCode`):** El elemento debe contener la URL `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE`, donde la palabra CUFE se reemplaza por el CUFE o CUDE del documento referenciado en `//cac:BillingReference/cac:InvoiceDocumentReference/cbc:UUID` (Anexo 1.9, CAB36). El QR de la representación gráfica lleva: número, fecha, hora, NIT del facturador, documento del adquiriente, valor, IVA, otros impuestos, valor total y CUFE/CUDE (Anexo 1.9, num. 11.1.7).
  3. **Firma Digital (`ds:Signature`):** Inyección del bloque de firma Xades-EPES sobre el archivo XML canónico[cite: 20]. Debe haber como mínimo dos `UBLExtension` (`sts:DianExtensions` y `ds:Signature`) y una sola firma (Anexo 1.9, CAC01 a CAC03).

### Paso 4.4: Identificación del Emisor (`AccountingSupplierParty`)
- **Ruta:** `/CreditNote/cac:AccountingSupplierParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos fiscales del emisor registrados en la factura original: Razón Social (`cbc:RegistrationName`), NIT (`cbc:CompanyID` con `schemeName="31"`), Dígito de Verificación (en `@schemeID`; debe ser informado y correcto, reglas CAJ24a, CAJ24b y CAJ25), responsabilidades fiscales (`cbc:TaxLevelCode`), régimen y ubicación territorial.

### Paso 4.5: Identificación del Adquiriente (`AccountingCustomerParty`)
- **Ruta:** `/CreditNote/cac:AccountingCustomerParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos del cliente registrado en la factura original sobre la cual se emite el ajuste. Si fue Consumidor Final, se mantiene `222222222222` con `schemeName="13"` (Anexo 1.9, CAK21 y CAK25). Si el adquiriente se identifica con NIT, el DV va en `@schemeID` y debe ser correcto (Anexo 1.9, CAK24 y CAK25).

### Paso 4.6: Líneas de Detalle de la Nota Crédito (`CreditNoteLine`) - ITERATIVO
Define los productos o servicios que sufren devolución, anulación o ajuste de valor[cite: 122].

- **Ruta:** `/CreditNote/cac:CreditNoteLine`
- **Nodos Obligatorios:**
  * `cbc:ID`: Número secuencial de la línea (1, 2, 3...).
  * `cbc:CreditedQuantity`: Cantidad devuelta o ajustada (mayor a `0.00`) con su atributo de unidad de medida `@unitCode`[cite: 21, 123] (Anexo 1.9, CAV04a, CAV04b y CAV05).
  * `cbc:LineExtensionAmount`: Subtotal ajustado de la línea = (Cantidad * Precio) - Descuentos + Cargos (Anexo 1.9, CAV06).
  * `cac:Item / cbc:Description`: Descripción del producto/servicio y código estándar.
  * `cac:Price / cbc:PriceAmount`: Precio unitario base.
  * `cac:TaxTotal`: Impuestos específicos devueltos o recalculados en la línea[cite: 101, 111]. Solo un `TaxTotal` por tributo en la línea (Anexo 1.9, CAX01); para tributos en porcentaje, `TaxAmount` = `TaxableAmount` * `Percent` (Anexo 1.9, CAX07).

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
- **Ruta:** `/CreditNote/cac:TaxTotal`
- **Regla Estricta:** Reagrupa el IVA, INC o retenciones proporcionales al monto devuelto o anulado[cite: 101, 111]. El monto global reportado debe corresponder exactamente a la sumatoria de los impuestos desglosados en las líneas de detalle[cite: 102, 113]. Debe existir un `TaxTotal` de cabecera por cada tipo de impuesto informado en las líneas; su `TaxAmount` es la suma de sus `TaxSubtotal` y cada `TaxSubtotal/cbc:TaxAmount` es `TaxableAmount` * `Percent` (Anexo 1.9, CAS01b, CAS02 y CAS07). Las retenciones se informan en `cac:WithholdingTaxTotal`.

### Paso 4.8: Totales Monetarios Legales (`LegalMonetaryTotal`)
El cierre aritmético de la Nota Crédito.

- **Ruta:** `/CreditNote/cac:LegalMonetaryTotal`
- **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los subtotales de línea ajustados (Anexo 1.9, CAU02).
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible ajustada, suma de las bases imponibles de las líneas (Anexo 1.9, CAU04).
  3. `cbc:TaxInclusiveAmount`: Valor bruto (`LineExtensionAmount`) + Impuestos recalculados de los `TaxTotal` de cabecera (Anexo 1.9, CAU06).
  4. `cbc:AllowanceTotalAmount`: Suma de descuentos globales.
  5. `cbc:ChargeTotalAmount`: Suma de recargos globales.
  6. `cbc:PayableAmount`: TOTAL DEL AJUSTE / CRÉDITO = `TaxInclusiveAmount` - Descuentos Globales + Recargos Globales (Anexo 1.9, CAU14).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML de la Nota Crédito ha sido construido en memoria con su respectivo CUDE y enlace `BillingReference` a la factura original[cite: 153, 155].
2. **Estado Firmado:** La firma digital Xades-EPES ha sido estampada sobre el documento canónico en el nodo `ds:Signature`[cite: 20].
3. **Estado Empaquetado:** El archivo XML se comprime en un contenedor `.zip` listo para su transmisión a los Web Services de la DIAN. El XML se nombra con el prefijo `nc` + NIT del facturador sin DV (10 dígitos) + código `ppp` + 2 últimos dígitos del año + consecutivo de 8 dígitos hexadecimales (ej. `nc08001972680001900000001.xml`), y el ZIP con el mismo patrón iniciando en `z` (Anexo 1.9, num. 6.7 y 6.8).
4. **Respuesta Esperada:** Tras la transmisión, la DIAN devuelve un `ApplicationResponse` aceptando la nota (ResponseCode `02`), vinculando irrevocablemente el saldo ajustado a la factura electrónica original[cite: 13, 15]. Si la nota es rechazada, el `ResponseCode` es `04` (Anexo 1.9, num. 6.5.3).

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `CAD03 - ProfileID no corresponde`, la solución es verificar que el texto del campo `ProfileID` sea exactamente "DIAN 2.1: Nota Crédito de Factura Electrónica de Venta" o "DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada" respetando mayúsculas y acentos[cite: 153].
- Si ocurre el error `CBG01 / CBG04 / CBG06 - BillingReference Invalido o Fecha Posterior`, la solución es verificar que el CUFE y la fecha reportados en `cac:BillingReference` coincidan exactamente con la factura registrada en la DIAN, y que la fecha de la Nota Crédito no sea anterior a la de la factura[cite: 155].
- Si ocurre el error `CAD09e` (la fecha de generación de la NC es diferente a la fecha de firma), la solución es generar y firmar la nota con la misma fecha de `cbc:IssueDate`.
- Si ocurre el error `LGC15` (la factura ya se encuentra aceptada), la solución es emitir la nota de ajuste para factura aceptada (`CustomizationID` `24`) (Anexo 1.9, num. 14.3).
- Si ocurre el error `CBF01 - DiscrepancyResponse Ausente`, la solución es incluir obligatoriamente el grupo `cac:DiscrepancyResponse` con el código de concepto de ajuste válido y su descripción explicativa[cite: 154, 155].
- Si ocurre el error `CUDE Invalido / Algoritmo incorrecto` (regla `CAD06`), la solución es revisar la concatenación para la generación del hash SHA-384, verificando que incluya los parámetros de la Nota Crédito en el orden exacto especificado en el Anexo Técnico[cite: 16, 153]: `NumFac` + `FecFac` + `HorFac` + `ValFac` + `01` + `ValImp1` + `04` + `ValImp2` + `03` + `ValImp3` + `ValTot` + `NitOFE` + `NumAdq` + `Software-PIN` + `TipoAmbiente`, con valores de dos decimales truncados, `0.00` para impuestos no referenciados y NIT sin DV (Anexo 1.9, num. 11.1.4.3).
- Si ocurre el error `Diferencia Aritmética en Impuestos` (reglas `CAS02`, `CAS07`, `CAX07` o `CAU06`), la solución es verificar que la devolución de tributos en `cac:TaxTotal` sea la sumatoria exacta (respetando la tolerancia de $2.00) de lo devuelto en cada `CreditNoteLine`[cite: 102, 113].

---

## 7. Tablas de Referencia y Anexos Técnicos

**Conceptos de Corrección / Discrepancia para Nota Crédito (ResponseCode)**

*Nota: la tabla oficial 13.2.4 está en la Caja de Herramientas de la DIAN y no en el PDF del Anexo 1.9; del PDF solo se confirma que el código `2` corresponde a anulación (regla CAD02a). Los códigos `5` y `6` de esta tabla difieren de los conceptos del manual 05 (descuento por pronto pago y descuento por volumen); deben verificarse contra la tabla oficial.*

| Código (ResponseCode) | Concepto del Ajuste | Descripción / Aplicación |
| :--- | :--- | :--- |
| **1** | Devolución parcial de los bienes / no aceptación de partes | Aplica cuando el cliente devuelve parte de las mercancías o rechaza un ítem específico[cite: 154]. |
| **2** | Anulación de factura electrónica | Anula la totalidad de los efectos comerciales y fiscales de la factura referenciada[cite: 154]. |
| **3** | Rebaja o descuento parcial o total | Otorga un descuento comercial posterior a la emisión de la factura de venta[cite: 154]. |
| **4** | Ajuste de precio | Modifica el valor unitario o total cobrado previamente por error en la tarifa[cite: 154]. |
| **5** | Otros | Correcciones administrativas generales no tipificadas en los otros códigos[cite: 154]. |
| **6** | Rescisión o resolución del contrato | Termina formalmente el contrato que dio origen a la factura de venta[cite: 154]. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Nota Crédito Electrónica (CreditNote) UBL 2.1, cálculos CUDE, referencia a factura de origen (BillingReference) y conceptos de discrepancia: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/09_manual_nota_credito.md

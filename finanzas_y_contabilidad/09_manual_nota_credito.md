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

La construcción de una Nota Crédito Electrónica (`CreditNote`) se divide en la orquestación secuencial del nodo raíz, la declaración del tipo de operación y concepto de corrección (`DiscrepancyResponse`), la vinculación obligatoria a la factura electrónica de origen (`BillingReference`), las extensiones de control DIAN (firma digital y proveedor tecnológico), la identificación de participantes (emisor/receptor), el desglose de ítems ajustados o devueltos (`CreditNoteLine`), la recalculación tributaria y el cierre aritmético de totales monetarios bajo el estándar internacional UBL 2.1 y el Anexo Técnico 1.8 (Resolución 165 de 2023)[cite: 148, 153, 154, 155].

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura de Origen Aceptada:** La factura electrónica de venta objeto de ajuste debe haber sido transmitida previamente y contar con estado "Aceptada" (ApplicationResponse `02`) en la plataforma DIAN[cite: 13, 148, 154].
2. **Disponibilidad del CUFE Original:** Se debe contar con el CUFE exacto de la factura afectada y su fecha de expedición para establecer la trazabilidad jurídica[cite: 154, 155].
3. **Sincronización NTP:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo y la firma digital (Xades-EPES)[cite: 20].
4. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación abierta (ONAC u homóloga)[cite: 20].
5. **Algoritmo CUDE Configurado:** Disponer de la Clave Técnica o PIN de software para el cálculo de la huella digital hash CUDE mediante el algoritmo SHA-384[cite: 16, 153].

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Perfil (CAD03):** El elemento `ProfileID` debe indicar exactamente el literal "DIAN 2.1: Nota Crédito de Factura Electrónica de Venta" o "DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada" según el código de operación (`CustomizationID`) utilizado[cite: 153].
- **Regla de Identificadores (CUDE):** A diferencia de la factura de venta (que utiliza CUFE), la Nota Crédito genera y transmite obligatoriamente el Código Único de Documento Electrónico (CUDE) calculado mediante la función Hash SHA-384[cite: 16, 153].
- **Regla de Coherencia Temporal (CBG06):** La fecha de emisión de la Nota Crédito (`CreditNote/cbc:IssueDate`) no puede ser anterior a la fecha de emisión de la factura electrónica referenciada[cite: 155].
- **Regla de Discrepancia Obligatoria (CBF01-CBF04):** Si se ajusta o anula una factura, es obligatorio declarar el grupo `cac:DiscrepancyResponse` indicando el código de respuesta (`cbc:ResponseCode`) y la justificación textual del ajuste[cite: 154, 155].
- **Regla de Signos (VLR01):** Todos los valores monetarios de ajuste o devolución deben expresarse en valores positivos[cite: 21]. El único campo que admite signo negativo es el ajuste por redondeo (`PayableRoundingAmount`)[cite: 21].
- **Codificación:** El documento XML debe estar estructurado en codificación estandarizada `UTF-8`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
Declara el elemento raíz `<CreditNote>` incorporando los espacios de nombres (*namespaces*) estándar de UBL 2.1 y la DIAN[cite: 152].

- **Ruta:** `/CreditNote`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`[cite: 152].
  * `cbc:CustomizationID`: `20` (Nota Crédito que referencia Factura) o `24` (Nota de Ajuste para Factura Aceptada)[cite: 153].
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Nota Crédito de Factura Electrónica de Venta` o `DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada`[cite: 153].
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas)[cite: 153].
  * `cbc:ID`: Prefijo y número consecutivo asignado a la nota crédito (Ej. `NC990000001`).
  * `cbc:UUID`: CUDE de 96 caracteres, calculado con SHA-384. Incluye el atributo `@schemeID="CUDE-SHA384"`[cite: 16, 153].
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición de la nota[cite: 155].
  * `cbc:CreditNoteTypeCode`: Código del tipo de nota (Fijo en `91` para Nota Crédito).
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción (Ej. `COP`).

### Paso 4.2: Referencia al Documento de Origen y Discrepancia (`DiscrepancyResponse` y `BillingReference`)
Paso exclusivo que vincula jurídicamente la Nota Crédito a la factura afectada y justifica la razón comercial del ajuste[cite: 154, 155].

- **Ruta:** `/CreditNote/cac:DiscrepancyResponse` y `/CreditNote/cac:BillingReference`
- **Procedimiento:**
  1. **Grupo `DiscrepancyResponse`:**
     * `cbc:ReferenceID`: Número de la factura electrónica afectada[cite: 154].
     * `cbc:ResponseCode`: Código de concepto del ajuste según la tabla oficial (Ej. `1` Devolución parcial, `2` Anulación de factura, `3` Rebaja/descuento)[cite: 154].
     * `cbc:Description`: Explicación detallada del motivo de la nota[cite: 154, 155].
  2. **Grupo `BillingReference / cac:InvoiceDocumentReference`:**
     * `cbc:ID`: Número de la factura afectada[cite: 155].
     * `cbc:UUID`: CUFE de la factura afectada, especificando el atributo `@schemeName="CUFE-SHA384"`[cite: 155].
     * `cbc:IssueDate`: Fecha original de emisión de la factura[cite: 155].

### Paso 4.3: Construcción de Extensiones DIAN (`UBLExtensions`)
Estructura reservada para alojar los metadatos de control fiscal, el código QR y la firma digital.

- **Ruta:** `/CreditNote/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):**
     * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT o Emisor) y `sts:SoftwareID`.
     * `sts:SoftwareSecurityCode`: SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Nota Crédito).
  2. **Generación de Código QR (`sts:QRCode`):** URL estructurada con los datos del documento (NumNC, Fecha, Hora, ValNC, ValIva, ValOtroIm, ValNCIm, CUDE).
  3. **Firma Digital (`ds:Signature`):** Inyección del bloque de firma Xades-EPES sobre el archivo XML canónico[cite: 20].

### Paso 4.4: Identificación del Emisor (`AccountingSupplierParty`)
- **Ruta:** `/CreditNote/cac:AccountingSupplierParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos fiscales del emisor registrados en la factura original: Razón Social (`cbc:RegistrationName`), NIT (`cbc:CompanyID` con `schemeID="31"`), Dígito de Verificación, responsabilidades fiscales (`cbc:TaxLevelCode`), régimen y ubicación territorial.

### Paso 4.5: Identificación del Adquiriente (`AccountingCustomerParty`)
- **Ruta:** `/CreditNote/cac:AccountingCustomerParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos del cliente registrado en la factura original sobre la cual se emite el ajuste. Si fue Consumidor Final, se mantiene `222222222222` con `schemeID="13"`.

### Paso 4.6: Líneas de Detalle de la Nota Crédito (`CreditNoteLine`) - ITERATIVO
Define los productos o servicios que sufren devolución, anulación o ajuste de valor[cite: 122].

- **Ruta:** `/CreditNote/cac:CreditNoteLine`
- **Nodos Obligatorios:**
  * `cbc:ID`: Número secuencial de la línea (1, 2, 3...).
  * `cbc:CreditedQuantity`: Cantidad devuelta o ajustada (mayor a `0.00`) con su atributo de unidad de medida `@unitCode`[cite: 21, 123].
  * `cbc:LineExtensionAmount`: Subtotal ajustado de la línea = (Cantidad * Precio) - Descuentos + Cargos.
  * `cac:Item / cbc:Description`: Descripción del producto/servicio y código estándar.
  * `cac:Price / cbc:PriceAmount`: Precio unitario base.
  * `cac:TaxTotal`: Impuestos específicos devueltos o recalculados en la línea[cite: 101, 111].

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
- **Ruta:** `/CreditNote/cac:TaxTotal`
- **Regla Estricta:** Reagrupa el IVA, INC o retenciones proporcionales al monto devuelto o anulado[cite: 101, 111]. El monto global reportado debe corresponder exactamente a la sumatoria de los impuestos desglosados en las líneas de detalle[cite: 102, 113].

### Paso 4.8: Totales Monetarios Legales (`LegalMonetaryTotal`)
El cierre aritmético de la Nota Crédito.

- **Ruta:** `/CreditNote/cac:LegalMonetaryTotal`
- **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los subtotales de línea ajustados.
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible ajustada.
  3. `cbc:TaxInclusiveAmount`: Subtotal base imponible + Impuestos recalculados.
  4. `cbc:AllowanceTotalAmount`: Suma de descuentos globales.
  5. `cbc:ChargeTotalAmount`: Suma de recargos globales.
  6. `cbc:PayableAmount`: TOTAL DEL AJUSTE / CRÉDITO = `TaxInclusiveAmount` - Descuentos Globales + Recargos Globales.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML de la Nota Crédito ha sido construido en memoria con su respectivo CUDE y enlace `BillingReference` a la factura original[cite: 153, 155].
2. **Estado Firmado:** La firma digital Xades-EPES ha sido estampada sobre el documento canónico en el nodo `ds:Signature`[cite: 20].
3. **Estado Empaquetado:** El archivo XML se comprime en un contenedor `.zip` listo para su transmisión a los Web Services de la DIAN.
4. **Respuesta Esperada:** Tras la transmisión, la DIAN devuelve un `ApplicationResponse` aceptando la nota (ResponseCode `02`), vinculando irrevocablemente el saldo ajustado a la factura electrónica original[cite: 13, 15].

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `CAD03 - ProfileID no corresponde`, la solución es verificar que el texto del campo `ProfileID` sea exactamente "DIAN 2.1: Nota Crédito de Factura Electrónica de Venta" o "DIAN 2.1: Nota de Ajuste para Factura Electrónica de Venta Aceptada" respetando mayúsculas y acentos[cite: 153].
- Si ocurre el error `CBG01 / CBG06 - BillingReference Invalido o Fecha Posterior`, la solución es verificar que el CUFE y la fecha reportados en `cac:BillingReference` coincidan exactamente con la factura registrada en la DIAN, y que la fecha de la Nota Crédito no sea anterior a la de la factura[cite: 155].
- Si ocurre el error `CBF01 - DiscrepancyResponse Ausente`, la solución es incluir obligatoriamente el grupo `cac:DiscrepancyResponse` con el código de concepto de ajuste válido y su descripción explicativa[cite: 154, 155].
- Si ocurre el error `CUDE Invalido / Algoritmo incorrecto`, la solución es revisar la concatenación para la generación del hash SHA-384, verificando que incluya los parámetros de la Nota Crédito en el orden exacto especificado en el Anexo Técnico[cite: 16, 153].
- Si ocurre el error `Diferencia Aritmética en Impuestos`, la solución es verificar que la devolución de tributos en `cac:TaxTotal` sea la sumatoria exacta (respetando la tolerancia de $2.00) de lo devuelto en cada `CreditNoteLine`[cite: 102, 113].

---

## 7. Tablas de Referencia y Anexos Técnicos

**Conceptos de Corrección / Discrepancia para Nota Crédito (ResponseCode)**
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

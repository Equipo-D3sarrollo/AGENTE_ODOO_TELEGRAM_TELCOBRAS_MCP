<!-- Path: finanzas_y_contabilidad/10_manual_nota_debito.md -->

# 🧾 Manual Operativo 10: Generación de Nota Débito (DebitNote) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el usuario o el sistema automatizado necesite construir, validar y empaquetar algorítmicamente el archivo XML correspondiente a una Nota Débito Electrónica, garantizando la interoperabilidad técnica y fiscal con los Web Services de la DIAN para aplicar cargos adicionales, cobro de intereses o incrementar el valor de una factura electrónica de venta previamente validada.

---

# MANUAL DE GENERACIÓN: NOTA DÉBITO (DEBITNOTE)

**ID del Manual:** DIAN-UBL2.1-M03  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (Extensible Markup Language)  
**Propósito:** Detallar el procedimiento algorítmico, técnico y normativo para la construcción, validación y ensamblaje de la Nota Débito Electrónica, garantizando la interoperabilidad con la DIAN y la correcta interpretación tanto humana como de sistemas automatizados (IA).

---

## 1. PROCEDIMIENTO GENERAL

La construcción de una Nota Débito Electrónica (`DebitNote`) se divide en la orquestación secuencial del nodo raíz, la declaración del tipo de operación y concepto de cargo adicional (`DiscrepancyResponse`), la vinculación a la factura electrónica de origen (`BillingReference`), las extensiones de control DIAN (firma digital y proveedor tecnológico), la identificación de participantes (emisor/receptor), el desglose de los ítems o cargos debitados (`DebitNoteLine`), el cálculo tributario de los nuevos cargos y el cierre aritmético de totales monetarios bajo el estándar internacional UBL 2.1 y el Anexo Técnico 1.8 (Resolución 165 de 2023).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura de Origen Aceptada:** La factura electrónica de venta a la cual se le aplicará el cargo adicional debe haber sido transmitida previamente y contar con estado "Aceptada" en la plataforma DIAN.
2. **Disponibilidad del CUFE Original:** Se debe contar con el CUFE exacto de la factura afectada y su fecha de expedición para establecer la trazabilidad jurídica del incremento.
3. **Sincronización NTP:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo y la firma digital (Xades-EPES).
4. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación abierta (ONAC u homóloga).
5. **Algoritmo CUDE Configurado:** Disponer de la Clave Técnica o PIN de software para el cálculo de la huella digital hash CUDE mediante el algoritmo SHA-384.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Perfil:** El elemento `ProfileID` debe indicar exactamente el literal "DIAN 2.1: Nota Débito de Factura Electrónica de Venta".
- **Regla de Identificadores (CUDE):** Al igual que la Nota Crédito, la Nota Débito genera y transmite obligatoriamente el Código Único de Documento Electrónico (CUDE) calculado mediante la función Hash SHA-384. No utiliza CUFE.
- **Regla de Coherencia Temporal:** La fecha de emisión de la Nota Débito (`DebitNote/cbc:IssueDate`) no puede ser anterior a la fecha de emisión de la factura electrónica referenciada.
- **Regla de Discrepancia Obligatoria:** Al generar un mayor valor sobre una factura, es obligatorio declarar el grupo `cac:DiscrepancyResponse` indicando el código de respuesta (`cbc:ResponseCode`) y la justificación textual del cargo adicional.
- **Regla de Signos:** Todos los valores monetarios de cargo o incremento deben expresarse en valores positivos. El único campo que admite signo negativo es el ajuste por redondeo (`PayableRoundingAmount`).
- **Codificación:** El documento XML debe estar estructurado en codificación estandarizada `UTF-8`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<DebitNote>` incorporando los espacios de nombres (*namespaces*) estándar de UBL 2.1 y la DIAN.

- **Ruta:** `/DebitNote`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Fijo en `30` (Nota Débito que referencia Factura de Venta).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Nota Débito de Factura Electrónica de Venta`.
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Prefijo y número consecutivo asignado a la nota débito (Ej. `ND990000001`).
  * `cbc:UUID`: CUDE de 96 caracteres, calculado con SHA-384. Incluye el atributo `@schemeID="CUDE-SHA384"`.
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición.
  * `cbc:DebitNoteTypeCode`: Fijo en `92` (Código asignado a la Nota Débito).
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción, ej. `COP`.

### Paso 4.2: Referencia al Documento de Origen y Discrepancia (`DiscrepancyResponse` y `BillingReference`)
Paso exclusivo que vincula la Nota Débito a la factura afectada y justifica la razón comercial del mayor valor cobrado.

- **Ruta:** `/DebitNote/cac:DiscrepancyResponse` y `/DebitNote/cac:BillingReference`
- **Procedimiento:**
  1. **Grupo `DiscrepancyResponse`:**
     * `cbc:ReferenceID`: Número de la factura electrónica afectada.
     * `cbc:ResponseCode`: Código de concepto del cobro adicional según tabla oficial (Ej. `1` Intereses, `2` Gastos por cobrar).
     * `cbc:Description`: Explicación detallada del motivo del cargo adicional.
  2. **Grupo `BillingReference / cac:InvoiceDocumentReference`:**
     * `cbc:ID`: Número de la factura afectada.
     * `cbc:UUID`: CUFE de la factura afectada, especificando el atributo `@schemeName="CUFE-SHA384"`.
     * `cbc:IssueDate`: Fecha original de emisión de la factura.

### Paso 4.3: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque exclusivo que encapsula parámetros de control fiscal, proveedor tecnológico y firma digital.

- **Ruta:** `/DebitNote/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):**
     * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT) y `sts:SoftwareID`.
     * `sts:SoftwareSecurityCode`: Calcula el SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Nota Débito).
  2. **Generación de Código QR (`sts:QRCode`):** URL estructurada con los datos del documento (NumND, Fecha, Hora, ValND, ValIva, ValOtroIm, ValNDIm, CUDE).
  3. **Firma Digital (`ds:Signature`):** Segundo `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico. No debe existir más de un nodo de firma.

### Paso 4.4: Identificación del Emisor (`AccountingSupplierParty`)
- **Ruta:** `/DebitNote/cac:AccountingSupplierParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos fiscales del emisor registrados en la factura original: Razón Social (`cbc:RegistrationName`), NIT (`cbc:CompanyID` con `schemeID="31"`), Dígito de Verificación, responsabilidades fiscales (`cbc:TaxLevelCode`) y régimen.

### Paso 4.5: Identificación del Adquiriente (`AccountingCustomerParty`)
- **Ruta:** `/DebitNote/cac:AccountingCustomerParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos del cliente registrado en la factura original sobre la cual se emite el cargo. Si es NIT, se valida estrictamente el Dígito de Verificación.

### Paso 4.6: Líneas de Detalle de la Nota Débito (`DebitNoteLine`) - ITERATIVO
Define los conceptos, productos o servicios que generan el cargo adicional o cobro por mayor valor.

- **Ruta:** `/DebitNote/cac:DebitNoteLine`
- **Nodos Obligatorios:**
  * `cbc:ID`: Número secuencial de la línea (1, 2, 3...).
  * `cbc:DebitedQuantity`: Cantidad cobrada o ajustada (mayor a `0.00`) con su código de unidad de medida `@unitCode`.
  * `cbc:LineExtensionAmount`: Subtotal debitado de la línea = (Cantidad * Precio) + Cargos - Descuentos.
  * `cac:Item / cbc:Description`: Descripción del cargo adicional (Ej. "Cobro por flete no facturado", "Intereses por mora").
  * `cac:Price / cbc:PriceAmount`: Precio unitario base del cargo.
  * `cac:TaxTotal`: Impuestos específicos generados por este nuevo cargo.

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
- **Ruta:** `/DebitNote/cac:TaxTotal`
- **Regla Estricta:** Reagrupa los impuestos (IVA, INC) proporcionales al monto cobrado adicionalmente. El valor reportado aquí es la sumatoria matemática exacta de todos los `TaxTotal` informados en las líneas de detalle (`DebitNoteLine`).

### Paso 4.8: Totales Monetarios Legales (`RequestedMonetaryTotal`)
El cierre aritmético de la Nota Débito. Determina el monto extra que debe pagar el cliente.

- **Ruta:** `/DebitNote/cac:RequestedMonetaryTotal` *(Nota: En DebitNote se usa RequestedMonetaryTotal)*
- **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los subtotales de las líneas debitadas.
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible de los cargos.
  3. `cbc:TaxInclusiveAmount`: Subtotal base imponible + Impuestos generados.
  4. `cbc:PayableAmount`: TOTAL DEL CARGO ADICIONAL = `TaxInclusiveAmount` - Descuentos + Recargos.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha cerrado en memoria, cuenta con el CUDE válido, el QR estructurado y la referencia `BillingReference` a la factura de origen.
2. **Estado Firmado:** El XML es procesado por un componente de firma que inyecta el `ds:Signature`. El archivo ya no puede ser alterado.
3. **Estado Empaquetado:** El archivo XML se comprime en formato `.zip` junto a sus validaciones internas, listo para transmisión.
4. **Respuesta Esperada:** El documento espera un `ApplicationResponse` por parte de la DIAN donde el elemento `ResponseCode` sea `02` (Documento Validado por la DIAN), lo cual incrementa formalmente el saldo de la factura referenciada.

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `CAD03 - ProfileID no corresponde`, la solución es verificar que el texto del campo `ProfileID` sea exactamente "DIAN 2.1: Nota Débito de Factura Electrónica de Venta" respetando mayúsculas y espacios.
- Si ocurre el error `Referencia de Factura Invalida`, la solución es verificar que el CUFE y la fecha reportados en `cac:BillingReference` coincidan exactamente con la factura previamente aceptada, y que la fecha de la Nota Débito no sea anterior a la de la factura original.
- Si ocurre el error `Ausencia de DiscrepancyResponse`, la solución es incluir obligatoriamente el grupo `cac:DiscrepancyResponse` con el código de concepto de cargo válido y su descripción explicativa.
- Si ocurre el error `CUDE Invalido / Algoritmo incorrecto`, la solución es revisar la concatenación estricta de la cadena fuente para SHA-384, garantizando que incluya los valores específicos de la Nota Débito en el orden dictado por el Anexo Técnico 1.8.
- Si ocurre el error `TAX-TOTAL-ERROR` (Discrepancia aritmética), la solución es verificar que el cálculo global de impuestos en `cac:TaxTotal` cuadre matemáticamente con la suma de los impuestos de cada `DebitNoteLine`, tolerando un desfase máximo de $2.00 COP.

---

## 7. Tablas de Referencia y Anexos Técnicos

**Conceptos de Corrección / Discrepancia para Nota Débito (ResponseCode)**
| Código (ResponseCode) | Concepto del Cargo Adicional | Descripción / Aplicación |
| :--- | :--- | :--- |
| **1** | Intereses | Cobro de intereses de mora o financiación sobre la factura original. |
| **2** | Gastos por cobrar | Cobro de gastos adicionales incurridos (ej. fletes, seguros no facturados previamente). |
| **3** | Cambio del valor | Ajuste por incremento en el precio unitario pactado originalmente. |
| **4** | Otros | Conceptos administrativos que incrementen el valor y no estén en las opciones anteriores. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Nota Débito Electrónica (DebitNote) UBL 2.1, cálculos CUDE, referencia a factura de origen (BillingReference) y conceptos de cargo adicional: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/10_manual_nota_debito.md

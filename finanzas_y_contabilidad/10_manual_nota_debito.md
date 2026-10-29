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

La construcción de una Nota Débito Electrónica (`DebitNote`) se divide en la orquestación secuencial del nodo raíz, la declaración del tipo de operación y concepto de cargo adicional (`DiscrepancyResponse`), la vinculación a la factura electrónica de origen (`BillingReference`), las extensiones de control DIAN (firma digital y proveedor tecnológico), la identificación de participantes (emisor/receptor), el desglose de los ítems o cargos debitados (`DebitNoteLine`), el cálculo tributario de los nuevos cargos y el cierre aritmético de totales monetarios bajo el estándar internacional UBL 2.1 y el Anexo Técnico 1.9 (Resolución 165 de 2023).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura de Origen Aceptada:** La factura electrónica de venta a la cual se le aplicará el cargo adicional debe haber sido transmitida previamente y contar con estado "Aceptada" en la plataforma DIAN. Si el adquiriente ya aceptó la factura de manera expresa o tácita (eventos `033` o `034`), la DIAN indica que no es posible la transmisión de una nota débito por cuanto la factura ya se encuentra aceptada (Anexo 1.9, regla LGC16).
2. **Disponibilidad del CUFE Original:** Se debe contar con el CUFE exacto de la factura afectada y su fecha de expedición para establecer la trazabilidad jurídica del incremento.
3. **Sincronización con la hora legal:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo y la firma digital (Xades-EPES). La hora legal la determina el reloj de la Superintendencia de Industria y Comercio (http://horalegal.inm.gov.co/) (Anexo 1.9, num. 10.5.8).
4. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación digital abierta autorizada por la ONAC; un documento firmado que no cumpla esta condición se entiende inválido (Anexo 1.9, num. 10.1).
5. **Algoritmo CUDE Configurado:** Disponer del PIN de software para el cálculo de la huella digital hash CUDE mediante el algoritmo SHA-384. El CUDE de la nota débito usa el `Software-PIN` registrado en el catálogo del participante, no la clave técnica (Anexo 1.9, num. 11.1.4.5).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Perfil (DAD03):** El elemento `ProfileID` debe indicar exactamente el literal "DIAN 2.1: Nota Débito de Factura Electrónica de Venta".
- **Regla de Identificadores (CUDE):** Al igual que la Nota Crédito, la Nota Débito genera y transmite obligatoriamente el Código Único de Documento Electrónico (CUDE) calculado mediante la función Hash SHA-384. No utiliza CUFE.
- **Regla de Coherencia Temporal (DBG06):** La fecha de emisión de la Nota Débito (`DebitNote/cbc:IssueDate`) no puede ser anterior a la fecha de emisión de la factura electrónica referenciada. Además, la fecha de generación de la nota debe ser igual a su fecha de firma (Anexo 1.9, regla DAD09e).
- **Regla de Discrepancia Obligatoria (DBF01-DBF04):** Al generar un mayor valor sobre una factura, es obligatorio declarar el grupo `cac:DiscrepancyResponse` indicando el código de respuesta (`cbc:ResponseCode`) y la justificación textual del cargo adicional. El grupo es obligatorio cuando `CustomizationID` es `30`, y la descripción debe corresponder al código informado (Anexo 1.9, DBF01 y DBF04).
- **Regla de Signos:** Todos los valores monetarios de cargo o incremento deben expresarse en valores positivos. El único campo que admite signo negativo es el ajuste por redondeo (`PayableRoundingAmount`).
- **Codificación:** El documento XML debe estar estructurado en codificación estandarizada `UTF-8` (Anexo 1.9, regla ZB03).
- **Contingencia:** La nota débito no tiene esquema de contingencia; se genera y transmite cuando los servicios de la DIAN funcionen normalmente (Anexo 1.9, num. 12.1, 12.2 y 12.3.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<DebitNote>` incorporando los espacios de nombres (*namespaces*) estándar de UBL 2.1 y la DIAN.

- **Ruta:** `/DebitNote`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Fijo en `30` (Nota Débito que referencia Factura de Venta). `30` es el valor por defecto; el tipo `32` se usa para notas débito sin referencia a una factura, informando el periodo que se afecta en `cac:InvoicePeriod` (Anexo 1.9, num. 14.3; DAE01).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Nota Débito de Factura Electrónica de Venta`.
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Prefijo y número consecutivo asignado a la nota débito (Ej. `ND990000001`), sin espacios ni guiones (Anexo 1.9, DAD05).
  * `cbc:UUID`: CUDE de 96 caracteres, calculado con SHA-384. El algoritmo se indica en el atributo `@schemeName="CUDE-SHA384"` y el ambiente de destino en `@schemeID` (`1` Producción, `2` Pruebas) (Anexo 1.9, DAD06, DAD07 y DAD08).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición, con la hora en zona horaria -5 (Anexo 1.9, DAD09 y DAD10).
  * `cbc:DebitNoteTypeCode`: Fijo en `92` (Código asignado a la Nota Débito). *Nota: la tabla de definición de la Nota Débito del Anexo 1.9 (num. 6.3) no incluye este elemento entre los campos de cabecera (DAD01 a DAD16); verificar su uso contra la Caja de Herramientas.*
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción, ej. `COP`.
  * `cbc:LineCountNumeric`: Número de líneas; debe coincidir con el número de grupos `cac:DebitNoteLine` (Anexo 1.9, DAD16).

### Paso 4.2: Referencia al Documento de Origen y Discrepancia (`DiscrepancyResponse` y `BillingReference`)
Paso exclusivo que vincula la Nota Débito a la factura afectada y justifica la razón comercial del mayor valor cobrado.

- **Ruta:** `/DebitNote/cac:DiscrepancyResponse` y `/DebitNote/cac:BillingReference`
- **Procedimiento:**
  1. **Grupo `DiscrepancyResponse`:**
     * `cbc:ReferenceID`: Identifica la sección de la factura original a la cual se aplica la corrección (Anexo 1.9, DBF02).
     * `cbc:ResponseCode`: Código de concepto del cobro adicional según tabla oficial (Ej. `1` Intereses, `2` Gastos por cobrar). La tabla 13.2.5 está en la Caja de Herramientas (Anexo 1.9, DBF03).
     * `cbc:Description`: Explicación detallada del motivo del cargo adicional. Debe corresponder al código informado en `cbc:ResponseCode` (Anexo 1.9, DBF04).
  2. **Grupo `BillingReference / cac:InvoiceDocumentReference`:**
     * `cbc:ID`: Número de la factura afectada.
     * `cbc:UUID`: CUFE de la factura afectada, especificando el atributo `@schemeName="CUFE-SHA384"`.
     * `cbc:IssueDate`: Fecha original de emisión de la factura.
     * Todas las facturas afectadas deben ser de un mismo adquiriente. Si `CustomizationID` es `30`, el grupo aparece una vez; si es `32`, no se valida. Si el CUFE de la factura relacionada no existe, la DIAN rechaza (Anexo 1.9, DBG01 y DBG04).

### Paso 4.3: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque exclusivo que encapsula parámetros de control fiscal, proveedor tecnológico y firma digital.

- **Ruta:** `/DebitNote/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):**
     * `sts:InvoiceSource`: `cbc:IdentificationCode` con el literal `CO` y atributos `@listAgencyID`, `@listAgencyName` y `@listSchemeURI` (Anexo 1.9, DAB13 a DAB17).
     * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT) y `sts:softwareID` (Anexo 1.9, DAB18 a DAB26).
     * `sts:SoftwareSecurityCode`: Calcula el SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Nota Débito) (Anexo 1.9, num. 11.1.8).
     * `sts:AuthorizationProvider`: NIT de la DIAN en `sts:AuthorizationProviderID` (Anexo 1.9, DAB30 a DAB35).
     * La nota débito no lleva grupo `sts:InvoiceControl` (resolución de numeración).
  2. **Generación de Código QR (`sts:QRCode`):** El elemento debe contener la URL `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE`, donde la palabra CUFE se reemplaza por el CUFE o CUDE del documento electrónico (Anexo 1.9, DAB36). El QR de la representación gráfica lleva: número, fecha, hora, NIT del facturador, documento del adquiriente, valor (`RequestedMonetaryTotal/cbc:LineExtensionAmount`), IVA, otros impuestos, valor total (`RequestedMonetaryTotal/cbc:PayableAmount`) y CUDE (Anexo 1.9, num. 11.1.7).
  3. **Firma Digital (`ds:Signature`):** `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico. Debe haber como mínimo dos `UBLExtension` (`sts:DianExtensions` y `ds:Signature`). No debe existir más de un nodo de firma (Anexo 1.9, DAC01 a DAC03).

### Paso 4.4: Identificación del Emisor (`AccountingSupplierParty`)
- **Ruta:** `/DebitNote/cac:AccountingSupplierParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos fiscales del emisor registrados en la factura original: Razón Social (`cbc:RegistrationName`), NIT (`cbc:CompanyID` con `schemeName="31"`), Dígito de Verificación (en `@schemeID`; debe ser informado y correcto, reglas DAJ24a, DAJ24b y DAJ25), responsabilidades fiscales (`cbc:TaxLevelCode`) y régimen.

### Paso 4.5: Identificación del Adquiriente (`AccountingCustomerParty`)
- **Ruta:** `/DebitNote/cac:AccountingCustomerParty/cac:Party`
- **Nodos Obligatorios:**
  * Debe coincidir exactamente con los datos del cliente registrado en la factura original sobre la cual se emite el cargo. Si es NIT, se valida estrictamente el Dígito de Verificación, que se informa en `@schemeID` (Anexo 1.9, DAK24 y DAK25).

### Paso 4.6: Líneas de Detalle de la Nota Débito (`DebitNoteLine`) - ITERATIVO
Define los conceptos, productos o servicios que generan el cargo adicional o cobro por mayor valor.

- **Ruta:** `/DebitNote/cac:DebitNoteLine`
- **Nodos Obligatorios:**
  * `cbc:ID`: Número secuencial de la línea (1, 2, 3...).
  * `cbc:DebitedQuantity`: Cantidad cobrada o ajustada (mayor a `0.00`) con su código de unidad de medida `@unitCode` (Anexo 1.9, DAV04a, DAV04b y DAV05).
  * `cbc:LineExtensionAmount`: Subtotal debitado de la línea = (Cantidad * Precio) + Cargos - Descuentos (Anexo 1.9, DAV06).
  * `cac:Item / cbc:Description`: Descripción del cargo adicional (Ej. "Cobro por flete no facturado", "Intereses por mora").
  * `cac:Price / cbc:PriceAmount`: Precio unitario base del cargo.
  * `cac:TaxTotal`: Impuestos específicos generados por este nuevo cargo. Solo un `TaxTotal` por tributo en la línea (Anexo 1.9, DAX01); para tributos en porcentaje, `TaxAmount` = `TaxableAmount` * `Percent` (Anexo 1.9, DAX07).

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
- **Ruta:** `/DebitNote/cac:TaxTotal`
- **Regla Estricta:** Reagrupa los impuestos (IVA, INC) proporcionales al monto cobrado adicionalmente. El valor reportado aquí es la sumatoria matemática exacta de todos los `TaxTotal` informados en las líneas de detalle (`DebitNoteLine`). Debe existir un `TaxTotal` de cabecera por cada tipo de impuesto informado en las líneas; su `TaxAmount` es la suma de sus `TaxSubtotal` y cada `TaxSubtotal/cbc:TaxAmount` es `TaxableAmount` * `Percent` (Anexo 1.9, DAS01b, DAS02 y DAS07). Las retenciones se informan en `cac:WithholdingTaxTotal`.

### Paso 4.8: Totales Monetarios Legales (`RequestedMonetaryTotal`)
El cierre aritmético de la Nota Débito. Determina el monto extra que debe pagar el cliente.

- **Ruta:** `/DebitNote/cac:RequestedMonetaryTotal` *(Nota: En DebitNote se usa RequestedMonetaryTotal)*
- **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los subtotales de las líneas debitadas (Anexo 1.9, DAU02).
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible de los cargos, suma de las bases imponibles de las líneas (Anexo 1.9, DAU04).
  3. `cbc:TaxInclusiveAmount`: Valor bruto (`LineExtensionAmount`) + Impuestos generados de los `TaxTotal` de cabecera (Anexo 1.9, DAU06).
  4. `cbc:PayableAmount`: TOTAL DEL CARGO ADICIONAL = `TaxInclusiveAmount` - Descuentos (`AllowanceTotalAmount`) + Recargos (`ChargeTotalAmount`) (Anexo 1.9, DAU14).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha cerrado en memoria, cuenta con el CUDE válido, el QR estructurado y la referencia `BillingReference` a la factura de origen.
2. **Estado Firmado:** El XML es procesado por un componente de firma que inyecta el `ds:Signature`. El archivo ya no puede ser alterado.
3. **Estado Empaquetado:** El archivo XML se comprime en formato `.zip` junto a sus validaciones internas, listo para transmisión. El XML se nombra con el prefijo `nd` + NIT del facturador sin DV (10 dígitos) + código `ppp` + 2 últimos dígitos del año + consecutivo de 8 dígitos hexadecimales (ej. `nd08001972680001900000003.xml`), y el ZIP con el mismo patrón iniciando en `z` (Anexo 1.9, num. 6.7 y 6.8).
4. **Respuesta Esperada:** El documento espera un `ApplicationResponse` por parte de la DIAN donde el elemento `ResponseCode` sea `02` (Documento Validado por la DIAN), lo cual incrementa formalmente el saldo de la factura referenciada. Si la nota es rechazada, el `ResponseCode` es `04` (Anexo 1.9, num. 6.5.3).

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `DAD03 - ProfileID no corresponde`, la solución es verificar que el texto del campo `ProfileID` sea exactamente "DIAN 2.1: Nota Débito de Factura Electrónica de Venta" respetando mayúsculas y espacios.
- Si ocurre el error `Referencia de Factura Invalida` (reglas `DBG04` o `DBG06`), la solución es verificar que el CUFE y la fecha reportados en `cac:BillingReference` coincidan exactamente con la factura previamente aceptada, y que la fecha de la Nota Débito no sea anterior a la de la factura original.
- Si ocurre el error `DAD09e` (la fecha de generación de la ND es diferente a la fecha de firma), la solución es generar y firmar la nota con la misma fecha de `cbc:IssueDate`.
- Si ocurre el error `LGC16` (la factura ya se encuentra aceptada), no es posible la transmisión de la nota débito (Anexo 1.9, num. 8.6).
- Si ocurre el error `Ausencia de DiscrepancyResponse`, la solución es incluir obligatoriamente el grupo `cac:DiscrepancyResponse` con el código de concepto de cargo válido y su descripción explicativa.
- Si ocurre el error `CUDE Invalido / Algoritmo incorrecto` (regla `DAD06`), la solución es revisar la concatenación estricta de la cadena fuente para SHA-384, garantizando que incluya los valores específicos de la Nota Débito en el orden dictado por el Anexo Técnico 1.9: `NumDb` + `FecDb` + `HorDb` + `ValDb` + `01` + `ValImp1` + `04` + `ValImp2` + `03` + `ValImp3` + `ValTot` + `NitOFE` + `NumAdq` + `Software-PIN` + `TipoAmbiente`, con valores de dos decimales truncados, `0.00` para impuestos no referenciados y NIT sin DV (Anexo 1.9, num. 11.1.4.5).
- Si ocurre un rechazo por discrepancia aritmética en impuestos (reglas `DAS02`, `DAS07`, `DAX07` o `DAU06`), la solución es verificar que el cálculo global de impuestos en `cac:TaxTotal` cuadre matemáticamente con la suma de los impuestos de cada `DebitNoteLine`, tolerando un desfase máximo de $2.00 COP.

---

## 7. Tablas de Referencia y Anexos Técnicos

**Conceptos de Corrección / Discrepancia para Nota Débito (ResponseCode)**

*Nota: la tabla oficial 13.2.5 está en la Caja de Herramientas de la DIAN y no en el PDF del Anexo 1.9 (num. 14.4.1.1.4).*

| Código (ResponseCode) | Concepto del Cargo Adicional | Descripción / Aplicación |
| :--- | :--- | :--- |
| **1** | Intereses | Cobro de intereses de mora o financiación sobre la factura original. |
| **2** | Gastos por cobrar | Cobro de gastos adicionales incurridos (ej. fletes, seguros no facturados previamente). |
| **3** | Cambio del valor | Ajuste por incremento en el precio unitario pactado originalmente. |
| **4** | Otros | Conceptos administrativos que incrementen el valor y no estén en las opciones anteriores. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Nota Débito Electrónica (DebitNote) UBL 2.1, cálculos CUDE, referencia a factura de origen (BillingReference) y conceptos de cargo adicional: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/10_manual_nota_debito.md

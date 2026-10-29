<!-- Path: finanzas_y_contabilidad/08_manual_factura_electronica_venta.md -->

# 🧾 Manual Operativo 08: Generación de Factura Electrónica de Venta (Invoice) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el usuario o el sistema automatizado necesite construir, validar y empaquetar algorítmicamente el archivo XML correspondiente a la factura electrónica estándar, garantizando la interoperabilidad técnica y fiscal con los Web Services de la DIAN para legalizar transacciones de bienes y servicios.

---

# MANUAL DE GENERACIÓN: FACTURA ELECTRÓNICA DE VENTA (INVOICE)

**ID del Manual:** DIAN-UBL2.1-M01  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (Extensible Markup Language)  
**Propósito:** Detallar el procedimiento algorítmico, técnico y normativo para la construcción, validación y ensamblaje de la Factura Electrónica de Venta estándar, garantizando la interoperabilidad con la DIAN y la correcta interpretación tanto humana como de sistemas automatizados (IA).

---

## 1. PROCEDIMIENTO GENERAL

La construcción de una Factura Electrónica de Venta (`Invoice`) se divide en la orquestación secuencial de metadatos de cabecera, extensiones de control DIAN (firma digital y parámetros de resolución), identificación de actores (emisor/receptor), condiciones comerciales, consolidación de ítems, cálculos tributarios cruzados y totales monetarios. Todo el archivo debe encapsularse bajo el estándar internacional UBL 2.1 y cumplir con el Anexo Técnico 1.9 (Resolución 165 de 2023).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Habilitación DIAN:** El emisor (NIT) debe estar en estado "Habilitado" en el catálogo de participantes de la DIAN.
2. **Sincronización con la hora legal:** El reloj del servidor debe estar sincronizado con el reloj de la Superintendencia de Industria y Comercio, que determina la hora legal colombiana (http://horalegal.inm.gov.co/), para la firma digital (Xades-EPES) y la marca de tiempo de emisión (Anexo 1.9, num. 10.5.8). La hora de emisión se informa en la zona horaria UTC-05:00 (regla FAD10).
3. **Resolución de Facturación:** Se debe tener una resolución de numeración (con prefijo y rango) activa y vigente extraída mediante el consumo del Web Service de la DIAN.
4. **Certificado Digital:** Contar con un certificado digital X.509 vigente y no revocado, expedido por una entidad de certificación digital abierta autorizada por la ONAC; un documento firmado que no cumpla esta condición se entiende inválido (Anexo 1.9, num. 10.1 y 10.5.6).
5. **Claves de Software:** Disponer del `SoftwareID`, `SoftwarePIN` y `Clave Técnica` (necesaria para el cálculo del CUFE). La clave técnica viene con la consulta del rango de numeración por web service y no está expuesta en el XML; cada rango nuevo trae una clave técnica diferente a la del rango anterior (Anexo 1.9, num. 11.1.6).

---


## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

* **Regla de Signos (VLR01):** Queda estrictamente prohibido enviar valores monetarios negativos (ej. `<cbc:LineExtensionAmount>-100.00</cbc:LineExtensionAmount>`). Todo descuento se expresa en valor absoluto positivo. El único campo que admite negativos es `PayableRoundingAmount`. Las tarifas tributarias tampoco admiten valores negativos (deben ser iguales o superiores a 0.00) y las cantidades de productos y servicios deben ser valores positivos mayores a 0.00 (Anexo 1.9, num. 5.3 y 5.4; reglas VLR01 y FAV04b).
* **Regla de Decimales:** Cada elemento numérico tiene el tamaño y número de decimales definido en su tabla (Anexo 1.9, num. 5.7, Tabla 4). Los valores monetarios de la factura (ej. `LineExtensionAmount`, `TaxAmount`, `PayableAmount`) y el precio unitario (`PriceAmount`) se definen con hasta seis (6) decimales (formato `p (0-6)`). En las operaciones aritméticas se aplica el redondeo *round-half-to-even* (NTC 3711) y, si hay diferencia entre los totales calculados y la suma de los parciales, se informa en `/Invoice/LegalMonetaryTotal/cbc:PayableRoundingAmount` (Anexo 1.9, num. 5.2.1).
* **Tolerancia Aritmética:** Los Web Services de la DIAN aplican una tolerancia de `+/- 2.00` en la validación cruzada (Ej. Precio * Cantidad vs Total Línea). En `cbc:TaxAmount` del IVA se permite además una tolerancia de más o menos cinco pesos (`$5.00`) para la aproximación al múltiplo de diez pesos (`$10.00`) más cercano (Anexo 1.9, num. 5.1.1.1 y 5.1.1.2).
* **Validación de Fechas (FAD09a, FAD09b, FAD09e):** La fecha de emisión (`cbc:IssueDate`) debe ser igual o superior a la fecha de inicio (`sts:AuthorizationPeriod/cbc:StartDate`) e igual o inferior a la fecha final (`sts:AuthorizationPeriod/cbc:EndDate`) de la autorización de numeración, y debe ser igual a la fecha de firma de la factura. En la versión 1.9 se eliminaron las reglas FAD09c y FAD09d (Anexo 1.9, num. 2.1 y 8.2).
* **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8` (Anexo 1.9, regla ZB03).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<Invoice>` con todos sus *namespaces* (`xmlns:ext`, `xmlns:cbc`, `xmlns:cac`, `xmlns:sts`, etc.).

* **Ruta:** `/Invoice`
* **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Tipo de operación: `10` (Estándar, valor por defecto), `09` (AIU), `11` (Mandatos), `12` (Transporte), etc. La tabla completa (13.1.5.1 Tipos de operación) está en la Caja de Herramientas de la DIAN (Anexo 1.9, num. 14 y 19.2; regla FAD02).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Factura Electrónica de Venta`.
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Número del documento incluyendo prefijo (Ej. `SETP990000000`). Solo puede contener números y letras, sin espacios ni guiones, y debe estar dentro del rango de numeración autorizado (Anexo 1.9, reglas FAD05a a FAD05e).
  * `cbc:UUID`: (CUFE) Identificador de 96 caracteres, calculado con SHA-384. El algoritmo se indica en `@schemeName="CUFE-SHA384"` y el ambiente de destino en `@schemeID` (`1` Producción, `2` Pruebas), que debe ser igual a `cbc:ProfileExecutionID` (Anexo 1.9, reglas FAD06, FAD07 y FAD08).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición. La fecha se informa en formato `YYYY-MM-DD` y la hora en zona horaria UTC-05:00 (Anexo 1.9, num. 5.6; reglas FAD09 y FAD10).
  * `cbc:InvoiceTypeCode`: `01` (Factura de Venta). El anexo también usa `02` (exportación), `03` (documento electrónico de transmisión de una factura de contingencia) y `04` (factura emitida por inconvenientes tecnológicos de la DIAN). La tabla completa (13.1.3 Tipo de Documento) está en la Caja de Herramientas (Anexo 1.9, num. 9.1, 11.1.4.1, 12.2 y 13.2; reglas FAD12 y FAK19).
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción, ej. `COP` o `USD`. Debe estar definida en el estándar ISO 4217 y ser la misma de todos los `@currencyID` del documento (Anexo 1.9, reglas FAD15a y FAD15b).
  * `cbc:LineCountNumeric`: Número de líneas de la factura; debe ser igual al número de grupos `cac:InvoiceLine` (Anexo 1.9, regla FAD16).

### Paso 4.2: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque exclusivo de Colombia que encapsula parámetros de control, proveedor tecnológico y firma.

* **Ruta:** `/Invoice/ext:UBLExtensions`
* **Procedimiento:**
  1.  **Extensión de Control (`sts:DianExtensions`):**
      * `sts:InvoiceControl`: Especifica `sts:InvoiceAuthorization` (N° de Resolución), fechas `sts:AuthorizationPeriod/cbc:StartDate` y `cbc:EndDate`, y el rango `sts:AuthorizedInvoices` con `sts:Prefix`, `sts:From` y `sts:To` (Anexo 1.9, FAB04 a FAB12).
      * `sts:InvoiceSource`: `cbc:IdentificationCode` con el literal `CO` y sus atributos `@listAgencyID="6"`, `@listAgencyName="United Nations Economic Commission for Europe"` y `@listSchemeURI="urn:oasis:names:specification:ubl:codelist:gc:CountryIdentificationCode-2.1"` (Anexo 1.9, FAB13 a FAB17).
      * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT sin DV, con `@schemeAgencyID="195"`, `@schemeAgencyName="CO, DIAN (Dirección de Impuestos y Aduanas Nacionales)"`, DV en `@schemeID` y `@schemeName="31"`) y `sts:softwareID`. Un obligado a facturar puede ser también proveedor tecnológico para sí mismo (Anexo 1.9, FAB18 a FAB26).
      * `sts:SoftwareSecurityCode`: Calcula el SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Factura) (Anexo 1.9, num. 11.1.8).
      * `sts:AuthorizationProvider`: `sts:AuthorizationProviderID` con el NIT de la DIAN (`800197268`), DV `4` en `@schemeID` y `@schemeName="31"` (Anexo 1.9, FAB30 a FAB35).
  2.  **Generación de Código QR (`sts:QRCode`):** El elemento contiene la URL `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE`, donde la palabra CUFE se reemplaza por el valor de `cbc:UUID`; en ambiente de habilitación la URL es `https://catalogo-vpfe-hab.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` (Anexo 1.9, FAB36 y num. 11.1.7.1). El código QR de la representación gráfica lleva: NumFac, FecFac, HorFac, NitFac, DocAdq, ValFac, ValIva, ValOtroIm, ValTolFac, CUFE y la URL; va en todas las páginas y mide mínimo 2 cm (Anexo 1.9, num. 11.1.7).
  3.  **Firma Digital (`ds:Signature`):** `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico. La factura debe tener como mínimo dos `UBLExtension`: una con `sts:DianExtensions` y otra con `ds:Signature`. No debe existir más de un nodo de firma o habrá rechazo (Anexo 1.9, reglas FAC01 y FAC03).

### Paso 4.3: Identificación del Emisor (`AccountingSupplierParty`)
* **Ruta:** `/Invoice/cac:AccountingSupplierParty/cac:Party`
* **Nodos Obligatorios:**
  * `cac:PartyTaxScheme`:
    * `cbc:RegistrationName`: Razón Social completa.
    * `cbc:CompanyID`: NIT del emisor. Requiere los atributos `@schemeAgencyID` (literal `195`), `@schemeAgencyName` (literal `CO, DIAN (Dirección de Impuestos y Aduanas Nacionales)`), `@schemeID` (Dígito de Verificación del NIT) y `@schemeName` (literal `31`). El DV debe ser informado y ser correcto (Anexo 1.9, FAJ21 a FAJ25; reglas FAJ24a y FAJ24b).
    * `cbc:TaxLevelCode`: Responsabilidades fiscales separadas por punto y coma (Ej. `O-13;O-15;O-47`). La lista de valores (13.2.6.1) está en la Caja de Herramientas (Anexo 1.9, FAJ26).
    * `cac:TaxScheme`: Identificador del impuesto (Ej. `01` para IVA).
  * `cac:RegistrationAddress`: Dirección fiscal. Debe tener como mínimo `cbc:ID` (código del municipio), `cbc:CityName`, `cbc:CountrySubentity` (Departamento), `cbc:CountrySubentityCode`, `cac:AddressLine/cbc:Line` y `cac:Country/cbc:IdentificationCode` (Anexo 1.9, FAJ28 y FAJ29).
  * Además son obligatorios: `cbc:AdditionalAccountID` (tipo de organización jurídica), `cac:PhysicalLocation/cac:Address` (con el código del municipio en `cbc:ID`) y `cac:PartyLegalEntity` (Anexo 1.9, FAJ02, FAJ07 a FAJ09 y FAJ42).

### Paso 4.4: Identificación del Adquiriente (`AccountingCustomerParty`)
* **Ruta:** `/Invoice/cac:AccountingCustomerParty/cac:Party`
* **Procedimiento Especial (Consumidor Final):**
  * Si el comprador es anónimo, se utiliza `cbc:CompanyID` = `222222222222` con `@schemeName="13"` (Cédula de ciudadanía). `RegistrationName` debe ser "consumidor final". Es obligatorio el grupo `cac:PartyIdentification` con `cbc:ID` = `222222222222`; en `cac:TaxScheme` se informa `cbc:ID` = `ZZ` y `cbc:Name` = `No aplica` (Anexo 1.9, FAK20, FAK21, FAK25, FAK40, FAK41, FAK61 y FAK62).
* **Procedimiento Estándar (NIT o Cédula):**
  * Misma estructura que el Emisor, adaptada al RUT del cliente. 
  * **Regla Crítica:** Si el tipo de documento es "31" (NIT), el NIT se informa sin DV en `cbc:CompanyID` y el DV en `@schemeID`; el sistema valida que el Dígito de Verificación esté informado y corresponda al NIT (Anexo 1.9, FAK21; reglas FAK24 y FAK25).

### Paso 4.5: Medios y Plazos de Pago (`PaymentMeans` y `PaymentTerms`)
* **Ruta:** `/Invoice/cac:PaymentMeans`
* **Nodos Obligatorios:**
  * `cbc:ID`: Medio de pago (Ej. `1` Contado, `2` Crédito).
  * `cbc:PaymentMeansCode`: Código de la tabla DIAN (Ej. `10` Efectivo, `42` Transferencia bancaria). La tabla de medios de pago está en la Caja de Herramientas (Anexo 1.9, regla FAN03).
  * `cbc:PaymentDueDate`: (Opcional, pero requerido si es a crédito) Fecha límite de pago. Si `cbc:ID` = `2` y no se informa, la DIAN rechaza (Anexo 1.9, regla FAN04).

### Paso 4.6: Líneas de Detalle de la Factura (`InvoiceLine`) - ITERATIVO
Este paso se repite por cada ítem (producto/servicio) a facturar. Todo cálculo parcial se consolida aquí.

* **Ruta:** `/Invoice/cac:InvoiceLine`
* **Nodos Obligatorios y Reglas por Línea:**
  * `cbc:ID`: Número secuencial (1, 2, 3...), consecutivo empezando en 1 (Anexo 1.9, regla FAV02b).
  * `cbc:InvoicedQuantity`: Cantidad mayor a `0.00`. Debe incluir el código de unidad de medida `@unitCode` (Ej. `EA` para unidades, `KGM` para kilogramos), que debe existir en la lista de unidades (Anexo 1.9, reglas FAV04a, FAV04b y FAV05).
  * `cbc:LineExtensionAmount`: Subtotal de la línea = (Precio Unitario * Cantidad) - Descuentos Línea + Recargos Línea. Si se informa `cac:PricingReference` (muestra o regalo comercial), el valor es `0.00` (Anexo 1.9, regla FAV06).
  * `cbc:Note`: En facturas por contratos de servicio tipo AIU, para el ítem Administración la nota debe empezar por el texto "Contrato de servicios AIU por concepto de:" e incluir el objeto del contrato facturado (Anexo 1.9, regla FAV03).
  * **(Opcional) Descuentos/Recargos (`cac:AllowanceCharge`):** Si aplica a la línea, se detalla si es cargo (`cbc:ChargeIndicator="true"`) o descuento (`false`), su base y su monto.
  * **Impuestos de la Línea (`cac:TaxTotal`):** Tributos del ítem (ej. IVA, INC, IC, Impuesto Nacional a los Combustibles); las retenciones se informan en `cac:WithholdingTaxTotal`. Solo un `TaxTotal` por tributo en la línea, y no se informa para ítems excluidos, facturas del régimen simple grupo I, ni ítems de contratos AIU que no hagan parte de la base gravable (Anexo 1.9, regla FAX01). 
    * `cbc:TaxAmount`: Valor del impuesto.
    * `cbc:TaxableAmount`: Base gravable.
    * `cbc:Percent`: Tarifa porcentual (Ej. `19.00`). Para tributos en porcentaje, `TaxAmount` = `TaxableAmount` * `Percent`; para tributos por unidad (ej. bolsas plásticas), `TaxAmount` = `PerUnitAmount` * cantidad (Anexo 1.9, regla FAX07).
  * `cac:Item`:
    * `cbc:Description`: Descripción detallada.
    * `cac:StandardItemIdentification`: Código de estándar (GTIN, UNSPSC). El anexo también contempla partidas arancelarias y códigos libres o autónomos (Anexo 1.9, num. 15).
  * `cac:Price / cbc:PriceAmount`: Valor unitario nominal del producto (Sin impuestos).

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
* **Ruta:** `/Invoice/cac:TaxTotal`
* **Regla Estricta:** Debe existir un `TaxTotal` a nivel de cabecera por cada tipo de impuesto que se informa a nivel de línea; si hay más de una tarifa del mismo impuesto, se informan en `TaxSubtotal` diferentes dentro del mismo `TaxTotal` (Anexo 1.9, reglas FAS01a y FAS01b).
* Se debe agrupar por cada tipo de impuesto (Ej. IVA general al 19%, IVA al 5%), con un `TaxSubtotal` por cada tarifa (Anexo 1.9, regla FAS04).
* **Postcondición:** `cac:TaxTotal/cbc:TaxAmount` debe ser la suma de los `TaxSubtotal/cbc:TaxAmount` de ese tributo, y cada `TaxSubtotal/cbc:TaxAmount` debe ser `TaxableAmount` * `Percent` (Anexo 1.9, reglas FAS02 y FAS07), con la tolerancia de `+/- 2.00` (Anexo 1.9, num. 5.1.1.1).
* Las retenciones (ReteIVA, ReteFuente) se informan en `/Invoice/cac:WithholdingTaxTotal` (Anexo 1.9, FAT01).

### Paso 4.8: Totales Monetarios Legales (`LegalMonetaryTotal`)
El cierre aritmético del documento. Representa lo que debe pagar el cliente.

* **Ruta:** `/Invoice/cac:LegalMonetaryTotal`
* **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los `LineExtensionAmount` de todas las líneas (Anexo 1.9, regla FAU02).
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible (Suma de las bases gravables `TaxableAmount` de todas las líneas) (Anexo 1.9, regla FAU04).
  3. `cbc:TaxInclusiveAmount`: Valor bruto (`LineExtensionAmount`) + suma de los tributos informados en los `TaxTotal` de cabecera (IVA, INC) (Anexo 1.9, regla FAU06).
  4. `cbc:AllowanceTotalAmount`: Suma de descuentos globales (`AllowanceCharge` con `ChargeIndicator="false"`) (Anexo 1.9, regla FAU08).
  5. `cbc:ChargeTotalAmount`: Suma de recargos globales (`AllowanceCharge` con `ChargeIndicator="true"`) (Anexo 1.9, regla FAU10).
  6. `cbc:PayableAmount`: TOTAL A PAGAR = `TaxInclusiveAmount` - Descuentos Globales + Recargos Globales (Anexo 1.9, regla FAU14).
* **Anticipos:** `cbc:PrepaidAmount` es la suma de los `cac:PrepaidPayment/cbc:PaidAmount` (Anexo 1.9, regla FAU12). Desde la versión 1.8 los anticipos son valores informativos y **no** se restan en `PayableAmount` (Anexo 1.9, num. 11.1.9.2).
* **Retenciones:** Los cálculos de `cac:LegalMonetaryTotal` no incluyen operaciones con `cac:WithholdingTaxTotal` (Anexo 1.9, num. 11.1.9.1).
* **Redondeo:** Si hay diferencia entre los totales calculados y la suma de los parciales, se informa en `cbc:PayableRoundingAmount`, único campo que admite valor negativo (Anexo 1.9, num. 5.2.1 y 5.3.1).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha cerrado en memoria, cuenta con el CUFE válido, el QR estructurado y la estructura canónica intacta.
2. **Estado Firmado:** El XML es procesado por un componente de firma que inyecta el `ds:Signature`. El archivo ya no puede ser alterado ni en un solo byte (o la firma perderá validez).
3. **Estado Empaquetado:** El archivo XML se comprime en formato `.zip` junto a sus validaciones internas, listo para ser consumido por el manual de *Transmisión a la DIAN (Web Services)*. El XML se nombra `fv` + NIT del facturador sin DV (10 dígitos con ceros a la izquierda) + código `ppp` (`000` Software Propio) + 2 últimos dígitos del año + consecutivo de 8 dígitos hexadecimales, y el ZIP con el mismo patrón iniciando en `z`; el consecutivo se reinicia en `00000001` cada 1 de enero (Anexo 1.9, num. 6.7 y 6.8). El XML no puede superar 500 KB (Anexo 1.9, regla ZA01).
4. **Respuesta Esperada:** El documento espera un `ApplicationResponse` por parte de la DIAN donde el elemento `ResponseCode` sea `02` (Documento Validado por la DIAN) o `04` (Documento Rechazado por la DIAN), detallando la regla de negocio incumplida en `LineResponse/Response/Description` (Anexo 1.9, num. 6.5.3 y 6.5.5).

## 6. Troubleshooting (Solución de Errores)

- Si ocurre un rechazo por discrepancia en impuestos (reglas `FAS02`, `FAS07`, `FAX02`, `FAX07` o `FAU06`), la solución es recalcular los montos dentro de cada `cac:InvoiceLine` y garantizar que cada `TaxAmount` sea la sumatoria de sus `TaxSubtotal` y el producto de base por tarifa (respetando la tolerancia de $2.00, y de $5.00 para la aproximación del IVA al múltiplo de $10.00).
- Si ocurre el error `Rechazo por Dígito de Verificación Invalido` (reglas `FAK24`, `FAK25`, `FAJ24a` o `FAJ24b`), la solución es verificar el NIT del receptor y calcular nuevamente el DV informado en el atributo `@schemeID` aplicando el algoritmo del Módulo 11 aprobado por la DIAN.
- Si ocurre el error `CUFE Invalido / Algoritmo incorrecto` (regla `FAD06`), la solución es revisar la concatenación estricta de la cadena fuente (`NumFac` + `FecFac` + `HorFac` + `ValFac` + `01` + `ValImp1` + `04` + `ValImp2` + `03` + `ValImp3` + `ValTot` + `NitOFE` + `NumAdq` + `ClTec` + `TipoAmbiente`) asegurando que no haya espacios adicionales y que el hash final resultante sea SHA-384. Los códigos `01`, `04` y `03` van siempre, con valor `0.00` si el impuesto no está referenciado; los valores van con punto decimal, dos decimales truncados, sin separadores de miles ni símbolo de pesos, y los NIT sin puntos, guiones ni DV (Anexo 1.9, num. 11.1.2).
- Si ocurre el error `XML mal formado o Firma Invalida (Documento Alterado)` (reglas `ZB01`, `ZB02` o `ZE02`), la solución es garantizar que ningún proceso intermedio modifique el archivo, ni siquiera un espacio o salto de línea, después de haber superado el paso inmutable de la firma electrónica.
- Si ocurre el error `FAD09e` (la fecha de generación de la factura es diferente a la fecha de firma), la solución es generar y firmar la factura con la misma fecha de `cbc:IssueDate`.
- Si ocurre el error `Resolución Invalida o Rango Consumido` (reglas `FAD05b` a `FAD05e`, `FAD09a` o `FAD09b`), la solución es detener inmediatamente la generación de facturas, tramitar una nueva numeración en el portal MUISCA de la DIAN y actualizar los parámetros `sts:InvoiceControl` en el componente, incluida la nueva clave técnica del rango (Anexo 1.9, num. 11.1.6).

---

## 7. Tablas de Referencia y Anexos Técnicos

**Perfiles y Códigos de Operación UBL (CustomizationID)**
| Documento | Uso principal | CustomizationID | ProfileID | Tipo Código Identificador |
| :--- | :--- | :--- | :--- | :--- |
| Factura Estándar | Venta general (bienes/servicios) | 10 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Mandato | Ventas a través de terceros | 11 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Transporte | Transporte de carga registrado en el RNDC | 12 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Cambiaria | Operaciones de divisa (código no listado en el PDF del anexo; verificar en la tabla 13.1.5.1 de la Caja de Herramientas) | 13 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Venta de divisas | Profesionales cambiarios; exige la UBLExtension `DebidaDiligencia` | 15 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Compra de divisas | Profesionales cambiarios; exige la UBLExtension `DebidaDiligencia` | 16 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura AIU | Obras civiles e ingeniería | 09 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Factura Electrónica de Venta (Invoice) UBL 2.1, cálculos CUFE, estructura de extensiones DIAN XML y reglas de validación: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/08_manual_factura_electronica_venta.md

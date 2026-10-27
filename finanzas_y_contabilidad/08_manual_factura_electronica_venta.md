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

La construcción de una Factura Electrónica de Venta (`Invoice`) se divide en la orquestación secuencial de metadatos de cabecera, extensiones de control DIAN (firma digital y parámetros de resolución), identificación de actores (emisor/receptor), condiciones comerciales, consolidación de ítems, cálculos tributarios cruzados y totales monetarios. Todo el archivo debe encapsularse bajo el estándar internacional UBL 2.1 y cumplir con el Anexo Técnico 1.8 (Resolución 165 de 2023).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Habilitación DIAN:** El emisor (NIT) debe estar en estado "Habilitado" en el catálogo de participantes de la DIAN.
2. **Sincronización NTP:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para la firma digital (Xades-EPES) y la marca de tiempo de emisión.
3. **Resolución de Facturación:** Se debe tener una resolución de numeración (con prefijo y rango) activa y vigente extraída mediante el consumo del Web Service de la DIAN.
4. **Certificado Digital:** Contar con un certificado digital X.509 vigente expedido por una ONAC u otra entidad de certificación digital abierta.
5. **Claves de Software:** Disponer del `SoftwareID`, `SoftwarePIN` y `Clave Técnica` (necesaria para el cálculo del CUFE).

---


## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

* **Regla de Signos (VLR01):** Queda estrictamente prohibido enviar valores monetarios negativos (ej. `<cbc:LineExtensionAmount>-100.00</cbc:LineExtensionAmount>`). Todo descuento se expresa en valor absoluto positivo. El único campo que admite negativos es `PayableRoundingAmount`.
* **Regla de Decimales (VLR02):** Se recomienda usar hasta dos (2) decimales (ej. `150.50`). Para tasas de cambio y cálculos internos (cantidad o precio unitario) se pueden usar hasta seis (6) decimales, pero el valor final monetario de la línea y el documento debe redondearse a 2.
* **Tolerancia Aritmética:** Los Web Services de la DIAN aplican una tolerancia de `+/- 2.00` en la validación cruzada (Ej. Precio * Cantidad vs Total Línea).
* **Validación de Fechas (FAD09):** La fecha de emisión (`cbc:IssueDate`) no puede ser superior a la fecha actual, ni tener una antigüedad mayor a 10 días calendario.
* **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
El proceso inicia declarando el elemento raíz `<Invoice>` con todos sus *namespaces* (`xmlns:ext`, `xmlns:cbc`, `xmlns:cac`, `xmlns:sts`, etc.).

* **Ruta:** `/Invoice`
* **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Fijo en `10` (Factura Estándar), `11` (Mandato), `09` (AIU), etc.
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: Factura Electrónica de Venta`.
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Número del documento incluyendo prefijo (Ej. `SETP990000000`).
  * `cbc:UUID`: (CUFE) Identificador de 96 caracteres, calculado con SHA-384. Incluye el esquema indicando el algoritmo de cálculo (`@schemeID="CUFE-SHA384"`).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de expedición.
  * `cbc:InvoiceTypeCode`: Fijo en `01` (Factura de Venta).
  * `cbc:DocumentCurrencyCode`: Moneda de la transacción, ej. `COP` o `USD`.

### Paso 4.2: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque exclusivo de Colombia que encapsula parámetros de control, proveedor tecnológico y firma.

* **Ruta:** `/Invoice/ext:UBLExtensions`
* **Procedimiento:**
  1.  **Extensión de Control (`sts:DianExtensions`):**
      * `sts:InvoiceControl`: Especifica `sts:InvoiceAuthorization` (N° de Resolución), fechas `sts:StartDate` y `sts:EndDate`, y rangos `sts:From` y `sts:To`.
      * `sts:SoftwareProvider`: `sts:ProviderID` (NIT del PT) y `sts:SoftwareID`.
      * `sts:SoftwareSecurityCode`: Calcula el SHA-384 de la concatenación (`SoftwareID` + `SoftwarePIN` + Número Factura).
  2.  **Generación de Código QR (`sts:QRCode`):** URL estructurada con los datos del documento (NumFactura, Fecha, Hora, ValFac, ValIva, ValOtroIm, ValFacIm, CUFE).
  3.  **Firma Digital (`ds:Signature`):** Segundo `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico. No debe existir más de un nodo de firma o habrá rechazo.

### Paso 4.3: Identificación del Emisor (`AccountingSupplierParty`)
* **Ruta:** `/Invoice/cac:AccountingSupplierParty/cac:Party`
* **Nodos Obligatorios:**
  * `cac:PartyTaxScheme`:
    * `cbc:RegistrationName`: Razón Social completa.
    * `cbc:CompanyID`: NIT del emisor. Requiere los atributos `@schemeID` (Fijo `31`), `@schemeName` (Fijo `31`) y `@schemeAgencyID` (Fijo `195`). Obligatorio calcular y añadir `@schemeAgencyName` o el Dígito de Verificación.
    * `cbc:TaxLevelCode`: Responsabilidades fiscales separadas por punto y coma (Ej. `O-13;O-15;O-47`).
    * `cac:TaxScheme`: Identificador del impuesto (Ej. `01` para IVA).
  * `cac:RegistrationAddress`: Dirección, `cbc:CityName`, `cbc:CountrySubentity` (Departamento), y código DANE en `cbc:CitySubdivisionName`.

### Paso 4.4: Identificación del Adquiriente (`AccountingCustomerParty`)
* **Ruta:** `/Invoice/cac:AccountingCustomerParty/cac:Party`
* **Procedimiento Especial (Consumidor Final):**
  * Si el comprador es anónimo, se utiliza `cbc:CompanyID` = `222222222222` con `@schemeID="13"` (Cédula de ciudadanía). `RegistrationName` debe ser "consumidor final".
* **Procedimiento Estándar (NIT o Cédula):**
  * Misma estructura que el Emisor, adaptada al RUT del cliente. 
  * **Regla Crítica:** Si el tipo de documento es "31" (NIT), el sistema valida exhaustivamente que el Dígito de Verificación corresponda matemáticamente al NIT.

### Paso 4.5: Medios y Plazos de Pago (`PaymentMeans` y `PaymentTerms`)
* **Ruta:** `/Invoice/cac:PaymentMeans`
* **Nodos Obligatorios:**
  * `cbc:ID`: Medio de pago (Ej. `1` Contado, `2` Crédito).
  * `cbc:PaymentMeansCode`: Código de la tabla DIAN (Ej. `10` Efectivo, `42` Transferencia bancaria).
  * `cbc:PaymentDueDate`: (Opcional, pero requerido si es a crédito) Fecha límite de pago.

### Paso 4.6: Líneas de Detalle de la Factura (`InvoiceLine`) - ITERATIVO
Este paso se repite por cada ítem (producto/servicio) a facturar. Todo cálculo parcial se consolida aquí.

* **Ruta:** `/Invoice/cac:InvoiceLine`
* **Nodos Obligatorios y Reglas por Línea:**
  * `cbc:ID`: Número secuencial (1, 2, 3...).
  * `cbc:InvoicedQuantity`: Cantidad mayor a `0.00`. Debe incluir el código de unidad de medida `@unitCode` (Ej. `EA` para unidades, `KGM` para kilogramos).
  * `cbc:LineExtensionAmount`: Subtotal de la línea = (Precio Unitario * Cantidad) - Descuentos Línea + Recargos Línea.
  * **(Opcional) Descuentos/Recargos (`cac:AllowanceCharge`):** Si aplica a la línea, se detalla si es cargo (`cbc:ChargeIndicator="true"`) o descuento (`false`), su base y su monto.
  * **Impuestos de la Línea (`cac:TaxTotal`):** Sumatoria de IVA, INC o retenciones exclusivas del ítem. 
    * `cbc:TaxAmount`: Valor del impuesto.
    * `cbc:TaxableAmount`: Base gravable.
    * `cbc:Percent`: Tarifa porcentual (Ej. `19.00`).
  * `cac:Item`:
    * `cbc:Description`: Descripción detallada.
    * `cac:StandardItemIdentification`: Código de estándar (GTIN, UNSPSC).
  * `cac:Price / cbc:PriceAmount`: Valor unitario nominal del producto (Sin impuestos).

### Paso 4.7: Consolidación de Tributos Globales (`TaxTotal` y `WithholdingTaxTotal`)
* **Ruta:** `/Invoice/cac:TaxTotal`
* **Regla Estricta:** El valor reportado aquí es la **sumatoria matemática exacta** de todos los `TaxTotal` informados en las líneas de detalle (`InvoiceLine`).
* Se debe agrupar por cada tipo de impuesto (Ej. IVA general al 19%, IVA al 5%).
* **Postcondición:** Si el cálculo global no cuadra con la sumatoria de las líneas (Tolerancia permitida de 2 COP), la DIAN arrojará el código de rechazo `TAX-TOTAL-ERROR`.

### Paso 4.8: Totales Monetarios Legales (`LegalMonetaryTotal`)
El cierre aritmético del documento. Representa lo que debe pagar el cliente.

* **Ruta:** `/Invoice/cac:LegalMonetaryTotal`
* **Algoritmo de Consolidación:**
  1. `cbc:LineExtensionAmount`: Suma total de los `LineExtensionAmount` de todas las líneas.
  2. `cbc:TaxExclusiveAmount`: Subtotal base imponible (Suma de bases gravables).
  3. `cbc:TaxInclusiveAmount`: Subtotal base imponible + Impuestos generados (IVA, INC).
  4. `cbc:AllowanceTotalAmount`: Suma de descuentos globales.
  5. `cbc:ChargeTotalAmount`: Suma de recargos globales.
  6. `cbc:PayableAmount`: TOTAL A PAGAR = `TaxInclusiveAmount` - Descuentos Globales + Recargos Globales - Anticipos (`PrepaidAmount`).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha cerrado en memoria, cuenta con el CUFE válido, el QR estructurado y la estructura canónica intacta.
2. **Estado Firmado:** El XML es procesado por un componente de firma que inyecta el `ds:Signature`. El archivo ya no puede ser alterado ni en un solo byte (o la firma perderá validez).
3. **Estado Empaquetado:** El archivo XML se comprime en formato `.zip` junto a sus validaciones internas, listo para ser consumido por el manual de *Transmisión a la DIAN (Web Services)*.
4. **Respuesta Esperada:** El documento espera un `ApplicationResponse` por parte de la DIAN donde el elemento `ResponseCode` sea `02` (Documento Validado por la DIAN) o un rechazo detallando la regla de negocio incumplida en `LineResponse/Response/Description`.

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `TAX-TOTAL-ERROR` (Discrepancia en impuestos), la solución es recalcular los montos dentro de cada `cac:InvoiceLine` y garantizar que el nodo global de impuestos sea la sumatoria exacta (respetando la tolerancia de $2.00) de los totales parciales.
- Si ocurre el error `Rechazo por Dígito de Verificación Invalido`, la solución es verificar el NIT del receptor y calcular nuevamente el atributo `@schemeAgencyName` aplicando el algoritmo del Módulo 11 aprobado por la DIAN.
- Si ocurre el error `CUFE Invalido / Algoritmo incorrecto`, la solución es revisar la concatenación estricta de la cadena fuente (`NumFactura` + `Fecha` + `Hora` + `Valores` + `NITs` + `Clave Técnica`) asegurando que no haya espacios adicionales y que el hash final resultante sea SHA-384.
- Si ocurre el error `XML mal formado o Firma Invalida (Documento Alterado)`, la solución es garantizar que ningún proceso intermedio modifique el archivo, ni siquiera un espacio o salto de línea, después de haber superado el paso inmutable de la firma electrónica.
- Si ocurre el error `Resolución Invalida o Rango Consumido`, la solución es detener inmediatamente la generación de facturas, tramitar una nueva numeración en el portal MUISCA de la DIAN y actualizar los parámetros `sts:InvoiceControl` en el componente.

---

## 7. Tablas de Referencia y Anexos Técnicos

**Perfiles y Códigos de Operación UBL (CustomizationID)**
| Documento | Uso principal | CustomizationID | ProfileID | Tipo Código Identificador |
| :--- | :--- | :--- | :--- | :--- |
| Factura Estándar | Venta general (bienes/servicios) | 10 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Mandato | Ventas a través de terceros | 11 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Transporte | Servicios de carga/pasajeros | 12 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura Cambiaria | Operaciones de divisa | 13 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |
| Factura AIU | Obras civiles e ingeniería | 09 | DIAN 2.1: Factura Electrónica de Venta | CUFE (SHA-384) |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Factura Electrónica de Venta (Invoice) UBL 2.1, cálculos CUFE, estructura de extensiones DIAN XML y reglas de validación: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/08_manual_factura_electronica_venta.md

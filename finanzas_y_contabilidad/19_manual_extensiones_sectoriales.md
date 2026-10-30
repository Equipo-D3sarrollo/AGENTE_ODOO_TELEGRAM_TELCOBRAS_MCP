<!-- Path: finanzas_y_contabilidad/19_manual_extensiones_sectoriales.md -->

# 🧩 Manual Operativo 19: Extensiones y Datos Sectoriales (Condicional) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Arquitectura de Datos y Sectores Regulados  
**Rol Requerido:** Analista Funcional Sectorial, Desarrollador Backend, Especialista de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual únicamente si el motor de facturación debe generar documentos para clientes que operan en sectores altamente regulados con requisitos tributarios o informativos extra, tales como el Sector Salud (MinSalud - RIPS), Sector Transporte de Carga, Operaciones de Intermediación (Mandatos), Control Cambiario (compra y venta de divisas por profesionales cambiarios), Exportaciones/Moneda Extranjera, Cárnicos, Sector Fiduciario o Notarios (Anexo 1.9, Suplementos H, I y K).

---

# MANUAL DE EXTENSIONES Y DATOS SECTORIALES (CONDICIONAL)

**ID del Manual:** DIAN-UBL2.1-M19  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN) + Resoluciones Sectoriales  
**Formato de Salida:** Nodos `ext:UBLExtensions`, `cac:AdditionalDocumentReference`, `cac:AgentParty`  
**Propósito:** Definir el mapeo estructural y algorítmico para inyectar metadatos específicos exigidos por entidades reguladoras ajenas a la DIAN (como el Ministerio de Salud o el Ministerio de Transporte) dentro del estándar UBL 2.1, sin romper la validación criptográfica y fiscal de la factura base.

---

## 1. PROCEDIMIENTO GENERAL

El estándar UBL 2.1 adoptado por la DIAN está diseñado para el comercio general. Sin embargo, ciertos sectores económicos requieren reportar información adicional en sus facturas electrónicas. Para esto, la DIAN habilitó mecanismos de extensibilidad:
1. **Cambio de Perfil (`CustomizationID`):** Para indicar a la DIAN que la factura obedece a un esquema sectorial (Ej. Operaciones en Salud).
2. **Nodos de Referencia (`AdditionalDocumentReference`):** Para referenciar documentos que amparan o soportan la transacción; es un grupo opcional y sus códigos los asigna el facturador (Anexo 1.9, num. 13.2). La información de la remesa de transporte no va aquí sino en `cac:AdditionalItemProperty` (Anexo 1.9, num. 19.2).
3. **Mapeo de Terceros (Mandato):** Para separar fiscalmente quién genera la factura (Mandatario) de quién recibe los ingresos (Mandante).
4. **Extensión Sectorial (`ext:UBLExtension`):** Grupo opcional de cabecera `CustomTagGeneral/Interoperabilidad` para reportar información adicional de un sector, con `Group/@schemeName` (nombre del sector), `Collection` y grupos `AdditionalInformation` con `Name` y `Value`; opcionalmente `InteroperabilidadPT` con la URL de descarga de documentos complementarios (sin credenciales; no se puede usar para que el adquiriente descargue el `AttachedDocument`) y el Web Service para la entrega de eventos (Anexo 1.9, Suplemento H, num. 18).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Parámetros de Entidad Habilitados:** El motor debe poder recibir en su JSON/API de entrada banderas booleanas sectoriales (ej. `is_health_sector: true`, `is_mandate: true`).
2. **Catálogos Adicionales:** Bases de datos precargadas con los catálogos de Ministerios correspondientes (ej. Códigos CUPS, CIE-10 para salud; Códigos de aduana para exportación).
3. **Flexibilidad del Validador XML:** El *schema validator* local (XSD) debe estar actualizado con los esquemas de extensiones avalados por la DIAN para evitar rechazos en el proceso de pre-firma.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de No Interferencia:** Ninguna extensión sectorial (`ext:ExtensionContent`) puede alterar o sobreescribir los nodos obligatorios de la DIAN (CUFE, QR, Firma, Totales Monetarios). Las extensiones se añaden al final del documento o en los bloques definidos.
- **Regla de Sector Salud (MinSalud):** Si se factura en el sector salud (Resolución 2275 de 2023), es de obligatorio cumplimiento la generación concurrente de los RIPS (Registro Individual de Prestación de Servicios) vinculados a la factura electrónica. Según el Anexo 1.9, el Ministerio de Salud reglamenta su sector bajo el estándar UBL 2.1 con grupos, elementos y atributos existentes en el anexo o definiciones propias; la URL `http://url.minsalud.gov.co/facturacion-electronica` rige una vez el Ministerio de Salud lo informe a los actores regulados (Anexo 1.9, num. 19.1). *La Resolución 2275 de 2023 y los RIPS no están en el Anexo 1.9.*
- **Regla del Ingreso a Terceros (Mandato):** En facturación por mandato, el NIT del facturador que firma electrónicamente es el del intermediario, pero los ingresos, retenciones e impuestos se declaran a nombre del NIT del mandante, el cual debe ir explícitamente en el XML. El tipo de operación es `11` (Mandatos) y el mandante se informa a nivel de ítem, un mandante por ítem (Anexo 1.9, num. 19.2; reglas FBA01 y FBA04).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (MAPEO SECTORIAL)

### Paso 4.1: Sector Salud (FEV-Salud / MinSalud)
Aplica para clínicas, hospitales, IPS y profesionales independientes de la salud.

- **Perfil de Customización (`CustomizationID`):** Debe modificarse. El valor estándar `10` cambia a los códigos asignados para salud según la operación, definidos en la tabla 13.1.5.1.1 Tipos de operación Salud de la Caja de Herramientas (Anexo 1.9, num. 14.1 y 14.2). El código `11` corresponde a Mandatos, no a salud (Anexo 1.9, num. 19.2).
- **Extensiones (Ruta XML):** `/Invoice/ext:UBLExtensions/...` (Se crea una nueva extensión específica para el Ministerio de Salud, según lo que reglamente el Ministerio de Salud; Anexo 1.9, num. 19.1).
- **Datos a inyectar:**
  - Código del Prestador de Servicios de Salud (REPS).
  - Modalidad de pago y contrato (Capitación, Evento, PGP).
  - Copagos, cuotas moderadoras y anticipos. *Según el Anexo 1.9, `PayableAmount` = `TaxInclusiveAmount` - `AllowanceTotalAmount` + `ChargeTotalAmount` (regla FAU14), y desde la versión 1.8 los anticipos (`PrepaidPayment`) son informativos y no se restan del `PayableAmount` (num. 11.1.9.2).*
  - Número de póliza o autorización.
- **Relación RIPS:** Debe generarse un identificador único que cruce la factura (XML) con el paquete JSON de los RIPS.

### Paso 4.2: Sector Transporte de Carga Terrestre
Aplica para empresas fleteras y logística.

- **Tipo de Operación:** `CustomizationID` = `12`, para el sector de transporte de carga cuando las operaciones se registran en el RNDC (Anexo 1.9, num. 19.2).
- **Información de la Remesa (`cac:InvoiceLine/cac:Item/cac:AdditionalItemProperty`):** El Ministerio de Transporte requiere esta información, contenida dentro de los 3 `cac:AdditionalItemProperty` que se permiten para las facturas de transporte de carga terrestre (Anexo 1.9, num. 19.2):
  - **Número Radicado de Aceptación de la Remesa:** Consecutivo único nacional que controla el RNDC; numérico de 1 hasta 100.000 millones.
  - **Número de Remesa:** Consecutivo interno de cada empresa de transporte; alfanumérico de hasta 15 caracteres; puede manejar prefijos.
  - **Cantidad transportada:** Numérico sin decimales.
  - **Unidad de medida:** `KGM` (Kilogramos) o `GLL` (Galones). El `@unitCode` de `cbc:ValueQuantity` es obligatorio cuando `CustomizationID` = `12` (regla FBF05).
  - **Valor flete:** Numérico sin decimales; valor del flete definido para la remesa.
  - **Tipo servicio:** `0` servicio adicional no incluido en el flete de la remesa; `1` remesa de transporte registrada en el RNDC.
  - **Orden de compra:** Número de la orden de servicio, orden de compra o remisión según consecutivo del Generador; pueden especificarse varias.
- **Registro Transporte (`/Invoice/cac:InvoiceLine/cbc:ID/@schemeID`):** `0` Servicio adicional, `1` Remesa de transporte registrada en el RNDC (tabla 13.5.1). Los conceptos de `cbc:Name` se toman de la tabla 13.5.2 Remesa Transporte de la Caja de Herramientas (Anexo 1.9, num. 14.7 y 19.2).
- **Información del Ítem (`cac:InvoiceLine`):** La descripción de la línea de factura debe detallar el trayecto (Origen - Destino), peso facturado, y el código del servicio asociado.

### Paso 4.3: Contratos de Mandato (Facturación por cuenta de terceros)
Aplica para inmobiliarias, agencias de viaje, consorcios y *marketplaces*.

- **Configuración del Emisor (`AccountingSupplierParty`):** Aquí van los datos del "Mandatario" (quien opera el software, expide y firma el XML).
- **Inyección del Mandante (`cac:Party` / `cac:AgentParty`):** Se debe mapear el nodo de intermediación donde se identifica con NIT y Nombre al verdadero dueño del ingreso ("Mandante"). La ruta es `/Invoice/cac:InvoiceLine/cac:Item/cac:InformationContentProviderParty/cac:PowerOfAttorney/cac:AgentParty/cac:PartyIdentification/cbc:ID`, con `@schemeAgencyID` = `195`, `@schemeAgencyName` = `CO, DIAN (Dirección de Impuestos y Aduanas Nacionales)`, el DV en `@schemeID` si `@schemeName` = `31`, y el tipo de documento en `@schemeName` (Anexo 1.9, FBA01 a FBA09). Si se informa `InformationContentProviderParty`, son obligatorios `PowerOfAttorney` y `AgentParty`; si el tipo de operación es mandatos y no se informa la identificación del mandante, la DIAN rechaza (FBA02, FBA03 y FBA05).
- **Manejo de Impuestos (`TaxTotal`):** Los tributos se calculan y reportan a nombre del Mandante. El XML exige un desglose preciso para evitar que la DIAN asuma esos impuestos como obligaciones del Mandatario.
- **Líneas de Factura:** Es posible tener facturas "Mixtas" donde una línea pertenece a un Mandante A, otra al Mandante B, y otra es ingreso propio del Mandatario (Comisión). Cada línea (`InvoiceLine`) permite especificar un mandante distinto si es necesario. La tabla 13.3.13 Mandatos (`@schemeID`) identifica a nivel de línea si el bien o servicio corresponde a un ingreso propio o a un ingreso recibido para terceros (Anexo 1.9, num. 14.5.12).
- **Eventos:** Cuando un evento (ApplicationResponse) es generado a través de un mandato, su `cbc:Note` debe contener `Nombre del Mandatario OBRANDO EN NOMBRE Y REPRESENTACION DE Nombre del mandante` (Anexo 1.9, AAD11).

### Paso 4.4: Exportaciones y Control Cambiario
Aplica para empresas que venden bienes o servicios al exterior en moneda extranjera. *El control cambiario de los profesionales cambiarios (compra y venta de divisas) se detalla en el Paso 4.5.*

- **Tipo de Documento (`InvoiceTypeCode`):** `02` (exportación). El código `CustomizationID` `11` corresponde a Mandatos, no a exportación (Anexo 1.9, num. 19.2; reglas FAK19, FAZ04 y FAZ05).
- **Moneda del Documento (`DocumentCurrencyCode`):** Moneda de negociación (Ej. `USD`, `EUR`).
- **Tasa de Cambio (`cac:PaymentExchangeRate`):** Obligatorio informar si la divisa base es diferente a COP (Anexo 1.9, regla FAR01).
  - `SourceCurrencyCode`: Moneda extranjera (USD). Debe ser igual a `cbc:DocumentCurrencyCode` (FAR02).
  - `SourceCurrencyBaseRate`: Debe ser `1.00` (FAR03).
  - `TargetCurrencyCode`: COP (FAR04).
  - `TargetCurrencyBaseRate`: Debe ser `1.00` (FAR05).
  - `CalculationRate`: Tasa Representativa del Mercado (TRM) del día o pactada. Valor reportado * tasa de cambio = valor en la divisa de `TargetCurrencyCode` (FAR06).
  - `Date`: Fecha de la tasa de cambio, en que se acordó la tasa (FAR07).
- **Impuestos (IVA):** Se emite con código `01` (IVA), tarifa `0.00%` (Exento).
- **Comprador (`AccountingCustomerParty`):** El `TaxLevelCode` del cliente en el exterior suele ser `R-99-PN`, utilizando la identificación fiscal de su país o un NIT genérico (ej. `444444044`). *El Anexo 1.9 no indica el NIT genérico; en exportación (`InvoiceTypeCode` = `02`) con valor total superior a 100 UVT aplican las reglas del grupo `PartyTaxScheme` del adquiriente (regla FAK19).*
- **Ítems:** En factura internacional (`InvoiceTypeCode` = `02`) se deben informar la marca (`cbc:BrandName`) y el modelo (`cbc:ModelName`) del artículo; si faltan, la DIAN notifica (Anexo 1.9, FAZ04 y FAZ05).
- **Extensión para la Representación Gráfica (opcional):** Para que la representación gráfica que genere el sistema muestre los valores en la divisa diferente a COP, se informa la UBLExtension `Interoperabilidad` con `Group/@schemeName` = `Exportación`. El grupo es opcional y no tiene reglas de validación, pero debe mantener el estándar UBL (Anexo 1.9, Suplemento K, num. 21).

### Paso 4.5: Control Cambiario (Profesionales Cambiarios)
- **Tipo de Operación:** `CustomizationID` `15` (venta de divisas) o `16` (compra de divisas) (Anexo 1.9, num. 19.3).
- **Extensión Obligatoria:** `//ext:UBLExtension[2]/ext:ExtensionContent/DebidaDiligencia`, con `Name` = literal `Cod_Diligencia` y `Value` = código de la tabla 18.3 Control Cambiario - Debida Diligencia de la Caja de Herramientas. Si `CustomizationID` es `15` o `16` y no se informa el grupo, o el literal o el código no son válidos, la DIAN rechaza (Anexo 1.9, reglas FADD01 a FADD05).

### Paso 4.6: Cárnicos, Sector Fiduciario y Notarios
- **Cárnicos:** Los mataderos, al facturar a sus clientes, pueden informar en el suplemento el NIT o documento de identificación del proveedor del ganado. Los nombres de los atributos son sugerencias (Anexo 1.9, num. 19.4).
- **Sector Fiduciario:** El anexo presenta un ejemplo elaborado a partir de información de Asofiduciarias; los nombres de los atributos son sugerencias (Anexo 1.9, num. 19.5).
- **Notarios:** Al facturar actos notariales correspondientes a enajenaciones de inmuebles a título oneroso o gratuito, se debe detallar el número de la matrícula inmobiliaria y el valor comercial de la transacción en los elementos `Name` (FBF02) y `Value` (FBF03) (Anexo 1.9, num. 19.6).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Aprobación Multientidad:** Una vez aceptada por la DIAN (Regreso de ApplicationResponse), el mismo documento XML (y sus RIPS asociados si es sector salud) es legalmente válido para radicación ante las EPS o el pagador institucional.
2. **Contabilidad Clara:** El motor garantiza que los ingresos de terceros (Mandato) no alteren el estado de resultados del facturador principal en el ERP, afectando únicamente las cuentas por pagar/cobrar.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `El CustomizationID no corresponde al tipo de operación`, verifique el mapeo en el API inicial. Las operaciones de Salud tienen CustomizationIDs propios (tabla 13.1.5.1.1); la exportación se identifica con `InvoiceTypeCode` `02`, no con `CustomizationID` `11`, que es Mandatos.
- Si ocurre rechazo en Sector Salud indicando `Valor de cuota moderadora no coincide con los totales`, valide la fórmula matemática de la DIAN: el `PayableAmount` debe ser igual a `TaxInclusiveAmount` - `AllowanceTotalAmount` + `ChargeTotalAmount` (regla FAU14); los anticipos son informativos y no se restan (Anexo 1.9, num. 11.1.9.2). Los copagos en salud no son impuestos, actúan como pagos anticipados.
- Si la DIAN emite una alerta sobre la tasa de cambio, revise el grupo `cac:PaymentExchangeRate`: `SourceCurrencyCode` igual a `DocumentCurrencyCode`, bases en `1.00`, `TargetCurrencyCode` COP, `CalculationRate` y `Date` informados (reglas FAR01 a FAR07). El anexo admite en `CalculationRate` el valor de la TRM o la tasa acordada entre las partes (FAR06).
- Si en Mandatos aparece la regla `El NIT del mandante no puede ser igual al del facturador` (mensaje no listado en el Anexo 1.9; las reglas de mandatos son FBA01 a FBA09), asegúrese de no enviar los datos de la propia empresa en los nodos de `AgentParty`.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipos de Operación y Códigos de Identificación (CustomizationID)**
| Código | Tipo de Operación | Caso de Uso Principal |
| :--- | :--- | :--- |
| **10** | Estándar | Facturación general nacional (valor por defecto). |
| **11** | Mandatos | Facturación por cuenta de terceros. |
| **09** | AIU | Contratos de servicio tipo AIU; la nota del ítem Administración es obligatoria (regla FAV03). |
| **12** | Transporte | Transporte de carga registrado en el RNDC (num. 19.2). |
| **15** | Venta de divisas | Profesionales cambiarios; exige `DebidaDiligencia` (num. 19.3). |
| **16** | Compra de divisas | Profesionales cambiarios; exige `DebidaDiligencia` (num. 19.3). |
| **XX** | Salud (FEV) | Regido por la última resolución del Ministerio de Salud; tabla 13.1.5.1.1 de la Caja de Herramientas. |

*La exportación no es un `CustomizationID`: se identifica con `InvoiceTypeCode` = `02` (Anexo 1.9, reglas FAK19, FAZ04 y FAZ05).*

*(Nota: Los códigos CustomizationID pueden variar según la subversión del anexo técnico, consulte siempre la tabla paramétrica vigente de la DIAN).*

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Extensiones Sectoriales, Sector Salud DIAN, RIPS MinSalud, Contratos de Mandato, Facturación Terceros, Exportaciones, Control Cambiario, Transporte RNDC, Notarios, CustomizationID, TRM: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/19_manual_extensiones_sectoriales.md

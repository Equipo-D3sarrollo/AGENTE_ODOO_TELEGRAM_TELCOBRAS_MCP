<!-- Path: finanzas_y_contabilidad/28_manual_exportacion_control_cambiario.md -->

# 🌎 Manual Operativo 28: Facturación de Exportación, Moneda Extranjera y Control Cambiario - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Comercio Exterior y Finanzas  
**Rol Requerido:** Analista de Facturación, Área Contable, Desarrollador de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando deba emitir una factura electrónica de exportación (`InvoiceTypeCode` = `02`), una factura en moneda diferente al peso colombiano (COP), o una factura de compra o venta de divisas de un profesional cambiario (`CustomizationID` = `15` o `16`).

---

# MANUAL DE EXPORTACIÓN, MONEDA EXTRANJERA Y CONTROL CAMBIARIO

**ID del Manual:** DIAN-UBL2.1-M28  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Factura electrónica (`Invoice`) y notas con datos de exportación, tasa de cambio y extensiones sectoriales  
**Propósito:** Recoger lo que el Anexo Técnico 1.9 establece para la factura de exportación (reglas FAK19, FAZ04 y FAZ05), la moneda y la tasa de cambio (FAD15a, FAD15b, FAR01 a FAR07, FGB01 a FGB07), la extensión de exportación (Suplemento K, num. 21) y el control cambiario (num. 19.3, FADD01 a FADD05). Este manual amplía los pasos 4.4 y 4.5 del Manual 19.

---

## 1. PROCEDIMIENTO GENERAL

- **Exportación:** La factura de exportación se identifica con `cbc:InvoiceTypeCode` = `02` (Anexo 1.9, reglas FAK19, FAZ04 y FAZ05). La tabla completa de tipos de documento (13.1.3) está en la Caja de Herramientas.
- **Moneda:** La divisa de toda la factura se informa en `cbc:DocumentCurrencyCode`, según ISO 4217, y debe ser la misma en todos los `@currencyID` (Anexo 1.9, reglas FAD15a y FAD15b). La tasa de cambio se informa en `cac:PaymentExchangeRate` (FAR01 a FAR07) y, si se necesita una tasa adicional, en `cac:PaymentAlternativeExchangeRate` (FGB01 a FGB07).
- **Control cambiario:** Para las ventas y compras de divisas de los profesionales cambiarios se usan los tipos de operación `15` y `16`, respectivamente, con la extensión `DebidaDiligencia` (Anexo 1.9, num. 19.3).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Tablas de la Caja de Herramientas:** Tipos de documento (13.1.3), tipos de operación (13.1.5.1), monedas ISO 4217 (13.3.3) y Control Cambiario - Debida Diligencia (18.3), en `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\Tablas Referenciadas` (Anexo 1.9, num. 14 y 19.3).
2. **Datos del Adquiriente del Exterior:** Grupo `PartyTaxScheme` del adquiriente con su nombre registrado (Anexo 1.9, regla FAK19).
3. **Tasa de Cambio:** Valor de la TRM o de la tasa acordada entre las partes, y la fecha en que se fijó (Anexo 1.9, FAR06 y FAR07).
4. **Datos del Artículo:** Marca y modelo de los artículos para factura internacional (Anexo 1.9, FAZ04 y FAZ05).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla del Adquiriente (FAK19, R):** No se encuentra el grupo `PartyTaxScheme` si no se informa y se cumple al menos una de estas situaciones: adquiriente persona jurídica (`AdditionalAccountID` = `1`); adquiriente persona natural (`AdditionalAccountID` = `2`); operación de exportación (`//cbc:InvoiceTypeCode` = `02`); valor total de la factura (`//LegalMonetaryTotal/cbc:PayableAmount`) superior a 100 UVT. *La regla tiene efecto `R` en la tabla de reglas, aunque su texto empieza con "Notificación".* En las notas aplican `CAK19` y `DAK19` (R) con `CreditNoteTypeCode` / `DebitNoteTypeCode` = `02`.
- **Regla de Marca y Modelo (FAZ04 y FAZ05, N):** La marca (`cbc:BrandName`) y el modelo (`cbc:ModelName`) del artículo deben informarse en caso de factura internacional (`InvoiceTypeCode` = `02`).
- **Regla de Moneda (FAD15a y FAD15b, R):** `DocumentCurrencyCode` debe estar en ISO 4217 y ser el mismo valor para todos los `@currencyID`, excepto la información que se exprese en la UBLExtension `/Invoice/ext:UBLExtensions/ext:UBLExtension/ext:ExtensionContent/CustomTagGeneral/TotalesCop`.
- **Regla de Control Cambiario (FADD03, R):** Si `CustomizationID` es `15` o `16` y no se informa el grupo `DebidaDiligencia`, la DIAN rechaza (Anexo 1.9, num. 19.3.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Marcar la Factura como Exportación
- `cbc:InvoiceTypeCode` = `02` (Anexo 1.9, FAK19, FAZ04 y FAZ05).
- Informar el grupo `cac:AccountingCustomerParty/cac:Party/cac:PartyTaxScheme` con el nombre registrado del adquiriente; si el adquiriente persona jurídica desea usar su nombre comercial en el archivo, se usa `cac:AccountingCustomerParty/cac:Party/cac:PartyName/cbc:Name` (Anexo 1.9, FAK19 y FAK20).
- Informar en cada ítem `cbc:BrandName` y `cbc:ModelName` (FAZ04 y FAZ05).
- En la codificación del ítem se puede usar la partida arancelaria (Sistema Armonizado). En las tablas de la nota débito el anexo indica que, para factura de exportación, se puede informar la subpartida arancelaria a 10 dígitos (Anexo 1.9, num. 15.3; DAZ10 y DAZ11; ver Manual 26).

### Paso 4.2: Informar la Tasa de Cambio (`cac:PaymentExchangeRate`)
**Según la tabla de reglas de validación de la factura (Anexo 1.9, num. 8.2):**

| Regla | Y | Campo | Regla |
| :--- | :--- | :--- | :--- |
| FAR01 | R | `PaymentExchangeRate` | Obligatorio informar si la divisa base es diferente a COP. Seguir definición estándar del UBL para este grupo. |
| FAR02 | R | `SourceCurrencyCode` | Rechazo si no es igual a `cbc:DocumentCurrencyCode` (remite a la regla FAD15b). |
| FAR03 | R | `SourceCurrencyBaseRate` | Base monetaria de la divisa extranjera para el cambio. Debe ser `1.00`. |
| FAR04 | R | `TargetCurrencyCode` | Divisa a la cual se hace la conversión. Debe ir diligenciado en COP si `DocumentCurrencyCode` es diferente a COP; rechazo si no corresponde a un valor de la lista. |
| FAR05 | R | `TargetCurrencyBaseRate` | Base monetaria para la conversión. Debe ser `1.00`. |
| FAR06 | R | `CalculationRate` | Valor de la tasa de cambio entre las divisas; por ejemplo, para USDCOP puede ser el valor de la TRM o la tasa acordada entre las partes. Es la base para la conversión: valor reportado * tasa de cambio = valor en la divisa de `TargetCurrencyCode`. |
| FAR07 | R | `Date` | Fecha en la que se fijó (acordó) la tasa de cambio. |

*Advertencia: la tabla de definición de la factura (num. 6.1) describe este grupo de otra forma: obligatorio "si la divisa base es COP y se quiere adicionalmente informar los valores totales en una moneda diferente al COP", con `SourceCurrencyCode` igual a COP y `TargetCurrencyCode` en moneda extranjera. Las dos tablas del anexo se contradicen; la tabla de reglas es la que contiene los rechazos (`R`) que aplica la validación. Conviene confirmar el caso con la DIAN antes de emitir.*

*En las notas crédito y débito las reglas equivalentes son `CAR02` (R) y `CAR03` a `CAR07` (N); `DAR01` y `DAR02` (R) y `DAR03` a `DAR07` (N) (Anexo 1.9, num. 8.3 y 8.4).*

### Paso 4.3: Informar una Tasa de Cambio Adicional (opcional)
(Anexo 1.9, FGB01 a FGB07)
- El grupo `cac:PaymentAlternativeExchangeRate` es un método alternativo para informar conversiones a otras divisas: si el emisor necesita informar una tasa de conversión adicional, puede usar este grupo.
- Tiene los mismos campos que `PaymentExchangeRate` (`SourceCurrencyCode`, `SourceCurrencyBaseRate`, `TargetCurrencyCode`, `TargetCurrencyBaseRate`, `CalculationRate`, `Date`); sus reglas `FGB02` a `FGB07` tienen efecto de notificación (`N`).

### Paso 4.4: Mostrar los Valores en Otra Divisa en la Representación Gráfica (Suplemento K)
(Anexo 1.9, num. 21)
- Si se desea usar la representación gráfica que genere el sistema para las facturas de exportación, se informa la UBLExtension "interoperabilidad" para que muestre los valores de la tasa de cambio de la moneda diferente al COP.
- El grupo es opcional y no cuenta con reglas de validación, pero debe mantener el estándar UBL.
- Estructura: `ext:UBLExtension/ext:ExtensionContent/CustomTagGeneral/Interoperabilidad/Group` con `@schemeName` = `Exportación`; dentro, `Collection` con `@schemeName` = `DATOS ADICIONALES` y grupos `AdditionalInformation` con `Name` (nombre del campo, de 1 a 100 caracteres) y `Value` (valor del campo, de 1 a 100 caracteres).
- Ejemplos del anexo de pares Name/Value: "responsable/Encargado" – "Pepito Perez"; "Lugar de Salida" – "Bogotá"; "Medio de transporte" – "Barco".

### Paso 4.5: Factura de Compra o Venta de Divisas (Control Cambiario)
(Anexo 1.9, num. 19.3)
- **Tipo de operación:** `CustomizationID` = `15` para ventas de divisas y `16` para compras de divisas que efectúen los profesionales cambiarios. Los tipos de operación permitidos son los de la columna "Código" de la tabla 13.1.5.1 de la Caja de Herramientas.
- **Extensión obligatoria:** `//ext:UBLExtension[2]/ext:ExtensionContent/DebidaDiligencia`, con un código de la columna "Código" de la tabla 18.3 Control Cambiario - Debida Diligencia (Caja de Herramientas).

| ID | Campo | Oc | Regla |
| :--- | :--- | :--- | :--- |
| FADD01 | `UBLExtension` | 0..1 | Grupo para información comercial adicional estandarizada del sector. |
| FADD02 | `ExtensionContent` | 0..1 | Grupo que contiene la información personalizada del sector. |
| FADD03 | `DebidaDiligencia` | 1..N | Grupo definido para el sector cambiario. Rechazo si `CustomizationID` = `15` o `16` y no se informa este grupo. |
| FADD04 | `Name` | 1..1 | Se debe informar el literal `Cod_Diligencia`. Rechazo si no se informa. |
| FADD05 | `Value` | 1..1 | Código de la tabla 18.3 Control Cambiario - Debida Diligencia. Rechazo si no se informa un código válido de la lista. |

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Exportación Identificada:** La factura tiene `InvoiceTypeCode` = `02`, el grupo `PartyTaxScheme` del adquiriente y la marca y el modelo de los artículos.
2. **Moneda Coherente:** Todos los `@currencyID` coinciden con `DocumentCurrencyCode`, y la tasa de cambio está informada con su fecha.
3. **Control Cambiario Completo:** Las facturas con `CustomizationID` `15` o `16` llevan la extensión `DebidaDiligencia` con un código válido.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre `FAK19`, la solución es informar el grupo `PartyTaxScheme` del adquiriente (Anexo 1.9, num. 8.2).
- Si ocurre `FAD15a`, la solución es usar un código de moneda ISO 4217; si ocurre `FAD15b`, todos los `@currencyID` deben ser iguales a `DocumentCurrencyCode` (Anexo 1.9, num. 8.2).
- Si ocurre `FAR01`, falta el grupo `PaymentExchangeRate` cuando la divisa es diferente a COP; si ocurre `FAR02` a `FAR07`, revisar la moneda de origen, las bases en `1.00`, la moneda destino, la tasa y la fecha (Anexo 1.9, num. 8.2).
- Si se recibe `FAZ04` o `FAZ05` (notificación), falta la marca o el modelo del artículo en la factura internacional.
- Si ocurre `FADD03`, `FADD04` o `FADD05`, la solución es informar la extensión `DebidaDiligencia` con `Name` = `Cod_Diligencia` y un `Value` válido de la tabla 18.3 (Anexo 1.9, num. 19.3.1).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Identificadores de la Operación**
| Caso | Elemento | Valor | Referencia |
| :--- | :--- | :--- | :--- |
| Exportación | `cbc:InvoiceTypeCode` | `02` | FAK19, FAZ04, FAZ05 |
| Venta de divisas | `cbc:CustomizationID` | `15` | Num. 19.3 |
| Compra de divisas | `cbc:CustomizationID` | `16` | Num. 19.3 |
| Moneda del documento | `cbc:DocumentCurrencyCode` | ISO 4217 | FAD15a, FAD15b |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Factura electrónica de exportación (InvoiceTypeCode 02), moneda extranjera y tasa de cambio (PaymentExchangeRate, PaymentAlternativeExchangeRate, TRM), extensión de exportación del Suplemento K y control cambiario de profesionales cambiarios (CustomizationID 15 y 16, DebidaDiligencia): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/28_manual_exportacion_control_cambiario.md

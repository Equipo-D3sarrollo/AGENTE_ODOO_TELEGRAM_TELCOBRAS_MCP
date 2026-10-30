<!-- Path: finanzas_y_contabilidad/18_manual_gestion_catalogos.md -->

# 🗂️ Manual Operativo 18: Gestión de Catálogos y Tablas Paramétricas DIAN - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Arquitectura de Datos y Parametrización  
**Rol Requerido:** Administrador de Base de Datos, Analista Funcional, Desarrollador Backend  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual durante la fase de despliegue inicial del software o cuando la DIAN publique actualizaciones normativas que modifiquen las listas de valores permitidos (códigos de impuestos, unidades de medida, medios de pago, municipios, responsabilidades fiscales, etc.).

---

# MANUAL DE GESTIÓN DE CATÁLOGOS DIAN

**ID del Manual:** DIAN-UBL2.1-M18  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Tablas maestras sincronizadas y mapeo JSON/XML.  
**Propósito:** Definir el modelo de datos, la estructura de almacenamiento y el procedimiento de actualización para garantizar que todos los nodos UBL del XML consuman códigos estandarizados y avalados por las tablas paramétricas oficiales de la DIAN.

---

## 1. PROCEDIMIENTO GENERAL

La facturación electrónica en Colombia es un modelo estrictamente estructurado. La DIAN no permite texto libre para definir variables clave (ej. no se puede escribir "Transferencia" o "Caja de Cartón"). El sistema debe implementar un repositorio central de "Catálogos" donde se almacenan las tablas paramétricas oficiales. Cuando un usuario factura, selecciona de listas desplegables (UI) y el motor backend extrae el "Código Técnico" correspondiente para inyectarlo en el XML.

**Fuente oficial de las tablas:** En el Anexo Técnico 1.9 las tablas de códigos no están en el PDF: se encuentran en la Caja de Herramientas de la DIAN, `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\Tablas Referenciadas`, en formato Excel `.xlsx` (Anexo 1.9, num. 13 y 14). El catálogo del sistema debe cargarse desde esos archivos.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Estructura de Base de Datos (Maestras):** El esquema de la base de datos debe contener tablas paramétricas independientes (ej. `dian_catalog_taxes`, `dian_catalog_payment_methods`, `dian_catalog_cities`).
2. **Interfaz de Administración:** El sistema debe proveer una UI (backend administrativo) donde el equipo de soporte pueda cargar actualizaciones de catálogos sin necesidad de modificar el código fuente del motor.
3. **Mapeo ERP vs. DIAN:** Si el sistema se integra con un ERP preexistente (ej. Odoo, SAP, Dynamics), se requiere una tabla puente ("Homologación") para traducir los códigos internos del ERP a los códigos exigidos por la DIAN.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Estricta Concordancia:** El motor de integración debe rechazar internamente cualquier documento que intente utilizar un código no presente en la última versión de los catálogos del Anexo 1.9.
- **Regla de Listas Internacionales:** Además de las tablas propias de la DIAN, el software debe gestionar catálogos internacionales adoptados por la norma: Divisas (ISO 4217), Países (ISO 3166-1 alpha-2), y Unidades de Medida (UN/ECE Rec 20). El anexo también adopta: Lenguaje ISO 639-1 (códigos de 2 letras en los atributos `@languageID`), Departamentos ISO 3166-2:CO (`cbc:CountrySubentity`, `cbc:CountrySubentityCode`), Municipios con fuente DANE, Código Postal (fuente www.codigopostal.gov.co, archivo `Códigos_Postales.xlsx`, columna E) e INCOTERMS (`cbc:LossRiskResponsibilityCode`) (Anexo 1.9, num. 14.5.2, 14.5.7 y 14.6). En los elementos `IdentificationCode` de país se usa el código alfa-2 (Anexo 1.9, num. 14.6.1).
- **Regla de Consistencia Tributaria:** Los códigos de responsabilidad fiscal (O-13, O-15, O-23, O-47) deben corresponder exactamente con el Régimen Tributario reportado (ej. no se puede enviar responsabilidad `O-13` Gran Contribuyente si se reporta como "Régimen Simplificado / No responsable").

---

## 4. DETALLE DE LOS PROCEDIMIENTOS Y MAPEO DE CATÁLOGOS

### 4.1. Catálogo de Tributos (Impuestos, Retenciones y Tasas)
Mapea los códigos para IVA, INC, ICA, retenciones en la fuente, etc.
- **Ruta XML Afectada:** `cac:TaxScheme/cbc:ID` (columna Identificador), `cac:TaxScheme/cbc:Name` (columna Nombre) de la tabla 13.2.2 Tributos de la Caja de Herramientas (Anexo 1.9, num. 14.4.1.1.1)
- **Mapeo Principal:**
  - `01` - IVA (Impuesto sobre las Ventas)
  - `02` - IC (Impuesto al Consumo)
  - `03` - ICA (Impuesto de Industria y Comercio)
  - `04` - INC (Impuesto Nacional al Consumo)
  - `05` - ReteIVA (Retención sobre el IVA)
  - `06` - ReteFuente (Retención en la Fuente por Renta)
  - `07` - ReteICA (Retención sobre el ICA)
  - `ZZ` - No Aplica / Nombre del tributo (Para facturas exentas que requieren este nodo por validación).
- *Nota: del PDF del anexo solo se confirman los códigos `01` (IVA), `04` (INC) y `03` (ICA), usados en el CUFE (num. 11.1.2), y el uso de `ZZ` / `No aplica` en el `cac:TaxScheme` del adquiriente consumidor final (reglas FAK40 y FAK41). Los demás códigos deben verificarse contra la tabla 13.2.2. Las retenciones (ReteIVA, ReteFuente) se informan en `cac:WithholdingTaxTotal`, no en `TaxTotal` (Anexo 1.9, FAT01).*

### 4.2. Catálogo de Medios y Formas de Pago
Define cómo y cuándo se paga la factura.

**Formas de Pago (`PaymentMeans/ID`):**
- `1` - Contado (Pago inmediato).
- `2` - Crédito (Requiere el elemento `cac:PaymentMeans/cbc:PaymentDueDate` indicando la fecha de vencimiento; si no se informa, la DIAN rechaza) (Anexo 1.9, regla FAN04). La tabla es la 13.3.4.1 Formas de Pago de la Caja de Herramientas (Anexo 1.9, num. 14.5.4.1).

**Medios de Pago (`PaymentMeans/PaymentMeansCode`):**
- `10` - Efectivo
- `41` - Transferencia Débito Bancaria
- `42` - Consignación Bancaria
- `48` - Tarjeta de Crédito
- `49` - Tarjeta Débito
- `97` - Compensación (Clearing entre empresas)
- `ZZ` - Mutuo acuerdo
- *Nota: la tabla oficial es la 13.3.4.2 Medios de Pago de la Caja de Herramientas (Anexo 1.9, num. 14.5.4.2; regla FAN03); estos códigos no están en el PDF del anexo y deben verificarse contra ella. El manual 08 describe el código `42` como "Transferencia bancaria" y este manual como "Consignación Bancaria".*

### 4.3. Catálogo de Unidades de Medida (UN/ECE Rec 20)
Determina en qué presentación se vende el ítem.
- **Ruta XML Afectada:** Atributo `@unitCode` en `cbc:InvoicedQuantity` (regla FAV05, rechazo), `cac:Price/cbc:BaseQuantity` (regla FBB05, rechazo), `cbc:BaseUnitMeasure` de los tributos por unidad (FAS10, FAX10) y `cbc:ValueQuantity` de `AdditionalItemProperty` en transporte (FBF05). `cbc:PriceAmount` no lleva `@unitCode`: lleva `@currencyID` (Anexo 1.9, FBB03). La tabla es la 13.3.6 Unidades de Cantidad de la Caja de Herramientas (Anexo 1.9, num. 14.5.6).
- **Mapeo Principal (Ejemplos Frecuentes):**
  - `94` - Unidad (Goods) / Pieza
  - `EA` - Cada Uno (Each)
  - `HUR` - Hora (Servicios)
  - `DAY` - Día
  - `KGM` - Kilogramo
  - `MTR` - Metro
  - `LTR` - Litro

### 4.4. Catálogo de Responsabilidades Fiscales (RUT)
Información tributaria extraída de la casilla 53 del RUT del emisor y adquiriente.
- **Ruta XML Afectada:** `cac:PartyTaxScheme/cbc:TaxLevelCode` (Dentro de `AccountingSupplierParty` y `AccountingCustomerParty`).
- **Mapeo (Valores concatenados separados por punto y coma si son múltiples, ej. `O-13;O-15`; tabla 13.2.6.1 de la Caja de Herramientas) (Anexo 1.9, FAJ26 y FAK26; num. 14.4.1.1.6):**
  - `O-13` - Gran Contribuyente
  - `O-15` - Autorretenedor
  - `O-23` - Agente de retención en el impuesto sobre las ventas
  - `O-47` - Régimen Simple de Tributación
  - `R-99-PN` - No Responsable (Persona Natural). Para consumidor final se debe informar `R-99-PN` (Anexo 1.9, FAK26).

### 4.5. Catálogo de División Política y Administrativa (DIVIPOLA)
Códigos para identificar los municipios de origen y destino, avalados por el DANE.
- **Ruta XML Afectada:** `cac:Address/cbc:ID` (código del municipio, 5 caracteres), `cbc:CityName` (nombre del municipio), `cbc:CountrySubentity` (nombre del departamento) y `cbc:CountrySubentityCode` (código del departamento, 2 caracteres) (Anexo 1.9, FAJ09 a FAJ12). El código del departamento debe coincidir con el del municipio (Anexo 1.9, regla CDG01). El anexo no usa `cbc:CitySubdivisionName` para el código del municipio.
- **Mapeo (Ejemplo):**
  - Departamento (Valle del Cauca): `76`
  - Municipio (Cali): `76001` (El XML requiere el código de 5 dígitos del DANE completo).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Integridad Referencial:** Cuando se procesa un JSON de entrada para generar una factura, el motor consulta las tablas maestras y valida que cada código exista en el catálogo de la versión 1.9.
2. **Actualización Segura:** Los cambios introducidos por nuevas resoluciones se cargan en la base de datos sin afectar el motor central de cálculos matemáticos o de firma digital.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si la DIAN rechaza con `FAD15a: El valor del elemento DocumentCurrencyCode no es un valor válido` (la `Regla 90` corresponde a un documento procesado anteriormente, Anexo 1.9, num. 8.1), verifique que el catálogo de divisas utilice la ISO 4217 de tres letras en mayúscula (ej. `COP`, `USD`, `EUR`). No se permite `pesos` o `$`.
- Si se presenta un error en el código del municipio (`FAJ09`, `FAK09` o `CDG01`), confirme que el catálogo interno no está enviando el nombre de la ciudad en texto libre en `cac:Address/cbc:ID`, sino el código numérico asignado por el DANE a dicho municipio, y que el código del departamento coincida con el del municipio.
- Si hay un error de `TaxLevelCode` en un cliente internacional (el anexo solo indica expresamente `R-99-PN` para consumidor final, regla FAK26), verifique que al exportar, el código del cliente esté parametrizado como `R-99-PN` o la equivalencia técnica dictaminada para el exterior (usualmente código país y NIT genérico `444444044`).
- Si recibe error en `unitCode`, es un problema típico en servicios. Los servicios no se venden en "Cajas", se debe usar el catálogo UN/ECE para tiempo (ej. `HUR` para hora) o servicios genéricos (`WSD` o `ZZ` dependiendo del anexo vigente).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipos de Catálogos DIAN y Frecuencia de Actualización**
| Nombre del Catálogo | Estándar Origen | Frecuencia de Cambio (Estimada) | Tipo de Validacion DIAN |
| :--- | :--- | :--- | :--- |
| **Tributos** | DIAN (Anexo Técnico) | Baja (Cambios normativos mayores) | Estricta (Rechazo inmediato) |
| **Unidades de Medida** | ONU (UN/ECE) | Muy Baja | Estricta (FAV05, FBB05) |
| **Municipios y Deptos** | DANE / ISO 3166-2:CO | Media (Creación de nuevos mcpios) | Rechazo en el código de municipio del emisor (FAJ09) y en la coincidencia departamento-municipio (CDG01); notificación en el nombre del departamento del emisor (FAJ11) y en el código de municipio del adquiriente (FAK09) |
| **Medios de Pago** | DIAN | Baja | Estricta (FAN02, FAN03) |
| **Monedas / Países** | ISO (4217 / 3166) | Nula | Estricta |

**Tablas Paramétricas del Anexo 1.9 y su Ubicación** (todas en `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\Tablas Referenciadas`, formato `.xlsx`; Anexo 1.9, num. 13 y 14)
| Tabla | Campo XML |
| :--- | :--- |
| 13.1.1 Ambiente de Destino del Documento | `cbc:ProfileExecutionID`, `cbc:UUID/@schemeID` |
| 13.1.2 Algoritmo (`CUFE-SHA384`, `CUDE-SHA384`; valores en el PDF) | `cbc:UUID/@schemeName` |
| 13.1.3 Tipo de Documento | `cbc:InvoiceTypeCode`, `cbc:CreditnoteTypeCode` |
| 13.1.5.1 Tipos de operación (y 13.1.5.1.1 Salud) | `cbc:CustomizationID` (factura) |
| 13.1.5.2 / 13.1.5.3 Tipos de operación de Nota Crédito / Nota Débito | `cbc:CustomizationID` (notas) |
| 13.1.6 Tipos Eventos | Eventos |
| 13.2.1 Documento de identificación | `cbc:CompanyID/@schemeName`, `sts:ProviderID/@schemeName` |
| 13.2.2 Tributos | `cac:TaxScheme/cbc:ID`, `cac:TaxScheme/cbc:Name` |
| 13.2.3 Tipo de organización jurídica | `cbc:AdditionalAccountID` |
| 13.2.4 / 13.2.5 Concepto de Corrección Notas Crédito / Débito | `cac:DiscrepancyResponse/cbc:ResponseCode` |
| 13.2.6.1 Responsabilidades fiscales | `cbc:TaxLevelCode` |
| 13.2.6.2 Para el grupo PartyTaxScheme | `cac:PartyTaxScheme` |
| 13.3.1 Eventos de un Documento Electrónico | `cac:Response/cbc:ResponseCode`, `cbc:Description` |
| 13.3.2 Lenguaje (ISO 639) | `@languageID` |
| 13.3.3 Moneda (ISO 4217) | `@currencyID` |
| 13.3.4.1 Formas de Pago / 13.3.4.2 Medios de Pago | `cac:PaymentMeans/cbc:ID`, `cbc:PaymentMeansCode` |
| 13.3.5 Productos | `@schemeID`, `@schemeName`, `@schemeAgencyID` |
| 13.3.6 Unidades de Cantidad | `@unitCode` |
| 13.3.7 Condiciones de entrega (INCOTERMS) | `cbc:LossRiskResponsibilityCode` |
| 13.3.9 Códigos de descuento | Descuentos |
| 13.3.10 Códigos para precios de referencia | Muestras y regalos sin valor comercial |
| 13.3.11 Tarifas por Impuesto | `cbc:Percent` del grupo `TaxTotal` |
| 13.3.12 Concepto de Reclamo | `@listID` del evento de reclamo |
| 13.3.13 Mandatos | `@schemeID` a nivel de línea |
| 13.4.1 Países (ISO 3166-1) | `cbc:IdentificationCode` |
| 13.4.2 Departamentos (ISO 3166-2:CO) | `cbc:CountrySubentity`, `cbc:CountrySubentityCode` |
| 13.4.3 Municipios | `cbc:CityName` |
| Códigos Postales (`Códigos_Postales.xlsx`, columna E) | `cbc:PostalZone` |
| 13.5.1 Registro Transporte / 13.5.2 Remesa Transporte | `@schemeID` / `cbc:Name` (transporte de carga) |
| 13.5.3 Mandatos Profesional de cambios | Profesionales de cambio |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Catálogos DIAN, Tablas Paramétricas, DIVIPOLA, UN/ECE Rec 20, ISO 4217, Códigos de Impuestos, Medios de Pago, TaxLevelCode, Caja de Herramientas: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/18_manual_gestion_catalogos.md

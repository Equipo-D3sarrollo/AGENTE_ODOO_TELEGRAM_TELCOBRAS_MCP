<!-- Path: finanzas_y_contabilidad/20_manual_fundamentos_convenciones.md -->

# 📐 Manual Operativo 20: Fundamentos y Convenciones del Anexo Técnico 1.9 - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual antes de construir o validar cualquier documento electrónico (Factura, Nota Crédito, Nota Débito, ApplicationResponse o AttachedDocument), para aplicar las convenciones generales que el Anexo Técnico 1.9 define para todos ellos: aproximaciones aritméticas, holguras, valores negativos, cantidades, lectura de las tablas de definición y de reglas, tipos de dato, tamaños, ubicación estándar de la información común, manejo de fechas y relación entre la representación gráfica y el XML.

---

# MANUAL DE FUNDAMENTOS Y CONVENCIONES DEL ANEXO TÉCNICO 1.9

**ID del Manual:** DIAN-UBL2.1-M20  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Reglas transversales aplicables a todos los documentos XML  
**Propósito:** Recoger las convenciones generales de la sección 5 del Anexo Técnico 1.9, que aplican a todos los documentos e instrumentos electrónicos, para que los manuales de generación (08 a 12) y de validación (14 y 15) se apliquen sobre una base común.

---

## 1. PROCEDIMIENTO GENERAL

El Anexo Técnico 1.9 describe el formato de los documentos e instrumentos electrónicos. El formato es un subconjunto del Universal Business Language – UBL, del cual se utilizan cinco tipos de documentos: `Invoice` (factura), `CreditNote` (Nota Crédito), `DebitNote` (Nota Débito), `ApplicationResponse` (Registro de Evento) y `AttachedDocument` (Contenedor Electrónico) (Anexo 1.9, num. 5).

El anexo impone dos requisitos sobre la información (Anexo 1.9, num. 5, 5.1 y 5.2):
1. **Confiabilidad:** presentar de forma clara e inequívoca la estructura de cómo y dónde debe ser incluida la información necesaria para informar correctamente la operación de venta de bienes y/o prestación de servicios a la autoridad tributaria.
2. **Calidad:** aclarar las limitaciones al brindar información en un elemento, tanto de manera lógica (por ejemplo, no permitir una dirección de cliente en Colombia en una operación de exportación) como aritmética (por ejemplo, el total de la factura debe corresponder a la suma de sus líneas, considerados los ajustes por descuentos o recargos).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Marco Normativo:** El anexo describe los documentos para las validaciones previstas en la Ley 2155 de 2021 y el Capítulo 4 del Título 1 de la Parte 6 del Libro 1 del Decreto 1625 de 2016 (modificado, sustituido y adicionado por los Decretos 358 de 2020 y 442 de 2023). Para el registro de las facturas como título valor (RADIAN) se aplican los artículos 772, 773 y 774 del Código de Comercio y el numeral 9 del artículo 2.2.2.53.2 del Decreto 1074 de 2015, modificado por el artículo 1 del Decreto 1154 de 2020. La generación, transmisión, validación, entrega y recibo de los requisitos del título valor deben cumplir el artículo 34 de la Resolución 000085 de 2022 y el anexo técnico (Anexo 1.9, num. 5).
2. **Función de Redondeo:** El motor debe implementar la fórmula de redondeo *round-half-to-even* (definición en `https://www.w3.org/TR/xpath-functions-31/#func-round-half-to-even`), que corresponde a la norma técnica colombiana NTC 3711 (Norma técnica internacional JIS Z 8401) (Anexo 1.9, num. 5.2.1, nota 2).
3. **Reloj del Servidor:** El "Date-Time" del computador o servidor donde se calcula la firma es controlado con el reloj atómico de la entidad, porque de él sale `xades:SigningTime` (Anexo 1.9, num. 5.9.1). La hora legal colombiana la determina el reloj de la Superintendencia de Industria y Comercio (http://horalegal.inm.gov.co/) (Anexo 1.9, num. 10.5.8).
4. **Tablas de Definición y de Reglas:** El equipo debe saber leer las tablas de definición (capítulo 6) y de reglas de validación (capítulo 8) del anexo según las convenciones de este manual (Anexo 1.9, num. 5.5 a 5.8).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Documento Rechazado:** Es el incumplimiento formal de alguna de las reglas de validación del anexo, que tiene como consecuencia que la DIAN no valide el documento electrónico (Anexo 1.9, num. 5.2).
- **Documento Validado:** Proceso informático de la DIAN que verifica las reglas de validación y da como resultado el no rechazo; como consecuencia, el documento electrónico se valida (Anexo 1.9, num. 5.2).
- **Validaciones de Notificación:** Cuando la información solicitada no corresponde a reglas que generan rechazo, se denominan "validaciones de notificación", para las cuales el proveedor tecnológico y/o facturador electrónico podrá informar la opción "otros o alternativa" definida en el anexo (Anexo 1.9, num. 5.2, nota 1).
- **Momentos de Validación:** La DIAN aplica las reglas al recibir en línea y al recibir en contingencia un documento para validación, ya sea del facturador electrónico directamente, a través de un Proveedor Tecnológico (PT) o a través de la solución gratuita de facturación electrónica (Anexo 1.9, num. 5.2).
- **Valores Monetarios Positivos (VLR01):** Todos los valores monetarios se expresan en valores positivos; la naturaleza del signo la otorga el concepto del campo, no el valor. El único campo permitido para expresar valores negativos es `PayableRoundingAmount` (Anexo 1.9, num. 5.3.1).
- **Tarifas (VLR01):** Las tarifas tributarias deben ser iguales o superiores a 0.00; no se permiten valores negativos (Anexo 1.9, num. 5.3.2).
- **Cantidades (FAV04b):** Las cantidades de los productos y servicios deben ser valores positivos mayores a 0.00 (Anexo 1.9, num. 5.4).
- **Campos Opcionales:** Los grupos o campos de información opcionales NO deben ser informados de manera obligatoria; si se decide informarlos, tienen las validaciones que señala el anexo (Anexo 1.9, num. 5.10).
- **Representación Gráfica:** La información presentada en la representación gráfica de los documentos electrónicos debe estar en el XML del documento electrónico correspondiente (Anexo 1.9, num. 5.10).
- **Prevalencia del Anexo:** Las ejemplificaciones del anexo muestran el uso de los campos XML en casuísticas particulares; NO son ejemplos de documentos electrónicos. Si hay diferencias entre las ejemplificaciones y el anexo técnico, siempre prevalece el anexo técnico (Anexo 1.9, num. 5.10).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONVENCIONES PASO A PASO)

### Paso 4.1: Aproximaciones Aritméticas (Redondeo)
Las reglas de validación con operaciones aritméticas sobre valores monetarios se aproximan según la cantidad de decimales definidos para el campo respectivo (Anexo 1.9, num. 5.2.1):

| Dígito siguiente al dígito menos significativo | Redondeo |
| :--- | :--- |
| Entre 0 y 4 | Mantener el dígito menos significativo |
| Entre 6 y 9 | Incrementar el dígito menos significativo |
| 5, y el segundo dígito siguiente al dígito menos significativo es cero o par | Mantener el dígito menos significativo |
| 5, y el segundo dígito siguiente al dígito menos significativo es impar | Incrementar el dígito menos significativo |

- **Diferencia por redondeo:** Si con este procedimiento hay diferencia entre los totales calculados y la suma de los parciales para el valor total de un documento, se usa el elemento `/Invoice/LegalMonetaryTotal/cbc:PayableRoundingAmount` para informar la diferencia (Anexo 1.9, num. 5.2.1).

### Paso 4.2: Holguras en Valores Monetarios
- **Holgura general:** Los elementos que definen valores monetarios permiten una tolerancia de error de `+` o `-` `2.00` (Anexo 1.9, num. 5.1.1.1).
- **Aproximación del IVA:** Según el artículo 1.3.1.1.1 del Decreto 1625 de 2016, cuando el valor del impuesto generado implique el pago de fracciones de diez pesos ($10.00), dicha fracción se podrá aproximar al múltiplo de diez pesos más cercano. Por eso, en los elementos `cbc:TaxAmount` en que se informe el IVA se permite una tolerancia de más o menos cinco pesos ($5.00) para la aproximación al múltiplo de diez pesos ($10.00) más cercano. Esta aproximación aplica para la factura electrónica de venta, las notas débito, las notas crédito y los demás documentos electrónicos que se derivan de la factura, en relación con el impuesto sobre las ventas (Anexo 1.9, num. 5.1.1.2).

### Paso 4.3: Identificadores de los Documentos Electrónicos
- El CUFE (facturas de venta) y el CUDE (demás documentos electrónicos) son los identificadores de los documentos electrónicos (Anexo 1.9, num. 5.2.1.1).
- Para la referencia cruzada entre documentos se incluye la etiqueta `cbc:UUID`, con el identificador y su atributo en las rutas `/Invoice/cbc:UUID` y `/Invoice/cbc:UUID/@schemeName` (Anexo 1.9, num. 5.2.1.1).
- Para los documentos tipo ApplicationResponse se establece la definición `CUDE-SHA384` (Anexo 1.9, num. 5.2.1.1).
- El cálculo de cada identificador se detalla en el Manual 15 (Anexo 1.9, num. 11).

### Paso 4.4: Lectura de las Tablas de Definición del Formato XML
Las columnas de las tablas de definición (capítulo 6 del anexo) significan lo siguiente (Anexo 1.9, num. 5.5.1, Tabla 1):

| Columna | Descripción |
| :--- | :--- |
| ID | Identificador único del elemento o atributo; sirve de base para la codificación de notificaciones o errores de cada uno de ellos. |
| NS | Namespace al que pertenece el campo (ver tabla de namespaces). |
| Campo | Nombre del elemento o grupo de elementos. Los atributos inician con el símbolo `@`. |
| Descripción | Descripción del elemento o grupo y su significado. |
| T | Tipo de elemento (Paso 4.5, Tabla 2). |
| F | Tipo de dato (Paso 4.5, Tabla 3). |
| Tam | Tamaño del elemento (Paso 4.6, Tabla 4). |
| Padre | Nombre del grupo que contiene este elemento o grupo. |
| Ocu | Cantidad de posibles ocurrencias del elemento o grupo. |
| Observaciones | Observaciones importantes sobre el campo, incluyendo listas de valores posibles y validaciones si aplican. |
| V | Versión en que el campo fue introducido en el formato, o en que fue modificado por última vez. |

**Namespaces (columna NS):**
| Prefijo | Namespace |
| :--- | :--- |
| `cbc` | `urn:oasis:names:specification:ubl:schema:xsd:CommonBasicComponents-2` |
| `cac` | `urn:oasis:names:specification:ubl:schema:xsd:CommonAggregateComponents-2` |
| `ext` | `urn:oasis:names:specification:ubl:schema:xsd:CommonExtensionComponents-2` |
| `sts` | `dian:gov:co:facturaelectronica:Structures-2-1` |
| `xades` | `http://uri.etsi.org/01903/v1.3.2#` |
| `xades141` (xmlns) | `http://uri.etsi.org/01903/v1.4.1#` |
| `ds` | `http://www.w3.org/2000/09/xmldsig#` |

La definición de los prefijos utilizados debe mencionarse a nivel de la cabecera de los documentos `Invoice`, `CreditNote`, `DebitNote`, `ApplicationResponse` o `AttachedDocument` (Anexo 1.9, num. 5.5.1).

**Ocurrencias (columna Ocu):**
- `1..1`: el elemento o grupo es obligatorio, con máximo una ocurrencia.
- `0..1`: el elemento o grupo es facultativo (posible de no ser informado), con máximo una ocurrencia.
- `1..N`: el elemento o grupo es obligatorio, con máximo N ocurrencias.
- `0..N`: el elemento o grupo es facultativo, con máximo N ocurrencias, donde N es cualquier valor.

### Paso 4.5: Tipos de Campo y Tipos de Dato
**Tipos de campo (columna T)** (Anexo 1.9, num. 5.6, Tabla 2):
| Tipo | Descripción |
| :--- | :--- |
| G | Grupo de elementos y/o grupos de elementos |
| E | Elemento |
| A | Atributo de un elemento |

**Tipos de dato (columna F)** (Anexo 1.9, num. 5.6, Tabla 3):
| Tipo | Descripción |
| :--- | :--- |
| A | Alfanumérico: son aceptados los caracteres UNICODE permitidos en el XML; corresponde al tipo `xsd:normalizedString`. |
| B | Booleano: acepta solamente los literales `true` y `false` (en minúsculas). |
| N | Numérico: solamente son aceptados los números `0` a `9`, el punto de separación decimal y las señales `+` y `-`. |
| F | Fecha: formato `YYYY-MM-DD`, de acuerdo con la norma ISO 8601-2 (YYYY: año; MM: mes; DD: día). |
| H | Hora: formato de tiempo universal coordinado de acuerdo con la norma ISO 8601-2, con hh (hora de 00 a 23), mm (minutos), ss (segundos), la señal `+` o `-` y la diferencia en horas y minutos con relación a la hora (UTC-05:00). Ejemplo del anexo: las dos y treinta de la tarde en Bogotá se informa como `14:30:00 UTC-05:00`. |
| I | Intervalo de tiempo: formato `<Fecha Inicial>/<Fecha Final>`, ambas con formato "F". Ejemplo: el período entre el 01 y el 30 de septiembre de 2018 se informa como `2018-09-01/2018-09-30`. |
| X | Documento XML. |

### Paso 4.6: Tamaños de los Elementos
- **Tamaño fijo:** No admite información con otro número de posiciones diferente al establecido; la información siempre tiene exactamente el mismo tamaño (Anexo 1.9, num. 5.7).
- **Tamaño variable:** Admite un rango de posiciones de un mínimo a un máximo. Si la información no usa el máximo, no se deben incluir caracteres de relleno, como ceros o blancos (Anexo 1.9, num. 5.7).
- **Tamaño mínimo 0:** Los elementos de tamaño variable con mínimo 0 admiten ser informados sin contenido; en ese caso el emisor declara que no existe o no está disponible la información correspondiente (Anexo 1.9, num. 5.7).
- **Modificaciones:** El tamaño de los campos es susceptible a modificaciones, que podrán ser implementadas por parte del facturador según lo que se requiera de acuerdo con su modelo de negocio (Anexo 1.9, num. 5.7).

**Formatos de tamaño (columna Tam)** (Anexo 1.9, num. 5.7, Tabla 4):
| Formato | Descripción |
| :--- | :--- |
| `x` | Tamaño exacto del elemento (ej.: `5`). |
| `x-y` | Tamaño mínimo "x", máximo "y" (ej.: `0-10`); es posible no expresar ningún valor, porque se permite el tamaño "0". |
| `x p n` | Tamaño exacto "x", con exactamente "n" casillas decimales (ej.: `11 p 4`). |
| `x p (n-m)` | Tamaño exacto "x", con entre "n" y "m" casillas decimales (ej.: `11 p (0-6)`). |
| `(x-y) p (n-m)` | Tamaño mínimo "x", máximo "y", con entre "n" y "m" casillas decimales (ej.: `1-11 p (0-6)`). Es obligatorio expresar algún valor, porque no se permite el tamaño "0". El número debe tener entre una (1) y once posiciones, aceptándose cualquier combinación desde once posiciones sin punto decimal hasta exactamente cuatro (4) posiciones antes del punto decimal y exactamente seis (6) después, pero la parte fraccionaria es opcional. |
| Valores separados por comas | El elemento se informa con tamaño de exactamente una de las opciones listadas (ej.: `1, 3, 5, 8` significa uno de estos cuatro tamaños fijos). |

**Ejemplos de valores numéricos** (Anexo 1.9, num. 5.7, Tabla 5):
| Formato | Para informar | Llenar el elemento con |
| :--- | :--- | :--- |
| `0-11 p (0-6)` | 1,105.13 | `1105.13` |
| `0-11 p (0-6)` | 1,105.137 | `1105.137` |
| `0-11 p (0-6)` | 1,105 | `1105` |
| `0-11 p (0-6)` | 0 | `0` |
| `0-11 p (0-6)` | Para no informar cantidad | Dejar el elemento vacío |
| `1-11` | 1,105 | `1105` |
| `1-11` | 0 | `0` |
| `1-11` | Para no informar cantidad | No es posible |

### Paso 4.7: Lectura de las Tablas de Reglas de Validación
Las columnas de las tablas de reglas (capítulo 8 del anexo) significan lo siguiente (Anexo 1.9, num. 5.8, Tabla 6):

| Columna | Descripción |
| :--- | :--- |
| Tipo | Categoría de la regla de validación. |
| # | Identificador de la regla de validación. |
| Campo | Nombre del campo en las tablas de formato. |
| Regla | Descripción de la regla de validación. |
| Cod | Código de mensaje correspondiente a la regla de validación. |
| Y | Efecto de la regla: `R` Rechazo (el procesamiento encontró problemas que impiden procesar la solicitud) o `N` Notificación (el procesamiento encontró indicios de potenciales problemas que no impiden procesar la solicitud). |
| Mensaje | Mensaje de respuesta como resultado de un rechazo o de una notificación. |
| V | Versión de las reglas de validación. |

El detalle de cómo interpretar las respuestas de la DIAN está en el Manual 14.

### Paso 4.8: Ubicación Estándar de la Información Común
Los nombres de grupos y namespaces pueden variar según el elemento padre o raíz, de acuerdo con el estándar UBL, pero la estructura se mantiene igual (Anexo 1.9, num. 5.9, Tabla 7):

| Dato | Elemento a utilizar |
| :--- | :--- |
| Nombre comercial de persona jurídica y nombre de persona natural | `..cac:Party/cac:PartyName` |
| Razón social de persona jurídica | `cac:PartyTaxScheme/cbc:RegistrationName` |
| NIT y otros documentos de identificación de una persona natural o jurídica | `..cac:PartyTaxScheme/cbc:CompanyID` |
| Dirección de una persona natural o jurídica | `cac:Party/cac:PhysicalLocation/cac:Address` |

### Paso 4.9: Manejo de las Fechas del Documento Electrónico
(Anexo 1.9, num. 5.9.1)

| Campo | Significado |
| :--- | :--- |
| `xades:SigningTime` | Fecha de realización del cálculo de la firma digital X509. Corresponde al "Date-Time" del computador o servidor donde se calcula la firma, controlado con el reloj atómico de la entidad. Es la fecha de generación del documento e instrumento electrónico y otorga la autenticidad del documento para efectos fiscales. |
| `cbc:IssueDate` | Fecha de emisión de la factura. Está relacionada con las fechas del `DueDate`, considerando la zona horaria de Colombia (-5). Validación de fecha calendario: la fecha de emisión debe estar en un rango apropiado con respecto a la fecha calendario. |
| `cbc:DueDate` | Fecha de vencimiento de la factura; debe estar asociada con las fechas negociadas o acordadas según los registros de `cac:PaymentMeans/cbc:PaymentDueDate`. |
| `cbc:ActualDeliveryDate` | Fecha de entrega del bien y/o prestación del servicio. |

**Reglas de validación relacionadas con las fechas:**
- La fecha de generación debe ser igual a la fecha de firma: `FAD09e` (factura), `CAD09e` (nota crédito), `DAD09e` (nota débito) y `AAD09e` (evento) (Anexo 1.9, num. 8.2, 8.3, 8.4 y 8.6).
- La fecha de emisión de la factura debe estar dentro de la vigencia de la autorización de numeración: `FAD09a` y `FAD09b` (Anexo 1.9, num. 8.2).
- Si la forma de pago es crédito (`cac:PaymentMeans/cbc:ID` = `2`) y no se informa `cbc:PaymentDueDate`, la DIAN rechaza: `FAN04` (Anexo 1.9, num. 8.2).
- Para el registro como título valor, si la factura no indica fecha de vencimiento se entiende que debe pagarse dentro de los treinta (30) días siguientes a la emisión (Anexo 1.9, num. 6.5.1).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Valores Consistentes:** Todos los valores monetarios del documento son positivos (salvo `PayableRoundingAmount`), con tarifas iguales o superiores a 0.00 y cantidades mayores a 0.00.
2. **Cálculos Cuadrados:** Los cálculos se redondearon con *round-half-to-even* y las diferencias de redondeo quedaron en `PayableRoundingAmount`, dentro de la holgura de `+/- 2.00` y, para el IVA, de `+/- 5.00`.
3. **Formato Conforme:** Cada campo respeta su tipo de dato, tamaño y ocurrencia según las tablas de definición.
4. **Fechas Coherentes:** La fecha de emisión coincide con la fecha de firma y con la vigencia de la numeración, y el vencimiento está asociado a la fecha de pago acordada.
5. **Representación Fiel:** Todo lo que muestra la representación gráfica está en el XML.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `VLR01` (los valores monetarios o porcentajes deben ser positivos), la solución es revisar que ningún valor monetario ni tarifa se envíe con signo negativo; el signo lo otorga el concepto del campo, y el único campo que admite negativo es `PayableRoundingAmount` (Anexo 1.9, num. 5.3 y 8.1).
- Si ocurre el error `FAV04b`, `CAV04b` o `DAV04b` (no se pueden expresar valores negativos en la cantidad), la solución es verificar que la cantidad de cada línea sea mayor a 0.00 (Anexo 1.9, num. 5.4).
- Si los totales no cuadran por centavos, la solución es aplicar *round-half-to-even* según los decimales del campo e informar la diferencia en `PayableRoundingAmount` (Anexo 1.9, num. 5.2.1).
- Si el IVA no cuadra por la aproximación al múltiplo de diez pesos, la solución es verificar que la diferencia esté dentro de la tolerancia de `+/- 5.00` en `cbc:TaxAmount` (Anexo 1.9, num. 5.1.1.2).
- Si ocurre el error `FAD09e`, `CAD09e`, `DAD09e` o `AAD09e` (la fecha de generación es diferente a la fecha de firma), la solución es generar y firmar el documento con la misma fecha de `cbc:IssueDate` (Anexo 1.9, num. 5.9.1 y 8).
- Si un campo opcional informado genera rechazo, la solución es corregirlo o no informarlo: los campos opcionales no son obligatorios, pero si se informan tienen las validaciones del anexo (Anexo 1.9, num. 5.10).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Resumen de Convenciones Transversales del Anexo 1.9**
| Convención | Regla | Referencia |
| :--- | :--- | :--- |
| Redondeo | *round-half-to-even* (NTC 3711); diferencias en `PayableRoundingAmount` | Num. 5.2.1 |
| Holgura monetaria | `+/- 2.00` | Num. 5.1.1.1 |
| Aproximación del IVA | `+/- 5.00` al múltiplo de `10.00` en `cbc:TaxAmount` | Num. 5.1.1.2 |
| Valores negativos | Solo `PayableRoundingAmount` (VLR01) | Num. 5.3.1 |
| Tarifas | Iguales o superiores a `0.00` (VLR01) | Num. 5.3.2 |
| Cantidades | Mayores a `0.00` (FAV04b) | Num. 5.4 |
| Fechas | `YYYY-MM-DD` (ISO 8601-2) | Num. 5.6, Tabla 3 |
| Horas | Con diferencia respecto a UTC-05:00 (ej. `14:30:00 UTC-05:00`) | Num. 5.6, Tabla 3 |
| Booleanos | `true` / `false` en minúsculas | Num. 5.6, Tabla 3 |
| Tamaño variable | Sin relleno de ceros o blancos | Num. 5.7 |
| Efecto de reglas | `R` Rechazo / `N` Notificación | Num. 5.8, Tabla 6 |
| Campos opcionales | No obligatorios; si se informan, se validan | Num. 5.10 |
| Representación gráfica | Su información debe estar en el XML | Num. 5.10 |
| Prevalencia | El anexo prevalece sobre las ejemplificaciones | Num. 5.10 |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Fundamentos y Convenciones del Anexo Técnico 1.9 DIAN: redondeo round-half-to-even, tolerancias, valores negativos, tipos de dato, tamaños, ocurrencias, ubicación estándar, fechas SigningTime, IssueDate, DueDate y ActualDeliveryDate, y representación gráfica: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/20_manual_fundamentos_convenciones.md

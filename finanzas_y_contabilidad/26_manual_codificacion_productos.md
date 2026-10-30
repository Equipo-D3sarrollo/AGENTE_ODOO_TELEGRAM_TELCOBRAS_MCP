<!-- Path: finanzas_y_contabilidad/26_manual_codificacion_productos.md -->

# 🏷️ Manual Operativo 26: Codificación de Productos y Servicios (UNSPSC, GTIN, Partidas Arancelarias y Códigos Libres) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Inventario y Facturación  
**Rol Requerido:** Analista de Facturación, Responsable de Maestro de Productos, Desarrollador de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando deba asignar o revisar el código estándar de los productos y servicios que se facturan electrónicamente (grupo `cac:StandardItemIdentification` de cada línea), escoger el estándar (UNSPSC, GTIN, partida arancelaria o código libre) o resolver rechazos de las reglas `FAZ09` a `FAZ15` y sus equivalentes en notas.

---

# MANUAL DE CODIFICACIÓN DE PRODUCTOS Y SERVICIOS

**ID del Manual:** DIAN-UBL2.1-M26  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Grupo `cac:Item/cac:StandardItemIdentification` de cada línea del documento  
**Propósito:** Recoger lo que el Anexo Técnico 1.9 establece sobre los códigos de productos (Suplemento E, num. 15), la tabla 13.3.5 Productos (num. 14.5.5) y las reglas de validación del ítem.

---

## 1. PROCEDIMIENTO GENERAL

Cada línea del documento identifica el artículo o servicio con un código de acuerdo con un estándar, informado en `cac:Item/cac:StandardItemIdentification/cbc:ID`, con los atributos `@schemeID`, `@schemeName`, `@schemeAgencyID` y `@schemeAgencyName` (Anexo 1.9, FAZ09 a FAZ15). El anexo describe cuatro tipos de codificación (Anexo 1.9, num. 15):
1. **UNSPSC** – Código Estándar de Productos y Servicios de Naciones Unidas (Colombia Compra Eficiente).
2. **GTIN** – Números Globales de Identificación de Productos (GS1).
3. **Partidas arancelarias** – Sistema Armonizado de Designación y Codificación de Mercancías (SA).
4. **Códigos libres o autónomos** – codificación propia de los sistemas de información de las empresas.

Adicionalmente, el vendedor puede identificar el artículo con su propio código en `cac:SellersItemIdentification` (Anexo 1.9, FAZ06 a FAZ08).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Tabla de Estándares:** La tabla 13.3.5 Productos (`@schemeID`, `@schemeName`, `@schemeAgencyID`) se encuentra en la Caja de Herramientas, ruta `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\Tablas Referenciadas`, en formato Excel `.xlsx` (Anexo 1.9, num. 14.5.5).
2. **Clasificador UNSPSC:** La tabla de códigos UNSPSC está en la Caja de Herramientas, ruta `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\`, archivo Excel `Clasificador de Bienes y Servicios.xls`; se debe usar el código de la columna G (Anexo 1.9, num. 15.1).
3. **Maestro de Productos:** Cada producto o servicio del sistema debe tener asignado al menos un código estándar, porque el grupo `StandardItemIdentification` debe ser informado (Anexo 1.9, regla FAZ09).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Obligatoriedad (FAZ09, R):** El grupo de identificación del artículo o servicio de acuerdo con un estándar debe ser informado. Son admitidas hasta tres codificaciones estándar para un mismo artículo. *En la tabla de definición de la factura este grupo aparece con ocurrencia `0..1`, pero la regla FAZ09 es de rechazo.*
- **Regla del Código (FAZ10, R):** El código debe estar de acuerdo con el estándar descrito en los atributos.
- **Regla del Estándar (FAZ11, R):** `@schemeID` debe corresponder a un valor de la columna `@schemeID` de la tabla 13.3.5.
- **Regla del Nombre del Estándar (FAZ12, R):** `@schemeName` debe corresponder al valor de la columna `@schemeName` de la tabla 13.3.5, el que sigue al `@schemeID` informado. Ejemplo del anexo: si `@schemeID` = `001`, el valor esperado en `@schemeName` es `UNSPSC`. Notificación si el código no existe en un estándar cerrado (ejemplo: UNSPSC) o si no sigue la regla de formación de un código abierto con valores verificables (ejemplos: GTIN, partidas arancelarias).
- **Regla de la Agencia (FAZ14, R):** Rechazo si `@schemeID` = `010` y `@schemeAgencyID` es diferente de `9`.
- **Sin validación:** `FAZ13` (`@schemeName`) y `FAZ15` (`@schemeAgencyName`, notificación) se marcan "Sin validación" en el anexo.
- **Notas Crédito y Débito:** Aplican las reglas equivalentes `CAZ09` a `CAZ11` y `DAZ09` a `DAZ11`, también de rechazo y con hasta tres codificaciones por artículo (Anexo 1.9, num. 8.3 y 8.4).
- **Exportación:** En las tablas de la nota débito el anexo indica que, para factura de exportación, se puede informar la subpartida arancelaria a 10 dígitos (Anexo 1.9, num. 6.3 y 8.4, DAZ10 y DAZ11).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Informar el Código en el XML
- **Ruta:** `/Invoice/cac:InvoiceLine/cac:Item/cac:StandardItemIdentification/cbc:ID`
- `@schemeID`: código del estándar, según la tabla 13.3.5 (FAZ11).
- `@schemeName`: nombre del estándar, según la tabla 13.3.5 (FAZ12). Ejemplo: `001` → `UNSPSC`.
- `@schemeAgencyID`: agencia del estándar; si `@schemeID` = `010`, debe ser `9` (FAZ14).
- `@schemeAgencyName`: nombre de la agencia (sin validación, FAZ15).
- Opcional: `cac:SellersItemIdentification/cbc:ID` (código del vendedor) y `cbc:ExtendedID` (código del vendedor para una subespecificación del artículo) (FAZ06 a FAZ08, notificación).

### Paso 4.2: Codificar con UNSPSC (Colombia Compra Eficiente)
(Anexo 1.9, num. 15.1)
- UNSPSC es una metodología uniforme de codificación para clasificar productos y servicios, con arreglo jerárquico y estructura lógica. La versión implementada en Colombia es la **UNSPSC V.14.080**, traducida al español.
- La guía de codificación está en `https://www.colombiacompra.gov.co/sites/cce_public/files/cce_documents/cce_guia_codificacion_bienes.pdf`.
- Los dos primeros dígitos de cada código representan la estructura lógica: **bienes** en los segmentos 10 al 60 y 95; **servicios** en los segmentos 70 a 94.

| Segmentos | Grupo |
| :--- | :--- |
| 10 al 15 | Materias primas |
| 20 al 27 | Equipo industrial |
| 30 al 41 | Componentes y suministros |
| 42 al 60 | Productos de uso final |
| 70 al 94 | Servicios |
| 95 | Terrenos, edificios, estructuras y vías |

- Ejemplos del anexo dentro de esos grupos: "Componentes y suministros para estructuras, edificación, construcción y obras civiles" (segmentos 30 al 41); "Difusión de tecnologías de información y telecomunicaciones" (42 al 60); "Servicios de edificación, construcción de instalaciones y mantenimiento" y "Servicios basados en ingeniería, investigación y tecnología" (70 al 94).
- El código a informar es el de la columna G del archivo `Clasificador de Bienes y Servicios.xls` de la Caja de Herramientas.

### Paso 4.3: Codificar con GTIN (GS1)
(Anexo 1.9, num. 15.2)
- El estándar GTIN lo regla GS1. Los GTIN identifican todos los artículos comerciales, incluidos productos y servicios que se venden, entregan y facturan en cualquier punto de la cadena de suministro. También se asigna GTIN a los servicios que pueden ser facturados, como el transporte o el almacenamiento.
- **Estructuras:** GTIN-8, GTIN-12, GTIN-13 y GTIN-14. Todas se almacenan en un campo de 14 dígitos; cuando hay menos de 14 números, se justifican a la derecha y se rellenan con ceros a la izquierda.
- **Composición:** prefijo GS1 (asignado por GS1 Global a cada organización miembro; no indica el país de producción), número de compañía, referencia del artículo (por lo general de 1 a 6 dígitos, no significativa) y dígito de control (último dígito, calculado a partir de los demás). La estructura GTIN-12 no contiene prefijo de organización miembro. En el GTIN-14, el indicador lleva el valor 1 a 8 para artículos de cantidad fija y 9 para artículos de cantidad variable.
- **Responsable de asignarlo:** por lo general el propietario de la marca (fabricante o proveedor, importador o mayorista, o minorista, según quién venda el producto con su marca registrada). El prefijo de compañía GS1 no puede venderse, alquilarse ni cederse.
- **GTIN-8:** solo para artículos que no pueden acomodar un código GTIN-13 o UPC-A, en los casos que describe el anexo (símbolo que excede el 25% del lateral más largo o el 12,5% del área de la etiqueta; etiqueta con lateral más largo menor a 40 cm² o área total menor a 80 cm²; productos cilíndricos de diámetro menor a 3 cm).
- Las guías de GS1 Colombia están en `https://www.gs1co.org/Portals/0/Contenido/Nueva%20gu%C3%ADa%20de%20identificaci%C3%B3n.pdf` y `https://www.gs1co.org/Portals/0/Contenido/Cartilla_GTIN14_GS1_Colombia.pdf`.

### Paso 4.4: Codificar con Partidas Arancelarias
(Anexo 1.9, num. 15.3)
- La clasificación arancelaria es un código numérico que se asigna a las mercancías que se importan o exportan; permite conocer los aranceles de importación, los trámites de exportación en origen, los requisitos de importación en destino o las medidas de política comercial que las afectan.
- Se rige por el Sistema Armonizado de Designación y Codificación de Mercancías (SA).

### Paso 4.5: Codificar con Códigos Libres o Autónomos
(Anexo 1.9, num. 15.4)
- Corresponden a una codificación propia de los sistemas de información de las empresas para la identificación inequívoca de los bienes o servicios que prestan.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Línea Identificada:** Cada línea tiene entre una y tres codificaciones estándar en `StandardItemIdentification`.
2. **Atributos Correctos:** `@schemeID` y `@schemeName` corresponden a la tabla 13.3.5, y si `@schemeID` = `010`, `@schemeAgencyID` = `9`.
3. **Maestro Actualizado:** El maestro de productos del sistema conserva el código estándar de cada ítem para reutilizarlo en facturas y notas.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre `FAZ09` (`CAZ09`, `DAZ09`), la solución es informar el grupo `StandardItemIdentification` en la línea, con hasta tres codificaciones.
- Si ocurre `FAZ10`, la solución es verificar que el código corresponda al estándar informado en los atributos.
- Si ocurre `FAZ11`, la solución es usar un `@schemeID` de la tabla 13.3.5 de la Caja de Herramientas.
- Si ocurre `FAZ12`, la solución es usar el `@schemeName` que corresponde al `@schemeID` en la tabla 13.3.5 (ejemplo: `001` → `UNSPSC`).
- Si ocurre `FAZ14`, la solución es informar `@schemeAgencyID` = `9` cuando `@schemeID` = `010`.
- Si un código GTIN de menos de 14 dígitos es rechazado, la solución es verificar su estructura y dígito de control según la guía de GS1; en la estructura de almacenamiento se justifica a la derecha con ceros a la izquierda (Anexo 1.9, num. 15.2).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Estándares de Codificación del Anexo 1.9**
| Estándar | Organismo / Fuente | Dónde está la tabla | Referencia |
| :--- | :--- | :--- | :--- |
| UNSPSC V.14.080 | Colombia Compra Eficiente / Naciones Unidas | `Clasificador de Bienes y Servicios.xls`, columna G (Caja de Herramientas) | Num. 15.1 |
| GTIN-8, 12, 13, 14 | GS1 | Guías de GS1 Colombia | Num. 15.2 |
| Partidas arancelarias | Sistema Armonizado (SA) | — | Num. 15.3 |
| Códigos libres o autónomos | La propia empresa | — | Num. 15.4 |
| Tabla de `@schemeID`, `@schemeName`, `@schemeAgencyID` | DIAN | Tabla 13.3.5 Productos (Caja de Herramientas) | Num. 14.5.5 |

**Reglas del Ítem (Factura)**
| Regla | Y | Campo |
| :--- | :--- | :--- |
| FAZ09 | R | `StandardItemIdentification` (hasta tres codificaciones) |
| FAZ10 | R | `ID` |
| FAZ11 | R | `@schemeID` |
| FAZ12 | R | `@schemeName` |
| FAZ13 | R | `@schemeName` (sin validación) |
| FAZ14 | R | `@schemeAgencyID` (`9` si `@schemeID` = `010`) |
| FAZ15 | N | `@schemeAgencyName` (sin validación) |
| FAZ06 a FAZ08 | N | `SellersItemIdentification`, `ID`, `ExtendedID` |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Codificación de productos y servicios en la factura electrónica DIAN: StandardItemIdentification, tabla 13.3.5, UNSPSC V.14.080 (Colombia Compra Eficiente), GTIN (GS1), partidas arancelarias, códigos libres y reglas FAZ09 a FAZ15: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/26_manual_codificacion_productos.md

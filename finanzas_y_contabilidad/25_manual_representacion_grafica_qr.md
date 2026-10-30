<!-- Path: finanzas_y_contabilidad/25_manual_representacion_grafica_qr.md -->

# 🖨️ Manual Operativo 25: Representación Gráfica y Código QR - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Diseñador de Reportes, Área Contable  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando deba diseñar o revisar la representación gráfica (PDF o impresión) de una factura electrónica, nota crédito o nota débito: contenido y formato del código QR, tamaño mínimo, presencia en todas las páginas, relación con el XML y validación de autenticidad de una representación gráfica.

---

# MANUAL DE REPRESENTACIÓN GRÁFICA Y CÓDIGO QR

**ID del Manual:** DIAN-UBL2.1-M25  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Representación gráfica (PDF, impresión u otro formato digital) con código QR  
**Propósito:** Recoger lo que el Anexo Técnico 1.9 establece sobre la representación gráfica de los documentos electrónicos y su código bidimensional QR (num. 11.1.7 y 11.1.7.1), su relación con el XML (num. 5.10) y su entrega a adquirentes que no son facturadores electrónicos (num. 9.3).

---

## 1. PROCEDIMIENTO GENERAL

Para la representación gráfica de los documentos e instrumentos electrónicos es requisito generar un código QR con la información que define el anexo (Anexo 1.9, num. 11.1.7). La representación gráfica puede ser diseñada de acuerdo con las necesidades del obligado a facturar electrónicamente (OFE); como el documento se genera en formato XML, cualquier herramienta informática de conversión a `.pdf`, `.docx` u otros formatos digitales es suficiente para cumplir con la normatividad vigente. Una alternativa a los formatos digitales es la impresión en papel. En ambos casos, el requisito es la inclusión del código QR (Anexo 1.9, num. 11.1.7).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **XML Generado:** La representación gráfica se construye a partir del XML del documento electrónico, que es el que tiene valor legal (Anexo 1.9, num. 11.1.7).
2. **Información en el XML:** Toda la información presentada en la representación gráfica debe estar en el XML del documento electrónico correspondiente (Anexo 1.9, num. 5.10).
3. **Generador de QR:** El sistema debe poder generar el código QR con los datos del numeral 11.1.7 y la URL de la DIAN según el ambiente (Anexo 1.9, num. 11.1.7.1).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Tamaño:** El tamaño mínimo del código QR es de 2 cm, para facilitar la lectura por los diferentes dispositivos (Anexo 1.9, num. 11.1.7).
- **Regla de Todas las Páginas:** La representación gráfica debe incluir el código QR en todas las páginas de los formatos digitales y de la impresión en papel (Anexo 1.9, num. 11.1.7).
- **Regla de Valor Legal:** La representación gráfica siempre será "una representación, una imagen" de la información consignada en el XML; el documento electrónico siempre será el que tenga valor legal para las autoridades nacionales (Anexo 1.9, num. 11.1.7).
- **Regla de Coherencia con el XML:** La información presentada en la representación gráfica debe estar en el XML (Anexo 1.9, num. 5.10).
- **Regla del Nombre Comercial:** El grupo `cac:PartyName` es obligatorio en caso de que se coloque el nombre comercial en la representación gráfica del documento; el nombre de la persona natural y la razón social de la persona jurídica deben informarse en `cac:PartyTaxScheme/cbc:RegistrationName` (Anexo 1.9, reglas FAJ05, FAJ06, FAK05 y FAK06 en la factura; CAJ05, CAJ06, CAK05 y CAK06 en la nota crédito; DAJ05, DAJ06, DAK05 y DAK06 en la nota débito; todas con efecto de notificación).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Armar el Contenido del Código QR
(Anexo 1.9, num. 11.1.7)

| Dato | Origen en el XML | Formato |
| :--- | :--- | :--- |
| `NumFac` | `/root/cbc:ID` | Número de la factura |
| `FecFac` | `/root/cbc:IssueDate` | Fecha de la factura |
| `HorFac` | `/root/cbc:IssueTime` | Hora de la factura (con GMT) |
| `NitFac` | `/root/cac:AccountingSupplierParty/cac:Party/cac:PartyTaxScheme/cbc:CompanyID` | NIT del facturador, sin puntos ni guiones |
| `DocAdq` | `/root/cac:AccountingCustomerParty/cac:Party/cac:PartyTaxScheme/cbc:CompanyID` | Número de identificación del adquirente, sin puntos ni guiones |
| `ValFac` | `/root/cac:LegalMonetaryTotal/cbc:LineExtensionAmount` o `/root/cac:RequestedMonetaryTotal/cbc:LineExtensionAmount` | Punto decimal, dos (2) decimales, sin separadores de miles ni símbolo de pesos |
| `ValIva` | `/root/cac:TaxTotal/cbc:TaxAmount` donde `/root/cac:TaxTotal/cac:TaxSubtotal/cac:TaxCategory/cac:TaxScheme/cbc:ID` = `01` | Punto decimal, dos (2) decimales, sin separadores de miles ni símbolo de pesos |
| `ValOtroIm` | Sumatoria de `/root/cac:TaxTotal[X]/cbc:TaxAmount[X]` donde el `TaxScheme/cbc:ID` sea diferente a `01` | Punto decimal, dos (2) decimales, sin separadores de miles ni símbolo de pesos |
| `ValTolFac` | `/root/cac:LegalMonetaryTotal/cbc:PayableAmount` o `/root/cac:RequestedMonetaryTotal/cbc:PayableAmount` | Punto decimal, dos (2) decimales, sin separadores de miles ni símbolo de pesos |
| `CUFE` / `CUDE` | `/root/cbc:UUID` | Identificador del documento |
| `QRCode` | `/root/ext:UBLExtensions/ext:UBLExtension/ext:ExtensionContent/sts:DianExtensions/sts:QRCode` | URL dispuesta por la DIAN (Paso 4.2) |

**Ejemplo del anexo:**
```
NumFac: 323200000129
FecFac: 2019-16-01
HorFac: 10:53:10-05:00
NitFac: 700085371
DocAdq: 800199436
ValFac: 1500000.00
ValIva: 285000.00
ValOtroIm: 0.00
ValTolFac: 1785000.00
CUFE: e5bac48e354bc907bccff0ea7d45fbf784f0a8e7243b58337361e1fbd430489d
https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=e5bac48e354bc907bccff0ea7d45fbf784f0a8e7243b58337361e1fbd430489d
```
*La fecha del ejemplo aparece en el anexo como `2019-16-01`, que no corresponde al formato `YYYY-MM-DD`; el formato de fecha del anexo es `YYYY-MM-DD` (Anexo 1.9, num. 5.6).*

### Paso 4.2: Usar la URL del Ambiente Correcto
(Anexo 1.9, num. 11.1.7.1)

La DIAN dispone estas URL con la información de la factura electrónica de venta, las notas débito, las notas crédito y los instrumentos electrónicos derivados:

| Ambiente | URL |
| :--- | :--- |
| Habilitación | `https://catalogo-vpfe-hab.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` |
| Producción | `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` |

En el XML, el elemento `sts:QRCode` de la factura debe contener la URL `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE`, donde la palabra CUFE se reemplaza por el valor de `cbc:UUID` (Anexo 1.9, regla FAB36). En la nota crédito, la palabra CUFE se reemplaza por el CUFE o CUDE del documento referenciado en `//cac:BillingReference/cac:InvoiceDocumentReference/cbc:UUID` (Anexo 1.9, CAB36); en la nota débito, por el CUFE o CUDE del documento electrónico (Anexo 1.9, DAB36).

### Paso 4.3: Ubicar el QR en la Representación Gráfica
- Incluir el código QR en **todas** las páginas, tanto en formatos digitales como en la impresión en papel (Anexo 1.9, num. 11.1.7).
- Tamaño mínimo de **2 cm** (Anexo 1.9, num. 11.1.7).

### Paso 4.4: Entregar la Representación Gráfica
- Al adquirente facturador electrónico, el PDF de la representación gráfica es opcional dentro del único `.ZIP` que contiene el `AttachedDocument` (Anexo 1.9, num. 9.1; ver Manual 17).
- Al adquirente que no es facturador electrónico, se entrega por el medio que autorice (correo o medio electrónico en formato digital de representación gráfica, en el formato electrónico de generación con la validación de la DIAN dentro del contenedor, impresión de la representación gráfica, o envío entre servidores con acuerdo). Si no informa el medio, se expide una impresión de la representación gráfica (Anexo 1.9, num. 9.3).

### Paso 4.5: Validar la Autenticidad de una Representación Gráfica
(Anexo 1.9, num. 11.1.7)
1. Acceder al sitio web que la DIAN disponga para ello.
2. Activar el hiperenlace y diligenciar los campos de información.
3. Disparar el botón de Validación.
4. Comparar lo que muestra la respuesta del sistema de facturación electrónica de la DIAN con lo que exhibe la representación gráfica que se tiene a la mano, y proceder en consecuencia.
5. Si la información difiere, se puede denunciar el hecho a la DIAN, porque puede tratarse de un documento apócrifo, sin validez legal, que podría ser evidencia de una acción que amerita ser investigada fiscalmente, con las implicaciones comerciales, administrativas y penales que se deriven por la infracción del Estatuto Tributario.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **QR Correcto:** El código QR contiene los datos del numeral 11.1.7 con sus formatos y la URL del ambiente correspondiente.
2. **QR Visible:** El código QR aparece en todas las páginas y mide como mínimo 2 cm.
3. **Coherencia:** Todo lo que muestra la representación gráfica está en el XML, que es el documento con valor legal.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `FAB36` (factura) o `AAB36` (evento), la solución es incluir en `sts:QRCode` la URL de consulta definida por la DIAN (Anexo 1.9, num. 8.2 y 8.6.1).
- Si el código QR no se lee en dispositivos, la solución es verificar que tenga como mínimo 2 cm (Anexo 1.9, num. 11.1.7).
- Si el código QR no aparece en alguna página del PDF o de la impresión, la solución es incluirlo en todas las páginas (Anexo 1.9, num. 11.1.7).
- Si la consulta del QR no encuentra el documento, la solución es verificar que la URL corresponda al ambiente (habilitación o producción) en que se validó el documento (Anexo 1.9, num. 11.1.7.1).
- Si la representación gráfica muestra un dato que no está en el XML, la solución es agregarlo al XML o retirarlo de la representación gráfica (Anexo 1.9, num. 5.10).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Requisitos de la Representación Gráfica**
| Requisito | Regla | Referencia |
| :--- | :--- | :--- |
| Código QR | Obligatorio, con los datos del numeral 11.1.7 | Num. 11.1.7 |
| Tamaño del QR | Mínimo 2 cm | Num. 11.1.7 |
| Páginas | QR en todas las páginas, digital e impresa | Num. 11.1.7 |
| Diseño | Libre, según las necesidades del OFE | Num. 11.1.7 |
| Formato | PDF, DOCX, otros formatos digitales o impresión en papel | Num. 11.1.7 |
| Valor legal | Lo tiene el XML; la representación gráfica es una imagen | Num. 11.1.7 |
| Contenido | Todo lo que muestra debe estar en el XML | Num. 5.10 |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Representación gráfica de la factura electrónica DIAN: contenido y formato del código QR (NumFac, FecFac, HorFac, NitFac, DocAdq, ValFac, ValIva, ValOtroIm, ValTolFac, CUFE), URL por ambiente, tamaño mínimo de 2 cm, QR en todas las páginas y validación de autenticidad: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/25_manual_representacion_grafica_qr.md

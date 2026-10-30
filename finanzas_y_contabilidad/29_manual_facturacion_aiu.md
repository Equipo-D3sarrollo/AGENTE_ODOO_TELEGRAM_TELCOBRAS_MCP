<!-- Path: finanzas_y_contabilidad/29_manual_facturacion_aiu.md -->

# 🏗️ Manual Operativo 29: Facturación Electrónica de Contratos AIU - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad / Obras y Proyectos  
**Rol Requerido:** Área Contable, Analista de Facturación, Desarrollador de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando deba emitir una factura electrónica (o una nota crédito o débito) por un contrato de servicios tipo AIU, para aplicar lo que el Anexo Técnico 1.9 exige en el XML: el tipo de operación, la nota del ítem Administración y el manejo de los tributos de los ítems que no hacen parte de la base gravable.

---

# MANUAL DE FACTURACIÓN ELECTRÓNICA DE CONTRATOS AIU

**ID del Manual:** DIAN-UBL2.1-M29  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Factura electrónica (`Invoice`) con `CustomizationID` = `09` y sus notas  
**Propósito:** Recoger todo lo que el Anexo Técnico 1.9 establece sobre las facturas por contratos de servicios tipo AIU: tipo de operación `09` (num. 19.2), reglas FAV03, CAV03 y DAV03 (nota del ítem Administración) y reglas FAX01, CAX01 y DAX01 (tributos de los ítems AIU que no hacen parte de la base gravable).

---

## 1. PROCEDIMIENTO GENERAL

El Anexo Técnico 1.9 trata los contratos de servicios tipo AIU en tres puntos:
1. **Tipo de operación:** `cbc:CustomizationID` = `09` (AIU) (Anexo 1.9, num. 19.2).
2. **Nota del ítem Administración:** En la línea del ítem Administración, `cbc:Note` debe empezar por el texto "Contrato de servicios AIU por concepto de:" y el contribuyente debe incluir el objeto del contrato facturado (Anexo 1.9, reglas FAV03, CAV03 y DAV03).
3. **Tributos por línea:** El grupo `cac:TaxTotal` de la línea no se informa para ítems cuyo concepto en contratos de AIU no haga parte de la base gravable (Anexo 1.9, reglas FAX01, CAX01 y DAX01).

*El anexo no define cómo se calculan la Administración, los Imprevistos o la Utilidad ni la base gravable del contrato; eso lo regula la norma tributaria y debe definirlo el Área Contable. Este manual solo recoge lo que el anexo exige en el documento electrónico.*

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Contrato AIU Identificado:** El sistema debe saber qué facturas corresponden a contratos de servicios tipo AIU para informar el tipo de operación `09`.
2. **Objeto del Contrato:** Se debe contar con el objeto del contrato facturado para incluirlo en la nota del ítem Administración (Anexo 1.9, FAV03).
3. **Definición Contable de los Ítems:** El Área Contable debe indicar qué ítems del contrato hacen parte de la base gravable y cuáles no, porque de eso depende si la línea lleva `cac:TaxTotal` (Anexo 1.9, FAX01).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla del Tipo de Operación:** `cbc:CustomizationID` = `09` (AIU). La tabla completa de tipos de operación (13.1.5.1) está en la Caja de Herramientas (Anexo 1.9, num. 14 y 19.2).
- **Regla de la Nota AIU (FAV03, N):** Obligatorio de informar para el caso de facturas por contratos de servicio tipo AIU, para el ítem Administración. En este caso `cbc:Note` debe empezar por el texto: "Contrato de servicios AIU por concepto de:" y el contribuyente debe incluir el objeto del contrato facturado. Mensaje si falta: "Nota no informada". *En la tabla de reglas esta regla tiene efecto de notificación (`N`), aunque el texto la describe como obligatoria.*
- **Regla de Tributos por Línea (FAX01, R):** Solo se informa un `TaxTotal` por tributo en una línea; no se informa para ítems excluidos de acuerdo con el Estatuto Tributario, ni para facturas del régimen simple grupo I, ni para ítems cuyo concepto en contratos de AIU no haga parte de la base gravable. A nivel de ítem solo aplica para tributos que deban informarse a nivel de ítem (por ejemplo IVA, INC, IC, Impuesto Nacional a los Combustibles, entre otros).
- **Notas Crédito y Débito:** Aplican las mismas reglas: `CAV03` y `DAV03` (N) para la nota del ítem Administración, y `CAX01` y `DAX01` (R) para los tributos por línea (Anexo 1.9, num. 8.3 y 8.4).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Cabecera de la Factura
- `cbc:CustomizationID` = `09` (Anexo 1.9, num. 19.2).
- Los demás elementos de cabecera se informan como en cualquier factura (ver Manual 08).

### Paso 4.2: Línea del Ítem Administración
- **Ruta:** `/Invoice/cac:InvoiceLine/cbc:Note`
- **Contenido:** texto que empieza por `Contrato de servicios AIU por concepto de:` seguido del objeto del contrato facturado (Anexo 1.9, FAV03).
- **Tamaño:** de 20 a 5000 caracteres; ocurrencia 0..N en `InvoiceLine` (Anexo 1.9, definición del campo FAV03).

### Paso 4.3: Tributos de Cada Línea
- Para los ítems del contrato que hacen parte de la base gravable, se informa `cac:InvoiceLine/cac:TaxTotal` con un solo `TaxTotal` por tributo (Anexo 1.9, FAX01).
- Para los ítems cuyo concepto en contratos de AIU no haga parte de la base gravable, **no** se informa `cac:TaxTotal` en la línea (Anexo 1.9, FAX01).
- Los totales del documento se calculan según las reglas de cabecera (FAS y FAU) descritas en el Manual 08.

### Paso 4.4: Notas Crédito y Débito de Facturas AIU
- **Nota crédito:** en la línea del ítem Administración, `/CreditNote/cac:CreditNoteLine/cbc:Note` con el mismo texto (CAV03); tributos por línea según CAX01.
- **Nota débito:** en la línea del ítem Administración, `/DebitNote/cac:DebitNoteLine/cbc:Note` con el mismo texto (DAV03); tributos por línea según DAX01.
- El tipo de operación de las notas es el de la nota (`20`, `22` o `24` en nota crédito; `30` o `32` en nota débito), no el `09` de la factura (Anexo 1.9, num. 14.3; ver Manuales 09 y 10).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Factura AIU Identificada:** La factura tiene `CustomizationID` = `09`.
2. **Nota AIU Informada:** El ítem Administración tiene la nota que empieza por "Contrato de servicios AIU por concepto de:" con el objeto del contrato.
3. **Tributos Correctos:** Solo los ítems que hacen parte de la base gravable llevan `TaxTotal` en la línea.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si se recibe la notificación `FAV03` (`CAV03`, `DAV03`) "Nota no informada", la solución es agregar en la línea del ítem Administración la nota que empieza por "Contrato de servicios AIU por concepto de:" con el objeto del contrato facturado.
- Si ocurre el rechazo `FAX01` (`CAX01`, `DAX01`), la solución es revisar los `TaxTotal` de las líneas: un solo `TaxTotal` por tributo, y ninguno en los ítems AIU que no hacen parte de la base gravable.
- Si hay duda sobre qué ítems hacen parte de la base gravable, la decisión es del Área Contable con base en la norma tributaria; el anexo no la define.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Reglas AIU del Anexo 1.9**
| Documento | Nota del ítem Administración | Tributos por línea |
| :--- | :--- | :--- |
| Factura | FAV03 (N) | FAX01 (R) |
| Nota crédito | CAV03 (N) | CAX01 (R) |
| Nota débito | DAV03 (N) | DAX01 (R) |

**Tipo de Operación**
| Código | Tipo de operación | Referencia |
| :--- | :--- | :--- |
| `09` | AIU | Num. 19.2 |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Facturación electrónica de contratos de servicios AIU: CustomizationID 09, nota obligatoria del ítem Administración ("Contrato de servicios AIU por concepto de:", reglas FAV03, CAV03, DAV03) y tributos por línea para ítems AIU que no hacen parte de la base gravable (FAX01, CAX01, DAX01): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/29_manual_facturacion_aiu.md

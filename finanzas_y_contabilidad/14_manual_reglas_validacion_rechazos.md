<!-- Path: finanzas_y_contabilidad/14_manual_reglas_validacion_rechazos.md -->

# ⚠️ Manual Operativo 14: Reglas de Validación (Rechazos y Notificaciones) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Soporte Técnico, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema requiera interceptar, leer e interpretar estructuralmente el archivo XML de respuesta (`ApplicationResponse`) emitido por la DIAN, para clasificar si un documento fue aceptado con notificaciones o rechazado por el incumplimiento de reglas de validación, permitiendo al usuario corregir el error y retransmitir.

---

# MANUAL DE REGLAS DE VALIDACIÓN (RECHAZOS Y NOTIFICACIONES)

**ID del Manual:** DIAN-UBL2.1-M07  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (ApplicationResponse) y Logs Transaccionales  
**Propósito:** Detallar el procedimiento algorítmico y técnico para extraer, catalogar y mapear las reglas de validación (códigos de rechazo y advertencias) ejecutadas por el motor de la DIAN, garantizando que el sistema automatizado de facturación maneje correctamente las excepciones y guíe la corrección de datos.

---

## 1. PROCEDIMIENTO GENERAL

Todo documento electrónico transmitido a la DIAN (Factura, Nota, Evento) es sometido a un motor de validación que ejecuta las reglas de negocio definidas en el Anexo Técnico 1.9 (más de 1.300 reglas identificadas en el capítulo 8 para factura, notas, eventos, firma y conexión). El procedimiento de lectura de reglas consiste en desempaquetar el `ApplicationResponse` devuelto por el Web Service, analizar el nodo raíz de validación (`cac:DocumentResponse/cac:Response/cbc:ResponseCode`), e iterar sobre los nodos hijos de línea de respuesta (`cac:LineResponse`) para extraer el código exacto de la regla fallida y su descripción textual. Esta información debe mapearse hacia la interfaz del usuario (ERP/CRM) para su corrección.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Recepción del ApplicationResponse:** El sistema de transmisión debe haber completado la consulta asíncrona (`GetStatusZip`) o sincrónica y obtenido el XML decodificado.
2. **Estructura XML Íntegra:** El archivo de respuesta no debe estar corrupto a nivel de etiquetas (Parsing).
3. **Catálogo de Reglas Actualizado:** El motor ERP debe contar con el mapeo interno de los prefijos de error (ej. `FAD`, `FAJ`, `FAK`, `FAS`, `FAU`, `FAV`, `VLR`, `LGC`) para guiar al usuario a la vista correcta de solución (ver Tabla de Referencia).
4. **Mecanismo de Desbloqueo:** El sistema de origen debe permitir editar un documento en estado "Rechazado" para corregir los campos erróneos y regenerar el XML manteniendo la misma numeración (ya que un rechazo implica que el documento nunca nació jurídicamente).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Severidad (Rechazo):** Si el `ResponseCode` general es `04` (Documento Rechazado), el documento carece de validez fiscal y no existe en los registros de la DIAN. No puede ser entregado al cliente bajo ninguna circunstancia.
- **Regla de Severidad (Notificación):** Si el `ResponseCode` general es `02` (Documento Validado), pero existen nodos `LineResponse` con advertencias (códigos de regla tipo Notificación), el documento **es legalmente válido**. La advertencia es de carácter informativo (ej. sugerencia de actualización de RUT).
- **Regla de Columna "Y" (Anexo 1.9, num. 5.8 y 8.1):** Cada regla tiene efecto `R` (Rechazo) o `N` (Notificación). Un documento solo recibe el sello de "validado" si no falla ninguna regla `R`, y puede recibirlo aunque falle cualquier número de reglas `N`. Cuando un elemento tiene más de una regla, al ID se le agregan letras (a, b, …).
- **Formato del Mensaje (Anexo 1.9, num. 8.1):** El resultado de una validación fallida es la concatenación del ID, el resultado (`R` o `N`) y el mensaje correspondiente; cuando el elemento puede ocurrir en distintas partes del XML, el mensaje incluye el Xpath completo.
- **Regla de Concurrencia de Errores:** La DIAN procesa el documento completo y puede devolver múltiples rechazos en un solo `ApplicationResponse`. El sistema debe iterar y mostrar *todos* los errores encontrados simultáneamente.
- **Codificación:** El análisis de las reglas de error debe soportar caracteres especiales y acentos en codificación `UTF-8` presentes en el nodo `<cbc:Description>`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (INTERPRETACIÓN DEL XML PASO A PASO)

### Paso 4.1: Extracción del Estado Global del Documento
Analiza la cabecera de la respuesta para determinar si el proceso es un éxito o un fallo.

- **Ruta:** `/ApplicationResponse/cac:DocumentResponse/cac:Response`
- **Procedimiento:**
  1. Leer el campo `cbc:ResponseCode`.
  2. Si es `02`, marcar el documento como **Aceptado**.
  3. Si es `04`, marcar el documento como **Rechazado**.
  4. Leer el campo `cbc:Description` general (ej. "Documento con errores en reglas de validación").

### Paso 4.2: Iteración de las Reglas Incumplidas (`LineResponse`)
Independientemente de si es un rechazo o un éxito con notificaciones, se deben inspeccionar los detalles de validación que el motor de la DIAN empaqueta en líneas individuales.

- **Ruta:** `/ApplicationResponse/cac:DocumentResponse/cac:LineResponse`
- **Procedimiento (Bucle Iterativo):**
  1. Por cada nodo `cac:LineResponse` encontrado, ubicar el grupo `cac:Response`.
  2. Leer el campo `cbc:ResponseCode`: Contiene el ID alfanumérico de la regla (Ej. `FAD09e`, `FAS02`, `VLR01`), de 4 a 10 caracteres (Anexo 1.9, AAI05).
  3. Leer el campo `cbc:Description`: Contiene la explicación textual (Ej. "La fecha de generación de la factura es diferente a la fecha de firma de la factura"), de 4 a 150 caracteres (Anexo 1.9, AAI06).
  4. Almacenar este par (Código + Descripción) en la bitácora de errores (log) asociada a la transacción.

### Paso 4.3: Clasificación y Notificación al Usuario
- **Procedimiento:**
  1. Utilizar un analizador léxico o tabla de mapeo que identifique el prefijo del `ResponseCode` (Ver Tabla de Referencia).
  2. Emitir una alerta en la interfaz de facturación (UI) resaltando exactamente qué sección requiere corrección (Ej. Si el error es `FAJ` o `FAK`, resaltar los datos del emisor o del contacto; si el error es `FAS` o `FAX`, resaltar las líneas de producto y tarifas de impuestos).

### Paso 4.4: Desbloqueo y Corrección de Documento
- **Procedimiento:**
  1. El documento rechazado (`04`) debe liberar su estado de bloqueo en el ERP.
  2. El usuario ajusta los datos y confirma.
  3. El sistema recalcula la estructura (recalculando obligatoriamente el hash del código QR y el CUFE/CUDE con la nueva información) e inicia un nuevo proceso desde el *Manual de Generación*.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Rechazado (`04`):** El documento comercial se detiene. El consecutivo contable sigue disponible para ser retransmitido una vez subsanado el error de fondo que generó el rechazo (Ej. se puede volver a usar la Factura N° 100).
2. **Estado Validado con Notificaciones (`02`):** El documento es plenamente legal. Se actualiza el estado a "Aceptado", se advierte al usuario sobre las notificaciones recibidas, pero se autoriza el empaquetado del XML y el pase al *Manual de Contenedor Electrónico* para entrega al cliente.
3. **Persistencia de Trazabilidad:** Todo código de rechazo y notificación reportado por la DIAN debe quedar registrado permanentemente en el historial del documento (Log) con propósito de auditoría técnica.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES DIAN)

- Si la DIAN arroja `Regla FAD09e` (Rechazo de Fecha), la solución es validar que el `cbc:IssueDate` enviado sea igual a la fecha de firma de la factura; debe actualizarse su fecha y recalcular su firma/CUFE antes de transmitir. Si arroja `FAD09a` o `FAD09b`, la fecha está por fuera de la vigencia de la autorización de numeración. Las reglas FAD09c y FAD09d fueron eliminadas en la versión 1.9 (Anexo 1.9, num. 2.1 y 8.2).
- Si la DIAN arroja `Regla VLR01` (Errores de Valor o Signo), la solución es auditar las extensiones de línea. Asegúrese de que no haya importes ni porcentajes negativos (Anexo 1.9, num. 5.3 y 8.1). Si arroja `FAV06`, la validación `Precio Unitario * Cantidad` menos descuentos más cargos debe cuadrar con `LineExtensionAmount`, con la tolerancia de `+/- 2.00` (Anexo 1.9, num. 5.1.1.1).
- Si la DIAN arroja `FAS02`, `FAS07`, `FAX02`, `FAX07` o `FAU06` (discrepancia en impuestos), la solución es que el sistema sume estrictamente los valores de `<cbc:TaxAmount>` de los `TaxSubtotal` y los compare con el agrupador `<cac:TaxTotal>`, verificando que cada `TaxAmount` sea base por tarifa, con la tolerancia de `+/- 2.00` y de `+/- 5.00` en el IVA para la aproximación al múltiplo de 10 (Anexo 1.9, num. 5.1.1.1 y 5.1.1.2).
- Si la DIAN arroja `Regla de Validación CUFE/CUDE` (`FAD06`, `CAD06`, `DAD06` o `AAD06`), la solución es verificar la concatenación exacta del Hash. Comúnmente ocurre cuando hay espacios en blanco, cuando los valores monetarios difieren un centavo en la cadena frente al XML, o cuando se usa la clave técnica de otro ambiente. Los valores de la cadena van con dos decimales truncados y los impuestos `01`, `04` y `03` se incluyen siempre, con `0.00` si no están referenciados (Anexo 1.9, num. 11.1.2).
- Si la DIAN arroja `Regla 90` (Documento procesado anteriormente), el número del documento ya fue transmitido: solo se puede transmitir una única vez el número del documento para el contribuyente (Anexo 1.9, num. 8.1).
- Si la DIAN arroja `CTG01`, la fecha y hora de firmado de una factura enviada por inconvenientes tecnológicos de la DIAN no corresponde a un período de contingencia establecido por la DIAN (Anexo 1.9, num. 8.1).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Nomenclatura y Prefijos de Reglas de Validación (Anexo Técnico 1.9)**

*El prefijo indica el documento (`F` factura, `C` nota crédito, `D` nota débito, `A` evento ApplicationResponse) y el grupo del XML; el efecto de cada regla (`R` Rechazo o `N` Notificación) está en la columna "Y" de cada regla (Anexo 1.9, num. 5.8 y 8.1).*

| Prefijo / Familia | Área de Validación en el XML | Severidad Habitual | Acción de Corrección en el Sistema |
| :--- | :--- | :--- | :--- |
| **FAD / CAD / DAD / AAD** | Datos de Cabecera (Versión, Tipo de operación, Perfil, Ambiente, Número, CUFE/CUDE, Fechas, Moneda) | **R o N según la regla** | Ajustar datos maestros del documento, fechas o ambiente de ejecución. |
| **FAB / CAB / DAB / AAB** | Extensiones DIAN (Numeración, Software, Proveedor, Código QR) | **R o N según la regla** | Verificar resolución, `SoftwareID`, `SoftwareSecurityCode` y URL del QR. |
| **FAC / CAC / DAC / AAC y DC** | Extensión de firma y validación de la firma digital (Anexo 1.9, num. 8.7) | **R o N según la regla** | Revisar la firma Xades-EPES y el certificado. |
| **FAJ / CAJ / DAJ** | Identificación del Emisor | **R o N según la regla** | Verificar NIT, Dígito de Verificación, responsabilidades y dirección del emisor. |
| **FAK / CAK / DAK** | Identificación del Adquiriente | **R o N según la regla** | Verificar Dígito de Verificación o actualizar el RUT del contacto. |
| **FAN / FBD** | Medios de pago y Anticipos | **R o N según la regla** | Revisar forma, medio y fecha de pago, y anticipos. |
| **FAS / CAS / DAS** | Tributos de cabecera | **R o N según la regla** | Ajustar tarifas (5%, 19%) y revisar sumatorias de agrupadores fiscales. |
| **FAT** | Retenciones (`WithholdingTaxTotal`) | **R o N según la regla** | Revisar bases y valores de retención. |
| **FAU / CAU / DAU** | Totales Monetarios | **R o N según la regla** | Recalcular valor bruto, base imponible, tributos, descuentos, cargos y total a pagar. |
| **FAV / CAV / DAV, FAX / CAX / DAX, FAZ, FBB** | Líneas de detalle, tributos de línea, ítem y precio | **R o N según la regla** | Recalcular importes unitarios, cantidades, descuentos y redondeo. |
| **CBF / CBG / DBF / DBG** | Discrepancia y referencia a la factura (Notas Crédito/Débito) | **R o N según la regla** | Verificar concepto de la nota y que el CUFE de origen sea exacto y válido. |
| **AAF / AAG / AAH / AAI** | Eventos: generador, receptor y respuesta del evento | **R o N según la regla** | Revisar las partes del evento, el código del evento y el documento referenciado. |
| **LGC** | Reglas lógicas entre eventos y notas (Anexo 1.9, num. 8.6) | **Rechazo** | Revisar la secuencia y los plazos de los eventos y si la factura ya fue aceptada. |
| **90 / CTG01 / VLR01 / CDG01 / RUT01** | Reglas generales: número ya procesado, período de contingencia, valores positivos, departamento y municipio, estado del RUT (Anexo 1.9, num. 8.1) | **Rechazo** | Revisar numeración, fecha de firma, signos, códigos DANE y estado del RUT. |
| **ZA / ZB / ZC / ZD / ZE** | Conexión: mensaje del web service, esquema XML, certificado de transmisión, certificado de firma y firma (Anexo 1.9, num. 8.8) | **R o N según la regla** | Revisar tamaño (máx. 500 KB), esquema y codificación UTF-8, y certificados. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Reglas de Validación, Manejo de Rechazos y Notificaciones DIAN, ApplicationResponse, Códigos FAD, VLR, FAS: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/14_manual_reglas_validacion_rechazos.md

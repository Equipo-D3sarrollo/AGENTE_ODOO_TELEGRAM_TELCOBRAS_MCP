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

Todo documento electrónico transmitido a la DIAN (Factura, Nota, Evento) es sometido a un motor de validación que ejecuta más de 200 reglas de negocio definidas en el Anexo Técnico 1.8. El procedimiento de lectura de reglas consiste en desempaquetar el `ApplicationResponse` devuelto por el Web Service, analizar el nodo raíz de validación (`cac:DocumentResponse/cac:Response/cbc:ResponseCode`), e iterar sobre los nodos hijos de línea de respuesta (`cac:LineResponse`) para extraer el código exacto de la regla fallida y su descripción textual. Esta información debe mapearse hacia la interfaz del usuario (ERP/CRM) para su corrección.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Recepción del ApplicationResponse:** El sistema de transmisión debe haber completado la consulta asíncrona (`GetStatusZip`) o sincrónica y obtenido el XML decodificado.
2. **Estructura XML Íntegra:** El archivo de respuesta no debe estar corrupto a nivel de etiquetas (Parsing).
3. **Catálogo de Reglas Actualizado:** El motor ERP debe contar con el mapeo interno de los prefijos de error (ej. `FAD`, `VLR`, `TAX`, `NIT`) para guiar al usuario a la vista correcta de solución.
4. **Mecanismo de Desbloqueo:** El sistema de origen debe permitir editar un documento en estado "Rechazado" para corregir los campos erróneos y regenerar el XML manteniendo la misma numeración (ya que un rechazo implica que el documento nunca nació jurídicamente).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Severidad (Rechazo):** Si el `ResponseCode` general es `04` (Documento Rechazado), el documento carece de validez fiscal y no existe en los registros de la DIAN. No puede ser entregado al cliente bajo ninguna circunstancia.
- **Regla de Severidad (Notificación):** Si el `ResponseCode` general es `02` (Documento Validado), pero existen nodos `LineResponse` con advertencias (códigos de regla tipo Notificación), el documento **es legalmente válido**. La advertencia es de carácter informativo (ej. sugerencia de actualización de RUT).
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
  2. Leer el campo `cbc:ResponseCode`: Contiene el ID alfanumérico de la regla (Ej. `FAD09`, `TAX-TOTAL-ERROR`, `VLR02`).
  3. Leer el campo `cbc:Description`: Contiene la explicación textual (Ej. "La fecha de emisión no puede ser mayor a la actual").
  4. Almacenar este par (Código + Descripción) en la bitácora de errores (log) asociada a la transacción.

### Paso 4.3: Clasificación y Notificación al Usuario
- **Procedimiento:**
  1. Utilizar un analizador léxico o tabla de mapeo que identifique el prefijo del `ResponseCode` (Ver Tabla de Referencia).
  2. Emitir una alerta en la interfaz de facturación (UI) resaltando exactamente qué sección requiere corrección (Ej. Si el error es `NIT`, resaltar los datos del contacto; si el error es `TAX`, resaltar las líneas de producto y tarifas de impuestos).

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

- Si la DIAN arroja `Regla FAD09` (Rechazo de Fecha), la solución es validar que el `cbc:IssueDate` enviado coincida con el día en que se transmite. Un documento guardado y no enviado por 15 días generará rechazo; debe actualizarse su fecha y recalcular su firma/CUFE antes de transmitir.
- Si la DIAN arroja `Regla VLR01` o `VLR02` (Errores de Valor o Signo), la solución es auditar las extensiones de línea. Asegúrese de que no haya importes negativos y que la validación `Precio Unitario * Cantidad` cuadre exactamente con `LineExtensionAmount` respetando 2 decimales.
- Si la DIAN arroja `TAX-TOTAL-ERROR`, la solución es que el sistema sume estrictamente los valores de `<cbc:TaxAmount>` de los ítems individuales y los compare con el agrupador en el `<cac:TaxTotal>` general. Deben ser matemáticamente idénticos.
- Si la DIAN arroja `Regla de Validación CUFE/CUDE`, la solución es verificar la concatenación exacta del Hash. Comúnmente ocurre cuando hay espacios en blanco, cuando los valores monetarios difieren un centavo en la cadena frente al XML, o cuando se usa la clave técnica de otro ambiente.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Nomenclatura y Prefijos de Reglas de Validación (Anexo Técnico 1.8)**
| Prefijo / Familia | Área de Validación en el XML | Severidad Habitual | Acción de Corrección en el Sistema |
| :--- | :--- | :--- | :--- |
| **FAD / CAD** | Datos de Cabecera (Fechas, Perfiles, Moneda) | **Rechazo** | Ajustar datos maestros del documento, fechas o ambiente de ejecución. |
| **NIT** | Identificación de Participantes (Emisor/Receptor) | **Rechazo** | Verificar Dígito de Verificación o actualizar el RUT del contacto. |
| **VLR / FAV** | Valores y Subtotales (Matemática de Línea) | **Rechazo** | Recalcular importes unitarios, cantidades, descuentos y redondeo. |
| **TAX / ZZZ** | Impuestos (Tributos globales e individuales) | **Rechazo** | Ajustar tarifas (5%, 19%) y revisar sumatorias de agrupadores fiscales. |
| **REF / CBG** | Referencias a otros documentos (Notas Crédito/Débito) | **Rechazo** | Verificar que el CUFE/CUDE de origen sea exacto y válido. |
| **NOT** | Reglas Informativas (Alertas DIAN) | **Notificación** | No impide expedición, revisar recomendación preventiva. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Reglas de Validación, Manejo de Rechazos y Notificaciones DIAN, ApplicationResponse, Códigos FAD, VLR, TAX: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/14_manual_reglas_validacion_rechazos.md

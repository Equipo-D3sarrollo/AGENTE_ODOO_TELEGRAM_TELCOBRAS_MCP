<!-- Path: finanzas_y_contabilidad/16_manual_contingencias_tecnologicas.md -->

# ⚠️ Manual Operativo 16: Contingencias Tecnológicas (Tipo 03 y Tipo 04) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Soporte y Operaciones Técnicas  
**Rol Requerido:** Administrador del Sistema, Soporte Nivel 2/3, Desarrollador Backend, Oficial de Cumplimiento  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema experimente caídas de infraestructura local, fallas de conexión a internet o daño en servidores que impidan facturar electrónicamente (Contingencia Tipo 03), o cuando se presenten inconvenientes tecnológicos en los servicios de validación previa de la DIAN, detectados con los mensajes de error de la tabla 12.2.1 o con demoras en los tiempos de respuesta (Contingencia Tipo 04).

---

# MANUAL DE CONTINGENCIAS TECNOLÓGICAS (TIPO 03 Y TIPO 04)

**ID del Manual:** DIAN-UBL2.1-M16  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** XML Transcrito con `InvoiceTypeCode` `03` (Tipo 03) y Factura Electrónica con `InvoiceTypeCode` `04` (Tipo 04), transmitidos por `SendBillSync`  
**Propósito:** Detallar los protocolos operativos y estructurales obligatorios para garantizar la continuidad del negocio durante escenarios de indisponibilidad técnica, asegurando el cumplimiento normativo en la expedición de comprobantes físicos y su posterior transmisión electrónica.

---

## 1. PROCEDIMIENTO GENERAL

Las contingencias tecnológicas son los mecanismos de excepción legales que permiten a los obligados a facturar continuar con su operación comercial cuando los sistemas fallan. El procedimiento varía drásticamente según el origen de la falla: 
* Si el origen de la falla es el Facturador (Tipo 03), el sistema debe detener la generación de XML, pasar a facturación física (talonario) y posteriormente realizar una transcripción electrónica de dichos documentos físicos. 
* Si el origen de la falla es la DIAN (Tipo 04), el sistema genera la factura electrónica cambiando `InvoiceTypeCode` al valor `04` (mismo prefijo y número), la vuelve a firmar, la entrega al adquiriente en un `AttachedDocument` sin el ApplicationResponse de la DIAN y la transmite por `SendBillSync` dentro de las 48 horas siguientes a detectar que el servicio de la DIAN está activo (Anexo 1.9, num. 12.2).
* Los documentos `CreditNote`, `DebitNote` y `ApplicationResponse` (eventos) no tienen esquema de contingencia: se generan, transmiten y expiden cuando el inconveniente esté superado (Anexo 1.9, num. 12.1, 12.2 y 12.3.1).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Resolución de Contingencia Activa:** El facturador debe contar con una resolución de numeración tipo "Factura de Talonario o de Papel" previamente autorizada por la DIAN y configurada (inactiva por defecto) en el sistema. Cuando `InvoiceTypeCode` = `03`, las verificaciones de la autorización del rango se realizan respecto a la numeración de contingencia, y a ese rango autorizado no se le asigna clave técnica (Anexo 1.9, num. 11.1.4.1, Nota-1 y Nota-2).
2. **Talonarios Físicos Disponibles:** El establecimiento comercial debe tener disponible la forma de expedir la factura de venta de talonario o de papel correspondiente a la resolución del punto 1, la cual se podrá generar de forma manual o autógrafa o a través de sistemas informáticos electrónicos (Anexo 1.9, num. 12.1).
3. **Mecanismo de Encolamiento (Queue):** El motor backend debe disponer de un sistema de colas (ej. RabbitMQ, Kafka, o tabla de base de datos de estados transaccionales) para almacenar de forma segura los XML no transmitidos.
4. **Módulo de Transcripción:** La interfaz de usuario (UI) debe contar con un formulario específico para la digitación manual (transcripción) de las facturas de papel emitidas durante la caída.
5. **Monitoreo de Timeouts:** El sistema debe contar con *circuit breakers* que detecten caídas al intentar consumir el Web Service `vpfe.dian.gov.co`: los mensajes de error de la tabla 12.2.1 (en la Caja de Herramientas, `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\Tablas Referenciadas`) y las demoras, definidas como una respuesta que toma más de 1 minuto o el mensaje "Time out" (Anexo 1.9, num. 12.3 y 12.3.1).
6. **Representante Legal Disponible (Tipo 03):** Para declarar el inconveniente tecnológico o su superación se requiere una carta firmada por el representante legal de la compañía o quien haga sus veces (Anexo 1.9, num. 12.1).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Plazos Máximos:** Todo documento expedido en contingencia (sea Tipo 03 o Tipo 04) debe ser transmitido a la DIAN en un plazo máximo de **cuarenta y ocho (48) horas**: para el Tipo 03, contadas desde el momento en que se supera el inconveniente; para el Tipo 04, una vez el facturador detecte que el servicio de la DIAN está activo (Anexo 1.9, num. 12.1 y 12.2).
- **Regla de Tipo de Documento:** La contingencia se identifica en `cbc:InvoiceTypeCode`: `03` para el documento electrónico de transmisión (transcripción de la factura de talonario o papel) y `04` para la factura electrónica expedida por inconvenientes tecnológicos de la DIAN (Anexo 1.9, num. 11.1.4.1 y 12.2). El atributo `cbc:ProfileExecutionID` no cambia por la contingencia: indica el ambiente, `1` Producción o `2` Pruebas (Anexo 1.9, FAD04).
- **Regla de Legalidad (Tipo 04):** Los facturadores electrónicos y proveedores tecnológicos que utilicen los servicios de validación previa podrán establecer automáticamente el procedimiento Tipo 04 si se cumplen las condiciones del numeral 12.2 (detección de un error de la tabla 12.2.1 y reintentos fallidos) o si persisten las demoras tras 5 intentos (num. 12.3.1). La DIAN toma la fecha y hora de generación (`SigningTime`) para determinar si la factura fue generada en un período de contingencia establecido por la DIAN; si no corresponde, rechaza con la regla `CTG01` (Anexo 1.9, num. 8.1 y 12.2).
- **Regla de Referencia Cruzada:** Toda factura electrónica producto de una transcripción (Tipo 03) debe llevar incrustada la referencia exacta (prefijo y número) de la factura de papel física que le fue entregada al cliente. El grupo `cac:AdditionalDocumentReference` es obligatorio cuando `InvoiceTypeCode` = `03`; si no se informa, la DIAN rechaza (Anexo 1.9, regla FAI01).
- **Regla de Método de Envío:** Los documentos de contingencia Tipo 03 y Tipo 04 se envían a través del método `SendBillSync` (Anexo 1.9, num. 12.1 y 12.2).
- **Regla de Documentos sin Contingencia:** `CreditNote`, `DebitNote` y `ApplicationResponse` no usan la numeración de contingencia sino la numeración establecida por el facturador para ellos (Anexo 1.9, num. 12.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CÁLCULOS Y ESTRUCTURAS)

### Paso 4.1: Activación y Operación en Contingencia Tipo 03 (Falla del Facturador)
Cuando el sistema ERP, la red local o el proveedor tecnológico fallan.

- **Acción Operativa:** Los cajeros o vendedores proceden a expedir facturas físicas de talonario.
- **Acción del Sistema:** El administrador marca en el sistema el inicio de la "Contingencia Local". El sistema bloquea los envíos automáticos e interrumpe el consumo de folios electrónicos.
- **Declaración ante la DIAN:** El facturador genera una carta declarando el inconveniente tecnológico (y luego otra declarando su superación), firmada por el representante legal o quien haga sus veces, y la remite a `contingencia.facturadorvp@dian.gov.co` con (Anexo 1.9, num. 12.1):
  - **Asunto:** NIT de la empresa separado con un guion del dígito de verificación; Nombre de la empresa.
  - **Adjunto:** PDF de la carta con la firma del representante legal o quien haga sus veces.
  - **Cuerpo del correo:** Datos de contacto (nombres, teléfono/celular de contacto).
  - Este correo es únicamente para informar el inconveniente tecnológico o su superación. Las constancias también se pueden entregar por escrito mediante radicado dirigido a la DIAN - nivel central - factura electrónica.

### Paso 4.2: Transcripción del Documento Físico a XML (Post-Contingencia Tipo 03)
Una vez superada la falla, los documentos de papel deben digitarse en el sistema.

- **Ruta XML Perfil:** `/Invoice/ext:UBLExtensions/.../sts:DianExtensions/sts:InvoiceControl/sts:InvoiceAuthorization` (Debe ser la resolución del talonario de papel).
- **Atributo Clave:** `<cbc:InvoiceTypeCode>03</cbc:InvoiceTypeCode>` (documento electrónico de transmisión). `cbc:ProfileExecutionID` mantiene el valor del ambiente (`1` Producción, `2` Pruebas).
- **Fecha y Hora:** `<cbc:IssueDate>` y `<cbc:IssueTime>` NO deben ser el momento de la digitación, sino la fecha y hora exactas en las que se elaboró la factura física entregada al adquiriente. *Nota: el Anexo 1.9 no indica expresamente qué fecha se informa en la transcripción; la regla FAD09e exige que la fecha de generación de la factura sea igual a la fecha de firma. Verificar el comportamiento con la DIAN.*
- **Referencia al Documento Físico:** Se debe agregar el bloque `AdditionalDocumentReference` indicando el folio del talonario físico, con `cbc:ID` (prefijo y número del documento referenciado) (Anexo 1.9, FAI01 y FAI02).
- **Cálculo del Identificador (CUDE):** El documento de transmisión no usa CUFE con clave técnica: se calcula un CUDE con SHA-384 sobre `NumFac` + `FecFac` + `HorFac` + `ValFac` + `01` + `ValImp1` + `04` + `ValImp2` + `03` + `ValImp3` + `ValTot` + `NitFE` + `NumAdq` + `Software-PIN` + `TipoAmbiente`, usando el PIN del software registrado en el catálogo del participante en lugar de la clave técnica, y se registra en `/Invoice/cbc:UUID` (Anexo 1.9, num. 11.1.3 y 11.1.4.1, Nota-2; num. 11.1.4.2).
- **Plazo y Envío:** La transcripción de cada factura de talonario o papel expedida durante la contingencia se expide dentro de las 48 horas siguientes al momento en que se supera el inconveniente, por el método `SendBillSync`; al transmitir, la DIAN informa las notificaciones y/o rechazos a que haya lugar (Anexo 1.9, num. 12.1).
- **Retorno a la Normalidad:** Superado el inconveniente, el facturador vuelve a generar, transmitir, expedir y recibir sus documentos e instrumentos electrónicos de manera habitual (Anexo 1.9, num. 12.1).

### Paso 4.3: Activación y Operación en Contingencia Tipo 04 (Falla de la DIAN)
Cuando el sistema local funciona perfecto, pero los Web Services de la DIAN retornan errores o timeouts constantes.

- **Condiciones de Activación (Anexo 1.9, num. 12.2):**
  1. Detección de un error informado en la tabla 12.2.1 Mensajes de error.
  2. Transmitir nuevamente la factura a los 5 segundos de la detección del error. Si persiste, realizar dos (2) intentos más, cada uno con intervalo de 5 segundos.
  3. Si al finalizar el último intento (15 segundos después de la transmisión inicial) persiste el error, el facturador expide directamente al adquiriente la factura electrónica sin validación previa, es decir, sin el ApplicationResponse de la DIAN.
  4. Mantener o archivar las evidencias del error.

- **Acción Operativa:** El comercio sigue vendiendo y facturando de manera 100% electrónica.
- **Estructura XML:** 
  - `<cbc:ProfileExecutionID>1</cbc:ProfileExecutionID>` (Se mantiene en 1).
  - `<cbc:InvoiceTypeCode>04</cbc:InvoiceTypeCode>`, manteniendo el mismo prefijo y número de factura. La numeración utilizada es la de factura electrónica de venta (Anexo 1.9, num. 12.2).
  - El XML se vuelve a firmar con Xades-EPES (Anexo 1.9, num. 12.2).
  - Se genera el Código QR asumiendo un CUFE válido.
- **Entrega al Adquiriente:** La factura electrónica sin ApplicationResponse (validación de la DIAN) se incluye en un `AttachedDocument` y se entrega al adquiriente (Anexo 1.9, num. 12.2). El PDF puede llevar una marca de agua: *"Emitido en Contingencia DIAN (Transmisión Diferida)"*. No se requiere el estado `Validado por la DIAN` para su entrega.
- **Duración:** Se expiden facturas con `InvoiceTypeCode` `04` mientras se presenten los inconvenientes tecnológicos de la DIAN (Anexo 1.9, num. 12.2).
- **Monitoreo:** Monitorear la conexión y los servicios web de la DIAN a los 30 minutos después de haber recibido el primer mensaje de error de la tabla 12.2.1. Mientras el servicio no esté restablecido, continuar el monitoreo. Si el servicio está restablecido, generar, transmitir y expedir normalmente los documentos (Anexo 1.9, num. 12.2).

### Paso 4.4: Transmisión Diferida y Liberación de Colas (Post-Contingencia Tipo 04)
Al normalizarse el servicio de la DIAN (cuando el facturador detecte que el servicio de la DIAN está activo).

- **Plazo:** El facturador tiene 48 horas para transmitir a la DIAN las facturas Tipo 04, contadas desde que detecte que el servicio de la DIAN está activo (Anexo 1.9, num. 12.2).
- **Consumo de Servicio Web:** El envío de estos documentos se realiza a través del método `SendBillSync`, que recibe un ZIP con un solo documento (Anexo 1.9, num. 7.10 y 12.2). El sistema de la DIAN toma la fecha y hora de generación del documento (`SigningTime`) para determinar si fue generado en un período de contingencia establecido por la DIAN (Anexo 1.9, num. 12.2; regla CTG01).
- **Actualización de Estado:** El motor procesa la respuesta sincrónica de `SendBillSync` y actualiza el estado de las facturas en la base de datos de `Encolado` a `Aceptado`.

### Paso 4.5: Demoras en los Tiempos de Respuesta de la DIAN
Se define por demora cuando la respuesta a una solicitud a uno de los servicios de factura electrónica con validación previa toma más de 1 minuto; en la demora los servicios se mantienen activos y no se generan los códigos de la tabla 12.2.1 (Anexo 1.9, num. 12.3.1).

1. A manera informativa, notificar a la DIAN la demora en la respuesta de los servicios.
2. Si durante la transmisión se recibe el mensaje "Time out", se entiende como una demora declarada por el sistema.
3. Transcurridos 2 minutos de la demora declarada, transmitir nuevamente los documentos. Si persiste, realizar cuatro (4) intentos más, cada uno con intervalo de 2 minutos.
4. Si el problema persiste tras los 5 intentos, se declara la contingencia Tipo 04, manteniendo y archivando las evidencias de las demoras.
5. En la contingencia Tipo 04, generar la factura con `InvoiceTypeCode` `04` manteniendo el mismo prefijo y número, volver a firmarla, incluirla sin ApplicationResponse en un `AttachedDocument` y entregarla al adquiriente.
6. Monitorear la conexión y los servicios web a los 30 minutos después de la última transmisión del punto 3.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Sincronización Completa:** Todas las operaciones de contingencia fueron exitosamente procesadas y cuentan con su respectivo ApplicationResponse (`Documento validado por la DIAN`) por parte de la DIAN.
2. **Trazabilidad de Logs:** El sistema almacena evidencia técnica (Logs de timeouts, errores HTTP 5xx) que justifican los horarios de las caídas de transmisión, preparándose para posibles auditorías. El anexo exige mantener o archivar las evidencias de los errores de la tabla 12.2.1 y de las demoras (Anexo 1.9, num. 12.2 y 12.3.1).
3. **Consumo de Folios:** El consecutivo de la resolución de papel fue actualizado y conciliado correctamente para el caso de Tipo 03.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `CTG01` (la fecha y hora de firmado del documento no corresponde a un período de contingencia establecido por la DIAN), la solución es verificar que el `SigningTime` de la factura Tipo 04 corresponda al período de contingencia establecido por la DIAN (Anexo 1.9, num. 8.1 y 12.2). La `Regla 90` corresponde a un documento procesado anteriormente: solo se puede transmitir una única vez el número del documento (Anexo 1.9, num. 8.1).
- Si ocurre el error `Resolución no autorizada para el tipo de documento`, la solución es revisar el Tipo 03; frecuentemente ocurre al intentar transcribir una factura de papel usando la resolución de factura electrónica estándar. Se debe usar estrictamente la resolución asignada para talonario contingencia.
- Si ocurre el error `ProfileExecutionID Inválido`, confirme que el valor corresponda al ambiente (`1` Producción, `2` Pruebas) y que sea igual a `cbc:UUID/@schemeID` (Anexo 1.9, FAD04 y FAD07). La contingencia no se informa en `ProfileExecutionID` sino en `InvoiceTypeCode` (`03` o `04`).
- Si ocurre el error `FAI01` en una transcripción Tipo 03, la solución es incluir el grupo `cac:AdditionalDocumentReference`, obligatorio cuando `InvoiceTypeCode` = `03`.
- Si hay bloqueos por "Demasiadas peticiones" tras un Tipo 04, la solución es implementar un patrón de *Throttling* en el vaciado de la cola, enviando un documento por petición a `SendBillSync` dentro del plazo de 48 horas (Anexo 1.9, num. 7.10 y 12.2).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipificación de Contingencias y Parámetros UBL 2.1**
| Escenario (Tipo) | Causante de la Falla | Facturación en Papel | InvoiceTypeCode | Tiempo de Transmisión Máximo |
| :--- | :--- | :--- | :--- | :--- |
| **Tipo 03** | Facturador Electrónico | **SÍ (Obligatorio)** | `03` (documento electrónico de transmisión) | 48 Horas desde superada la falla, por `SendBillSync` |
| **Tipo 04** | DIAN (Servicios de validación previa) | **NO** (Sigue Electrónico) | `04` | 48 Horas desde que el facturador detecte que el servicio de la DIAN está activo, por `SendBillSync` |
| **Notas y Eventos** | Cualquiera | **NO** | No aplica (sin esquema de contingencia) | Se generan, transmiten y expiden cuando el inconveniente esté superado |

*Fuente: Anexo 1.9, num. 12.1, 12.2 y 12.3.1. `cbc:ProfileExecutionID` no cambia por la contingencia: indica el ambiente (`1` Producción, `2` Pruebas).*

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Contingencias Tecnológicas DIAN, Tipo 03 y Tipo 04, Transmisión Diferida, Facturación en Papel y Transcripción, InvoiceTypeCode 03 y 04: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/16_manual_contingencias_tecnologicas.md

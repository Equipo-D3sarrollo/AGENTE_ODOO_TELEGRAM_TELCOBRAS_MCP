<!-- Path: finanzas_y_contabilidad/12_manual_registro_eventos_radian.md -->

# 🧾 Manual Operativo 12: Generación de Registro de Eventos (Application Response - RADIAN) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Área Contable, Analista de Facturación  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el usuario o el sistema automatizado necesite construir, validar y empaquetar algorítmicamente el archivo XML correspondiente a un Evento Electrónico (acuse de recibo, recibo de bien/servicio, aceptación, rechazo o eventos RADIAN), garantizando la interoperabilidad técnica y fiscal con los Web Services de la DIAN para constituir la factura como título valor.

---

# MANUAL DE GENERACIÓN: REGISTRO DE EVENTOS (APPLICATION RESPONSE - RADIAN)

**ID del Manual:** DIAN-UBL2.1-M05  
**Versión de UBL:** 2.1  
**Formato de Salida:** XML (Extensible Markup Language)  
**Propósito:** Detallar el procedimiento algorítmico, técnico y normativo para la construcción, validación y ensamblaje de los eventos electrónicos asociados a una factura de venta, asegurando la trazabilidad legal, la conversión a título valor y el cumplimiento de las reglas del ecosistema RADIAN bajo el estándar internacional UBL 2.1.

---

## 1. PROCEDIMIENTO GENERAL

La construcción de un Registro de Evento Electrónico utiliza el esquema estandarizado `ApplicationResponse`. El procedimiento requiere orquestar el nodo raíz, identificar al emisor del evento (quien realiza la acción, ej. el adquiriente) y al receptor (quien recibe la notificación, ej. el emisor de la factura original). Es obligatorio declarar el código oficial del evento en el nodo de respuesta (`Response`) y vincular indisolublemente el evento a la factura original mediante su identificador CUFE (`DocumentReference`). Todo el documento debe ser firmado digitalmente.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura de Origen Validada:** La factura electrónica sobre la cual recae el evento debe existir en la plataforma DIAN y contar con su respectivo CUFE. Cada evento solamente puede ser registrado en un documento para el cual existe un evento "Documento Validado por la DIAN" (Anexo 1.9, num. 6.5.5).
2. **Sincronización con la hora legal:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo, crítico para determinar los plazos legales (ej. 3 días hábiles para aceptación tácita). La hora legal la determina el reloj de la Superintendencia de Industria y Comercio (http://horalegal.inm.gov.co/) (Anexo 1.9, num. 10.5.8).
3. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación digital abierta autorizada por la ONAC (Anexo 1.9, num. 10.1).
4. **Algoritmo CUDE Configurado:** Disponer del PIN de software para calcular el hash SHA-384 del evento (CUDE). El CUDE del evento tiene una fórmula propia: `Num_DE` + `Fec_Emi` + `Hor_Emi` + `NitFE` (documento de quien genera el evento) + `DocAdq` (documento de quien recibe el evento) + `ResponseCode` + `ID` (prefijo y número del documento referenciado) + `DocumentTypeCode` + `Software-PIN` (Anexo 1.9, num. 11.1.5).
5. **Secuencialidad Lógica:** Entender que los eventos básicos operan en cadena estricta: Acuse de Recibo (030) -> Recibo del Bien (032) -> Aceptación Expresa (033) o Reclamo (031). La aceptación tácita (034) solo se transmite pasados 3 días hábiles después del evento 032 (Anexo 1.9, regla LGC14).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Secuencia (RADIAN):** La DIAN rechazará un evento de "Recibo del Bien" si la factura no cuenta previamente con un "Acuse de Recibo". Igualmente, rechazará una "Aceptación" o "Reclamo" si faltan los eventos previos (Anexo 1.9, reglas LGC03, LGC09, LGC12 y LGC13).
- **Reglas de Exclusión y Plazos (Anexo 1.9, num. 6.5.3 Tabla 8 y num. 8.6):** No se puede reclamar un documento aceptado expresa o tácitamente (LGC02); no se puede aceptar un documento rechazado (LGC04); no se puede generar aceptación tácita sobre un documento aceptado expresamente (LGC05) ni aceptación expresa sobre uno aceptado tácitamente (LGC07); no se puede generar aceptación expresa pasados los 3 días hábiles siguientes al evento 032 (LGC06); solo se permite el registro de un único evento cuando aplique (LGC01, LGC20).
- **Regla de Identificadores (CUDE):** Todos los eventos (sin excepción) generan su propio Código Único de Documento Electrónico (CUDE) calculado mediante el algoritmo SHA-384. El evento NO usa CUFE (Anexo 1.9, AAD06 y AAD08).
- **Regla de Referencia Obligatoria:** Es mandatorio incluir la referencia exacta al documento comercial afectado (CUFE de la factura) en el grupo `DocumentReference`.
- **Regla de Exclusividad de Firma:** El XML del evento debe contener un solo nodo `ds:Signature` que garantiza el no repudio de la acción comercial o legal ejecutada (Anexo 1.9, AAC03).
- **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8`.
- **Contingencia:** Los eventos (ApplicationResponse) no tienen esquema de contingencia; se generan y transmiten cuando los servicios de la DIAN funcionen normalmente (Anexo 1.9, num. 12.1, 12.2 y 12.3.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
Declara el elemento raíz `<ApplicationResponse>` con los *namespaces* estándar.

- **Ruta:** `/ApplicationResponse`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: Literal `1` (Anexo 1.9, AAD02).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1: ApplicationResponse de Factura Electrónica de Venta` (Anexo 1.9, AAD03).
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Número consecutivo interno de quien genera el evento (Anexo 1.9, AAD05).
  * `cbc:UUID`: CUDE del evento, calculado con SHA-384. El algoritmo se indica en `@schemeName="CUDE-SHA384"` y el ambiente de destino en `@schemeID` (Anexo 1.9, AAD06, AAD07 y AAD08).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de generación del evento. La fecha no puede ser anterior a la del documento referenciado (Anexo 1.9, AAD09 y AAD10), y la fecha de generación del evento debe ser igual a la fecha de firma del evento (regla AAD09e, nueva en la versión 1.9).
  * `cbc:Note`: Obligatoria para el evento `034` (Aceptación tácita), con una de las notas de manifestación bajo la gravedad de juramento que el anexo incluye al final del numeral 6.5.5.5 (la regla AAD11 las referencia como numeral 6.5.5.7); y cuando el documento es generado a través de un mandato, con el texto `Nombre del Mandatario OBRANDO EN NOMBRE Y REPRESENTACION DE Nombre del mandante` (Anexo 1.9, AAD11). Si falta la nota del evento de aceptación tácita o la nota de mandato, la DIAN rechaza (AAD11a, AAD11b).

### Paso 4.2: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque para alojar información de control y la firma digital.

- **Ruta:** `/ApplicationResponse/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):** Información del software emisor y código de seguridad si el anexo técnico lo exige para el tipo de evento. Incluye `sts:InvoiceSource` (literal `CO`), `sts:SoftwareProvider` (`sts:ProviderID` y `sts:softwareID`), `sts:SoftwareSecurityCode` (SHA-384 de `SoftwareID` + `SoftwarePIN` + número del ApplicationResponse), `sts:AuthorizationProvider` (NIT de la DIAN) y `sts:QRCode` (Anexo 1.9, AAB13 a AAB36; num. 11.1.8).
  2. **Firma Digital (`ds:Signature`):** `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico que confiere validez jurídica al evento. Debe haber como mínimo dos `UBLExtension` (`sts:DianExtensions` y `ds:Signature`) (Anexo 1.9, AAC01 a AAC03).

### Paso 4.3: Emisor del Evento (`SenderParty`)
Define quién está ejecutando la acción (quien firma el evento).

- **Ruta:** `/ApplicationResponse/cac:SenderParty`
- **Nodos Obligatorios:**
  * Si el evento es un acuse de recibo, el `SenderParty` será el cliente (Adquiriente). En la aceptación tácita (`034`) quien genera el evento es el Emisor/Facturador y quien lo recibe es la DIAN (Anexo 1.9, num. 6.5.5.5).
  * Debe contener el NIT (`cbc:CompanyID` con `@schemeAgencyID="195"`, el Dígito de Verificación en `@schemeID`, el tipo de identificación en `@schemeName` y el tipo de organización en `@schemeVersionID`) y Razón Social o Nombre (`cbc:RegistrationName`), además del grupo `cac:TaxScheme` (Anexo 1.9, AAF01 a AAF11 y AAF20).

### Paso 4.4: Receptor del Evento (`ReceiverParty`)
Define a quién va dirigido el evento.

- **Ruta:** `/ApplicationResponse/cac:ReceiverParty`
- **Nodos Obligatorios:**
  * Generalmente corresponde al emisor de la factura original.
  * Debe contener NIT, Dígito de Verificación y Razón Social, con la misma estructura de atributos del `SenderParty` y el grupo `cac:TaxScheme` (Anexo 1.9, AAG01 a AAG11 y AAG23).

### Paso 4.5: Declaración del Evento y Referencia (`DocumentResponse`)
Paso crítico donde se especifica qué acción se está realizando y sobre qué documento.

- **Ruta:** `/ApplicationResponse/cac:DocumentResponse`
- **Procedimiento:**
  1. **Grupo `cac:Response`:**
     * `cbc:ResponseCode`: Código numérico oficial de la DIAN para el evento (Ej. `030`, `032`, `033`).
     * `cbc:Description`: Texto descriptivo con el literal definido para cada evento (Ej. `030`: "Acuse de recibo de Factura Electrónica de Venta"; `031`: "Reclamo de la Factura Electrónica de Venta"; `033`: "Aceptación expresa"; `034`: "Aceptación Tácita") (Anexo 1.9, num. 6.5.5, AAH04).
     * Para el reclamo (`031`), el atributo `cbc:ResponseCode/@listID` informa el tipo de reclamo según la lista de la Caja de Herramientas (Anexo 1.9, AAH10).
  2. **Grupo `cac:DocumentReference`:**
     * `cbc:ID`: Número de la factura afectada.
     * `cbc:UUID`: CUFE original de la factura afectada, con el atributo `@schemeName="CUFE-SHA384"`.
     * `cbc:DocumentTypeCode`: Fijo en `01` (Factura de Venta). Debe corresponder al tipo del documento registrado en el sistema (Anexo 1.9, AAH09).
     * Si el `cbc:UUID` no existe en la base de datos de la DIAN, el evento es rechazado (Anexo 1.9, AAH07).
  3. **Grupo `cac:IssuerParty/cac:Person`** (acuse de recibo `030`): Datos de quien recibió la factura: `cbc:ID` (con DV en `@schemeID` si `@schemeName` = `31` y tipo de documento en `@schemeName`), `cbc:FirstName`, `cbc:FamilyName` y, opcionalmente, `cbc:JobTitle` y `cbc:OrganizationDepartment` (Anexo 1.9, AAH11 a AAH19).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha estructurado en memoria, cuenta con el CUDE validado y hace referencia estricta a la factura base.
2. **Estado Firmado:** La firma digital asegura la autenticidad y no repudio del evento generado, consolidando su peso probatorio (título valor).
3. **Estado Empaquetado:** El archivo se comprime en un `.zip` listo para enviarse al Web Service de registro de eventos de la DIAN. El servicio `SendEventUpdateStatus` es sincrónico y el ZIP debe contener un solo ApplicationResponse (Anexo 1.9, num. 7.13). El XML se nombra con el prefijo `ar` + NIT sin DV (10 dígitos) + código `ppp` + 2 últimos dígitos del año + consecutivo de 8 dígitos hexadecimales (Anexo 1.9, num. 6.7).
4. **Respuesta Esperada:** La DIAN procesará el evento y responderá con otro `ApplicationResponse` validando la correcta aplicación en su base de datos. Para el registro de la factura electrónica de venta como título valor en el RADIAN se validan: la fecha de vencimiento de la factura, el acuse de recibo (`030`), el recibo del bien o prestación del servicio (`032`) y la aceptación expresa (`033`), la aceptación tácita (`034`) o el reclamo (`031`) (Anexo 1.9, num. 5). Si la factura no indica fecha de vencimiento, se entiende que debe pagarse dentro de los treinta (30) días siguientes a la emisión (Anexo 1.9, num. 6.5.1).
5. **Entrega al otro participante:** El evento se entrega por correo electrónico en un único `.zip` con un AttachedDocument que contiene el ApplicationResponse de la DIAN y el ApplicationResponse del evento, con el asunto `Evento;Número del documento referenciado;NIT del generador del evento;Nombre del generador del evento;Número del documento electrónico;Código del tipo de documento;Línea de negocio` (la línea de negocio es opcional) y peso máximo de 2 Megas (Anexo 1.9, num. 9.2).

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `Secuencia de Eventos Inválida`, la solución es garantizar a nivel de código o interfaz de usuario que no se intente enviar una Aceptación Expresa (033) o Reclamo (031) sin haber procesado exitosamente el Acuse de Recibo (030) y el Recibo del Bien (032).
- Si ocurre el error `CUDE Invalido en Evento` (regla `AAD06`), la solución es auditar la cadena de texto base usada para el SHA-384, verificando que los parámetros coincidan con la estructura solicitada para el documento `ApplicationResponse`: `Num_DE` + `Fec_Emi` + `Hor_Emi` + `NitFE` + `DocAdq` + `ResponseCode` + `ID` + `DocumentTypeCode` + `Software-PIN` (Anexo 1.9, num. 11.1.5).
- Si ocurre el error `Referencia de Factura Invalida`, la solución es verificar que el `cbc:UUID` dentro del bloque `DocumentReference` contenga exactamente el CUFE de la factura, sin espacios y reportado en el momento original de la emisión.
- Si ocurre el error `AAD09e` (la fecha de generación del evento es diferente a la fecha de firma), la solución es generar y firmar el evento con la misma fecha de `cbc:IssueDate`.
- Si ocurre el error `Regla AAD09 - Fecha Inválida`, la solución es corroborar la sincronización del servidor (NTP), ya que el evento no puede tener una fecha anterior a la expedición de la factura, ni superior a la fecha actual del sistema de la DIAN.

---

## 7. Tablas de Referencia y Anexos Técnicos

**Códigos de Eventos de Facturación (ResponseCode - ApplicationResponse)**
| Código (ResponseCode) | Nombre del Evento | Descripción / Uso Comercial |
| :--- | :--- | :--- |
| **030** | Acuse de recibo de Factura | Emitido por el adquiriente indicando la recepción de la factura y contenedor XML. |
| **032** | Recibo del bien o servicio | Emitido por el adquiriente confirmando la entrega física o prestación satisfactoria. |
| **033** | Aceptación expresa | Aprobación total de las condiciones comerciales. La factura se entiende aceptada expresamente y no se pueden expedir notas crédito o débito una vez generado este evento (Anexo 1.9, num. 6.5.5.4). |
| **031** | Reclamo o Rechazo | Rechazo de la factura por inconsistencias. Impide el registro de la aceptación expresa y de la aceptación tácita; se puede generar una nota crédito o débito (Anexo 1.9, num. 6.5.5.2). |
| **034** | Aceptación tácita | Emitido por el proveedor si transcurren 3 días hábiles tras el evento 032 sin reclamos. Lo recibe la DIAN, es una manifestación bajo la gravedad de juramento y después de este evento no se pueden expedir notas crédito o débito (Anexo 1.9, num. 6.5.5.5). |
| **02** | Documento validado por la DIAN | Respuesta de la DIAN cuando el documento es validado (Anexo 1.9, num. 6.5.5). |
| **04** | Documento rechazado por la DIAN | Respuesta de la DIAN cuando el documento no es validado; impide el registro de los demás eventos (Anexo 1.9, num. 6.5.3, Tabla 8). |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Registro de Eventos (ApplicationResponse) UBL 2.1, Acuse de Recibo, Aceptación y RADIAN, cálculos CUDE y DocumentReference: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/12_manual_registro_eventos_radian.md

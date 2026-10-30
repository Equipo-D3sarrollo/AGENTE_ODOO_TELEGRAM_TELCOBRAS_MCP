<!-- Path: finanzas_y_contabilidad/23_manual_habilitacion_set_pruebas.md -->

# 🧪 Manual Operativo 23: Habilitación, Set de Pruebas y Herramienta SOAP UI - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Habilitación y Pruebas  
**Rol Requerido:** Desarrollador de Integración, Administrador del Sistema, Área Contable  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando una empresa vaya a habilitarse como facturador electrónico (o habilitar un nuevo software o modo de operación) y deba enviar el set de pruebas a la DIAN en el ambiente de habilitación, o cuando necesite probar manualmente los web services de la DIAN con la herramienta SOAP UI.

---

# MANUAL DE HABILITACIÓN, SET DE PRUEBAS Y SOAP UI

**ID del Manual:** DIAN-UBL2.1-M23  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** ZIP con los documentos del set de pruebas enviado por `SendTestSetAsync` y consultas por `GetStatusZip`  
**Propósito:** Recoger lo que el Anexo Técnico 1.9 establece sobre los ambientes, el envío del set de pruebas de habilitación (num. 7.9), la herramienta SOAP UI (Suplemento F, num. 16) y la guía de habilitación (Suplemento J, num. 20).

---

## 1. PROCEDIMIENTO GENERAL

La DIAN dispone de un ambiente de **producción en habilitación**, donde el facturador envía los documentos del set de pruebas para cumplir su proceso de habilitación, y un ambiente de **producción en operación**, donde se transmiten los documentos reales (Anexo 1.9, num. 7.9.1 y 7.15.1). En habilitación, los documentos (factura electrónica, nota crédito y nota débito) se envían en un ZIP con el método `SendTestSetAsync`, que devuelve un TrackId (ZipKey); con ese TrackId se consulta el resultado de la validación con el método `GetStatusZip` (Anexo 1.9, num. 7.9.1).

*La guía de habilitación (pasos en el catálogo de participantes, composición del set de pruebas, etc.) no está en el PDF del anexo: se encuentra en https://www.dian.gov.co/impuestos/factura-electronica/lo-que-deberias-saber/Paginas/instructivos.aspx (Anexo 1.9, num. 20).*

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Modo de Operación Configurado:** El `testSetId` se genera al configurar un modo de operación para el proceso de habilitación sobre el ambiente de producción en habilitación (Anexo 1.9, num. 7.9.2).
2. **Rango y Clave Técnica de Pruebas:** En el ambiente de producción en habilitación, el catálogo de participantes genera el rango a utilizar (autorización, prefijo, rango desde y hasta, fechas desde y hasta y la clave técnica) para realizar las pruebas o el proceso de habilitación. El servicio `GetNumberingRange` solo está disponible en producción en operación (Anexo 1.9, num. 7.15.1).
3. **URL del Web Service:** La URL del web service está expuesta en el catálogo de participantes (habilitación o producción), en la opción Participantes, Facturador (Anexo 1.9, num. 16.5).
4. **Certificado Digital:** Contar con el certificado digital para la firma de los documentos y la autenticación ante el web service (Anexo 1.9, num. 7.4 y 10.1).
5. **Correo de Recepción:** Durante la habilitación se diligencia, como campo obligatorio, el correo electrónico único de recepción de documentos electrónicos (Anexo 1.9, num. 9).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Ambiente:** `cbc:ProfileExecutionID` indica el ambiente de destino: `1` Producción, `2` Pruebas; el valor de `cbc:UUID/@schemeID` debe ser igual: en igualdad confirma el ambiente y en desigualdad rechaza el procesamiento (Anexo 1.9, reglas FAD04 y FAD07).
- **Regla del Servicio de Habilitación:** `SendTestSetAsync` está disponible únicamente en el ambiente de producción en habilitación (Anexo 1.9, num. 7.9.1).
- **Regla del Contenido del ZIP:** El ZIP puede contener uno o todos los documentos asociados al set de pruebas (Anexo 1.9, num. 7.9.1 y 7.9.2).
- **Regla de Validaciones Iniciales:** Al recibir el ZIP, la DIAN genera un TrackId y valida que el ZIP no esté vacío, no esté corrupto y que los archivos UBL no den error de lectura; y que en cada UBL no estén vacíos el CUFE, el número, la fecha de emisión, el NIT del emisor y el NIT del receptor, y que la versión del UBL sea "UBL 2.1". El procedimiento de validación se aplica sobre el contenido del valor del campo UBL transmitido (Anexo 1.9, num. 7.9.1).
- **Regla del Código QR en Habilitación:** En ambiente de habilitación la URL del código QR es `https://catalogo-vpfe-hab.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE`; en producción es `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` (Anexo 1.9, num. 11.1.7.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Preparar los Documentos del Set de Pruebas
- Generar los documentos (factura electrónica, nota crédito y nota débito) con el rango y la clave técnica de pruebas generados por el catálogo de participantes (Anexo 1.9, num. 7.9.1 y 7.15.1).
- Informar `cbc:ProfileExecutionID` = `2` y `cbc:UUID/@schemeID` = `2` (Anexo 1.9, reglas FAD04 y FAD07).
- Nombrar los XML y el ZIP según los numerales 6.7 y 6.8 (ver Manual 21).

### Paso 4.2: Enviar el ZIP con `SendTestSetAsync`
(Anexo 1.9, num. 7.9)

- **Función:** Recibir un ZIP con UBLs de documentos electrónicos para pruebas de habilitación. **Proceso:** Asincrónico.
- **Petición:**

| Elemento | Y | Descripción | Tipo | Ocu | Long |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `fileName` | R | Nombre del ZIP | String | 1 | 100 |
| `contentFile` | R | Arreglo de bytes que representa el ZIP adjunto y que contiene los UBL a validar | Arreglo de byte | 1 | 50 MB |
| `testSetId` | R | Valor TestID que se genera al configurar un modo de operación para el proceso de habilitación sobre el ambiente de producción en habilitación | String | 1 | 36 |

- **Respuesta:**

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `XmlParamsResponseTrackId` | R | Nodo padre de la lista de errores que pudiesen presentarse en el procesamiento de los documentos. Si hay algún error, se presenta con sus elementos hijos; si no, no se informa. |
| `processedMessage` | R | Mensaje de error de validaciones iniciales. Si no hay errores de validaciones iniciales, no se entrega mensaje. |
| `xmlFileName` | R | Nombre del archivo que contiene el error. |
| `zipKey` | R | Número generado una vez concluidas exitosamente las validaciones iniciales, cuando los documentos pasan a la cola de validación. Es el TrackId que se usa en el método `GetStatusZip` para obtener el resultado de las validaciones. |

### Paso 4.3: Consultar el Resultado con `GetStatusZip`
(Anexo 1.9, num. 7.12)

- **Función:** Obtener el estado de todos los documentos asociados a un ZIP en el proceso de validación. **Proceso:** Sincrónico. Disponible en los ambientes de producción en habilitación y producción en operación.
- **Petición:** `trackId` (R): valor del ticket entregado por el envío.
- **Respuesta (por documento):**

| Elemento | Y | Descripción |
| :--- | :--- | :--- |
| `ErrorMessage/string` | R | Mensaje de la validación del documento ante las reglas de la DIAN (código y descripción de las reglas fallidas). |
| `IsValid` | R | `true` (válido) o `false` (no válido). |
| `StatusCode` | R | `00` Procesado Correctamente; `66` NSU no encontrado; `90` TrackId no encontrado; `99` Validaciones contienen errores en campos mandatorios. |
| `StatusDescription` | R | Descripción del estado, con los mismos códigos. |
| `StatusMessage` | O | Descripción del error de cada validación inicial; si no hay errores no entrega descripción. |
| `XmlBase64Bytes` | — | ApplicationResponse con la información del evento correspondiente, en base64. |
| `XmlBytes` | R | Valor del parámetro: `true`. |
| `xmlFileName` | R | Nombre del archivo UBL del documento electrónico. |

### Paso 4.4: Configurar SOAP UI para Probar los Web Services
(Anexo 1.9, Suplemento F, num. 16)

SoapUI es una herramienta para pruebas a aplicaciones con arquitectura orientada a servicios (SOA); soporta SOAP, por lo que es adecuada para probar el web service de la DIAN y sus métodos (num. 16.1).

1. **Descargar:** Desde el sitio oficial `https://www.soapui.org/downloads/soapui.html` (num. 16.2).
2. **Ejecutar** la aplicación instalada (num. 16.3).
3. **Crear proyecto SOAP:** Menú `File/New SOAP Project` (num. 16.4).
4. **Configuración inicial:** Ingresar el nombre del proyecto y cargar la URL WSDL. La URL del web service está en el catálogo de participantes (habilitación o producción), opción Participantes, Facturador (num. 16.5).
5. **Keystore:** Agregar un nuevo certificado y su contraseña (num. 16.6).
6. **WS-Security Signature:** Agregar una nueva configuración con un nombre y una entrada de tipo Signature con el certificado y la contraseña configurados. Los demás campos deben tener los valores de la Ilustración 5 del anexo (num. 16.7):

| Campo | Valor (Ilustración 5) |
| :--- | :--- |
| Key Identifier Type | `Binary Security Token` |
| Signature Algorithm | `http://www.w3.org/2001/04/xmldsig-more#rsa-sha256` |
| Signature Canonicalization | `http://www.w3.org/2001/10/xml-exc-c14n#` |
| Digest Algorithm | `http://www.w3.org/2001/04/xmlenc#sha256` |
| Use Single Certificate | Marcado (`Use single certificate for signing`) |
| Parts | Name `To`, Namespace `http://www.w3.org/2005/08/addressing`, Encode `Element` |

   *Esta configuración corresponde a la firma del mensaje SOAP (WS-Security). La firma del documento XML usa la canonicalización definida en la política de firma (`http://www.w3.org/TR/2001/REC-xml-c14n-20010315`; ver Manual 15).*
7. **Timestamp:** Configurar el tiempo de vigencia del token de seguridad en milisegundos (en la Ilustración 6 del anexo, `Time To Live` = `60000` y `Millisecond Precision` marcado) (num. 16.8).
8. **Autenticación y WS-A:** En el Request, agregar autorización `Basic` y seleccionar en `Outgoing WSS` la configuración WS-Security creada; habilitar las opciones `WS-A addressing` y `wsa:To` (num. 16.9).
9. **Ejecutar `GetStatus`:** Ingresar un TrackId; en el resultado, `XmlBytes` representa el arreglo de bytes del ApplicationResponse (num. 16.10).
10. **Ejecutar `SendBillAsync`:** Agregar el nombre del archivo `.zip`, cargar los XML adjuntos, seleccionar `Part.` y habilitar `Cached` (num. 16.11 y 16.12).
11. **Recomendación:** Después de crear o actualizar la configuración de WS-Security, eliminar el request anterior y crear uno nuevo, porque los requests existentes no reflejan las actualizaciones de la configuración global (num. 16.13).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **ZIP Recibido:** `SendTestSetAsync` devolvió un `zipKey` sin errores de validaciones iniciales.
2. **Documentos Validados:** `GetStatusZip` devuelve para cada documento `IsValid` = `true` y `StatusCode` = `00`, con su ApplicationResponse.
3. **Paso a Producción:** Terminada la habilitación según la guía de la DIAN, el facturador opera en el ambiente de producción en operación con `ProfileExecutionID` = `1`, el rango autorizado y la clave técnica que entrega `GetNumberingRange` (ver Manual 22).

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si `SendTestSetAsync` devuelve `processedMessage` con errores, la solución es revisar el `xmlFileName` informado: el ZIP no debe estar vacío ni corrupto, los UBL deben poder leerse y no deben estar vacíos el CUFE, el número, la fecha de emisión, el NIT del emisor, el NIT del receptor ni la versión "UBL 2.1" (Anexo 1.9, num. 7.9.1).
- Si ocurre el error `FAD04` o `FAD07`, la solución es verificar que `ProfileExecutionID` y `UUID/@schemeID` tengan el mismo valor y correspondan al ambiente (`2` en pruebas) (Anexo 1.9, num. 8.2).
- Si `GetStatusZip` responde `90`, el TrackId no fue encontrado; si responde `99`, las validaciones contienen errores en campos mandatorios (Anexo 1.9, num. 7.12.3).
- Si `GetNumberingRange` no responde en habilitación, es el comportamiento esperado: en ese ambiente el rango y la clave técnica los genera el catálogo de participantes (Anexo 1.9, num. 7.15.1).
- Si SOAP UI no aplica una nueva configuración de WS-Security, la solución es eliminar el request anterior y crear uno nuevo (Anexo 1.9, num. 16.13).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Servicios y Ambientes**
| Servicio | Producción en habilitación | Producción en operación | Referencia |
| :--- | :--- | :--- | :--- |
| `SendTestSetAsync` | Sí (únicamente) | No | Num. 7.9.1 |
| `SendBillSync` | Sí | Sí | Num. 7.10 |
| `GetStatus` | Sí | Sí | Num. 7.11 |
| `GetStatusZip` | Sí | Sí | Num. 7.12 |
| `GetNumberingRange` | No (el rango lo genera el catálogo de participantes) | Sí (únicamente) | Num. 7.15.1 |
| `GetExchangeEmails` | Lista correos registrados en habilitación o producción | Lista correos registrados en habilitación o producción | Num. 7.16.1 |

**Valores por Ambiente**
| Concepto | Habilitación / Pruebas | Producción |
| :--- | :--- | :--- |
| `cbc:ProfileExecutionID` y `cbc:UUID/@schemeID` | `2` | `1` |
| URL del código QR | `https://catalogo-vpfe-hab.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` | `https://catalogo-vpfe.dian.gov.co/document/searchqr?documentkey=CUFE/CUDE` |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Habilitación como facturador electrónico DIAN: ambientes de habilitación y producción, set de pruebas con SendTestSetAsync y testSetId, consulta con GetStatusZip, configuración de SOAP UI (WS-Security, Timestamp, WS-A) y guía de habilitación: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/23_manual_habilitacion_set_pruebas.md

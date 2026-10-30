<!-- Path: finanzas_y_contabilidad/21_manual_nomenclatura_empaquetado_conexion.md -->

# 🗜️ Manual Operativo 21: Nomenclatura, Empaquetado de Archivos y Reglas de Conexión - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Infraestructura y Transmisión  
**Rol Requerido:** Desarrollador de Integración, Administrador de Infraestructura, Soporte Nivel 2/3  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema deba nombrar los archivos XML de los documentos electrónicos, empaquetarlos en el archivo ZIP que se entrega a la DIAN por los web services de recepción, o diagnosticar los rechazos y notificaciones que la DIAN genera al establecer la conexión (mensaje del web service, esquema XML, certificados digitales y firma).

---

# MANUAL DE NOMENCLATURA, EMPAQUETADO Y REGLAS DE CONEXIÓN

**ID del Manual:** DIAN-UBL2.1-M21  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Archivos `.xml` nombrados según la guía DIAN y archivo `.zip` para los web services de recepción  
**Propósito:** Recoger las reglas de los numerales 6.7, 6.8, 6.9 y 8.8 del Anexo Técnico 1.9 para nombrar y empaquetar los documentos electrónicos que se entregan a la DIAN y para interpretar las reglas de validación relativas al establecimiento de la conexión.

---

## 1. PROCEDIMIENTO GENERAL

Antes de transmitir, cada documento electrónico se guarda como un archivo XML con el nombre que exige la DIAN (Anexo 1.9, num. 6.7). Uno o más de esos archivos se comprimen en un archivo ZIP con su propia nomenclatura, que es el único formato para la entrega de archivos comprimidos a la DIAN mediante un web service de recepción (Anexo 1.9, num. 6.8). Al recibirlo, la DIAN aplica primero las reglas relativas al establecimiento de la conexión: tamaño del mensaje, esquema XML, certificado de transmisión, certificado de firma y firma (Anexo 1.9, num. 8.8).

*La nomenclatura de este manual aplica a los archivos que se entregan a la DIAN. Los nombres de los archivos que se envían por correo electrónico al adquiriente no están reglamentados por la DIAN (Anexo 1.9, num. 9.1; ver Manual 17).*

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **NIT del Facturador:** Disponer del NIT del facturador electrónico sin dígito de verificación (Anexo 1.9, num. 6.7).
2. **Código de Asignación (`ppp`):** Disponer del código de tres (3) dígitos asignado por la DIAN al proveedor tecnológico. La tabla de códigos de asignación está en la Caja de Herramientas, en la ruta `Caja_de_herramientas_Factura_Electronica_Validacion_Previa.zip\Anexo Tecnico\`, archivo Excel `Códigos de Asignación.xlsx`; se deben utilizar los códigos de la columna C (Anexo 1.9, num. 6.9). Para el Software Propio el código es `000` y para la Facturación Gratuita DIAN es `001` (Anexo 1.9, num. 6.7).
3. **Contadores de Consecutivos:** El sistema debe llevar, por año, el consecutivo de archivos XML enviados y el consecutivo de paquetes ZIP enviados, ambos en hexadecimal (Anexo 1.9, num. 6.7 y 6.8).
4. **Certificados Digitales:** Contar con el certificado digital de transmisión (conexión) y el certificado digital de firma vigentes, no revocados y con cadena de confianza verificable (Anexo 1.9, num. 8.8.3 y 8.8.4).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Tamaños Constantes:** Los tamaños de cada variable del nombre son constantes; es necesario generar el ajuste con ceros a la izquierda en cada uno de ellos (Anexo 1.9, num. 6.7).
- **Regla del Año:** El año `aa` corresponde al año en vigencia (Anexo 1.9, num. 6.7).
- **Regla de Reinicio:** Cada año, el 1 de enero, se debe reiniciar a `00000001` el consecutivo de archivos enviados `dddddddd`. El consecutivo del ZIP también se inicia en `00000001` cada primero de enero (Anexo 1.9, num. 6.7 y 6.8).
- **Regla de Rango del Consecutivo:** El consecutivo va en el rango `00000001` <= `FFFFFFFF` (Anexo 1.9, num. 6.7 y 6.8).
- **Regla de Envío Sincrónico:** Si el ZIP se transmite por el servicio sincrónico, la cantidad de documentos electrónicos debe ser igual a uno (1); en caso contrario, el resultado de la operación será RECHAZO (Anexo 1.9, num. 6.8).
- **Regla de Envío Asincrónico:** Si el ZIP se transmite por el servicio asincrónico, la cantidad de documentos electrónicos debe ser inferior a 51, y el contenido podrá ser combinado: se pueden incluir `fv`, `nc`, `nd` y `ar` dentro del mismo archivo comprimido (Anexo 1.9, num. 6.8).
- **Regla de Contenido del ZIP:** Cada archivo `.xml` dentro del ZIP debe ser un documento electrónico UBL-DIAN (Anexo 1.9, num. 6.8).
- **Regla de Formato Único:** El formato de nombre del ZIP es el único para la entrega de archivos comprimidos a la DIAN (Anexo 1.9, num. 6.8).
- **Regla de Tamaño del XML (ZA01):** El archivo XML no puede ser superior a 500 KB (Anexo 1.9, num. 8.8.1).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (PASO A PASO)

### Paso 4.1: Nombrar el Archivo XML del Documento Electrónico
(Anexo 1.9, num. 6.7)

**Estructura:** `[prefijo]nnnnnnnnnnpppaadddddddd.xml`

| Componente | Descripción |
| :--- | :--- |
| Prefijo | `fv` Factura de Venta, `nc` Nota Crédito, `nd` Nota Débito, `ar` Application Response, `ad` Attached Document. |
| `nnnnnnnnnn` | NIT del Facturador Electrónico sin DV, de diez (10) dígitos alineados a la derecha y rellenos con ceros a la izquierda. |
| `ppp` | Código asignado por la DIAN al PT, de tres (3) dígitos (`000` Software Propio, `001` Facturación Gratuita DIAN; demás códigos en la tabla del num. 6.9). |
| `aa` | Dos (2) últimos dígitos del año calendario. |
| `dddddddd` | Consecutivo de archivos enviados, de ocho (8) dígitos hexadecimales alineados a la derecha y ajustados a la izquierda con ceros, en el rango `00000001` <= `FFFFFFFF`. |

**Ejemplos del anexo (Facturador Electrónico con NIT 800197268, software propio, año 2019):**
| Documento | Nombre del archivo |
| :--- | :--- |
| Décima primera factura | `fv08001972680001900000011.xml` |
| Primera nota crédito | `nc08001972680001900000001.xml` |
| Tercera nota débito | `nd08001972680001900000003.xml` |
| Octavo Application Response | `ar08001972680001900000008.xml` |
| Primer Attached Document | `ad08001972680001900000001.xml` |

*En el PDF, el ejemplo de la factura aparece escrito como `Fv08001972680001900000011.xml` y el del ZIP como `Z08001972680001900000011.zip`, con mayúscula inicial; la guía de nomenclatura define los prefijos en minúscula (`fv`, `z`) (Anexo 1.9, num. 6.7 y 6.8).*

### Paso 4.2: Nombrar y Armar el Archivo ZIP
(Anexo 1.9, num. 6.8)

**Estructura:** `znnnnnnnnnnpppaadddddddd.zip`

| Componente | Descripción |
| :--- | :--- |
| `z` | Comprimido. Archivo que contiene uno o varios archivos `*.xml`. |
| `nnnnnnnnnn` | NIT del Facturador Electrónico sin DV, de diez (10) dígitos alineados a la derecha y rellenos con ceros a la izquierda. |
| `ppp` | Código asignado por la DIAN al PT, de tres (3) dígitos (num. 6.9). |
| `aa` | Dos (2) últimos dígitos del año calendario. |
| `dddddddd` | Consecutivo del paquete de archivos comprimidos enviados, de ocho (8) dígitos hexadecimales alineados a la derecha y ajustados a la izquierda con ceros, en el rango `00000001` <= `FFFFFFFF`. Corresponde al envío del archivo `.zip` enviado a la entidad. |

- **Ejemplo del anexo:** `z08001972680001900000011.zip`.
- **Cantidad de documentos:** uno (1) para el servicio sincrónico; inferior a 51 para el servicio asincrónico, con contenido combinable (`fv`, `nc`, `nd`, `ar`).
- **Parámetros del web service:** El nombre del ZIP se envía en `fileName` (longitud hasta 100) y el ZIP en `contentFile`, arreglo de bytes de hasta 50 MB que contiene los UBL a validar (Anexo 1.9, num. 7.8.2).

### Paso 4.3: Validaciones del Mensaje del Web Service
(Anexo 1.9, num. 8.8.1)

| Regla | Y | Validación | Mensaje |
| :--- | :--- | :--- | :--- |
| ZA01 | R | Verificar si el tamaño del archivo XML es superior a 500 KB. | Tamaño del mensaje superior al límite establecido [Máximo: 500 KB] |
| ZA02 | N | Verificar si el servicio está parado momentáneamente. | Servicio parado momentáneamente [corto plazo] |
| ZA03 | N | Verificar si el servicio está parado sin previsión. | Servicio parado sin previsión |

### Paso 4.4: Validaciones del Esquema XML
(Anexo 1.9, num. 8.8.2)

| Regla | Y | Validación | Mensaje |
| :--- | :--- | :--- | :--- |
| ZB01 | R | Verificar si el esquema XML está correcto. | Fallo en el esquema XML del archivo |
| ZB02 | R | Verificar la existencia de caracteres de edición en el inicio o fin del mensaje o entre los tags. | No es permitida la presencia de caracteres de edición en el inicio/fin o entre los tags del mensaje |
| ZB03 | R | Verificar si el XML utiliza una codificación diferente de UTF-8. | XML con codificación diferente de UTF-8 |
| ZB04 | R | Verificar las personalizaciones de DIAN (prefijos de namespaces). | XML no cumple con las personalizaciones de UBL-DIAN |

### Paso 4.5: Validaciones del Certificado Digital de Transmisión (Conexión)
(Anexo 1.9, num. 8.8.3)

| Regla | Y | Validación | Mensaje |
| :--- | :--- | :--- | :--- |
| ZC01 | R | Verificar validez del certificado digital de transmisión. | Certificado de la Transmisión vencido |
| ZC02 | R | Error en el acceso a la Lista de Certificados Revocados (CRL): falta la dirección de la CRL (CRLDistributionPoint), o error en el acceso a la CRL o CRL inexistente. | Certificado Firma – Error en el acceso a la CRL |
| ZC03 | R | Verificar la Lista de Certificados Revocados (CRL). | Certificado de Transmisión revocado |
| ZC04 | R | Verificar la cadena de certificación: certificado de la AC emisora no registrado, certificado de AC revocado o certificado no asignado por la AC emisora del certificado. | Certificado de Transmisión – Error en la Cadena de Certificación |
| ZC05 | R | Verificar la cadena de confianza del certificado. | La cadena de confianza no se pudo verificar o se encuentra revocada. |
| ZC06 | R | El certificado debe tener los atributos de conexión. | El certificado no contiene los atributos para realizar conexión de transmisión. |

### Paso 4.6: Validaciones del Certificado Digital de Firma (Firma XML)
(Anexo 1.9, num. 8.8.4)

| Regla | Y | Validación | Mensaje |
| :--- | :--- | :--- | :--- |
| ZD01 | R | Verificar si existe certificado de firma. | Certificado de Firma inexistente en el archivo |
| ZD02 | R | Verificar la validez (fecha de inicio y fecha fin) del certificado digital de la firma. | Certificado de la Firma con data de validez inválida |
| ZD03 | R | Error en el acceso a la Lista de Certificados Revocados (CRL): falta la dirección de la CRL (CRLDistributionPoint), o error en el acceso a la CRL o CRL inexistente. | Certificado de la Firma – Error en el acceso a la CRL |
| ZD04 | R | Verificar la Lista de Certificados Revocados (CRL). | Certificado de la Firma revocado |
| ZD05 | R | Verificar la cadena de certificación: certificado de la AC emisora no registrado, certificado de AC revocado o certificado no asignado por la AC emisora del certificado. | Cadena de confianza del certificado digital es incorrecta, certificado no fue emitido por una ECD avalada por la ONAC. |
| ZD06 | R | Verificar la cadena de confianza del certificado. | La cadena de confianza no se puede verificar o se encuentra revocada. |
| ZD07 | R | El certificado tiene que tener los atributos de no repudio para firmar digitalmente. | El certificado no contiene los atributos para realizar la firma digital con no repudio. |

### Paso 4.7: Validaciones de la Firma
(Anexo 1.9, num. 8.8.5)

| Regla | Y | Validación | Mensaje |
| :--- | :--- | :--- | :--- |
| ZE01 | R | Verificar si la firma está en el estándar (XMLDSig con formato XAdES-EPES). | Certificado de la Firma con estándar inválido |
| ZE02 | R | Verificar si el valor de la firma es válido (difiere del calculado). | Valor de la Firma inválido |
| ZE03 | R | Identificación (ID) del emisor difiere de la identificación (propietario) del certificado digital. | ID del emisor difiere del propietario del Certificado Digital |

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Archivos Nombrados:** Cada documento electrónico tiene un nombre `fv`, `nc`, `nd`, `ar` o `ad` con NIT de 10 dígitos, código `ppp`, año y consecutivo hexadecimal de 8 dígitos.
2. **Paquete Listo:** El ZIP tiene nombre `z...`, contiene un (1) documento si va al servicio sincrónico o menos de 51 si va al asincrónico, y cada XML pesa como máximo 500 KB.
3. **Conexión Aceptada:** El mensaje superó las reglas ZA, ZB, ZC, ZD y ZE, y el documento pasa a las reglas de validación de su tipo (Manuales 08 a 12 y 14).
4. **Consecutivos Actualizados:** Los contadores de archivos y de paquetes quedan incrementados y se reinician el 1 de enero.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si un envío sincrónico es rechazado por cantidad de documentos, la solución es verificar que el ZIP contenga exactamente un (1) documento electrónico (Anexo 1.9, num. 6.8).
- Si un envío asincrónico es rechazado por cantidad de documentos, la solución es fraccionar el lote para que cada ZIP contenga menos de 51 documentos (Anexo 1.9, num. 6.8).
- Si ocurre el error `ZA01`, la solución es reducir el tamaño del archivo XML a 500 KB o menos (Anexo 1.9, num. 8.8.1).
- Si se recibe `ZA02` o `ZA03` (notificaciones de servicio parado), la solución es seguir los procedimientos de inconvenientes tecnológicos del Manual 16 (Anexo 1.9, num. 12).
- Si ocurre el error `ZB02`, la solución es eliminar los caracteres de edición (espacios, saltos de línea) al inicio o fin del mensaje o entre los tags (Anexo 1.9, num. 8.8.2).
- Si ocurre el error `ZB03`, la solución es generar el XML con codificación UTF-8 (Anexo 1.9, num. 8.8.2).
- Si ocurre el error `ZB04`, la solución es usar los prefijos de namespaces definidos en el anexo (ver Manual 20, Paso 4.4) (Anexo 1.9, num. 5.5.1 y 8.8.2).
- Si ocurre un error `ZC01` a `ZC06`, la solución es revisar el certificado de transmisión: vigencia, revocación, acceso a la CRL, cadena de certificación y atributos de conexión (Anexo 1.9, num. 8.8.3).
- Si ocurre un error `ZD01` a `ZD07`, la solución es revisar el certificado de firma: que exista en el archivo, su vigencia, revocación, acceso a la CRL, que haya sido emitido por una ECD avalada por la ONAC y que tenga atributos de no repudio (Anexo 1.9, num. 8.8.4).
- Si ocurre el error `ZE03`, la solución es verificar que la identificación del emisor del documento corresponda al propietario del certificado digital (Anexo 1.9, num. 8.8.5).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Prefijos de Nombre de Archivo**
| Prefijo | Tipo de archivo | Referencia |
| :--- | :--- | :--- |
| `fv` | Factura de Venta (`.xml`) | Num. 6.7 |
| `nc` | Nota Crédito (`.xml`) | Num. 6.7 |
| `nd` | Nota Débito (`.xml`) | Num. 6.7 |
| `ar` | Application Response (`.xml`) | Num. 6.7 |
| `ad` | Attached Document (`.xml`) | Num. 6.7 |
| `z` | Archivo comprimido (`.zip`) entregado a la DIAN | Num. 6.8 |

**Grupos de Reglas de Conexión (num. 8.8)**
| Grupo | Ámbito | Reglas |
| :--- | :--- | :--- |
| ZA | Mensaje del web service | ZA01 (R), ZA02 (N), ZA03 (N) |
| ZB | Esquema XML | ZB01 a ZB04 (R) |
| ZC | Certificado digital de transmisión (conexión) | ZC01 a ZC06 (R) |
| ZD | Certificado digital de firma (firma XML) | ZD01 a ZD07 (R) |
| ZE | Firma | ZE01 a ZE03 (R) |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Nomenclatura de archivos XML (fv, nc, nd, ar, ad) y ZIP (z) para la DIAN, códigos de asignación ppp, límites de documentos por ZIP y reglas de conexión ZA, ZB, ZC, ZD y ZE: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/21_manual_nomenclatura_empaquetado_conexion.md

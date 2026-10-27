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

1. **Factura de Origen Validada:** La factura electrónica sobre la cual recae el evento debe existir en la plataforma DIAN y contar con su respectivo CUFE.
2. **Sincronización NTP:** El reloj del servidor debe estar sincronizado con la hora legal de Colombia (UTC-05:00) para el sellado de tiempo, crítico para determinar los plazos legales (ej. 3 días hábiles para aceptación tácita).
3. **Certificado Digital Vigente:** Contar con un certificado digital X.509 activo expedido por una entidad de certificación abierta (ONAC).
4. **Algoritmo CUDE Configurado:** Disponer de la Clave Técnica o PIN de software para calcular el hash SHA-384 del evento (CUDE).
5. **Secuencialidad Lógica:** Entender que los eventos básicos operan en cadena estricta: Acuse de Recibo (030) -> Recibo del Bien (032) -> Aceptación Expresa (033) o Reclamo (031).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Secuencia (RADIAN):** La DIAN rechazará un evento de "Recibo del Bien" si la factura no cuenta previamente con un "Acuse de Recibo". Igualmente, rechazará una "Aceptación" o "Reclamo" si faltan los eventos previos.
- **Regla de Identificadores (CUDE):** Todos los eventos (sin excepción) generan su propio Código Único de Documento Electrónico (CUDE) calculado mediante el algoritmo SHA-384. El evento NO usa CUFE.
- **Regla de Referencia Obligatoria:** Es mandatorio incluir la referencia exacta al documento comercial afectado (CUFE de la factura) en el grupo `DocumentReference`.
- **Regla de Exclusividad de Firma:** El XML del evento debe contener un solo nodo `ds:Signature` que garantiza el no repudio de la acción comercial o legal ejecutada.
- **Codificación:** El documento XML debe estar codificado obligatoriamente en `UTF-8`.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CONSTRUCCIÓN DEL XML PASO A PASO)

### Paso 4.1: Definición del Nodo Raíz y Cabecera del Documento
Declara el elemento raíz `<ApplicationResponse>` con los *namespaces* estándar.

- **Ruta:** `/ApplicationResponse`
- **Nodos Obligatorios:**
  * `cbc:UBLVersionID`: Fijo en `UBL 2.1`.
  * `cbc:CustomizationID`: El identificador de la operación (Ej. el código del evento DIAN o una plantilla específica para RADIAN).
  * `cbc:ProfileID`: Fijo en `DIAN 2.1` o según especificidad del Anexo 1.8.
  * `cbc:ProfileExecutionID`: `1` (Producción) o `2` (Pruebas).
  * `cbc:ID`: Número consecutivo interno de quien genera el evento.
  * `cbc:UUID`: CUDE del evento, calculado con SHA-384 (`@schemeID="CUDE-SHA384"`).
  * `cbc:IssueDate` y `cbc:IssueTime`: Fecha y hora de generación del evento.

### Paso 4.2: Construcción de Extensiones DIAN (`UBLExtensions`)
Bloque para alojar información de control y la firma digital.

- **Ruta:** `/ApplicationResponse/ext:UBLExtensions`
- **Procedimiento:**
  1. **Extensión de Control (`sts:DianExtensions`):** Información del software emisor y código de seguridad si el anexo técnico lo exige para el tipo de evento.
  2. **Firma Digital (`ds:Signature`):** Segundo `UBLExtension` que aloja la firma Xades-EPES del documento XML canónico que confiere validez jurídica al evento.

### Paso 4.3: Emisor del Evento (`SenderParty`)
Define quién está ejecutando la acción (quien firma el evento).

- **Ruta:** `/ApplicationResponse/cac:SenderParty`
- **Nodos Obligatorios:**
  * Si el evento es un acuse de recibo, el `SenderParty` será el cliente (Adquiriente).
  * Debe contener el NIT (`cbc:CompanyID` con `schemeID="31"`), Dígito de Verificación y Razón Social o Nombre.

### Paso 4.4: Receptor del Evento (`ReceiverParty`)
Define a quién va dirigido el evento.

- **Ruta:** `/ApplicationResponse/cac:ReceiverParty`
- **Nodos Obligatorios:**
  * Generalmente corresponde al emisor de la factura original.
  * Debe contener NIT, Dígito de Verificación y Razón Social.

### Paso 4.5: Declaración del Evento y Referencia (`DocumentResponse`)
Paso crítico donde se especifica qué acción se está realizando y sobre qué documento.

- **Ruta:** `/ApplicationResponse/cac:DocumentResponse`
- **Procedimiento:**
  1. **Grupo `cac:Response`:**
     * `cbc:ResponseCode`: Código numérico oficial de la DIAN para el evento (Ej. `030`, `032`, `033`).
     * `cbc:Description`: Texto descriptivo (Ej. "Acuse de recibo de la factura electrónica de venta").
  2. **Grupo `cac:DocumentReference`:**
     * `cbc:ID`: Número de la factura afectada.
     * `cbc:UUID`: CUFE original de la factura afectada, con el atributo `@schemeName="CUFE-SHA384"`.
     * `cbc:DocumentTypeCode`: Fijo en `01` (Factura de Venta).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Estado Generado:** El XML se ha estructurado en memoria, cuenta con el CUDE validado y hace referencia estricta a la factura base.
2. **Estado Firmado:** La firma digital asegura la autenticidad y no repudio del evento generado, consolidando su peso probatorio (título valor).
3. **Estado Empaquetado:** El archivo se comprime en un `.zip` listo para enviarse al Web Service de registro de eventos de la DIAN.
4. **Respuesta Esperada:** La DIAN procesará el evento y responderá con otro `ApplicationResponse` validando la correcta aplicación en su base de datos. Una vez registrados el `030`, `032` y `033`, la factura se habilita comercialmente para operaciones de factoring (RADIAN).

---

## 6. Troubleshooting (Solución de Errores)

- Si ocurre el error `Secuencia de Eventos Inválida`, la solución es garantizar a nivel de código o interfaz de usuario que no se intente enviar una Aceptación Expresa (033) o Reclamo (031) sin haber procesado exitosamente el Acuse de Recibo (030) y el Recibo del Bien (032).
- Si ocurre el error `CUDE Invalido en Evento`, la solución es auditar la cadena de texto base usada para el SHA-384, verificando que los parámetros coincidan con la estructura solicitada para el documento `ApplicationResponse`.
- Si ocurre el error `Referencia de Factura Invalida`, la solución es verificar que el `cbc:UUID` dentro del bloque `DocumentReference` contenga exactamente el CUFE de la factura, sin espacios y reportado en el momento original de la emisión.
- Si ocurre el error `Regla FAD09 - Fecha Inválida`, la solución es corroborar la sincronización del servidor (NTP), ya que el evento no puede tener una fecha anterior a la expedición de la factura, ni superior a la fecha actual del sistema de la DIAN.

---

## 7. Tablas de Referencia y Anexos Técnicos

**Códigos de Eventos de Facturación (ResponseCode - ApplicationResponse)**
| Código (ResponseCode) | Nombre del Evento | Descripción / Uso Comercial |
| :--- | :--- | :--- |
| **030** | Acuse de recibo de Factura | Emitido por el adquiriente indicando la recepción de la factura y contenedor XML. |
| **032** | Recibo del bien o servicio | Emitido por el adquiriente confirmando la entrega física o prestación satisfactoria. |
| **033** | Aceptación expresa | Aprobación total de las condiciones comerciales. Convierte la factura en título valor. |
| **031** | Reclamo o Rechazo | Rechazo de la factura por inconsistencias. Inhabilita la aceptación expresa. |
| **034** | Aceptación tácita | Emitido por el proveedor si transcurren 3 días hábiles tras el evento 032 sin reclamos. |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Generación de Registro de Eventos (ApplicationResponse) UBL 2.1, Acuse de Recibo, Aceptación y RADIAN, cálculos CUDE y DocumentReference: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/12_manual_registro_eventos_radian.md

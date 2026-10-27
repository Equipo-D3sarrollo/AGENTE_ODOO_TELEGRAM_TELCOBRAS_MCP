<!-- Path: finanzas_y_contabilidad/16_manual_contingencias_tecnologicas.md -->

# ⚠️ Manual Operativo 16: Contingencias Tecnológicas (Tipo 03 y Tipo 04) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Soporte y Operaciones Técnicas  
**Rol Requerido:** Administrador del Sistema, Soporte Nivel 2/3, Desarrollador Backend, Oficial de Cumplimiento  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando el sistema experimente caídas de infraestructura local, fallas de conexión a internet o daño en servidores que impidan facturar electrónicamente (Contingencia Tipo 03), o cuando la DIAN declare oficialmente la indisponibilidad de sus Servicios Informáticos Electrónicos (Contingencia Tipo 04).

---

# MANUAL DE CONTINGENCIAS TECNOLÓGICAS (TIPO 03 Y TIPO 04)

**ID del Manual:** DIAN-UBL2.1-M16  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** XML Transcrito (Tipo 03), Cola de Envíos Asíncronos (Tipo 04)  
**Propósito:** Detallar los protocolos operativos y estructurales obligatorios para garantizar la continuidad del negocio durante escenarios de indisponibilidad técnica, asegurando el cumplimiento normativo en la expedición de comprobantes físicos y su posterior transmisión electrónica.

---

## 1. PROCEDIMIENTO GENERAL

Las contingencias tecnológicas son los mecanismos de excepción legales que permiten a los obligados a facturar continuar con su operación comercial cuando los sistemas fallan. El procedimiento varía drásticamente según el origen de la falla: 
* Si el origen de la falla es el Facturador (Tipo 03), el sistema debe detener la generación de XML, pasar a facturación física (talonario) y posteriormente realizar una transcripción electrónica de dichos documentos físicos. 
* Si el origen de la falla es la DIAN (Tipo 04), el sistema sigue generando y firmando XML normalmente, pero detiene la transmisión, encolando los documentos para un envío masivo o diferido una vez se restablezca el servicio oficial.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Resolución de Contingencia Activa:** El facturador debe contar con una resolución de numeración tipo "Factura de Talonario o de Papel" previamente autorizada por la DIAN y configurada (inactiva por defecto) en el sistema.
2. **Talonarios Físicos Disponibles:** El establecimiento comercial debe tener físicamente los talonarios impresos por una litografía autorizada, correspondientes a la resolución del punto 1.
3. **Mecanismo de Encolamiento (Queue):** El motor backend debe disponer de un sistema de colas (ej. RabbitMQ, Kafka, o tabla de base de datos de estados transaccionales) para almacenar de forma segura los XML no transmitidos.
4. **Módulo de Transcripción:** La interfaz de usuario (UI) debe contar con un formulario específico para la digitación manual (transcripción) de las facturas de papel emitidas durante la caída.
5. **Monitoreo de Timeouts:** El sistema debe contar con *circuit breakers* que detecten caídas (HTTP 500, 503, o timeouts > 30s) al intentar consumir el Web Service `vpfe.dian.gov.co`.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Plazos Máximos:** Todo documento expedido en contingencia (sea Tipo 03 o Tipo 04) debe ser transmitido a la DIAN en un plazo máximo de **cuarenta y ocho (48) horas** contadas a partir de la superación de la falla (Tipo 03) o del comunicado oficial de levantamiento (Tipo 04).
- **Regla de Entorno de Contingencia (Tipo 03):** El atributo `ProfileExecutionID` dentro del XML debe cambiar estrictamente del valor `1` (Operación Normal) al valor `2` (Contingencia).
- **Regla de Legalidad (Tipo 04):** El facturador NO puede autodeclararse en contingencia Tipo 04. Esta solo es válida si la DIAN publica un comunicado oficial de intermitencia de sus Servicios Informáticos Electrónicos (SIE).
- **Regla de Referencia Cruzada:** Toda factura electrónica producto de una transcripción (Tipo 03) debe llevar incrustada la referencia exacta (prefijo y número) de la factura de papel física que le fue entregada al cliente.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (CÁLCULOS Y ESTRUCTURAS)

### Paso 4.1: Activación y Operación en Contingencia Tipo 03 (Falla del Facturador)
Cuando el sistema ERP, la red local o el proveedor tecnológico fallan.

- **Acción Operativa:** Los cajeros o vendedores proceden a expedir facturas físicas de talonario.
- **Acción del Sistema:** El administrador marca en el sistema el inicio de la "Contingencia Local". El sistema bloquea los envíos automáticos e interrumpe el consumo de folios electrónicos.

### Paso 4.2: Transcripción del Documento Físico a XML (Post-Contingencia Tipo 03)
Una vez superada la falla, los documentos de papel deben digitarse en el sistema.

- **Ruta XML Perfil:** `/Invoice/ext:UBLExtensions/.../sts:DianExtensions/sts:InvoiceControl/sts:InvoiceAuthorization` (Debe ser la resolución del talonario de papel).
- **Atributo Clave:** `<cbc:ProfileExecutionID>2</cbc:ProfileExecutionID>`
- **Fecha y Hora:** `<cbc:IssueDate>` y `<cbc:IssueTime>` NO deben ser el momento de la digitación, sino la fecha y hora exactas en las que se elaboró la factura física entregada al adquiriente.
- **Referencia al Documento Físico:** Se debe agregar el bloque `AdditionalDocumentReference` indicando el folio del talonario físico.
- **Cálculo de CUFE:** Se calcula normalmente con el algoritmo SHA-384, pero utilizando el prefijo y número de la factura de papel.

### Paso 4.3: Activación y Operación en Contingencia Tipo 04 (Falla de la DIAN)
Cuando el sistema local funciona perfecto, pero los Web Services de la DIAN retornan errores o timeouts constantes y existe un aviso oficial.

- **Acción Operativa:** El comercio sigue vendiendo y facturando de manera 100% electrónica.
- **Estructura XML:** 
  - `<cbc:ProfileExecutionID>1</cbc:ProfileExecutionID>` (Se mantiene en 1).
  - El XML se genera y firma con Xades-EPES de forma normal.
  - Se genera el Código QR asumiendo un CUFE válido.
- **Entrega al Adquiriente:** El PDF y el XML se entregan de inmediato al cliente. El PDF puede llevar una marca de agua: *"Emitido en Contingencia DIAN (Transmisión Diferida)"*. No se requiere el estado `Validado por la DIAN` para su entrega.

### Paso 4.4: Transmisión Diferida y Liberación de Colas (Post-Contingencia Tipo 04)
Al normalizarse el servicio de la DIAN (comunicado de levantamiento).

- **Mecanismo de Envío:** El sistema debe agrupar los XML represados en un archivo comprimido ZIP.
- **Consumo de Servicio Web:** Se recomienda usar el método asíncrono `SendZip` (Lotes) en lugar de enviar documento por documento mediante `SendBillSync`, para evitar saturación y penalizaciones por exceso de peticiones.
- **Actualización de Estado:** El motor procesa el `GetStatus` (o `GetStatusZip`) y actualiza el estado de las facturas en la base de datos de `Encolado` a `Aceptado`.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Sincronización Completa:** Todas las operaciones de contingencia fueron exitosamente procesadas y cuentan con su respectivo ApplicationResponse (`Acuse de Aceptación`) por parte de la DIAN.
2. **Trazabilidad de Logs:** El sistema almacena evidencia técnica (Logs de timeouts, errores HTTP 5xx) que justifican los horarios de las caídas de transmisión, preparándose para posibles auditorías.
3. **Consumo de Folios:** El consecutivo de la resolución de papel fue actualizado y conciliado correctamente para el caso de Tipo 03.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `Regla 90: Fecha de emisión fuera del rango permitido`, la solución es verificar que la transmisión no haya excedido el límite normativo de 48 horas post-contingencia. Si se excede, requerirá justificación formal ante la DIAN o pago de extemporaneidad.
- Si ocurre el error `Resolución no autorizada para el tipo de documento`, la solución es revisar el Tipo 03; frecuentemente ocurre al intentar transcribir una factura de papel usando la resolución de factura electrónica estándar. Se debe usar estrictamente la resolución asignada para talonario contingencia.
- Si ocurre el error `ProfileExecutionID Inválido`, confirme que no esté enviando el valor `2` durante una falla de la DIAN. El valor `2` es EXCLUSIVO para fallas del facturador (Tipo 03).
- Si hay bloqueos por "Demasiadas peticiones" tras un Tipo 04, la solución es implementar un patrón de *Throttling* en el vaciado de la cola (ej. enviar lotes de 100 facturas cada minuto).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipificación de Contingencias y Parámetros UBL 2.1**
| Escenario (Tipo) | Causante de la Falla | Facturación en Papel | ProfileExecutionID | Tiempo de Transmisión Máximo |
| :--- | :--- | :--- | :--- | :--- |
| **Tipo 03** | Facturador / Proveedor Tec. | **SÍ (Obligatorio)** | `2` | 48 Horas desde superada la falla |
| **Tipo 04** | DIAN (Servicios SIE) | **NO** (Sigue Electrónico) | `1` | 48 Horas desde aviso de restablecimiento |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Contingencias Tecnológicas DIAN, Tipo 03 y Tipo 04, Transmisión Diferida, Facturación en Papel y Transcripción, ProfileExecutionID: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/16_manual_contingencias_tecnologicas.md

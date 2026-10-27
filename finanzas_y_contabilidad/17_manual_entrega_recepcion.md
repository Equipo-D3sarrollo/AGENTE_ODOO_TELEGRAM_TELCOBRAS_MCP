<!-- Path: finanzas_y_contabilidad/17_manual_entrega_recepcion.md -->

# 📨 Manual Operativo 17: Entrega y Recepción (Interoperabilidad) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad  
**Rol Requerido:** Desarrollador de Integración, Analista de Cuentas por Pagar/Cobrar, Arquitecto de Software  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual al implementar los mecanismos de distribución de facturas hacia los adquirentes (clientes) y el buzón tributario de recepción de facturas de proveedores, asegurando la correcta lectura de archivos `.ZIP`, validación de esquemas y generación de eventos de acuse (Título Valor).

---

# MANUAL DE ENTREGA Y RECEPCIÓN (INTEROPERABILIDAD)

**ID del Manual:** DIAN-UBL2.1-M17  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Archivo Contenedor `.ZIP`, Correo Electrónico (SMTP/IMAP) y XML (ApplicationResponse)  
**Propósito:** Definir estrictamente las reglas de nomenclatura, estructura de correo electrónico y generación del contenedor electrónico exigidas por la DIAN para la entrega de facturas al comprador, así como estandarizar la recepción y expedición de eventos de acuse de recibo y mercancías para proveedores.

---

## 1. PROCEDIMIENTO GENERAL

La interoperabilidad garantiza que los diferentes softwares del mercado colombiano puedan leer e interpretar automáticamente los documentos electrónicos sin intervención humana. El proceso se divide en dos vías: 
1. **Emisión (Entrega):** Consiste en encapsular el XML de la factura validada por la DIAN y su PDF en un archivo `.ZIP`, enviándolo por correo electrónico bajo una estructura de "Asunto" estandarizada. 
2. **Recepción (Compras):** Consiste en leer correos entrantes, extraer el XML del proveedor, validarlo y emitir (mediante el formato `ApplicationResponse`) los eventos requeridos para convertir la factura en título valor (Acuse de Recibo y Recibo de Bienes/Servicios).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Servidor de Correo (SMTP/IMAP):** Configuración de un servidor SMTP para el envío masivo transaccional de facturas y un buzón IMAP dedicado (ej. `recepcionfe@miempresa.com`) para la lectura automatizada de compras.
2. **Generador de PDF:** Motor de renderizado habilitado para leer la data del XML y plasmarla gráficamente incluyendo el Código QR y el CUFE.
3. **Módulo de Eventos (RADIAN):** Capacidad del sistema para firmar y transmitir documentos `ApplicationResponse` hacia la DIAN al momento de recibir facturas de terceros.
4. **Almacenamiento (Storage):** Repositorio seguro en la nube o local para almacenar los `.ZIP` de emisión y recepción durante el periodo de conservación legal (mínimo 5 años).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Formato del Archivo Adjunto:** El único formato válido para transmitir electrónicamente la factura al cliente es un archivo comprimido `.ZIP` que contenga el `AttachedDocument` (XML) y opcionalmente el PDF.
- **Regla del XML Contenedor (AttachedDocument):** El XML enviado al cliente NO es la factura pura generada en el paso 1, sino un contenedor `AttachedDocument` que envuelve dos elementos: la Factura Electrónica y el `ApplicationResponse` (Validación oficial de la DIAN).
- **Regla del Asunto del Correo:** El asunto del email debe seguir estrictamente un patrón de separación por punto y coma (`;`) sin espacios alrededor del separador.
- **Regla de Aceptación Tácita:** Si el comprador (recepción) genera el Acuse de Recibo y el Recibo de Mercancía, pero no emite Aceptación Expresa o Reclamo en los 3 días hábiles siguientes, el sistema debe permitir al proveedor registrar la "Aceptación Tácita".

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (INTEROPERABILIDAD PASO A PASO)

### Paso 4.1: Estructuración del Contenedor `.ZIP` (Entrega al Cliente)
El archivo a adjuntar en el correo electrónico.

- **Nomenclatura del ZIP:** Debe seguir el estándar de la DIAN. Ejemplo: `z{NIT_Facturador}{Prefijo}{NumeroFactura}.zip` o equivalente según el Anexo 1.9.
- **Contenido del ZIP:** 
  1. `AttachedDocument.xml` (El XML canónico y validado por la DIAN).
  2. `RepresentacionGrafica.pdf` (El archivo visual para el usuario).

### Paso 4.2: Asunto Estandarizado del Correo Electrónico (Entrega)
El motor de envío debe concatenar las variables comerciales separadas por punto y coma (`;`), en este orden exacto:

**Estructura requerida:**
`NIT del Facturador; Nombre del Facturador; Número de Factura; Código del Tipo de Documento; Nombre Comercial Facturador; CUFE o CUDE`

**Ejemplo de Asunto (Subject):**
`900123456;Empresa XYZ SAS;SETP99001;01;Almacenes XYZ;7b3f9...d2e`

### Paso 4.3: Representación Gráfica (PDF)
Aunque la DIAN regula principalmente el XML, el PDF de entrega es obligatorio y debe cumplir con requisitos visuales mínimos:
- Mostrar la leyenda "Factura Electrónica de Venta".
- Renderizar el Código QR validable (URL apuntando al catálogo de la DIAN).
- Exponer el CUFE de forma legible.
- Desglosar claramente los impuestos (IVA, INC, Retenciones).

### Paso 4.4: Recepción Automática de Facturas (Compras)
Cuando un proveedor envía una factura al buzón IMAP de la empresa:
1. El sistema lee el asunto y descarga el archivo `.ZIP`.
2. Extrae el `AttachedDocument.xml`.
3. Valida la firma digital del proveedor y el sello de la DIAN.
4. Mapea la información hacia el módulo de "Cuentas por Pagar".

### Paso 4.5: Generación de Acuses y Título Valor (Eventos DIAN)
Para deducir los costos/gastos fiscalmente, el sistema receptor debe generar y enviar a la DIAN XMLs tipo `ApplicationResponse`:

1. **Evento 030 - Acuse de Recibo de la Factura:** Se genera automáticamente al procesar el XML válido desde el buzón de correo.
2. **Evento 032 - Recibo del Bien o Servicio:** Generado por el usuario de bodega o supervisor tras verificar que la entrega es conforme.
3. **Evento 033 - Aceptación Expresa:** Generada por contabilidad para confirmar la obligación de pago. (Opcionalmente Evento 031 si hay Reclamo).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Email Entregado:** El sistema registra en base de datos la confirmación de entrega del servidor SMTP o servicios como SendGrid/AWS SES.
2. **Cuentas por Pagar Actualizadas:** La factura del proveedor entra en estado "Recibida" y posteriormente "Acusada", lista para provisión contable.
3. **Título Valor Habilitado:** Una vez emitido el evento 032 (Recibo de Mercancía) y validado por la DIAN, la factura queda habilitada legalmente para factoring (Radian) y deducción fiscal (Renta/IVA).

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `Rechazo de buzón del cliente`, la solución es validar el tamaño del `.ZIP`. Muchos servidores bloquean adjuntos superiores a 2MB. Asegúrese de que el PDF esté optimizado.
- Si las facturas de proveedores no se importan automáticamente, verifique la codificación del correo electrónico; algunos proveedores envían correos en formatos no estándar. El lector IMAP debe estar preparado para decodificar Base64 o Quoted-Printable.
- Si el `Evento 032 (Recibo de Bienes)` es rechazado por la DIAN, verifique que la fecha y hora (`IssueDate`, `IssueTime`) del evento sean estrictamente superiores (posteriores) a la fecha y hora del `Evento 030 (Acuse de Recibo)`.
- Si el asunto del correo es clasificado como Spam por el cliente, asegúrese de que el dominio emisor cuente con los registros SPF, DKIM y DMARC correctamente configurados.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Catálogo de Eventos de Recepción (ApplicationResponse - RADIAN)**
| Código de Evento | Descripción del Evento | ¿Quién lo emite? | Prerrequisito |
| :--- | :--- | :--- | :--- |
| **030** | Acuse de recibo de la Factura Electrónica de Venta | Comprador / Adquirente | Estructura XML Válida |
| **032** | Recibo del bien o prestación del servicio | Comprador / Adquirente | Evento 030 previo |
| **033** | Aceptación expresa de la factura | Comprador / Adquirente | Evento 032 previo |
| **031** | Reclamo de la Factura | Comprador / Adquirente | Evento 030 (Antes del 033) |

**Codificación Tipo de Documento para Asunto del Correo**
| Código | Tipo de Documento |
| :--- | :--- |
| **01** | Factura Electrónica de Venta |
| **91** | Nota Crédito |
| **92** | Nota Débito |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Interoperabilidad DIAN, Recepción de Facturas, Asunto de Correo, AttachedDocument, Acuse de Recibo Eventos 030 032 033: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/17_manual_entrega_recepcion.md

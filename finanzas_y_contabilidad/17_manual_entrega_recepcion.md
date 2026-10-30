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

1. **Servidor de Correo (SMTP/IMAP):** Configuración de un servidor SMTP para el envío masivo transaccional de facturas y un buzón IMAP dedicado (ej. `recepcionfe@miempresa.com`) para la lectura automatizada de compras. Durante la habilitación se diligencia, como campo obligatorio, el correo electrónico ÚNICO de recepción de documentos electrónicos, que se usa para la expedición del `AttachedDocument` del facturador al adquiriente y para la entrega del `AttachedDocument` del adquiriente al facturador; el emisor debe informar dicho correo en el campo de correo electrónico de la factura (Anexo 1.9, num. 9).
2. **Capacidad del Buzón:** El buzón de recepción debe garantizar en cualquier momento un espacio disponible mínimo de 20 Megas (Anexo 1.9, num. 9.1 y 9.2).
3. **Generador de PDF:** Motor de renderizado habilitado para leer la data del XML y plasmarla gráficamente incluyendo el Código QR y el CUFE.
4. **Módulo de Eventos (RADIAN):** Capacidad del sistema para firmar y transmitir documentos `ApplicationResponse` hacia la DIAN al momento de recibir facturas de terceros.
5. **Almacenamiento (Storage):** Repositorio seguro en la nube o local para almacenar los `.ZIP` de emisión y recepción durante el periodo de conservación legal (mínimo 5 años).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Formato del Archivo Adjunto:** El único formato válido para transmitir electrónicamente la factura al cliente es un archivo comprimido `.ZIP` que contenga el `AttachedDocument` (XML) y opcionalmente el PDF. Dentro del mismo único `.ZIP` también se pueden incluir de manera opcional documentos complementarios, estos dentro de un archivo `.zip` (Anexo 1.9, num. 9.1).
- **Regla de Peso:** El peso máximo por envío es de 2 Megas. El cuerpo del correo no está reglamentado (Anexo 1.9, num. 9.1 y 9.2).
- **Regla de Portales:** El facturador electrónico no puede obligar al adquiriente a la descarga de los documentos electrónicos a través de portales web (Anexo 1.9, num. 9).
- **Regla del XML Contenedor (AttachedDocument):** El XML enviado al cliente NO es la factura pura generada en el paso 1, sino un contenedor `AttachedDocument` que envuelve dos elementos: la Factura Electrónica y el `ApplicationResponse` (Validación oficial de la DIAN).
- **Regla del Asunto del Correo:** El asunto del email debe seguir estrictamente un patrón de separación por punto y coma (`;`) sin espacios alrededor del separador.
- **Regla de Aceptación Tácita:** Si el comprador (recepción) genera el Acuse de Recibo y el Recibo de Mercancía, pero no emite Aceptación Expresa o Reclamo en los 3 días hábiles siguientes, el sistema debe permitir al proveedor registrar la "Aceptación Tácita". La aceptación tácita (`034`) solo se transmite pasados 3 días hábiles después del evento `032`, y la aceptación expresa (`033`) no puede generarse pasados esos 3 días hábiles (Anexo 1.9, reglas LGC14 y LGC06).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (INTEROPERABILIDAD PASO A PASO)

### Paso 4.1: Estructuración del Contenedor `.ZIP` (Entrega al Cliente)
El archivo a adjuntar en el correo electrónico.

- **Nomenclatura del ZIP:** Los nombres (FileName) de los archivos que se envían por correo al adquiriente no están reglamentados por la DIAN; corresponden a un acuerdo entre las partes (Anexo 1.9, num. 9.1).
- **Contenido del ZIP:** 
  1. `AttachedDocument.xml` (El XML canónico y validado por la DIAN), que contiene el ApplicationResponse de aprobación de la DIAN y la factura electrónica, nota crédito o nota débito (Anexo 1.9, num. 9.1).
  2. `RepresentacionGrafica.pdf` (El archivo visual para el usuario). Opcional dentro del mismo `.ZIP` (Anexo 1.9, num. 9.1).
  3. Documentos complementarios (opcional), dentro de un archivo `.zip` (Anexo 1.9, num. 9.1).

### Paso 4.2: Asunto Estandarizado del Correo Electrónico (Entrega)
El motor de envío debe concatenar las variables comerciales separadas por punto y coma (`;`), en este orden exacto:

**Estructura requerida:**
`NIT del Facturador; Nombre del Facturador; Número de Factura; Código del Tipo de Documento; Nombre Comercial Facturador; Línea de negocio`

- La línea de negocio es opcional (acuerdo comercial entre las partes) y no está en el XML (Anexo 1.9, num. 9.1).
- Origen de cada campo en el XML: NIT del Facturador `//cac:AccountingSupplierParty/cac:Party/cac:PartyTaxScheme/cbc:CompanyID`; Nombre del Facturador `//cac:AccountingSupplierParty/cac:Party/cac:PartyTaxScheme/cbc:RegistrationName`; Número del documento `//cbc:ID`; Código del tipo de documento `//cbc:InvoiceTypeCode` (para notas crédito y débito, los valores de la tabla correspondiente); Nombre comercial `//cac:AccountingSupplierParty/cac:Party/cac:PartyName/cbc:Name` o `//cac:AccountingSupplierParty/cac:Party/cac:PartyTaxScheme/cbc:RegistrationName` (Anexo 1.9, num. 9.1).

**Ejemplo de Asunto (Subject):**
`99998888;Facturador Ejemplo;FEV500;01;Facturador Ejemplo;ContabilidadBog` (Anexo 1.9, num. 9.1)

**Asunto para Eventos (Anexo 1.9, num. 9.2):**
`Evento; Número del documento referenciado; NIT del generador del evento; Nombre del generador del evento; Número del documento electrónico; Código del tipo de documento; Línea de negocio`

- La palabra `Evento` va literal; el número del documento referenciado es `/Invoice/cbc:ID`; el NIT y nombre del generador salen de `/ApplicationResponse/cac:SenderParty/cac:PartyTaxScheme`; el número del documento electrónico es `/ApplicationResponse/cbc:ID`; el código es `/ApplicationResponse/cac:DocumentResponse/cac:Response/cbc:ResponseCode`; la línea de negocio es opcional.
- El adjunto es un único `.zip` con un `AttachedDocument` que contiene el ApplicationResponse (DIAN) y el ApplicationResponse (Evento), con las mismas reglas de PDF opcional, documentos complementarios, 2 Megas y buzón de 20 Megas.
- Ejemplo: `Evento;FEV500;10203040;Facturador Ejemplo;APPR10;030;ContabilidadBog`

### Paso 4.3: Representación Gráfica (PDF)
Aunque la DIAN regula principalmente el XML, cuando se entrega el PDF debe cumplir con requisitos visuales mínimos. *Nota: dentro del `.ZIP` enviado por correo el PDF es opcional (Anexo 1.9, num. 9.1); la impresión de la representación gráfica es obligatoria cuando el adquiriente no facturador no informa el medio de recepción (Anexo 1.9, num. 9.3). La representación gráfica siempre es una representación del XML, que es el documento con valor legal, y la información que presenta debe estar en el XML (Anexo 1.9, num. 11.1.7).*
- Mostrar la leyenda "Factura Electrónica de Venta".
- Renderizar el Código QR validable (URL apuntando al catálogo de la DIAN), con un tamaño mínimo de 2 cm y en todas las páginas de la representación gráfica (Anexo 1.9, num. 11.1.7).
- Exponer el CUFE de forma legible.
- Desglosar claramente los impuestos (IVA, INC, Retenciones).

### Paso 4.3.1: Entrega a Adquirentes que no son Facturadores Electrónicos
El adquirente señala el medio por el cual autoriza la entrega de la factura electrónica de venta (Anexo 1.9, num. 9.3):
- Por correo electrónico a la dirección que suministró, o por cualquier otro medio o dispositivo electrónico que señale, en formato digital de representación gráfica.
- Por correo electrónico a la dirección que suministró, o por cualquier otro medio o dispositivo electrónico que señale, en el formato electrónico de generación junto con el documento de validación «Documento validado por la DIAN», incluidos en el contenedor electrónico.
- Impresión de representación gráfica.
- Por envío electrónico entre el servidor del facturador y el del adquirente, siempre que exista acuerdo entre ellos.
- Si el adquirente no informa o señala el medio de recepción, se debe expedir una impresión de representación gráfica.

### Paso 4.4: Recepción Automática de Facturas (Compras)
Cuando un proveedor envía una factura al buzón IMAP de la empresa:
1. El sistema lee el asunto y descarga el archivo `.ZIP`.
2. Extrae el `AttachedDocument.xml`.
3. Valida la firma digital del proveedor y el sello de la DIAN.
4. Mapea la información hacia el módulo de "Cuentas por Pagar".

### Paso 4.5: Generación de Acuses y Título Valor (Eventos DIAN)
Para el registro de la factura como título valor, el sistema receptor debe generar y enviar a la DIAN XMLs tipo `ApplicationResponse` (Anexo 1.9, num. 5):

1. **Evento 030 - Acuse de Recibo de la Factura:** Se genera automáticamente al procesar el XML válido desde el buzón de correo. Debe informar los datos de la persona que recibió la factura en `cac:IssuerParty/cac:Person` (Anexo 1.9, num. 6.5.5.1).
2. **Evento 032 - Recibo del Bien o Servicio:** Generado por el usuario de bodega o supervisor tras verificar que la entrega es conforme. Requiere un acuse de recibo (`030`) previo y debe informar los datos de la persona que recibió el bien o servicio en `cac:IssuerParty/cac:Person` (Anexo 1.9, num. 6.5.5.3; regla LGC09).
3. **Evento 033 - Aceptación Expresa:** Generada por contabilidad para confirmar la obligación de pago. (Opcionalmente Evento 031 si hay Reclamo). La aceptación expresa requiere los eventos `030` y `032` previos y no puede generarse pasados 3 días hábiles del `032`; el reclamo (`031`) requiere los eventos `030` y `032` previos y no procede si la factura ya fue aceptada (Anexo 1.9, reglas LGC02, LGC03, LGC06, LGC12 y LGC13).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Email Entregado:** El sistema registra en base de datos la confirmación de entrega del servidor SMTP o servicios como SendGrid/AWS SES.
2. **Cuentas por Pagar Actualizadas:** La factura del proveedor entra en estado "Recibida" y posteriormente "Acusada", lista para provisión contable.
3. **Título Valor:** Para el registro de la factura como título valor en el RADIAN se validan la fecha de vencimiento de la factura, el acuse de recibo (`030`), el recibo del bien o prestación del servicio (`032`) y la aceptación expresa (`033`), la aceptación tácita (`034`) o el reclamo (`031`) (Anexo 1.9, num. 5).

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `Rechazo de buzón del cliente`, la solución es validar el tamaño del `.ZIP`. El peso máximo por envío es de 2 Megas y el buzón de recepción debe garantizar un espacio disponible mínimo de 20 Megas (Anexo 1.9, num. 9.1). Asegúrese de que el PDF esté optimizado.
- Si las facturas de proveedores no se importan automáticamente, verifique la codificación del correo electrónico; algunos proveedores envían correos en formatos no estándar. El lector IMAP debe estar preparado para decodificar Base64 o Quoted-Printable.
- Si el `Evento 032 (Recibo de Bienes)` es rechazado por la DIAN, verifique que la fecha y hora (`IssueDate`, `IssueTime`) del evento sean estrictamente superiores (posteriores) a la fecha y hora del `Evento 030 (Acuse de Recibo)`, y que exista un acuse de recibo (`030`) previo registrado en la DIAN (Anexo 1.9, regla LGC09).
- Si el asunto del correo es clasificado como Spam por el cliente, asegúrese de que el dominio emisor cuente con los registros SPF, DKIM y DMARC correctamente configurados.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Catálogo de Eventos de Recepción (ApplicationResponse - RADIAN)**
| Código de Evento | Descripción del Evento | ¿Quién lo emite? | Prerrequisito |
| :--- | :--- | :--- | :--- |
| **030** | Acuse de recibo de la Factura Electrónica de Venta | Comprador / Adquirente | Estructura XML Válida; la factura debe tener el evento «Documento validado por la DIAN» (Anexo 1.9, num. 6.5.5) |
| **032** | Recibo del bien o prestación del servicio | Comprador / Adquirente | Evento 030 previo |
| **033** | Aceptación expresa de la factura | Comprador / Adquirente | Eventos 030 y 032 previos; dentro de los 3 días hábiles siguientes al 032 (LGC06, LGC12, LGC13) |
| **031** | Reclamo de la Factura | Comprador / Adquirente | Eventos 030 y 032 previos; no procede si la factura fue aceptada expresa o tácitamente (LGC02, LGC03) |
| **034** | Aceptación tácita | Emisor / Facturador Electrónico (lo recibe la DIAN) | Pasados 3 días hábiles después del 032 (LGC14) |

**Codificación Tipo de Documento para Asunto del Correo**

*Nota: el código del tipo de documento sale de `//cbc:InvoiceTypeCode`; las tablas de valores están en la Caja de Herramientas de la DIAN (Anexo 1.9, num. 9.1). Para eventos se usa el `ResponseCode` del evento (num. 9.2).*

| Código | Tipo de Documento |
| :--- | :--- |
| **01** | Factura Electrónica de Venta |
| **91** | Nota Crédito |
| **92** | Nota Débito |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Interoperabilidad DIAN, Recepción de Facturas, Asunto de Correo, AttachedDocument, Acuse de Recibo Eventos 030 032 033: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/17_manual_entrega_recepcion.md

<!-- Path: finanzas_y_contabilidad/24_manual_radian_titulo_valor.md -->

# 📜 Manual Operativo 24: RADIAN - Factura Electrónica de Venta como Título Valor - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Finanzas y Contabilidad / Cartera  
**Rol Requerido:** Área Contable, Analista de Cartera, Desarrollador de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual cuando una factura electrónica de venta deba quedar registrada como título valor en el RADIAN: para conocer los requisitos que valida la DIAN, el contenido obligatorio de cada evento (acuse de recibo, recibo del bien o servicio, aceptación expresa, aceptación tácita y reclamo), los plazos, la matriz de eventos excluyentes y las reglas lógicas que rechazan eventos y notas.

---

# MANUAL DE RADIAN: FACTURA ELECTRÓNICA DE VENTA COMO TÍTULO VALOR

**ID del Manual:** DIAN-UBL2.1-M24  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Eventos `ApplicationResponse` registrados ante la DIAN  
**Propósito:** Recoger lo que el Anexo Técnico 1.9 establece para el registro de la factura electrónica de venta como título valor en el RADIAN: requisitos (num. 5 y 6.5.1), garantía de registro (num. 6.5.2), relacionamiento de eventos (num. 6.5.3, Tabla 8) y reglas lógicas (num. 8.6). La estructura XML de cada evento se detalla en el Manual 12.

---

## 1. PROCEDIMIENTO GENERAL

De conformidad con los artículos 772, 773 y 774 del Código de Comercio y el numeral 9 del artículo 2.2.2.53.2 del Decreto 1074 de 2015, modificado por el artículo 1 del Decreto 1154 de 2020, para el registro de las facturas electrónicas de venta como título valor en el RADIAN la DIAN valida (Anexo 1.9, num. 5):
1. Fecha de vencimiento de la factura electrónica de venta.
2. Acuse de recibo de la factura electrónica de venta.
3. Recibo del bien o prestación del servicio.
4. Aceptación expresa, aceptación tácita o reclamo de la factura electrónica de venta.

La generación, transmisión, validación, entrega y recibo de estos requisitos debe cumplir el artículo 34 de la Resolución 000085 de 2022 y el anexo técnico (Anexo 1.9, num. 5). Cada evento se registra con un documento electrónico `ApplicationResponse`, que por contener la firma digital de quien lo origina es también un instrumento electrónico que deja constancia verificable de lo que declara (Anexo 1.9, num. 6.5).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Factura a Crédito:** La generación de los eventos aplica para facturas electrónicas cuyo medio de pago es crédito y que requieran disponerse como título valor (Anexo 1.9, num. 6.5).
2. **Fecha de Emisión igual a la de Generación:** Se requiere que la fecha de emisión de la factura electrónica corresponda a la fecha de su generación (Anexo 1.9, num. 6.5).
3. **Factura Validada:** Cada evento solamente puede ser registrado en un documento para el cual existe el evento "Documento Validado por la DIAN" (Anexo 1.9, num. 6.5.5).
4. **Fecha de Vencimiento:** Se debe indicar la fecha (día, mes y año) de vencimiento para el pago de la factura (numeral 1 del artículo 774 del Código de Comercio). En ausencia de mención expresa, se entiende que debe ser pagada dentro de los treinta (30) días siguientes a la emisión (Anexo 1.9, num. 6.5.1).
5. **Firmas Digitales:** Cada evento lleva la firma digital de quien lo genera (Anexo 1.9, num. 6.5 y 6.5.1).

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Notas 1 a 1:** Las notas crédito y notas débito asociadas a facturas que se quieran disponer como título valor se deben realizar con referencias 1 a 1: los tipos de operación de las notas crédito y débito deben ser `20` y `30` (Anexo 1.9, num. 6.5).
- **Regla de Aceptación:** Una vez generado un evento de aceptación (expresa o tácita) NO se pueden generar notas crédito o notas débito a la factura (Anexo 1.9, num. 6.5; reglas LGC15 y LGC16).
- **Regla de Garantía de Registro:** Algunos eventos necesitan que quien los genera tenga absoluta seguridad del contenido del documento al que se refieren y de que existe en la base de datos de la DIAN; por eso requieren informar en el cuerpo del evento las claves principales del documento al que se aplica (Anexo 1.9, num. 6.5.2).
- **Regla de Conflicto:** Cuando exista conflicto entre declaraciones y no haya manera automática de decidir cuál prevalece, será necesaria la intervención de la DIAN para resolverlo, probablemente por medio de contacto con uno o ambos contribuyentes (Anexo 1.9, num. 6.5.3).

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (REQUISITOS POR EVENTO)

### Paso 4.1: Acuse de Recibo de la Factura (`030`)
(Anexo 1.9, num. 6.5.1.1)
1. Número del evento.
2. CUDE del evento.
3. Fecha y hora de recibo de la factura electrónica de venta (artículo 774 del Código de Comercio y numeral 11 del artículo 2.2.2.53.2 del Decreto 1074 de 2015, modificado por el artículo 1 del Decreto 1154 de 2020).
4. CUFE de la factura que se acusa.
5. Apellidos y nombres o razón social de quien sea el encargado de recibir la factura (artículo 774 del Código de Comercio).
6. Tipo y número de identificación o NIT de quien sea el encargado de recibir la factura (artículo 774 del Código de Comercio).
7. Firma digital del encargado de recibo de la factura (artículo 774 del Código de Comercio). Si esta firma no coincide con la del adquirente/deudor/aceptante, se podrá optar por cualquiera de las dos.
8. Apellidos y nombres o razón social del adquirente/deudor/aceptante.
9. Tipo y número de identificación o NIT del adquirente/deudor/aceptante.
10. Firma digital del adquirente/deudor/aceptante.
11. Contenedor electrónico.

### Paso 4.2: Recibo del Bien o Prestación del Servicio (`032`)
(Anexo 1.9, num. 6.5.1.2)
1. Número del evento.
2. CUDE del evento.
3. Fecha y hora de recibo del bien o servicio (artículo 773 del Código de Comercio).
4. CUFE de la factura que se asocia al bien o servicio.
5. Apellidos y nombre del funcionario autorizado para recibir el bien o servicio (artículo 773 del Código de Comercio).
6. Tipo y número de identificación del funcionario autorizado para recibir el bien o servicio (artículo 773 del Código de Comercio).
7. Apellidos y nombres o razón social del adquirente/deudor/aceptante.
8. Tipo y número de identificación o NIT del adquirente/deudor/aceptante.
9. Observaciones.
10. Firma digital del funcionario autorizado para recibir el bien o servicio (artículo 773 del Código de Comercio). Si no coincide con la del adquirente/deudor/aceptante, se podrá optar por cualquiera de las dos.
11. Firma digital del adquirente/deudor/aceptante.
12. Contenedor electrónico.

### Paso 4.3: Aceptación Expresa (`033`)
(Anexo 1.9, num. 6.5.1.3)
1. Número del evento.
2. CUDE del evento.
3. CUFE de la factura que se acepta expresamente.
4. Indicación expresa de la aceptación de la factura (artículo 773 del Código de Comercio y numeral 1 del artículo 2.2.2.53.4 del Decreto 1074 de 2015, modificado por el artículo 1 del Decreto 1154 de 2020).
5. Fecha y hora de generación de la aceptación expresa.
6. Funcionalidad para el cómputo de tres (3) días hábiles siguientes al recibo de la mercancía o del servicio (numeral 1 del artículo 2.2.2.53.4 del Decreto 1074 de 2015).
7. Apellidos y nombre o razón social del adquirente/deudor/aceptante que emite la aceptación expresa (artículo 773 del Código de Comercio).
8. Firma digital del adquirente/deudor/aceptante que emite la aceptación expresa.
9. Contenedor electrónico.

### Paso 4.4: Aceptación Tácita (`034`)
(Anexo 1.9, num. 6.5.1.4)
1. Número del evento.
2. CUDE del evento.
3. CUFE de la factura que se acepta tácitamente.
4. Fecha y hora de generación de la aceptación tácita.
5. Apellidos y nombre o razón social del emisor o facturador electrónico que expide la aceptación tácita.
6. Tipo y número de documento o NIT del emisor o facturador electrónico que expide la aceptación tácita.
7. Indicación expresa de la factura electrónica de venta (artículo 773 del Código de Comercio y numeral 1 del artículo 2.2.2.53.4 del Decreto 1074 de 2015).
8. Constancia electrónica del emisor o facturador de los hechos que dan lugar a la aceptación tácita en el RADIAN, que se entiende hecha bajo la gravedad de juramento (numeral 2 y parágrafo 2 del artículo 2.2.2.53.4 del Decreto 1074 de 2015).
9. Apellidos y nombre o razón social del adquirente/deudor/aceptante que no emite la aceptación expresa (artículo 773 del Código de Comercio).
10. Firma digital del emisor o facturador electrónico que emite la aceptación tácita.
11. Contenedor electrónico.

### Paso 4.5: Reclamo de la Factura (`031`)
(Anexo 1.9, num. 6.5.1.5)
1. Número del evento.
2. CUDE del evento.
3. CUFE de la factura contra la que se reclama.
4. Fecha y hora de generación del reclamo.
5. Concepto de reclamo contra la factura (numeral 2 del artículo 2.2.2.53.4 del Decreto 1074 de 2015). En el XML se informa en `cbc:ResponseCode/@listID` según la tabla de la Caja de Herramientas (Anexo 1.9, regla AAH10).
6. Cómputo de tres días hábiles para remitir el reclamo al emisor o facturador electrónico (artículo 773 del Código de Comercio y numeral 2 del artículo 2.2.2.53.4 del Decreto 1074 de 2015).
7. Apellidos y nombre o razón social del adquirente/deudor/aceptante que genera el reclamo.
8. Tipo y número de documento o NIT del adquirente/deudor/aceptante que genera el reclamo.
9. Firma digital del adquirente/deudor/aceptante que realiza el reclamo.
10. Contenedor electrónico.

### Paso 4.6: Aplicar la Matriz de Eventos Excluyentes
(Anexo 1.9, num. 6.5.3, Tabla 8)

Si ocurre el evento de la fila, no puede ocurrir el evento marcado con X en la columna para el mismo documento.

| Evento registrado | 02 | 04 | 030 | 031 | 032 | 033 | 034 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **02** Documento validado por la DIAN | | X | | | | | |
| **04** Documento rechazado por la DIAN | X | | X | X | X | X | X |
| **030** Acuse de recibo | | | | | | | |
| **031** Reclamo | | | | | | X | X |
| **032** Recibo del bien o prestación del servicio | | | | | | | |
| **033** Aceptación expresa | | | | X | | | |
| **034** Aceptación tácita | | | | X | | | |

### Paso 4.7: Aplicar las Reglas Lógicas de Secuencia y Plazo
(Anexo 1.9, num. 8.6; todas con efecto Rechazo)

| Regla | Descripción |
| :--- | :--- |
| LGC01 / LGC20 | Solo se permite el registro de un único evento cuando este aplique (mensaje: "Evento registrado previamente"). |
| LGC02 | No se puede reclamar un documento que ha sido aceptado (expresa o tácitamente) previamente. |
| LGC03 | No se puede recibir un reclamo si previamente no se han recibido los eventos de acuse de recibo y de recibo del bien y prestación del servicio. |
| LGC04 | No se puede aceptar (expresa o tácitamente) un documento que ha sido rechazado previamente. |
| LGC05 | No se puede generar una aceptación tácita sobre un documento aceptado expresamente previamente. |
| LGC06 | No se puede generar una aceptación expresa pasados los 3 días hábiles siguientes a la generación del evento de recibo del bien o prestación del servicio. |
| LGC07 | No se puede generar una aceptación expresa sobre un documento aceptado tácitamente previamente. |
| LGC09 | No se puede generar un recibo del bien y prestación de servicios sobre un documento que no cuente con un evento de acuse de recibo. |
| LGC10 | Evento mandato: no se cuenta con un mandato vigente a la fecha entre el mandante y el mandatario. |
| LGC11 | Evento mandato: el mandatario no puede enviar este evento al documento o evento referenciado porque no cuenta con un mandato vigente para este. |
| LGC12 | Solo se puede transmitir el evento `033` después de haber transmitido el evento `032`. |
| LGC13 | Solo se puede transmitir el evento `033` después de haber transmitido el evento `030`. |
| LGC14 | Solo se puede transmitir el evento `034` pasados 3 días hábiles después de la transmisión del evento `032`. |
| LGC15 | No es posible la transmisión de una nota crédito, por cuanto la factura ya se encuentra aceptada. |
| LGC16 | No es posible la transmisión de una nota débito, por cuanto la factura ya se encuentra aceptada. |

**Secuencia que resulta de las reglas:** `030` (acuse) → `032` (recibo del bien o servicio) → dentro de los 3 días hábiles siguientes, `033` (aceptación expresa) o `031` (reclamo); pasados los 3 días hábiles sin aceptación expresa, el emisor puede generar `034` (aceptación tácita).

### Paso 4.8: Verificar las Reglas de Quién Genera y Recibe Cada Evento
(Anexo 1.9, num. 8.6.1)
- `AAF01a` a `AAF01e` (Rechazo): el nombre o razón social de quien genera el evento debe estar autorizado para generar ese evento (acuse, reclamo, recibo, aceptación expresa, aceptación tácita).
- `AAG01a` a `AAG01d` (Rechazo): el adquiriente debe estar autorizado para recibir el evento; `AAG01e` (Rechazo): en la aceptación tácita deben informarse los datos de la DIAN como receptor.
- `AAD11a` (Rechazo): en la aceptación tácita es obligatoria la nota; `AAD11b` (Rechazo): si el evento lo genera un mandatario, es obligatoria la nota de mandato.
- `AAH11` a `AAH16` (Rechazo) en el acuse de recibo y en el recibo del bien o servicio: es obligatorio informar quién recibió (`IssuerParty/Person` con identificación, nombres y apellidos); `AAH17` y `AAH18` (Notificación): cargo y área de quien recibió.

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Requisitos Completos:** La factura tiene fecha de vencimiento (o se entienden 30 días), acuse de recibo (`030`), recibo del bien o servicio (`032`) y aceptación expresa (`033`), aceptación tácita (`034`) o reclamo (`031`).
2. **Bloqueo de Notas:** Con aceptación expresa o tácita registrada, la factura no admite notas crédito ni débito.
3. **Consulta:** Los eventos y las notas crédito asociados a la factura como título valor se consultan con `GetStatusEvent` y `GetReferenceNotes` (ver Manual 22).

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre `LGC03` o `LGC09`, la solución es transmitir primero los eventos previos que faltan (`030` y/o `032`).
- Si ocurre `LGC06`, ya pasaron los 3 días hábiles siguientes al evento `032`: la aceptación expresa ya no procede (Anexo 1.9, num. 8.6).
- Si ocurre `LGC14`, todavía no han pasado 3 días hábiles desde el evento `032`: la aceptación tácita aún no procede (Anexo 1.9, num. 8.6).
- Si ocurre `LGC02`, `LGC05` o `LGC07`, la factura ya tiene una aceptación registrada que excluye el evento enviado (Anexo 1.9, num. 6.5.3 y 8.6).
- Si ocurre `LGC15` o `LGC16`, la factura ya fue aceptada: no se pueden emitir notas crédito ni débito; para la nota crédito existe la nota de ajuste para factura aceptada, tipo de operación `24` (Anexo 1.9, num. 14.3; ver Manual 09).
- Si ocurre `AAD11a`, falta la nota obligatoria del evento de aceptación tácita (ver Manual 12).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Eventos del Título Valor**
| Código | Evento | Quién lo genera | Requisitos (num.) |
| :--- | :--- | :--- | :--- |
| 030 | Acuse de recibo de la factura | Adquirente/deudor/aceptante (encargado de recibir) | 6.5.1.1 |
| 032 | Recibo del bien o prestación del servicio | Adquirente/deudor/aceptante (funcionario autorizado) | 6.5.1.2 |
| 033 | Aceptación expresa | Adquirente/deudor/aceptante | 6.5.1.3 |
| 034 | Aceptación tácita | Emisor o facturador electrónico | 6.5.1.4 |
| 031 | Reclamo | Adquirente/deudor/aceptante | 6.5.1.5 |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- RADIAN y factura electrónica de venta como título valor: requisitos de cada evento (030, 031, 032, 033, 034), fecha de vencimiento, matriz de eventos excluyentes (Tabla 8), reglas lógicas LGC, plazos de 3 días hábiles y bloqueo de notas tras la aceptación: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/24_manual_radian_titulo_valor.md

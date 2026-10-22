<!-- Path: finanzas_y_contabilidad/04_agente_ia_causacion.md -->

# Agente de IA de Causación de Facturas en Odoo

**Módulo Odoo:** Contabilidad / Proveedores[cite: 28]
**Rol Requerido:** Área Contable
**Cuándo usar este documento:** Usa este documento cuando necesites comprender cómo el Agente de IA procesa, propone y clasifica automáticamente las facturas de proveedores (cuentas, impuestos y centros de costos) y qué acciones debe tomar el equipo de contabilidad[cite: 26].

## 1. Prerrequisitos y Condiciones
* La factura del proveedor debe ingresar previamente mediante PDF en OneDrive y registro de la fila correspondiente en el Excel de recepción (según el Manual 1)[cite: 27, 28].
* El agente de IA actúa como un auxiliar contable que analiza el plan de cuentas, impuestos y centros de costos existentes en Odoo[cite: 26].
* El agente nunca contabiliza o confirma facturas de manera autónoma; la decisión final y contabilización definitiva recae siempre en el Área Contable[cite: 26, 28].
* Si el nivel de confianza del agente es del 85% o superior, la propuesta pasa directamente al borrador en Odoo[cite: 27]. Si es menor al 85% o existen dudas, el agente envía una solicitud de revisión por Telegram[cite: 27].

## 2. Pasos de Ejecución y Recorrido de la Factura
1. **La factura entra:** Se almacena el PDF en OneDrive y se registra la fila en el Excel[cite: 27, 28].
2. **Lee la factura:** El sistema extrae el proveedor, conceptos, cantidades, valores e impuestos[cite: 27].
3. **Consulta Odoo:** Revisa qué cuentas, impuestos y centros de costos existen actualmente en Odoo[cite: 27].
4. **Propone:** Define la cuenta, impuestos y analítica, indicando su nivel de confianza y la razón[cite: 27].
5. **Valida:** Confirma que todos los elementos propuestos existen en Odoo y cuadran con los valores de la factura[cite: 27].
6. **Usted revisa y confirma:** Contabilidad revisa el borrador en Odoo y confirma el documento[cite: 27, 28].

### Pasos Operativos para Contabilidad
1. **Cargue la factura como siempre:** Guarde el PDF en OneDrive y registre la fila en el Excel de recepción[cite: 28]. Procure que el PDF sea legible, ya que documentos escaneados torcidos o borrosos provocan que el agente solicite revisión[cite: 28].
2. **Atienda las solicitudes en Telegram:** Cuando el agente dude, enviará una propuesta al grupo de Telegram[cite: 28]. Revise el concepto y la razón[cite: 28]. Si es correcta, confírmela; si algo es incorrecto, corríjalo indicando el valor exacto para alimentar las reglas del sistema[cite: 28].
3. **Revise el borrador en Odoo:** 
   * Vaya a **Contabilidad** > **Proveedores** > **Facturas** y filtre por el estado **Borrador**[cite: 28].
   * Abra la factura y revise las líneas (concepto, valor, cuenta, impuestos y analítica)[cite: 28].
   * Compare el total del borrador con el total del PDF adjunto[cite: 28].
   * Verifique las retenciones aplicables según el criterio contable[cite: 28].
4. **Confirme la factura:** Si todo es correcto, pulse **Confirmar** para que Odoo genere el asiento contable[cite: 28].

## 3. Reglas de Negocio Estrictas
* **Restricción de Creación (Fuente de Verdad):** El agente de IA **nunca** crea cuentas contables nuevas, inventa posiciones fiscales, cambia la configuración contable, crea impuestos nuevos, asigna centros de costos inexistentes ni confirma facturas sin revisión[cite: 31].
* **Condición de Gasto Nuevo:** Si llega un tipo de gasto nuevo que no cuenta con su respectiva cuenta, impuesto o centro de costos en Odoo, el agente lo enviará automáticamente a revisión[cite: 31]. La solución operativa consiste en que el contador cree dicho valor previamente en Odoo para que el agente pueda utilizarlo en el futuro[cite: 31].
* **Control de Duplicados:** Si se detecta una factura duplicada con el mismo CUFE, el sistema la detiene automáticamente para evitar un doble registro[cite: 31].

## 4. Troubleshooting y Solución de Revisiones
Cuando el agente detecta incidencias o baja confianza, solicita revisión mediante las siguientes casuísticas[cite: 31]:

| Situación / Alerta | Causa probable | Qué hacer |
| :--- | :--- | :--- |
| **Confianza baja**[cite: 31] | El concepto de la factura es ambiguo o existen varias cuentas posibles[cite: 31]. | Confirme o corrija directamente en Telegram utilizando el valor exacto[cite: 31]. |
| **Valor no existe en Odoo**[cite: 31] | El gasto requiere una cuenta, impuesto o centro de costos que no se encuentra configurado[cite: 31]. | El contador crea el valor faltante en Odoo y posteriormente se corrige la propuesta[cite: 31]. |
| **Proveedor no identificado**[cite: 31] | El NIT de la factura no coincide con ningún proveedor registrado en Odoo[cite: 31]. | Revise el NIT en el archivo Excel (Manual 1) o proceda a crear el proveedor[cite: 31]. |
| **Información incompleta o ilegible**[cite: 31] | El documento PDF no se pudo leer correctamente[cite: 31]. | Reemplace el archivo PDF por una versión legible o complete el borrador de forma manual[cite: 31]. |
| **Inconsistencia fiscal o contable**[cite: 31] | Los impuestos reflejados en la factura no cuadran con los configurados en el sistema[cite: 31]. | Revise la factura y, si el proveedor facturó de forma incorrecta, gestione la corrección directamente con él[cite: 31]. |
| **Factura duplicada**[cite: 31] | Ya se encuentra registrada una factura previa con el mismo código CUFE[cite: 31]. | No realice ninguna acción; el sistema detiene el proceso de forma automática para prevenir duplicados[cite: 31]. |
| **Error técnico persistente**[cite: 31] | Falla temporal de comunicación o conexión entre Make y Odoo[cite: 31]. | Si el error persiste, notifique al equipo de soporte técnico[cite: 31]. |

## 5. Tablas de Referencia y Anexos Técnicos

### Estados de una Factura en el Ciclo del Agente[cite: 30]
| Estado | Qué significa | ¿Debe hacer algo el usuario? |
| :--- | :--- | :--- |
| **RECIBIDO / VALIDANDO**[cite: 30] | El sistema se encuentra leyendo, clasificando y verificando la factura[cite: 30]. | No, debe esperar[cite: 30]. |
| **APROBADO**[cite: 30] | La propuesta generada es válida y se enruta hacia Odoo[cite: 30]. | No[cite: 30]. |
| **PROCESADO**[cite: 30] | La propuesta ya se encuentra registrada en el borrador de Odoo[cite: 30]. | Sí: revisar y confirmar los pasos de validación contable[cite: 30]. |
| **REVISIÓN**[cite: 30] | El agente experimenta dudas o carece de información; se envía notificación por Telegram[cite: 30]. | Sí: confirmar o corregir la propuesta[cite: 30]. |
| **ERROR**[cite: 30] | Falla técnica relacionada con el archivo o la conexión con Odoo; el sistema reintenta automáticamente[cite: 30]. | Solo si el error persiste, debe avisar a soporte[cite: 30]. |

### Componentes de una Propuesta del Agente[cite: 26, 29]
* **Proveedor:** Nombre o entidad emisora[cite: 26, 29].
* **Producto/Servicio:** Descripción del bien adquirido[cite: 26, 29].
* **Clasificación:** Categoría operativa de la compra[cite: 26, 29].
* **Cuenta Contable:** Cuenta de gasto o costo asignada del plan de cuentas de Odoo[cite: 26, 29].
* **Impuesto:** Tributos vinculados según la configuración de Odoo[cite: 26, 29].
* **Analítica:** Centro de costos o proyecto con su porcentaje de distribución[cite: 26, 29].
* **Confianza:** Nivel de seguridad del agente expresado en porcentaje (0% a 100%)[cite: 26, 29].
* **Razón:** Argumento textual que justifica la clasificación seleccionada[cite: 26, 29].

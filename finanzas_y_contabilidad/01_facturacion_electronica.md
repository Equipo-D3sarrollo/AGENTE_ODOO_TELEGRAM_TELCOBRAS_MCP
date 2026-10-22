<!-- Path: finanzas_y_contabilidad/01_facturacion_electronica.md -->

# Facturación Electrónica con la DIAN en Odoo (Recepción y Emisión)

**Módulo Odoo:** Contabilidad / Compras / Ventas[cite: 85, 90, 94]  
**Rol Requerido:** Área Contable[cite: 85]  
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 85]  
**Elaborado por:** Joshua Lores Florez[cite: 85]  
**Cuándo usar este documento:** Utilice este manual para conocer el funcionamiento de la recepción automática de facturas de proveedores (Parte A) y la emisión de facturas de venta electrónicas (FE) validadas directamente ante la DIAN (Parte B)[cite: 85].

---

## PARTE A: Recepción Automática de Facturas de Proveedores

### 1. ¿Qué hace esta herramienta?
La herramienta automatiza el registro manual de facturas de proveedores en Odoo[cite: 86]. Valida que el documento no esté repetido por medio del CUFE, verifica que el proveedor exista mediante el NIT, desbloquea archivos PDF protegidos con contraseña, crea el borrador en Odoo con el PDF adjunto y envía notificaciones automáticas por Telegram[cite: 86, 87].

| Tarea | Antes (a mano) | Ahora (automático) |
| :--- | :--- | :--- |
| Buscar el proveedor por NIT | 1 a 2 minutos[cite: 86] | Menos de 1 segundo[cite: 86] |
| Crear la factura en Odoo | 3 a 5 minutos[cite: 86] | Unos segundos[cite: 86] |
| Revisar si la factura está repetida | A ojo, con riesgo de error[cite: 86] | Automático, por el CUFE[cite: 86] |
| Errores de digitación (fechas, NIT) | Frecuentes[cite: 86] | Se eliminan[cite: 86] |

### 2. Paso a Paso para Contabilidad
1. **Guardar el PDF en OneDrive:** Suba el archivo PDF en la carpeta de contabilidad del año (`OneDrive > Documents > Respaldo > AREACONTABLE > 07. 2026 > 02. Telcobras > 01. Contabilidad 2026`)[cite: 88]. El nombre del archivo debe iniciar con el prefijo y número de la factura (ej. `OB-1427 NOMBRE PROVEEDOR.pdf`)[cite: 88].
2. **Anotar la factura en el Excel de recepción:** Abra el archivo `RECEPCION DE FACTURAS TB <MES>2026.xlsx` y agregue una fila con los datos correspondientes: Prefijo, Folio, CUFE (de 96 caracteres, copiado y nunca digitado), Fecha de emisión (`AAAA-MM-DD`), NIT del proveedor (solo números, sin puntos ni dígito de verificación), Razón social, IVA, INC, Total, Centro de costos y Tipo de operación[cite: 88, 89].
3. **Esperar el aviso en Telegram:** El sistema enviará una notificación al grupo indicando si la factura fue aceptada o si requiere revisión[cite: 89].

### 3. Estados en el Excel y Manejo de Errores
* **`VALIDADO_EN_ODOO`:** Factura aceptada y en proceso de registro[cite: 91].
* **`PROCESADO_EN_ODOO`:** Borrador creado en Odoo con su PDF adjunto[cite: 91].
* **`REQUIERE_REVISION`:** La factura no se procesó; revise el motivo en Telegram[cite: 91].

| Lo que ve en Telegram | Causa más probable | Qué hacer |
| :--- | :--- | :--- |
| **"Factura duplicada"**[cite: 91] | Mismo CUFE o mismo proveedor y número ya registrados[cite: 91]. | Busque en Odoo la factura existente. Si es distinta, verifique el CUFE y número[cite: 91]. |
| **"Proveedor no encontrado"**[cite: 91] | NIT con puntos, guiones, dígito de verificación o error de digitación[cite: 91]. | Corrija el NIT en el Excel (solo números) o cree el proveedor en Odoo[cite: 91]. |
| **"Datos incompletos" o CUFE inválido**[cite: 91] | CUFE incompleto, con espacios o digitado manualmente[cite: 91]. | Copie de nuevo el CUFE completo de 96 caracteres desde el PDF[cite: 91]. |
| **El borrador existe sin PDF**[cite: 91] | El archivo no estaba en la carpeta o su nombre no sigue el formato establecido[cite: 91]. | Verifique el nombre del archivo en la carpeta de OneDrive o adjúntelo a mano[cite: 91]. |

---

## PARTE B: Emisión de Facturas de Venta Electrónicas (FE)

### 7. Facturación Electrónica de Venta Ante la DIAN
Telcobras se conecta directamente con la DIAN mediante el servicio gratuito de **Software Propio** (sin intermediarios tecnológicos como Carvajal)[cite: 93, 94, 96]. 

* **Diario:** Ventas, prefijo `FE`, facturación electrónica UBL 2.1 (Colombia) activa[cite: 94, 97].
* **Resolución DIAN:** Número `18764112172814`, vigente del 6 de julio de 2026 al 6 de julio de 2028[cite: 94, 97].
* **Rango autorizado:** Del `FE711` al `FE10000`[cite: 94, 97].

### 8. Paso a Paso para Emitir una Factura de Venta
1. **Verificar el cliente:** Confirme que el contacto tenga tipo y número de identificación (con dígito de verificación si es NIT), razón social, dirección, ciudad, país, correo electrónico de facturación y responsabilidades fiscales[cite: 94].
2. **Crear la factura en Odoo:** Vaya a **Contabilidad > Clientes > Facturas > Nuevo**, verifique que el diario sea **Ventas (prefijo FE)**, seleccione el cliente, la fecha, el plazo de pago y agregue las líneas de productos o servicios con sus respectivos impuestos[cite: 94]. Odoo asigna automáticamente el siguiente consecutivo[cite: 94].
3. **Confirmar y enviar a la DIAN:** Pulse **Confirmar** y utilice el botón de envío con la opción DIAN activa[cite: 95]. Odoo firmará la factura digitalmente y la transmitirá a la DIAN[cite: 93, 95]. Verifique que el estado DIAN sea **Aceptado** y que el PDF muestre el CUFE y el código QR[cite: 95].

### 9. Manejo de Rechazos y Resolución
* **Rechazo por datos del cliente:** Complete la información faltante en el contacto y vuelva a enviar la misma factura[cite: 96].
* **Secuencia desconfigurada:** Continúe en el número más alto existente + 1 (ej. `FE712`) dentro del rango autorizado[cite: 96].
* **Errores en factura aceptada:** No se borra ni edita; emita una **nota crédito** desde la misma factura y una nueva factura correcta[cite: 96].
* **Vencimiento de resolución:** Avise a soporte al llegar al 10% restante (al llegar a `FE9100`) o con dos meses de anticipación a la fecha de finalización[cite: 96].

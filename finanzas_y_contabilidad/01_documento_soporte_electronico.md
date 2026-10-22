<!-- Path: finanzas_y_contabilidad/01_documento_soporte_electronico.md -->

# Emisión de Documento Soporte Electrónico (DIAN)

**Módulo Odoo:** Contabilidad
**Rol Requerido:** Área Contable
**Cuándo usar este documento:** Usa este documento cuando el usuario necesite registrar compras a proveedores no obligados a facturar (personas naturales, técnicos, independientes sin IVA) y deba emitir el Documento Soporte Electrónico ante la DIAN para legalizar el gasto.

## 1. Prerrequisitos y Condiciones
*   El proveedor NO debe estar obligado a facturar (verificar RUT).
*   El contacto del proveedor en Odoo debe tener completos y exactos los siguientes campos: Tipo de identificación (sin puntos ni guiones, con dígito de verificación si es NIT), Nombre completo (como en el RUT), Dirección, Ciudad (elegida de la lista, no texto libre), País y Correo electrónico.
*   Debe existir resolución vigente en la DIAN.

## 2. Pasos de Ejecución (Ruta UI)
1.  Navega al módulo **Contabilidad** > **Proveedores** > **Facturas**.
2.  Haz clic en **Nuevo**.
3.  En el campo `Diario`, selecciona obligatoriamente **Documento soporte**.
4.  Selecciona el proveedor en el campo `Proveedor` y la fecha de compra en `Fecha`.
5.  En la pestaña `Líneas de factura`, agrega el concepto, cantidad, valor, impuestos/retenciones aplicables y cuenta contable.
6.  Revisa los valores y haz clic en **Confirmar**.
7.  Haz clic en **Enviar / Enviar e imprimir** asegurando que la opción DIAN esté activa.
8.  Verifica que el estado DIAN cambie a **Aceptado** y que el PDF generado incluya el código CUDS y el código QR.

## 3. Reglas de Negocio Estrictas
*   **Condición:** Si el proveedor entrega factura electrónica, se registra como factura normal en el diario "Compras". Si el proveedor NO está obligado a facturar, se utiliza obligatoriamente el diario "Documento soporte".
*   **Prohibición Expresa:** NUNCA modifiques manualmente el número de secuencia (consecutivo DSO...) a menos que la secuencia se desconfigure. Odoo lo asigna automáticamente.
*   **Prohibición Expresa:** NUNCA repitas un número de documento. Si hay desconfiguración, continúa desde el número más alto existente + 1.
*   **Regla de Rango:** Se debe notificar a soporte técnico si queda menos del 10% del rango de numeración o faltan menos de 2 meses para el vencimiento de la resolución.

## 4. Troubleshooting (Solución de Errores)
*   Si ocurre el error `Rechazo por datos del proveedor`, la solución es completar el contacto del proveedor (tipo/número de identificación, dirección, ciudad, correo) y volver a enviar el mismo documento.
*   Si ocurre el error `Salió con prefijo distinto a DSO`, la solución es pasarlo a borrador, cambiar el diario a "Documento soporte" y confirmar de nuevo (solo si no se ha enviado a la DIAN). Si ya se envió, avisar a soporte.
*   Si ocurre el error `Número repetido`, la solución es asignar manualmente el número más alto existente + 1 y reintentar.
*   Si ocurre el error `Número fuera del rango autorizado` o `Resolución vencida`, la solución es no emitir más documentos y avisar a soporte técnico para tramitar nueva numeración.
*   Si ocurre el error `No hay respuesta de la DIAN`, la solución es esperar unos minutos y reintentar el envío por falla temporal de conexión.

## 5. Tablas de Referencia y Anexos Técnicos
**Resoluciones DIAN Activas (Configuración de Diarios)**
| Diario | Para qué | Prefijo | Rango | Vigencia |
| :--- | :--- | :--- | :--- | :--- |
| Documento soporte | Compras a no obligados | DSO | 1 a 5.000 | 28 ago 2028 |
| Ventas | Facturas de venta Telcobras | FE | 711 a 10.000 | 6 jul 2028 |

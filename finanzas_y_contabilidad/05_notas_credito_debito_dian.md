<!-- Path: finanzas_y_contabilidad/05_notas_credito_debito_dian.md -->

# Notas crédito y débito DIAN en Odoo[cite: 87]
**Guía rápida para Contabilidad. Odoo 19**[cite: 87]
**Empresa:** Telcobras S.A.S.[cite: 87]

Cómo usar esta guía: haga los pasos de esta página[cite: 87]. Cuando un paso diga (Ref. A), (Ref. B), etc., la explicación está en las referencias[cite: 87, 88].

> **Regla de oro:** El diario de la nota siempre es Nota Credito o Nota Debito[cite: 87]. Si queda en Ventas, la DIAN la rechaza[cite: 87].

## NOTA CRÉDITO
**Para anular una factura, registrar una devolución o dar un descuento**[cite: 87]

1. Contabilidad → Clientes → Facturas abra la factura[cite: 87]. En su pestaña DIAN debe decir Aceptado[cite: 87].
2. Pulse el botón Nota de crédito[cite: 87].
3. Escriba el Motivo y en Usar diario específico elija Nota Credito[cite: 87].
4. Pulse Revertir[cite: 87]. Se crea la nota en borrador[cite: 87].
5. Elija el Concepto Nota de Crédito (Ref. A)[cite: 87, 88]. Si es parcial, deje solo lo que se devuelve o descuenta[cite: 87].
6. Solo la primera nota de todas: cambie el número a NC1[cite: 87]. Las demás salen solas[cite: 87].
7. Pulse Confirmar y luego Enviar con las casillas DIAN y Correo marcadas[cite: 87].
8. Revise la pestaña DIAN de la nota: debe decir Aceptado[cite: 87]. Si sale un error, vea Ref. C[cite: 87, 88].

*Si la nota queda confirmada sola:* pulse Restablecer a borrador, haga los pasos 5 y 6 y siga[cite: 87].

## NOTA DÉBITO
**Para cobrarle más al cliente: intereses, gastos o un mayor valor**[cite: 87]

1. Abra la factura[cite: 87]. En su pestaña DIAN debe decir Aceptado[cite: 87].
2. Pulse el botón Nota de débito[cite: 87].
3. Escriba el Motivo y en Usar diario específico elija Nota Debito[cite: 87]. Marque Copiar líneas solo si se repiten las de la factura[cite: 87].
4. Pulse Crear nota de débito[cite: 87].
5. Elija el concepto (Ref. B) y agregue la línea de lo que se cobra, con su valor e IVA[cite: 87, 88].
6. Solo la primera nota de todas: cambie el número a ND1[cite: 87].
7. Pulse Confirmar y luego Enviar con las casillas DIAN y Correo marcadas[cite: 87].
8. Revise la pestaña DIAN: debe decir Aceptado[cite: 87]. Si sale un error, vea Ref. C[cite: 87, 88].

*Antes de cualquier otra acción sobre una nota aceptada, lea la Ref. D.*[cite: 87, 88]

---

## REFERENCIAS

### Ref. A. Qué concepto elegir en la nota crédito[cite: 88]
| Concepto en Odoo | Úselo cuando |
| :--- | :--- |
| Anulación de factura electrónica[cite: 88] | La factura completa queda sin efecto[cite: 88]. Mismas líneas y mismo total que la factura[cite: 88]. Con este concepto la DIAN exige el tipo de operación `20` (Anexo 1.9, regla CAD02a). |
| Devolución parcial[cite: 88] | El cliente devuelve parte de lo facturado[cite: 88]. |
| Rebaja o descuento parcial o total[cite: 88] | Se baja el valor después de facturar[cite: 88]. |
| Ajuste de precio[cite: 88] | El precio facturado estaba mal[cite: 88]. |
| Descuento por pronto pago[cite: 88] | El cliente pagó antes y se le reconoce[cite: 88]. |
| Descuento por volumen[cite: 88] | Descuento por las cantidades compradas[cite: 88]. |

### Ref. B. Qué concepto elegir en la nota débito[cite: 88]
| Concepto en Odoo | Úselo cuando |
| :--- | :--- |
| Intereses[cite: 88] | Mora o financiación sobre la factura[cite: 88]. |
| Gastos por cobrar[cite: 88] | Fletes, envíos u otros gastos no facturados[cite: 88]. |
| Cambio del valor[cite: 88] | El valor facturado quedó por debajo del real[cite: 88]. |
| Otros[cite: 88] | Cualquier otro mayor valor; explíquelo en el Motivo[cite: 88]. |

### Ref. C. Si la DIAN rechaza la nota[cite: 88]
| Mensaje | Qué hacer |
| :--- | :--- |
| CAJ50: el prefijo informado no corresponde...[cite: 88] | La nota quedó en el diario Ventas[cite: 88]. Cancelar y Restablecer a borrador, crearla de nuevo desde la factura eligiendo el diario Nota Credito o Nota Debito[cite: 88]. |
| RUT01: la validación del estado del RUT...[cite: 88] | Es solo un aviso[cite: 88]. No hacer nada[cite: 88]. Esto aplica si la nota quedó Aceptado. Si la nota quedó rechazada, siga la fila "Cualquier otro código": en el anexo, RUT01 es regla de rechazo cuando el facturador electrónico y/o el proveedor tecnológico tiene el RUT en estado cancelado, suspendido o inactivo (Anexo 1.9, regla RUT01). |
| Cualquier otro código[cite: 88] | No reintente[cite: 88]. Copie el mensaje completo y envíelo al equipo de Desarrollo[cite: 88]. |

### Ref. D. Reglas que no se pueden romper[cite: 88]
1. Una nota Aceptada no se edita ni se borra[cite: 88]. Si quedó mal, se corrige con otra nota[cite: 88].
2. La factura original no se reenvía ni se modifica[cite: 88].
3. Las notas tienen su propia numeración (NC1, NC2... / ND1, ND2...)[cite: 88]. No usan el número de la factura[cite: 88].
4. El sello Revertido / Pagado en la factura no significa que la nota se envió[cite: 88]. Solo cuenta el estado Aceptado[cite: 88].
5. Si se anula una factura de una orden de venta, la orden vuelve a quedar por facturar[cite: 88]: cancélela o refacture desde ella[cite: 88].
6. Si hubo productos devueltos, la devolución física se registra aparte en Inventario[cite: 88].
7. Si el cliente ya aceptó la factura de manera expresa o tácita (eventos 033 o 034), la DIAN no permite transmitir nota crédito ni nota débito (Anexo 1.9, reglas LGC15 y LGC16). Para la nota crédito existe la nota de ajuste para factura aceptada, tipo de operación `24` (Anexo 1.9, num. 14.3). Si la DIAN rechaza la nota por LGC15 o LGC16, aplique la Ref. C. El estado Aceptado de la pestaña DIAN no es la aceptación del cliente.
8. La fecha de la nota debe ser igual a la fecha de su firma; si es diferente, la DIAN la rechaza (Anexo 1.9, reglas CAD09e y DAD09e).
9. Las notas crédito y débito no tienen esquema de contingencia: si los servicios de la DIAN no están disponibles, se generan, transmiten y expiden cuando el servicio funcione normalmente (Anexo 1.9, num. 12.1, 12.2 y 12.3.1).

### Ref. E. Configuración de diarios (solo el administrador la modifica)[cite: 88]
| Diario | Código | Resolución DIAN | Secuencias dedicadas | Casilla Nota de Débito |
| :--- | :--- | :--- | :--- | :--- |
| Ventas[cite: 88] | FE[cite: 88] | Sí[cite: 88] | Desmarcadas[cite: 88] | Desmarcada[cite: 88] |
| Nota Credito[cite: 88] | NC[cite: 88] | Vacía[cite: 88] | Desmarcadas[cite: 88] | Desmarcada[cite: 88] |
| Nota Debito[cite: 88] | ND[cite: 88] | Vacía[cite: 88] | Desmarcadas[cite: 88] | Marcada[cite: 88] |

Los tres son de tipo Ventas con UBL 2.1 marcado[cite: 88]. En Nota Credito y Nota Debito no se pulsa "Volver a cargar la configuración de la DIAN": las notas no tienen resolución y el aviso de rango no encontrado es normal[cite: 88].

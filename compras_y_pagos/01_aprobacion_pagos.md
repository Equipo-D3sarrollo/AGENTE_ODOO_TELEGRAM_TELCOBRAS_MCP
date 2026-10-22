<!-- Path: compras_y_pagos/01_aprobacion_pagos.md -->

# MANUAL DE USO COMPRAS, GERENCIA Y CONTABILIDAD
## Agente de aprobación de pagos por Telegram
Cómo pedir el estudio de pagos, cómo aprueba o rechaza el gerente desde el celular y dónde queda registrada cada decisión[cite: 22].

* **Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 22]
* **Dirigido a:** Ejecutiva de Compras, Gerencia Financiera y Área Contable[cite: 22]
* **Elaborado por:** Joshua Lores Florez[cite: 22]

---

## 1. ¿Qué hace este agente?
Antes, para aprobar pagos había que buscar a mano en Odoo las facturas pendientes de cada proveedor, armar la lista, pasársela al gerente y anotar lo que aprobaba[cite: 23]. Ahora ese proceso se hace desde Telegram en pocos pasos[cite: 23].

Piénselo como la carpeta de cuentas por pagar que se lleva a firma[cite: 23]. La ejecutiva de compras pide la carpeta de uno o varios proveedores[cite: 23]. El sistema la arma solo, con las facturas que de verdad están pendientes en Odoo, y se la entrega al gerente en un solo mensaje por proveedor[cite: 23]. El gerente "firma" o "devuelve" cada factura con un botón, y el sistema lo anota en el libro de control (Excel)[cite: 23]. Nada se pierde y nada se anota dos veces[cite: 23].

### Quién hace qué
* **Ejecutiva de Compras:** Pide el estudio de pagos en Telegram con los NIT de los proveedores[cite: 23].
* **Gerente Financiero:** Recibe el resumen por proveedor y aprueba o rechaza cada factura con un botón[cite: 23].
* **Contabilidad / Tesorería:** Consulta en el Excel qué quedó aprobado o rechazado y procede con el pago[cite: 23].

**Nota importante:** Aprobar no es pagar[cite: 23]. El botón "Aprobar" deja registrada la autorización del gerente[cite: 23]. El pago se realiza después, en el proceso normal de tesorería[cite: 23].

---

## 2. El recorrido de una solicitud
El proceso tiene dos momentos[cite: 24]. Entre uno y otro puede pasar un minuto o varios días: el sistema espera a que el gerente decida[cite: 24].

### Momento 1: La Solicitud
1. **Ejecutiva pide:** Envía `/estudiopagos` con los NIT[cite: 24].
2. **Busca en Odoo:** Proveedor por NIT y sus facturas con saldo pendiente[cite: 24].
3. **Arma el resumen:** Descarta las ya enviadas y agrupa por proveedor[cite: 24].
4. **El sistema espera:** El gerente decide cuando tenga tiempo[cite: 24].

### Momento 2: La Decisión
5. **Gerente decide:** Pulsa **Aprobar** o **X Rechazar** en cada factura[cite: 24].
6. **Se anota en Excel:** Hoja `APROBADOS` o `RECHAZADOS`, con fecha[cite: 24].
7. **Botón desaparece:** La factura evaluada sale del mensaje[cite: 24]. Queda cerrada[cite: 24].

### ¿Qué facturas aparecen en el estudio?
Solo las que cumplen las tres condiciones a la vez[cite: 24]:
1. **Factura de proveedor:** Es una factura de compra, no de venta[cite: 24].
2. **Publicada:** Está confirmada en Odoo, no en borrador[cite: 24].
3. **Con saldo pendiente:** No pagada o pagada parcialmente[cite: 24].

*Si una factura sigue en borrador, no aparecerá[cite: 24]. Primero debe confirmarse en Odoo (vea el Manual 4, paso 4)[cite: 24].*

---

## 3. Paso a paso Ejecutiva de Compras

1. **Abra el chat del bot corporativo:** En Telegram, abra el chat del bot[cite: 25].
2. **Escriba el comando con los NIT:** Escriba `/estudiopagos`, un espacio y los NIT de los proveedores separados por comas[cite: 25]:
   `/estudiopagos 900123456, 800987654`[cite: 25]
   * Solo NIT, nunca el nombre[cite: 25]. Así se evitan confusiones como "Telcobras", "Telcovras" o "Telco"[cite: 25].
   * Solo números: sin puntos, sin guion y sin dígito de verificación, igual que el NIT registrado en Odoo[cite: 25].
   * Puede pedir uno o varios proveedores en el mismo mensaje[cite: 25].
   * *No se repiten facturas:* si una factura ya se le envió antes al gerente, no se vuelve a mandar[cite: 25].
3. **Lea la respuesta del bot:**[cite: 25]

| Si el bot responde... | Significa y qué hacer |
| :--- | :--- |
| `Procesando solicitud...`[cite: 25] | Todo bien[cite: 25]. El resumen le llegará al gerente[cite: 25]. |
| `El NIT X no existe en Odoo`[cite: 25] | Revise que el NIT esté bien escrito[cite: 25]. Si lo está, el proveedor no está creado en Odoo[cite: 25]. |
| `El proveedor X está a paz y salvo`[cite: 25] | No tiene facturas publicadas con saldo pendiente[cite: 25]. |
| Pide el formato correcto[cite: 25] | El comando quedó mal escrito o faltó el NIT[cite: 25]. Vuelva a enviarlo como en el paso 2[cite: 25]. |

---

## 4. Paso a paso Gerente Financiero

1. **Lea el resumen del proveedor:** Llega un solo mensaje por proveedor, con todas sus facturas pendientes y la deuda total[cite: 26]. Así su celular no se llena de notificaciones[cite: 26].
2. **Apruebe o rechace cada factura:**[cite: 26]
   * Pulse para aprobar o **X** para rechazar la factura[cite: 26].
   * Aparece un aviso corto: `Factura registrada`[cite: 26].
   * Los botones de esa factura desaparecen del mensaje[cite: 26]. Las demás siguen esperando su decisión[cite: 26].

Puede decidir cuando quiera: hoy o dentro de tres días; el sistema espera[cite: 26]. Y si por mala señal pulsa el mismo botón varias veces, solo cuenta una vez (el bot puede avisar "Esta factura ya fue evaluada")[cite: 26].

---

## 5. Dónde queda registrada cada decisión
Cada clic del gerente se escribe automáticamente en el libro de Excel, en una de sus dos hojas[cite: 27]:

* **Hoja `APROBADOS`:** Columnas: `NIT`, `Proveedor`, `Factura`, `Monto`, `Fecha` (del clic)[cite: 27].
* **Hoja `RECHAZADOS`:** Columnas: `NIT`, `Proveedor`, `Factura`, `Monto`, `Fecha` (del clic)[cite: 27].

**Contabilidad y tesorería:** Use la hoja `APROBADOS` como la lista autorizada para programar los pagos[cite: 27]. No edite ni borre filas: es el registro de control de las decisiones del gerente[cite: 27].

### Estados internos de una factura
* `PENDIENTE_APROBACION` $\rightarrow$ (el gerente pulsa un botón) $\rightarrow$ `PROCESADO`[cite: 27]
* Una factura en `PROCESADO` ya tiene decisión y no vuelve a aparecer en los estudios[cite: 27].

---

## 6. Qué hacer cuando algo sale mal

| Situación | Causa más probable | Qué hacer |
| :--- | :--- | :--- |
| **Una factura que sé que se debe no aparece**[cite: 27] | Está en borrador en Odoo, ya figura como pagada o ya se le envió antes al gerente[cite: 27]. | Revísela en Odoo[cite: 27]. Si está en borrador, confírmela y pida el estudio de nuevo[cite: 27]. |
| **Necesito volver a enviar una factura al gerente**[cite: 27] | El sistema no repite facturas ya enviadas[cite: 27]. | Pida a soporte que la libere del registro interno[cite: 27]. |
| **El gerente se equivocó (aprobó en vez de rechazar)**[cite: 27] | La decisión ya quedó registrada y el botón desapareció[cite: 27]. | No se puede deshacer desde Telegram[cite: 27]. Avise a contabilidad y a soporte para dejar la corrección documentada[cite: 27]. |
| **El botón no responde o se queda cargando**[cite: 27] | Mala conexión del celular[cite: 27]. | Espere unos segundos[cite: 27]. Pulsar varias veces no duplica el registro[cite: 27]. |
| **No llega ninguna respuesta del bot**[cite: 27] | El flujo está detenido o falló la conexión con Odoo[cite: 27]. | Avise a soporte técnico[cite: 27]. |

---

## 7. Anexo: Para soporte técnico
*Herramientas:* Make (orquestador), Telegram Bot, Odoo 19 Online (API), Data Store de Make, Microsoft 365 Excel[cite: 28].

* **Escenario 1: Solicitud y envío (push):**[cite: 28]
  * `Telegram Bot Watch Updates`: Escucha mensajes con `/estudiopagos`; captura `chat_id` y texto[cite: 28].
  * `Text Parser`: Limpia el comando y convierte los NIT en un arreglo[cite: 28].
  * `Iterator`: Procesa un NIT a la vez[cite: 28].
  * `Odoo Search/Read res.partner`: Dominio `[["vat", "=", NIT]]`. Si no existe, avisa a la ejecutiva[cite: 28].
  * `Odoo Search/Read account.move`: Dominio: `partner_id`, `move_type in_invoice`, `state = posted`, `payment_state != paid`[cite: 28].
  * `Iterator 2`: Recorre las facturas devueltas[cite: 28].
  * `Data Store Check / Add record`: Idempotencia: solo registra facturas nuevas con estado `Pendiente_Aprobacion`[cite: 28].
  * `Array Aggregator`: Agrupa por proveedor y arma la matriz de botones inline (`callback_data`)[cite: 28].
  * `Telegram Bot Send Message`: Envía un solo mensaje por proveedor al `chat_id` del gerente[cite: 28].

* **Escenario 2: Captura de decisión y registro (pull):**[cite: 28]
  * `Telegram Bot Watch Updates (callback)`: Recibe el clic con datos tipo `APROBAR_ID456 / RECHAZAR_ID456`[cite: 28].
  * `Tools Set Variable`: Separa la acción y el ID de la factura[cite: 28].
  * `Data Store Get / Update record`: Anti doble clic: solo continúa si el estado es `Pendiente_Aprobacion` y lo cambia a `PROCESADO`[cite: 28].
  * `Router`: Ruta Aprobar o Rechazar[cite: 28].
  * `Excel Add a Worksheet Row`: Escribe en la hoja `APROBADOS` o `RECHAZADOS`[cite: 28].
  * `Telegram Bot Edit Message Reply Markup`: Quita los botones de la factura evaluada y responde el callback[cite: 28].

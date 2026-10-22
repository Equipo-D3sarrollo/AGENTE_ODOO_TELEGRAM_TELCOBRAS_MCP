<!-- Path: inventario_y_logistica/06_control_flota.md -->

# Control de la Flota de Vehículos en Odoo

**Módulo Odoo:** Flota[cite: 27, 28]
**Rol Requerido:** Responsables de flota, Área Operativa y Área Contable[cite: 27]
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 27]
**Elaborado por:** Joshua Lores Florez[cite: 27]
**Cuándo usar este documento:** Usa este manual cuando necesites registrar vehículos nuevos, gestionar salidas y regresos diarios, programar mantenimientos, controlar costos u administrar contratos vigentes (como el SOAT y pólizas) en el módulo de Flota de Odoo[cite: 27, 28].

---

## 1. ¿Para qué sirve el módulo Flota?
Telcobras utiliza sus vehículos para la operación (técnicos, visitas, administración)[cite: 28]. Dado que no se compran ni se venden vehículos como modelo de negocio, el módulo está adaptado para responder a una sola pregunta clave del día a día: **¿qué vehículo está libre, cuál está en la calle y cuál está en el taller?**[cite: 28]

Funciona como el tablero de llaves de una portería[cite: 28]. Cada vehículo cuenta con una tarjeta digital que se desplaza según su estado operativo[cite: 28].

### Estados del Tablero (Vista Kanban)
* **Disponible:** El vehículo se encuentra en la sede y está libre para ser utilizado[cite: 28].
* **En servicio:** El vehículo está en la calle operando con un conductor asignado[cite: 28].
* **Mantenimiento:** El vehículo se encuentra en el taller por una reparación o servicio programado[cite: 28].
* **Fuera de servicio:** El vehículo se encuentra inoperativo por un período prolongado (daño mayor, trámites, etc.). Es un estado distinto a un mantenimiento normal[cite: 28].

---

## 2. Configuración del Módulo
El módulo estándar de Odoo está diseñado para compraventa de vehículos, por lo que se ajustó específicamente para una flota propia de uso operativo[cite: 29]:
* **Estados del vehículo:** Se reemplazaron los estados comerciales por los estados de disponibilidad (*Disponible*, *En servicio*, *Mantenimiento* y *Fuera de servicio*)[cite: 29].
* **Tipos de servicio:** Se configuraron los 6 mantenimientos habituales de la flota[cite: 29]:
  1. *Cambio de aceite* (por tiempo o kilometraje)[cite: 29].
  2. *Servicio de frenos* (por kilometraje o revisión)[cite: 29].
  3. *Rotación de neumáticos* (por kilometraje)[cite: 29].
  4. *Inspección anual* (por tiempo)[cite: 29].
  5. *Reemplazo de batería* (por tiempo o falla)[cite: 29].
  6. *Correa de distribución* (por kilometraje)[cite: 29].
* **Etiquetas:** Se usan etiquetas operativas en lugar de categorías de venta (ej. *Técnicos*, *Administrativo* o por sede/zona)[cite: 29].
* **Contratos de flota:** Se emplean para controlar vigencias y costos recurrentes como SOAT, pólizas, lavado, GPS/rastreo y otros servicios[cite: 29].
* **Alerta de fin de contrato:** Sistema de avisos anticipados antes de que venza un documento obligatorio[cite: 29].
* **Fabricantes y modelos:** Almacenan datos predeterminados (combustible, transmisión, puertas, asientos) que se autocompletan al registrar un vehículo[cite: 29].

> **¡Advertencia importante sobre conductores!** 
> Al crear o editar el contacto de un conductor, **deje vacío el campo Empresa**[cite: 29]. Si se le asigna únicamente a *Telcobras*, la caja menor de *Xpertic* se bloqueará al realizarle pagos debido a restricciones multiempresa (consulte el Manual 3)[cite: 29].

---

## 3. El Día a Día: Salida y Regreso
El control operativo se realiza moviendo la tarjeta del vehículo en el tablero y actualizando el odómetro[cite: 30]. Odoo no cuenta con un botón automático de salida/entrada[cite: 30].

### Cuando el vehículo sale:
1. Vaya a **Flota** > **Vehículos** > **Vehículos** (vista de tablero)[cite: 30].
2. Arrastre la tarjeta del vehículo desde *Disponible* hasta *En servicio*[cite: 30].
3. Abra la tarjeta y asigne el **Conductor** correspondiente[cite: 30].
4. Anote la lectura actual del odómetro (kilometraje de salida)[cite: 30].

### Cuando el vehículo regresa:
1. Arrastre la tarjeta desde *En servicio* hasta *Disponible*[cite: 30].
2. Registre la lectura final del odómetro[cite: 30].
3. Anote cualquier novedad (golpe, ruido o falla) en las notas de la ficha del vehículo[cite: 30].

*Cálculo automático de recorrido:* Odómetro al regresar menos odómetro al salir (ej. 45.380 - 45.210 = 170 km recorridos)[cite: 30]. El historial (*chatter*) registra automáticamente fecha, hora y usuario responsable de cada movimiento[cite: 30].

---

## 4. Mantenimiento, Combustible y Costos

### Envío de un vehículo al taller:
1. Mueva la tarjeta del vehículo al estado **Mantenimiento**[cite: 31].
2. Cree el registro del servicio en **Flota** > **Flota** > **Servicios** > **Nuevo**[cite: 31] indicando la fecha, tipo de servicio, vehículo, proveedor (taller), número de factura, odómetro y costo[cite: 31].
3. Cuando el vehículo salga del taller, devuelva la tarjeta al estado **Disponible**[cite: 31].
*Regla de oro:* La tarjeta en el tablero y el registro de servicio deben ir siempre de la mano para evitar desfases en el historial[cite: 31].

### Registro de combustible y gastos ocasionales:
* Registre cada tanqueo y gasto menor (peajes, parqueadero o lavado ocasional) en **Flota** > **Flota** > **Servicios**, especificando su valor y odómetro[cite: 31].
* Los pagos recurrentes contratados (lavado mensual, plataforma GPS) o con vigencia legal (SOAT, pólizas) **no van en servicios**, sino como contratos[cite: 31].

---

## 5. Contratos: SOAT, Pólizas y Pagos Recurrentes
Un contrato de flota en Odoo registra obligaciones con vigencia determinada (fecha de inicio, vencimiento, proveedor y costo)[cite: 32].

### Configuración inicial de Tipos de Contrato:
Vaya a **Flota** > **Configuración** > **Tipos de servicio** > **Nuevo** (con categoría *Contrato*)[cite: 32]:
* **SOAT:** Vigencia anual obligatoria para todos los vehículos[cite: 32].
* **Seguro / Póliza:** Póliza contra todo riesgo o responsabilidad civil (anual o mensual)[cite: 32].
* **Lavado:** Servicio periódico contratado (mensual)[cite: 32].
* **GPS / Rastreo:** Plataforma de monitoreo y SIM del equipo (mensual)[cite: 32].
* **Otros servicios recurrentes:** Pagos fijos como parqueaderos contratados[cite: 32].

Active la *Alerta de fin de contrato* en **Flota** > **Configuración** > **Ajustes** (se recomienda un aviso con 30 días de anticipación)[cite: 32].

### Registrar un contrato (SOAT, póliza, GPS...)
1. Vaya a **Flota** > **Flota** > **Contratos** > **Nuevo** (o desde la ficha del vehículo con el botón *Contratos*)[cite: 33].
2. Complete los campos obligatorios[cite: 33]:
   * **Nombre:** Tipo + Placa + Año (ej. `SOAT-ABC-123-2026`)[cite: 33].
   * **Fechas:** Inicio y vencimiento exactas según el documento[cite: 33].
   * **Costo recurrente y Frecuencia:** Ingrese el valor en *Costo recurrente* y deje el costo de activación en **cero** para evitar duplicar costos en los reportes[cite: 33].
3. Adjunte el PDF oficial del documento en el historial (*chatter* / clip) del contrato[cite: 33].
*Renovación anual:* Cierre el contrato anterior y cree uno nuevo con la vigencia actualizada para preservar el historial año a año[cite: 33].

---

## 6. Registro de un Vehículo Nuevo
Para dar de alta un nuevo activo, diríjase a **Flota** > **Vehículos** > **Vehículos** > **Nuevo** y diligencie[cite: 35]:
* **Matrícula (Placa):** Identificador principal[cite: 35].
* **Modelo:** Al seleccionarlo, se autocompletan datos técnicos (transmisión, combustible, etc.)[cite: 35].
* **Año, color y último odómetro**[cite: 35].
* **Conductor y Responsable de flota**[cite: 35].
* **Estado inicial:** *Disponible*[cite: 35].
* **Etiquetas:** Asigne la zona o área correspondiente[cite: 35].
*Sugerencia:* Suba fotografías del vehículo al crearlo (frente, costados, interior y daños previos) como respaldo ante futuros incidentes[cite: 35].

---

## 7. Consultas y Reportes
* **Tablero Kanban:** Estado actual de toda la flota en tiempo real[cite: 35].
* **Listas y Pivotes:** Filtros personalizables por conductor, estado, etiqueta o servicio[cite: 35].
* **Contratos por vencer:** Control desde **Flota** > **Flota** > **Contratos** filtrando por vigencia[cite: 35].
* **Análisis de costos:** Mide el costo por kilómetro y el costo total de propiedad por vehículo[cite: 34, 35].

---

## 8. Troubleshooting (Errores Comunes)

| Problema detectado | Causa probable | Solución |
| :--- | :--- | :--- |
| **El tablero dice "En servicio" pero el vehículo está en la sede**[cite: 36] | No se actualizó el estado al regresar[cite: 36]. | Mueva la tarjeta a *Disponible* y registre el odómetro final[cite: 36]. |
| **Odoo rechaza la lectura del odómetro**[cite: 36] | El valor ingresado es menor al registro anterior por error de digitación[cite: 36]. | Verifique el odómetro físico del vehículo y reingrese el número correcto[cite: 36]. |
| **Vehículo en "Mantenimiento" sin registro de servicio**[cite: 36] | Se movió la tarjeta en el tablero pero se omitió el asiento del taller[cite: 36]. | Cree el registro en *Flota > Flota > Servicios* adjuntando la factura[cite: 36]. |
| **La caja menor de Xpertic arroja "Error de acceso" con un conductor**[cite: 36] | El contacto del conductor tiene una empresa asignada exclusivamente[cite: 36]. | Deje vacío el campo *Empresa* en la ficha del contacto (Manual 3)[cite: 36]. |
| **El costo del SOAT se duplica en el análisis**[cite: 36] | Se llenaron simultáneamente el costo de activación y el recurrente[cite: 36]. | Deje el costo de activación en cero y use únicamente el *Costo recurrente*[cite: 36]. |
| **No llega la alerta de vencimiento de un contrato**[cite: 36] | La alerta está deshabilitada o falta la fecha de vencimiento[cite: 36]. | Active las alertas en *Flota > Configuración > Ajustes* y complete la fecha límite[cite: 36]. |

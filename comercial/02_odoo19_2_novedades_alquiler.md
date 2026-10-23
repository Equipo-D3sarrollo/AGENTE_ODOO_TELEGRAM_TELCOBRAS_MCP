<!-- Path: comercial/02_odoo19_2_novedades_alquiler.md -->

# 🏗️ Manual Operativo 02: Novedades del Módulo de Alquiler y Planificación de Recursos (Odoo 19.1 y 19.2)

**Módulo / Área:** Comercial y Proyectos / Alquileres, Servicios y Planificación  
**Rol Requerido:** Administrador del Sistema, Analistas Comerciales, Coordinador de Proyectos  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual (aplicable a las versiones online SaaS 19.1/19.2) para configurar y comercializar el alquiler temporal de herramientas especializadas (ej. fusionadoras, OTDR, plantas eléctricas) o para asignar técnicos y recursos limitados a proyectos de obra, garantizando que el sistema bloquee reservas cruzadas y asigne precios automáticos por volumen o temporada.

---

## 1. Rediseño de la Configuración de Precios y Periodicidad

El módulo de Alquiler en las subversiones 19.1 y 19.2 elimina la casilla tradicional de "Se puede alquilar". Ahora, la naturaleza de alquiler de un producto se determina configurándole una **Periodicidad**.

1. **Configuración Base:** Ir a la pestaña **Ventas > Alquiler** del producto y definir su periodicidad base (ej. Días, Horas, Noches).
2. **Precio Unitario:** El precio de venta del producto se convierte en el **precio base por periodo** (ej. si la periodicidad es "Días" y el precio es $100.000, el cálculo base será $100.000 diarios).
3. **Restricción de Ventas:** Un producto configurado para alquiler no debe agregarse a órdenes de venta directas. Es mandatorio separar el catálogo creando ítems distintos (ej. "Fusionadora Venta" vs. "Fusionadora Alquiler").

---

## 2. Listas de Precios Avanzadas por Volumen o Temporada

Para incentivar alquileres prolongados (ej. contratos semanales en obra), se emplean las **Listas de Precios** para modificar el valor unitario en función del volumen:

* **Cantidad Mínima en Alquiler:** Se calcula multiplicando la `cantidad física del equipo` por el `periodo de alquiler` (ej. alquilar 1 equipo por 7 días o 7 equipos por 1 día activan la misma regla de cantidad mínima "7").
* **Configuración:** Desde la pestaña de Precios del producto, configurar una regla que establezca un precio menor (ej. $80.000/día) cuando la cantidad mínima alcance o supere 7.
* **Precios por Temporada:** Las reglas permiten establecer rangos de fechas de validez. Así, es posible configurar picos tarifarios durante temporadas de alta demanda de proyectos o emergencias climatológicas.

---

## 3. Integración con el Módulo de Planificación de Recursos

La integración entre Alquiler y Planificación permite asegurar la disponibilidad física de equipos críticos o personal certificado para evitar sobreventas (reservas duplicadas).

1. **Definición de Roles de Servicio:** Crear productos de tipo **Servicio** (ej. "Operario Certificado" o "Sala de Servidores Temporal") configurados con la opción de **Planificar el servicio**.
2. **Asignación de Recursos limitados:** Vincular el rol a los recursos físicos existentes (ej. 3 equipos técnicos disponibles).
3. **Bloqueo Inteligente:** Si un cliente en el portal o un comercial en el backend intenta reservar un recurso en una fecha donde la disponibilidad es cero, **Odoo bloqueará automáticamente la confirmación de la orden**.
4. **Turnos Previos (Padding/Preparación):** Desde Planificación, el equipo logístico puede crear turnos manuales en los días previos a un alquiler para bloquear el recurso y destinarlo a mantenimiento o alistamiento de la carga.

---

## 4. Alquiler Temporal vs. Suscripciones Recurrentes

Para los servicios que Telcobras comercializa, es crucial distinguir el módulo correcto de aplicación:

| Característica / Escenario | Módulo de Alquiler | Módulo de Suscripción |
| :--- | :--- | :--- |
| **Duración del Acuerdo** | Periodo definido (Fecha de inicio y fin claras). | Indefinido o recurrente prolongado. |
| **Mecánica de Facturación** | Facturación total por la orden consolidada. | Facturación periódica (mes a mes) automatizada. |
| **Casos de Uso Telcobras** | Préstamo de herramientas para un contratista por obra (días/semanas). | Servicios en comodato (equipos de red), soporte de infraestructura, acceso a canales dedicados. |

---

## 5. Directrices Operativas (Puntos de Control)

* **Traslados de Inventario:** Activar la configuración de traslados logísticos para alquiler desde la aplicación de *Inventario*, permitiendo a Telcobras conocer la ubicación exacta (obras o bodegas) donde se encuentra prestado cada activo.
* **Tiempos de Margen:** Las versiones actuales omiten multas automáticas y márgenes globales. Los tiempos de preparación (limpieza, mantenimiento, despacho) deben imputarse ya sea configurando horas por defecto en la ficha del producto, o agregando turnos manuales en Planificación.

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Alquiler y Planificación de Recursos en Odoo 19.1/19.2 (Configuración de precios por periodicidad, listas de precios avanzadas por temporada, bloqueo de órdenes por disponibilidad de recursos, y diferenciación frente a suscripciones): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/comercial/02_odoo19_2_novedades_alquiler.md

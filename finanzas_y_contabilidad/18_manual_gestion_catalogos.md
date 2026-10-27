<!-- Path: finanzas_y_contabilidad/18_manual_gestion_catalogos.md -->

# 🗂️ Manual Operativo 18: Gestión de Catálogos y Tablas Paramétricas DIAN - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Arquitectura de Datos y Parametrización  
**Rol Requerido:** Administrador de Base de Datos, Analista Funcional, Desarrollador Backend  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual durante la fase de despliegue inicial del software o cuando la DIAN publique actualizaciones normativas que modifiquen las listas de valores permitidos (códigos de impuestos, unidades de medida, medios de pago, municipios, responsabilidades fiscales, etc.).

---

# MANUAL DE GESTIÓN DE CATÁLOGOS DIAN

**ID del Manual:** DIAN-UBL2.1-M18  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN)  
**Formato de Salida:** Tablas maestras sincronizadas y mapeo JSON/XML.  
**Propósito:** Definir el modelo de datos, la estructura de almacenamiento y el procedimiento de actualización para garantizar que todos los nodos UBL del XML consuman códigos estandarizados y avalados por las tablas paramétricas oficiales de la DIAN.

---

## 1. PROCEDIMIENTO GENERAL

La facturación electrónica en Colombia es un modelo estrictamente estructurado. La DIAN no permite texto libre para definir variables clave (ej. no se puede escribir "Transferencia" o "Caja de Cartón"). El sistema debe implementar un repositorio central de "Catálogos" donde se almacenan las tablas paramétricas oficiales. Cuando un usuario factura, selecciona de listas desplegables (UI) y el motor backend extrae el "Código Técnico" correspondiente para inyectarlo en el XML.

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Estructura de Base de Datos (Maestras):** El esquema de la base de datos debe contener tablas paramétricas independientes (ej. `dian_catalog_taxes`, `dian_catalog_payment_methods`, `dian_catalog_cities`).
2. **Interfaz de Administración:** El sistema debe proveer una UI (backend administrativo) donde el equipo de soporte pueda cargar actualizaciones de catálogos sin necesidad de modificar el código fuente del motor.
3. **Mapeo ERP vs. DIAN:** Si el sistema se integra con un ERP preexistente (ej. Odoo, SAP, Dynamics), se requiere una tabla puente ("Homologación") para traducir los códigos internos del ERP a los códigos exigidos por la DIAN.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de Estricta Concordancia:** El motor de integración debe rechazar internamente cualquier documento que intente utilizar un código no presente en la última versión de los catálogos del Anexo 1.9.
- **Regla de Listas Internacionales:** Además de las tablas propias de la DIAN, el software debe gestionar catálogos internacionales adoptados por la norma: Divisas (ISO 4217), Países (ISO 3166-1 alpha-2), y Unidades de Medida (UN/ECE Rec 20).
- **Regla de Consistencia Tributaria:** Los códigos de responsabilidad fiscal (O-13, O-15, O-23, O-47) deben corresponder exactamente con el Régimen Tributario reportado (ej. no se puede enviar responsabilidad `O-13` Gran Contribuyente si se reporta como "Régimen Simplificado / No responsable").

---

## 4. DETALLE DE LOS PROCEDIMIENTOS Y MAPEO DE CATÁLOGOS

### 4.1. Catálogo de Tributos (Impuestos, Retenciones y Tasas)
Mapea los códigos para IVA, INC, ICA, retenciones en la fuente, etc.
- **Ruta XML Afectada:** `cac:TaxScheme/cbc:ID`, `cac:TaxScheme/cbc:Name`
- **Mapeo Principal:**
  - `01` - IVA (Impuesto sobre las Ventas)
  - `02` - IC (Impuesto al Consumo)
  - `03` - ICA (Impuesto de Industria y Comercio)
  - `04` - INC (Impuesto Nacional al Consumo)
  - `05` - ReteIVA (Retención sobre el IVA)
  - `06` - ReteFuente (Retención en la Fuente por Renta)
  - `07` - ReteICA (Retención sobre el ICA)
  - `ZZ` - No Aplica / Nombre del tributo (Para facturas exentas que requieren este nodo por validación).

### 4.2. Catálogo de Medios y Formas de Pago
Define cómo y cuándo se paga la factura.

**Formas de Pago (`PaymentMeans/ID`):**
- `1` - Contado (Pago inmediato).
- `2` - Crédito (Requiere nodo `PaymentTerms` indicando la fecha de vencimiento).

**Medios de Pago (`PaymentMeans/PaymentMeansCode`):**
- `10` - Efectivo
- `41` - Transferencia Débito Bancaria
- `42` - Consignación Bancaria
- `48` - Tarjeta de Crédito
- `49` - Tarjeta Débito
- `97` - Compensación (Clearing entre empresas)
- `ZZ` - Mutuo acuerdo

### 4.3. Catálogo de Unidades de Medida (UN/ECE Rec 20)
Determina en qué presentación se vende el ítem.
- **Ruta XML Afectada:** Atributo `@unitCode` en `cbc:InvoicedQuantity`, `cbc:BaseQuantity`, `cbc:PriceAmount`.
- **Mapeo Principal (Ejemplos Frecuentes):**
  - `94` - Unidad (Goods) / Pieza
  - `EA` - Cada Uno (Each)
  - `HUR` - Hora (Servicios)
  - `DAY` - Día
  - `KGM` - Kilogramo
  - `MTR` - Metro
  - `LTR` - Litro

### 4.4. Catálogo de Responsabilidades Fiscales (RUT)
Información tributaria extraída de la casilla 53 del RUT del emisor y adquiriente.
- **Ruta XML Afectada:** `cac:PartyTaxScheme/cbc:TaxLevelCode` (Dentro de `AccountingSupplierParty` y `AccountingCustomerParty`).
- **Mapeo (Valores concatenados separados por punto y coma si son múltiples):**
  - `O-13` - Gran Contribuyente
  - `O-15` - Autorretenedor
  - `O-23` - Agente de retención en el impuesto sobre las ventas
  - `O-47` - Régimen Simple de Tributación
  - `R-99-PN` - No Responsable (Persona Natural)

### 4.5. Catálogo de División Política y Administrativa (DIVIPOLA)
Códigos para identificar los municipios de origen y destino, avalados por el DANE.
- **Ruta XML Afectada:** `cac:Address/cbc:CitySubdivisionName` y `cbc:CityName`.
- **Mapeo (Ejemplo):**
  - Departamento (Valle del Cauca): `76`
  - Municipio (Cali): `76001` (El XML requiere el código de 5 dígitos del DANE completo).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Integridad Referencial:** Cuando se procesa un JSON de entrada para generar una factura, el motor consulta las tablas maestras y valida que cada código exista en el catálogo de la versión 1.9.
2. **Actualización Segura:** Los cambios introducidos por nuevas resoluciones se cargan en la base de datos sin afectar el motor central de cálculos matemáticos o de firma digital.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si la DIAN rechaza con `Regla 90: El valor del elemento DocumentCurrencyCode no es un valor válido`, verifique que el catálogo de divisas utilice la ISO 4217 de tres letras en mayúscula (ej. `COP`, `USD`, `EUR`). No se permite `pesos` o `$`.
- Si se presenta `Error al validar CitySubdivisionName`, confirme que el catálogo interno no está enviando el nombre de la ciudad en texto libre, sino el código numérico asignado por el DANE a dicho municipio.
- Si hay un error de `TaxLevelCode` en un cliente internacional, verifique que al exportar, el código del cliente esté parametrizado como `R-99-PN` o la equivalencia técnica dictaminada para el exterior (usualmente código país y NIT genérico `444444044`).
- Si recibe error en `unitCode`, es un problema típico en servicios. Los servicios no se venden en "Cajas", se debe usar el catálogo UN/ECE para tiempo (ej. `HUR` para hora) o servicios genéricos (`WSD` o `ZZ` dependiendo del anexo vigente).

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipos de Catálogos DIAN y Frecuencia de Actualización**
| Nombre del Catálogo | Estándar Origen | Frecuencia de Cambio (Estimada) | Tipo de Validacion DIAN |
| :--- | :--- | :--- | :--- |
| **Tributos** | DIAN (Anexo Técnico) | Baja (Cambios normativos mayores) | Estricta (Rechazo inmediato) |
| **Unidades de Medida** | ONU (UN/ECE) | Muy Baja | Estricta |
| **Municipios y Deptos** | DANE | Media (Creación de nuevos mcpios) | Estricta |
| **Medios de Pago** | DIAN | Baja | Estricta |
| **Monedas / Países** | ISO (4217 / 3166) | Nula | Estricta |

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Catálogos DIAN, Tablas Paramétricas, DIVIPOLA, UN/ECE Rec 20, ISO 4217, Códigos de Impuestos, Medios de Pago, TaxLevelCode: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/18_manual_gestion_catalogos.md

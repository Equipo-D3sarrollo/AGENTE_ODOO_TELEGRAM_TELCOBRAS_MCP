<!-- Path: finanzas_y_contabilidad/19_manual_extensiones_sectoriales.md -->

# 🧩 Manual Operativo 19: Extensiones y Datos Sectoriales (Condicional) - UBL 2.1

**Módulo / Área:** Motor de Integración DIAN / Arquitectura de Datos y Sectores Regulados  
**Rol Requerido:** Analista Funcional Sectorial, Desarrollador Backend, Especialista de Integración  
**Empresa / Proyecto:** Motor DIAN Colombia (Resolución 165)  
**Cuándo usar este documento:** Utilice este manual únicamente si el motor de facturación debe generar documentos para clientes que operan en sectores altamente regulados con requisitos tributarios o informativos extra, tales como el Sector Salud (MinSalud - RIPS), Sector Transporte de Carga, Operaciones de Intermediación (Mandatos) o Control Cambiario (Exportaciones/Moneda Extranjera).

---

# MANUAL DE EXTENSIONES Y DATOS SECTORIALES (CONDICIONAL)

**ID del Manual:** DIAN-UBL2.1-M19  
**Versión de UBL:** 2.1 / Anexo Técnico 1.9 (Resolución 165 DIAN) + Resoluciones Sectoriales  
**Formato de Salida:** Nodos `ext:UBLExtensions`, `cac:AdditionalDocumentReference`, `cac:AgentParty`  
**Propósito:** Definir el mapeo estructural y algorítmico para inyectar metadatos específicos exigidos por entidades reguladoras ajenas a la DIAN (como el Ministerio de Salud o la SuperTransporte) dentro del estándar UBL 2.1, sin romper la validación criptográfica y fiscal de la factura base.

---

## 1. PROCEDIMIENTO GENERAL

El estándar UBL 2.1 adoptado por la DIAN está diseñado para el comercio general. Sin embargo, ciertos sectores económicos requieren reportar información adicional en sus facturas electrónicas. Para esto, la DIAN habilitó mecanismos de extensibilidad:
1. **Cambio de Perfil (`CustomizationID`):** Para indicar a la DIAN que la factura obedece a un esquema sectorial (Ej. Operaciones en Salud).
2. **Nodos de Referencia (`AdditionalDocumentReference`):** Para vincular documentos externos como manifiestos de aduana o remesas de transporte.
3. **Mapeo de Terceros (Mandato):** Para separar fiscalmente quién genera la factura (Mandatario) de quién recibe los ingresos (Mandante).

---

## 2. PRECONDICIONES DEL SISTEMA Y NEGOCIO

1. **Parámetros de Entidad Habilitados:** El motor debe poder recibir en su JSON/API de entrada banderas booleanas sectoriales (ej. `is_health_sector: true`, `is_mandate: true`).
2. **Catálogos Adicionales:** Bases de datos precargadas con los catálogos de Ministerios correspondientes (ej. Códigos CUPS, CIE-10 para salud; Códigos de aduana para exportación).
3. **Flexibilidad del Validador XML:** El *schema validator* local (XSD) debe estar actualizado con los esquemas de extensiones avalados por la DIAN para evitar rechazos en el proceso de pre-firma.

---

## 3. REGLAS DE NEGOCIO Y VALIDACIÓN CRÍTICA (PRE-ENSAMBLAJE)

- **Regla de No Interferencia:** Ninguna extensión sectorial (`ext:ExtensionContent`) puede alterar o sobreescribir los nodos obligatorios de la DIAN (CUFE, QR, Firma, Totales Monetarios). Las extensiones se añaden al final del documento o en los bloques definidos.
- **Regla de Sector Salud (MinSalud):** Si se factura en el sector salud (Resolución 2275 de 2023), es de obligatorio cumplimiento la generación concurrente de los RIPS (Registro Individual de Prestación de Servicios) vinculados a la factura electrónica.
- **Regla del Ingreso a Terceros (Mandato):** En facturación por mandato, el NIT del facturador que firma electrónicamente es el del intermediario, pero los ingresos, retenciones e impuestos se declaran a nombre del NIT del mandante, el cual debe ir explícitamente en el XML.

---

## 4. DETALLE DE LOS PROCEDIMIENTOS (MAPEO SECTORIAL)

### Paso 4.1: Sector Salud (FEV-Salud / MinSalud)
Aplica para clínicas, hospitales, IPS y profesionales independientes de la salud.

- **Perfil de Customización (`CustomizationID`):** Debe modificarse. El valor estándar `10` cambia a los códigos asignados para salud según la operación (ej. `11` o el definido en el anexo vigente).
- **Extensiones (Ruta XML):** `/Invoice/ext:UBLExtensions/...` (Se crea una nueva extensión específica para el Ministerio de Salud).
- **Datos a inyectar:**
  - Código del Prestador de Servicios de Salud (REPS).
  - Modalidad de pago y contrato (Capitación, Evento, PGP).
  - Copagos, cuotas moderadoras y anticipos (Descontados del `PayableAmount`).
  - Número de póliza o autorización.
- **Relación RIPS:** Debe generarse un identificador único que cruce la factura (XML) con el paquete JSON de los RIPS.

### Paso 4.2: Sector Transporte de Carga Terrestre
Aplica para empresas fleteras y logística.

- **Datos de Referencia (`AdditionalDocumentReference`):** Se deben incluir referencias directas a los documentos legales de transporte.
  - **Remesa de Transporte:** Número de la remesa amparada.
  - **Manifiesto de Carga:** Si aplica.
- **Información del Ítem (`cac:InvoiceLine`):** La descripción de la línea de factura debe detallar el trayecto (Origen - Destino), peso facturado, y el código del servicio asociado.

### Paso 4.3: Contratos de Mandato (Facturación por cuenta de terceros)
Aplica para inmobiliarias, agencias de viaje, consorcios y *marketplaces*.

- **Configuración del Emisor (`AccountingSupplierParty`):** Aquí van los datos del "Mandatario" (quien opera el software, expide y firma el XML).
- **Inyección del Mandante (`cac:Party` / `cac:AgentParty`):** Se debe mapear el nodo de intermediación donde se identifica con NIT y Nombre al verdadero dueño del ingreso ("Mandante").
- **Manejo de Impuestos (`TaxTotal`):** Los tributos se calculan y reportan a nombre del Mandante. El XML exige un desglose preciso para evitar que la DIAN asuma esos impuestos como obligaciones del Mandatario.
- **Líneas de Factura:** Es posible tener facturas "Mixtas" donde una línea pertenece a un Mandante A, otra al Mandante B, y otra es ingreso propio del Mandatario (Comisión). Cada línea (`InvoiceLine`) permite especificar un mandante distinto si es necesario.

### Paso 4.4: Exportaciones y Control Cambiario
Aplica para empresas que venden bienes o servicios al exterior en moneda extranjera.

- **Tipo de Operación (`CustomizationID`):** `11` (Operación de Exportación).
- **Moneda del Documento (`DocumentCurrencyCode`):** Moneda de negociación (Ej. `USD`, `EUR`).
- **Tasa de Cambio (`cac:PaymentExchangeRate`):**
  - `SourceCurrencyCode`: Moneda extranjera (USD).
  - `TargetCurrencyCode`: COP.
  - `CalculationRate`: Tasa Representativa del Mercado (TRM) del día o pactada.
  - `Date`: Fecha de la tasa de cambio.
- **Impuestos (IVA):** Se emite con código `01` (IVA), tarifa `0.00%` (Exento).
- **Comprador (`AccountingCustomerParty`):** El `TaxLevelCode` del cliente en el exterior suele ser `R-99-PN`, utilizando la identificación fiscal de su país o un NIT genérico (ej. `444444044`).

---

## 5. POSTCONDICIONES Y MANEJO DE ESTADOS

1. **Aprobación Multientidad:** Una vez aceptada por la DIAN (Regreso de ApplicationResponse), el mismo documento XML (y sus RIPS asociados si es sector salud) es legalmente válido para radicación ante las EPS o el pagador institucional.
2. **Contabilidad Clara:** El motor garantiza que los ingresos de terceros (Mandato) no alteren el estado de resultados del facturador principal en el ERP, afectando únicamente las cuentas por pagar/cobrar.

---

## 6. TROUBLESHOOTING (SOLUCIÓN DE ERRORES FRECUENTES)

- Si ocurre el error `El CustomizationID no corresponde al tipo de operación`, verifique el mapeo en el API inicial. Operaciones de Exportación y Salud tienen CustomizationIDs estrictamente distintos a la factura estándar.
- Si ocurre rechazo en Sector Salud indicando `Valor de cuota moderadora no coincide con los totales`, valide la fórmula matemática de la DIAN: El `PayableAmount` debe ser igual a `TaxInclusiveAmount` menos el `Anticipo/Copago`. Los copagos en salud no son impuestos, actúan como pagos anticipados.
- Si la DIAN emite una alerta indicando que `La TRM no es válida para la fecha`, revise la integración del motor con la API del Banco de la República / Superfinanciera, ya que el XML debe reportar la tasa oficial.
- Si en Mandatos aparece la regla `El NIT del mandante no puede ser igual al del facturador`, asegúrese de no enviar los datos de la propia empresa en los nodos de `AgentParty`.

---

## 7. TABLAS DE REFERENCIA Y ANEXOS TÉCNICOS

**Tipos de Operación y Códigos de Identificación (CustomizationID)**
| Código | Tipo de Operación | Caso de Uso Principal |
| :--- | :--- | :--- |
| **10** | Estándar | Facturación general nacional. |
| **11** | Mandatos | Facturación por cuenta de terceros. |
| **09** | AIU | Operaciones de Arquitectura, Ingeniería y Construcción. |
| **11** | Exportación | Venta de bienes y servicios al exterior. |
| **XX** | Salud (FEV) | Regido por la última resolución del Ministerio de Salud. |

*(Nota: Los códigos CustomizationID pueden variar según la subversión del anexo técnico, consulte siempre la tabla paramétrica vigente de la DIAN).*

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Extensiones Sectoriales, Sector Salud DIAN, RIPS MinSalud, Contratos de Mandato, Facturación Terceros, Exportaciones, CustomizationID, TRM: https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/finanzas_y_contabilidad/19_manual_extensiones_sectoriales.md

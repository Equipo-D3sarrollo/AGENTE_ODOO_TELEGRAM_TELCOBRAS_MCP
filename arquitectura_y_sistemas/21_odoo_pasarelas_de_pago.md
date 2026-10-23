<!-- Path:arquitectura_y_sistemas/21_odoo_pasarelas_de_pago.md -->

# 💳 Manual Operativo 21: Configuración y Gestión de Pasarelas de Pago en Odoo (Cobros Online y Suscripciones)

**Módulo / Área:** Financiero y Comercial / Tesorería y Facturación  
**Rol Requerido:** Administrador del Sistema, Director Financiero, Tesorería  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para comprender, configurar y operar los mecanismos de cobro en línea mediante pasarelas de pago nativas en Odoo, permitiendo automatizar la recepción de anticipos, pagos de facturas a través del portal de clientes y cobros recurrentes por suscripción.

---

## 1. Fundamentos del Ciclo de Cobro en Línea

Una pasarela de pago es el servicio tecnológico encargado de cifrar, autorizar y procesar de manera segura las transacciones con tarjetas débito, crédito o medios electrónicos de pago (como PSE), conectando al consumidor directamente con los procesadores autorizados y el banco receptor sin comprometer los datos financieros sensibles del comercio.

**Canales de Aplicación en Odoo:**
* **E-Commerce:** Tienda en línea abierta al público general.
* **Portal de Clientes:** Pago directo de cotizaciones (anticipos de obra) o facturas pendientes desde el enlace enviado al cliente.
* **Suscripciones Recurrentes:** Cobro automático periódico de planes de soporte, mantenimiento o servicios atados a un ciclo temporal (mensual, anual).

---

## 2. Disponibilidad de Proveedores de Pago en Latinoamérica y Colombia

Odoo incluye integraciones nativas con múltiples pasarelas internacionales. Sin embargo, su habilitación depende estrictamente del país de registro de la compañía en el sistema:
* **Mercado Pago:** Nativa y compatible con operaciones en **Colombia**, permitiendo procesar tarjetas y medios locales.
* **PayPal:** Disponible de forma nativa para Colombia, orientada principalmente a transacciones internacionales.
* **Stripe:** Restringida geográficamente a mercados específicos (como México y Brasil); las instancias corporativas configuradas con país Colombia no permiten su activación nativa.

---

## 3. Procedimiento de Activación de un Proveedor de Pago

**Ruta:** Contabilidad / Sitio Web > Configuración > Proveedores de pago

1. Localizar el proveedor correspondiente (ej. *Mercado Pago*) y hacer clic en **Instalar**.
2. Ingresar a la consola de desarrolladores de la pasarela para obtener las **Credenciales API** (llaves públicas y privadas / tokens de acceso).
3. Pegar los códigos en los campos correspondientes dentro de Odoo.
4. Establecer inicialmente el estado en **Modo de prueba (Sandbox)** para realizar validaciones controladas.
5. Una vez superadas las pruebas con transacciones simuladas, cambiar el estado a **Habilitado (Producción)**.

---

## 4. Casos de Uso y Flujo Operativo

### 4.1. Pago de Anticipos o Cotizaciones por el Portal
1. El equipo comercial envía una cotización estructurada al cliente por correo electrónico con acceso al portal web.
2. El cliente visualiza el documento y hace clic en el botón de **Pagar**, seleccionando la pasarela configurada.
3. Tras completarse la transacción exitosamente en la pasarela, Odoo convierte automáticamente el presupuesto en orden de venta, genera la factura y concilia de manera instantánea el pago recibido, dejando constancia en el historial (*chatter*) de la operación.

### 4.2. Gestión de Suscripciones y Cobros Recurrentes
1. Se configuran productos bajo esquemas de cobro periódico (ej. planes de soporte técnico anual o mensual).
2. El cliente suscribe el servicio en línea autorizando cargos recurrentes.
3. El módulo de **Suscripciones** registra la fecha de próxima facturación y, si la pasarela lo soporta, ejecuta el cargo automático en el periodo estipulado generando el asiento contable y la factura electrónica respectiva sin intervención manual.

---

## 5. Consideraciones Clave para Telcobras en Colombia

* **Moneda e Impuestos:** Garantizar que los catálogos y pasarelas operen bajo la moneda oficial (**COP**) e incluyan correctamente las tarifas impositivas vigentes (**IVA del 19%**).
* **Facturación Electrónica DIAN:** Las facturas generadas de forma automatizada por cobros en línea deben acoplarse con la localización colombiana, asegurando que se incluyan los datos fiscales del cliente (**NIT con dígito de verificación**, tipo de documento y régimen fiscal).
* **Conciliación de Comisiones Netas:** Las pasarelas de pago suelen depositar los fondos descontando comisiones transaccionales. Es indispensable coordinar con el departamento contable el registro de estos costos financieros para mantener la precisión en los procesos de conciliación bancaria y causación.
* **Seguridad de Accesos:** Las llaves privadas y credenciales de producción de las pasarelas son de carácter estrictamente confidencial. Jamás deben exponerse en manuales públicos, chats o correos sin cifrar.

---
### 🔗 Línea de Enrutamiento para el System Prompt
- Pasarelas de Pago en Odoo (Configuración de proveedores nativos como Mercado Pago, cobro de anticipos por portal, integración con suscripciones y consideraciones fiscales en Colombia): https://raw.githubusercontent.com/Equipo-D3sarrollo/AGENTE_ODOO_TELEGRAM_TELCOBRAS_MCP/main/financiero/21_odoo_pasarelas_de_pago.md

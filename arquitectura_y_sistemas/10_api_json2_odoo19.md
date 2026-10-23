<!-- Path: arquitectura_y_sistemas/10_api_json2_odoo19.md -->

# 🔗 Manual Operativo: Nueva API JSON-2 y Endpoint `/doc` en Odoo 19

**Módulo / Área:** Arquitectura y Sistemas / Integraciones  
**Rol Requerido:** Administrador de Sistemas, Desarrollador Integrador  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual cuando requiera conectar aplicaciones externas (agentes de IA, e-commerce, apps móviles, etc.) con Odoo 19 Online, explorar los modelos de datos en tiempo real o migrar integraciones antiguas.

---

## 1. Contexto de la Actualización a Odoo 19

Las APIs tradicionales XML-RPC y JSON-RPC (`/xmlrpc`, `/jsonrpc`) se consideran obsoletas en Odoo 19 a favor de la nueva arquitectura **API JSON-2**. 
* **Impacto Operativo:** Las integraciones creadas para Odoo 18 o inferiores dejarán de funcionar si no se migran a este nuevo protocolo. Todo desarrollo nuevo en Telcobras debe utilizar exclusivamente la nueva API JSON-2.

---

## 2. El Endpoint interactivo `/doc`

Odoo 19 introduce una interfaz nativa que funciona como documentación viva y probador de peticiones (similar a Postman) integrado en el mismo ERP.

**Procedimiento de Acceso y Uso:**
1. Inicie sesión en Odoo 19.
2. Añada `/doc` a la URL de su instancia (ej. `https://telcobras.odoo.com/doc`).
3. **Panel Izquierdo:** Visualizará el diccionario completo de Modelos (tablas) activos en la instancia.
4. Seleccione un modelo (ej. `res.partner` para contactos) para explorar:
   * **Campos técnicos:** Nombre, tipo de dato y restricciones.
   * **Métodos disponibles:** `search_read` (buscar), `read` (leer), `create` (crear), `write` (modificar), `unlink` (eliminar).
5. **Caja de Prueba:** Permite redactar el cuerpo JSON de la petición y ejecutarla directamente desde el navegador. Odoo devolverá la respuesta estructurada en tiempo real.

---

## 3. Autenticación y API Keys

Para ejecutar pruebas en `/doc` o consumos desde una aplicación externa, es obligatorio el uso de credenciales encriptadas (Bearer Tokens).

**Procedimiento de Seguridad (Estricto Telcobras):**
1. Crear un usuario dedicado a la integración en Odoo (ej. *"API - Agente Causación"*).
2. Asignar a este usuario **únicamente los permisos estrictamente necesarios** (Principio de mínimo privilegio). 
3. Iniciar sesión con este usuario técnico e ir a *Mis preferencias > Seguridad de la cuenta > Nueva clave API*.
4. **Alerta de Seguridad:** Copie la API Key generada inmediatamente en el gestor de secretos corporativo. Odoo nunca volverá a mostrarla en pantalla.
5. Inserte la API Key en el probador de `/doc` o en las cabeceras de su aplicación externa.

---

## 4. Estructura de Petición API JSON-2

Las llamadas externas hacia Odoo 19 Online deben respetar el siguiente formato HTTP estándar:

* **Método:** `POST`
* **Endpoint / URL:** `https://<instancia>.odoo.com/json/2/<modelo>/<método>`
  * *Ejemplo de búsqueda de contactos:* `.../json/2/res.partner/search_read`
* **Cabeceras (Headers) Obligatorias:**
  * `Authorization: bearer <API_KEY>`
  * `Content-Type: application/json`
  * (Opcional, si hay multibase): `X-Odoo-Database: <nombre_bd>`

* **Estructura del Cuerpo (Body JSON):**  
  Debe estructurarse pasando los argumentos por nombre. A continuación, un ejemplo para consultar contactos que sean tipo empresa:
```json
{
  "domain": [["is_company", "=", true]],
  "fields": ["name", "email", "phone"],
  "limit": 10
}

<!-- Path: arquitectura_y_sistemas/11_controllers_odoo16_18_historico.md -->

# 🗄️ Manual Histórico: Controllers en Odoo (Versiones 16 a 18)

**Módulo / Área:** Arquitectura y Sistemas / Desarrollo  
**Clasificación:** HISTÓRICO / NO APLICABLE A PRODUCCIÓN (Odoo Online)  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual únicamente para fines de consulta conceptual, migración de módulos antiguos o despliegue en entornos locales/Docker, ya que detalla el uso de Controllers en Python, tecnología incompatible con Odoo 19 Online.

---

## ⚠️ Restricción Arquitectónica (Telcobras Odoo 19 Online)

En las versiones On-Premise o en Odoo.sh (versiones 16, 17 y 18), un desarrollador podía escribir clases de Python heredando de `http.Controller` para crear endpoints, webhooks o formularios públicos.

📌 **Política Telcobras:** Como operamos en **Odoo 19 Online (SaaS)**, está estrictamente prohibido y técnicamente bloqueado instalar módulos con código Python personalizado.
* Si un sistema externo necesita leer o escribir datos, se debe utilizar la **API JSON-2 nativa** (Ver manual `10_api_json2_odoo19.md`).
* Si se necesita recibir un webhook, se utilizará un orquestador externo (ej. Make.com) o las automatizaciones nativas de Odoo 19.

---

## 1. ¿Qué era un Controller en Odoo?

En el patrón MVC (Modelo-Vista-Controlador), las vistas (XML) no se muestran solas. Un *controller* es una clase de Python que define una ruta web (URL), recibe la petición HTTP, valida los parámetros y le indica al sistema qué vista HTML renderizar o qué JSON devolver.

**Flujo teórico correcto de un controller:**
1. **Recibe** la petición.
2. **Valida** los datos.
3. **Delega** la lógica de negocio al Modelo (el controller no debe contener reglas de negocio).
4. **Responde** con un HTML o un JSON.

---

## 2. Estructura de Rutas y Autenticación (Versiones Legacy)

La habilitación de URLs se hacía mediante el decorador `@http.route`. 

**Parámetros clave:**
* **`type`:** Definía si devolvía una plantilla (`'http'`) o un JSON para APIs (`'json'`). *Nota: En Odoo 19, las rutas personalizadas de tipo JSON fueron reemplazadas por `'jsonrpc'`.*
* **`auth`:** Nivel de seguridad.
  * `public`: Accesible sin sesión iniciada.
  * `user`: Exigía sesión activa en Odoo (de lo contrario, redirigía al login).

### 2.1. Ejemplo de Controller para renderizar HTML
```python
from odoo import http
from odoo.http import request

class EstateController(http.Controller):
    @http.route('/estate/list', type='http', auth='user')
    def estate_list(self, **kw):
        properties = request.env['estate.property'].search([])
        return request.render('mi_modulo.estate_list', {
            'properties': properties,
        })
```

### 2.2. Ejemplo de Controller API (Creación de registros)
Si se necesitaba que un tercero creara datos, se habilitaba un endpoint que devolvía respuestas JSON.

```python
class EstateApi(http.Controller):
    @http.route('/api/estate/create/<string:property_name>', type='http', auth='user')
    def estate_create_from_url(self, property_name, **kw):
        prop = request.env['estate.property'].create({'name': property_name})
        return request.make_json_response({'status': 'ok', 'id': prop.id})
```

---

## 3. Vulnerabilidades Comunes al Desarrollar Controllers

Si un consultor técnico propone desarrollar un Controller en un entorno de pruebas, se deben auditar los siguientes riesgos que el video original omitía:
1. **CSRF (Cross-Site Request Forgery):** Las peticiones POST tipo `'http'` exigían un token CSRF de seguridad, a menos que el desarrollador apagara imprudentemente la seguridad con `csrf=False`.
2. **Duplicación de registros (Falta de Idempotencia):** Al recibir un POST para crear un dato, si la red fallaba y el emisor reintentaba, el Controller generaba duplicados. Siempre se debía buscar el ID externo antes de crear uno nuevo.
3. **Ignorar reglas del modelo:** Modificar validaciones en el modelo solo para que un Controller de pruebas devuelva un status 200 corrompe la base de datos a largo plazo.

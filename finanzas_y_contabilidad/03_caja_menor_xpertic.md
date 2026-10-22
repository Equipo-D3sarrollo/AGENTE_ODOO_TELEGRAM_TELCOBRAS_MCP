<!-- Path: finanzas_y_contabilidad/03_caja_menor_xpertic.md -->

# MANUAL DE USO ÁREA CONTABLE
## Caja menor multiempresa: error de acceso y cómo evitarlo
Por qué se bloqueaba la caja menor de Xpertic, cómo se corrigió y qué cuidar al crear contactos y usuarios en Odoo[cite: 21].

* **Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 21]
* **Dirigido a:** Área Contable y administradores de Odoo[cite: 21]
* **Elaborado por:** Joshua Lores Florez[cite: 21]

---

## 1. ¿Qué pasaba?
En Odoo se manejan dos empresas a la vez: TELCOBRAS S.A.S. y XPERTIC[cite: 22]. La caja menor se lleva en Xpertic[cite: 22]. Al intentar abrir o conciliar la caja menor, Odoo mostraba este mensaje y no dejaba continuar[cite: 22]:

> **Error de acceso**[cite: 22]
> Estos registros están restringidos[cite: 22].
> Area Gerencia (id=2) no tiene acceso "leer" a:[cite: 22]
> - Contacto, MIGUEL ALFONSO RAMIREZ TORIJANO (res.partner: 478, company=TELCOBRAS S.A.S.)[cite: 22]
> Las siguientes reglas son responsables:[cite: 22]
> - res.partner company[cite: 22]
> Si necesitas acceso, pregúntale a un administrador[cite: 22].
> Parece que se trata de un problema multiempresa, quizá pueda acceder al registro si cambia a la empresa: TELCOBRAS S.A.S[cite: 22].

### Cómo leer el mensaje[cite: 22]
* **1. "Area Gerencia (id=2) no tiene acceso 'leer'...":** Es el usuario que intentó abrir la caja menor[cite: 22].
* **2. "Contacto, MIGUEL ALFONSO RAMIREZ TORIJANO (res.partner: 478, company=TELCOBRAS S.A.S.)":** Es el contacto que causa el bloqueo[cite: 22]. Está asignado solo a Telcobras[cite: 22].
* **3. "res.partner company":** La regla de Odoo que dice "un contacto de una empresa solo lo ve esa empresa"[cite: 22].
* **4. "Parece que se trata de un problema multiempresa...":** Confirma que el problema es la empresa asignada al contacto, no un daño en la caja menor[cite: 22].

La pista clave siempre es la línea 2: ahí aparece el nombre del contacto y la empresa a la que está atado[cite: 22]. Ese es el contacto que hay que corregir[cite: 22].

---

## 2. ¿Por qué pasa? (explicado simple)
Piense en Odoo como un edificio con dos oficinas: la oficina Telcobras y la oficina Xpertic[cite: 23]. Cada contacto (proveedor, empleado, tercero) tiene su "archivador"[cite: 23]. Si el archivador se guarda dentro de una oficina, solo esa oficina puede abrirlo[cite: 23]. Si se deja en el pasillo común (empresa vacía), las dos oficinas pueden abrirlo[cite: 23].

* **Así se bloqueaba:** La caja menor de Xpertic tiene un pago a Miguel, pero el archivador de Miguel está encerrado en Telcobras[cite: 23]. Xpertic no puede leerlo y, como la caja menor lo necesita, se bloquea toda la caja menor[cite: 23].
* **Así quedó corregido:** Al dejar el campo Empresa del contacto vacío, Miguel queda en el pasillo común[cite: 23]. Las dos empresas lo pueden leer y la caja menor abre sin error[cite: 23].

### Qué se corrigió[cite: 23]
| Caso | Qué tenía | Corrección |
| :--- | :--- | :--- |
| **Contacto Miriam** | Tenía acceso a la caja menor y su contacto estaba atado a una sola empresa. La caja menor de Xpertic quedaba bloqueada para los demás usuarios[cite: 23]. | Se dejó su campo Empresa vacío[cite: 23]. Desde entonces cualquier usuario registrado en Xpertic puede revisar la caja menor[cite: 23]. |
| **Contacto Miguel Alfonso Ramírez Torijano** | El error volvió a aparecer porque a este contacto se le asignó la empresa Telcobras (para temas de Flota)[cite: 23]. Xpertic le hace pagos por caja menor y no podía leerlo[cite: 23]. | Mismo arreglo: dejar su campo Empresa vacío (ver sección 3)[cite: 23]. |

**Importante:** Este error puede volver a pasar con cualquier contacto al que se le asigne una empresa[cite: 23]. No es un daño de la caja menor: es la configuración del contacto[cite: 23]. Por eso las reglas de la sección 4 son las que evitan que se repita[cite: 23].

---

## 3. Qué hacer si aparece el error

### A. Salida rápida (cualquier usuario)[cite: 24]
Sirve para seguir trabajando mientras un administrador corrige el contacto[cite: 24]. Solo funciona si su usuario tiene permiso en las dos empresas[cite: 24].
1. Arriba a la derecha de Odoo, abra el selector de empresas (donde aparece el nombre de la empresa actual)[cite: 24].
2. Marque las dos empresas: **TELCOBRAS S.A.S.** y **XPERTIC**[cite: 24].
3. Vuelva a abrir la caja menor[cite: 24].

*Ojo:* Con las dos empresas marcadas, verifique en qué empresa queda registrado cada movimiento nuevo antes de guardarlo[cite: 24].

### B. Solución definitiva (administrador de Odoo)[cite: 24]
1. Copie el nombre del contacto que aparece en el mensaje de error (línea 2). Ejemplo: `MIGUEL ALFONSO RAMIREZ TORIJANO`[cite: 24].
2. Vaya a **Contactos** y busque ese nombre[cite: 24]. Si no aparece, active las dos empresas en el selector (paso A)[cite: 24].
3. Abra el contacto y ubique el campo **Empresa** (normalmente en la pestaña *Ventas y compras*)[cite: 24].
4. Borre la empresa y deje el campo vacío[cite: 24]. Guarde[cite: 24].
5. Vuelva a abrir la caja menor desde Xpertic y confirme que ya no aparece el error[cite: 24].

Si el error muestra otro contacto, repita el procedimiento con ese contacto[cite: 24]. Puede haber varios contactos atados a una empresa[cite: 24]. El mensaje muestra uno a la vez[cite: 24].

---

## 4. Reglas para que no vuelva a pasar

| Cuando... | Haga esto... |
| :--- | :--- |
| **Crea un contacto nuevo**[cite: 25] | Deje el campo Empresa vacío[cite: 25]. Así lo pueden usar Telcobras y Xpertic[cite: 25]. Es la opción por defecto recomendada[cite: 25]. |
| **Alguien pide "asignarle la empresa" a un contacto**[cite: 25] | No lo haga sin revisar antes si ese contacto aparece en movimientos de la otra empresa (caja menor, pagos, gastos, flota)[cite: 25]. Si aparece, asignarle empresa bloqueará esos movimientos[cite: 25]. |
| **Un módulo (por ejemplo Flota) parece necesitar la empresa en el contacto**[cite: 25] | Consulte con soporte antes de asignarla[cite: 25]. Este fue justamente el origen del segundo bloqueo[cite: 25]. |
| **Crea o modifica un usuario de Odoo**[cite: 25] | Si la persona maneja caja menor o pagos de las dos empresas, dele acceso a las dos empresas permitidas (`TELCOBRAS S.A.S.` y `XPERTIC`)[cite: 25]. |
| **Aparece "Error de acceso... res.partner company"**[cite: 25] | Tome captura del mensaje y aplique la sección 3[cite: 25]. No borre ni modifique movimientos de la caja menor: el problema está en el contacto[cite: 25]. |

### Criterios de configuración de empresa
* **Empresa vacía cuando:**[cite: 25]
  * El contacto recibe pagos o aparece en movimientos de las dos empresas[cite: 25].
  * Es un empleado, conductor o proveedor compartido[cite: 25].
  * No está seguro: vacío es lo más seguro[cite: 25].
* **Asignar empresa solo si:**[cite: 25]
  * El contacto pertenece solo a una empresa y nunca va a aparecer en movimientos de la otra[cite: 25].
  * Hay una razón de confidencialidad para que la otra empresa no lo vea[cite: 25].

Tenga en cuenta que un contacto con empresa vacía lo pueden ver los usuarios de las dos empresas[cite: 25].

---

## 5. Usuarios actuales de Odoo
Hoy hay 6 usuarios internos y ya se usaron todos los asientos de la suscripción[cite: 26]. Para agregar un usuario nuevo hay que comprar otro asiento[cite: 26].

| Usuario | Función | Revisión |
| :--- | :--- | :--- |
| **Area Administrativa**[cite: 26] | Administrador[cite: 26] | Para cada usuario que maneje caja menor o pagos de Xpertic, confirme que en su ficha (pestaña *Permisos de acceso*) tenga las dos empresas permitidas: `TELCOBRAS S.A.S.` y `XPERTIC`[cite: 26]. |
| **Area Centro de Gestion**[cite: 26] | Miembro[cite: 26] | Confirmar acceso a ambas empresas si el usuario maneja caja menor o pagos de Xpertic[cite: 26]. |
| **Area Contabilidad**[cite: 26] | Administrador[cite: 26] | Confirmar acceso a ambas empresas si el usuario maneja caja menor o pagos de Xpertic[cite: 26]. |
| **Area Gerencia**[cite: 26] | Administrador[cite: 26] | Confirmar acceso a ambas empresas si el usuario maneja caja menor o pagos de Xpertic[cite: 26]. |
| **Area Operativa y Proyectos**[cite: 26] | Miembro[cite: 26] | Confirmar acceso a ambas empresas si el usuario maneja caja menor o pagos de Xpertic[cite: 26]. |
| **Claudia Diago**[cite: 26] | Administrador[cite: 26] | Confirmar acceso a ambas empresas si el usuario maneja caja menor o pagos de Xpertic[cite: 26]. |

---

## 6. Resumen en una línea
Si la caja menor de Xpertic se bloquea con "Error de acceso", busque el contacto que nombra el mensaje, déjele el campo Empresa vacío y vuelva a intentar[cite: 26]. Para que no se repita, no le asigne empresa a contactos que usan las dos compañías[cite: 26].

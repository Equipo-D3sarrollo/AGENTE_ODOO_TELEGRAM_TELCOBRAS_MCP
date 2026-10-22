<!-- Path: inventario_y_logistica/07_creacion_productos_nomenclatura.md -->

# Creación de Productos y Nomenclatura en Inventario (Odoo 19)

**Módulo Odoo:** Inventario / Compras[cite: 32]
**Rol Requerido:** Bodega, Compras, Área Operativa y Área Contable[cite: 32]
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 32]
**Elaborado por:** Joshua Lores Florez[cite: 32]
**Cuándo usar este documento:** Utiliza este manual cuando necesites crear un nuevo producto en Odoo, definir su referencia interna, asignarle la categoría correcta o consultar las reglas de nomenclatura y seguimiento de inventario[cite: 32, 33].

---

## 1. ¿Para qué sirve la referencia interna?
La referencia interna es el código corto con el que Odoo identifica cada producto (aparece entre corchetes delante del nombre, por ejemplo: `[TEC-EQ-036] MIKROTIK HAP LITE RB941`)[cite: 33]. Sirve para buscarlo, contarlo y reportarlo sin depender de cómo se haya escrito el nombre[cite: 33].

### Anatomía de un código
Un código se compone de tres partes (ejemplo: `TEC-EQ-036`)[cite: 33]:
1. **Rama:** (Ej. `TEC` técnico, `ADM` administrativo, `PRY` proyectos, `SRV` servicios)[cite: 33].
2. **Grupo:** Subcategoría (Ej. `EQ` equipos activos, `CAB` cableado, `ELE` eléctrico)[cite: 33].
3. **Consecutivo:** Tres dígitos que corresponden al siguiente número libre dentro de ese prefijo[cite: 33]. *(Nota: Los servicios y proyectos tienen un solo nivel, ej. `SRV-012`, `PRY-004`)*[cite: 33].

### Las Cinco Reglas de Oro de la Referencia Interna
1. **Una referencia, un producto:** Nunca existan dos productos con el mismo código[cite: 33].
2. **Nunca copiar el nombre en la referencia:** Está prohibido usar el nombre del producto como código[cite: 33].
3. **Nunca poner un serial o una MAC como referencia:** El serial identifica una unidad específica y se registra al recibir la mercancía; la referencia identifica el tipo de producto[cite: 33].
4. **Los números no se reutilizan:** Aunque un producto se archive, su número queda ocupado. El siguiente siempre será el mayor usado + 1[cite: 33].
5. **Hardware Nokia:** Conserva su código de parte original (ej. `4752668.102`). Solo si no lo posee se utiliza `NOK-###` (y `NOK-KIT-###` para kits armados por Telcobras)[cite: 33].

---

## 2. Tabla de Prefijos y Categorías
Cada prefijo va estrictamente vinculado a una categoría de Odoo[cite: 34].

### Material Técnico (`TEC-`)
| Prefijo | Categoría | Qué va (Incluye) | Qué NO va (Exclusiones) |
| :--- | :--- | :--- | :--- |
| **TEC-EQ-** | Equipos activos[cite: 34] | Equipos que se energizan y procesan tráfico: routers, Mikrotik, switches, radios, módems, ONT/OLT, conversores, firewalls, PLC, servidores[cite: 34]. | Antenas pasivas (`TEC-ANT`), fuentes y PoE (`TEC-ENE`), SFP y patch cords (`TEC-CAB`)[cite: 34]. |
| **TEC-ANT-** | Antenas y satélites[cite: 34] | Antenas sectoriales, parabólicas (*dish*), GPS y platos satelitales[cite: 34]. | Radios con antena integrada como PowerBeam o Rocket (`TEC-EQ`); soportes (`TEC-SOP`)[cite: 34]. |
| **TEC-CAB-** | Cableado y conectividad[cite: 34] | Cable UTP y fibra, patch cords, jumpers, conectores y plugs RJ45, jacks, faceplates, ODF, splitters, SFP[cite: 34]. | Cable eléctrico de potencia (`TEC-ELE`); cable de cobre desnudo para tierra (`TEC-SPT`)[cite: 34]. |
| **TEC-HER-** | Herramientas[cite: 34] | Herramientas manuales y eléctricas: alicates, llaves, destornilladores, ponchadoras, taladros, pinzas amperimétricas[cite: 34]. | Equipo de altura (`TEC-ALT`) y EPP (`TEC-EPP`)[cite: 34]. |
| **TEC-ALT-** | Equipo de altura[cite: 34] | Arneses, eslingas, mosquetones, frenos/descendedores, líneas de vida, escaleras[cite: 34]. | Cascos, guantes y EPP general (`TEC-EPP`); alquiler de andamios (`SRV`)[cite: 34]. |
| **TEC-CON-** | Consumibles técnicos[cite: 34] | Material de gasto en instalación: amarres, cintas, termoencogibles, tornillería, chazos, aerosoles, espuma, limpiadores, marquillas, etiquetas[cite: 34]. | Terminales y accesorios eléctricos (`TEC-ELE`); conectores RJ45 (`TEC-CAB`)[cite: 34]. |
| **TEC-ELE-** | Material eléctrico y canalización[cite: 34] | Tubería EMT/PVC, curvas, uniones, abrazaderas, conduletas, cajas, canaletas, coraza, breakers, tomas y tapas, terminales, cable eléctrico, tableros[cite: 34]. | Puesta a tierra y pararrayos (`TEC-SPT`)[cite: 34]. |
| **TEC-SPT-** | Puesta a tierra y pararrayos[cite: 34] | Varillas de cobre, soldadura exotérmica y moldes, puntas captoras, grapas, cemento conductivo, barrajes (*busbar*), aisladores, cable de cobre desnudo, cajas de inspección[cite: 34]. | Cable eléctrico aislado (`TEC-ELE`)[cite: 34]. |
| **TEC-SOP-** | Soportería y herrajes[cite: 35] | Soportes tipo bandera, bandejas portacable, riel, brazos, soportes de antena, racks, tensores, guaya, candados y señalética[cite: 35]. | Abrazaderas de tubería (`TEC-ELE`)[cite: 35]. |
| **TEC-ENE-** | Energía[cite: 35] | Adaptadores de corriente, inyectores PoE, UPS, inversores, regletas y protectores de sobretensión[cite: 35]. | Breakers y tacos (`TEC-ELE`)[cite: 35]. |
| **TEC-EPP-** | EPP y seguridad[cite: 35] | Botas, cascos, gafas, guantes, overoles, chalecos, caretas, extintores (la talla va en el nombre)[cite: 35]. | Arneses y eslingas (`TEC-ALT`)[cite: 35]. |

### Administrativo, Proyectos y Servicios
| Prefijo | Categoría | Qué va | Qué NO va |
| :--- | :--- | :--- | :--- |
| **ADM-COM-** | Equipo de cómputo[cite: 35] | Computadores, monitores, discos, lectoras y periféricos de oficina[cite: 35]. | Servidores y equipos de red (`TEC-EQ`)[cite: 35]. |
| **ADM-OFI-** | Insumos de oficina[cite: 35] | Papelería, útiles de oficina y bienestar[cite: 35]. | Cómputo (`ADM-COM`)[cite: 35]. |
| **ADM-FLT-** | Repuestos de vehículos[cite: 35] | Repuestos e insumos de vehículos propios: aceite, clutch, bombillos, filtros (ver Manual 6)[cite: 35]. | Mantenimiento de taller (`SRV` o servicios en flota)[cite: 35]. |
| **PRY-** | Materiales de proyecto[cite: 35] | Materiales comprados para un proyecto específico de cliente que no forman parte del stock técnico: acabados, luminarias, aire acondicionado, variadores[cite: 35]. | Material técnico de uso recurrente (`TEC-`)[cite: 35]. |
| **SRV-** | Servicios[cite: 35] | Todo lo que se vende o compra y no se almacena: canales dedicados, internet, instalación, mano de obra, diseño, soporte, alquileres, fletes, trámites[cite: 35]. | Ningún elemento físico[cite: 35]. |
| **NOKIA** | Hardware Nokia[cite: 35] | Hardware y accesorios del proyecto Nokia conservando su código de parte (ej. `4752668.102` o `NOK-###`)[cite: 35]. | Material propio de Telcobras aunque se use en sitios Nokia (`TEC-`)[cite: 35]. |

---

## 3. Configuración Técnica por Tipo de Producto
Cada prefijo exige una configuración específica en Odoo respecto al seguimiento y la unidad de medida[cite: 36, 37]:

| Prefijo | Rastrear inventario | Unidad de medida | Tipo de producto |
| :--- | :--- | :--- | :--- |
| **TEC-EQ-** / **TEC-ANT-** | Por número de serie único[cite: 36] | Unidades[cite: 36] | Bienes[cite: 36] |
| **TEC-CAB-** | Sin seguimiento (lote opcional si se controla el rollo)[cite: 36] | Metros (cable) / Unidades (conectores)[cite: 36] | Bienes[cite: 36] |
| **TEC-HER-** | Serie solo para herramientas costosas o prestadas; el resto sin seguimiento[cite: 36] | Unidades[cite: 36] | Bienes[cite: 36] |
| **TEC-ALT-** | Por número de serie (recomendado en arneses y eslingas por inspección y vencimiento)[cite: 36] | Unidades[cite: 36] | Bienes[cite: 36] |
| **TEC-CON-** / **TEC-ELE-** / **TEC-SPT-** / **TEC-SOP-** / **TEC-EPP-** | Sin seguimiento[cite: 36] | Unidades, paquetes o metros (según aplique)[cite: 36] | Bienes[cite: 36] |
| **TEC-ENE-** | Serie para UPS e inversores; el resto sin seguimiento[cite: 36] | Unidades[cite: 36] | Bienes[cite: 36] |
| **ADM-COM-** | Por número de serie único[cite: 36] | Unidades[cite: 36] | Bienes[cite: 36] |
| **ADM-OFI-** / **ADM-FLT-** / **PRY-** | Sin seguimiento[cite: 36] | Unidades, resmas o según proyecto[cite: 36, 37] | Bienes[cite: 36, 37] |
| **SRV-** | No aplica[cite: 37] | Unidad, Mbps o unidad de venta[cite: 37] | **Servicio** (sin inventario; no aparece en conteos ni bodega)[cite: 37] |
| **Código Nokia** | Por número de serie único[cite: 37] | Unidades[cite: 37] | Bienes[cite: 37] |

---

## 4. Guía de Decisión: ¿Qué prefijo elegir?
Responda en orden estricto; la primera respuesta afirmativa determina el prefijo[cite: 38]:

1. **¿Es algo que no se guarda en bodega?** (canal, internet, instalación, mano de obra, alquiler, flete, trámite) $\rightarrow$ **`SRV-`**[cite: 38]
2. **¿Es hardware o accesorio del proyecto Nokia?** $\rightarrow$ **Código de parte Nokia**[cite: 38]
3. **¿Se compra para un proyecto puntual de un cliente y no es material técnico habitual?** (acabados, luminarias, aire acondicionado) $\rightarrow$ **`PRY-`**[cite: 38]
4. **¿Es de oficina, cómputo del personal o repuesto de vehículo propio?** $\rightarrow$ **`ADM-COM-`**, **`ADM-OFI-`** o **`ADM-FLT-`**[cite: 38]
5. **Si nada de lo anterior aplica:** Es material técnico $\rightarrow$ Busque su grupo en la tabla de la sección 2 (`TEC-...`)[cite: 38].

### Desambiguación técnica frecuente
* **Radio con antena integrada** (PowerBeam, Rocket, Force): Es **`TEC-EQ-`** porque procesa tráfico y se energiza. La antena suelta es `TEC-ANT-`[cite: 38].
* **Cables:** UTP, fibra y patch cords van en **`TEC-CAB-`**. Cable de potencia va en **`TEC-ELE-`**. Cobre desnudo para puesta a tierra va en **`TEC-SPT-`**[cite: 38].
* **Inyector PoE o fuente:** Va en **`TEC-ENE-`**, aunque incluya cable[cite: 38].
* **Abrazaderas:** De tubería van en **`TEC-ELE-`**. De torre o antena van en **`TEC-SOP-`**[cite: 38].
* **Protección:** Trabajos en alturas van en **`TEC-ALT-`**; EPP general va en **`TEC-EPP-`**[cite: 38].

---

## 5. Procedimiento para Crear un Producto en Odoo

### Paso 1: Buscar antes de crear
En **Inventario** > **Productos** > **Productos**, busque por palabra clave y **active el filtro Archivado** para verificar si el producto ya existe (en revisiones previas se hallaron productos duplicados por omisión de este paso)[cite: 39].

### Paso 2: Llenar la ficha
Haga clic en **Nuevo** y diligencie los campos obligatorios[cite: 39]:
* **Nombre:** Formato estricto `$Tipo + marca + modelo + medida$`, en mayúsculas (ej. `ROUTER MIKROTIK HAP LITE RB941`, `TUBERIA METALICA EMT 1 PULGADA`, `BOTAS 40`). La talla o medida **siempre** va en el nombre[cite: 39].
* **Tipo de producto:** *Bienes* para todo lo físico; *Servicio* para los códigos `SRV-`[cite: 39].
* **Categoría:** Elija la subcategoría correcta. Nunca deje un producto en la raíz (*Material técnico*) ni sin categoría[cite: 39].
* **Referencia interna:** Déjela vacía si la automatización está activa (Odoo asignará la siguiente al guardar). Si no está automatizada, aplique el paso 3[cite: 39].
* **Rastrear inventario y Unidad de medida:** Configure según las tablas de la sección 3[cite: 39].

### Paso 3: Calcular el siguiente número (si no hay automatización)
1. Escriba el prefijo en la barra de búsqueda de productos (ej. `TEC-HER-`)[cite: 39].
2. Active el filtro **Archivado** (los códigos archivados cuentan como usados)[cite: 39].
3. Ordene la columna *Referencia interna* de mayor a menor. El siguiente número consecutivo será el primero de la lista + 1 (siempre a tres dígitos, ej. `TEC-HER-016`)[cite: 39].

---

## 6. Qué hacer si un producto no encaja en ninguna categoría
**No se inventa un prefijo por cuenta propia[cite: 40].** Quien detecta la necesidad deja el producto pendiente y avisa al responsable de inventario[cite: 40].

### Protocolo del Responsable de Inventario
1. Verifica si el ítem realmente no cabe en ninguna categoría actual (sección 4). Una categoría nueva solo se justifica si agrupará múltiples productos[cite: 40].
2. En **Inventario** > **Configuración** > **Categorías** > **Nuevo**, crea la subcategoría en la rama correcta (*Material técnico*, *Administrativo* o *Proyectos*)[cite: 40].
3. Asigna un prefijo único: rama + 2 o 3 letras + guion (ej. `TEC-FOP-`) en el campo *Prefijo* de la categoría[cite: 40].
4. Copia el método de costo y valoración de las categorías hermanas para salvaguardar la contabilidad[cite: 40].
5. Actualiza la documentación técnica correspondiente[cite: 40].

---

## 7. Errores Comunes Identificados en Auditoría
| Anomalía detectada | Causa principal | Acción correctiva |
| :--- | :--- | :--- |
| **Producto sin categoría o nombre copiado como referencia**[cite: 41] | La categoría no era obligatoria antes y se llenaron campos sin norma[cite: 41]. | Asignar subcategoría obligatoria al crear y usar estrictamente el esquema de prefijo + consecutivo[cite: 41]. |
| **Serial o MAC en la referencia interna**[cite: 41] | Creación errónea de una ficha por cada unidad física individual[cite: 41]. | Manejar un único producto con seguimiento por serie; el serial único se registra únicamente en la recepción[cite: 41]. |
| **Producto duplicado o fichas con `(copia)`**[cite: 41] | Duplicación de fichas sin cambiar nombre ni referencia[cite: 41]. | Crear siempre el producto desde cero o renombrar y corregir la referencia de inmediato[cite: 41]. |
| **Cable configurado en "Unidades"**[cite: 41] | Uso de la unidad por defecto del sistema[cite: 41]. | Configurar cables y alambres en **metros** (o kilogramos si se compran al peso)[cite: 41]. |
| **Existencias negativas**[cite: 41] | Entrega de material sin registrar su entrada previa en Odoo[cite: 41]. | Registrar formalmente la recepción de compras o ajustar mediante inventario físico[cite: 41]. |

---

## 8. Checklist de Validación al Crear un Producto
* [ ] Busqué el producto previamente (activando el filtro *Archivado*) y confirmé que no existe[cite: 41].
* [ ] El nombre cumple estrictamente el formato `$tipo + marca + modelo + medida$` en mayúsculas[cite: 41].
* [ ] El tipo de producto es *Bienes* (físico) o *Servicio* (`SRV-`)[cite: 41].
* [ ] La categoría asignada es una subcategoría correcta según el árbol jerárquico[cite: 41].
* [ ] La referencia interna empieza con el prefijo correcto y el número consecutivo es el siguiente libre[cite: 41].
* [ ] La referencia **no** contiene el nombre, un número de serie ni una dirección MAC[cite: 41].
* [ ] El seguimiento (serie, lote o sin seguimiento) y la unidad de medida coinciden con las especificaciones técnicas[cite: 41].
* [ ] Si es un componente Nokia, se utiliza su código de parte original[cite: 41].

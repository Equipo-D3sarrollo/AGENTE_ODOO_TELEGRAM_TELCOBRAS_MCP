<!-- Path: rrhh_y_proyectos/09_odoo_equipo_desarrollo.md -->

# Manual de Puesta en Marcha: Odoo para el Equipo de Desarrollo

**Módulo Odoo:** Múltiples (Proyecto, Hojas de horas, Planeación, Evaluaciones, Encuestas, Inventario)[cite: 63]
**Rol Requerido:** Integrantes del equipo de Desarrollo de software y líder de equipo[cite: 63]
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 63]
**Elaborado por:** Joshua Lores Florez (Versión 1.0)[cite: 63]
**Cuándo usar este documento:** Utiliza este manual para guiar la puesta en marcha y configuración de los módulos de trabajo en Odoo 19 directamente sobre el entorno de producción, utilizando los datos de prueba propios de cada integrante sin alterar registros de otras áreas[cite: 63, 64].

---

## 1. Reglas de Oro para Trabajar en el Odoo Real
Para evitar alteraciones indebidas en producción, se deben seguir estrictamente las siguientes reglas operativas[cite: 66]:
* **Buscar antes de crear:** Active siempre el filtro *Archivado* y marque ambas empresas en el selector antes de crear cualquier registro[cite: 66]. Si ya existe, utilícelo[cite: 66].
* **No cambiar lo que no es suyo:** No edite ni borre registros de otras áreas, de otras personas o creados antes de este manual[cite: 66].
* **Odoo guarda automáticamente:** Al salir de un formulario Odoo guarda los cambios automáticamente[cite: 66]. Si cometió un error, pulse *Descartar* (la X junto a la nube) antes de salir[cite: 66].
* **Nunca eliminar:** Los registros de prueba deben **archivarse** (*Archivar*), nunca eliminarse para no romper historiales ni movimientos contables[cite: 66].
* **Prefijo PRUEBA:** Todo elemento de prueba debe comenzar con la palabra `PRUEBA` en su nombre (ej. `PRUEBA SERVICIO DESARROLLO`)[cite: 66].
* **Empresa vacía:** En contactos, productos y plantillas donde el campo Empresa pueda dejarse vacío, déjelo **vacío** para evitar bloqueos multiempresa (Manual 3)[cite: 66].
* **Ajustes globales:** Las pantallas de configuración global se consultan, no se modifican, ya que afectan a toda la empresa[cite: 66].
* **Cero contabilidad:** No confirme facturas, pagos, asientos contables, cuentas, posiciones fiscales ni costos de productos[cite: 66].
* **Correos reales:** Acciones como enviar, publicar o confirmar envían correos electrónicos reales; asegúrese de dirigirlos únicamente a contactos etiquetados como *Equipo Desarrollo*[cite: 66].
* **Gestión de aplicaciones:** Nadie instala ni desinstala aplicaciones por cuenta propia[cite: 66].
* **Errores y contraseñas:** Ante cualquier error inesperado, deténgase y tome captura de pantalla[cite: 66]. Nunca registre contraseñas en textos, tareas o chats[cite: 66].

---

## 2. Ruta de Ejecución y Módulos
El trabajo se divide en módulos secuenciales y paralelos[cite: 64, 65]:
1. **Módulo P (Primeros pasos):** Movilidad y navegación en Odoo sin alterar datos por error[cite: 64, 69].
2. **Módulo A (Su ficha):** Revisión de contacto, ficha de empleado y usuario como datos de prueba[cite: 64, 72].
3. **Módulo B (Proyecto):** Creación de proyectos (*Desarrollo*, *Errores*, *Incidencias*), etapas, etiquetas y tareas[cite: 64, 76].
4. **Módulo C (Hojas de horas):** Registro de horas reales sobre tareas y validación por parte del líder[cite: 64, 80].
5. **Módulo D (Planeación):** Funciones, plantillas y publicación de turnos del equipo[cite: 64, 82].
6. **Módulo E (Evaluaciones):** Carga de objetivos mensuales, habilidades y ejecución de evaluaciones de prueba[cite: 64, 84].
7. **Módulo F (Encuestas):** Creación y prueba de encuestas de retroalimentación interna[cite: 64, 88].
8. **Módulo G (Inventario - en paralelo):** Revisión de ajustes, creación de productos de prueba (`PRUEBA-DEV-01`, `PRUEBA-DEV-02`) y operaciones sin impacto contable[cite: 64, 91].
9. **Módulo Z (Cierre):** Archivado de pruebas, verificación de empresas y firma de la lista de verificación[cite: 64, 94].

---

## 3. Resumen de Módulos y Responsabilidades

| Módulo | Qué queda listo | Responsable principal |
| :--- | :--- | :--- |
| **P. Primeros pasos**[cite: 64, 65] | Cada persona se mueve en Odoo y sabe cómo no cambiar nada por error[cite: 65]. | Cada uno[cite: 64, 65] |
| **A. Su ficha**[cite: 64, 65] | Contacto, ficha de empleado y usuario revisados; contacto sin empresa y con etiqueta *Equipo Desarrollo*[cite: 65]. | Cada uno[cite: 64, 65] |
| **B. Proyecto**[cite: 64, 65] | Proyectos *Desarrollo*, *Errores* e *Incidencias* con etapas, etiquetas, tareas y vistas[cite: 65]. | Responsable asignado[cite: 65] |
| **C. Hojas de horas**[cite: 64, 65] | Cada integrante registra horas sobre sus tareas y el líder las valida[cite: 65]. | Responsable asignado[cite: 65] |
| **D. Planeación**[cite: 64, 65] | Funciones y plantillas del equipo; turnos publicados[cite: 65]. | Responsable asignado[cite: 65] |
| **E. Evaluaciones**[cite: 64, 65] | Plantilla probada, objetivos del mes y evaluación completa de prueba[cite: 65]. | Líder + Responsable[cite: 65] |
| **F. Encuestas**[cite: 64, 65] | Encuesta de retroalimentación probada dentro del equipo[cite: 65]. | Responsable asignado[cite: 65] |
| **G. Inventario**[cite: 64, 65] | Ajustes revisados, productos de prueba y operación sin efecto contable[cite: 65]. | Responsable asignado[cite: 65] |
| **Z. Cierre**[cite: 64, 65] | Pruebas archivadas, empresas verificadas y lista de verificación firmada[cite: 65]. | Líder[cite: 64, 65] |

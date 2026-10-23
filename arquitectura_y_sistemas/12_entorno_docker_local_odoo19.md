<!-- Path: arquitectura_y_sistemas/12_entorno_docker_local_odoo19.md -->

# 🐳 Manual Operativo 12: Despliegue de Entorno Local de Pruebas con Docker (Odoo 19)

**Módulo / Área:** Arquitectura y Sistemas / Infraestructura y Desarrollo  
**Clasificación:** SOPORTE LOCAL / ENTORNO DE PRUEBAS (No aplicable a producción SaaS)  
**Empresa / Proyecto:** Telcobras S.A.S.  
**Cuándo usar este documento:** Utilice este manual para configurar y levantar un entorno local de pruebas o desarrollo de Odoo 19 en equipos de cómputo (Windows, macOS o Linux) mediante Docker y Docker Compose, permitiendo realizar pruebas controladas sin afectar la instancia en la nube de Telcobras[cite: 24].

---

## ⚠️ Advertencia Operativa (Entorno Local vs. Producción)

* **Restricción SaaS:** Telcobras opera exclusivamente sobre **Odoo 19 Online (SaaS)**[cite: 3], por lo que este procedimiento con Docker **no** se utiliza para entornos de producción[cite: 24].
* **Propósito del Entorno Local:** Este despliegue con Docker se restringe a tareas de pruebas, validación de scripts de integración, experimentación con la API JSON-2 y capacitación del equipo técnico[cite: 24]. Al tratarse de una versión *Community* local, características avanzadas de la edición *Enterprise* de Telcobras (como la IA nativa y nómina avanzada) no están disponibles[cite: 24].

---

## 1. Requisitos Previos e Instalación

Para desplegar el entorno local, el equipo de desarrollo debe contar con las herramientas base instaladas en el sistema operativo (Windows, macOS o Linux)[cite: 24]:
1. **Docker Desktop** (o Docker Engine con el plugin de Docker Compose en Linux)[cite: 24].
2. **Visual Studio Code** (recomendado para la gestión de archivos y contenedores)[cite: 24].

Verificación de comandos en terminal:
    docker --version
    docker compose version

---

## 2. Estructura de Directorios del Proyecto

1. Cree una carpeta de trabajo en el equipo local (ej. `odoo19-docker`)[cite: 24].
2. Dentro de ella, cree la subcarpeta **`custom-addons`** para almacenar los módulos personalizados o de terceros[cite: 24].
3. Cree el archivo principal de orquestación llamado **`compose.yml`**[cite: 24].

La estructura final debe verse así[cite: 24]:
    odoo19-docker/
    ├── compose.yml
    └── custom-addons/        # Directorio para módulos externos o de desarrollo propio

---

## 3. Plantilla de Referencia para `compose.yml`

A continuación se presenta la estructura estándar de configuración para levantar Odoo 19 junto a su base de datos PostgreSQL 17 garantizando la persistencia de datos mediante volúmenes con nombre[cite: 24]:

    services:
      db:
        image: postgres:17
        environment:
          POSTGRES_DB: postgres
          POSTGRES_USER: odoo
          POSTGRES_PASSWORD: ${DB_PASSWORD:-telcobras_local_pass}
        volumes:
          - db-data:/var/lib/postgresql/data
        restart: unless-stopped

      odoo:
        image: odoo:19.0
        depends_on:
          - db
        ports:
          - "8069:8069"
        environment:
          HOST: db
          USER: odoo
          PASSWORD: ${DB_PASSWORD:-telcobras_local_pass}
        volumes:
          - odoo-data:/var/lib/odoo          # Persistencia del filestore y sesiones
          - ./custom-addons:/mnt/extra-addons # Directorio de módulos personalizados
        restart: unless-stopped

    volumes:
      db-data:
      odoo-data:

---

## 4. Comandos Operativos de Gestión (Docker Compose)

Ubíquese en la terminal dentro de la carpeta del proyecto para ejecutar las operaciones del ciclo de vida de los contenedores[cite: 24]:

| Acción | Comando | Descripción |
|---|---|---|
| **Arrancar contenedores (Segundo plano)** | `docker compose up -d` | Inicia la base de datos y Odoo liberando la terminal[cite: 24]. |
| **Arrancar viendo registros (Logs)** | `docker compose up` | Muestra en tiempo real la salida de consola de ambos servicios (Ctrl + C para salir)[cite: 24]. |
| **Monitorear logs en vivo** | `docker compose logs -f` | Sigue el flujo de actividad de los contenedores activos[cite: 24]. |
| **Detener contenedores** | `docker compose down` | Apaga el servicio conservando los datos en los volúmenes[cite: 24]. |
| **Actualizar imagen oficial** | `docker compose pull` | Descarga las últimas correcciones publicadas para la versión 19.0[cite: 24]. |

---

## 5. Validación y Puesta en Marcha

1. Una vez ejecutado el arranque, abra el navegador web e ingrese a: `http://localhost:8069`[cite: 24].
2. Complete el formulario inicial para crear una base de datos de pruebas (asegúrese de marcar los datos de demostración si lo requiere para prácticas)[cite: 24].
3. Diríjase a **Ajustes** en la parte inferior de la interfaz para verificar que el sistema reporta la versión **Odoo 19 Community**[cite: 24].
4. **Instalación de módulos en desarrollo:** Coloque cualquier carpeta de módulo dentro de `custom-addons/`, ejecute `docker compose restart odoo`, active el *Modo Desarrollador* en Odoo y pulse en **Apps > Actualizar lista de aplicaciones** para habilitarlo[cite: 24].

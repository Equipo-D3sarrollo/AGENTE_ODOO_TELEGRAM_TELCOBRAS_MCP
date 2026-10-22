<!-- Path: arquitectura_y_sistemas/03_evaluacion_hosting_cloud.md -->

# Evaluación de Hosting y Plataformas Cloud para Telcobras

**Módulo / Área:** Infraestructura / Arquitectura Cloud[cite: 49, 50]  
**Rol Requerido:** Equipo de Desarrollo y Dirección Técnica[cite: 50]  
**Empresa:** Telcobras S.A.S. NIT 900.281.488-1[cite: 50]  
**Cuándo usar este documento:** Utilice este manual para conocer la evaluación comparativa de proveedores de hosting y nube (Latinoamérica Hosting, Hostinger, DigitalOcean, AWS, Azure, Google Cloud), los costos de referencia y la estrategia de adopción y migración gradual de Telcobras[cite: 50, 56].

---

## 1. Resumen Ejecutivo
Telcobras dispone actualmente del plan H4 de Latinoamérica Hosting por \$450.000 COP al año para hosting web tradicional[cite: 50]. Para soportar nuevas aplicaciones, integraciones y bases de datos, se evalúa adoptar alternativas modernas como **DigitalOcean App Platform** mediante una prueba piloto, manteniendo una estrategia de portabilidad hacia AWS, Azure o Google Cloud sin dependencias exclusivas de un solo proveedor[cite: 50].

---

## 2. Cuadro Comparativo de Precios de Referencia
*Valores de referencia publicados por los proveedores en octubre de 2026[cite: 50, 51].*

| Proveedor / Servicio | Precio de Referencia | Qué Ofrece | Principal Beneficio |
| :--- | :--- | :--- | :--- |
| **Latinoamérica Hosting H4**[cite: 51] | \$450.000 COP/año[cite: 51] | Hosting web, correo, bases de datos y cPanel[cite: 51] | Bajo costo y continuidad[cite: 51] |
| **Hostinger VPS KVM 2**[cite: 51] | \$610.800 COP/año (equivalente anualizado a la tarifa de renovación)[cite: 51] | VPS con 2 vCPU, 8 GB RAM, 100 GB NVMe y 8 TB transferencia[cite: 51] | Control del servidor y flexibilidad[cite: 51] |
| **DigitalOcean App Platform**[cite: 51] | Desde US\$5/mes por componente (~US\$60/año base)[cite: 51] | Despliegue administrado de aplicaciones y contenedores[cite: 51] | Menos administración de infraestructura[cite: 51] |
| **AWS Lightsail**[cite: 51] | Desde US\$5/mes por VPS básico (~US\$60/año)[cite: 51] | Servidor virtual, almacenamiento y servicios complementarios[cite: 51] | Entrada sencilla al ecosistema AWS[cite: 51] |
| **Microsoft Azure App Service**[cite: 51] | Plan Windows B1: US\$54,75/mes (~US\$657/año)[cite: 51] | Plataforma administrada para apps web y APIs[cite: 51] | Integración con herramientas Microsoft[cite: 51] |
| **Google Cloud Run**[cite: 51] | Pago por consumo (aplica nivel gratuito según consumo)[cite: 51] | Ejecución administrada de aplicaciones y contenedores[cite: 51] | Escalado según demanda y facturación por consumo[cite: 51] |

---

## 3. Análisis Detallado por Alternativa

### 3.1 Latinoamérica Hosting H4
* **Tipo:** Hosting web tradicional con cPanel[cite: 52].
* **Ventajas:** Costo anual reducido, administración sencilla, servicio actual de la empresa[cite: 52].
* **Limitaciones:** Menor control del entorno de ejecución; acceso a Docker y nivel de sistema operativo no confirmados[cite: 52].
* **Papel propuesto:** Mantener sitios y servicios compatibles mientras se evalúan nuevas necesidades[cite: 52].

### 3.2 Hostinger VPS KVM 2
* **Tipo:** Servidor virtual privado[cite: 52].
* **Ventajas:** Flexibilidad para varios proyectos en una máquina, control total del SO[cite: 52].
* **Limitaciones:** La empresa asume la responsabilidad de seguridad, actualizaciones, respaldos y monitoreo[cite: 52].
* **Papel propuesto:** Alternativa económica si se cuenta con capacidad técnica interna de administración[cite: 52].

### 3.3 DigitalOcean App Platform
* **Tipo:** Plataforma administrada de aplicaciones (PaaS)[cite: 53].
* **Ventajas:** Despliegue integrado con Git, menor gestión de SO, bases de datos administradas y escalabilidad por componentes[cite: 53].
* **Limitaciones:** Costo incremental por componentes adicionales; la seguridad del código y datos sigue a cargo de la empresa[cite: 53].
* **Papel propuesto:** Alternativa inicial recomendada para pruebas de modernización[cite: 53].

### 3.4 AWS Lightsail / Azure App Service / Google Cloud Run
* **AWS Lightsail:** Entrada sencilla al ecosistema AWS para proyectos futuros[cite: 53].
* **Azure App Service:** Ideal para desarrollos integrados con tecnologías empresariales de Microsoft[cite: 53, 54].
* **Google Cloud Run:** Excelente para arquitecturas basadas en contenedores con pago por consumo estricto[cite: 54].

---

## 4. Estrategia Propuesta para Telcobras
1. **Fase de Evaluación y Piloto:** Seleccionar un proyecto representativo, calcular sus requerimientos de recursos y desplegarlo en *DigitalOcean App Platform* para medir costos reales y operación[cite: 55].
2. **Modernización de Proyectos:** Gestionar código en Git, automatizar despliegues, documentar variables de entorno, mantener respaldos exportables y evitar dependencias propietarias de un solo proveedor[cite: 55].
3. **Crecimiento y Migración Futura:** Trasladar servicios específicos a AWS, Azure o Google Cloud según las necesidades técnicas y comerciales lo requieran[cite: 55].

---

## 5. Recomendación Final
Se recomienda **aprobar la fase de evaluación técnica y económica de DigitalOcean App Platform** mediante una prueba piloto, manteniendo el plan H4 para servicios compatibles y considerando Hostinger VPS como opción de menor costo si se administra internamente[cite: 56]. AWS, Azure y Google Cloud quedan reservadas como opciones de escala futura[cite: 56].

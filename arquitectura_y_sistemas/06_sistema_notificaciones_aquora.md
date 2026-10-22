<!-- Path: arquitectura_y_sistemas/06_sistema_notificaciones_aquora.md -->

# Informe Técnico: Sistema de Notificaciones Aquora (Monitoreo PTAP)

**Módulo / Área:** Operaciones, Monitoreo PTAP y Arquitectura de Sistemas[cite: 136]  
**Rol Requerido:** Administradores, Equipo de Desarrollo y Dirección Técnica[cite: 137]  
**Empresa / Proyecto:** Telcobras S.A.S. / Xpertic (Aquora v1.3.0 en producción, 7 de septiembre de 2026)[cite: 136, 177]  
**Cuándo usar este documento:** Utilice este informe técnico para comprender la arquitectura del subsistema de notificaciones de Aquora, su catálogo de avisos, el histórico de incidencias de producción, principios de diseño y la viabilidad de integración de Inteligencia Artificial (IA) en niveles controlados[cite: 137].

---

## 1. Resumen y Funcionamiento Actual (Parte I)

El subsistema de notificaciones entró en producción el 6 de agosto de 2026[cite: 137]. Opera mediante detectores en el servidor que generan alertas persistentes acotadas a la planta de cada cuenta[cite: 137].

### 1.1 La Tubería de un Aviso
El flujo consta de seis etapas principales y decisiones de diseño fundamentales[cite: 138]:
1. **Antigüedad basada en el reloj del PLC:** Se mide con el tiempo del equipo de campo y no con el servidor para evitar arranques erróneos tras reinicios[cite: 138].
2. **Independencia de los detectores:** Leen la caché en memoria y cuentan con manejo de errores aislado por ciclo[cite: 138].
3. **Deduplicación por base de datos:** Se gestiona mediante un índice único en MySQL (`tipo:planta:señal:gravedad:día`)[cite: 138].
4. **Gravedad incluida en la clave:** Permite que un empeoramiento de la condición vuelva a generar aviso el mismo día[cite: 138, 145].
5. **Día local (Colombia):** Evita cambios de clave masivos en horario nocturno universal[cite: 138].
6. **Filtrado vs. Silenciamiento:** El servidor entrega toda la información de la planta; las preferencias del usuario solo determinan qué sonido se emite en el dispositivo[cite: 138].

### 1.2 Catálogo de Avisos (7 Tipos)
| Aviso | Umbral Principal | Gravedad | Propósito / Acción |
| :--- | :--- | :--- | :--- |
| **Sensor sin refrescar**[cite: 139] | 1 h (crítico a partir de 24 h)[cite: 139] | Aviso / Crítico[cite: 139] | Invalida valores en pantalla y avisa al administrador[cite: 139]. |
| **Señal fuera de rango**[cite: 139] | Según mapeo físico u operativo[cite: 139] | Crítico (señal) / Aviso (físico)[cite: 139] | Informa anomalías operativas o errores instrumentales[cite: 139]. |
| **Nivel de tanque**[cite: 139] | 3% sobre máximo, 1 cm tendencia, 0.1 l/s caudal[cite: 139] | Crítico o Aviso según veredicto[cite: 139] | 5 veredictos (bajo cayendo, rebosando, mal medido, recuperando, indeterminado)[cite: 139, 140]. |
| **Autonomía de tanque**[cite: 140] | 50% o $\le$1 h al ritmo actual[cite: 140] | Crítico ($\le$1 h) / Aviso[cite: 140] | Proyecta la disponibilidad de agua en el tanque[cite: 140]. |
| **Maniobra de válvula**[cite: 140] | Evento de apertura/cierre[cite: 140] | Aviso o Informativo[cite: 140] | Registra el estado de la orden (rechazada, confirmada, a ciegas)[cite: 140]. |
| **Válvula movida a mano**[cite: 140] | Caudal $>$0.1 l/s sin orden previa (ventana 30 min)[cite: 140] | Aviso[cite: 140] | Se declara de forma probable ante manipulación física[cite: 140]. |
| **Libro de firmas roto**[cite: 140] | Barrido cada 6 h[cite: 140] | Crítico siempre[cite: 140] | Detecta alteraciones en la cadena de firmas de maniobras[cite: 140]. |

---

## 2. Capas del Sistema y Preferencias (Parte I)

* **Bandeja:** Historial persistente de 30 días alojado en la base de datos del servidor[cite: 141]. No se puede borrar[cite: 141].
* **Panel del sistema:** Notificaciones push locales generadas por tareas de fondo en dispositivos Android cada 15 minutos (entrega "lo mejor posible")[cite: 143].
* **Avisos en pantalla / Novedades:** Mensajes efímeros o registro estático de versiones del repositorio[cite: 141].
* **Preferencias de Usuario:** Las reglas de silencio operan como lista negra[cite: 142]. Las válvulas y el libro de firmas roto **nunca se pueden silenciar** por motivos de auditoría y seguridad operativa[cite: 142].

---

## 3. Principios y Lecciones de Producción (Parte II)

El sistema evolucionó a partir de múltiples incidencias de producción[cite: 144]. Sus cinco principios rectores son:
1. **La salud se mide al final de la cadena:** Vigilar solo la entrada no garantiza el resultado final para el usuario[cite: 147].
2. **Un aviso que miente deslegitima el sistema:** Cualquier inexactitud destruye la confianza del operador[cite: 147].
3. **El ruido operativo es un riesgo de seguridad:** Demasiadas alertas provocan que se ignoren todas[cite: 147].
4. **Apuntar a la causa correcta:** No se generan alarmas sobre datos obsoletos ni se confunden fallos de comunicación con variables de proceso[cite: 147].
5. **El historial es evidencia:** No existen funciones de borrado de avisos; las listas de exclusión actúan como filtros de sonido y no de almacenamiento[cite: 147].

---

## 4. Viabilidad y Propuesta de Integración de IA (Parte III)

La IA se ubica **estrictamente al lado de la tubería de avisos, nunca dentro**, garantizando que la emisión de alertas siga siendo determinista[cite: 151]. Se proponen tres niveles de implementación[cite: 152, 153]:

| Nivel de IA | Descripción y Propósito | Riesgo | Costo Estimado |
| :--- | :--- | :--- | :--- |
| **Nivel 1: Parte de turno**[cite: 152] | Tarjeta resumen superior con los eventos del turno agrupados por causa probable[cite: 152]. | Nulo (aditivo)[cite: 152] | 8 - 12 USD / mes[cite: 154] |
| **Nivel 2: Informe semanal**[cite: 152] | Análisis batch del corpus de avisos, silencios y tiempos de lectura para proponer mejoras en umbrales[cite: 152]. | Bajo (fuera de línea)[cite: 152] | $\sim$0.20 USD / mes[cite: 154] |
| **Nivel 3: Redacción asistida**[cite: 153] | Asistencia de modelo para redactar textos del catálogo planta por planta (cero IA en ejecución)[cite: 153]. | Bajo (constantes estáticas)[cite: 153] | Coste único de horas[cite: 154] |

### Recomendación Estratégica
Implementar la **Fase A (Informe semanal - Nivel 2)** antes de la semana de octubre enfocada en alarmas configurables, permitiendo ajustar los umbrales basándose en 6 semanas de datos reales de producción[cite: 177].

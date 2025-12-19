# **Ejercicio final - Monitoreo integral de infraestructura**

**Objetivo:** Integrar todos los conceptos aprendidos a lo largo del workshop para configurar y verificar un monitoreo completo de una infraestructura real, aplicando buenas prácticas y demostrando la capacidad de analizar problemas.

---

## **📋 Descripción del escenario**

**Contexto empresarial:**

Una empresa necesita implementar monitoreo integral para su infraestructura crítica de servicios web. La infraestructura está compuesta por:

1. **Servidor Web (SRV-Prod-WebServer)**:

   - Servicio desplegado sobre **Oracle Cloud Infrastructure**.
   - Sistema operativo **Linux** con **Nginx** como servidor web.
   - Página web corporativa accesible públicamente.
   - **Método de monitoreo**: con agente (tiene preconfigurado **Zabbix Agent** con el puerto por defecto).
   - **DNS**: `web.conatel-lab.conatel.cloud`

2. **Switch Core (SW-Prod-Core1)**:

   - Equipo: **Cisco Nexus 9000** Series Switches con sistema operativo **Cisco NX-OS**.
   - **Función**: Switch principal (Core) que conecta el servidor web a la red corporativa.
   - **Rol en la arquitectura**: Interconecta el servidor web con el switch edge y la red interna.
   - **Método de monitoreo**: sin agente (Agent-Less) con protocolo SNMP (tiene preconfigurado **SNMPv2** con el puerto por defecto).
   - **IP**: `10.0.10.1`.

3. **Switch Edge (SW-Prod-Edge1)**:

   - Equipo: **Cisco Nexus 9000** Series Switches con sistema operativo **Cisco NX-OS**.
   - **Función**: Switch perimetral (Edge) que conecta la infraestructura interna con Internet.
   - **Rol en la arquitectura**: Primer punto de entrada desde Internet hacia la red corporativa.
   - **Método de monitoreo**: sin agente (Agent-Less) con protocolo SNMP (tiene preconfigurado **SNMPv2** con el puerto por defecto).
   - **IP**: `10.0.10.2`.

**Arquitectura de red:**

La infraestructura sigue una topología simple donde:

- **SW-Prod-Edge1** (Edge Switch): Es el switch perimetral que conecta la infraestructura con Internet.
- **SW-Prod-Core1** (Core Switch): Es el switch principal que conecta el servidor web con el resto de la red.
- **SRV-Prod-WebServer**: El servidor web que aloja la página corporativa accesible desde Internet.

**Diagrama de topología:**

```
Internet
   ↓
[Switch SW-Prod-Edge1] ←→ [Switch SW-Prod-Core1] ←→ [Servidor Web SRV-Prod-WebServer]
                                                              ↓
                                                      Nginx (Puerto 80)
```

**Requisitos del monitoreo:**

- Detectar problemas de disponibilidad de la página web.
- Monitorear la conectividad de red (switches).
- Alertar sobre problemas críticos en tiempo real.
- Organizar la infraestructura de forma lógica.
- Aplicar buenas prácticas de configuración.

---

## **0. Preparación inicial: Desactivar hosts de ejercicios anteriores**

**Objetivo**: Desactivar los hosts creados en ejercicios anteriores para evitar confusiones y mantener un entorno limpio.

**Tareas:**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>
2. Buscar y desactivar los siguientes hosts configurados en ejercicios anteriores:
   - `SRV-Test`
   - `SW-Demo1`
   - `SW-Demo2`
   - `SW-Demo3`
   - `SRV-Demo-Web-Server`
3. Para cada host:
   - Seleccionar el host
   - Hacer clic en <span style="color: blue;"><strong>Disable</strong></span> o editar el host y cambiar el **Status** a `Disabled`
   - <span style="color: blue;"><strong>Update</strong></span>

> **💡 Nota**: Esto desactivará los hosts de ejercicios anteriores para que no interfieran con el ejercicio final. Los hosts desactivados no serán monitoreados pero permanecerán en la base de datos.

---

## **1. Revisión y organización de infraestructura existente**

**Objetivo**: Revisar y preparar los hosts para el ejercicio final, asegurando que sigan las mejores prácticas de configuración.

**Tareas:**

- Crear los hosts pertinentes desde cero.
- **Aplicar templates estándar siguiendo las mejores prácticas**:
  - Asegurar que los items y discovery rules estén dentro de templates en lugar de estar configurados directamente en los hosts.
  - Esto facilita el mantenimiento, la reutilización y la estandarización de la configuración.
- Verificar que los hosts sigan las mejores prácticas de configuración.

> **💡 Nota**: Para crear los hosts, se puede seguir las pŕacticas que se realizaron en los ejercicios [ejercicio integrador](ejercicio-integrador.md), [ejercicio 8.4](ejercicio-8.4.md) o [ejercicio 9.8](ejercicio-9.8.md).

---

## **2. Organización de grupos y aplicación de buenas prácticas**

**Objetivo**: Reorganizar los hosts en una estructura lógica jerárquica que refleje la arquitectura real de la infraestructura.

**Tareas:**

- Crear una **estructura jerárquica de grupos de hosts**:
  - Grupo principal para la infraestructura
  - Subgrupos para diferentes tipos de componentes (Web Servers, Network Devices)
- Mover los hosts existentes a los grupos apropiados
- **Aplicar tags** consistentes a los **hosts** para facilitar el filtrado y la organización
- Verificar que la organización facilite la gestión y asignación de permisos

**Estructura sugerida de grupos:**

```
Infraestructura
  ├── Web Servers
  │     └── SRV-Prod-WebServer
  └── Network Devices
        ├── SW-Prod-Core1
        └── SW-Prod-Edge1
```

> Siguiendo las pŕacticas que se realizaron en [ejercicio 9.8](ejercicio-9.8.md).

**Tags sugeridos para aplicar:**

- `component` *(ej: `web-server,router,switch`)*
- `environment` *(ej: `production,development,testing`)*
- `os` *(ej: `linux,windows,unix,cisco ios,cisco nexus`)*
- `vendor` *(ej: `cisco,juniper,arista,huawei`)*
- `model` *(ej: `nexus-9000,juniper-srx,arista-sw,huawei-s5700`)*

---

## **3. Verificación y mejora de triggers existentes**

**Objetivo**: Revisar los triggers configurados en los templates y asegurar que estén correctamente organizados y optimizados.

**Tareas:**

- **Completar los triggers de las severidades (Warning, Average, High) y dependencias** para estos tipos de problemas:
  - **ICMP Ping**:
    - **Warning**: `last(...)=0`
    - **Average**: `last(...#2)=0`
    - **High**: `max(...#3)=0`
    - **Dependencias**: (Warning → Average → High)

  - **Memoria**:
    - **Warning**: `min(...,15m)>{$MEMORY.UTIL.WAR}` → Valor 75
    - **Average**: `min(...,15m)>{$MEMORY.UTIL.AVG}` → Valor 85
    - **High**: `min(...,10m)>{$MEMORY.UTIL.HIGH}` → Valor 95
    - **Dependencias**: (Warning → Average → High)

  - **CPU**:
    - **Warning**: `min(...,15m)>{$CPU.UTIL.WAR}` → Valor 50
    - **Average**: `min(...,10m)>{$CPU.UTIL.AVG}` → Valor 75
    - **High**: `min(...,10m)>{$CPU.UTIL.HIGH}` → Valor 90
    - **Dependencias**: (Warning → Average → High)

  - **Interfaces**:
    - **Warning**: Para estado 3 (testing) → `(last(...)=3 and last(...,#1)<>last(...,#2))` → recovery `last(...)<>3`
    - **High**: Para estado 2 (down) → `(last(...)=2 and last(...,#1)<>last(...,#2))` → recovery `last(...)<>2`
    - **Dependencias**: (Warning → High)

- Agregar tags a los triggers para mejor categorización (`scope: availability`, `scope: performance`, `scope: capacity`).
- Verificar que los triggers tengan descripciones claras y útiles.

> **💡 Nota**: Para revisar los triggers, se puede seguir las pŕacticas que se realizaron en los ejercicios [ejercicio 6.4](ejercicio-6.4.md), [ejercicio 8.4](ejercicio-8.4.md) o [ejercicio 9.8](ejercicio-9.8.md).

---

## **4. Verificación y creación de notificaciones y acciones**

**Objetivo**: Asegurar que el flujo completo de alertas y notificaciones esté funcionando correctamente, y crear acciones adicionales con diferentes condiciones utilizando los grupos, severidades y tags configurados.

**Tareas:**

- Verificar que la acción configurada en [ejercicio 7.3](ejercicio-7.3.md) esté activa y funcionando.
- Actualizar las condiciones de la acción existente si es necesario para reflejar los nuevos grupos de hosts (`Infraestructura` o sus subgrupos).
- **Agregar permisos de grupos de hosts al grupo de usuarios del usuario "Notificaciones"** para que las acciones puedan funcionar correctamente con los nuevos grupos creados.
- **Crear 3 acciones nuevas** con diferentes condiciones utilizando:
  - **Grupos de hosts** (Infraestructura/Web Servers, Infraestructura/Network Devices)
  - **Severidades** (Warning, Average, High)
  - **Tags** (component, scope, environment, etc.)
- Confirmar que el usuario "Notificaciones" esté correctamente configurado con sus grupos, roles y permisos de grupos de hosts.
- Probar el flujo completo: **Item → Trigger → Problema/Evento → Acción → Operaciones (Notificaciones/Comandos/Scripts)**.

> **💡 Nota**: Al finalizar esta sección, deberías tener **4 acciones en total**: 1 acción actualizada (la del ejercicio 7.3) + 3 acciones nuevas.

**Acciones a crear:**

1. **Acción para alertas críticas en servidores web**: Condiciones usando grupo `Infraestructura/Web Servers` y severidad `High` o superior.
2. **Acción para problemas de capacidad y rendimiento**: Condiciones usando tags (`scope: capacity` o `scope: performance`) y severidad `Average` o superior.
3. **Acción para dispositivos de red**: Condiciones usando grupo `Infraestructura/Network Devices` y tags (`component: network-switch`).

---

## **5. Configuración de período de mantenimiento (OPCIONAL)**

**Objetivo**: Aprender a configurar períodos de mantenimiento para suprimir alertas durante mantenimientos planificados.

**Tareas:**

- Crear un período de mantenimiento para el servidor web, ya sea por Host o por el Host group al cual pertenece.
- Configurar fecha/hora de inicio y fin del mantenimiento.
- Verificar que las alertas se supriman durante el período de mantenimiento.
- Cancelar el mantenimiento antes de que termine y verificar que las alertas vuelvan a funcionar normalmente.

> **💡 Nota**: Esta sección es opcional y puede omitirse si el tiempo es limitado.

---

## **6. Análisis y verificación integral**

**Objetivo**: Realizar una verificación completa de que todo el monitoreo esté funcionando correctamente.

**Tareas:**

- <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>: 

  - Verificar el estado de disponibilidad de todos los hosts.
  - Confirmar que todos los hosts muestren estado verde (disponible).
  - Revisar los problemas activos por host si los hay.

- <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>:

  - Revisar todos los problemas activos.
  - Verificar que los problemas muestren la información correcta (severidad, descripción, etc.).
  - Confirmar que las dependencias entre triggers funcionen correctamente (problemas suprimidos).

- <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>:

  - Filtrar por cada host y verificar que se estén recopilando métricas
  - Confirmar que los items muestren valores recientes (no más de unos minutos).
  - Verificar que los value mappings funcionen correctamente (mostrando valores legibles).

- <span style="color: purple;"><strong>Reports</strong></span> → <span style="color: violet;"><strong>System information</strong></span>:

  - Revisar el resumen general de la infraestructura.
  - Verificar estadísticas de hosts, items, triggers, problemas, etc.

---

## **7. Simulación de incidente y análisis (con instructor)**

**Objetivo**: Demostrar la capacidad de identificar, analizar y responder a un problema real en tiempo real.

**Tareas:**
- El instructor simulará un problema (por ejemplo, desconectar un servicio o generar un evento de fallo)
- Los participantes deben:
  1. **Identificar** el problema en Monitoring → Problems
  2. **Verificar** que se haya recibido la notificación correspondiente
  3. **Analizar** la causa raíz revisando:
     - Métricas históricas en Latest data
     - Gráficos relacionados
     - Estado de los hosts y servicios relacionados
  4. **Confirmar** la recuperación automática o manual cuando el instructor restaure el servicio

---

## **8. Checklist de verificación final**

**Objetivo**: Validar que todos los elementos estén configurados correctamente según las buenas prácticas aprendidas.

Marcar como completado cada elemento:

- [ ] **Organización**:
  - [ ] Hosts organizados en grupos lógicos jerárquicos
  - [ ] Tags aplicados consistentemente a hosts y triggers
  - [ ] Estructura clara y fácil de mantener

- [ ] **Configuración**:
  - [ ] Templates aplicados correctamente a los hosts
  - [ ] Items recopilando datos correctamente
  - [ ] Value mappings funcionando (valores legibles)

- [ ] **Alertas**:
  - [ ] Triggers configurados con severidades apropiadas
  - [ ] Dependencias entre triggers configuradas donde corresponde
  - [ ] Descripciones y operational data útiles en los triggers

- [ ] **Notificaciones**:
  - [ ] Acciones configuradas y activas
  - [ ] Usuarios y grupos de usuarios configurados correctamente
  - [ ] Flujo completo funcionando: Trigger → Evento → Acción → Notificación

- [ ] **Verificación**:
  - [ ] Todos los hosts muestran estado disponible (verde)
  - [ ] Métricas recopilándose en Latest data
  - [ ] Problemas visibles correctamente en Problems
  - [ ] Sistema de monitoreo operativo y funcional

---

## **9. Resumen del ejercicio**

Al finalizar este ejercicio, habrás demostrado:

1. **Capacidad de organización**: Aplicar buenas prácticas para organizar una infraestructura de monitoreo real
2. **Comprensión integral**: Integrar todos los conceptos aprendidos (hosts, templates, items, triggers, acciones, notificaciones)
3. **Análisis de problemas**: Identificar y analizar problemas en tiempo real
4. **Aplicación de buenas prácticas**: Usar tags, grupos, dependencias, y organización jerárquica
5. **Verificación completa**: Validar que todos los componentes del monitoreo funcionen correctamente

**Conceptos clave aplicados:**
- Organización jerárquica de grupos de hosts
- Uso de templates para estandarización
- Configuración de triggers con dependencias
- Sistema completo de notificaciones
- Tags para categorización
- Análisis de problemas y métricas
- Buenas prácticas de configuración y escalabilidad

---

> **💡 Nota importante:** Este ejercicio integra todos los conocimientos adquiridos durante el workshop. Si tienes dudas sobre cómo realizar alguna tarea, puedes consultar los ejercicios anteriores realizados:
> - [Ejercicio 4.8](ejercicio-4.8.md) - Crear host y aplicar template
> - [Ejercicio integrador](ejercicio-integrador.md) - Template completo con LLD
> - [Ejercicio 6.4](ejercicio-6.4.md) - Configuración de triggers
> - [Ejercicio 7.3](ejercicio-7.3.md) - Configuración de notificaciones
> - [Ejercicio 8.4](ejercicio-8.4.md) - Monitoreo agent-less
> - [Ejercicio 9.8](ejercicio-9.8.md) - Organización y permisos

---

<!--
> **💡 ¿Necesitas ayuda?**
> Si después de intentar resolver el ejercicio necesitas consultar la solución detallada, puedes acceder a: [Ejercicio final - Monitoreo integral de infraestructura (Solución completa)](ejercicio-final-solucion.md).
-->

---
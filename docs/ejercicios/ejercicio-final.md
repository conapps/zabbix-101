# **Ejercicio final - Monitoreo integral de infraestructura**

**Objetivo:** Integrar todos los conceptos aprendidos a lo largo del workshop para configurar y verificar un monitoreo completo de una infraestructura real, aplicando buenas prácticas y demostrando la capacidad de analizar problemas.

---

## **📋 Descripción del escenario**

**Contexto empresarial:**

Una empresa necesita implementar monitoreo integral para su infraestructura crítica de servicios web. La infraestructura está compuesta por:

1. **Servidor Web (SRV-Demo-Web-Server)**:

   - Sistema operativo **Linux** con **Nginx** como servidor web.
   - Servicio desplegado sobre **Oracle Cloud Infrastructure**.
   - Servicio desplegado con **Ansible**.
   - Página web corporativa accesible públicamente.
   - **Zabbix Agent**: Ya tiene preconfigurado el agente de Zabbix.
   - **Método de monitoreo**: Agent-less (ICMP, TCP, HTTP).

2. **Switch de red (SW-Demo2)**:

   - **Cisco Nexus 9000** Series.
   - Conecta el servidor web a la red corporativa.
   - Permite acceso al servidor web desde internet.
   - **SNMP**: Ya tiene preconfigurado SNMPv2.
   - **Método de monitoreo**: SNMPv2.

3. **Switch adicional (SW-Demo3)**:

   - **Cisco Nexus 9000** Series.
   - Parte de la infraestructura de red.
   - **SNMP**: Ya tiene preconfigurado SNMPv2.
   - Monitoreado mediante template estándar de Cisco.
   - **Método de monitoreo**: SNMPv2 con template predefinido.

**Arquitectura de red:**
```
Internet
   ↓
[Switch SW-Demo3] ←→ [Switch SW-Demo2] ←→ [Servidor Web SRV-Demo-Web-Server]
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

## **1. Revisión y organización de infraestructura existente**

**Objetivo**: Revisar los hosts configurados en ejercicios anteriores y prepararlos para una organización integral, asegurando que sigan las mejores prácticas de configuración.

**Tareas:**

- Identificar todos los hosts existentes configurados durante el workshop.
- Revisar la configuración actual de cada host.
- **Verificar que los templates estén correctamente aplicados y seguir las mejores prácticas**:
  - Asegurar que los items y discovery rules estén dentro de templates en lugar de estar configurados directamente en los hosts.
  - Esto facilita el mantenimiento, la reutilización y la estandarización de la configuración.
- Identificar qué mejoras de organización son necesarias.
- **Reorganizar hosts según sea necesario**:
  - Algunos hosts pueden tener items o configuraciones directamente en el host que deberían estar en templates.
  - Aplicar templates estándar donde corresponda para seguir las mejores prácticas.

**Hosts a revisar:**

- `SRV-Demo-Web-Server` (configurado en [ejercicio 8.4](ejercicio-8.4.md))
- `SW-Demo2` (configurado en [ejercicio integrador](ejercicio-integrador.md))
- `SW-Demo3` (configurado en [ejercicio 9.8](ejercicio-9.8.md))
- Otros hosts existentes si los hay.

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
  │     └── SRV-Demo-Web-Server
  └── Network Devices
        ├── SW-Demo2
        └── SW-Demo3
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

**Objetivo**: Revisar los triggers configurados en ejercicios anteriores (o si se siguieron las buenas prácticas de los templates) y asegurar que estén correctamente organizados y optimizados.

**Tareas:**

- *Revisar los triggers* existentes de los ejercicios anteriores:
  - Triggers del [ejercicio 8.4](ejercicio-8.4.md) (icmp).
  - Triggers del [ejercicio 6.4](ejercicio-6.4.md) (interfaces, CPU, memoria).
- **Completar las 3 severidades (Warning, Average, High) con dependencias** para cada tipo de problema de los templates:
  - **ICMP Ping**: Tiene un trigger High con expresión `last(...)=0` → Modificar severidad a **Warning**, crear trigger **Average** con `last(...#2)=0`, y crear trigger **High** con expresión más robusta `max(...#3)=0`, configurando dependencias (Warning → Average → High).
  - **Memoria**: Ya tienen Warning y Average → Crear trigger **High** y configurar dependencias (Warning → Average → High).
  - **CPU**: Solo tienen Average → Crear triggers **Warning** y **High**, y configurar dependencias (Warning → Average → High).
  - **Interfaces**: Solo tienen High (Link down, estado 2) → Crear trigger **Warning** (estado 3 - testing), y configurar dependencia (Warning → High).
- Agregar tags a los triggers para mejor categorización (`scope: availability`, `scope: performance`, `scope: capacity`).
- Verificar que los triggers tengan descripciones claras y útiles.

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


> **💡 ¿Necesitas ayuda?**
> Si después de intentar resolver el ejercicio necesitas consultar la solución detallada, puedes acceder a: [Ejercicio final - Monitoreo integral de infraestructura (Solución completa)](ejercicios/ejercicio-final-solucion.md).


---
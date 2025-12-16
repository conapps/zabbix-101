# Workshop de Zabbix

## <span style="color: blue;"><strong>Introducción, fundamentos y primeros pasos del Monitoreo con Zabbix</strong></span>

## **Módulo –** [Bienvenida e introducción](modulos-detallado.md#módulo--bienvenida-e-introducción)

**Objetivo:** Presentación, objetivos, agenda, expectativas.

### <u>Conceptos clave</u>

- Presentación de instructores y participantes.
- Objetivos generales del workshop.
- Agenda de los días.
- Metodología: **70% práctica – 30% teoría**.
- Expectativas y dinámica de trabajo.
- Herramientas que se utilizarán.

## **Módulo 1 – [Introducción al monitoreo y a Zabbix](modulos-detallado.md#módulo-1--introducción-al-monitoreo-y-a-zabbix)**

**Objetivo:** Entender qué es el monitoreo, para qué sirve y por qué **Zabbix** es una herramienta poderosa para gestionar la infraestructura de TI.

### <u>Conceptos clave</u>

- **Qué es el monitoreo** y **por qué es importante**.
- **Casos típicos de uso** en empresas.
- **Qué es Zabbix** y para qué se utiliza.
- Beneficios principales.
- **Ventajas principales** de **Zabbix** frente a **otras soluciones**.

---

## **Módulo 2 – [Arquitectura y Componentes principales de Zabbix](modulos-detallado.md#módulo-2--arquitectura-y-componentes-principales-de-zabbix)**

**Objetivo:** Comprender cómo se compone **Zabbix** y cómo interactúan sus elementos principales y adicionales dentro de una infraestructura de monitoreo.

### <u>Conceptos clave</u>

- Cómo funciona la **arquitectura general** de Zabbix.
- Cuáles son los **componentes principales**:
    - **Zabbix Server**
    - **Base de datos**
    - **Frontend Web**
- Cuáles son los **componentes adicionales**:
    - **Zabbix Agent**
    - **Zabbix Proxy**
    - **Java Gateway**
    - **Web Service**
    - **API REST**
- Diagrama de arquitectura básico.
- Cómo se comunican los componentes entre sí.
- Principales **puertos de comunicación** entre componentes.

---

## **Módulo 3 – [Interfaz Web](modulos-detallado.md#módulo-3--interfaz-web)**

**Objetivo:** Familiarizarse con el **frontend web** de Zabbix, explorar menús, dashboards, gráficos y eventos, y aprender a navegar entre las vistas principales.

### <u>Conceptos clave</u>

- Cómo acceder al **frontend web** y realizar login.
- Recorrido general por la **interfaz gráfica**.
- Secciones principales del menú y explicación de dashboards y vistas principales.

> 📋 [Ejercicio práctico 3.3 - Exploración del frontend](ejercicios/ejercicio-3.3.md)

---

## **Módulo 4 – [Monitoreo básico de hosts](modulos-detallado.md#módulo-4--monitoreo-básico-de-hosts-y-servicios)**

**Objetivo:** Aprender a agregar equipos a monitorear y aplicar templates para obtener métricas, eventos y gráficos.

### <u>Conceptos clave</u>

- Qué es un **host** y qué podemos monitorear.
- Cómo dar **alta de un host** desde cero.
- Qué son los **templates** y para qué se usan.
- Cómo asociar **templates** a múltiples hosts.
- Qué son los **host groups** y cómo agrupar equipos.
- Tipos de **interfaces** y cuándo usarlas.
- Qué son las **tags** y para qué sirven.
- Qué son las **macros** y cómo ayudan a automatizar configuraciones.
- Cómo usar el **inventario** para gestionar información de los hosts.
- Cómo verificar **gráficas**, **eventos** y **métricas**.

> 📋 [Ejercicio práctico 4.8 - Agregar host y verificar métricas](ejercicios/ejercicio-4.8.md)

---

## **Módulo 5 – [Visualización de datos y descubrimiento automático](modulos-detallado.md#módulo-5--visualización-de-datos-y-descubrimiento-automático)**

**Objetivo:** Ver datos en tiempo real (interpretar gráficas, ítems y triggers) y aprender a automatizar el descubrimiento de recursos para reducir la configuración manual.

### <u>Conceptos clave</u>

- Cómo visualizar **métricas** en tiempo real.
- Diferentes tipos de **gráficos** y su interpretación.
- Cómo analizar **eventos** y **problemas activos**.
- Qué es **Low-Level Discovery (LLD)** y para qué se usa.
- Creación de **reglas básicas de descubrimiento**.

> 📋 [Ejercicio práctico 5.3 - Configuración de descubrimiento de interfaces de red](ejercicios/ejercicio-5.3.md)
---

## **Módulo – [Ejercicio integrador](modulos-detallado.md#módulo--ejercicio-integrador)**

**Objetivo:** Realizar un ejercicio práctico completo que combine lo visto en los **primeros 5 módulos**. Cada participante dará de alta un host, configurará un template con items y reglas de descubrimiento, aplicando buenas prácticas de monitoreo.

### <u>Conceptos clave</u>

- Crear un **host** desde cero con configuración SNMP.
- Crear un **template** desde cero con items básicos del sistema.
    - Asociar items al **inventario del host**.
- Configurar **Value Mappings** para interpretar valores numéricos.
- Investigar y consultar **MIBs** estándars.
- Configurar **reglas de Low-Level Discovery (LLD)** en el template.
    - Crear **item prototypes** con value mappings y configuraciones apropiadas.
- Aplicar el template al host y verificar que todos los elementos se hayan creado correctamente.
- Ejecutar las reglas de descubrimiento y verificar la creación automática de items.
- Confirmar que los **value mappings** muestren valores legibles en lugar de números.
- Verificar el **inventario del host** y confirmar que los campos se han poblado automáticamente desde los items configurados.

> 📋 [Ejercicio integrador - Template con LLD y Value Mappings](ejercicios/ejercicio-integrador.md)

---

## **Módulo – [Cierre - Primera parte](modulos-detallado.md#módulo--cierre--primera-parte)**

**Objetivo:** Resolver dudas, repasar <u>Conceptos clave</u> y adelantar los siguientes temas del workshop.

### <u>Conceptos clave</u>

- Espacio para preguntas y respuestas.
- Repaso general de los módulos vistos.
- Revisión de los ejercicios prácticos realizados.
- Resumen de conceptos principales aprendidos.
- Avance de los siguientes temas del workshop.

---

## <span style="color: blue;"><strong>Alertas, automatización y mejores prácticas</strong></span>

## **Módulo – [Bienvenida y repaso](modulos-detallado.md#módulo--bienvenida-y-repaso)**

**Objetivo:** Resolver dudas pendientes y reforzar los conceptos principales antes de avanzar con los siguientes módulos.

### <u>Conceptos clave</u>

- Resolución de dudas.
- Repaso general de los módulos anteriores:
    - Introducción al monitoreo y Zabbix.
    - Arquitectura y componentes principales.
    - Interfaz web.
    - Monitoreo básico de hosts.
    - Visualización de datos y descubrimiento automático.
- Confirmación de que todos completaron los ejercicios.
- Preparación para los siguientes temas.

## **Módulo 6 – [Triggers y eventos](modulos-detallado.md#módulo-6--triggers-y-eventos)**

**Objetivo:** Configurar condiciones para generar alertas.

### <u>Conceptos clave</u>

- ¿Qué es un **trigger** y qué evalúa?
- Estados del trigger: **OK**, **Problem**, **Unknown**.
- ¿Qué es un **evento** y cuándo se genera?
- Tipos de eventos: **Problem** y **Recovery**.
- **Severity** (severidad) de problemas: *Information, Warning, Average, High, Disaster*.
- Expresiones de trigger: ¿cómo definir umbrales y ventanas de tiempo?

> 📋 [Ejercicio práctico 6.4 - Configuración de triggers](ejercicios/ejercicio-6.4.md)

---

## **Módulo 7 – [Acciones y notificaciones](modulos-detallado.md#módulo-7--acciones-y-notificaciones)**

**Objetivo:** Aprender a configurar **acciones** y **canales de notificación** para enviar alertas automáticas.

### <u>Conceptos clave</u>

- Qué es una **acción** y para qué sirve.
- Condiciones y operaciones de las acciones.
- Qué son los **Media Types** y cuáles admite Zabbix.
- Configurar canales de notificación (correo, Telegram, Slack, etc.).
- Configurar un **usuario** con canal de notificación.
- Crear una **acción** que envíe alertas automáticas.
- Validar el envío de notificaciones personalizadas.

> 📋 [Ejercicio práctico 7.3 - Configuración de notificaciones](ejercicios/ejercicio-7.3.md)

---

## **Módulo 8 – [Recopilación de datos (métricas)](modulos-detallado.md#módulo-8--recopilación-de-datos-métricas)**

**Objetivo:** Conocer los diferentes métodos de monitoreo que ofrece **Zabbix** y aprender a elegir el más adecuado según el tipo de recurso.

### <u>Conceptos clave</u>

- Recolección de datos con **Zabbix Agent** y **Zabbix Proxy**.
- Monitoreo **sin agente** (agentless) desde el **Zabbix Server**.
- **Simple checks**: Ping (ICMP) y verificación de puertos.
- **SNMP**: Monitoreo de dispositivos de red.
- **HTTP**: Verificar disponibilidad de sitios y servicios web.
- **IPMI, JMX y SSH**: Monitoreo de hardware y aplicaciones.
- **ODBC**: Monitoreo de bases de datos.
- **Java Monitoring**: JMX y aplicaciones Java.
- **Virtualización**: Monitoreo de VMs, hipervisores y datastores.
- Uso de **métricas personalizadas** y **scripts propios**.

→ **Demo  / ejercicio práctico**: Monitoreo por ping y chequeo HTTP.

---

## **Módulo 9 – [Buenas prácticas de configuración y escalabilidad](modulos-detallado.md#módulo-9--buenas-prácticas-de-configuración-y-escalabilidad)**

**Objetivo:** Configurar **Zabbix** de forma eficiente y escalable, organizando correctamente **hosts, templates, permisos, roles y proxies** para mantener un sistema limpio, optimizado y fácil de administrar.

### <u>Conceptos clave</u>

- Uso recomendado de **templates** vs. ítems manuales.
- Organización lógica de **hosts** y **grupos de hosts**.
- Definición de **roles y permisos** para distintos perfiles.
- Uso de **proxies** para entornos distribuidos.
- Establecer **nombres claros y consistentes**.
- Estandarizar la configuración para simplificar la administración.

→ **Ejercicio práctico (Opcional)**

---

## **Módulo 10 – [Roadmap y ecosistema Zabbix](modulos-detallado.md#módulo-10--roadmap-y-ecosistema-zabbix)**

**Objetivo:** Explorar las posibilidades de **automatización con la API de Zabbix**, mostrar **integraciones clave** (como Grafana) y entender cómo **ampliar el alcance del monitoreo**.

### <u>Conceptos clave</u>

- Introducción a la **API de Zabbix** y casos de uso.
- Integración con **Grafana** para dashboards avanzados.
- Conexiones con herramientas externas:
    
    **→ Prometheus**, **Ansible**, **Slack/Telegram/Teams**, **Elastic/Kibana**.
    
- Uso de **plugins y scripts** de la comunidad.
- Comparativa con otras soluciones de monitoreo.
- Novedades recientes y **roadmap de Zabbix??**

→ **Ejercicio práctico (Opcional: Avanzado)**

---

## **Módulo – [Ejercicio final: monitoreo completo](modulos-detallado.md#módulo--ejercicio-final-monitoreo-completo)**

**Objetivo:** Simulación de escenario real: alta de hosts, triggers, dashboards, alertas.

---

## **Módulo – [Cierre del Workshop](modulos-detallado.md#módulo--cierre-del-workshop)**

**Objetivo:** Cerrar el workshop, responder preguntas, analizar casos reales y recoger feedback.

### <u>Conceptos clave</u>

- Discusión de **casos reales** de monitoreo.
- Revisión de los **módulos avanzados** vistos.
- Espacio de **Q&A** (preguntas y respuestas).
- Intercambio de **buenas prácticas** y experiencias.
- Feedback sobre el workshop.
- Adelantar **próximos pasos** y recursos adicionales.

---
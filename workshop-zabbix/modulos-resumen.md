# Workshop de Zabbix

## **Introducción, fundamentos y primeros pasos del Monitoreo con Zabbix**

## **Módulo –** [Bienvenida e introducción](modulos-detallado.md#módulo--bienvenida-e-introducción)

**Objetivo:** Presentación, objetivos, agenda, expectativas.

### Conceptos clave

- Presentación de instructores y participantes.
- Objetivos generales del workshop.
- Agenda de los días.
- Metodología: **70% práctica – 30% teoría**.
- Expectativas y dinámica de trabajo.
- Herramientas que se utilizarán.

## **Módulo 1 – [Introducción al monitoreo y a Zabbix](modulos-detallado.md#módulo-1--introducción-al-monitoreo-y-a-zabbix)**

**Objetivo:** Entender qué es el monitoreo, para qué sirve y por qué **Zabbix** es una herramienta poderosa para gestionar la infraestructura de TI.

### Conceptos clave

- **Qué es el monitoreo** y **por qué es importante**.
- **Casos típicos de uso** en empresas.
- **Qué es Zabbix** y para qué se utiliza.
- Beneficios principales.
- **Ventajas principales** de **Zabbix** frente a **otras soluciones**.

---

## **Módulo 2 – [Arquitectura y Componentes principales de Zabbix](modulos-detallado.md#módulo-2--arquitectura-y-componentes-principales-de-zabbix)**

**Objetivo:** Comprender cómo se compone **Zabbix** y cómo interactúan sus elementos principales y adicionales dentro de una infraestructura de monitoreo.

### Conceptos clave

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

→ Esquema visual de arquitectura *(podría incluir un diagrama simple o una demo rápida mostrando cómo se comunican)*.

---

## **Módulo 3 – [Interfaz Web](modulos-detallado.md#módulo-3--interfaz-web)**

**Objetivo:** Familiarizarse con el **frontend web** de Zabbix, explorar menús, dashboards, gráficos y eventos, y aprender a navegar entre las vistas principales.

### Conceptos clave

- Cómo acceder al **frontend web** y realizar login.
- Recorrido general por la **interfaz gráfica**.
- Secciones principales del menú y explicación de dashboards y vistas principales.

→ **Ejercicio práctico**: Exploración del frontend.

---

## **Módulo 4 – [Monitoreo básico de hosts y servicios](modulos-detallado.md#módulo-4--monitoreo-básico-de-hosts-y-servicios)**

**Objetivo:** Aprender a agregar equipos a monitorear y aplicar templates para obtener métricas, eventos y gráficos.

### Conceptos clave

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

→ **Ejercicio práctico**: Agregar un equipo local y verificar métricas.

---

## **Módulo 5 – [Visualización de datos y descubrimiento automático](modulos-detallado.md#módulo-5--visualización-de-datos-y-descubrimiento-automático)**

**Objetivo:** Ver datos en tiempo real (interpretar gráficas, ítems y triggers) y aprender a automatizar el descubrimiento de recursos para reducir la configuración manual.

### Conceptos clave

- Cómo visualizar **métricas** en tiempo real.
- Diferentes tipos de **gráficos** y su interpretación.
- Cómo analizar **eventos** y **problemas activos**.
- Qué es **Low-Level Discovery (LLD)** y para qué se usa.
- Creación de **reglas básicas de descubrimiento**.

→ **Ejercicio práctico**: Detección automática de interfaces de red.

---

## **Módulo – [Ejercicios integradores](modulos-detallado.md#módulo--ejercicios-integradores)**

**Objetivo:** Realizar un ejercicio práctico completo que combine lo visto en los **primeros 5 módulos**. Cada participante dará de alta un host, asociará un template, validará métricas, visualizará los dashboards, configurará descubrimientos automáticos y verificará eventos y problemas.

### Conceptos clave

- Acceso al **frontend** y exploración inicial.
- Alta de un **host** desde cero.
- Asociación de **templates** predefinidos.
- Verificación de **métricas** en **Latest Data**.
- Visualización de **gráficos** y **eventos**.
- Creación de una **regla de Low-Level Discovery (LLD)**.
- Validación de ítems, triggers y gráficos generados automáticamente.
- Revisión final de **problemas** y **alertas activas**.

---

## **Módulo – [Cierre - Primera parte](modulos-detallado.md#módulo--cierre--primera-parte)**

**Objetivo:** Resolver dudas, repasar conceptos clave y adelantar los siguientes temas del workshop.

### Conceptos clave

- Espacio para preguntas y respuestas.
- Repaso general de los módulos vistos.
- Revisión de los ejercicios prácticos realizados.
- Resumen de conceptos principales aprendidos.
- Avance de los siguientes temas del workshop.

---

## **Alertas, automatización y mejores prácticas**

## **Módulo – [Bienvenida y repaso](modulos-detallado.md#módulo--bienvenida-y-repaso)**

**Objetivo:** Resolver dudas pendientes y reforzar los conceptos principales antes de avanzar con los siguientes módulos.

### Conceptos clave

- Resolución de dudas.
- Repaso general de los módulos anteriores:
    - Introducción al monitoreo y Zabbix.
    - Arquitectura y componentes principales.
    - Interfaz web.
    - Monitoreo básico de hosts y servicios.
    - Visualización de datos y descubrimiento automático.
- Confirmación de que todos completaron los ejercicios.
- Preparación para los siguientes temas.

## **Módulo 6 – [Triggers y eventos](modulos-detallado.md#módulo-6--triggers-y-eventos)**

**Objetivo:** Configurar condiciones para generar alertas.

### Conceptos clave

- ¿Qué es un **trigger** y qué evalúa?
- Estados del trigger: **OK**, **Problem**, **Unknown**.
- ¿Qué es un **evento** y cuándo se genera?
- Tipos de eventos: **Problem** y **Recovery**.
- **Severity** (severidad) de problemas: *Information, Warning, Average, High, Disaster*.
- Expresiones de trigger: ¿cómo definir umbrales y ventanas de tiempo?

→ **Ejercicio práctico**: Crear un trigger para alertar si la CPU supera el 80%.

---

## **Módulo 7 – [Acciones y notificaciones](modulos-detallado.md#módulo-7--acciones-y-notificaciones)**

**Objetivo:** Aprender a configurar **acciones** y **canales de notificación** para enviar alertas automáticas.

### Conceptos clave

- Qué es una **acción** y para qué sirve.
- Condiciones y operaciones de las acciones.
- Qué son los **Media Types** y cuáles admite Zabbix.
- Configurar canales de notificación (correo, Telegram, Slack, etc.).
- Configurar un **usuario** con canal de notificación.
- Crear una **acción** que envíe alertas automáticas.
- Validar el envío de notificaciones personalizadas.

→ **Ejercicio práctico**: Configurar un usuario que reciba alertas personalizadas.

---

## **Módulo 8 – [Recopilación de datos (métricas)](modulos-detallado.md#módulo-8--recopilación-de-datos-métricas)**

**Objetivo:** Conocer los diferentes métodos de monitoreo que ofrece **Zabbix** y aprender a elegir el más adecuado según el tipo de recurso.

### Conceptos clave

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

### Conceptos clave

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

### Conceptos clave

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

### Conceptos clave

- Discusión de **casos reales** de monitoreo.
- Revisión de los **módulos avanzados** vistos.
- Espacio de **Q&A** (preguntas y respuestas).
- Intercambio de **buenas prácticas** y experiencias.
- Feedback sobre el workshop.
- Adelantar **próximos pasos** y recursos adicionales.

---

# **Materiales a preparar:**

- **La idea sería enfocarlo 70% práctica** y **30% teoría**.
- En cada módulo voy a incluir **demo o ejercicios prácticos guiados**.
- **Manual resumido para los asistentes**: teoría básica + guía de ejercicios prácticos.
    - Glosario de términos clave.
    - Diagramas de arquitectura y flujo de datos.
    - Capturas de pantalla de cada módulo.
    - Tips y recomendaciones rápidas.
    - **Guía de ejercicios prácticos paso a paso**.
- Preparar un **entorno de laboratorio** con:
    - Un servidor Zabbix desplegado automáticamente.
    - 2-3 hosts virtuales por asistente para que cada uno pueda monitorear.
    - Templates preconfigurados para ahorrar tiempo y usar en los ejercicios rápidos.

---

- **Temas que deje afuera:**
    - Web monitoring - Web scenarios (Monitreo Web)
    - Maintenance (Mantenimiento)
    - Configuration import/export (Importación/exportación de configuración)
    - Dashboard (no muestro como se crean sino como visualizar lo que esta)
    - Host → Autoregistration
    - Host → Network Discovery
    - ITEM + Preprocessing (Preprocesamiento)
    - User parameters (Parámetros de usuario)
    - USERS (Usuarios) → me quedó por fuera la [autenticación de usuarios](https://www.zabbix.com/la/features#authentication)
    - USER GROUPS (Grupos de Usuarios) lo doy como buena practica pero medio por arriba
    - User Roles (Roles de usuario) lo doy como buena practica pero medio por arriba
    - Audit (Auditoría)
    - Zabbix server health (Estado del Zabbix server)
    - Zabbix queue (cola de Zabbix)
    - Network Maps (Mapas de red)

---
# **8.4. Ejercicio práctico**

**Objetivo**: Configurar monitoreo **agent-less** usando **ICMP Ping**, **TCP Port Check** y **HTTP Check** para validar la disponibilidad de un host y servicios web, y crear un trigger para alertar cuando el host no responde.

---

## **1. Verificar el servidor web antes de configurar el monitoreo**

> **💡 Nota importante:** Antes de comenzar a configurar el monitoreo, es importante verificar que el servidor web esté funcionando correctamente. El host que vamos a monitorear es un servidor con página web disponible en `https://web.conatel-lab.conatel.cloud`.

1. **Abrir el navegador** e ingresar a la URL del servidor web: `https://web.conatel-lab.conatel.cloud`

2. **Verificar que la página web se cargue correctamente** y mostrar la página en el navegador.

    <img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="8.4. Ejercicio práctico - Captura 1 - Página web del servidor" style="max-width: 100%; height: auto;">

    > **💡 Nota**: Esta verificación previa nos permite confirmar que el servidor está funcionando antes de comenzar a monitorearlo. Si la página no carga, debemos contactar al instructor antes de continuar.

---

## **2. Crear un host para monitoreo agent-less**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → <span style="color: blue;"><strong>Create host</strong></span>.

2. Configurar el host:

    1. **Host name** *(parámetro obligatorio)*:

        → Host name: `SRV-Demo-Web-Server`

    2. **No asociar template** (dejar sin template).

    3. **Groups** *(parámetro obligatorio)*:

        → Groups: `demo` y crear grupo `Web Servers` *(si no existe)*

    4. Configurar **interfaces** para el método de monitoreo con **agente Zabbix**:

        → Interfaces: <span style="color: blue;"><strong>Add</strong> (Guardar)</span> y seleccionar <strong>Agent</strong> quedando 'Type: Agent'.

        → DNS name: `web.conatel-lab.conatel.cloud`

        → Seleccionar en 'Connect to': <strong>DNS</strong>.

    5. *Opcionalmente* se puede agregar una descripción.

        → Description: `Servidor con web para monitoreo agent-less`

    6. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    7. Por ahora no verificar la conectividad *(ya que no se ha creado el item y no hay datos para consultar)*

---

## **3. Crear Value Mapping para items de conectividad**

**Objetivo**: Crear un value mapping a nivel de host para convertir los valores numéricos (0 y 1) en texto legible (Down/Up) que se usará en múltiples items de conectividad.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Value mapping</strong></span> → <span style="color: blue;"><strong>Create value map</strong></span>

2. Configurar el Value Mapping:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `Service Status`

    2. **Mappings**:

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar cada mapeo:

        - Value: `0` → Mapped to: `Down`
        - Value: `1` → Mapped to: `Up`

    3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    > **💡 Nota**: Este value mapping se usará en los items ICMP Ping y TCP Port 80 Check para mostrar valores legibles en lugar de números. Cuando veas los valores en Latest Data o Graphs, en lugar de ver `0` o `1`, verás `Down` o `Up`, lo que hace mucho más fácil interpretar el estado del servicio.

---

## **4. Configurar item ICMP Ping (Simple check)**

**Objetivo**: Monitorear la disponibilidad básica del host mediante ping ICMP.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. Configurar el item:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `ICMP Ping`

    2. **Type**:

        → Type: `Simple check`

        > **💡 ¿Por qué Simple check?**
        >
        > Los **Simple checks** son comprobaciones básicas que Zabbix realiza directamente desde el servidor sin necesidad de instalar un agente en el dispositivo monitoreado. Son ideales para:
        > - Verificar disponibilidad básica (ping ICMP)
        > - Comprobar si puertos TCP están abiertos
        > - Monitoreo agent-less cuando no se puede instalar un agente
        >
        > En este caso, usamos `Simple check` porque queremos monitorear la disponibilidad del host mediante ping ICMP sin necesidad de tener un agente instalado.

    3. **Key** *(parámetro obligatorio)*:

        → Key: `icmpping`

        > **💡 Nota**: El key `icmpping` es un simple check predefinido de Zabbix que realiza un ping ICMP al host. Retorna `1` si el host responde o `0` si no responde.

    4. **Type of information**:

        → Type of information: `Numeric (unsigned)`

    5. **Units**:

        → Units: *(dejar vacío)*

    6. **Update interval**:

        → Update interval: `30s`

        > **💡 Nota**: Un intervalo de 30 segundos es adecuado para monitoreo de disponibilidad básica. En producción, se puede ajustar según las necesidades (1m, 5m, etc.).

    7. **Value mapping**:

        → Value mapping: Seleccionar `Service Status` (el value mapping creado anteriormente)

    8. **Description** *(opcional)*:

        → Description: `Monitoreo de disponibilidad básica mediante ping ICMP. Retorna 1 si el host responde, 0 si no responde.`

    9. **Tags** *(opcional)*:

        - Name: `component` | Value: `connectivity`

    10. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    > **💡 Nota sobre templates:** Existe un template predefinido llamado **"ICMP Ping"** que contiene esta misma configuración de item ICMP. Si quieres revisar cómo está configurado, puedes ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> y buscar el template **"ICMP Ping"** para ver su configuración.

---

## **5. Configurar item TCP Port Check (Simple check)**

**Objetivo**: Verificar si un puerto TCP específico está abierto y respondiendo.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. Configurar el item:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `TCP Port 80 Check`

    2. **Type**:

        → Type: `Simple check`

    3. **Key** *(parámetro obligatorio)*:

        → Key: `net.tcp.service[tcp,,80]`

        > **💡 Nota**: El formato del key es `net.tcp.service[<protocolo>,<nombre_del_servicio>,<puerto>]`. Se puede usar cualquier puerto (80 para HTTP, 443 para HTTPS, 22 para SSH, etc.).

    4. **Type of information**:

        → Type of information: `Numeric (unsigned)`

    5. **Units**:

        → Units: *(dejar vacío)*

    6. **Update interval**:

        → Update interval: `1m`

    7. **Value mapping**:

        → Value mapping: Seleccionar `Service Status` (el value mapping creado anteriormente)

    8. **Description** *(opcional)*:

        → Description: `Verificación de disponibilidad del puerto TCP 80 (HTTP). Retorna 1 si el puerto está abierto y respondiendo, 0 si no responde.`

    9. **Tags** *(opcional)*:

        - Name: `component` | Value: `connectivity`

    10. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **6. Configurar item HTTP Check (HTTP agent)**

**Objetivo**: Monitorear la disponibilidad y respuesta de un servicio web mediante HTTP/HTTPS.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. Configurar el item:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `HTTP Check - Zabbix Website`

    2. **Type**:

        → Type: `HTTP agent`

    3. **Key** *(parámetro obligatorio)*:

        → Key: `http.check[zabbix.com]`

    4. **Type of information**:

        → Type of information: `Numeric (unsigned)`

    5. **URL** *(parámetro obligatorio)*:

        → URL: `https://www.zabbix.com`

        > **💡 Nota**: Se puede usar cualquier URL pública. El instructor puede proporcionar una URL específica para el ejercicio.

    6. **Request method**:

        → Request type: `GET` *(por defecto)*

    7. **Timeout**:

        → Timeout: `10s` *(tiempo máximo de espera para la respuesta)*

    8. **Status codes**:

        → Status codes: `200` *(código HTTP de éxito)*

        > **💡 Nota**: El item retornará `1` si el servidor responde con código 200 (OK), o `0` si no responde o retorna otro código.

    9. **Update interval**:

        → Update interval: `2m`

    10. **Description** *(opcional)*:

        → Description: `Monitoreo de disponibilidad del sitio web mediante HTTP check. Retorna 1 si el servidor responde con código 200, 0 en caso contrario.`

    11. **Tags** *(opcional)*:

        - Name: `component` | Value: `web`

    12. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **7. Crear trigger para ICMP Ping**

**Objetivo**: Crear un trigger que se active cuando el host no responde a ping ICMP.

> **💡 ¿Cómo crear un trigger?**
>
> Existen **varias formas** de crear un trigger en Zabbix:
>
> 1. **Desde un item**: En la columna **Name** aparecerán todos los items y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span> *(Zabbix prellenará automáticamente el item en la expresión)*.
>
> 2. **Desde la pestaña Triggers**: En la esquina superior derecha de la pantalla hacer clic en <span style="color: blue;"><strong>Create trigger</strong></span>.

1. Crear el trigger desde el item **"ICMP Ping"**:

    - En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span>.
    - Localizar el item **"ICMP Ping"** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span>.

2. Configurar el trigger:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `Host {HOST.NAME} is down (no response to ICMP ping)`

    2. **Severity** *(parámetro obligatorio)*:

        → Severity: `High` *(Alta)*

    3. **Expression** *(parámetro obligatorio)*:

        → Expression: `last(/SRV-Demo-Web-Server/icmpping)=0`

        > **💡 ¿Qué hace esta expresión?**
        >
        > Esta expresión verifica si el último valor del item `icmpping` es igual a `0`, lo que significa que el host no está respondiendo a ping ICMP.

    4. **Recovery expression** *(opcional)*:

        → Recovery expression: `last(/SRV-Demo-Web-Server/icmpping)=1`

        > **💡 ¿Qué es Recovery expression?**
        >
        > La **Recovery expression** define cuándo el trigger debe volver al estado OK. En este caso, el trigger se recupera automáticamente cuando el host vuelve a responder a ping (valor 1).

    5. **Description** *(opcional)*:

        → Description: `El host {HOST.NAME} no está respondiendo a ping ICMP. Esto puede indicar que el host está inaccesible, apagado o que hay problemas de conectividad de red.`

    6. **Tags** *(opcional)*:

        - Name: `scope` | Value: `availability`

    7. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **8. Verificar la recopilación de datos**

1. Esperar aproximadamente **2-3 minutos** para que Zabbix comience a recopilar datos de los items configurados.

2. Verificar la columna **Availability** del host:

    - <span style="color: green;">🟢 Verde</span> → Host disponible y respondiendo.
    - <span style="color: red;">🔴 Rojo</span> → Host no disponible o no responde.
    - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

3. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>.

4. Filtrar por el host **"SRV-Demo-Web-Server"** y verificar que aparezcan los tres items:

    - **ICMP Ping**: Debe mostrar `Up` si el host responde, o `Down` si no responde *(gracias al value mapping "Service Status" configurado, en lugar de ver `1` o `0`)*.
    - **TCP Port 80 Check**: Debe mostrar `Up` si el puerto está abierto, o `Down` si no responde *(gracias al value mapping "Service Status" configurado, en lugar de ver `1` o `0`)*.
    - **HTTP Check - Zabbix Website**: Debe mostrar `1` si el sitio web responde con código 200, o `0` en caso contrario.

---

## **9. Visualizar resultados en Graphs**

1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Graphs</strong></span>.

2. Filtrar por el host **"SRV-Demo-Web-Server"**.

3. Crear un gráfico simple (opcional):

    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar **"SRV-Demo-Web-Server"** → Pestaña <span style="color: violet;"><strong>Graphs</strong></span> → <span style="color: blue;"><strong>Create graph</strong></span>.
    - Agregar los items **"ICMP Ping"**, **"TCP Port 80 Check"** y **"HTTP Check - Zabbix Website"** al gráfico.
    - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

4. Visualizar el gráfico en <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Graphs</strong></span> para ver la evolución de los valores en el tiempo.

---

## **10. Solicitar al instructor que genere un problema**

1. **Solicitar al instructor que genere un problema de conectividad**:

    - Pedir al instructor que simule un problema de conectividad para el host **"SRV-Demo-Web-Server"**.

2. **Verificar en Zabbix que el problema se haya generado**:

    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>.
    - Verificar que aparezca el problema **"Host SRV-Demo-Web-Server is down (no response to ICMP ping)"** con severidad **High**.
    - Verificar que la severidad coincida con la configurada en el trigger.

3. **Verificar en Latest Data**:

    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>.
    - Filtrar por el host **"SRV-Demo-Web-Server"**.
    - Verificar que el item **"ICMP Ping"** muestre valor `Down` (host no responde) *(gracias al value mapping configurado, en lugar de ver `0`)*.

4. **Verificar la recuperación** (cuando el instructor restaure el acceso):

    - Una vez que el instructor restaure el acceso al host, esperar aproximadamente **30 segundos** (intervalo de actualización del item).
    - Verificar en <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span> que el problema haya cambiado a estado **OK** (recuperado).
    - Verificar en <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> que el item **"ICMP Ping"** muestre valor `Up` (host responde nuevamente) *(gracias al value mapping configurado, en lugar de ver `1`)*.

---

## **Resumen del ejercicio**

Este ejercicio práctico cubre la configuración de monitoreo **agent-less** en Zabbix:

1. **Verificación previa**: Se verificó que el servidor web esté funcionando antes de comenzar el monitoreo.
2. **Creación de host**: Se creó un host sin template para monitoreo agent-less.
3. **Configuración de Value Mapping**: Se creó un value mapping genérico "Service Status" a nivel de host para convertir valores numéricos (0/1) en texto legible (Down/Up) que se usa en múltiples items.
4. **Configuración de items agent-less**:
    - **ICMP Ping**: Monitoreo básico de disponibilidad mediante ping (con value mapping "Service Status").
    - **TCP Port Check**: Verificación de disponibilidad de puertos TCP (con value mapping "Service Status").
    - **HTTP Check**: Monitoreo de disponibilidad de servicios web.
5. **Configuración de trigger**: Se creó un trigger que alerta cuando el host no responde a ping ICMP.
6. **Verificación**: Se verificó la recopilación de datos y la visualización en graphs y problems.

> **💡 Nota importante:** Los métodos agent-less son útiles cuando no se puede instalar un agente en el dispositivo monitoreado, pero proporcionan información más limitada que el monitoreo con agente. Para métricas detalladas del sistema operativo, se recomienda usar Zabbix Agent.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="8.4. Ejercicio práctico - Captura 1 - Página web del servidor" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="8.4. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_3.png" alt="8.4. Ejercicio práctico - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_4.png" alt="8.4. Ejercicio práctico - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_5.png" alt="8.4. Ejercicio práctico - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_6.png" alt="8.4. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

</details>


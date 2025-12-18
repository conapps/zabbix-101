# **8.4. Ejercicio práctico**

**Objetivo**: Configurar monitoreo **agent-less** usando **ICMP Ping**, **TCP Port Check** y **HTTP Check** para validar la disponibilidad de un host y servicios web, y crear un trigger para alertar cuando el host no responde.

---

## **1. Verificar el servidor web antes de configurar el monitoreo**

> **💡 Nota importante:** Antes de comenzar a configurar el monitoreo, es importante verificar que el servidor web esté funcionando correctamente. El host que vamos a monitorear es un servidor con página web disponible.

1. **Abrir el navegador** e ingresar a la URL del servidor web: `http://web.conatel-lab.conatel.cloud`

2. **Verificar que la página web se cargue correctamente**.

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

        → Port: `10050` *(protocolo por defecto para Agent)*

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

    > **💡 Nota**: Este value mapping se usará en los items ICMP Ping y TCP Port: 80 Check para mostrar valores legibles en lugar de números. Cuando veas los valores en Latest Data o Graphs, en lugar de ver `0` o `1`, verás `Down` o `Up`, lo que hace mucho más fácil interpretar el estado del servicio.

---

## **4. Crear Value Mapping para códigos de estado HTTP**

**Objetivo**: Crear un value mapping para convertir los códigos de estado HTTP numéricos en texto legible (200 = OK, 404 = Not Found, etc.) que se usará en el item HTTP Check.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Value mapping</strong></span> → <span style="color: blue;"><strong>Create value map</strong></span>

2. Configurar el Value Mapping:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `HTTP Status Codes`

    2. **Mappings**:

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar cada mapeo de códigos de estado HTTP comunes:

        - Value: `200` → Mapped to: `OK`
        - Value: `301` → Mapped to: `Moved Permanently`
        - Value: `302` → Mapped to: `Found (Redirect)`
        - Value: `400` → Mapped to: `Bad Request`
        - Value: `401` → Mapped to: `Unauthorized`
        - Value: `403` → Mapped to: `Forbidden`
        - Value: `404` → Mapped to: `Not Found`
        - Value: `500` → Mapped to: `Internal Server Error`
        - Value: `502` → Mapped to: `Bad Gateway`
        - Value: `503` → Mapped to: `Service Unavailable`
        - Value: `504` → Mapped to: `Gateway Timeout`

        > **💡 Nota**: Puedes agregar solo los códigos que consideres más relevantes para tu entorno. Los códigos más comunes son 200 (OK), 404 (Not Found) y 500 (Internal Server Error).

    3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    > **💡 Nota**: Este value mapping se usará en el item HTTP Check para mostrar valores legibles en lugar de números. Cuando veas los valores en Latest Data o Graphs, en lugar de ver `200` o `404`, verás `OK` o `Not Found`, lo que hace mucho más fácil interpretar el estado del servicio web.

---

## **5. Configurar item ICMP Ping (Simple check)**

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

        > **📚 Documentación oficial:** Para más detalles sobre Simple checks items, consulta [Zabbix - Simple checks items](https://www.zabbix.com/documentation/6.0/es/manual/config/items/itemtypes/simple_checks).

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

## **6. Configurar item TCP Port Check (Simple check)**

**Objetivo**: Verificar si un puerto TCP específico está abierto y respondiendo.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. Configurar el item:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `TCP Port: 80 Check`

    2. **Type**:

        → Type: `Simple check`

        > **📚 Documentación oficial:** Para más detalles sobre Simple checks items, consulta [Zabbix - Simple checks items](https://www.zabbix.com/documentation/6.0/es/manual/config/items/itemtypes/simple_checks).

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

## **7. Configurar item HTTP Check (HTTP agent)**

**Objetivo**: Monitorear la disponibilidad y respuesta de un servicio web mediante HTTP/HTTPS.

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. Configurar el item:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `HTTP Check - Website`

    2. **Type**:

        → Type: `HTTP agent`

    3. **Key** *(parámetro obligatorio)*:

        → Key: `http.check[web.conatel-lab.conatel.cloud]`

        > **💡 Nota**: El formato del key es `http.check[<URL>]`.

    4. **Type of information**:

        → Type of information: `Numeric (unsigned)`

    5. **URL** *(parámetro obligatorio)*:

        → URL: `http://web.conatel-lab.conatel.cloud`

        > **💡 Nota**: La URL debe ser la misma que la especificada en el key y se puede usar cualquier URL pública.

    6. **Request method**:

        → Request type: `GET` *(por defecto)*

    7. **Timeout**:

        → Timeout: `10s` *(tiempo máximo de espera para la respuesta)*

    8. **Required status codes**:

        → Required status codes: `200` *(código HTTP de éxito)*

        > **💡 Nota**: Este campo valida que la respuesta tenga el código especificado, pero no afecta el valor almacenado. El valor se extraerá mediante preprocesamiento.

    9. **Retrieve mode**:

        → Retrieve mode: Seleccionar `Headers` *(esto permite obtener el código de estado HTTP)*

        > **💡 Nota importante**: Por defecto, HTTP agent recupera el "Body" (contenido HTML), pero necesitamos los "Headers" para obtener el código de estado HTTP como número.

    10. **Update interval**:

        → Update interval: `2m`

    11. **Value mapping**:

        → Value mapping: Seleccionar `HTTP Status Codes` (el value mapping creado anteriormente)

        > **💡 Nota**: Este value mapping convertirá los códigos de estado HTTP numéricos en texto legible. Por ejemplo, en lugar de ver `200` o `404`, verás `OK` o `Not Found` en Latest Data y Graphs, lo que hace mucho más fácil interpretar el estado del servicio web.

    12. **Description** *(opcional)*:

        → Description: `Monitoreo de disponibilidad del sitio web mediante HTTP check. Almacena el código de estado HTTP de la respuesta (200 = OK, otros códigos indican problemas).`

    13. **Tags** *(opcional)*:

        - Name: `component` | Value: `web`

    14. **Ir a la pestaña Preprocessing**:

        Necesitamos configurar preprocesamiento para extraer el código de estado HTTP de los headers.

        1. Hacer clic en la pestaña <span style="color: blue;"><strong>Preprocessing</strong></span> (ubicada junto a la pestaña "Item").

        2. Configurar el primer paso de preprocesamiento:

            - Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar un paso de preprocesamiento.

            - **Name**: Seleccionar `Regular expression`

            - **Pattern**: Ingresar `^HTTP/[^\s]+\s+(\d+)` *(expresión regular para extraer el código de estado HTTP)*

            - **Output**: Ingresar `\1` *(captura el primer grupo, que es el código de estado)*

                > **💡 Nota**: Esta expresión regular busca el código de estado HTTP al inicio de los headers. Funciona con cualquier versión de HTTP (HTTP/1.1, HTTP/2, HTTP/3, etc.). El formato es "HTTP/2 200" o "HTTP/1.1 200", y la expresión captura el número del código de estado (200, 404, 500, etc.).

        3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span> el paso de preprocesamiento.

        > **💡 Nota importante**: Con esta configuración, el item almacenará el código de estado HTTP real (200, 404, 500, etc.) como número. Un código 200 indica que el sitio está disponible correctamente.
        >
        > **⚠️ Si no estás familiarizado con expresiones regulares**: Esta puede parecer una configuración compleja, pero es necesaria para convertir el resultado HTTP (que viene como texto) en un número. La expresión regular `^HTTP/[^\s]+\s+(\d+)` busca el patrón "HTTP/2 200" o "HTTP/1.1 200" al inicio de los headers y extrae solo el número del código de estado (200, 404, 500, etc.).

    16. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    > **📚 Documentación oficial:** Para más detalles sobre HTTP agent items, consulta [Zabbix - HTTP agent items](https://www.zabbix.com/documentation/6.0/es/manual/config/items/itemtypes/http).

    > **💡 Alternativa más simple: Web Scenarios (Recomendado para usuarios sin experiencia)**
    >
    > Si el preprocesamiento con expresiones regulares te resulta complicado, puedes usar **Web Scenarios** (Escenarios Web), que son más simples y están diseñados específicamente para monitorear sitios web. Los Web Scenarios:
    > - **No requieren preprocesamiento** - Manejan automáticamente el código de estado HTTP
    > - Son más fáciles de configurar - Solo necesitas especificar la URL y el código esperado
    > - Proporcionan métricas adicionales como tiempo de respuesta
    > - Permiten monitorear múltiples pasos si es necesario
    >
    > **¿Cuándo usar cada uno?**
    > - **HTTP agent items**: Más flexibles, permiten procesar el contenido de la respuesta, integrarse con otros items del mismo host
    > - **Web Scenarios**: Más simples, ideales para verificar disponibilidad de sitios web sin necesidad de procesar respuestas complejas
    >
    > Si prefieres usar Web Scenarios en lugar de HTTP agent items, puedes crearlos desde la pestaña **"Web scenarios"** del host. Solo necesitas crear un escenario con un paso que apunte a la URL deseada y especificar "200" como código de estado requerido.

---

## **8. Crear trigger para ICMP Ping**

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

        → Name: `Unavailable by ICMP ping`

    2. **Event name**

        → Event name: `Host {HOST.NAME} is down (no response to ICMP ping)`

    2. **Severity** *(parámetro obligatorio)*:

        → Severity: `High` *(Alta)*

    3. **Expression** *(parámetro obligatorio)*:

        → Expression: `last(/SRV-Demo-Web-Server/icmpping)=0`

        > **💡 ¿Qué hace esta expresión?**
        >
        > Esta expresión verifica si el último valor del item `icmpping` es igual a `0`, lo que significa que el host no está respondiendo a ping ICMP.
        >
        > **💡 Expresión alternativa (más robusta):**
        >
        > También puedes usar `max(/SRV-Demo-Web-Server/icmpping,#3)=0` en lugar de `last(...)=0`. Esta expresión verifica si el **máximo valor de los últimos 3 valores** es igual a `0`, lo que reduce falsas alarmas causadas por valores puntuales o problemas temporales de red. Es más robusta porque requiere que **todos** los últimos 3 valores sean `0` para activarse.

    4. **Recovery expression** *(opcional)*:

        → Recovery expression: `last(/SRV-Demo-Web-Server/icmpping)=1`

        > **💡 ¿Qué es Recovery expression?**
        >
        > La **Recovery expression** define cuándo el trigger debe volver al estado OK. En este caso, el trigger se recupera automáticamente cuando el host vuelve a responder a ping (valor 1).

    5. **Description** *(opcional)*:

        → Description: `No disponible por ping ICMP. Este trigger se activa cuando la solicitud de ping ICMP al dispositivo devolvió un tiempo de espera agotado. Esto puede indicar que el host está inaccesible, apagado o que hay problemas de conectividad de red. Por favor, verifique la conectividad del dispositivo.`

    6. **Tags** *(opcional)*:

        - Name: `scope` | Value: `availability`

    7. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **9. Verificar la recopilación de datos**

1. Esperar aproximadamente **2-3 minutos** para que Zabbix comience a recopilar datos de los items configurados.

2. Verificar la columna **Availability** del host:

    - <span style="color: green;">🟢 Verde</span> → Host disponible y respondiendo.
    - <span style="color: red;">🔴 Rojo</span> → Host no disponible o no responde.
    - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

3. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>.

4. Filtrar por el host **"SRV-Demo-Web-Server"** y verificar que aparezcan los tres items:

    - **ICMP Ping**: Debe mostrar `Up` si el host responde, o `Down` si no responde *(gracias al value mapping "Service Status" configurado, en lugar de ver `1` o `0`)*.
    - **TCP Port: 80 Check**: Debe mostrar `Up` si el puerto está abierto, o `Down` si no responde *(gracias al value mapping "Service Status" configurado, en lugar de ver `1` o `0`)*.
    - **HTTP Check - Website**: Debe mostrar el código de estado HTTP convertido a texto legible gracias al value mapping "HTTP Status Codes" (por ejemplo, `OK` para código 200, `Not Found` para código 404, `Internal Server Error` para código 500, etc.).

5. Visualizar resultados de los items con Graphs.

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

5. Verificar que la página web esté funcionando correctamente [URL del sitio web](http://web.conatel-lab.conatel.cloud).

---

## **Resumen del ejercicio**

Este ejercicio práctico cubre la configuración de monitoreo **agent-less** en Zabbix:

1. **Verificación previa**: Se verificó que el servidor web esté funcionando antes de comenzar el monitoreo.
2. **Creación de host**: Se creó un host sin template para monitoreo agent-less.
3. **Configuración de Value Mappings**:
    - Se creó un value mapping "Service Status" para convertir valores numéricos (0/1) en texto legible (Down/Up) que se usa en items de conectividad.
    - Se creó un value mapping "HTTP Status Codes" para convertir códigos de estado HTTP (200, 404, 500, etc.) en texto legible (OK, Not Found, Internal Server Error, etc.).
4. **Configuración de items agent-less**:
    - **ICMP Ping**: Monitoreo básico de disponibilidad mediante ping (con value mapping "Service Status").
    - **TCP Port Check**: Verificación de disponibilidad de puertos TCP (con value mapping "Service Status").
    - **HTTP Check**: Monitoreo de disponibilidad de servicios web (con value mapping "HTTP Status Codes").
5. **Configuración de trigger**: Se creó un trigger que alerta cuando el host no responde a ping ICMP.
6. **Verificación**: Se verificó la recopilación de datos y la visualización de graphs y problems.

> **💡 Nota importante:** Los métodos agent-less son útiles cuando no se puede instalar un agente en el dispositivo monitoreado, pero proporcionan información más limitada que el monitoreo con agente. Para métricas detalladas del sistema operativo, se recomienda usar Zabbix Agent.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="8.4. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

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

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_7.png" alt="8.4. Ejercicio práctico - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_8.png" alt="8.4. Ejercicio práctico - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_9.png" alt="8.4. Ejercicio práctico - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_10.png" alt="8.4. Ejercicio práctico - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_11.png" alt="8.4. Ejercicio práctico - Captura 11" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_12.png" alt="8.4. Ejercicio práctico - Captura 12" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_13.png" alt="8.4. Ejercicio práctico - Captura 13" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_14.png" alt="8.4. Ejercicio práctico - Captura 14" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_15.png" alt="8.4. Ejercicio práctico - Captura 15" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/8.4.%20Ejercicio%20pr%C3%A1ctico_16.png" alt="8.4. Ejercicio práctico - Captura 16" style="max-width: 100%; height: auto;">

</div>

</details>


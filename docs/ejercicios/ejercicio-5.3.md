# **5.3. Ejercicio práctico**

**Objetivo**: Configuración de una regla de descubrimiento de **interfaces de red**.

**<u>Pasos guiados</u>**

1. Crear un nuevo host:

    1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → <span style="color: blue;"><strong>Create host</strong></span>.

    2. Configurar:
        1. Nombre del host *(parámetro obligatorio)*.

            → Host name: `SW-Demo1`

        2. **No asociar template** (dejar sin template).

        3. Elegir un Grupo de hosts *(parámetro obligatorio)*.

            → Groups: `demo` y crear grupo `Switches`

        4. Configurar **interfaces** para el método de monitoreo con **SNMP**:

            → Interfaces: <span style="color: blue;"><strong>Add</strong> (Guardar)</span> y seleccionar **SNMP** quedando 'Type: SNMP'.

            → IP address: `10.0.10.1`

            → Port: `161` *(protocolo por defecto para SNMP)*

            → SNMP version: `SNMPv2`

            → Community: `snmp-demo`

        5. *Opcionalmente* se puede agregar una descripción.

            → Description: `Switch virtual Cisco Nexus 9000`

        6. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

        7. Por ahora no verificar la conectividad *(ya que no se ha creado el item y no hay datos para consultar)*

2. Crear un item para monitorear el nombre del sistema:

    1. En el host **"SW-Demo1"**, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

    2. Configurar:

        1. Nombre del item *(parámetro obligatorio)*.

            → Name: `System name`

        2. Tipo de verificación.

            → Type: `SNMP agent`

        3. Clave *(parámetro obligatorio, debe ser único)*.

            → Key: `system.name`

        4. Tipo de información.

            → Type of information: `Character`

        5. Identificador de objetos SNMP *(parámetro obligatorio)*.

            → SNMP OID: `1.3.6.1.2.1.1.5.0`

        6. Frecuencia de consulta *(parámetro obligatorio)*.

            → Update interval: `1h`

        7. *Opcionalmente* se puede agregar una descripción.

            → Description: `Nombre asignado administrativamente para este nodo gestionado. Por convención, este es el nombre de dominio completamente calificado (FQDN) del nodo. Si el nombre es desconocido, el valor es una cadena de longitud cero.`

            > **💡 Nota:** Este OID pertenece a la MIB [SNMPv2-MIB](https://mibs.observium.org/mib/SNMPv2-MIB/) y proporciona el nombre del sistema del dispositivo. Es útil para identificar y etiquetar dispositivos en el monitoreo.

        8. *Opcionalmente* se puede agregar uno o más tags (etiquetas)

            - Name: `component` | Value: `system`

        9. **Probar el item antes de guardar:**

            - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> y luego en la nueva ventana <span style="color: blue;"><strong>Get value and test</strong></span>.
            - Verificar que se obtenga un valor de texto (string) que representa el nombre del sistema del dispositivo `NX1`.

        10. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    3. Verificar que el item funcione correctamente:

        1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host **"SW-Demo1"**.
        2. Verificar que el item **"System name"** aún no muestre datos (esto es normal ya que el intervalo de actualización está configurado en 1 hora).
        3. Volver a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → seleccionar el host **"SW-Demo1"** → pestaña <span style="color: violet;"><strong>Items</strong></span>.
        4. Localizar el item **"System name"** y hacer clic en <span style="color: blue;"><strong>Execute now</strong></span> (Ejecutar ahora) para ejecutar manualmente el item sin esperar el intervalo configurado (1 hora).
        5. Esperar unos minutos para que Zabbix procese la consulta.
        6. Volver a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host **"SW-Demo1"**.
        7. Verificar que el item **"System name"** ahora muestre el nombre del dispositivo.

3. Configurar la regla de descubrimiento:

    1. En el host recientemente creado ir a la columna <span style="color: violet;"><strong>Discovery</strong></span> → <span style="color: blue;"><strong>Create discovery rule</strong></span>

    2. Configurar:

        1. Nombre de la regla *(parámetro obligatorio)*.

            → Name: `Network Interfaces Discovery`

        2. Tipo de verificación (SNMP, agente o script).

            → Type: `SNMP agent`

            > **💡 ¿Qué es un agente SNMP?**
            >
            > Un **agente SNMP** es un software que se ejecuta en el dispositivo de red y responde a las solicitudes SNMP proporcionando información sobre el estado y las métricas del dispositivo. Cuando Zabbix realiza una consulta SNMP, el agente recopila y envía los datos solicitados, permitiendo que Zabbix pueda monitorear y analizar el dispositivo.

        3. Clave *(parámetro obligatorio, debe ser único y no debe coincidir con ninguna otra regla de descubrimiento)*.

            → Key: `net.if.discovery`

        4. Identificador de objetos SNMP *(parámetro obligatorio)*

            → SNMP OID: `discovery[{#IFOPERSTATUS},1.3.6.1.2.1.2.2.1.8,{#IFADMINSTATUS},1.3.6.1.2.1.2.2.1.7,{#IFALIAS},1.3.6.1.2.1.31.1.1.1.18,{#IFNAME},1.3.6.1.2.1.31.1.1.1.1,{#IFDESCR},1.3.6.1.2.1.2.2.1.2,{#IFTYPE},1.3.6.1.2.1.2.2.1.3]`

            > **💡 ¿Qué hace este comando?**
            >
            > Esta regla de descubrimiento le indica a Zabbix que **consulte múltiples OIDs SNMP** del dispositivo para **descubrir automáticamente las interfaces de red** y extraer información sobre cada una.
            >
            > **Formato:** `discovery[{#MACRO1}, oid1, {#MACRO2}, oid2, …,]`
            >
            > **¿Qué significa cada parte?**
            > - **`{#MACRO1}`, `{#MACRO2}`, etc.** → Son **nombres de macros LLD** (Low Level Discovery) que almacenarán los valores obtenidos. Estas macros se pueden usar posteriormente en items, triggers y gráficos prototipo.
            > - **`oid1`, `oid2`, etc.** → Son **OIDs (Object Identifiers)** que representan direcciones numéricas únicas en el árbol MIB SNMP. Cada OID apunta a una información específica del dispositivo.
            > - **`{#SNMPINDEX}`** → Es una **macro automática** que Zabbix genera para cada entidad descubierta. Contiene el índice numérico del OID (por ejemplo, si se descubre la interfaz con índice 1, 2, 3, etc.).
            >
            > **¿Cómo funciona?**
            > 1. Zabbix consulta cada OID especificado al dispositivo SNMP.
            > 2. El dispositivo responde con una lista de valores, cada uno asociado a un índice (1, 2, 3, etc.).
            > 3. Zabbix agrupa los resultados por el índice común (`{#SNMPINDEX}`).
            > 4. Para cada grupo, crea macros con los valores obtenidos (por ejemplo, `{#IFDESCR}` = "GigabitEthernet0/1", `{#IFNAME}` = "Gi0/1").
            >
            > **En este ejemplo específico:**
            > - `{#IFOPERSTATUS}` → Estado operativo de la interfaz (up/down).
            > - `{#IFADMINSTATUS}` → Estado administrativo de la interfaz (enabled/disabled).
            > - `{#IFALIAS}` → Alias o descripción personalizada de la interfaz.
            > - `{#IFNAME}` → Nombre corto de la interfaz (ej: "Gi0/1").
            > - `{#IFDESCR}` → Descripción completa de la interfaz.
            > - `{#IFTYPE}` → Tipo de interfaz (ethernet, loopback, etc.).
            >
            > Con esta información, Zabbix puede crear automáticamente items, triggers y gráficos para cada interfaz descubierta.

        5. Frecuencia de descubrimiento *(parámetro obligatorio)*.

            → Update interval: `1h`

        6. Mantener recursos perdidos *(parámetro obligatorio)*.

            → Keep lost resources period: `30d`

        7. *Opcionalmente* se puede agregar una descripción.

            → Description: `Descubriendo interfaces desde IF-MIB.`

        8. Probar la regla de descubrimiento antes de guardar:

            - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> y luego en la nueva ventana <span style="color: blue;"><strong>Get value and test</strong></span>.
            - Verificar que aparezcan resultados en formato JSON mostrando las interfaces descubiertas empezando por `{#SNMPINDEX}`.
            - Cada resultado debe contener las macros configuradas (`{#IFOPERSTATUS}`, `{#IFADMINSTATUS}`, `{#IFALIAS}`, `{#IFNAME}`, `{#IFDESCR}`, `{#IFTYPE}`, `{#SNMPINDEX}`).
            - **Guardar los primeros dos valores de `{#SNMPINDEX}`** que aparezcan (por ejemplo: `83886080` y `436207616`). Estos se usarán para probar los item prototypes más adelante.

        9. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

        > **Nota importante:** Para que se creen automáticamente los 
        items, triggers y gráficos, es necesario configurar **item 
        prototypes**, **trigger prototypes** y **graph prototypes** en 
        la regla de descubrimiento. Sin estos prototipos, el discovery 
        solo descubrirá las entidades pero no crea los elementos de 
        monitoreo automáticamente.

4. Crear **item prototypes** para monitorear las interfaces descubiertas:

    1. En la regla de descubrimiento creada, hacer clic en <span style="color: violet;"><strong>Item prototypes</strong></span> → <span style="color: blue;"><strong>Create item prototype</strong></span>

    2. Configurar el primer item prototype:

        1. Nombre del item *(parámetro obligatorio)*.

            → Name: `Interface {#IFDESCR}({#IFALIAS}): Operational status`

        2. Tipo de verificación

            → Type: `SNMP agent`

        3. Clave *(parámetro obligatorio, debe ser único y no debe coincidir con ninguna otro item del host)*.

            → Key: `net.if.status[{#SNMPINDEX}]`

        4. Tipo de información.

            → Type of information: `Numeric (unsigned)`

        5. Identificador de objetos SNMP *(parámetro obligatorio)*

            → SNMP OID: `1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}`

        6. Frecuencia de consulta *(parámetro obligatorio)*.

            → Update interval: `1m`

        7. Dejar el resto de los parámetros por defecto.

        8. *Opcionalmente* se puede agregar una descripción.

            → Description: `El estado operativo actual de la interfaz. Sus valores pueden ser: 1-up/activo, 2-down/inactivo, 3-testing/prueba, 4-unknown/desconocido, 5-dormant/inactivo, 6-notPresent/no presente, 7-lowerLayerDown/capa inferior inactiva.`

            > **💡 ¿Qué son las MIBs y para qué se usan?**
            >
            > Las **MIBs (Management Information Base)** son archivos de texto que definen la estructura de datos disponibles a través de SNMP. Contienen:
            > - **OIDs (Object Identifiers)** → Direcciones numéricas únicas de cada objeto monitoreable.
            > - **Nombres descriptivos** → Nombres legibles para los OIDs (ej: `ifOperStatus`).
            > - **Tipos de datos** → Qué tipo de valor devuelve cada OID (entero, string, etc.).
            > - **Descripciones y valores posibles** → Qué significa cada valor (como los estados de la interfaz: 1=up, 2=down, etc.).
            >
            > **¿Cómo se usan?**
            > - Se consultan las MIBs para encontrar los **OIDs correctos** que necesitamos monitorear.
            > - Permiten entender qué **datos están disponibles** en un dispositivo SNMP.
            > - Ayudan a interpretar los **valores devueltos** por el dispositivo.
            >
            > **Ejemplo:** En este caso, consultamos la [IF-MIB](https://mibs.observium.org/mib/IF-MIB/) para obtener el OID `1.3.6.1.2.1.2.2.1.8` (ifOperStatus) y entender qué significan sus valores (1=up, 2=down, etc.).

        9. *Opcionalmente* se puede agregar uno o más tags (etiquetas)

            - Name: `component` | Value: `network`
            - Name: `interface` | Value: `{#IFDESCR}`

        10. Probar el item prototype antes de guardar:

            - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> y luego en la nueva ventana en el campo **Macros**, reemplazar `{#SNMPINDEX}` con uno de los valores guardados anteriormente (por ejemplo: `83886080`).
            - Hacer clic en <span style="color: blue;"><strong>Get value and test</strong></span> y verificar que se obtenga un valor numérico (1, 2, 3, etc.) que representa el estado operativo de la interfaz.
            - Repetir el test con el segundo valor de SNMPINDEX guardado (por ejemplo: `436207616`) para confirmar que funciona correctamente.

        11. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    3. Configurar el segundo item prototype:

        1. Nombre del item *(parámetro obligatorio)*.

            → Name: `Interface {#IFDESCR}({#IFALIAS}): Administrative status`

        2. Tipo de verificación

            → Type: `SNMP agent`

        3. Clave *(parámetro obligatorio, debe ser único y no debe coincidir con ninguna otro item del host)*.

            → Key: `net.if.adminstatus[{#SNMPINDEX}]`

        4. Tipo de información.

            → Type of information: `Numeric (unsigned)`

        5. Identificador de objetos SNMP *(parámetro obligatorio)*

            → SNMP OID: `1.3.6.1.2.1.2.2.1.7.{#SNMPINDEX}`

        6. Frecuencia de consulta *(parámetro obligatorio)*.

            → Update interval: `1m`

        7. Dejar el resto de los parámetros por defecto.

        8. *Opcionalmente* se puede agregar una descripción.

            → Description: `El estado administrativo de la interfaz (configurado por el administrador). Sus valores pueden ser: 1-up/activado, 2-down/desactivado, 3-testing/prueba.`

        9. *Opcionalmente* se puede agregar uno o más tags (etiquetas)

            - Name: `component` | Value: `network`
            - Name: `interface` | Value: `{#IFDESCR}`

        10. Probar el item prototype antes de guardar:

            - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> y luego en la nueva ventana en el campo **Macros**, reemplazar `{#SNMPINDEX}` con uno de los valores guardados anteriormente (por ejemplo: `83886080`).
            - Hacer clic en <span style="color: blue;"><strong>Get value and test</strong></span> y verificar que se obtenga un valor numérico (1, 2 o 3) que representa el estado administrativo de la interfaz.
            - Repetir el test con el segundo valor de SNMPINDEX guardado (por ejemplo: `436207616`) para confirmar que funciona correctamente.

        11. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

5. Ejecutar la regla de descubrimiento y verificar los items creados automáticamente:

    1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → seleccionar el host **"SW-Demo1"** → pestaña <span style="color: violet;"><strong>Discovery</strong></span>.

    2. Localizar la regla **"Network Interfaces Discovery"** y hacer clic en <span style="color: blue;"><strong>Execute now</strong> (Ejecutar ahora)</span> para ejecutar la regla manualmente sin esperar el intervalo configurado (1 hora).

    3. Esperar unos minutos para que Zabbix procese la regla de descubrimiento y cree los items automáticamente.

    4. Ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> del host **"SW-Demo1"** y verificar que se hayan creado items para cada interfaz descubierta (usando las macros `{#IFDESCR}`, `{#IFALIAS}` y `{#SNMPINDEX}`), esperar `1m` a que se consulten los datos (o ejecutar manualmente el item con <span style="color: blue;"><strong>Execute now</strong> (Ejecutar ahora)</span>).

    5. Verificar la conectividad del host:

        - Verificar la columna **Availability**:
            - <span style="color: green;">🟢 Verde</span> → Host disponible y agente respondiendo.
            - <span style="color: red;">🔴 Rojo</span> → Host no disponible o agente no responde.
            - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

        > **Nota:** Puede tomar unos minutos para que el estado cambie de gris a verde/rojo según la conectividad.

    6. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host para ver las métricas recolectadas.

> **💡 Nota:** Los **trigger prototypes** y **graph prototypes** se pueden configurar de manera similar. Los triggers se verán en detalle en el Módulo 7.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="5.3. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="5.3. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_3.png" alt="5.3. Ejercicio práctico - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_4.png" alt="5.3. Ejercicio práctico - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_6.png" alt="5.3. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_7.png" alt="5.3. Ejercicio práctico - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_8.png" alt="5.3. Ejercicio práctico - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_9.png" alt="5.3. Ejercicio práctico - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_10.png" alt="5.3. Ejercicio práctico - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_11.png" alt="5.3. Ejercicio práctico - Captura 11" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/5.3.%20Ejercicio%20pr%C3%A1ctico_12.png" alt="5.3. Ejercicio práctico - Captura 12" style="max-width: 100%; height: auto;">

</div>

</details>
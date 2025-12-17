# **Ejercicio integrador**

**Objetivo:** Realizar un ejercicio práctico completo que combine lo visto en los **primeros 5 módulos**. Crear un host, configurar un template con items y reglas de descubrimiento, y aplicar buenas prácticas de monitoreo.

---

### **1. Crear un nuevo host**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → <span style="color: blue;"><strong>Create host</strong></span>.

2. Configurar:

    1. Nombre del host *(parámetro obligatorio)*.

        → Host name: `SW-Demo2`

    2. **No asociar template** (dejar sin template por ahora).

    3. Elegir un Grupo de hosts *(parámetro obligatorio)*.

        → Groups: `demo` y crear grupo `Switches`

    4. Configurar **interfaces** para el método de monitoreo con **SNMP**:

        → Interfaces: <span style="color: blue;"><strong>Add</strong> (Guardar)</span> y seleccionar **SNMP** quedando 'Type: SNMP'.

        → IP address: `10.0.10.2`

        → Port: `161` *(protocolo por defecto para SNMP)*

        → SNMP version: `SNMPv2`

        → Community: `{$SNMP_COMMUNITY}` *(por defecto aparece esta macro, **no cambiar el valor aquí**)*

        > **💡 ¿Por qué usar macros en lugar de valores directos?**
        >
        > Las **macros** permiten centralizar configuraciones y reutilizarlas en múltiples hosts. En lugar de escribir el mismo valor (como la community SNMP) en cada host, se define una vez como macro y se referencia con `{$MACRO}`. Esto facilita:
        > - **Mantenimiento**: Si cambia la community, solo se actualiza en un lugar.
        > - **Seguridad**: Al usar **Secret Text**, el valor se oculta en la interfaz y no se muestra en logs o exportaciones.
        > - **Flexibilidad**: Diferentes hosts pueden usar diferentes valores de la misma macro según su contexto.
        >
        > **¿Por qué Secret Text?**
        >
        > Las credenciales y valores sensibles (como communities SNMP, contraseñas, tokens) deben configurarse como **Secret Text** para:
        > - Ocultar el valor en la interfaz web (se muestra como asteriscos).
        > - Prevenir que aparezcan en logs, exportaciones o capturas de pantalla.
        > - Mejorar la seguridad general del sistema de monitoreo.

    5. Configurar la **macro** para la community SNMP:

        → Ir a la pestaña <span style="color: violet;"><strong>Macros</strong></span> del host y crear una nueva macro:

        - Macro: `{$SNMP_COMMUNITY}`
        - Value: `snmp-demo`
        - Type: Seleccionar **Secret Text** *(oculta el valor en la interfaz)*
        - Description: `Community SNMPv2`

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    6. *Opcionalmente* se puede agregar una descripción.

        → Description: `Switch virtual Cisco Nexus 9000`

    7. Configurar el **inventario** del host:

        → Ir a la pestaña <span style="color: violet;"><strong>Inventory</strong></span> del host.

        → Cambiar el modo de **Disabled** a **Automatic** *(necesario para que los items asociados al inventario puedan poblar automáticamente los campos)*

        > **💡 Nota:** El modo **Automatic** permite que los items configurados con "Populates host inventory field" actualicen automáticamente los campos del inventario del host.

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    9. No verificar la conectividad del host.

        > **Nota:** Puede tomar unos minutos para que el estado cambie de gris a verde/rojo según la conectividad.

---

### **2. Crear un template y configurar items**

1. Crear un nuevo template:

    1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → <span style="color: blue;"><strong>Create template</strong></span>

    2. Configurar:

        1. Nombre del template *(parámetro obligatorio)*.

            → Template name: `Template Network Switch by SNMP`

        2. Elegir un Grupo de templates *(parámetro obligatorio)*.

            → Groups: `Templates/Network devices`

        3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

2. Crear items en el template:

    1. <u>Item: <strong>System name</strong></u> *(seguir los mismos pasos del ejercicio práctico <a href="ejercicio-5.3.md"><strong>5.3</strong></a>)*

        1. En el template creado, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

        2. Configurar:

            - Name: `System Name`
            - Type: `SNMP agent`
            - Key: `system.name`
            - Type of information: `Character`
            - SNMP OID: `1.3.6.1.2.1.1.5.0`
            - Update interval: `1h`
            - Description: `Nombre asignado administrativamente para este nodo gestionado. Por convención, este es el nombre de dominio completamente calificado (FQDN) del nodo. Si el nombre es desconocido, el valor es una cadena de longitud cero.`

                > **💡 Nota:** Este OID pertenece a la MIB [SNMPv2-MIB](https://mibs.observium.org/mib/SNMPv2-MIB/).

            - Populates host inventory field: seleccionar `Name` del menú desplegable.

                Esto hará que el valor de este item se use automáticamente para poblar el campo "Name" del inventario del host.

                > **💡 ¿Qué significa asociar un item al inventario?**
                >
                > Al asociar un item al inventario del host, el valor del item se usa automáticamente para llenar un campo específico del inventario del host (como el nombre, sistema operativo, ubicación, etc.). Esto permite mantener información del inventario actualizada automáticamente sin intervención manual.
                >
                > **Importante:** Solo se puede asociar **un item a un campo de inventario**. No pueden haber varios items asociados al mismo campo de inventario (por ejemplo, no puede haber dos items diferentes asociados al campo "Name").

        4. *Opcionalmente* se puede agregar uno o más tags (etiquetas)
            - Name: `component` | Value: `system`

        5. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    2. <u><strong>OPCIONAL:</strong></u> Investigación de la MIB SNMPv2-MIB y creación de nuevos items en el template

        > **💡 Objetivo:** Investigar la MIB **SNMPv2-MIB** y crear items adicionales para monitorear información del sistema.

        1. Consultar la MIB **SNMPv2-MIB** en: [SNMPv2-MIB](https://mibs.observium.org/mib/SNMPv2-MIB/)

        2. Crear nuevos items en el template para monitorear los siguientes objetos del sistema (en este caso a todos los OIDs que van a agregar deben agregarle al final `.0`):

            - **System Description** (`sysDescr`)
            - **System Object ID** (`sysObjectID`)
            - **System Uptime** (`sysUpTime`)
            - Y otros que consideren importantes (por ejemplo: `sysContact`, `sysLocation`)

            > **💡 ¿Por qué determinados OIDs deben terminar en `.0`?**
            >
            > Los OIDs en la MIB SNMPv2-MIB que terminan en `.0` son **objetos escalares** en SNMP. Esto significa que representan un **único valor** para todo el sistema, a diferencia de los objetos tabulares (como las interfaces de red) que tienen múltiples instancias identificadas por índices (1, 2, 3, etc.).
            >
            > En la MIB SNMPv2-MIB, los objetos del sistema (`sysDescr`, `sysObjectID`, `sysUpTime`, etc.) son escalares porque cada dispositivo tiene solo **un** nombre de sistema, **una** descripción, **un** tiempo de actividad, etc. Por eso sus OIDs terminan en `.0` (índice 0), indicando que es la única instancia de ese objeto.

        3. Para cada item, analizar y configurar:

            - **Type of information**: Determinar si es texto (Character), numérico (Numeric), etc., según el tipo de dato que devuelve el OID.
            - **Units**: Verificar si el objeto requiere unidades de medida (por ejemplo, tiempo, porcentaje, etc.).
            - **Update interval**: Establecer un intervalo de actualización adecuado según la frecuencia con la que cambia el dato:
                - Datos estáticos (que no cambian frecuentemente): intervalos largos (1h, 24h).
                - Datos dinámicos (que cambian constantemente): intervalos cortos (1m, 5m).
            - **Populates host inventory field**: Considerar si alguno de estos items puede asociarse a un campo del inventario del host (por ejemplo, System Description puede asociarse a "OS (full details)"). Recordar que solo un item puede asociarse a cada campo de inventario.

        4. Agregar descripciones y tags apropiados a cada item.

        <details>
        <summary><strong>📋 Solución - Items sugeridos de SNMPv2-MIB</strong></summary>

        <div>

        <p>A continuación se muestran los items recomendados con sus configuraciones:</p>

        <h4><strong>1. System Description</strong></h4>

        <ul>
        <li>Name: <code>System Description</code></li>
        <li>Type: <code>SNMP agent</code></li>
        <li>Key: <code>system.description</code></li>
        <li>Type of information: <code>Character</code></li>
        <li>SNMP OID: <code>1.3.6.1.2.1.1.1.0</code></li>
        <li>Update interval: <code>1h</code> <em>(dato estático que rara vez cambia)</em></li>
        <li>Description: <code>Descripción textual del sistema, incluyendo el nombre del sistema operativo, versión del software y hardware.</code></li>
        <li><strong>Populates host inventory field</strong>: <code>OS (full details)</code> <em>(asociar este item al inventario para poblar automáticamente la información del sistema operativo)</em></li>
        <li>Tags: Name: <code>component</code> | Value: <code>system</code></li>
        </ul>

        <h4><strong>2. System Object ID</strong></h4>

        <ul>
        <li>Name: <code>System Object ID</code></li>
        <li>Type: <code>SNMP agent</code></li>
        <li>Key: <code>system.objectid</code></li>
        <li>Type of information: <code>Character</code></li>
        <li>SNMP OID: <code>1.3.6.1.2.1.1.2.0</code></li>
        <li>Update interval: <code>24h</code> <em>(dato estático que identifica el tipo de dispositivo)</em></li>
        <li>Description: <code>Identificador del objeto del sistema que identifica el tipo de dispositivo o sistema gestionado.</code></li>
        <li>Tags: Name: <code>component</code> | Value: <code>system</code></li>
        </ul>

        <h4><strong>3. System Uptime</strong></h4>

        <ul>
        <li>Name: <code>System Uptime</code></li>
        <li>Type: <code>SNMP agent</code></li>
        <li>Key: <code>system.uptime</code></li>
        <li>Type of information: <code>Numeric (unsigned)</code></li>
        <li>SNMP OID: <code>1.3.6.1.2.1.1.3.0</code></li>
        <li>Units: <code>uptime</code> <em>(Zabbix convertirá automáticamente TimeTicks a formato legible)</em></li>
        <li>Update interval: <code>1m</code> <em>(dato dinámico que cambia constantemente)</em></li>
        <li>Description: <code>Tiempo transcurrido desde el último reinicio del sistema, medido en centésimas de segundo (TimeTicks).</code></li>
        <li>Tags: Name: <code>component</code> | Value: <code>system</code></li>
        <li><strong>Preprocessing</strong>:
            <ul>
            <li>Ir a la pestaña <strong>Preprocessing</strong> del item, <span style="color: blue;"><strong>Add</strong> (Agregar)</span> un 'Preprocessing step':</li>
            <li>Name: <code>Custom multiplier</code></li>
            <li>Parameters: <code>0.01</code> <em>(necesario porque el dato está medido en centésimas de segundo - TimeTicks)</em></li>
            </ul>
        </li>
        </ul>

        <h4><strong>4. System Contact (Opcional)</strong></h4>

        <ul>
        <li>Name: <code>System Contact</code></li>
        <li>Type: <code>SNMP agent</code></li>
        <li>Key: <code>system.contact</code></li>
        <li>Type of information: <code>Character</code></li>
        <li>SNMP OID: <code>1.3.6.1.2.1.1.4.0</code></li>
        <li>Update interval: <code>24h</code> <em>(dato administrativo que rara vez cambia)</em></li>
        <li>Description: <code>Información de contacto de la persona responsable de este sistema gestionado.</code></li>
        <li><strong>Populates host inventory field</strong>: <code>Contact</code> <em>(asociar este item al inventario para poblar automáticamente la información del contacto)</em></li>
        <li>Tags: Name: <code>component</code> | Value: <code>system</code></li>
        </ul>

        <h4><strong>5. System Location (Opcional)</strong></h4>

        <ul>
        <li>Name: <code>System Location</code></li>
        <li>Type: <code>SNMP agent</code></li>
        <li>Key: <code>system.location</code></li>
        <li>Type of information: <code>Character</code></li>
        <li>SNMP OID: <code>1.3.6.1.2.1.1.6.0</code></li>
        <li>Update interval: <code>24h</code> <em>(dato administrativo que rara vez cambia)</em></li>
        <li>Description: <code>Ubicación física del sistema gestionado.</code></li>
        <li><strong>Populates host inventory field</strong>: <code>Location</code> <em>(asociar este item al inventario para poblar automáticamente la información de la ubicación)</em></li>
        <li>Tags: Name: <code>component</code> | Value: <code>system</code></li>
        </ul>

        <blockquote>
        <p><strong>💡 Nota importante:</strong> Todos estos OIDs pertenecen a la MIB <a href="https://mibs.observium.org/mib/SNMPv2-MIB/">SNMPv2-MIB</a> y son estándar para todos los dispositivos SNMP. El OID <code>sysUpTime</code> devuelve valores en <strong>TimeTicks</strong> (centésimas de segundo), pero Zabbix puede convertirlos automáticamente a formato legible si se usa la unidad <code>uptime</code>.</p>
        </blockquote>

        </div>

        </details>

    3. **Item: Memory utilization**

        1. <span style="color: blue;"><strong>Create item</strong></span>

        2. Configurar:

            - Name: `Memory utilization`
            - Type: `SNMP agent`
            - Key: `cseSysMemoryUtilization`
            - Type of information: `Numeric (unsigned)`
            - SNMP OID: `1.3.6.1.4.1.9.9.305.1.1.2.0`
            - Units: `%`
            - Update interval: `1m`
            - Description: `La utilización promedio de memoria en el supervisor activo.`

            > **💡 Nota:** Este OID pertenece a la MIB **CISCO-SYSTEM-EXT-MIB**. Ver más información en: [CISCO-SYSTEM-EXT-MIB](http://www.circitor.fr/Mibs/Html/C/CISCO-SYSTEM-EXT-MIB.php)

        3. *Opcionalmente* se puede agregar uno o más tags (etiquetas)
            - Name: `component` | Value: `memory`

        4. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

### **3. Configurar Value Mappings en el template**

> **💡 ¿Qué es Value Mapping y para qué sirve?**
>
> Los **Value Mappings** (mapeos de valores) son una funcionalidad de Zabbix que permite **traducir valores numéricos en texto legible** para facilitar la interpretación de los datos monitoreados.
>
> **¿Para qué se usan?**
> - **Mejorar la legibilidad**: En lugar de ver números como `1`, `2`, `3` en los dashboards y reportes, se muestran valores descriptivos como `up`, `down`, `testing`.
> - **Facilitar la interpretación**: Los operadores pueden entender rápidamente el estado de un dispositivo sin necesidad de consultar documentación técnica.
> - **Estándares de la industria**: Muchos protocolos como SNMP devuelven valores numéricos codificados según estándares (como IF-MIB para interfaces de red). Los Value Mappings permiten mostrar estos valores según su significado estándar.
>
> **Ejemplo práctico**: En este ejercicio, cuando Zabbix monitorea el estado operativo de una interfaz de red, recibe valores numéricos (`1`, `2`, `3`, etc.). Con los Value Mappings configurados, estos números se mostrarán como `up`, `down`, `testing`, etc., haciendo mucho más fácil entender el estado de las interfaces en los dashboards y reportes.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Template creado <span style="color: blue;"><strong>`Template Network Switch by SNMP`</strong></span>  → <span style="color: violet;"><strong>Value mapping</strong></span> → <span style="color: blue;"><strong>Create value map</strong></span>

2. Crear Value Mapping para **IF-MIB::ifOperStatus**:

    - Name: `IF-MIB::ifOperStatus`
    - Mappings:
        - Value: `1` → Mapped to: `up`
        - Value: `2` → Mapped to: `down`
        - Value: `3` → Mapped to: `testing`
        - Value: `4` → Mapped to: `unknown`
        - Value: `5` → Mapped to: `dormant`
        - Value: `6` → Mapped to: `notPresent`
        - Value: `7` → Mapped to: `lowerLayerDown`

    - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. Crear Value Mapping para **IF-MIB::ifAdminStatus**:

    - Name: `IF-MIB::ifAdminStatus`
    - Mappings:
        - Value: `1` → Mapped to: `up`
        - Value: `2` → Mapped to: `down`
        - Value: `3` → Mapped to: `testing`

    - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

### **4. Configurar reglas de descubrimiento en el template**

1. En el template **"Template Network Switch by SNMP"**, ir a la pestaña <span style="color: violet;"><strong>Discovery</strong></span> → <span style="color: blue;"><strong>Create discovery rule</strong></span>

2. <u>Regla de descubrimiento: <strong>Network Interfaces Discovery</strong></u> *(seguir los mismos pasos del ejercicio práctico <a href="ejercicio-5.3.md"><strong>5.3</strong></a>)*

    1. Name: `Network Interfaces Discovery`
    2. Type: `SNMP agent`
    3. Key: `net.if.discovery`
    4. SNMP OID: `discovery[{#IFOPERSTATUS},1.3.6.1.2.1.2.2.1.8,{#IFADMINSTATUS},1.3.6.1.2.1.2.2.1.7,{#IFALIAS},1.3.6.1.2.1.31.1.1.1.18,{#IFNAME},1.3.6.1.2.1.31.1.1.1.1,{#IFDESCR},1.3.6.1.2.1.2.2.1.2,{#IFTYPE},1.3.6.1.2.1.2.2.1.3]`
    5. Update interval: `1h`
    6. Keep lost resources period: `30d`
    7. Description: `Descubriendo interfaces desde [IF-MIB](https://mibs.observium.org/mib/IF-MIB/).`
    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. <u>Regla de descubrimiento: <strong>CPU Discovery</strong></u>

    1. Name: `CPU Discovery`
    2. Type: `SNMP agent`
    3. Key: `cpu.discovery`
    4. SNMP OID: `discovery[{#SNMPVALUE},1.3.6.1.4.1.9.9.109.1.1.1.1.2]`
    5. Update interval: `1h`
    6. Keep lost resources period: `30d`
    7. Description: `Descubriendo CPUs del dispositivo.`
    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

### **5. Configurar item prototypes en la reglas de descubrimientos creadas**

1. <u>Item prototype: <strong>Operational status</strong></u> *(seguir los mismos pasos del ejercicio práctico <a href="ejercicio-5.3.md"><strong>5.3</strong></a>)*

    1. En la regla de descubrimiento **"Network Interfaces Discovery"**, ir a la pestaña <span style="color: violet;"><strong>Item prototypes</strong></span> → <span style="color: blue;"><strong>Create item prototype</strong></span>

        - Name: `Interface {#IFDESCR}({#IFALIAS}): Operational status`
        - Type: `SNMP agent`
        - Key: `net.if.status[{#SNMPINDEX}]`
        - Type of information: `Numeric (unsigned)`
        - SNMP OID: `1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}`
        - Update interval: `1m`
        - Dejar el resto de los parámetros por defecto.
        - *Opcionalmente* Description: `El estado operativo actual de la interfaz. Sus valores pueden ser: 1-up/activo, 2-down/inactivo, 3-testing/prueba, 4-unknown/desconocido, 5-dormant/inactivo, 6-notPresent/no presente, 7-lowerLayerDown/capa inferior inactiva.`
        - *Opcionalmente* se puede agregar uno o más tags (etiquetas):
            - Name: `component` | Value: `network`
            - Name: `interface` | Value: `{#IFDESCR}`
        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

2. <u>Item prototype: <strong>CPU Utilization</strong></u>

    1. En la regla de descubrimiento **"CPU Discovery"**, ir a la pestaña <span style="color: violet;"><strong>Item prototypes</strong></span> → <span style="color: blue;"><strong>Create item prototype</strong></span>

        - Name: `CPU Utilization {#SNMPVALUE}`
        - Type: `SNMP agent`
        - Key: `cpu.utilization[{#SNMPVALUE}]`
        - Type of information: `Numeric (unsigned)`
        - SNMP OID: `1.3.6.1.4.1.9.9.109.1.1.1.1.8.{#SNMPINDEX}`
        - Units: `%`
        - Update interval: `1m`
        - *Opcionalmente* Description: `Utilización promedio de CPU durante 5 minutos. Este OID proporciona una vista más precisa del rendimiento del router a lo largo del tiempo. El umbral recomendado es del 90%, aunque puede variar según la plataforma del dispositivo.`
            > **💡 Nota:** Este OID pertenece a la MIB [CISCO-PROCESS-MIB](https://mibs.observium.org/mib/CISCO-PROCESS-MIB/) y corresponde al objeto `cpmCPUTotal5minRev`, que proporciona una medición más precisa que los intervalos de 1 minuto o 5 segundos.
        - *Opcionalmente* se puede agregar uno o más tags (etiquetas):
            - Name: `component` | Value: `cpu`
        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. <u><strong>OPCIONAL:</strong></u><strong> Investigación de la MIB IF-MIB y creación de nuevos items prototypes pertenecientes al Discovery en el template</strong>

    > **💡 Objetivo:** Investigar la MIB **IF-MIB** y crear nuevos item prototypes para monitorear información de las interfaces de red descubiertas.

    1. Consultar la MIB **IF-MIB** en: [IF-MIB](https://mibs.observium.org/mib/IF-MIB/)

    2. Crear nuevos item prototypes en la regla de descubrimiento **"Network Interfaces Discovery"** para monitorear:

        - **Administrative status** (`ifAdminStatus`)
        - **Name** (`ifName`)
        - **Name Alias** (`ifAlias`)
        - **Interface type** (`ifType`)
        - Y otros que consideren importantes

        > **💡 Nota sobre OIDs tabulares:**
        >
        > A diferencia de los objetos escalares de SNMPv2-MIB que terminan en `.0`, los objetos de IF-MIB son **tabulares** y requieren un índice `{#SNMPINDEX}` para identificar cada interfaz. Por ejemplo:
        > - `1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}` → Estado operativo de la interfaz con índice `{#SNMPINDEX}`
        > - `1.3.6.1.2.1.31.1.1.1.1.{#SNMPINDEX}` → Nombre de la interfaz con índice `{#SNMPINDEX}`
        >
        > El macro `{#SNMPINDEX}` es descubierto automáticamente por la regla de descubrimiento y representa el índice SNMP de cada interfaz.

    3. Para cada item prototype, analizar y configurar:

        - **Type of information**: Determinar si es texto (Character), numérico (Numeric), etc., según el tipo de dato que devuelve el OID.
        - **Value mapping**: Aplicar cuando corresponda (por ejemplo, para Operational status y Administrative status).
        - **Update interval**: Establecer un intervalo de actualización adecuado según la frecuencia con la que cambia el dato:
            - Datos estáticos (nombre, tipo, alias): intervalos largos (1h, 24h).
            - Datos dinámicos (estados operativos): intervalos cortos (1m, 5m).
        - **Tags**: Agregar tags apropiados para facilitar el filtrado y organización.

    4. Agregar descripciones y tags apropiados a cada item prototype.

    <details>
    <summary><strong>📋 Solución - Item prototypes sugeridos de IF-MIB</strong></summary>

    <div>

    <p>A continuación se muestran los item prototypes recomendados con sus configuraciones:</p>

    <h4><strong>1. Operational status</strong></h4>

    <ul>
    <li>Name: <code>Interface {#IFDESCR}({#IFALIAS}): Operational status</code></li>
    <li>Type: <code>SNMP agent</code></li>
    <li>Key: <code>net.if.status[{#SNMPINDEX}]</code></li>
    <li>Type of information: <code>Numeric (unsigned)</code></li>
    <li>SNMP OID: <code>1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}</code></li>
    <li>Update interval: <code>1m</code> <em>(dato dinámico que cambia según el estado de la interfaz)</em></li>
    <li><strong>Value mapping</strong>: Seleccionar <code>IF-MIB::ifOperStatus</code> (creado anteriormente)</li>
    <li>Description: <code>Estado operativo actual de la interfaz. Indica si la interfaz está funcionando correctamente.</code></li>
    <li><strong>Tags</strong>:
        <ul>
        <li>Name: <code>component</code> | Value: <code>network</code></li>
        <li>Name: <code>interface</code> | Value: <code>{#IFDESCR}</code></li>
        </ul>
    </li>
    <li><span style="color: blue;"><strong>Add</strong> (Guardar)</span></li>
    </ul>

    <h4><strong>2. Administrative status</strong></h4>

    <ul>
    <li>Name: <code>Interface {#IFDESCR}({#IFALIAS}): Administrative status</code></li>
    <li>Type: <code>SNMP agent</code></li>
    <li>Key: <code>net.if.adminstatus[{#SNMPINDEX}]</code></li>
    <li>Type of information: <code>Numeric (unsigned)</code></li>
    <li>SNMP OID: <code>1.3.6.1.2.1.2.2.1.7.{#SNMPINDEX}</code></li>
    <li>Update interval: <code>1m</code> <em>(dato dinámico que puede cambiar cuando se configura la interfaz)</em></li>
    <li><strong>Value mapping</strong>: Seleccionar <code>IF-MIB::ifAdminStatus</code> (creado anteriormente)</li>
    <li>Description: <code>Estado administrativo de la interfaz. Indica si la interfaz está habilitada o deshabilitada por el administrador.</code></li>
    <li><strong>Tags</strong>:
        <ul>
        <li>Name: <code>component</code> | Value: <code>network</code></li>
        <li>Name: <code>interface</code> | Value: <code>{#IFDESCR}</code></li>
        </ul>
    </li>
    <li><span style="color: blue;"><strong>Add</strong> (Guardar)</span></li>
    </ul>

    <h4><strong>3. Name</strong></h4>

    <ul>
    <li>Name: <code>Interface {#IFDESCR}({#IFALIAS}): Name</code></li>
    <li>Type: <code>SNMP agent</code></li>
    <li>Key: <code>net.if.name[{#SNMPINDEX}]</code></li>
    <li>Type of information: <code>Text</code></li>
    <li>SNMP OID: <code>1.3.6.1.2.1.31.1.1.1.1.{#SNMPINDEX}</code></li>
    <li>Update interval: <code>24h</code> <em>(dato estático que identifica el nombre de la interfaz)</em></li>
    <li>Description: <code>Nombre textual de la interfaz, típicamente el nombre asignado por el sistema operativo.</code></li>
    <li><strong>Tags</strong>:
        <ul>
        <li>Name: <code>component</code> | Value: <code>network</code></li>
        <li>Name: <code>interface</code> | Value: <code>{#IFDESCR}</code></li>
        </ul>
    </li>
    <li><span style="color: blue;"><strong>Add</strong> (Guardar)</span></li>
    </ul>

    <h4><strong>4. Name Alias</strong></h4>

    <ul>
    <li>Name: <code>Interface {#IFDESCR}({#IFALIAS}): Name Alias</code></li>
    <li>Type: <code>SNMP agent</code></li>
    <li>Key: <code>net.if.alias[{#SNMPINDEX}]</code></li>
    <li>Type of information: <code>Text</code></li>
    <li>SNMP OID: <code>1.3.6.1.2.1.2.2.1.2.{#SNMPINDEX}</code></li>
    <li>Update interval: <code>24h</code> <em>(dato estático que rara vez cambia)</em></li>
    <li>Description: <code>Alias o descripción textual de la interfaz, típicamente asignado por el administrador para facilitar la identificación.</code></li>
    <li><strong>Tags</strong>:
        <ul>
        <li>Name: <code>component</code> | Value: <code>network</code></li>
        <li>Name: <code>interface</code> | Value: <code>{#IFDESCR}</code></li>
        </ul>
    </li>
    <li><span style="color: blue;"><strong>Add</strong> (Guardar)</span></li>
    </ul>

    <h4><strong>5. Interface type</strong></h4>

    <ul>
    <li>Name: <code>Interface {#IFDESCR}({#IFALIAS}): Interface type</code></li>
    <li>Type: <code>SNMP agent</code></li>
    <li>Key: <code>net.if.type[{#SNMPINDEX}]</code></li>
    <li>Type of information: <code>Numeric (unsigned)</code></li>
    <li>SNMP OID: <code>1.3.6.1.2.1.2.2.1.3.{#SNMPINDEX}</code></li>
    <li>Update interval: <code>24h</code> <em>(dato estático que identifica el tipo de interfaz)</em></li>
    <li>Description: <code>Tipo de interfaz según la enumeración IANAifType. Identifica si es Ethernet, WiFi, Serial, etc.</code></li>
    <li><strong>Tags</strong>:
        <ul>
        <li>Name: <code>component</code> | Value: <code>network</code></li>
        <li>Name: <code>interface</code> | Value: <code>{#IFDESCR}</code></li>
        </ul>
    </li>
    <li><span style="color: blue;"><strong>Add</strong> (Guardar)</span></li>
    </ul>

    <blockquote>
    <p><strong>💡 Nota importante:</strong> Todos estos OIDs pertenecen a la MIB <a href="https://mibs.observium.org/mib/IF-MIB/">IF-MIB</a> y son estándar para todos los dispositivos SNMP que implementan el monitoreo de interfaces de red. Los OIDs requieren el índice <code>{#SNMPINDEX}</code> porque son objetos tabulares que tienen múltiples instancias (una por cada interfaz del dispositivo).</p>
    </blockquote>

    </div>

    </details>

---

### **5. Aplicar el template al host**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → seleccionar el host **"SW-Demo2"**.

2. En la pestaña <span style="color: violet;"><strong>Templates</strong></span>, hacer clic en <span style="color: blue;"><strong>Select</strong></span> y buscar **"Template Network Switch by SNMP"**.

3. Seleccionar el template y hacer clic en <span style="color: blue;"><strong>Add</strong></span>.

4. <span style="color: blue;"><strong>Update</strong></span> (Actualizar).

5. Verificar que los items y las reglas de discovery se hayan aplicado al host.

---

### **6. Ejecutar y verificar**

1. Ejecutar las reglas de descubrimiento manualmente:

    - En el host **"SW-Demo2"**, ir a la pestaña <span style="color: violet;"><strong>Discovery</strong></span>.
    - Localizar las reglas de discovery y hacer clic en <span style="color: blue;"><strong>Execute now</strong></span> en cada una:
        - **Network Interfaces Discovery**
        - **CPU Discovery**
    - Esperar unos minutos para que se creen los items automáticamente y se consulten sus datos.

2. Verificar los items creados:

    - Ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> del host y verificar que se hayan creado los items del template y los item prototypes.
    - Seleccionar los items de tipo **SNMP agent** y hacer clic en <span style="color: blue;"><strong>Execute now</strong></span> para actualizar los datos.
    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host **"SW-Demo2"**.
    - Verificar que los items muestren valores y que los estados de las interfaces se muestren con los value mappings (up/down en lugar de números).

3. Verificar la conectividad del host **"SW-Demo2"**:

    - Verificar la columna **Availability**:
        - <span style="color: green;">🟢 Verde</span> → Host disponible y agente respondiendo.
        - <span style="color: red;">🔴 Rojo</span> → Host no disponible o agente no responde.
        - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

4. Verificar el **inventario del host**:

    - Verificar que los campos del inventario se hayan poblado automáticamente desde los items configurados:
        - **Name**: Debe estar tener el mismo valor que el item "System name".
        - **OS (full details)**: Debe estar tener el mismo valor que el item "System Description".
        - **Contact**: Debe estar tener el mismo valor que el item "System Contact".
        - **Location**: Debe estar tener el mismo valor que el item "System Location".

    - Se puede verificar de dos formas:
        - <span style="color: purple;"><strong>Inventory</strong></span> → <span style="color: violet;"><strong>Overview</strong></span> → **Resumen de datos de inventario** (vista general de todos los hosts).
        - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar el host **"SW-Demo2"** → Pestaña <span style="color: violet;"><strong>Inventory</strong></span> → **Información detallada de cada equipo** (vista específica del host).

    - Confirmar que los campos muestren los valores recolectados por los items asociados.

    > **Nota:** Puede tomar unos minutos para que se creen los items y se actualicen los datos en el inventario.

---

### **Resultado esperado**

Al finalizar el ejercicio, cada participante deberá:

- Tener **un host nuevo** (SW-Demo2) configurado y monitoreado con SNMP.
- Tener un **template** con items de sistema:
    - Items básicos: System name (asociado al inventario), System Description (asociado al inventario), System Object ID, System Uptime (con preprocessing), System Contact, System Location, Memory utilization.
    - **Mínimo requerido**: 1 item por template.
- Tener configurados correctamente **Value Mappings** para interpretar los estados de las interfaces de números a valores legibles (up/down):
    - `IF-MIB::ifOperStatus` (up, down, testing, etc.)
    - `IF-MIB::ifAdminStatus` (up, down, testing)
    - **Total**: 2 Value Mappings.
- Tener configuradas correctamente **dos reglas de Low-Level Discovery** (Network Interfaces, CPU) con **item prototypes** en el template:
    - **Network Interfaces Discovery**: Item prototypes para Operational status, Administrative status, Name, Name Alias, Interface type.
    - **CPU Discovery**: Item prototype para CPU Utilization.
    - **Total**: 2 discovery rules
    - **Mínimo requerido**: 2 item prototypes (uno por cada discovery rule)
- Tener agregados correctamente **tags** a los items e item prototypes para facilitar el filtrado y organización.
- Tener asociados correctamente items al **inventario del host**.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador1.png" alt="Ejercicio integrador - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador1_1.png" alt="Ejercicio integrador - Captura 1.1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador1_2.png" alt="Ejercicio integrador - Captura 1.2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador2.png" alt="Ejercicio integrador - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3.png" alt="Ejercicio integrador - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_1.png" alt="Ejercicio integrador - Captura 3.1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_2.png" alt="Ejercicio integrador - Captura 3.2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_3.png" alt="Ejercicio integrador - Captura 3.3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_4.png" alt="Ejercicio integrador - Captura 3.4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_5.png" alt="Ejercicio integrador - Captura 3.5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_6.png" alt="Ejercicio integrador - Captura 3.6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_7.png" alt="Ejercicio integrador - Captura 3.7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3_8.png" alt="Ejercicio integrador - Captura 3.8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador4.png" alt="Ejercicio integrador - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador4_1.png" alt="Ejercicio integrador - Captura 4.1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador4_2.png" alt="Ejercicio integrador - Captura 4.2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador5.png" alt="Ejercicio integrador - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6.png" alt="Ejercicio integrador - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6_1.png" alt="Ejercicio integrador - Captura 6.1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6_2.png" alt="Ejercicio integrador - Captura 6.2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6_3.png" alt="Ejercicio integrador - Captura 6.3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6_4.png" alt="Ejercicio integrador - Captura 6.4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6_5.png" alt="Ejercicio integrador - Captura 6.5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador7.png" alt="Ejercicio integrador - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador8.png" alt="Ejercicio integrador - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador9.png" alt="Ejercicio integrador - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador10.png" alt="Ejercicio integrador - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador11.png" alt="Ejercicio integrador - Captura 11" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador12.png" alt="Ejercicio integrador - Captura 12" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador13.png" alt="Ejercicio integrador - Captura 13" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador14.png" alt="Ejercicio integrador - Captura 14" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador15.png" alt="Ejercicio integrador - Captura 15" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador16.png" alt="Ejercicio integrador - Captura 16" style="max-width: 100%; height: auto;">

</div>

</details>
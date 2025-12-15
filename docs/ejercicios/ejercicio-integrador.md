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

        → Community: `snmp-demo`

    5. *Opcionalmente* se puede agregar una descripción.

        → Description: `Switch virtual Cisco Nexus 9000`

    6. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    7. Verificar la conectividad

        - Verificar la columna **Availability**:
            - <span style="color: green;">🟢 Verde</span> → Host disponible y agente respondiendo.
            - <span style="color: red;">🔴 Rojo</span> → Host no disponible o agente no responde.
            - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

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

    1. **Item: System name**

        1. En el template creado, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

        2. Configurar:
            - Name: `System name`
            - Type: `SNMP agent`
            - Key: `system.name`
            - Type of information: `Character`
            - SNMP OID: `1.3.6.1.2.1.1.5.0`
            - Update interval: `1h`
            - Description: `Nombre asignado administrativamente para este nodo gestionado. Por convención, este es el nombre de dominio completamente calificado (FQDN) del nodo. Si el nombre es desconocido, el valor es una cadena de longitud cero.`

            > **💡 Nota:** Este OID pertenece a la MIB **SNMPv2-MIB**.

        3. *Opcionalmente* se puede agregar uno o más tags (etiquetas)
            - Name: `component` | Value: `system`

        4. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    2. **Item: Memory utilization**

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

1. Crear Value Mapping para **IF-MIB::ifOperStatus**:

    1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Template creado <span style="color: blue;"><strong>`Template Network Switch by SNMP`</strong></span>  → <span style="color: violet;"><strong>Value mapping</strong></span> → <span style="color: blue;"><strong>Create value map</strong></span>

    2. Configurar:
        - Name: `IF-MIB::ifOperStatus`
        - Mappings:
            - Value: `1` → Mapped to: `up`
            - Value: `2` → Mapped to: `down`
            - Value: `3` → Mapped to: `testing`
            - Value: `4` → Mapped to: `unknown`
            - Value: `5` → Mapped to: `dormant`
            - Value: `6` → Mapped to: `notPresent`
            - Value: `7` → Mapped to: `lowerLayerDown`

    3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

2. Crear Value Mapping para **IF-MIB::ifAdminStatus**:

    1. <span style="color: blue;"><strong>Create value map</strong></span>

    2. Configurar:
        - Name: `IF-MIB::ifAdminStatus`
        - Mappings:
            - Value: `1` → Mapped to: `up`
            - Value: `2` → Mapped to: `down`
            - Value: `3` → Mapped to: `testing`

    3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

### **4. Configurar la regla de descubrimiento en el template**

1. En el template **"Template Network Switch by SNMP"**, ir a la pestaña <span style="color: violet;"><strong>Discovery</strong></span> → <span style="color: blue;"><strong>Create discovery rule</strong></span>

2. Configurar la regla de descubrimiento (seguir los mismos pasos del ejercicio práctico 5.3):

    1. Name: `Network Interfaces Discovery`
    2. Type: `SNMP agent`
    3. Key: `net.if.discovery`
    4. SNMP OID: `discovery[{#IFOPERSTATUS},1.3.6.1.2.1.2.2.1.8,{#IFADMINSTATUS},1.3.6.1.2.1.2.2.1.7,{#IFALIAS},1.3.6.1.2.1.31.1.1.1.18,{#IFNAME},1.3.6.1.2.1.31.1.1.1.1,{#IFDESCR},1.3.6.1.2.1.2.2.1.2,{#IFTYPE},1.3.6.1.2.1.2.2.1.3]`
    5. Update interval: `1h`
    6. Keep lost resources period: `30d`
    7. Description: `Descubriendo interfaces desde IF-MIB.`
    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. Crear **item prototypes** en la regla de descubrimiento (seguir los mismos pasos del ejercicio práctico 5.3):

    1. **Item prototype 1**: Operational status
        - Name: `Interface {#IFDESCR}({#IFALIAS}): Operational status`
        - Type: `SNMP agent`
        - Key: `net.if.status[{#SNMPINDEX}]`
        - Type of information: `Numeric (unsigned)`
        - SNMP OID: `1.3.6.1.2.1.2.2.1.8.{#SNMPINDEX}`
        - Update interval: `1m`
        - **Value mapping**: Seleccionar `IF-MIB::ifOperStatus` (creado anteriormente)
        - **Tags**:
            - Name: `component` | Value: `network`
            - Name: `interface` | Value: `{#IFDESCR}`
        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    2. **Item prototype 2**: Administrative status
        - Name: `Interface {#IFDESCR}({#IFALIAS}): Administrative status`
        - Type: `SNMP agent`
        - Key: `net.if.adminstatus[{#SNMPINDEX}]`
        - Type of information: `Numeric (unsigned)`
        - SNMP OID: `1.3.6.1.2.1.2.2.1.7.{#SNMPINDEX}`
        - Update interval: `1m`
        - **Value mapping**: Seleccionar `IF-MIB::ifAdminStatus` (creado anteriormente)
        - **Tags**:
            - Name: `component` | Value: `network`
            - Name: `interface` | Value: `{#IFDESCR}`
        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

4. Configurar regla de descubrimiento para **CPU Discovery**:

    1. En el template, ir a la pestaña <span style="color: violet;"><strong>Discovery</strong></span> → <span style="color: blue;"><strong>Create discovery rule</strong></span>

    2. Configurar:
        1. Name: `CPU Discovery`
        2. Type: `SNMP agent`
        3. Key: `cpu.discovery`
        4. SNMP OID: `discovery[{#SNMPVALUE},1.3.6.1.4.1.9.9.109.1.1.1.1.2]`
        5. Update interval: `1h`
        6. Keep lost resources period: `30d`
        7. Description: `Descubriendo CPUs del dispositivo.`
        8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    3. Crear **item prototype** para CPU:
        - Name: `CPU Utilization {#SNMPVALUE}`
        - Type: `SNMP agent`
        - Key: `cpu.utilization[{#SNMPVALUE}]`
        - Type of information: `Numeric (unsigned)`
        - SNMP OID: `1.3.6.1.4.1.9.9.109.1.1.1.1.8.{#SNMPINDEX}`
        - Units: `%`
        - Update interval: `1m`
        - Description: `Utilización promedio de CPU durante 5 minutos. Este OID proporciona una vista más precisa del rendimiento del router a lo largo del tiempo. El umbral recomendado es del 90%, aunque puede variar según la plataforma del dispositivo.`

            > **💡 Nota:** Este OID pertenece a la MIB **CISCO-PROCESS-MIB** y corresponde al objeto `cpmCPUTotal5minRev`, que proporciona una medición más precisa que los intervalos de 1 minuto o 5 segundos.

        - **Tags**:
            - Name: `component` | Value: `cpu`
        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

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
    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host **"SW-Demo2"**.
    - Verificar que los items muestren valores y que los estados de las interfaces se muestren con los value mappings (up/down en lugar de números).

---

### **Resultado esperado**

Al finalizar el ejercicio, cada participante deberá:

- Tener **un host nuevo** (SW-Demo2) configurado y monitoreado.
- Crear un **template** con items de sistema (System name, Memory utilization).
- Configurar **Value Mappings** para interpretar los estados de las interfaces.
- Configurar **dos reglas de Low-Level Discovery** (Network Interfaces, CPU) con **items prototypes** en el template.
- Agregar **tags** a los item prototypes para facilitar el filtrado y organización.
- Aplicar el template al host y verificar que todos los elementos se hayan creado correctamente.
- Verificar que los **value mappings** funcionen correctamente mostrando valores legibles (up/down) en lugar de números.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador1.png" alt="Ejercicio integrador - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador2.png" alt="Ejercicio integrador - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador3.png" alt="Ejercicio integrador - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador4.png" alt="Ejercicio integrador - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador5.png" alt="Ejercicio integrador - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20integrador6.png" alt="Ejercicio integrador - Captura 6" style="max-width: 100%; height: auto;">

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

</details>
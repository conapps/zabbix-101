# **6.4. Ejercicio práctico**

**Objetivo**: Configuración de **triggers** (disparadores) para alertar sobre problemas en interfaces de red, CPU y memoria utilizando el template **"Template Network Switch by SNMP"**.

**<u>Pasos guiados</u>**

> **💡 ¿Cómo crear un trigger?**
>
> Existen **dos formas** de crear un trigger en Zabbix:
>
> 1. **Desde un item**: Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar el host → Pestaña <span style="color: violet;"><strong>Items</strong></span> → Seleccionar un item → Hacer clic en <span style="color: blue;"><strong>Create trigger</strong></span> (Zabbix prellenará automáticamente el item en la expresión).
>
> 2. **Desde la pestaña Triggers**: Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar el host → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span> (deberás seleccionar manualmente el item en la expresión).
>
> **Recomendación**: Para este ejercicio, crearemos los triggers directamente en el **template** para que se apliquen automáticamente a todos los hosts que usen el template.

---

## **1. Crear trigger para interfaces de red (Link down)**

**Objetivo**: Crear un trigger que se active cuando una interfaz de red cambia a estado DOWN.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `Interface {#IFDESCR}({#IFALIAS}): Link down`

        > **💡 ¿Qué es el Name de un trigger?**
        >
        > El **Name** es el identificador del trigger que se muestra en listas, dashboards y reportes. Debe ser descriptivo y claro para que los operadores entiendan rápidamente qué problema está siendo monitoreado.

    2. **Event name** *(opcional)*:

        → Event name: *(dejar vacío o usar el mismo que Name)*

        > **💡 ¿Cuál es la diferencia entre Name y Event name?**
        >
        > - **Name**: Se usa como identificador del trigger en la interfaz de Zabbix (listas, dashboards, etc.). Puede contener macros como `{#IFDESCR}` que se expanden dinámicamente.
        >
        > - **Event name**: Se usa para identificar eventos específicos cuando el trigger se activa. Si no se define, se usa el Name. El Event name es útil cuando quieres que el nombre del evento sea diferente al nombre del trigger (por ejemplo, para incluir información adicional como valores de umbral).
        >
        > **Ejemplo**: Si el Name es "CPU alta" y el Event name es "CPU alta (>{$CPU.UTIL.AVG}% durante 5m)", el evento mostrará el valor del umbral en el nombre del evento, facilitando la identificación del problema.

    3. **Operational data** *(opcional)*:

        → Operational data: `Current state: {ITEM.LASTVALUE1}`

        > **💡 ¿Qué es Operational data?**
        >
        > El **Operational data** proporciona información adicional sobre el estado actual cuando el trigger se activa. Se muestra en la vista de Problems y puede incluir macros como `{ITEM.LASTVALUE1}`, `{ITEM.VALUE1}`, etc., para mostrar valores específicos del item que disparó el trigger.

    4. **Severity** *(parámetro obligatorio)*:

        → Severity: `High` *(Alta)*

    5. **Expression** *(parámetro obligatorio)*:

        → Expression: `last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}])=2 and last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}],#1)<>last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}],#2)`

        > **💡 ¿Qué hace esta expresión?**
        >
        > Esta expresión verifica dos condiciones:
        > 1. `last(...)=2` → El último valor del estado operativo es 2 (DOWN).
        > 2. `last(...,#1)<>last(...,#2)` → El valor anterior (#1) es diferente al valor anterior a ese (#2). Esto asegura que el trigger solo se active cuando el estado **cambia** a DOWN, no si ya estaba en DOWN.
        >
        > **¿Por qué es importante?**
        >
        > Esta configuración evita que se active la alerta para interfaces que están "eternamente apagadas" o que nunca han estado activas. Solo alerta cuando una interfaz que estaba funcionando (UP) cambia a DOWN, lo cual es un evento más crítico.

    6. **Recovery expression** *(opcional)*:

        → Recovery expression: `last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}])<>2`

        > **💡 ¿Qué es Recovery expression?**
        >
        > La **Recovery expression** define cuándo el trigger debe volver al estado OK. Si no se define, Zabbix usa la negación de la expresión principal. En este caso, el trigger se recupera automáticamente cuando el estado es distinto a DOWN (código 2), es decir, cuando la interfaz vuelve a estar UP.

    7. **Description** *(opcional)*:

        → Description: `Interfaz {#IFDESCR}({#IFALIAS}): Enlace caído. Este trigger se activa cuando el estado operativo de la Interfaz {#IFDESCR}({#IFALIAS}) cambia a DOWN (código 2). Si la interfaz es considerada importante, la alerta se activará solo si el estado operativo estaba en UP anteriormente. Esta configuración evita que se active la alerta para interfaces que están "eternamente apagadas". La alerta se recupera automáticamente solo cuando el estado es distinto a down (código 2).`

    8. **Tags** *(opcional)*:

        - Name: `scope` | Value: `availability`

    9. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **2. Crear macros a nivel de template para triggers**

> **💡 ¿Por qué crear macros a nivel de template?**
>
> Las **macros a nivel de template** permiten centralizar valores de umbrales y configuraciones que se usan en múltiples triggers. Esto facilita:
> - **Mantenimiento**: Cambiar un umbral en un solo lugar afecta a todos los triggers que lo usan.
> - **Flexibilidad**: Diferentes hosts pueden usar diferentes valores de la misma macro según su contexto.
> - **Estándares**: Permite definir valores estándar para toda la organización.
>
> **Ejemplo**: Si defines `{$CPU.UTIL.AVG}` como macro en el template con valor `75`, todos los triggers que usen esta macro usarán el mismo umbral. Si necesitas cambiar el umbral para un host específico, puedes sobrescribir la macro a nivel de host.

### **2.1. Crear macro para CPU**

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Macros</strong></span>

2. Crear la macro:
    - Hacer clic en <span style="color: blue;"><strong>Add</strong></span>
    - Configurar:
        - Macro: `{$CPU.UTIL.AVG}`
        - Value: `5` *(valor de demo para generar alertas fácilmente)*
        - Description: `Umbral promedio de utilización de CPU (%). Se activa alerta cuando la CPU supera este valor durante el período configurado.`

    > **💡 Nota sobre valores de demo vs producción:**
    >
    > - **Valor de demo**: `5` → Se usa un valor bajo para facilitar la generación de alertas durante las demostraciones y pruebas.
    > - **Valor de producción**: `75` → En entornos reales, típicamente se usa un umbral del 75% para evitar falsas alarmas y alertar solo cuando hay un problema real.
    > - **Período de tiempo**: En este ejercicio usamos `3m` (3 minutos), pero en producción suele usarse `10m` (10 minutos) para evitar alertas por picos temporales.

3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

### **2.2. Crear macro para memoria**

1. En la misma pestaña <span style="color: violet;"><strong>Macros</strong></span> del template, crear otra macro:
    - Hacer clic en <span style="color: blue;"><strong>Add</strong></span>
    - Configurar:
        - Macro: `{$MEMORY.UTIL.WAR}`
        - Value: `50` *(valor de demo para generar alertas fácilmente)*
        - Description: `Umbral de advertencia de utilización de memoria (%). Se activa alerta cuando la memoria supera este valor durante el período configurado.`

    > **💡 Nota sobre valores de demo vs producción:**
    >
    > - **Valor de demo**: `50` → Se usa un valor bajo para facilitar la generación de alertas durante las demostraciones y pruebas.
    > - **Valor de producción**: `75` → En entornos reales, típicamente se usa un umbral del 75% para evitar falsas alarmas y alertar solo cuando hay un problema real.
    > - **Período de tiempo**: En este ejercicio usamos `15m` (15 minutos), pero en producción suele usarse un período más largo para evitar alertas por picos temporales.

2. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **3. Crear trigger para CPU (utilizando macros)**

**Objetivo**: Crear un trigger que se active cuando la utilización de CPU supera un umbral configurado mediante una macro.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name**:

        → Name: `{#SNMPVALUE}: Average CPU utilization`

    2. **Event name**:

        → Event name: `{#SNMPVALUE}: Average CPU utilization (over {$CPU.UTIL.AVG}% for 5m)`

        > **💡 Nota**: El Event name incluye el valor del umbral (`{$CPU.UTIL.AVG}`) para que sea visible en los eventos cuando el trigger se active. Esto facilita la identificación del problema sin necesidad de consultar la configuración del trigger.

    3. **Operational data**:

        → Operational data: `Current utilization: {ITEM.LASTVALUE}`

    4. **Severity**:

        → Severity: `Average` *(Media)*

    5. **Expression**:

        → Expression: `min(/Template Network Switch by SNMP/cpu.utilization[{#SNMPVALUE}],3m)>{$CPU.UTIL.AVG}`

        > **💡 ¿Qué hace esta expresión?**
        >
        > - `min(...,3m)` → Obtiene el valor mínimo de utilización de CPU en los últimos 3 minutos.
        > - `>{$CPU.UTIL.AVG}` → Compara con el umbral definido en la macro (5% en demo, 75% en producción).
        >
        > **¿Por qué usar `min()` en lugar de `avg()`?**
        >
        > Usar `min()` asegura que el trigger se active solo si **todos** los valores en el período son superiores al umbral, evitando falsas alarmas por picos temporales.

    6. **Description**:

        → Description: `Utilización promedio de la CPU. Este trigger se activa cuando la utilización de la CPU en los últimos 3 minutos es mayor que {$CPU.UTIL.AVG}. Es importante monitorear esta condición para evitar sobrecargas que puedan afectar el rendimiento del sistema.`

    7. **Tags**:

        - Name: `scope` | Value: `capacity`
        - Name: `scope` | Value: `performance`

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **4. Crear trigger para memoria (utilizando macros)**

**Objetivo**: Crear un trigger que se active cuando la utilización de memoria supera un umbral configurado mediante una macro.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name**:

        → Name: `Warning memory utilization`

    2. **Event name**:

        → Event name: `Warning memory utilization (>{$MEMORY.UTIL.WAR}% for 3m)`

    3. **Operational data**:

        → Operational data: `Value: {ITEM.VALUE1} - Last: {ITEM.LASTVALUE1}`

        > **💡 Nota**: Se muestran tanto el valor procesado (`{ITEM.VALUE1}`) como el último valor recibido (`{ITEM.LASTVALUE1}`) para proporcionar más contexto sobre el estado de la memoria.

    4. **Severity**:

        → Severity: `Warning` *(Advertencia)*

    5. **Expression**:

        → Expression: `min(/Template Network Switch by SNMP/cseSysMemoryUtilization,15m) > {$MEMORY.UTIL.WAR}`

        > **💡 Nota**: Se usa `min()` con un período de 15 minutos para evitar alertas por picos temporales de memoria.

    6. **Description**:

        → Description: `Advertencia de utilización de la memoria. Este trigger se activa cuando la utilización de la memoria en los últimos 15 minutos es mayor que {$MEMORY.UTIL.WAR}. Esto indica una creciente demanda de memoria que podría convertirse en un problema si no se gestiona adecuadamente. Es posible que el sistema tarde en responder. Es importante supervisar y, si es necesario, optimizar el uso de recursos. La alerta se recupera automáticamente solo cuando el valor es menor que {$MEMORY.UTIL.WAR}.`

    7. **Tags**:

        - Name: `scope` | Value: `capacity`
        - Name: `scope` | Value: `performance`

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **5. Verificar los triggers creados**

1. Verificar que los triggers se hayan creado correctamente:
    - En el template, ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span>.
    - Verificar que aparezcan los tres triggers creados:
        - `Interface {#IFDESCR}({#IFALIAS}): Link down`
        - `{#SNMPVALUE}: Average CPU utilization`
        - `Warning memory utilization`

2. Verificar que los triggers se apliquen a los hosts:
    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar un host que use el template (por ejemplo, **"SW-Demo2"**).
    - Ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span>.
    - Verificar que los triggers del template aparezcan listados (con el icono de template indicando que provienen del template).

3. Verificar en la vista de problemas:
    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>.
    - Los triggers aparecerán cuando se cumplan las condiciones configuradas.
    - Verificar que los Event names y Operational data se muestren correctamente.

---

> **💡 Nota importante:** Los triggers creados en el template se aplicarán automáticamente a todos los hosts que usen el template. Si necesitas modificar los umbrales para un host específico, puedes sobrescribir las macros a nivel de host en lugar de modificar el template.

---

### **Resumen del ejercicio**

Este ejercicio práctico cubre la creación de tres triggers diferentes:

1. **Trigger para interfaces de red (Link down)**: Se activa cuando una interfaz cambia a estado DOWN, utilizando expresiones avanzadas para evitar alertas en interfaces que nunca estuvieron activas.

2. **Trigger para CPU**: Utiliza macros de template (`{$CPU.UTIL.AVG}`) para definir umbrales configurables y se activa cuando la utilización de CPU supera el umbral durante un período determinado.

3. **Trigger para memoria**: Similar al de CPU, utiliza macros de template (`{$MEMORY.UTIL.WAR}`) y se activa cuando la utilización de memoria supera el umbral configurado.

**Conceptos clave cubiertos:**

- Creación de triggers desde items o desde la pestaña Triggers
- Diferencia entre Name y Event name
- Uso de Operational data para proporcionar contexto
- Creación de macros a nivel de template para centralizar umbrales
- Expresiones avanzadas con funciones de tiempo (`min()`, `last()`)
- Recovery expressions para definir condiciones de recuperación
- Tags para categorizar y filtrar triggers
- Valores de demo vs valores de producción
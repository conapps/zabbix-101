# **6.4. Ejercicio práctico**

**Objetivo**: Configuración de **triggers** (disparadores) para alertar sobre problemas en interfaces de red, CPU y memoria utilizando el template **"Template Network Switch by SNMP"** anteriormente creado en [Ejercicio integrador - Template con LLD y Value Mappings](ejercicios/ejercicio-integrador.md).

**<u>Pasos guiados</u>**

> **💡 ¿Cómo crear un trigger?**
>
> Existen **varias formas** de crear un trigger en Zabbix:
>
> 1. **Desde un item**: En la columna **Name** apareceran todos los items y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span> *(Zabbix prellenará automáticamente el item en la expresión)*.
>
> 2. **Desde la pestaña Triggers**: En la esquina superior derecha de la pantalla hacer clic en <span style="color: blue;"><strong>Create trigger</strong></span>.
>
> **Recomendación**: Para este ejercicio, crearemos los triggers directamente en el **template** para que se apliquen automáticamente a todos los hosts que usen el template.

---

## **1. Crear trigger para interfaces de red (Link down)**

**Objetivo**: Crear un trigger que se active cuando una interfaz de red cambia a estado DOWN.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> ingresar a los **Item prototypes** de **Network Interfaces Discovery**, ubicar el item **Interface {#IFDESCR}({#IFALIAS}): Operational status** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger prototype</strong></span>

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

        → Expression: `(last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}])=2 and last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}],#1)<>last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}],#2))`

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

    - Configurar:

        - Macro: `{$CPU.UTIL.AVG}`
        - Value: `5` *(valor de demo para generar alertas fácilmente)*
        - Description: `Umbral promedio de utilización de CPU (%) para alerta Average.`

    > **💡 Nota sobre valores de demo vs producción:**
    >
    > - **Valor de demo**: `5` → Se usa un valor bajo para facilitar la generación de alertas durante las demostraciones y pruebas.
    > - **Valor de producción**: `75` → En entornos reales, típicamente se usa un umbral del 75% para evitar falsas alarmas y alertar solo cuando hay un problema real.

### **2.2. Crear macro para memoria**

1. En la misma pestaña <span style="color: violet;"><strong>Macros</strong></span> del template, crear otra macro:

    - <span style="color: blue;"><strong>Add</strong> (Agregar)</span>
    - Configurar:

        - Macro: `{$MEMORY.UTIL.WAR}`
        - Value: `50` *(valor de demo para generar alertas fácilmente)*
        - Description: `Umbral de advertencia de utilización de memoria (%) para alerta Warning.`

    > **💡 Nota sobre valores de demo vs producción:**
    >
    > - **Valor de demo**: `50` → Se usa un valor bajo para facilitar la generación de alertas durante las demostraciones y pruebas.
    > - **Valor de producción**: `75` → En entornos reales, típicamente se usa un umbral del 75% para evitar falsas alarmas y alertar solo cuando hay un problema real.

2. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

---

## **3. Crear trigger para CPU (utilizando macros)**

**Objetivo**: Crear un trigger que se active cuando la utilización de CPU supera un umbral configurado mediante una macro.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> ingresar a los **Item prototypes** de **CPU Discovery**, ubicar el item **{#SNMPINDEX}: CPU utilization** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger prototype</strong></span>

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
        >
        > **Período de tiempo**: En este ejercicio usamos `3m` (3 minutos), pero en producción suele usarse `10m` (10 minutos) para evitar alertas por picos temporales.

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
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Items</strong></span> ubicar el item **Memory utilization** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span>

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

        → Expression: `min(/Template Network Switch by SNMP/cseSysMemoryUtilization,3m) > {$MEMORY.UTIL.WAR}`

        > **💡 Nota**: Se usa `min()` con un período de 3 minutos para evitar alertas por picos temporales de memoria.
        >
        > **Período de tiempo**: En este ejercicio usamos `3m` (3 minutos), pero en producción suele usarse `15m` (15 minutos) para evitar alertas por picos temporales.

    6. **Description**:

        → Description: `Advertencia de utilización de la memoria. Este trigger se activa cuando la utilización de la memoria en los últimos 3 minutos es mayor que {$MEMORY.UTIL.WAR}. Esto indica una creciente demanda de memoria que podría convertirse en un problema si no se gestiona adecuadamente. Es posible que el sistema tarde en responder. Es importante supervisar y, si es necesario, optimizar el uso de recursos. La alerta se recupera automáticamente solo cuando el valor es menor que {$MEMORY.UTIL.WAR}.`

    7. **Tags**:

        - Name: `scope` | Value: `capacity`
        - Name: `scope` | Value: `performance`

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **5. Verificar los triggers creados**

1. Verificar que los triggers se hayan creado correctamente y esten aplicados a los hosts:

    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> el host **"SW-Demo2"**:
    - En la pestaña <span style="color: violet;"><strong>Discovery</strong></span>, localizar las reglas de discovery y hacer clic en <span style="color: blue;"><strong>Execute now</strong></span> en cada una:
        - **Network Interfaces Discovery**
        - **CPU Discovery**
    - Esperar unos minutos para que se creen los triggers correspondientes.
    - Ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span>.
    - Verificar que los triggers del template aparezcan listados (con el icono de template indicando que provienen del template) y que esten aplicados a los hosts.

2. **Solicitar al instructor que genere un problema en las interfaces**:

    - Una vez finalizada la verificación de triggers, avisar al instructor para que genere un problema en las interfaces de red.
    - Esto permitirá ver el trigger **"Interface {#IFDESCR}({#IFALIAS}): Link down"** activarse en la vista de Problems.
    - El instructor puede generar el problema deshabilitando una interfaz o simulando una caída de enlace.

3. Verificar en la vista de problemas:

    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>.
    - Los triggers aparecerán cuando se cumplan las condiciones configuradas.
    - Verificar que los Event names y Operational data se muestren correctamente.

---

## **6. Crear macro adicional y trigger con dependencia**

**Objetivo**: Crear una macro adicional para memoria y configurar un trigger con dependencia para demostrar cómo funcionan las dependencias entre triggers.

### **6.1. Crear macro para memoria (Average)**

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Macros</strong></span>

2. Crear la macro:
    - <span style="color: blue;"><strong>Add</strong> (Agregar)</span>
    - Configurar:
        - Macro: `{$MEMORY.UTIL.MAX}`
        - Value: `75` *(valor de demo para generar alertas fácilmente)*
        - Description: `Umbral máximo de utilización de memoria (%) para alerta Average.`

    > **💡 Nota sobre valores de demo vs producción:**
    >
    > - **Valor de demo**: `75` → Se usa un valor bajo para facilitar la generación de alertas durante las demostraciones y pruebas.
    > - **Valor de producción**: `85` → En entornos reales, típicamente se usa un umbral del 85% para evitar falsas alarmas y alertar solo cuando hay un problema real que requiere atención inmediata.

3. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

### **6.2. Crear trigger para memoria (Average)**

**Objetivo**: Crear un trigger que se active cuando la utilización de memoria supera un umbral más alto configurado mediante una macro.

1. Ir al template **"Template Network Switch by SNMP"**:
    - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Items</strong></span> ubicar el item **Memory utilization** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name**:

        → Name: `Average memory utilization`

    2. **Event name**:

        → Event name: `Average memory utilization (>{$MEMORY.UTIL.MAX}% for 3m)`

    3. **Severity**:

        → Severity: `Average` *(Media)*

    4. **Expression**:

        → Expression: `min(/Template Network Switch by SNMP/cseSysMemoryUtilization,3m) > {$MEMORY.UTIL.MAX}`

        > **💡 Nota**: Se usa `min()` con un período de 3 minutos para evitar alertas por picos temporales de memoria.
        >
        > **Período de tiempo**: En este ejercicio usamos `3m` (3 minutos), pero en producción suele usarse `15m` (15 minutos) para evitar alertas por picos temporales y obtener una visión más precisa del uso de memoria a lo largo del tiempo.

    5. **Description**:

        → Description: `Utilización promedio de la memoria. Este trigger se activa cuando la utilización de la memoria en los últimos 3 minutos es mayor que {$MEMORY.UTIL.MAX}. Esto indica un uso elevado de la memoria. Se considera revisar el uso de la memoria, ya que este estado puede afectar el rendimiento del sistema y requiere atención. Es posible que el sistema tarde en responder. Es recomendable monitorear y ajustar la carga de trabajo para prevenir posibles problemas.`

    6. **Tags**:

        - Name: `scope` | Value: `capacity`
        - Name: `scope` | Value: `performance`

    7. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

### **6.3. Configurar dependencia en el trigger de memoria (Warning)**

1. Editar el trigger **"Warning memory utilization"**:
    - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span>.
    - Localizar el trigger **"Warning memory utilization"** y hacer clic en él para editarlo.

2. Configurar la dependencia:
    - Ir a la pestaña <span style="color: violet;"><strong>Dependencies</strong></span>.
    - Hacer clic en <span style="color: blue;"><strong>Add</strong></span>.
    - Seleccionar el trigger **"Average memory utilization"** (el que acabamos de crear).
    - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    > **💡 ¿Qué es una dependencia entre triggers?**
    >
    > Las **dependencias** permiten establecer relaciones entre triggers. Cuando un trigger depende de otro:
    > - Si el trigger **dependiente** (el que tiene la dependencia) se activa, pero el trigger del que **depende** también está activo, el trigger dependiente se **suprime** (no se muestra como problema).
    > - Esto evita mostrar múltiples alertas relacionadas cuando hay un problema raíz más importante.
    >
    > **En este caso**: Si el trigger "Average memory utilization" está activo (mayor severidad), el trigger "Warning memory utilization" se suprimirá, mostrando solo el problema de memoria más crítico.

---

### **6.4. Verificar el comportamiento de las dependencias**

1. Verificar los triggers creados en el host:
    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar el host **"SW-Demo2"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span>.
    - Verificar que los triggers **"Warning memory utilization"** y **"Average memory utilization"** aparezcan listados.
    - Verificar que el trigger **"Warning memory utilization"** tenga configurada la dependencia hacia **"Average memory utilization"**.

2. Esperar **3 minutos** (tiempo configurado en los triggers) para que se actualicen los valores y se activen los triggers según sus condiciones.

3. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>.

4. Observar el comportamiento:

    > **💡 Nota importante:** Deberías notar que:
    > - Si ambos triggers se activan (Warning memory utilization y Average memory utilization), solo aparecerá el trigger **"Average memory utilization"** en la vista de Problems.
    > - El trigger **"Warning memory utilization"** estará **suprimido** (suppressed) porque depende del trigger Average.
    > - Esto demuestra cómo las dependencias ayudan a priorizar problemas y evitar alertas redundantes cuando hay un problema más crítico que requiere atención inmediata.

5. **Solicitar al instructor para demo de Grafana**:
    - Una vez finalizada la verificación de dependencias, avisar al instructor para realizar la demo de Grafana.

---

### **6.5. Ejercicio adicional (opcional)**

Se puede repetir el proceso de los pasos **6.1, 6.2, 6.3 y 6.4** para crear un trigger adicional con severidad **High**:

1. Crear una macro `{$MEMORY.UTIL.HIGH}` con valor `90` (valor de demo, en producción suele ser 95).
2. Crear un trigger **"High memory utilization"** con severidad High y expresión `min(/Template Network Switch by SNMP/cseSysMemoryUtilization,3m) > {$MEMORY.UTIL.HIGH}`.
3. Configurar dependencia en el trigger **"Average memory utilization"** hacia el nuevo trigger **"High memory utilization"**.
4. Verificar en Problems que cuando se active el trigger High, el trigger Average se suprima automáticamente.
5. Esto crea una jerarquía completa de alertas: Warning → Average → High, donde solo se muestra la alerta de mayor severidad.

> - **Para hacer desaparecer las alertas**: Puedes subir los umbrales de las macros (`{$MEMORY.UTIL.WAR}`, `{$MEMORY.UTIL.MAX}`, `{$CPU.UTIL.AVG}`) a valores más altos para que las condiciones de los triggers dejen de cumplirse. Puedes verificar los valores actuales en <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> filtrando por el host y revisando el valor del item **Memory utilization** o **CPU Utilization**.

---

> **💡 Nota importante:** Los triggers creados en el template se aplicarán automáticamente a todos los hosts que usen el template. Si necesitas modificar los umbrales para un host específico, puedes sobrescribir las macros a nivel de host en lugar de modificar el template.

---

### **Resumen del ejercicio**

Este ejercicio práctico cubre la creación de triggers y la configuración de dependencias:

1. **Trigger para interfaces de red (Link down)**: Se activa cuando una interfaz cambia a estado DOWN, utilizando expresiones avanzadas para evitar alertas en interfaces que nunca estuvieron activas.

2. **Trigger para CPU**: Utiliza macros de template (`{$CPU.UTIL.AVG}`) para definir umbrales configurables y se activa cuando la utilización de CPU supera el umbral durante un período determinado.

3. **Trigger para memoria (Warning)**: Utiliza macros de template (`{$MEMORY.UTIL.WAR}`) y se activa cuando la utilización de memoria supera el umbral configurado.

4. **Trigger para memoria (Average)**: Utiliza macros de template (`{$MEMORY.UTIL.MAX}`) con un umbral más alto y severidad Average, configurado con dependencia del trigger Warning.

5. **Configuración de dependencias**: Se demuestra cómo configurar dependencias entre triggers para priorizar alertas y evitar notificaciones redundantes.

**Conceptos clave cubiertos:**

- Creación de triggers desde items o desde la pestaña Triggers
- Diferencia entre Name y Event name
- Uso de Operational data para proporcionar contexto
- Creación de macros a nivel de template para centralizar umbrales
- Expresiones avanzadas con funciones de tiempo (`min()`, `last()`)
- Recovery expressions para definir condiciones de recuperación
- Tags para categorizar y filtrar triggers
- Configuración de dependencias entre triggers
- Comportamiento de triggers suprimidos (suppressed) cuando hay dependencias
- Valores de demo vs valores de producción

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="6.4. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="6.4. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_3.png" alt="6.4. Ejercicio práctico - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_4.png" alt="6.4. Ejercicio práctico - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_5.png" alt="6.4. Ejercicio práctico - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/6.4.%20Ejercicio%20pr%C3%A1ctico_6.png" alt="6.4. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

</details>
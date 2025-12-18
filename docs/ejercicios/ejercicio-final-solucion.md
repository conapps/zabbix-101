# **Ejercicio final - Solución completa**

> **📋 Este documento contiene la solución detallada del ejercicio final.**  
> **Se recomienda intentar resolver el ejercicio primero consultando [Ejercicio final - Monitoreo integral de infraestructura](ejercicios/ejercicio-final.md) antes de ver esta solución.**

---

## **📋 Descripción del escenario**

**Contexto empresarial:**

Una empresa necesita implementar monitoreo integral para su infraestructura crítica de servicios web. La infraestructura está compuesta por:

1. **Servidor Web (SRV-Demo-Web-Server)**:

   - Sistema operativo **Linux** con **Nginx** como servidor web.
   - Servicio desplegado sobre **Oracle Cloud Infrastructure**.
   - Servicio desplegado con **Ansible**.
   - Página web corporativa accesible públicamente.
   - **Método de monitoreo**: Agent-less (ICMP, TCP, HTTP).

2. **Switch de red (SW-Demo2)**:

   - **Cisco Nexus 9000** Series.
   - Conecta el servidor web a la red corporativa.
   - Permite acceso al servidor web desde internet.
   - **Método de monitoreo**: SNMPv2.

3. **Switch adicional (SW-Demo3)**:

   - **Cisco Nexus 9000** Series.
   - Parte de la infraestructura de red.
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

---

## **1. Revisión y organización de infraestructura existente**

**Objetivo**: Revisar los hosts configurados en ejercicios anteriores y prepararlos para una organización integral.

### **1.1. Identificar hosts existentes**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>

2. Identificar los siguientes hosts configurados en ejercicios anteriores:
   - `SRV-Demo-Web-Server` (configurado en [ejercicio 8.4](ejercicio-8.4.md))
   - `SW-Demo2` (configurado en [ejercicio integrador](ejercicio-integrador.md))
   - `SW-Demo3` (configurado en [ejercicio 9.8](ejercicio-9.8.md))

3. Para cada host, verificar:
   - Los grupos a los que pertenece
   - Los templates aplicados
   - El estado de disponibilidad
   - Las interfaces configuradas

---

## **2. Organización de grupos y aplicación de buenas prácticas**

**Objetivo**: Reorganizar los hosts en una estructura lógica jerárquica que refleje la arquitectura real de la infraestructura.

### **2.1. Crear estructura jerárquica de grupos de hosts**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Host groups</strong></span> → <span style="color: blue;"><strong>Create host group</strong></span>

2. Crear los siguientes grupos en orden:

    1. **Grupo principal "Infraestructura"**:
        → Name: `Infraestructura`
        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    2. **Subgrupo "Web Servers"**:
        → Name: `Infraestructura/Web Servers`
        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    3. **Subgrupo "Network Devices"**:
        → Name: `Infraestructura/Network Devices`
        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

### **2.2. Mover hosts a los grupos apropiados**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>

2. **Mover SRV-Demo-Web-Server**:

    1. Seleccionar el host `SRV-Demo-Web-Server`
    2. Hacer clic para editarlo
    3. En la pestaña <span style="color: violet;"><strong>Groups</strong></span>:
        → Quitar el grupo `demo` (o mantenerlo si es necesario)
        → Agregar el grupo `Infraestructura/Web Servers`
    4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

3. **Mover SW-Demo2 y SW-Demo3** (múltiples hosts con la misma configuración):

    > **💡 Tip - Actualización masiva (Mass update):**
    >
    > Cuando varios hosts necesitan la misma configuración (como en este caso, ambos switches necesitan moverse al mismo grupo), es más eficiente usar **Mass update** en lugar de editar cada host individualmente.
    >
    > Esta es la opción más eficiente cuando varios hosts necesitan los mismos cambios. Sin embargo, también se puede hacer de forma individual como se muestra a continuación.

    **Opción 1: Actualización masiva (recomendado para múltiples hosts)**

    1. Seleccionar ambos hosts `SW-Demo2` y `SW-Demo3` (mantener presionada la tecla Ctrl/Cmd y hacer clic en cada host)
    2. Hacer clic en <span style="color: blue;"><strong>Mass update</strong></span> (actualización masiva)
    3. En la pestaña **Host**:
        → Tildar la opción **Host groups** → <span style="color: blue;"><strong>Add</strong> (Agregar)</span> y buscar y seleccionar el grupo `Infraestructura/Network Devices`
    4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    **Opción 2: Edición individual (si prefieres hacerlo uno por uno)**

    1. Seleccionar el host `SW-Demo2`
    2. Hacer clic para editarlo
    3. En la pestaña <span style="color: violet;"><strong>Groups</strong></span>:
        → Quitar el grupo `demo` (o mantenerlo si es necesario)
        → Agregar el grupo `Infraestructura/Network Devices`
    4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    5. Repetir los pasos 1-4 para `SW-Demo3`

### **2.3. Aplicar tags consistentes a los hosts**

1. **Agregar tags al host SRV-Demo-Web-Server**:

    1. Editar el host `SRV-Demo-Web-Server`
    2. En la pestaña <span style="color: violet;"><strong>Tags</strong></span>, agregar:
        - Name: `component` | Value: `web-server`
        - Name: `environment` | Value: `production`
        - Name: `os` | Value: `linux`
    3. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

2. **Agregar tags a los switches** (múltiples hosts con los mismos tags):

    > **💡 Tip - Actualización masiva (Mass update) para tags:**
    >
    > Cuando varios hosts necesitan los mismos tags (como en este caso, ambos switches necesitan los mismos tags), es más eficiente usar **Mass update** en lugar de editar cada host individualmente.
    >
    > Esta es la opción más eficiente cuando varios hosts necesitan los mismos tags. Sin embargo, también puedes hacerlo de forma individual como se muestra a continuación.

    **Opción 1: Actualización masiva (recomendado para múltiples hosts)**

    1. Seleccionar ambos hosts `SW-Demo2` y `SW-Demo3` (mantener presionada la tecla Ctrl/Cmd y hacer clic en cada host)
    2. Hacer clic en <span style="color: blue;"><strong>Mass update</strong></span> (actualización masiva)
    3. En la pestaña **Tags**:
        → Tildar la opción **Tags** → Agregar los tags:
        - Name: `component` | Value: `network-switch`
        - Name: `vendor` | Value: `cisco`
        - Name: `model` | Value: `nexus-9000`
    4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    **Opción 2: Edición individual (si prefieres hacerlo uno por uno)**

    1. Editar el host `SW-Demo2`
    2. En la pestaña <span style="color: violet;"><strong>Tags</strong></span>, agregar:
        - Name: `component` | Value: `network-switch`
        - Name: `vendor` | Value: `cisco`
        - Name: `model` | Value: `nexus-9000`
    3. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    4. Repetir los pasos 1-3 para `SW-Demo3` con los mismos tags

---

## **3. Verificación y mejora de triggers existentes**

**Objetivo**: Revisar los triggers configurados en ejercicios anteriores y asegurar que estén correctamente organizados y optimizados con las 3 severidades (Warning, Average, High) y sus dependencias.

### **3.1. Completar triggers de ICMP Ping - 3 severidades con dependencias**

El trigger de ICMP ping configurado en el [ejercicio 8.4](ejercicio-8.4.md) tiene severidad High y usa la expresión `last(/SRV-Demo-Web-Server/icmpping)=0`. Necesitamos modificar este trigger y crear los triggers adicionales para completar las 3 severidades con dependencias.

#### **3.1.1. Modificar el trigger existente (High → Warning)**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar `SRV-Demo-Web-Server` → Pestaña <span style="color: violet;"><strong>Triggers</strong></span>

2. Editar el trigger **"Unavailable by ICMP ping"**:

    1. Cambiar **Severity** de `High` a `Warning`
    2. **Name**: Cambiar a `Unavailable by ICMP ping (Warning)` *(opcional, pero recomendado para claridad)*
    3. Verificar que la **Expression** sea: `last(/SRV-Demo-Web-Server/icmpping)=0`
    4. En la pestaña <span style="color: violet;"><strong>Tags</strong></span>, agregar (si no lo tiene):
        - Name: `scope` | Value: `availability`
    5. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

#### **3.1.2. Crear trigger Average para ICMP Ping**

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name**:
        → Name: `Unavailable by ICMP ping (Average)`

    2. **Event name**:
        → Event name: `Host {HOST.NAME} is down (no response to ICMP ping - confirmed)`

    3. **Severity**:
        → Severity: `Average` *(Media)*

    4. **Expression**:
        → Expression: `last(/SRV-Demo-Web-Server/icmpping,#2)=0`

        > **💡 Nota**: Esta expresión verifica si el último valor hace 2 períodos (#2) es igual a `0`, proporcionando una verificación más robusta que solo el último valor inmediato. Esto ayuda a reducir falsas alarmas causadas por problemas temporales de red.

    5. **Recovery expression**:
        → Recovery expression: `last(/SRV-Demo-Web-Server/icmpping)=1`

    6. **Description**:
        → Description: `No disponible por ping ICMP (confirmado). Este trigger se activa cuando la solicitud de ping ICMP al dispositivo devolvió un tiempo de espera agotado. Esto puede indicar que el host está inaccesible, apagado o que hay problemas de conectividad de red.`

    7. **Tags**:
        - Name: `scope` | Value: `availability`

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.1.3. Crear trigger High para ICMP Ping (expresión robusta)**

1. En el host **"SRV-Demo-Web-Server"**, ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span> → <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:

    1. **Name**:
        → Name: `Unavailable by ICMP ping (High)`

    2. **Event name**:
        → Event name: `Host {HOST.NAME} is down (no response to ICMP ping - critical)`

    3. **Severity**:
        → Severity: `High` *(Alta)*

    4. **Expression**:
        → Expression: `max(/SRV-Demo-Web-Server/icmpping,#3)=0`

        > **💡 Nota importante**: Esta expresión es más robusta que `last(...)=0`. Verifica si el **máximo valor de los últimos 3 valores** es igual a `0`, lo que reduce falsas alarmas causadas por valores puntuales o problemas temporales de red. Requiere que **todos** los últimos 3 valores sean `0` para activarse, siendo más confiable para detectar problemas críticos.

        > **💡 Referencia**: Esta expresión alternativa se menciona en el [ejercicio 8.4](ejercicio-8.4.md) como una opción más robusta.

    5. **Recovery expression**:
        → Recovery expression: `last(/SRV-Demo-Web-Server/icmpping)=1`

    6. **Description**:
        → Description: `No disponible por ping ICMP (crítico). Este trigger se activa cuando el host no responde a ping ICMP durante los últimos 3 intentos consecutivos, lo que indica un problema crítico de conectividad. Por favor, verifique la conectividad del dispositivo inmediatamente.`

    7. **Tags**:
        - Name: `scope` | Value: `availability`

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.1.4. Configurar dependencias para triggers de ICMP Ping**

1. Editar el trigger **"Unavailable by ICMP ping (Warning)"**:
   - Ir al host **"SRV-Demo-Web-Server"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → Seleccionar `Unavailable by ICMP ping (Warning)`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencias hacia:
     - `Unavailable by ICMP ping (Average)`
     - `Unavailable by ICMP ping (High)`
     - Para cada una: <span style="color: blue;"><strong>Add</strong></span> → Seleccionar el trigger correspondiente
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

2. Editar el trigger **"Unavailable by ICMP ping (Average)"**:
   - Ir al host **"SRV-Demo-Web-Server"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → Seleccionar `Unavailable by ICMP ping (Average)`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencia hacia `Unavailable by ICMP ping (High)`:
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `Unavailable by ICMP ping (High)`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

3. **Resultado**: 
   - Warning → depende de Average y High
   - Average → depende de High
   - High → no tiene dependencias (es el más crítico)
   
   Esto significa que cuando el host no responde a ping durante los últimos 3 intentos consecutivos, solo se mostrará el trigger High, y los triggers Warning/Average se suprimirán porque dependen de High.

### **3.2. Completar triggers de memoria (3 severidades con dependencias)**

Los triggers de memoria ya tienen Warning y Average configurados. Necesitamos crear el trigger High y configurar todas las dependencias.

#### **3.2.1. Crear macro para memoria (High)**

1. Ir al template **"Template Network Switch by SNMP"**:
   - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Macros</strong></span>

2. Verificar si existe la macro `{$MEMORY.UTIL.HIGH}`, si no existe crearla:
   - <span style="color: blue;"><strong>Add</strong> (Agregar)</span>
   - Macro: `{$MEMORY.UTIL.HIGH}`
   - Value: `90` *(valor de demo para generar alertas fácilmente)*
   - Description: `Umbral máximo de utilización de memoria (%) para alerta High.`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.2.2. Crear trigger High memory utilization**

1. Ir al template **"Template Network Switch by SNMP"**:
   - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"**
   - Pestaña <span style="color: violet;"><strong>Items</strong></span> ubicar el item **'Memory utilization'** y a la izquierda del mismo fijarse en el icono de <span style="text-align: center; display: inline-block; width: 1em;">⋯</span> y seleccionar <span style="color: blue;"><strong>Create trigger</strong></span>

2. Configurar el trigger:
   - **Name**: `High memory utilization`
   - **Event name**: `High memory utilization (>{$MEMORY.UTIL.HIGH}% for 3m)`
   - **Operational data**: `Value: {ITEM.VALUE1} - Last: {ITEM.LASTVALUE1}`
   - **Severity**: `High` *(Alta)*
   - **Expression**: `min(/Template Network Switch by SNMP/cseSysMemoryUtilization,3m) > {$MEMORY.UTIL.HIGH}`
   - **Tags**:
     - Name: `scope` | Value: `performance`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.2.3. Configurar dependencias para triggers de memoria**

1. Editar el trigger **"Warning memory utilization"**:
   - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → Seleccionar `Warning memory utilization`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Si no tiene dependencia hacia `Average memory utilization`, agregarla:
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `Average memory utilization`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

2. Editar el trigger **"Average memory utilization"**:
   - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Triggers</strong></span> → Seleccionar `Average memory utilization`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencia hacia `High memory utilization`:
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `High memory utilization`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

3. **Resultado**: La cadena de dependencias debe ser: Warning → Average → High

### **3.3. Completar triggers de CPU (3 severidades con dependencias)**

Actualmente solo existe el trigger Average para CPU. Necesitamos crear los triggers Warning y High, y configurar las dependencias.

#### **3.3.1. Crear macros para CPU (Warning y High)**

1. Ir al template **"Template Network Switch by SNMP"**:
   - <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Templates</strong></span> → Seleccionar **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Macros</strong></span>

2. Verificar si existe la macro `{$CPU.UTIL.WAR}`, si no existe crearla:
   - <span style="color: blue;"><strong>Add</strong> (Agregar)</span>
   - Macro: `{$CPU.UTIL.WAR}`
   - Value: `60` *(valor de demo)*
   - Description: `Umbral de utilización de CPU (%) para alerta Warning.`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. Verificar si existe la macro `{$CPU.UTIL.HIGH}`, si no existe crearla:
   - <span style="color: blue;"><strong>Add</strong> (Agregar)</span>
   - Macro: `{$CPU.UTIL.HIGH}`
   - Value: `85` *(valor de demo)*
   - Description: `Umbral máximo de utilización de CPU (%) para alerta High.`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.3.2. Crear trigger Warning CPU utilization**

1. Ir al template **"Template Network Switch by SNMP"**:
   - Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"CPU Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span>
   - <span style="color: blue;"><strong>Create trigger prototype</strong></span>

2. Configurar el trigger:
   - **Name**: `{#SNMPVALUE}: Warning CPU utilization`
   - **Event name**: `{#SNMPVALUE}: Warning CPU utilization (over {$CPU.UTIL.WAR}% for 5m)`
   - **Operational data**: `Current utilization: {ITEM.LASTVALUE}`
   - **Severity**: `Warning` *(Advertencia)*
   - **Expression**: `min(/Template Network Switch by SNMP/cpu.utilization[{#SNMPVALUE}],5m) > {$CPU.UTIL.WAR}`
   - **Tags**:
     - Name: `scope` | Value: `capacity`
     - Name: `scope` | Value: `performance`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.3.3. Crear trigger High CPU utilization**

1. Ir al template **"Template Network Switch by SNMP"**:
   - Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"CPU Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span>
   - <span style="color: blue;"><strong>Create trigger prototype</strong></span>

2. Configurar el trigger:
   - **Name**: `{#SNMPVALUE}: High CPU utilization`
   - **Event name**: `{#SNMPVALUE}: High CPU utilization (over {$CPU.UTIL.HIGH}% for 5m)`
   - **Operational data**: `Current utilization: {ITEM.LASTVALUE}`
   - **Severity**: `High` *(Alta)*
   - **Expression**: `min(/Template Network Switch by SNMP/cpu.utilization[{#SNMPVALUE}],5m) > {$CPU.UTIL.HIGH}`
   - **Tags**:
     - Name: `scope` | Value: `capacity`
     - Name: `scope` | Value: `performance`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

#### **3.3.4. Configurar dependencias para triggers de CPU**

1. Editar el trigger **"Warning CPU utilization"**:
   - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"CPU Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span> → Seleccionar `{#SNMPVALUE}: Warning CPU utilization`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencia hacia `{#SNMPVALUE}: Average CPU utilization`:
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `{#SNMPVALUE}: Average CPU utilization`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

2. Editar el trigger **"Average CPU utilization"**:
   - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"CPU Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span> → Seleccionar `{#SNMPVALUE}: Average CPU utilization`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencia hacia `{#SNMPVALUE}: High CPU utilization`:
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `{#SNMPVALUE}: High CPU utilization`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

3. **Resultado**: La cadena de dependencias debe ser: Warning → Average → High

4. **Ejecutar discovery para aplicar los nuevos triggers**:
   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar `SW-Demo2` o `SW-Demo3`
   - Pestaña <span style="color: violet;"><strong>Discovery</strong></span> → Regla **"CPU Discovery"** → <span style="color: blue;"><strong>Execute now</strong></span>
   - Esperar unos minutos para que se creen los nuevos triggers

### **3.4. Completar triggers de interfaces - 2 severidades con dependencias**

Actualmente solo existe el trigger High para interfaces cuando están en estado DOWN (estado 2). Necesitamos crear el trigger Warning para detectar cuando las interfaces están en estado TESTING (estado 3), y configurar las dependencias.

> **💡 Nota sobre estados de interfaz (IF-MIB::ifOperStatus):**
> - **Estado 1 (up)**: La interfaz está operativa y funcionando normalmente.
> - **Estado 2 (down)**: La interfaz no está operativa (sin conectividad).
> - **Estado 3 (testing)**: La interfaz está en modo de prueba, lo que puede indicar problemas de configuración o hardware.

#### **3.4.1. Crear trigger Warning para interfaces en estado testing**

1. Ir al template **"Template Network Switch by SNMP"**:
   - Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"Network Interfaces Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span>
   - <span style="color: blue;"><strong>Create trigger prototype</strong></span>

2. Configurar el trigger:
   - **Name**: `Interface {#IFDESCR}({#IFALIAS}): Operational status is testing (Warning)`
   - **Event name**: `Interface {#IFDESCR}({#IFALIAS}): Interface in testing state`
   - **Severity**: `Warning` *(Advertencia)*
   - **Expression**: `last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}])=3`
   - **Recovery expression**: `last(/Template Network Switch by SNMP/net.if.status[{#SNMPINDEX}])<>3`
   - **Tags**:
     - Name: `scope` | Value: `availability`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

   > **💡 Nota**: Este trigger se activa cuando una interfaz entra en estado testing (3), lo cual puede indicar problemas potenciales antes de que la interfaz caiga completamente.

#### **3.4.2. Configurar dependencias para triggers de interfaces**

Las dependencias deben configurarse de manera que cuando una interfaz esté en estado DOWN (High), el trigger de TESTING (Warning) se suprima, ya que DOWN es un estado más crítico que TESTING.

1. Editar el trigger **"Operational status is testing (Warning)"**:
   - Ir al template **"Template Network Switch by SNMP"** → Pestaña <span style="color: violet;"><strong>Discovery rules</strong></span> → Regla **"Network Interfaces Discovery"** → Pestaña <span style="color: violet;"><strong>Trigger prototypes</strong></span> → Seleccionar `Interface {#IFDESCR}({#IFALIAS}): Operational status is testing (Warning)`
   - Pestaña <span style="color: violet;"><strong>Dependencies</strong></span>
   - Agregar dependencia hacia `Interface {#IFDESCR}({#IFALIAS}): Link down` (el trigger High existente):
     - <span style="color: blue;"><strong>Add</strong></span> → Seleccionar `Interface {#IFDESCR}({#IFALIAS}): Link down`
   - <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

2. **Resultado**: 
   - Warning (testing) → depende de High (down)
   - High (down) → no tiene dependencias (es el más crítico)
   
   Esto significa que cuando una interfaz está DOWN, solo se mostrará el trigger High, y el trigger Warning de testing se suprimirá porque depende de High.

4. **Ejecutar discovery para aplicar los nuevos triggers**:
   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar `SW-Demo2` o `SW-Demo3`
   - Pestaña <span style="color: violet;"><strong>Discovery</strong></span> → Regla **"Network Interfaces Discovery"** → <span style="color: blue;"><strong>Execute now</strong></span>
   - Esperar unos minutos para que se creen los nuevos triggers

### **3.5. Verificar triggers y dependencias configuradas**

1. **Verificar triggers del servidor web (SRV-Demo-Web-Server)**:
   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar `SRV-Demo-Web-Server` → Pestaña <span style="color: violet;"><strong>Triggers</strong></span>
   - Verificar que existan los triggers de ICMP Ping con las 3 severidades:
     - **ICMP Ping**: Warning (`last(...)=0`), Average (`last(...#2)=0`), High (`max(...#3)=0`)

2. **Verificar triggers de los switches (SW-Demo2 o SW-Demo3)**:
   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar `SW-Demo2` → Pestaña <span style="color: violet;"><strong>Triggers</strong></span>
   - Verificar que existan todos los triggers con las severidades correspondientes:
     - **Memoria**: Warning, Average, High
     - **CPU**: Warning, Average, High
     - **Interfaces**: Warning (testing - estado 3), High (Link down - estado 2)

3. Verificar que las dependencias estén configuradas correctamente:
   - **ICMP Ping, Memoria, CPU**: Warning → Average → High
   - **Interfaces**: Warning → High

### **3.6. Agregar tags a todos los triggers**

Verificar que todos los triggers tengan tags apropiados:
- **Availability triggers** (interfaces, ICMP): `scope: availability`
- **Performance triggers** (CPU): `scope: performance`
- **Capacity triggers** (memoria): `scope: capacity`

---

## **4. Verificación y creación de notificaciones y acciones**

**Objetivo**: Asegurar que el flujo completo de alertas y notificaciones esté funcionando correctamente, y crear acciones adicionales con diferentes condiciones utilizando los grupos, severidades y tags configurados.

### **4.1. Verificar y actualizar acción existente**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: violet;"><strong>Trigger actions</strong></span>

2. Verificar que exista la acción configurada en el [ejercicio 7.3](ejercicio-7.3.md):
   - `Notificar problemas de red y sistema`

3. **Actualizar condiciones de la acción** (si es necesario):

    1. Editar la acción `Notificar problemas de red y sistema`
    2. Revisar las condiciones configuradas en la pestaña <span style="color: violet;"><strong>Conditions</strong></span>
    3. Actualizar para incluir los nuevos grupos:
        - Si tenía condición `Host groups` → `equals` → `demo`, actualizar o agregar:
        - Condición 1: `Host groups` → `equals` → `Infraestructura` (para incluir todos los subgrupos)
        - O agregar condiciones separadas para cada subgrupo:
          - `Host groups` → `equals` → `Infraestructura/Web Servers`
          - `Host groups` → `equals` → `Infraestructura/Network Devices`
    4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span> si se hicieron cambios

### **4.2. Crear acción para alertas críticas en servidores web**

**Objetivo**: Crear una acción que se active solo para problemas críticos (severidad High o superior) en servidores web.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: violet;"><strong>Trigger actions</strong></span> → <span style="color: blue;"><strong>Create action</strong></span>

2. Configurar la acción:

    1. **Name**:
        → Name: `Alertas críticas - Servidores Web`

    2. **Conditions** (condiciones):
        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar condiciones.

        - **Condición 1**:
            - **Condition type**: `Host groups`
            - **Operator**: `equals`
            - **Host group**: `Infraestructura/Web Servers`

        - **Condición 2**:
            - **Condition type**: `Trigger severity`
            - **Operator**: `is greater than or equals`
            - **Severity**: `High`

        > **💡 Nota**: Con estas condiciones, la acción se activará solo cuando haya un problema con severidad High o superior en hosts del grupo "Infraestructura/Web Servers" (como el servidor web).

    3. **Operations** (operaciones):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar una operación:

        - **Send to users**: Hacer clic en <span style="color: blue;"><strong>Add</strong></span> y seleccionar el usuario **"Notificaciones"**
        - **Send only to**: Seleccionar `Email (HTML)`
        - **Subject**: `[CRÍTICO] Problema en servidor web: {TRIGGER.NAME}`
        - **Message**: *(usar mensaje personalizado o dejar el mensaje por defecto)*
        → <span style="color: blue;"><strong>Add</strong></span> (Guardar la operación)

    4. **Status**: `Enabled`

    5. <span style="color: blue;"><strong>Add</strong> (Guardar la acción)</span>

### **4.3. Crear acción para problemas de capacidad y rendimiento**

**Objetivo**: Crear una acción que se active para problemas relacionados con capacidad (memoria) y rendimiento (CPU) usando tags.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: violet;"><strong>Trigger actions</strong></span> → <span style="color: blue;"><strong>Create action</strong></span>

2. Configurar la acción:

    1. **Name**:
        → Name: `Problemas de capacidad y rendimiento`

    2. **Conditions** (condiciones):
        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar condiciones.

        - **Condición 1** (problemas de capacidad):
            - **Condition type**: `Tag value`
            - **Tag**: `scope`
            - **Operator**: `equals`
            - **Value**: `capacity`

        - **Condición 2** (problemas de rendimiento):
            - **Condition type**: `Tag name`
            - **Tag**: `scope`
            - **Operator**: `equals`
            - **Value**: `performance`

        > **💡 Nota**: Estas dos condiciones están en **OR** por defecto, lo que significa que la acción se activará si el trigger tiene el tag `scope: capacity` **O** `scope: performance`. Esto cubre los triggers de memoria (capacity) y CPU (performance).

        - **Condición 3** (severidad mínima):
            - **Condition type**: `Trigger severity`
            - **Operator**: `is greater than or equals`
            - **Severity**: `Average`

        > **💡 Nota**: Con esta condición adicional, la acción solo se activará para problemas de severidad Average o superior, evitando alertas por warnings menores.

    3. **Operations** (operaciones):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar una operación:

        - **Send to users**: Hacer clic en <span style="color: blue;"><strong>Add</strong></span> y seleccionar el usuario **"Notificaciones"**
        - **Send only to**: Seleccionar `Email (HTML)`
        - **Subject**: `[ALERTA] Problema de {TRIGGER.TAGS.scope}: {TRIGGER.NAME}`
        - **Message**: *(usar mensaje personalizado o dejar el mensaje por defecto)*
        → <span style="color: blue;"><strong>Add</strong></span> (Guardar la operación)

    4. **Status**: `Enabled`

    5. <span style="color: blue;"><strong>Add</strong> (Guardar la acción)</span>

### **4.4. Crear acción para dispositivos de red**

**Objetivo**: Crear una acción que combine grupo de hosts y tags para problemas en dispositivos de red.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: violet;"><strong>Trigger actions</strong></span> → <span style="color: blue;"><strong>Create action</strong></span>

2. Configurar la acción:

    1. **Name**:
        → Name: `Alertas de dispositivos de red`

    2. **Conditions** (condiciones):
        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar condiciones.

        - **Condición 1**:
            - **Condition type**: `Host groups`
            - **Operator**: `equals`
            - **Host group**: `Infraestructura/Network Devices`

        - **Condición 2**:
            - **Condition type**: `Tag value`
            - **Tag**: `component`
            - **Operator**: `equals`
            - **Value**: `network-switch`

        - **Condición 3**:
            - **Condition type**: `Trigger severity`
            - **Operator**: `is greater than or equals`
            - **Severity**: `Warning`

        > **💡 Nota**: Esta acción se activará cuando haya un problema con severidad Warning o superior en hosts del grupo "Infraestructura/Network Devices" que tengan el tag `component: network-switch`. Esto asegura que solo se activen alertas para switches de red.

    3. **Operations** (operaciones):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar una operación:

        - **Send to users**: Hacer clic en <span style="color: blue;"><strong>Add</strong></span> y seleccionar el usuario **"Notificaciones"**
        - **Send only to**: Seleccionar `Email (HTML)`
        - **Subject**: `[RED] {HOST.NAME}: {TRIGGER.NAME}`
        - **Message**: *(usar mensaje personalizado o dejar el mensaje por defecto)*
        → <span style="color: blue;"><strong>Add</strong></span> (Guardar la operación)

    4. **Status**: `Enabled`

    5. <span style="color: blue;"><strong>Add</strong> (Guardar la acción)</span>

### **4.5. Verificar acciones creadas**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: violet;"><strong>Trigger actions</strong></span>

2. Verificar que existan las siguientes acciones:
   - `Notificar problemas de red y sistema` (actualizada)
   - `Alertas críticas - Servidores Web` (nueva)
   - `Problemas de capacidad y rendimiento` (nueva)
   - `Alertas de dispositivos de red` (nueva)

3. Verificar que todas las acciones estén **Enabled**

### **4.6. Verificar usuario "Notificaciones"**

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span> → Seleccionar `Notificaciones`

2. Verificar:
   - Que el usuario tenga configurado su correo electrónico en **Media**
   - Que el usuario esté en los grupos apropiados (según el [ejercicio 9.8](ejercicio-9.8.md))
   - Que tenga el rol correcto asignado

### **4.7. Agregar permisos de grupos de hosts al grupo de usuarios del usuario "Notificaciones"**

> **💡 Nota importante**: Para que las acciones funcionen correctamente con los nuevos grupos de hosts creados (`Infraestructura/Web Servers`, `Infraestructura/Network Devices`), es necesario agregar permisos de estos grupos al grupo de usuarios al que pertenece el usuario "Notificaciones".

1. Identificar a qué grupo de usuarios pertenece el usuario "Notificaciones":
   - Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span> → Seleccionar `Notificaciones`
   - Revisar en la pestaña <span style="color: violet;"><strong>Groups</strong></span> a qué grupo(s) pertenece el usuario
   - *(Nota: Según el [ejercicio 9.8](ejercicio-9.8.md), el usuario puede estar en grupos como "Cliente Demo" o "Notificaciones Demo")*

2. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>User groups</strong></span> → Seleccionar el grupo de usuarios correspondiente

3. En la pestaña <span style="color: violet;"><strong>Permissions</strong></span>, agregar permisos para los nuevos grupos de hosts:
   - Hacer clic en <span style="color: blue;"><strong>Select</strong></span> para agregar permisos
   - Agregar los siguientes grupos de hosts con permiso **Read** (Lectura):
     - `Infraestructura` (o incluir los subgrupos específicos)
     - `Infraestructura/Web Servers`
     - `Infraestructura/Network Devices`
   - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

   > **💡 Nota**: El permiso **Read** es suficiente para que el usuario pueda recibir notificaciones relacionadas con estos grupos de hosts. Para este caso de uso (notificaciones), no se requiere permiso de escritura.

4. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span> el grupo de usuarios

5. **Verificar que los permisos se hayan aplicado correctamente**:
   - Verificar en la pestaña <span style="color: violet;"><strong>Permissions</strong></span> que los nuevos grupos de hosts aparezcan listados con permiso **Read**
   - Opcionalmente, verificar en el usuario "Notificaciones" que su grupo de usuarios tenga los permisos correctos

### **4.8. Probar el flujo completo**

Para probar el flujo completo, se puede solicitar al instructor que:
1. Active un trigger específico (por ejemplo, desconectar una interfaz de red o generar alta utilización de CPU/memoria)
2. Verificar que se cree el problema en <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>
3. Verificar que se ejecute la acción correspondiente
4. Verificar que se reciba la notificación por correo electrónico

> **💡 Tip**: Recuerda que las acciones se ejecutan cuando se **dispara** un trigger (cuando cambia de OK a PROBLEM), no cuando el trigger ya está en estado PROBLEM.

---

## **5. Configuración de período de mantenimiento (OPCIONAL)**

**Objetivo**: Aprender a configurar períodos de mantenimiento para suprimir alertas durante mantenimientos planificados.

### **5.1. Crear período de mantenimiento**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Maintenance</strong></span> → <span style="color: blue;"><strong>Create maintenance period</strong></span>

2. Configurar el mantenimiento:

    1. **Name** *(parámetro obligatorio)*:
        → Name: `Mantenimiento programado - Servidor Web`

    2. **Maintenance type**:
        → Maintenance type: `With data collection` *(permite recolección de datos pero suprime notificaciones)*

    3. **Active since**:
        → Active since: Seleccionar fecha/hora actual o próxima

    4. **Active till**:
        → Active till: Seleccionar una hora 30 minutos después de "Active since"

    5. **Host groups**:
        → Agregar el grupo `Infraestructura/Web Servers`

    6. **Description** *(opcional)*:
        → Description: `Mantenimiento programado para pruebas del ejercicio final`

    7. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

### **5.2. Verificar que las alertas se supriman**

1. Esperar a que inicie el período de mantenimiento (o cambiar la fecha de inicio a un tiempo pasado)

2. Si hay problemas activos en el host durante el mantenimiento, verificar que:
   - El icono de mantenimiento aparezca junto al host
   - Las notificaciones se supriman durante este período

### **5.3. Cancelar el mantenimiento**

1. Editar el período de mantenimiento creado

2. Cambiar **Active till** a una fecha pasada o hacer clic en <span style="color: red;"><strong>Delete</strong></span> para eliminarlo

3. Verificar que las alertas vuelvan a funcionar normalmente

---

## **6. Análisis y verificación integral**

**Objetivo**: Realizar una verificación completa de que todo el monitoreo esté funcionando correctamente.

### **6.1. Verificar estado de hosts**

1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>

2. Filtrar por el grupo `Infraestructura` para ver todos los hosts organizados

3. Verificar el estado de disponibilidad:
   - **🟢 Verde**: Host disponible y respondiendo
   - **🔴 Rojo**: Host no disponible (verificar conectividad)
   - **⚪ Gris**: Host deshabilitado o sin monitoreo

4. Revisar los problemas activos por host (columna "Problems")

### **6.2. Revisar problemas activos**

1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>

2. Revisar todos los problemas activos:
   - Verificar que muestren información correcta (severidad, descripción, operational data)
   - Verificar que los problemas suprimidos (suppressed) aparezcan correctamente cuando hay dependencias

3. Filtrar problemas por:
   - Severidad
   - Tags (scope: availability, capacity, performance)
   - Hosts o grupos de hosts

### **6.3. Verificar recopilación de métricas**

1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>

2. Para cada host, verificar:

    **SRV-Demo-Web-Server**:
    - Filtrar por `SRV-Demo-Web-Server`
    - Verificar que existan items:
      - `ICMP Ping`
      - `TCP Port: 80 Check`
      - `HTTP Check - Website`
    - Verificar que los valores sean recientes (últimos minutos)
    - Verificar que los items muestren los value mappings correctos:
      - `ICMP Ping` y `TCP Port: 80 Check` deben mostrar `Up` o `Down` (gracias al value mapping "Service Status")
      - `HTTP Check - Website` debe mostrar `OK` en lugar de `200` (gracias al value mapping "HTTP Status Codes")

    **SW-Demo2**:
    - Filtrar por `SW-Demo2`
    - Verificar que se estén recopilando métricas SNMP:
      - Items del sistema (System Name, System Description, etc.)
      - Memory utilization
      - Items descubiertos (interfaces de red, CPU)
    - Verificar que los estados de interfaces muestren value mappings (up/down en lugar de números)

    **SW-Demo3**:
    - Filtrar por `SW-Demo3`
    - Verificar que se estén recopilando métricas del template `Cisco Nexus 9000 Series by SNMP`
    - Verificar que los items descubiertos estén funcionando

### **6.4. Revisar información del sistema**

1. Ir a <span style="color: purple;"><strong>Reports</strong></span> → <span style="color: violet;"><strong>System information</strong></span>

2. Revisar las estadísticas generales:
   - Número de hosts
   - Número de items
   - Número de triggers
   - Problemas activos
   - Versión de Zabbix

---

## **7. Simulación de incidente y análisis (con instructor)**

**Objetivo**: Demostrar la capacidad de identificar, analizar y responder a un problema real en tiempo real.

> **💡 Nota**: Esta sección requiere la participación del instructor para simular un problema.

### **7.1. Identificar el problema**

1. Cuando el instructor simule un problema, revisar inmediatamente:

    <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>:
    - Identificar el nuevo problema que aparece
    - Verificar la severidad y descripción
    - Revisar el operational data para obtener información adicional

2. Verificar la notificación:
    - Revisar el correo electrónico configurado para el usuario "Notificaciones"
    - Verificar que el mensaje contenga información útil sobre el problema

### **7.2. Analizar la causa raíz**

1. **Revisar métricas históricas**:

    1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>
    2. Filtrar por el host afectado
    3. Revisar los items relacionados con el problema
    4. Hacer clic en el gráfico o historial para ver tendencias

2. **Revisar gráficos relacionados**:

    1. Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> → <span style="color: violet;"><strong>Graphs</strong></span>
    2. Filtrar por el host afectado
    3. Revisar gráficos relevantes al problema

3. **Verificar estado de servicios relacionados**:

    1. Revisar si hay otros problemas relacionados
    2. Verificar si hay dependencias que puedan estar afectando el problema principal

### **7.3. Confirmar la recuperación**

1. Cuando el instructor restaure el servicio:

    1. Volver a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>
    2. Verificar que el problema cambie a estado "Resolved" o "OK"
    3. Confirmar que se haya generado un evento de recuperación
    4. Verificar que se haya enviado una notificación de recuperación (si está configurada)

---

## **8. Checklist de verificación final**

### **Organización:**
- [x] Hosts organizados en grupos lógicos jerárquicos (`Infraestructura/Web Servers`, `Infraestructura/Network Devices`)
- [x] Tags aplicados consistentemente a hosts y triggers
- [x] Estructura clara y fácil de mantener

### **Configuración:**
- [x] Templates aplicados correctamente a los hosts
- [x] Items recopilando datos correctamente (verificado en Latest data)
- [x] Value mappings funcionando (valores legibles como OK, up/down)

### **Alertas:**
- [x] Triggers configurados con severidades apropiadas
- [x] Dependencias entre triggers configuradas donde corresponde
- [x] Descripciones y operational data útiles en los triggers
- [x] Tags aplicados a triggers para categorización

### **Notificaciones:**
- [x] Acciones configuradas y activas (4 acciones: 1 actualizada + 3 nuevas)
- [x] Usuarios y grupos de usuarios configurados correctamente
- [x] Permisos de grupos de hosts agregados al grupo de usuarios del usuario "Notificaciones"
- [x] Flujo completo funcionando: Trigger → Evento → Acción → Notificación
- [x] Notificaciones recibidas durante la simulación de incidente

### **Verificación:**
- [x] Todos los hosts muestran estado disponible (verde) - o se identifican correctamente los problemas
- [x] Métricas recopilándose en Latest data con valores recientes
- [x] Problemas visibles correctamente en Problems
- [x] Sistema de monitoreo operativo y funcional

---

## **9. Resumen del ejercicio**

Este ejercicio final ha integrado todos los conceptos aprendidos durante el workshop:

1. **Organización de infraestructura**: Se creó una estructura jerárquica de grupos que refleja la arquitectura real de la red.

2. **Aplicación de buenas prácticas**: Se utilizaron tags, organización lógica, y estructura clara para facilitar el mantenimiento.

3. **Verificación integral**: Se validó que todos los componentes del monitoreo (hosts, items, triggers, notificaciones) funcionen correctamente.

4. **Análisis de problemas**: Se demostró la capacidad de identificar y analizar problemas en tiempo real.

5. **Flujo completo de monitoreo**: Se validó el ciclo completo desde la recolección de datos hasta la notificación de problemas.

**Conceptos clave aplicados:**
- Organización jerárquica de grupos de hosts
- Uso de templates para estandarización
- Configuración de triggers con dependencias y severidades apropiadas
- Sistema completo de notificaciones y acciones
- Tags para categorización y filtrado
- Análisis de problemas y métricas históricas
- Configuración de períodos de mantenimiento
- Buenas prácticas de configuración y escalabilidad

---

> **💡 Nota**: Este ejercicio demuestra que todos los conceptos aprendidos pueden integrarse en una configuración de monitoreo completa y funcional para infraestructuras reales.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_1.png" alt="Ejercicio final solucion - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_2.png" alt="Ejercicio final solucion - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_3.png" alt="Ejercicio final solucion - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_4.png" alt="Ejercicio final solucion - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_5.png" alt="Ejercicio final solucion - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_6.png" alt="Ejercicio final solucion - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_7.png" alt="Ejercicio final solucion - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_8.png" alt="Ejercicio final solucion - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_9.png" alt="Ejercicio final solucion - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_10.png" alt="Ejercicio final solucion - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_11.png" alt="Ejercicio final solucion - Captura 11" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_12.png" alt="Ejercicio final solucion - Captura 12" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_13.png" alt="Ejercicio final solucion - Captura 13" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/Ejercicio%20final%20solucion_14.png" alt="Ejercicio final solucion - Captura 14" style="max-width: 100%; height: auto;">

</div>

</details>
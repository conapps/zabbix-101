# **7.3. Ejercicio práctico**

**Objetivo**: Configurar **notificaciones por correo electrónico** para recibir alertas cuando se activen los triggers configurados en el ejercicio anterior.

**<u>Pasos guiados</u>**

---

## **Demo: Configuración de Media Type y Action (mostrada por el instructor)**

> **📺 Esta sección será demostrada por el instructor antes del ejercicio práctico.**

El instructor mostrará cómo:

1. **Configurar un Media Type**:
    - Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Media types</strong></span> → <span style="color: blue;"><strong>Create media type</strong></span>.
    - Seleccionar el tipo: <strong>Email</strong>, <strong>Telegram</strong>, <strong>Slack</strong>, <strong>Webhook</strong> o <strong>Script</strong>.
    - Completar la configuración requerida (servidores SMTP, tokens, URLs, etc.).
    - Probar el envío de mensajes desde la opción <strong>Test</strong>.

2. **Crear una Action**:
    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: blue;"><strong>Create action</strong></span>.
    - Definir:
        - <strong>Name</strong>: Nombre claro y representativo.
        - <strong>Conditions</strong>: Host, host group, trigger name, severity, tag, etc.
        - <strong>Operations</strong>: A quién notificar.
        - Canal de notificación.
        - Mensaje personalizado.
        - (Opcional) <strong>Recovery operations</strong>: Enviar alertas cuando el problema se resuelve.
        - (Opcional) <strong>Update operations</strong>: Notificaciones adicionales si cambia el estado.

---

## **Ejercicio práctico: Configurar usuario y acción para recibir notificaciones**

### **1. Crear un usuario para recibir notificaciones**

**Objetivo**: Crear un usuario que recibirá las alertas por correo electrónico.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span> → <span style="color: blue;"><strong>Create user</strong></span>.

2. Configurar el usuario:

    1. **Username** *(parámetro obligatorio)*:

        → Username: `Notificaciones`

    2. **Groups** *(parámetro obligatorio)*:

        → Groups: Seleccionar `demo` *(crear el grupo si no existe)*

    3. **Password** *(parámetro obligatorio)*:

        → Password: *(dejar valores por defecto o establecer una contraseña)*

    4. **Media** (canales de notificación):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar un canal de notificación.

        → Configurar:
            - **Type**: Seleccionar `Email (HTML)` *(o el tipo de email configurado por el instructor)*
            - **Send to**: Ingresar **tu dirección de correo electrónico** *(la dirección donde quieres recibir las alertas)*
            - **When active**: *(dejar valores por defecto - normalmente 1-7,00:00-24:00 para recibir notificaciones todos los días)*
            - **Use if severity**: Seleccionar las severidades que quieres recibir:
                - ☑ Information
                - ☑ Warning
                - ☑ Average
                - ☑ High
                - ☑ Disaster
            - **Status**: `Enabled`

        → <span style="color: blue;"><strong>Add</strong></span> (Guardar el media)

    5. **Permissions** (permisos):

        → **Role**: Seleccionar `demo Role` *(o el rol asignado para participantes del workshop)*

        > **💡 Nota**: El rol `demo Role` proporciona permisos de solo lectura para visualizar dashboards, problemas y métricas, pero no permite modificar configuraciones. Esto es adecuado para usuarios que solo necesitan recibir notificaciones.

    6. **Resto de configuraciones**: *(dejar valores por defecto)*

    7. <span style="color: blue;"><strong>Add</strong></span> (Guardar el usuario)

3. Verificar que el usuario se haya creado correctamente:
    - Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span>.
    - Verificar que el usuario **"Notificaciones"** aparezca en la lista.
    - Verificar que tenga el grupo `demo` y el rol `demo Role` asignados.

---

### **2. Crear una acción para enviar notificaciones**

**Objetivo**: Crear una acción que se active cuando los triggers configurados en el [ejercicio 6.4](ejercicio-6.4.md) detecten problemas y envíe notificaciones al usuario creado.

> **💡 Recordatorio de los triggers configurados:**
>
> En el ejercicio anterior se configuraron tres triggers en el template **"Template Network Switch by SNMP"**:
> 1. `Interface {#IFDESCR}({#IFALIAS}): Link down` - Severity: **High**
> 2. `{#SNMPVALUE}: Average CPU utilization` - Severity: **Average**
> 3. `Warning memory utilization` - Severity: **Warning**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span> → <span style="color: blue;"><strong>Create action</strong></span>.

2. Configurar la acción:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `Notificar problemas de red y sistema`

    2. **Conditions** (condiciones para activar la acción):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar condiciones.

        → Configurar condiciones que se activen para los triggers del template:

        **Opción 1: Por severidad** (recomendado para este ejercicio):
        - Condition 1:
            - **Condition type**: `Trigger severity`
            - **Operator**: `equals`
            - **Severity**: `High`
        - Condition 2:
            - **Condition type**: `Trigger severity`
            - **Operator**: `equals`
            - **Severity**: `Average`
        - Condition 3:
            - **Condition type**: `Trigger severity`
            - **Operator**: `equals`
            - **Severity**: `Warning`

        > **💡 Nota**: Con estas condiciones, la acción se activará para triggers con severidad High, Average o Warning, cubriendo los tres triggers configurados anteriormente.

        **Opción 2: Por host group** (alternativa):
        - Condition:
            - **Condition type**: `Host group`
            - **Operator**: `equals`
            - **Host group**: `Switches` *(o el grupo donde están los hosts con el template)*

        **Opción 3: Por tag** (alternativa):
        - Condition:
            - **Condition type**: `Trigger tag`
            - **Tag**: `scope`
            - **Operator**: `equals`
            - **Value**: `availability` o `capacity` o `performance`

        > **💡 Recomendación**: Para este ejercicio, usar la **Opción 1 (por severidad)** es la más simple y cubre todos los triggers configurados.

    3. **Operations** (operaciones a ejecutar cuando se cumplan las condiciones):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar una operación.

        → Configurar la operación:

        - **Send to users**: Hacer clic en <span style="color: blue;"><strong>Add</strong></span> y seleccionar el usuario **"Notificaciones"** creado anteriormente.

        - **Send only to**: Seleccionar `Email (HTML)` *(o el tipo de media configurado)*

        - **Subject**: *(opcional, dejar por defecto o personalizar)*

            Ejemplo: `{TRIGGER.SEVERITY}: {TRIGGER.NAME} en {HOST.NAME}`

        - **Message**: *(opcional, dejar por defecto o personalizar)*

            Ejemplo de mensaje personalizado:
            ```
            Alerta: {TRIGGER.NAME}
            Host: {HOST.NAME}
            Severidad: {TRIGGER.SEVERITY}
            Estado: {TRIGGER.STATUS}
            Último valor: {ITEM.LASTVALUE}
            Hora: {EVENT.DATE} {EVENT.TIME}
            ```

        - **Operation details**:
            - **Step duration**: `1m` *(tiempo entre intentos de notificación)*
            - **Steps**: `1` *(número de pasos de escalamiento)*

        → <span style="color: blue;"><strong>Add</strong></span> (Guardar la operación)

    4. **Recovery operations** (operaciones cuando el problema se resuelve):

        → Hacer clic en <span style="color: blue;"><strong>Add</strong></span> para agregar una operación de recuperación.

        → Configurar:
            - **Send to users**: Seleccionar el usuario **"Notificaciones"**
            - **Send only to**: Seleccionar `Email (HTML)`
            - **Subject**: *(opcional)*

                Ejemplo: `Recuperado: {TRIGGER.NAME} en {HOST.NAME}`

            - **Message**: *(opcional, dejar por defecto)*

        → <span style="color: blue;"><strong>Add</strong></span> (Guardar la operación de recuperación)

    5. **Update operations** (operaciones cuando cambia el estado):

        → *(Opcional, dejar vacío para este ejercicio)*

    6. <span style="color: blue;"><strong>Add</strong></span> (Guardar la acción)

3. Verificar que la acción se haya creado correctamente:
    - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Actions</strong></span>.
    - Verificar que la acción **"Notificar problemas de red y sistema"** aparezca en la lista.
    - Verificar que las condiciones y operaciones estén configuradas correctamente.

---

### **3. Solicitar al instructor que genere un problema y verificar la notificación**

**Objetivo**: Validar que el sistema de notificaciones funcione correctamente.

1. **Solicitar al instructor que genere un problema**:
    - Pedir al instructor que active uno de los triggers configurados en el [ejercicio 6.4](ejercicio-6.4.md):
        - Trigger de interfaz (Link down)
        - Trigger de CPU (Average CPU utilization)
        - Trigger de memoria (Warning memory utilization)

    > **💡 Nota**: El instructor puede generar un problema de varias formas:
    > - Modificando temporalmente los umbrales de las macros (`{$CPU.UTIL.AVG}` o `{$MEMORY.UTIL.WAR}`) para que se activen más fácilmente.
    > - Simulando un problema en el dispositivo monitoreado.
    > - Usando la función "Test" en los triggers para generar eventos de prueba.

2. **Verificar en Zabbix que el problema se haya generado**:
    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span>.
    - Verificar que aparezca el problema generado por el instructor.
    - Verificar que la severidad coincida con la configurada en el trigger.

3. **Verificar que la acción se haya ejecutado**:
    - En la vista de **Problems**, hacer clic en el problema para ver los detalles.
    - Verificar en la pestaña **Actions** o **History** que la acción se haya ejecutado.
    - Verificar que aparezca el usuario **"Notificaciones"** como destinatario de la notificación.

4. **Verificar el correo electrónico**:
    - Revisar la bandeja de entrada del correo electrónico configurado en el usuario **"Notificaciones"**.
    - Verificar que haya llegado un correo con:
        - El asunto configurado (o el por defecto).
        - El nombre del trigger que se activó.
        - La información del host y el problema.
        - La severidad del problema.
    - *(Si no llega el correo, verificar la carpeta de spam o contactar al instructor para verificar la configuración del Media Type)*

5. **Verificar la notificación de recuperación** (opcional):
    - Una vez que el problema se resuelva (cuando el trigger vuelva a estado OK), verificar que llegue un correo de recuperación.
    - El correo debe indicar que el problema se ha resuelto.

---

## **Resumen del ejercicio**

Este ejercicio práctico cubre la configuración completa del sistema de notificaciones:

1. **Creación de usuario**: Se creó un usuario **"Notificaciones"** con permisos de solo lectura (`demo Role`) y configurado para recibir alertas por correo electrónico.

2. **Creación de acción**: Se configuró una acción que se activa cuando los triggers del template detectan problemas (por severidad High, Average o Warning) y envía notificaciones al usuario creado.

3. **Validación**: Se verificó que el sistema funcione correctamente generando un problema y confirmando que la notificación llegue al correo electrónico configurado.

**Conceptos clave cubiertos:**

- Creación de usuarios con permisos específicos
- Configuración de canales de notificación (Media) para usuarios
- Creación de acciones con condiciones y operaciones
- Configuración de operaciones de recuperación (Recovery operations)
- Uso de macros en mensajes de notificación (`{TRIGGER.NAME}`, `{HOST.NAME}`, etc.)
- Validación del flujo completo de notificaciones

---

> **💡 Nota importante:** Las acciones se ejecutan automáticamente cuando se cumplen las condiciones configuradas. Es importante verificar que las condiciones coincidan con los triggers que se quieren monitorear para asegurar que las notificaciones se envíen correctamente.


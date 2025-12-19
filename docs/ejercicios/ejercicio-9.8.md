# **9.8. Ejercicio práctico**

**Objetivo**: Aplicar **buenas prácticas de configuración y escalabilidad** reorganizando hosts en grupos lógicos, creando **roles y permisos personalizados**, configurando **grupos de usuarios** con permisos granulares, y verificando el control de acceso mediante usuarios de prueba.

---

## **1. Crear host y asignar template**

**Objetivo**: Crear un nuevo host y asignarle un template predefinido para aplicar buenas prácticas de uso de templates en lugar de crear ítems manuales.

> **💡 Referencia**: Para ver los detalles completos sobre cómo crear un host con configuración SNMP, consulta la sección [1. Crear un nuevo host](ejercicio-integrador.md#1-crear-un-nuevo-host) del ejercicio integrador.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → <span style="color: blue;"><strong>Create host</strong></span>

2. Configurar el host siguiendo la referencia mencionada:

    1. **Host name** *(parámetro obligatorio)*:

        → Host name: `SW-Demo3`

    2. Asociar un *template predefinido*.

        → Templates: `Cisco Nexus 9000 Series by SNMP`

        > **💡 Nota importante**: Al asignar un template al host, automáticamente se aplicarán todos los ítems, triggers, gráficos, reglas de descubrimiento y dashboards definidos en el template. Esto es una buena práctica porque:
        > - **Estandariza** el monitoreo de dispositivos similares
        > - **Facilita el mantenimiento** (los cambios en el template se propagan automáticamente)
        > - **Ahorra tiempo** al no tener que crear ítems manualmente

    3. **Groups** *(parámetro obligatorio)*:

        → Groups: `demo` y crear o seleccionar el grupo `Switches`

    4. **Interfaces**:

        → Interfaces: <span style="color: blue;"><strong>Add</strong></span> y seleccionar **SNMP**

        → IP address: `10.0.10.1`

        → Port: `161`

        → SNMP version: `SNMPv2`

        → Community: `{$SNMP_COMMUNITY}` *(usar la macro como en el ejercicio de referencia)*

    5. **Macros**:

        → Ir a la pestaña <span style="color: violet;"><strong>Macros</strong></span>

        → Agregar macro:

        - Macro: `{$SNMP_COMMUNITY}`
        - Value: `snmp-demo`
        - Type: **Secret Text**
        - Description: `Community SNMPv2`

        → <span style="color: blue;"><strong>Add</strong></span>

    6. **Description** *(opcional)*:

        → Description: `Switch virtual Cisco Nexus 9000 para demostración de templates`

    7. Configurar el **inventario** del host:

        → Ir a la pestaña <span style="color: violet;"><strong>Inventory</strong></span> del host.

        → Cambiar el modo de **Disabled** a **Automatic** *(necesario para que los items asociados al inventario puedan poblar automáticamente los campos)*

        > **💡 Nota:** El modo **Automatic** permite que los items configurados con "Populates host inventory field" actualicen automáticamente los campos del inventario del host.

    8. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. **Verificar que el template se haya aplicado correctamente**:

    1. En el host recientemente creado `SW-Demo3`, ir a la pestaña <span style="color: violet;"><strong>Items</strong></span> y verificar que aparezcan múltiples ítems heredados del template `Cisco Nexus 9000 Series by SNMP`

        > **💡 Nota**: Los ítems heredados del template mostrarán el nombre del template entre corchetes junto al nombre del elemento, por ejemplo: `[Template Cisco Nexus 9000 Series by SNMP] Nombre del item`.

    2. Ir a la pestaña <span style="color: violet;"><strong>Triggers</strong></span> y verificar que aparezcan triggers heredados del template `Cisco Nexus 9000 Series by SNMP`

    3. Ir a la pestaña <span style="color: violet;"><strong>Graphs</strong></span> y verificar que aparezcan gráficos heredados del template `Cisco Nexus 9000 Series by SNMP`

    4. Ir a la pestaña <span style="color: violet;"><strong>Discovery</strong></span> y verificar que aparezcan reglas de descubrimiento (LLD) heredadas del template `Cisco Nexus 9000 Series by SNMP`

    5. **Forzar la ejecución de las reglas de descubrimiento**:

        - En la pestaña <span style="color: violet;"><strong>Discovery</strong></span>, seleccionar las reglas de descubrimiento y hacer clic en <span style="color: blue;"><strong>Execute now</strong></span> (si está disponible) o esperar a que se ejecuten automáticamente según su intervalo configurado.
        - Esperar unos minutos para que Zabbix realice el descubrimiento.
        - Volver a la pestaña <span style="color: violet;"><strong>Items</strong></span> y verificar que ahora aparezcan nuevos ítems descubiertos automáticamente (por ejemplo, interfaces de red, discos, etc.).

    6. Verificar la conectividad del host **"SW-Demo3"**:

        - Verificar la columna **Availability**:
            - <span style="color: green;">🟢 Verde</span> → Host disponible y agente respondiendo.
            - <span style="color: red;">🔴 Rojo</span> → Host no disponible o agente no responde.
            - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

    7. **Verificar la recopilación de datos**:

        - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span>
        - Filtrar por el host `SW-Demo3`
        - Verificar que se estén recopilando datos de los ítems SNMP (valores con timestamp reciente)
        - Verificar que los campos del inventario del host se hayan poblado automáticamente con información del dispositivo (ir a la pestaña <span style="color: violet;"><strong>Inventory</strong></span> del host y verificar campos como "Name", "Type", "Serial number", etc.)

---

## **2. Reorganizar grupos de hosts**

**Objetivo**: Crear una estructura jerárquica de grupos de hosts para mejorar la organización y facilitar la asignación de permisos.

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Host groups</strong></span> → <span style="color: blue;"><strong>Create host group</strong></span>

2. Crear la siguiente estructura de grupos:

    1. **Grupo principal "Clientes"**:

        → Name: `Clientes`

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    2. **Grupo "Cliente Demo"**:

        → Name: `Clientes/Cliente Demo`

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    3. **Subgrupo "Servidores"**:

        → Name: `Clientes/Cliente Demo/Servidores`

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    4. **Subgrupo "Networking"**:

        → Name: `Clientes/Cliente Demo/Networking`

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    > **💡 Nota**: Esta estructura jerárquica facilita la organización y permite asignar permisos de forma granular. Por ejemplo, un usuario puede tener acceso solo a "Servidores" o a todo el "Cliente Demo".

3. **Mover hosts existentes a los nuevos grupos**:

    1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>

    2. Seleccionar los hosts que quieras reorganizar (por ejemplo, los hosts de servidores).

    3. Hacer clic en <span style="color: blue;"><strong>Mass update</strong></span> (actualización masiva).

    4. En la pestaña **Host**:

        → Tildar la opción **Host groups** → <span style="color: blue;"><strong>Add</strong> (Agregar)</span> y buscar y seleccionar el grupo `Clientes/Cliente Demo/Servidores`

        → <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

    > **💡 Nota**: Si prefieres hacerlo de forma individual, puedes editar cada host y modificar sus grupos en la pestaña **Groups**.

---

## **3. Crear un rol de usuario personalizado**

**Objetivo**: Crear un rol personalizado con permisos limitados para usuarios que solo necesiten visualizar datos de monitoreo y recibir notificaciones.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>User roles</strong></span> → <span style="color: blue;"><strong>Create user role</strong></span>

2. Configurar el rol:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `Notificaciones role`

    2. **User type**:

        → User type: `User` *(permisos de usuario)*

    6. **Access to UI elements**:

        → Se puede definir el acceso a los elementos de la interfaz web, como: **Monitoring**, **Services**, **Inventory**, **Reports**

        - **Monitoring**: Permite ver los datos de monitoreo.
        - **Services**: Permite ver los servicios.
        - **Inventory**: Permite ver el inventario.
        - **Reports**: Permite ver los reportes.

        → <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **4. Crear grupo de usuarios personalizado**

**Objetivo**: Crear un grupo de usuarios para aplicar permisos y configuraciones en bloque.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>User groups</strong></span> → <span style="color: blue;"><strong>Create user group</strong></span>

2. Configurar el grupo:

    1. **Group name** *(parámetro obligatorio)*:

        → Group name: `Cliente Demo`

    2. **Users**:

        → Por ahora dejar vacío (agregaremos usuarios después).

    3. **Frontend access**:

        → Frontend access: `System default` *(usará el acceso definido en el rol)*

    4. **Debug mode**:

        → Debug mode: *(dejar deshabilitado)*

     5. Pestaña **Permissions**:

        → Configurar permisos sobre **Host groups**:

        - Seleccionar el grupo `Clientes/Cliente Demo`
        - Permission: `Read` *(solo lectura)*

        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

        > Visualizar que el grupo `Clientes/Cliente Demo` tiene permisos de lectura.

        > **💡 Nota**: Los permisos del grupo se aplicarán a todos los usuarios del grupo. Los permisos más restrictivos siempre tienen prioridad.

    6. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **5. Crear otro grupo de usuarios personalizado**

**Objetivo**: Crear un grupo de usuarios para aplicar permisos y configuraciones en bloque.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>User groups</strong></span> → <span style="color: blue;"><strong>Create user group</strong></span>

2. Configurar el grupo:

    1. **Group name** *(parámetro obligatorio)*:

        → Group name: `Notificaciones Demo`

    2. **Users**:

        → Por ahora dejar vacío (agregaremos usuarios después).

    3. **Frontend access**:

        → Frontend access: `Disabled` *(deshabilitar el acceso al frontend)*

    4. **Debug mode**:

        → Debug mode: *(dejar deshabilitado)*

     5. Pestaña **Permissions**:

        → Configurar permisos sobre **Host groups**:

        - Seleccionar el grupo `Clientes`
        - Permission: `Read-write` *(lectura y escritura)*
        - Seleccionar: `Include subgroups` *(incluir los subgrupos)*

        - <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

        > Visualizar que el grupo `Clientes` (y todos sus subgrupos, incluido `Clientes/Cliente Demo`) tiene permisos de lectura y escritura.

        > **💡 Nota**: Los permisos del grupo se aplicarán a todos los usuarios del grupo. Los permisos más restrictivos siempre tienen prioridad. Al seleccionar "Include subgroups", los permisos se aplican a todos los subgrupos anidados.

    6. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

---

## **6. Modificar usuario "Notificaciones"**

**Objetivo**: Modificar el usuario "Notificaciones".

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span> → seleccionar el usuario "Notificaciones" ye ingresar para editarlo:

    1. **Groups** *(parámetro obligatorio)*:
        
        → Groups: Quitar el grupo `demo` y seleccionar el grupo `Cliente Demo`


    2. **User role**:

        → User role: Quitar el rol `demo Role` y seleccionar el rol `Notificaciones role`

    3. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

---

## **7. Verificar permisos del usuario "Notificaciones"**

**Objetivo**: Confirmar que el usuario "Notificaciones" tiene los permisos correctos.

1. **Cerrar sesión** del usuario actual:

    - Hacer clic en el usuario actual en la esquina superior derecha
    - Seleccionar <span style="color: blue;"><strong>Sign out</strong></span>

    **O** usar una **ventana de incógnito** del navegador:

    - Abrir una nueva ventana de incógnito (Ctrl+Shift+N en Chrome/Edge, Ctrl+Shift+P en Firefox)

2. **Iniciar sesión con el usuario "Notificaciones"**:

    - Username: `Notificaciones`
    - Password: `Demo123!` *(o la contraseña configurada en el [ejercicio 7.3](../ejercicios/ejercicio-7.3.md))*

3. **Verificar las restricciones de acceso**:

    - ✅ Debe poder ver: **Monitoring**, **Services**, **Inventory**, **Reports** (solo lectura)
    - ❌ No debe poder ver: **Configuration**, **Administration** (no debe aparecer en el menú)

4. **Verificar permisos sobre host groups**:

    - Ir a <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>
    - Debe poder ver solo los hosts del grupo `Clientes/Cliente Demo` (si configuraste ese permiso)
    - Intentar acceder a otros hosts fuera de ese grupo (si existen) - no debería poder verlos o deberían estar ocultos

5. **Verificar que no puede modificar configuraciones**:

    - Intentar acceder directamente a una URL de configuración (si es posible)
    - Debe mostrar un error de permisos insuficientes

6. **Volver a iniciar sesión** con el usuario original (demo):

    - Username: `demo`
    - Password: `Zabbix123!`

---

## **8. Modificar usuario "Notificaciones" para que no tenga acceso al frontend**

**Objetivo**: Modificar el usuario "Notificaciones" para que no tenga acceso al frontend.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>Users</strong></span> → seleccionar el usuario "Notificaciones" ye ingresar para editarlo:

    1. **Groups** *(parámetro obligatorio)*:
        
        → Groups: Quitar el grupo `Cliente Demo` y seleccionar el grupo `Notificaciones Demo`


    2. **User role**:

        → User role: Mantener el rol `Notificaciones role`

    3. <span style="color: blue;"><strong>Update</strong> (Actualizar)</span>

---

## **9. Verificar que el usuario "Notificaciones" no puede acceder al frontend**

**Objetivo**: Confirmar que el usuario con Frontend access: Disabled no puede iniciar sesión en la interfaz web.

1. **Cerrar sesión** del usuario actual:

    - Hacer clic en el usuario actual en la esquina superior derecha
    - Seleccionar <span style="color: blue;"><strong>Sign out</strong></span>

    **O** usar una **ventana de incógnito** del navegador:

    - Abrir una nueva ventana de incógnito (Ctrl+Shift+N en Chrome/Edge, Ctrl+Shift+P en Firefox)

2. **Intentar iniciar sesión con el usuario "Notificaciones"**:

    - Username: `Notificaciones`
    - Password: `Demo123!` *(o la contraseña configurada en el [ejercicio 7.3](../ejercicios/ejercicio-7.3.md))*

3. **Verificar el mensaje de error**:

    - Debe aparecer un mensaje indicando que el acceso está deshabilitado o que las credenciales no son válidas para acceso al frontend.

    > **💡 Nota**: Esto confirma que el usuario no puede acceder a la interfaz web, pero seguirá recibiendo notificaciones por los canales configurados (correo, SMS, etc.).

4. **Volver a iniciar sesión** con el usuario original (demo):

    - Username: `demo`
    - Password: `Zabbix123!`


## **10. Resumen del ejercicio**

Este ejercicio práctico cubre las siguientes buenas prácticas de configuración y escalabilidad:

1. **Asignación de templates**: Se creó el host `SW-Demo3` y se le asignó el template `Cisco Nexus 9000 Series by SNMP` para aplicar buenas prácticas de uso de templates en lugar de crear ítems manuales.

2. **Organización de grupos de hosts**: Se creó una estructura jerárquica de grupos (`Clientes/Cliente Demo/Servidores`) que facilita la gestión y asignación de permisos.

3. **Roles de usuario personalizados**: Se creó un rol "Notificaciones role" con permisos limitados para usuarios que solo necesitan recibir notificaciones.

4. **Grupos de usuarios**: Se crearon grupos de usuarios (`Cliente Demo`, `Notificaciones Demo`) con diferentes niveles de permisos (Read, Read-write) para aplicar permisos y configuraciones en bloque.

5. **Permisos granulares**: Se asignaron permisos específicos sobre grupos de hosts (Read, Read-write) con opción de incluir subgrupos, para controlar el acceso de forma jerárquica.

6. **Modificación de usuarios existentes**: Se modificó el usuario "Notificaciones" (creado en el ejercicio 7.3) asignándole diferentes grupos y roles para demostrar cómo cambiar permisos dinámicamente.

7. **Usuarios sin acceso al frontend**: Se configuró el grupo "Notificaciones Demo" con Frontend access: Disabled y se asignó al usuario "Notificaciones". Se verificó que el usuario no puede acceder al frontend mediante prueba de login, pero sigue recibiendo notificaciones.

> **💡 Buenas prácticas aplicadas:**
> - **Separación de responsabilidades**: Usuarios de solo notificaciones vs usuarios operativos vs administradores.
> - **Principio de menor privilegio**: Cada usuario tiene solo los permisos necesarios para su función.
> - **Organización jerárquica**: Grupos de hosts estructurados facilitan la gestión y escalabilidad.
> - **Multi-tenancy**: La estructura permite aislar recursos por cliente o departamento.

---

## **11. Limpieza (opcional)**

Si deseas limpiar los elementos creados durante este ejercicio:

1. **Modificar el usuario "Notificaciones"** (revertir a su estado original del ejercicio 7.3):
    - Cambiar el grupo de `Notificaciones Demo` de vuelta a `demo`
    - Cambiar el rol de vuelta a `demo Role`

2. **Eliminar grupos de usuarios creados**: `Cliente Demo`, `Notificaciones Demo`

3. **Eliminar rol personalizado**: `Notificaciones role`

4. **Revertir grupos de hosts** a su estado original (opcional):
    - Mover hosts de vuelta a sus grupos originales si se reorganizaron

5. **Eliminar host creado** (opcional): `SW-Demo3`

> **💡 Nota importante**: 
> - El usuario "Notificaciones" fue creado en el [ejercicio 7.3](ejercicio-7.3.md), por lo que NO debe eliminarse, solo revertirse a su configuración original.
> - Consulta con el instructor antes de eliminar elementos si otros ejercicios dependen de ellos.

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="9.8. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="9.8. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_3.png" alt="9.8. Ejercicio práctico - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_4.png" alt="9.8. Ejercicio práctico - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_5.png" alt="9.8. Ejercicio práctico - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_6.png" alt="9.8. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_6_2.png" alt="9.8. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_7.png" alt="9.8. Ejercicio práctico - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_8.png" alt="9.8. Ejercicio práctico - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_9.png" alt="9.8. Ejercicio práctico - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_10.png" alt="9.8. Ejercicio práctico - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_11.png" alt="9.8. Ejercicio práctico - Captura 11" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_12.png" alt="9.8. Ejercicio práctico - Captura 12" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_13.png" alt="9.8. Ejercicio práctico - Captura 13" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_14.png" alt="9.8. Ejercicio práctico - Captura 14" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_15.png" alt="9.8. Ejercicio práctico - Captura 15" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_16.png" alt="9.8. Ejercicio práctico - Captura 16" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_17.png" alt="9.8. Ejercicio práctico - Captura 17" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_18.png" alt="9.8. Ejercicio práctico - Captura 18" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/9.8.%20Ejercicio%20pr%C3%A1ctico_19.png" alt="9.8. Ejercicio práctico - Captura 19" style="max-width: 100%; height: auto;">

</div>

</details>
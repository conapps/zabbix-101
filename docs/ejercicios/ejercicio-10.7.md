# **10.7. Ejercicio práctico (Opcional: Avanzado)**

**Objetivo**: Realizar consultas básicas a la **API de Zabbix** para obtener información de hosts y realizar operaciones sencillas.

> **💡 Nota importante:** Este ejercicio es completamente opcional y está orientado a participantes que deseen profundizar en el uso de la API de Zabbix. La funcionalidad básica de la API ya fue demostrada por el instructor en la sección 10.1 del Módulo 10.

---

## **1. Generar un API Token**

**Objetivo**: Crear un token de API para autenticarse en las solicitudes a la API de Zabbix.

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>General</strong></span> → <span style="color: violet;"><strong>API tokens</strong></span> → <span style="color: blue;"><strong>Create API token</strong></span>

2. Configurar el token:

    1. **Name** *(parámetro obligatorio)*:

        → Name: `{$ZABBIX.TOKEN}`

    2. **User** *(parámetro obligatorio)*:

        → User: Seleccionar un usuario (por ejemplo, `demo`)

    3. **Expires at** *(opcional)*:

        → Dejar vacío o configurar una fecha de expiración si lo deseas.

        > **💡 Nota**: En producción, se recomienda establecer fechas de expiración para tokens de API por razones de seguridad.

    4. **Description** *(opcional)*:

        → Description: `Token para realizar ejercicios prácticos con la API de Zabbix.`

    5. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

3. **Copiar el token generado**:

    - Después de guardar, el token se mostrará una sola vez.
    - **⚠️ Importante**: Copia el token (**Auth token:**) y guárdalo en un lugar seguro, ya que no podrás verlo nuevamente después de cerrar esta ventana.

    > **💡 Nota**: El token tendrá un formato similar a: `abc123def456ghi789jkl012mno345pqr678stu901vwx234yz`

---

## **2. Consultar la versión de Zabbix (Ejemplo básico sin autenticación)**

**Objetivo**: Realizar una consulta básica a la API de Zabbix para obtener la versión instalada, sin necesidad de autenticación.

> **💡 Nota**: El método `apiinfo.version` es uno de los pocos métodos de la API que no requiere autenticación, lo que lo hace ideal para comenzar a aprender a usar la API de Zabbix.

### **2.1. Consulta básica con cURL**

1. **Abrir una terminal o línea de comandos**.

2. **Realizar una consulta para obtener la versión de Zabbix**:

    ```bash
    curl -X POST -H "Content-Type: application/json" \
    -d '{
        "jsonrpc": "2.0",
        "method": "apiinfo.version",
        "params": {},
        "auth": null,
        "id": 1
    }' \
    https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php
    ```

    > **💡 Nota**: 
    > - Reemplaza `X` en la URL con el número asignado a tu zabbix.
    > - El método `apiinfo.version` no requiere autenticación, por lo que `auth` es `null`.
    > - Este método no requiere parámetros, por lo que `params` es un objeto vacío `{}`.

3. **Verificar la respuesta**:

    - La respuesta debería ser un JSON con la versión de Zabbix.
    - Si la consulta fue exitosa, verás un objeto JSON con la versión.

    > **💡 Ejemplo de respuesta exitosa**:
    > ```json
    > {
    >     "jsonrpc": "2.0",
    >     "result": "6.0.0",
    >     "id": 1
    > }
    > ```

---

### **2.2. Usar la API desde un Item HTTP Agent - Versión de Zabbix**

**Objetivo**: Crear un item de tipo **HTTP Agent** que consulte la versión de Zabbix mediante la API, sin necesidad de usar herramientas externas como cURL.

> **💡 Nota**: Este es un ejemplo básico que no requiere autenticación, ideal para comenzar a trabajar con items HTTP Agent que consultan la API de Zabbix.

1. **Navegar al host donde deseas crear el item**:

   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>
   - Seleccionar el host **SRV-Demo-Web-Server** (o el host que desees)
   - Hacer clic en <span style="color: blue;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. **Configurar los parámetros básicos del item**:

    - **Name**: `Consulta API - Version de Zabbix Server`

    - **Type**: Seleccionar **HTTP agent**

    - **Key**: `check.api.zbx.version`

    - **Type of information**: Seleccionar **Text**

    - **URL**: `http://zabbix-web:8080/api_jsonrpc.php`
        > **💡 Nota**: `zabbix-web` es el nombre del servicio interno de Zabbix. Si estás usando una URL externa, puedes usar `https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php`
    
    - **Request type**: Seleccionar **POST**
    
    - **Timeout**: `10s`
    
    - **Request body type**: Seleccionar **Raw data**
    
    - **Request body**: 
        ```json
        {
          "jsonrpc": "2.0",
          "method": "apiinfo.version",
          "params": {},
          "id": 1
        }
        ```
        > **💡 Nota**: Este método no requiere autenticación, por lo que no incluimos el campo `auth` en el request body.
    
    - **Headers**: Agregar el siguiente header:
        - **Name**: `Content-Type` | **Value**: `application/json`
    
    - **Required status codes**: `200`
    
    - **Retrieve mode**: Seleccionar **Body**

    - **Update interval**: `1m` (o el intervalo que prefieras)

4. **Configurar la pestaña "Preprocessing"**:

    - **Preprocessing steps**: Agregar un paso de preprocesamiento:

        - **Name**: `JavaScript`
        - **Parameters**: 
            ```javascript
            return JSON.parse(value).result;
            ```
        > **💡 Nota**: Este paso de preprocesamiento JavaScript:
        > - Parsea la respuesta JSON de la API
        > - Extrae el valor `result` que contiene la versión de Zabbix (por ejemplo, "7.0.0")
        > - El valor resultante será un texto con la versión de Zabbix

5. **Verificar el funcionamiento antes de guardar**:
    - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> (ubicado en la parte inferior del formulario)
    - Esto ejecutará una prueba de la consulta HTTP y mostrará el resultado
    - **Resultado esperado del test**:
        - Deberías ver la respuesta JSON de la API en la sección "Result"
        - El valor procesado (después del preprocesamiento JavaScript) debería mostrar la **versión de Zabbix** (por ejemplo, `6.0.0`)
    - Si el test es exitoso, el valor procesado mostrará la versión correctamente
    - Si hay algún error, revisa:
        - Que la URL sea accesible
        - Que los headers estén correctamente configurados
        - Que el request body tenga el formato JSON correcto

6. **Guardar el item**:
    - Una vez que el test muestre el resultado correcto (la versión de Zabbix), hacer clic en <span style="color: blue;"><strong>Add</strong></span> para guardar el item

---

## **3. Realizar una consulta básica a la API para listar hosts**

**Objetivo**: Realizar una consulta básica a la API de Zabbix para obtener la lista de hosts monitoreados.

> **💡 Nota**: Para realizar consultas a la API, puedes usar diferentes herramientas:
> - **cURL** (desde la línea de comandos)
> - **Postman** (interfaz gráfica)
> - **Python** con la librería `requests`
> - **JavaScript** con `fetch` o `axios`
>
> En este ejercicio se mostrará el ejemplo usando **cURL**, pero puedes adaptarlo a la herramienta que prefieras.

### **3.1. Consulta básica con cURL**

1. **Abrir una terminal o línea de comandos**.

2. **Realizar una consulta para listar hosts**:

    ```bash
    curl -X POST -H "Content-Type: application/json" \
    -d '{
        "jsonrpc": "2.0",
        "method": "host.get",
        "params": {
            "output": ["hostid", "host", "name", "status"]
        },
        "auth": "TOKEN_API",
        "id": 1
    }' \
    https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php
    ```

    > **💡 Nota**: 
    > - Reemplaza `TOKEN_API` con el token generado en el paso 1.
    > - Reemplaza `X` en la URL con el número asignado a tu zabbix.
    > - El método `host.get` obtiene información de los hosts.
    > - El parámetro `output` especifica qué campos deseas obtener.

3. **Verificar la respuesta**:

    - La respuesta debería ser un JSON con la lista de hosts.
    - Si la consulta fue exitosa, verás un array de objetos con información de cada host.

    > **💡 Ejemplo de respuesta exitosa**:
    > ```json
    > {
    >     "jsonrpc": "2.0",
    >     "result": [
    >         {
    >             "hostid": "10001",
    >             "host": "Zabbix server",
    >             "name": "Zabbix server",
    >             "status": "0"
    >         },
    >         {
    >             "hostid": "10002",
    >             "host": "SW-Demo1",
    >             "name": "SW-Demo1",
    >             "status": "0"
    >         }
    >     ],
    >     "id": 1
    > }
    > ```

---

### **3.2. Usar la API desde un Item HTTP Agent - Cantidad de Hosts (Ejemplo con autenticación)**

**Objetivo**: Crear un item de tipo **HTTP Agent** que consulte la API de Zabbix para obtener la cantidad de hosts activos, utilizando autenticación mediante token.

> **💡 Nota**: Este ejemplo requiere autenticación mediante token de API. Asegúrate de haber completado la sección 1 para generar el token y configurar la macro `{$ZABBIX.TOKEN}`.

1. **Navegar al host donde deseas crear el item**:

1. **Navegar al host donde deseas crear el item**:

   - Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span>
   - Seleccionar el host **SRV-Demo-Web-Server** (o el host que desees)
   - Hacer clic en <span style="color: blue;"><strong>Items</strong></span> → <span style="color: blue;"><strong>Create item</strong></span>

2. **Configurar los parámetros básicos del item**:

    - **Name**: `Consulta API - Host de Zabbix Server`

    - **Type**: Seleccionar **HTTP agent**

    - **Key**: `check.api.zbx.host`

    - **Type of information**: Seleccionar **Numeric (unsigned)**

    - **Update interval**: `1m` (o el intervalo que prefieras)

    - **URL**: `http://zabbix-web:8080/api_jsonrpc.php`
        > **💡 Nota**: `zabbix-web` es el nombre del servicio interno de Zabbix. Si estás usando una URL externa, puedes usar `https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php`
    
    - **Request type**: Seleccionar **POST**
    
    - **Timeout**: `10s`
    
    - **Request body type**: Seleccionar **Raw data**
    
    - **Request body**: 
        ```json
        {
          "jsonrpc": "2.0",
          "method": "host.get",
          "params": {
            "output": ["hostid"],
            "filter": { "status": 0 }
          },
          "auth": "{$ZABBIX.TOKEN}",
          "id": 1
        }
        ```
        > **💡 Nota**: `{$ZABBIX.TOKEN}` es una macro de usuario que debe contener el token de API generado en el paso 1. Asegúrate de que esta macro esté definida para el usuario o el host correspondiente.
    
    - **Headers**: Agregar los siguientes headers:

        - **Name**: `Content-Type` | **Value**: `application/json`

    - **Required status codes**: `200`
    
    - **Retrieve mode**: Seleccionar **Body**

4. **Configurar la pestaña "Preprocessing"**:

    - **Preprocessing steps**: Agregar un paso de preprocesamiento:

        - **Name**: `JavaScript`

        - **Parameters**: 
            ```javascript
            return JSON.parse(value).result.length;
            ```
        > **💡 Nota**: Este paso de preprocesamiento JavaScript:
        > - Parsea la respuesta JSON de la API
        > - Extrae el array `result` de la respuesta
        > - Retorna la cantidad de hosts activos (`.length`)
        > - El valor resultante será un número que representa la cantidad de hosts activos en Zabbix

5. **Verificar el funcionamiento antes de guardar**:
    - Hacer clic en el botón <span style="color: blue;"><strong>Test</strong></span> (ubicado en la parte inferior del formulario)
    - Esto ejecutará una prueba de la consulta HTTP y mostrará el resultado
    - **Resultado esperado del test**:
        - Deberías ver la respuesta JSON de la API en la sección "Result"
        - El valor procesado (después del preprocesamiento JavaScript) debería mostrar un número que representa el **total de hosts activos** en Zabbix
        - Por ejemplo, si hay 7 hosts activos, el valor procesado debería ser `7`
    - Si el test es exitoso, el valor procesado mostrará la cantidad de hosts activos correctamente
    - Si hay algún error, revisa:
        - Que la macro `{$ZABBIX.TOKEN}` esté correctamente definida
        - Que la URL sea accesible
        - Que los headers estén correctamente configurados

6. **Guardar el item**:
    - Una vez que el test muestre el resultado correcto (el total de hosts activos), hacer clic en <span style="color: blue;"><strong>Add</strong></span> para guardar el item

> **💡 Ventajas de usar HTTP Agent para consultas a la API**:
> - **Automatización**: El item consulta la API automáticamente según el intervalo configurado.
> - **Historial**: Los valores se almacenan en la base de datos de Zabbix, permitiendo crear gráficos y análisis de tendencias.
> - **Alertas**: Puedes crear triggers basados en estos valores (por ejemplo, alertar si el número de hosts activos cambia)
> - **Integración**: Los datos están disponibles en dashboards y reportes de .

> **⚠️ Consideraciones importantes**:
> - **Definición de la macro `{$ZABBIX.TOKEN}`**: La macro puede estar definida en diferentes niveles, y Zabbix buscará la macro en el siguiente orden de prioridad:
>   1. **A nivel del host**: <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → Seleccionar el host → <span style="color: violet;"><strong>Macros</strong></span>
>   2. **A nivel del template**: Si el host tiene un template asociado, la macro puede estar definida en el template → <span style="color: violet;"><strong>Macros</strong></span>
>   3. **A nivel global**: <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>General</strong></span> → <span style="color: violet;"><strong>Macros</strong></span>
> - El item utilizará la macro del nivel más específico disponible (host > template > global).
> - Asegúrate de que la macro contenga un token válido generado previamente (ver sección 1).
> - El token debe tener los permisos necesarios para realizar la consulta.
> - Considera la seguridad: los tokens deben tener fechas de expiración y permisos limitados según sea necesario.

---

## **4. Realizar consultas adicionales (opcional)**

**Objetivo**: Explorar otras consultas útiles a la API de Zabbix para obtener información de diferentes elementos.

### **4.1. Obtener información de triggers**

Realizar una consulta para obtener los triggers configurados:

```bash
curl -X POST -H "Content-Type: application/json" \
-d '{
    "jsonrpc": "2.0",
    "method": "trigger.get",
    "params": {
        "output": ["triggerid", "description", "priority", "value"],
        "selectHosts": ["host"]
    },
    "auth": "TOKEN_API",
    "id": 2
}' \
https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php
```

> **💡 Nota**: Esta consulta obtiene información de triggers, incluyendo su descripción, prioridad (severidad) y estado. El parámetro `selectHosts` incluye información del host asociado a cada trigger.

### **4.2. Obtener información de items**

Realizar una consulta para obtener los items de un host específico:

```bash
curl -X POST -H "Content-Type: application/json" \
-d '{
    "jsonrpc": "2.0",
    "method": "item.get",
    "params": {
        "output": ["itemid", "name", "key_", "lastvalue", "lastclock"],
        "hostids": ["10001"],
        "limit": 10
    },
    "auth": "TOKEN_API",
    "id": 3
}' \
https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php
```

> **💡 Nota**: 
> - Reemplaza `10001` con el `hostid` de un host que desees consultar (puedes obtenerlo de la consulta anterior).
> - El parámetro `limit` limita el número de resultados (útil para evitar respuestas muy grandes).
> - Esta consulta obtiene información de items, incluyendo su nombre, clave, último valor y timestamp del último valor.

### **4.3. Obtener información de templates**

Realizar una consulta para obtener los templates disponibles:

```bash
curl -X POST -H "Content-Type: application/json" \
-d '{
    "jsonrpc": "2.0",
    "method": "template.get",
    "params": {
        "output": ["templateid", "host", "name"],
        "search": {
            "host": "Linux"
        }
    },
    "auth": "TOKEN_API",
    "id": 4
}' \
https://alertasX.conatel-lab.conatel.cloud/api_jsonrpc.php
```

> **💡 Nota**: Esta consulta busca templates que contengan "Linux" en su nombre. El parámetro `search` permite filtrar resultados.

---

## **5. Resumen del ejercicio**

Este ejercicio práctico cubre los siguientes conceptos básicos de la API de Zabbix:

1. **Generación de API tokens**: Creación de tokens de autenticación para acceder a la API de forma segura.

2. **Consulta básica de versión (sin autenticación)**:
   - Uso del método `apiinfo.version` mediante cURL para obtener la versión de Zabbix.
   - Creación de un item HTTP Agent que consulta la versión automáticamente.

3. **Consulta básica de hosts (con autenticación)**:
   - Uso del método `host.get` mediante cURL para obtener información de hosts monitoreados.
   - Creación de un item HTTP Agent que consulta la cantidad de hosts activos utilizando tokens de API.

4. **Consultas adicionales**: Exploración de otros métodos útiles como:
   - `trigger.get` para obtener información de triggers
   - `item.get` para obtener información de items
   - `template.get` para obtener información de templates

**Conceptos clave cubiertos:**

- Autenticación mediante API tokens
- Estructura de solicitudes JSON-RPC
- Métodos de API más comunes (`apiinfo.version`, `host.get`, `trigger.get`, `item.get`, `template.get`)
- Parámetros útiles (`output`, `selectHosts`, `hostids`, `limit`, `search`)
- Interpretación de respuestas JSON
- Uso de HTTP Agent para consultas automáticas a la API
- Preprocesamiento con JavaScript para extraer datos de respuestas JSON

> **💡 Nota importante**: La API de Zabbix es muy extensa y permite realizar muchas más operaciones además de consultas (como crear, actualizar o eliminar hosts, items, triggers, etc.). Este ejercicio cubre solo las consultas básicas más comunes.

---

## **6. Limpieza (opcional)**

Si deseas limpiar el token creado durante este ejercicio:

1. Ir a <span style="color: purple;"><strong>Administration</strong></span> → <span style="color: violet;"><strong>API tokens</strong></span>
2. Seleccionar el token creado y hacer clic en <span style="color: red;"><strong>Delete</strong></span>

> **💡 Nota**: Si no eliminas el token, este seguirá activo y podrá usarse para realizar consultas a la API. En producción, es importante gestionar adecuadamente los tokens por razones de seguridad.

---

> **📚 Documentación oficial:** Para más detalles y ejemplos de uso de la API, consulta:
> - [Zabbix - API Reference](https://www.zabbix.com/documentation/6.0/es/manual/api/reference)
> - [Zabbix - API Examples](https://www.zabbix.com/documentation/6.0/es/manual/api)

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="10.7. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="10.7. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_3.png" alt="10.7. Ejercicio práctico - Captura 3" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_4.png" alt="10.7. Ejercicio práctico - Captura 4" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_5.png" alt="10.7. Ejercicio práctico - Captura 5" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_6.png" alt="10.7. Ejercicio práctico - Captura 6" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_7.png" alt="10.7. Ejercicio práctico - Captura 7" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_8.png" alt="10.7. Ejercicio práctico - Captura 8" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_9.png" alt="10.7. Ejercicio práctico - Captura 9" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_10.png" alt="10.7. Ejercicio práctico - Captura 10" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/10.7.%20Ejercicio%20pr%C3%A1ctico_11.png" alt="10.7. Ejercicio práctico - Captura 11" style="max-width: 100%; height: auto;">

</div>

</details>
# **4.8. Ejercicio práctico**

**Objetivo**: Agregar un host desde cero, asociar un template y verificar métricas.

**<u>Procedimiento básico</u>**

1. Ir a <span style="color: purple;"><strong>Configuration</strong></span> → <span style="color: violet;"><strong>Hosts</strong></span> → <span style="color: blue;"><strong>Create host</strong></span>

2. Configurar:

    1. Nombre del host *(parámetro obligatorio)*.

        → Host name: `SRV-Test`

    2. Asociar un *template predefinido*.

        → Templates: `Linux by Zabbix agent`

        > **💡 ¿Qué proporciona este template?**
        >
        > El template **"Linux by Zabbix agent"** incluye una colección predefinida de items, triggers y gráficos para monitorear servidores Linux. Incluye métricas como: CPU, memoria, disco, red, etc. Al asociarlo al host, estos elementos se aplican automáticamente sin necesidad de configurarlos manualmente.

        > **📚 Documentación oficial:** Para más detalles sobre el templates listos para usar, consulta [Zabbix - Templates](https://www.zabbix.com/documentation/6.0/es/manual/config/templates_out_of_the_box).

    3. Elegir un Grupo de hosts *(parámetro obligatorio)*.

        → Groups: `demo` y `Linux servers`

    4. Configurar **interfaces** para el método de monitoreo con **agente Zabbix**:

        → Interfaces: <span style="color: blue;"><strong>Add</strong> (Guardar)</span> y seleccionar <strong>Agent</strong> quedando 'Type: Agent'.

        → DNS name: `test.conatel-lab.conatel.cloud`

        → Seleccionar en 'Connect to': <strong>DNS</strong>.

        > **💡 ¿Qué es el agente Zabbix?**
        >
        > El **agente Zabbix** es un software ligero que se instala en el servidor a monitorear. Se comunica con el servidor Zabbix/Proxy para enviar métricas del sistema (CPU, memoria, disco, red, etc.) de forma activa o pasiva. A diferencia de SNMP, el agente Zabbix proporciona monitoreo más detallado y específico para sistemas operativos.

    5. <span style="color: blue;"><strong>Add</strong> (Guardar)</span>

    6. Verificar la conectividad

        - Verificar la columna **Availability**:
            - <span style="color: green;">🟢 Verde</span> → Host disponible y agente respondiendo.
            - <span style="color: red;">🔴 Rojo</span> → Host no disponible o agente no responde.
            - <span style="color: grey;">⚪ Gris</span> → Host deshabilitado o sin monitoreo.

        > **Nota:** Puede tomar unos minutos para que el estado cambie de gris a verde/rojo según la conectividad.

3. Validar que las métricas se recolecten:

    1. <span style="color: purple;"><strong>Monitoring</strong></span>→ <span style="color: violet;"><strong>Hosts</strong></span> y seleccionar <span style="color: violet;"><strong>Latest Data</strong></span>
    2. o <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Latest data</strong></span> y filtrar por el host recién creado.

4. **⚠️ Importante:** Una vez completados los pasos anteriores, <u><strong>avisar al instructor</strong></u> para que se simule un problema. Esto generará una alerta que podrán visualizar en:

    - <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Dashboards</strong></span> → **Global view** (dashboard principal).
    - <span style="color: purple;"><strong>Monitoring</strong></span> → <span style="color: violet;"><strong>Problems</strong></span> (lista de problemas activos).

---

<details>
<summary><strong>📸 Solución - Capturas de pantalla</strong></summary>

A continuación se muestran las capturas de pantalla de referencia para este ejercicio:

<div style="margin: 20px 0;">

<img src="../imagenes/4.8.%20Ejercicio%20pr%C3%A1ctico_1.png" alt="4.8. Ejercicio práctico - Captura 1" style="max-width: 100%; height: auto;">

</div>

<div style="margin: 20px 0;">

<img src="../imagenes/4.8.%20Ejercicio%20pr%C3%A1ctico_2.png" alt="4.8. Ejercicio práctico - Captura 2" style="max-width: 100%; height: auto;">

</div>

</details>
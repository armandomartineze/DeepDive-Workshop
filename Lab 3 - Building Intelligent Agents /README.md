<div align="center">
# 🤖 Lab 3 - Building Intelligent Agents

[![Oracle AI Database Private Agent Factory](https://img.shields.io/badge/DPAF%20-OCI-C74634?style=for-the-badge)](https://docs.oracle.com/en/database/oracle/agent-factory/index.html)

*Aprovisionamos desde Marketplace una factoría privada de agentes sobre Oracle Database 26ai, la integramos con nuestra Autonomous y construimos agentes Text‑to‑SQL y flujos conversacionales.*

</div>

---
## Contenido
- [3.1 Creación de la red (VCN)](#sec-3.1)
- [3.2 Despliegue desde OCI Marketplace](#32-despliegue-desde-oci-marketplace)
- [3.3 Registro inicial y configuración de modelos](#33-registro-inicial-y-configuración-de-modelos)
- [3.4 Navegación por la plataforma](#34-navegación-por-la-plataforma)
- [3.5 Lab · Data Analysis Agent (Text‑to‑SQL)](#35-lab--data-analysis-agent-text-to-sql)
- [3.6 Lab · Agent Builder — Narrador futbolístico](#36-lab--agent-builder--narrador-futbolístico)
---

### 3.1 Creación de la red (VCN)

Navega a **Networking → Virtual Cloud Networks** y confirma el compartment.

<p align="center"><img src="/images/image 8.png" alt="VCN menu"/></p>

Crea una VCN **con acceso a internet**:

<p align="center"><img src="/images/image 9.png" alt="VCN wizard"/></p>

| Campo | Valor |
|---|---|
| **Name** | `vcn-agent` |

El resto de valores por defecto → **Next → Create**.

#### 🔐 Configuración de puertos

Una vez creada la VCN, en el panel **Security** abre **Security Lists** y selecciona la lista por defecto (`Default Security List for …`).

<p align="center"><img width="800" src="/images/image 10.png" alt="Security Lists"/></p>
<p align="center"><img src="/images/image 11.png" alt="Default SL"/></p>

En **Security Rules → Add Ingress Rules** añade:

<p align="center"><img src="/images/image 12.png" alt="Rules"/></p>
<p align="center"><img src="/images/image 13.png" alt="Add rule"/></p>
<p align="center"><img width="800" src="/images/image 14.png" alt="Rule detail"/></p>

| Source CIDR | Destination Port Range | Propósito |
|---|---|---|
| `0.0.0.0/0` | `8080` | Interfaz web de DPAF |
| `0.0.0.0/0` | `1521` | Conexión a Oracle Database |

<p align="center"><img width="800" src="/images/image 15.png" alt="Rule 8080"/></p>
<p align="center"><img src="/images/image 16.png" alt="Rule 1521"/></p>

Confirma con **Add Ingress Rules**.

---

### 3.2 Despliegue desde OCI Marketplace

🔗 [Oracle AI Database Private Agent Factory · Marketplace Listing](https://marketplace.oracle.com/listings/oracle-ai-database-private-agent-factory/ocid1.mktpublisting.oc1.iad.amaaaaaaknuwtjiawz3nex7vjo2usqfv3jr5v6scz5uzvg7mef6ykxuc5zaa)

Navega a **Marketplace → All Applications** y busca la aplicación:

<p align="center"><img width="900" src="/images/image 17.png" alt="Marketplace"/></p>

```
Oracle AI Database Private Agent Factory
```

<p align="center"><img src="/images/image 18.png" alt="Buscar app"/></p>
<p align="center"><img src="/images/image 19.png" alt="Seleccionar"/></p>

Selecciona la app → **Launch Stack**. Confirma el compartment.

<p align="center"><img src="/images/image 20.png" alt="Create stack"/></p>

#### 1️⃣ Stack information

Nombre y descripción del stack (puedes dejar la descripción por defecto).

#### 2️⃣ Configure variables

**General settings**
```yaml
Region:             <tu región actual de OCI>
VM compartment:     <tu compartment>
Subnet compartment: <tu compartment>
```

> 🔎 **Importante:** no dejes `us-chicago-1` por defecto a menos que realmente estés desplegando en Chicago. Para evitar problemas de conectividad y compatibilidad, mantén en la **misma región** el stack, la VM de Agent Factory, Autonomous Database, AIDP y OCI Generative AI. En este workshop hemos validado especialmente estas regiones:
>
> - **Chicago** → `us-chicago-1`
> - **São Paulo** → `sa-saopaulo-1`
> - **London** → `uk-london-1`
> - **Frankfurt** → `eu-frankfurt-1`

**Network Configuration**
```yaml
VCN:                    vcn-agent
Existing subnet:        <subred pública>
Public or Private:      public
```

**Agent Factory VM**
```yaml
Agent Factory server display name: AgentFactoryVM
Agent Factory server shape:        VM.Standard.E5.Flex
```

<p align="center"><img width="800" src="/images/shape_vm_agent_factory.jpg" alt="Shape"/></p>

**Public SSH key**

Se requiere una llave pública SSH.

Si no deseas generarla durante el workshop, puedes usar esta clave temporal incluida en el repositorio:

- <a href="./oraclelabs.pub" download="oraclelabs.pub">Descargar `oraclelabs.pub`</a>

> ⚠️ Esta clave se incluye únicamente para fines de laboratorio. No se recomienda reutilizarla en entornos productivos ni en recursos que deban permanecer activos después del workshop.

Si prefieres crear tu propia llave, puedes generarla con PowerShell:
> ```powershell
> ssh-keygen -t rsa -b 4096 -f .\oraclelabs
> ```
> Luego carga la llave **pública** (`.pub`). Windows puede confundir la extensión con Microsoft Publisher.

<p align="center"><img src="/images/image 39.png" alt="SSH key"/></p>

#### 3️⃣ Review

Revisa la configuración y lanza el stack. El proceso toma **3–4 minutos**. Cuando finaliza, el último log muestra un **link de acceso** a DPAF.

<p align="center"><img src="/images/instalacion_AF.jpg" alt="Instalación DPAF"/></p>
---

### 3.3 Registro inicial y configuración de modelos

Abre el link entregado por el stack. Verás la página de **registro inicial**:

<p align="center"><img src="/images/image 25.png" alt="Registro"/></p>

Registra tu cuenta y continúa a la **conexión con la base de datos**, cargando la Wallet que descargaste en el paso **1.2**.

<p align="center"><img src="/images/image 26.png" alt="Wallet"/></p>

| Campo | Valor |
|---|---|
| **Air‑gapped environment** | `No` |
| **Does the database server use a wallet?** | `Yes` |
| **Are the OCI certificates added to the wallet?** | `Yes` |

Prueba la conexión; un mensaje de éxito confirma la comunicación con la base.

<p align="center"><img width="900" src="/images/image 27.png" alt="Conn OK"/></p>

Al presionar **Next** verás los logs de instalación. En el paso siguiente configuraremos los modelos.

<details>
<summary><b>🔐 Anexo · Creación de API Keys y credenciales OCI</b></summary>

<br>

Este anexo explica cómo **crear y descargar un API Key** en Oracle Cloud, y cómo obtener las variables necesarias para establecer conexión con los servicios de OCI desde aplicaciones externas (SDK, Python, scripts, DPAF, etc.).

> ⚠️ **Importante:** no basta con descargar la llave. En la pantalla de configuración debes **copiar el bloque al archivo `~/.oci/config`** y presionar el botón **Add**. Si no presionas **Add**, la llave descargada queda **no válida** o no asociada correctamente al usuario.

#### 📋 Requisitos

- Acceso activo a tu cuenta de **Oracle Cloud Infrastructure**.
- Permisos en tu usuario para administrar **Tokens and Keys**.

---

#### 1 · Acceder al perfil del usuario

En la consola de OCI, arriba a la derecha, haz clic en el **icono de usuario** y selecciona tu cuenta.

<p align="center"><img src="/images/54hy45hy.jpg" alt="Menú de usuario"/></p>

---

#### 2 · Abrir "Tokens and Keys"

Dentro del panel de tu usuario, entra a la pestaña **Tokens and Keys**.

<p align="center"><img src="/images/hr5thg.jpg" alt="Tokens and Keys"/></p>

---

#### 3 · Crear y descargar el API Key

Ubica la sección **API Keys** → clic en **Add API Key**.

<p align="center"><img src="/images/ewfwefwe.jpg" alt="Add API Key"/></p>

Selecciona **Generate API Key Pair** y luego **Download private key**.

<p align="center"><img src="/images/trewhgertgh.jpg" alt="Download private key"/></p>

> ✅ **Resultado esperado:** tendrás un archivo `.pem` descargado (normalmente `oci_api_key.pem`).

---

#### 4 · Copiar la configuración y presionar **Add** (paso crítico)

Al terminar la descarga, OCI muestra un bloque de **configuración sugerida** con los campos necesarios. Cópialo a tu archivo `~/.oci/config`:

```ini
[DEFAULT]
user=ocid1.user.oc1..aaaaaaaa...
fingerprint=12:34:56:78:90:ab:cd:ef:...
tenancy=ocid1.tenancy.oc1..aaaaaaaa...
region=<tu-region>
key_file=/RUTA/A/.oci/oci_api_key.pem
```

Luego presiona **Add** en la consola. Si no lo haces, la llave queda huérfana.

---

#### 5 · Obtener el Compartment ID

Algunas integraciones (incluido DPAF) requieren el **OCID del compartment** donde corren los servicios.

| Paso | Acción |
|---|---|
| 1 | Menú lateral → **Identity & Security → Compartments** |
| 2 | Busca y selecciona el compartment (por ejemplo `ora26ai`) |
| 3 | Copia el valor de **OCID** desde los detalles |

---
Para nuestro caso, al ser cuentas Trial, el compartment ID (__OCID del compartment__) es el mismo del Tenant (__OCID del Tenant__) ya que estamos trabajando sobre el root. 

#### 6 · Variables finales que necesitarás

Al terminar este proceso deberías tener a mano:

| Variable | Dónde se obtiene |
|---|---|
| `user` | OCID de tu usuario · Identity → My profile |
| `fingerprint` | Se muestra al crear el API Key |
| `tenancy` | OCID de tenancy · Administration → Tenancy details |
| `region` | Región donde estás ejecutando el workshop (por ejemplo `us-chicago-1`, `sa-saopaulo-1`, `uk-london-1` o `eu-frankfurt-1`) |
| `key_file` | Ruta local al `.pem` descargado |
| `compartment_id` | OCID del compartment (paso 5) |

Con estas seis variables puedes autenticar llamadas al SDK de OCI, configurar modelos en DPAF, o conectar desde scripts Python.

</details>

#### Configuración del modelo de lenguaje (LLM)

<p align="center"><img width="900" src="/images/image 28.png" alt="LLM config"/></p>

Esta configuración **depende de la región**. Antes de completar el formulario, identifica primero el código de región de tu tenancy y usa ese mismo valor en el endpoint del servicio.

El endpoint base sigue este patrón:

```text
https://inference.generativeai.<tu-region>.oci.oraclecloud.com
```

Para que el laboratorio sea reproducible sin importar si estás en Chicago, São Paulo, London o Frankfurt, recomendamos usar una combinación de modelos que se mantiene disponible en esas cuatro regiones:

| Región OCI | Código de región | Endpoint | LLM recomendado para este workshop | Embeddings recomendados |
|---|---|---|---|---|
| Chicago | `us-chicago-1` | `https://inference.generativeai.us-chicago-1.oci.oraclecloud.com` | `cohere.command-r-08-2024` | `cohere.embed-multilingual-v3.0` |
| São Paulo | `sa-saopaulo-1` | `https://inference.generativeai.sa-saopaulo-1.oci.oraclecloud.com` | `cohere.command-r-08-2024` | `cohere.embed-multilingual-v3.0` |
| London | `uk-london-1` | `https://inference.generativeai.uk-london-1.oci.oraclecloud.com` | `cohere.command-r-08-2024` | `cohere.embed-multilingual-v3.0` |
| Frankfurt | `eu-frankfurt-1` | `https://inference.generativeai.eu-frankfurt-1.oci.oraclecloud.com` | `cohere.command-r-08-2024` | `cohere.embed-multilingual-v3.0` |

```yaml
Model id:       cohere.command-r-08-2024
Endpoint:       https://inference.generativeai.<tu-region>.oci.oraclecloud.com
Compartment ID: ocid1.compartment...     # Identity and Security → Compartments
User ID:        ocid1.user.oc1...        # Identity → My profile
```

> 🔎 Puedes usar cualquier modelo disponible en el [OCI Generative AI Playground — Chat](https://cloud.oracle.com/ai-service/generative-ai/playground/chat) de la consola o en la documentación oficial [Generative AI Models by Region](https://docs.oracle.com/en-us/iaas/Content/generative-ai/model-endpoint-regions.htm). Si tu región ofrece otros modelos y prefieres usarlos, recuerda cambiar **ambas cosas**: el `Model id` y el `Endpoint`.

<p align="center"><img src="./images/image 29.png" alt="LLM form"/></p>

#### Configuración del modelo de Embeddings

Al hacer scroll encontrarás la opción para agregar un modelo de embeddings.

<p align="center"><img src="/images/image 30.png" alt="Embeddings"/></p>

Selecciona **OCI Gen AI** y completa:

```yaml
Model id:       cohere.embed-multilingual-v3.0
Endpoint:       https://inference.generativeai.<tu-region>.oci.oraclecloud.com
Compartment ID: ocid1.compartment...
User ID:        ocid1.user.oc1...
```

> 💡 Para este workshop recomendamos `cohere.embed-multilingual-v3.0` porque evita fricciones entre regiones. Si en tu región también tienes disponible `cohere.embed-multilingual-image-v3.0` y deseas usar capacidades multimodales, puedes reemplazarlo, pero verifica primero que esté habilitado en tu tenancy y en esa región específica.

> 🔎 Lista de modelos disponibles: [OCI Generative AI Playground — Embed](https://cloud.oracle.com/ai-service/generative-ai/playground/embed) de la consola o en la documentación oficial [Generative AI Models by Region](https://docs.oracle.com/en-us/iaas/Content/generative-ai/model-endpoint-regions.htm).

Si las conexiones son exitosas, continúa con la instalación.

<p align="center"><img src="/images/image 31.png" alt="Ready"/></p>
<p align="center"><img width="900" src="/images/image 32.png" alt="Installing"/></p>

#### Reinicio obligatorio de la VM de Agent Factory (Workshop)

En este workshop, este paso es obligatorio.

Después de completar exitosamente la instalación del stack y la configuración inicial, reinicia la VM de Agent Factory antes de continuar con el uso de la plataforma.

Motivo operativo en taller:
- Reduce errores intermitentes de sesión/login.
- Evita problemas donde componentes UI no reflejan cambios (por ejemplo, Data Source o catálogos que no aparecen de inmediato).
- Estabiliza el arranque de servicios para los laboratorios siguientes.

Procedimiento:
1. Ve a **OCI Console** -> **Compute** -> **Instances**.
2. Selecciona la instancia desplegada por el stack (por ejemplo `AgentFactoryVM`).
3. Haz clic en **Reboot** (reinicio normal, no reinicio forzado).
4. Espera a que la instancia vuelva a estado **Running**.

Validación después del reinicio:
1. Verifica que puedas ingresar de nuevo a:
- `https://<IP_PUBLICA>:8080/agentFactory/#/login`
2. Verifica que la plataforma cargue correctamente el home.
3. Continúa con la navegación de la plataforma y los laboratorios.
---

### 3.4 Navegación por la plataforma

Al finalizar, accederás a la **home de DPAF**:

<p align="center"><img width="900" src="/images/dpaf home.png" alt="DPAF home"/></p>

Ya puedes construir tus propios flujos y agentes de IA.

---

### 3.5 Lab · Data Analysis Agent (Text‑to‑SQL)

> ⚽ **Caso de uso:** construirás un agente de análisis sobre estadísticas de la **Copa Mundial de Fútbol 2022**. El agente entenderá preguntas en lenguaje natural, las traducirá a SQL y devolverá respuestas, tablas y visualizaciones — sin escribir una sola línea de código.

#### Paso 1 · Crear el Data Source

En el panel izquierdo selecciona **Data Source** y crea uno de tipo **Database**:

| Campo | Valor |
|---|---|
| **Name** | *Nombre descriptivo de la conexión* |
| **Description** | *Propósito de la fuente* |
| **Connection type** | *Carga la Wallet descargada en 1.2* |
| **Username** | `ADMIN` |
| **Password** | *la contraseña de tu Autonomous* |

<p align="center"><img src="/images/dpaf_image12.png" alt="Data source"/></p>

Haz clic en **Test Connection** y luego **Add Database Source**.

> ✅ Al volver al panel **Data Source** verás tu nueva fuente listada.

#### Paso 2 · Crear el Data Analysis Agent

En el menú izquierdo → **Data Analysis Agents → Create Agent**.

<p align="center"><img src="/images/dpaf_image13.png" alt="Agents menu"/></p>
<p align="center"><img src="/images/dpaf_image14.png" alt="Create agent"/></p>

**2.1 Selección de la base de datos** — elige la fuente configurada.

**2.2 Selección de tablas** — usa la barra de búsqueda para encontrar las tablas (el nombre de cada tabla corresponde al archivo CSV cargado, sin la extensión `.csv`).

<p align="center"><img src="/images/dpaf_image15.png" alt="Tables 1"/></p>
<p align="center"><img src="/images/dpaf_image16.png" alt="Tables 2"/></p>

> **Tabla a seleccionar:** `BRONZE_WC_MATCHES`.
>
> En este workshop, la tabla utilizada por el agente es `BRONZE_WC_MATCHES`. No se debe seleccionar `DATOS`.

Confirma con **Add New Source**.

<p align="center"><img src="/images/dpaf_image17.png" alt="Confirm"/></p>

**2.3 Revisión** — valida base y tablas → **Next**.

__Agent name__ --> Agente de análisis de datos de fútbol

__Description__ --> Este agente analiza los datos históricos de partidos de la copa mundial

<p align="center"><img src="/images/dpaf_image18.png" alt="Review 1"/></p>
<p align="center"><img src="/images/dpaf_image20.png" alt="Review 2"/></p>

**2.4 Publicación** — **Publish Agent**.

<p align="center"><img src="/images/dpaf_image19.png" alt="Publish"/></p>

#### Paso 3 · Usar el agente

Abre el agente con **Open Agent**.

<p align="center"><img width="900" src="/images/dpaf_image21.png" alt="Open agent"/></p>

Pulsa **Execute Exploration**: el agente analiza automáticamente los datos y genera visualizaciones según los tipos detectados.

<p align="center"><img width="900" src="/images/dpaf_image22.png" alt="Exploration"/></p>

Ahora hazle preguntas en **lenguaje natural**:

> 💬 *"¿Cuántos equipos participaron?"*
> 💬 *"¿Cuál fue el estadio con más goles anotados?"*
> 💬 *"Muestra la distribución de goles por fase del torneo"*

Haz clic en el botón **SQL** para auditar la consulta generada por el agente.

---

### 3.6 Lab · Agent Builder — Narrador futbolístico

Construirás un flujo visual en **Agent Builder** en dos etapas:

1. **Parte 1** — agente narrador simple (4 bloques).
2. **Parte 2** — flujo completo con Text‑to‑SQL sobre la base de datos real.

---

#### ⚽ Parte 1 · Agente narrador futbolístico

Flujo mínimo y funcional con cuatro bloques: `Chat input` → `Prompt` → `Agent` → `Chat output`.

<p align="center"><img width="900" src="/images/image 37.png" alt="Flujo Parte 1"/></p>

##### 1.1 · Crear un nuevo flujo

Menú izquierdo → **Agent Builder** → **New Flow**.

<p align="center"><img src="/images/image 35.png" alt="Agent Builder"/></p>

##### 1.2 · Bloque `Chat input`

Sección **INPUTS** → arrastra **Chat input** al lienzo. Expone la variable `Message`.

##### 1.3 · Bloque `Prompt`

Sección **INPUTS** → arrastra **Prompt**. Configura el campo **Template**:

```text
Eres un narrador deportivo experto en fútbol, apasionado y elocuente.
Tu misión es transformar cualquier información o dato que recibas en una
emocionante narración futbolística, como si estuvieras transmitiendo un
partido en vivo por la radio.

No importa si el input es un resultado, una lista de números, un nombre
o cualquier otro dato: conviértelo en una narración dinámica, con emoción
y vocabulario propio del fútbol.
```

> 💡 Sin variables `{{}}`. La salida **Prompt message** se conectará al campo **Custom instructions** del `Agent`.

##### 1.4 · Bloque `Agent`

Sección **AGENTS** → arrastra **Agent** y configura:

| Campo | Valor |
|---|---|
| **Select LLM to use** | El mismo LLM conversacional que configuraste antes. Recomendado: `cohere.command-r-08-2024 (oci)` si aparece disponible en tu región |
| **Temperature** | `0.01` |
| **Agent description** | `Agent` |

Conexiones:

- `Prompt.Prompt message` → `Agent.Custom instructions`
- `Chat input.Message` → `Agent.Prompt`

> 💡 La personalidad entra como **instrucción de sistema**, mientras que el mensaje del usuario va al campo **Prompt** del agente.

##### 1.5 · Bloque `Chat output`

Sección **OUTPUTS** → arrastra **Chat output** y conecta `Agent.Message` → `Chat output.Message`.

##### 1.6 · Guardar y probar

**Save** → **Playground**. Prueba con, por ejemplo:

> `3 - 1`
> `Messi, Mbappé, Vinicius`
> `El partido duró 90 minutos y hubo 4 tarjetas amarillas`

<p align="center"><img width="900" src="/images/image 36.png" alt="Playground P1"/></p>

- <details>
  <summary><strong>Referencia visual del flujo final</strong></summary>

  <br>

  ![Flujo final del laboratorio 3.6.1](images/img%2C%20flujo%20final%203-6-1.png)
  </details>
---

#### ⚽ Parte 2 · Flujo completo con Text‑to‑SQL

Extenderemos el flujo para que reciba preguntas, genere SQL, lo ejecute contra la base de datos real y responda como narración futbolística.

<p align="center"><img width="900" src="/images/image 38.png" alt="Flujo Parte 2"/></p>

##### 2.1 · Crear el Data Source (si no ejecutaste el Lab 3.5)

Para esta parte necesitas una conexión de tipo **Database** disponible en DPAF. Si ya la creaste en el Lab **3.5**, puedes reutilizarla y continuar al paso siguiente.

En el panel izquierdo selecciona **Data Source** y crea uno de tipo **Database**:

| Campo | Valor |
|---|---|
| **Name** | *Nombre descriptivo de la conexión* |
| **Description** | *Propósito de la fuente* |
| **Connection type** | *Carga la Wallet descargada en 1.2* |
| **Username** | `ADMIN` |
| **Password** | *la contraseña de tu Autonomous* |

<p align="center"><img src="/images/dpaf_image12.png" alt="Data source"/></p>

Haz clic en **Test Connection** y luego **Add Database Source**.

> ✅ Al volver al panel **Data Source** verás tu nueva fuente listada.

##### 2.2 · Continuar editando el flujo

Seguimos trabajando sobre el flujo de la Parte 1.

##### 2.3 · Primer `Prompt` — generador de SQL

Añade un bloque **Prompt** con este **Template**:

```text
Eres un agente que genera consultas SQL para responder a la siguiente pregunta:

{{question}}

Tienes una tabla de datos de partidos de fútbol con la siguiente estructura.

CREATE TABLE "ADMIN"."BRONZE_WC_MATCHES"
 ( "HOME_TEAM_NAME"   VARCHAR2(64),
   "AWAY_TEAM_NAME"   VARCHAR2(64),
   "HOME_TEAM_ID"     NUMBER,
   "AWAY_TEAM_ID"     NUMBER,
   "HOME_TEAM_GOALS"  NUMBER,
   "AWAY_TEAM_GOALS"  NUMBER,
   "DATE_RW"          TIMESTAMP(6) WITH TIME ZONE,
   "REFEREE"          VARCHAR2(64),
   "VENUE_NAME"       VARCHAR2(64),
   "VENUE_CITY"       VARCHAR2(64)
 );

Debes generar únicamente código SQL, sin comentarios (ni `--` ni `/** */`).
Cualquier texto adicional constituye un error grave. No finalices el SQL con `;`.

Ejemplo:
Pregunta: ¿Cuántos partidos se jugaron en Doha?
Respuesta esperada:
SELECT COUNT(*) AS numero_de_partidos_en_doha
FROM "ADMIN"."BRONZE_WC_MATCHES"
WHERE VENUE_CITY LIKE '%Doha%'
```

Conecta `Chat input.Message` → `Prompt.question`.

##### 2.4 · Bloque `LLM`

Sección **LANGUAGE MODEL** → añade **LLM**.

| Campo | Valor |
|---|---|
| **Select LLM to use** | El mismo LLM conversacional que configuraste antes. Recomendado: `cohere.command-r-08-2024 (oci)` si aparece disponible en tu región |
| **Temperature** | `0.01` |

Conecta `Prompt(SQL generator).Prompt message` → `LLM.Prompt`.

> 💡 Una temperatura muy baja fuerza respuestas deterministas — ideal para SQL.

##### 2.5 · Bloque `SQL query`

Sección **DATA** → añade **SQL query**.

| Campo | Valor |
|---|---|
| **Select database** | El `Data Source` creado en el paso 2.1 |
| **Include columns** | ✅ |
| **Query** | *conectado desde `LLM.Message`* |

<p align="center"><img width="900" src="/images/image 40.png" alt="SQL query"/></p>

##### 2.6 · Segundo `Prompt` — narrador con datos reales

Añade un segundo bloque **Prompt** que combine pregunta + SQL + datos:

```text
Eres un asistente experto en fútbol, con personalidad cercana y entusiasta.
Tu rol es transformar datos crudos en respuestas claras, narrativas y fáciles
de entender, como si le explicaras a un amigo apasionado del fútbol.

El sistema ha ejecutado la consulta:
{{sql}}

Los datos disponibles para responder son:
{{datos}}

Instrucciones:
- Si la pregunta no está relacionada con fútbol, responde amablemente que solo
  puedes ayudar con preguntas sobre fútbol y no continúes procesando la solicitud.
- Responde ÚNICAMENTE con la información contenida en {{datos}} — no uses
  conocimiento externo ni completes con datos que no estén en el resultado.
- Si {{datos}} no contiene suficiente información, dilo claramente.
- Responde en lenguaje natural y conversacional, no listes los datos crudos.
- Incluye siempre una tabla con los datos de {{datos}}, formateada de forma clara.
- Contextualiza el dato: si es un número, explica qué significa.
- Menciona el SQL usado: {{sql}}
- Responde en el mismo idioma en que el usuario hizo la pregunta.
```

Presiona **Save prompt** (crea automáticamente los nodos `{{...}}`) y conecta:

| Variable | Fuente |
|---|---|
| `{{question}}` | `Chat input.Message` |
| `{{sql}}` | `LLM.Message` |
| `{{datos}}` | `SQL query.JSON` |

##### 2.7 · Bloque `Agent`

Conecta `Prompt(narrador).Prompt message` → `Agent.Prompt`.

##### 2.8 · Bloque `Chat output`

Verifica que `Agent.Message` → `Chat output.Message`.

##### 2.9 · Guardar y publicar

**Save** → revisa el diagrama (debe coincidir con el esquema). **Publish** para dejarlo disponible.

<p align="center"><img width="900" src="/images/image 38.png" alt="Flujo final"/></p>

##### 2.10 · Probar el flujo completo

En el **Playground**:

> 💬 `¿Cuántos partidos se jugaron en Doha?`
> 
> 💬 `¿Qué equipo anotó más goles de local?`
> 
> 💬 `¿Cuál fue el partido con más goles en total?`

El agente consultará la base de datos y devolverá la respuesta en formato narrativo, incluyendo tabla de datos y el SQL ejecutado. 🎉

---

## 🏁 ¡Workshop completado!

Has construido, de extremo a extremo, una plataforma de datos moderna con IA generativa sobre Oracle Cloud Infrastructure:

- ✅ Infraestructura: Autonomous AI Database 26ai + AI Data Platform
- ✅ Arquitectura medallón: catálogos Bronze / Silver / Gold
- ✅ Notebooks ejecutados sobre cluster de AIDP
- ✅ Factoría privada de agentes desplegada desde Marketplace
- ✅ Agente **Text‑to‑SQL** sin escribir código
- ✅ Flujo conversacional con **Agent Builder**, integrado con la base de datos real

---

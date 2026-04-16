# Lab 1 - AI Ready Architecture



En este primer laboratorio se aporvisionaran los recursos, necesitas estar conectado a tu consola de Oracle Cloud Infrastructure.

## Contenido
[1  Creación y configuración del entorno](#sec-1)  
[1.1  Creación de la Autonomous AI Database](#sec-1.1)  
[1.2  Creación de AI Data Platform](#sec-1.2)  

---
<a id="sec-1"></a>
### Paso 1 - Creación y configuración del entorno  



En este paso vamos a inicializar los servicios utilizados, crear una Autonomous AI Database y una AI Data Platform directamente desde la consola de OCI.  

<a id="sec-1.1"></a>
#### 1.1 - Creación de la Autonomous AI Database


Haz clic en el menú de hamburguesa, en la parte superior izquierda de la pantalla, para acceder al menú de servicios disponibles en OCI. Con el menú abierto, busca Oracle AI Database y Autonomous AI Database, y abre ese servicio.

<img width="1913" height="1230" alt="image" src="/images/15e75f59-ab1a-4395-afc0-f8c75fcd5b44" />  


Verifica que estés en el compartimento correcto (puedes crearla en cualquiera) y haz clic en el botón para crear Autonomous AI Database.

<img width="1890" height="642" alt="image-2" src="/images/d0134d24-bce9-4a46-b195-7db8700381b6" />



En la pantalla de creación, agrega el nombre de tu base de datos autónoma como `DeepDiveAutonomousDatabase` en ambos campos. Elige la opción Transaction Processing para el tipo de base de datos.

<img width="1896" height="972" alt="image-4" src="/images/59a45429-a30c-40ce-8695-a4af0f58eea6" />

Desplázate un poco más abajo y selecciona la versión de base de datos `26ai` y almacenamiento de 100GB en la configuración.

<img width="1463" height="471" alt="image-5" src="/images/39f0ac5d-5c1f-46c1-8122-98a2bce13c05" />

<img width="1302" height="372" alt="image-6" src="/images/a5ba0ac6-e375-4684-9aba-53884ac32d35" />

Por último, en la sección de credenciales de la misma pantalla, crea una contraseña que puedas recordar para el usuario administrador de la base de datos `admin`. La contraseña debe:
- Tener de 12 a 30 caracteres
- Tener al menos una letra mayúscula y un número
- No puede contener comillas dobles ni simples, ni el nombre del usuario

Cuando la contraseña sea correcta, haz clic en el botón inferior derecho para confirmar la información y continuar con la creación de la base de datos.

<img width="1912" height="990" alt="image-7" src="/images/9c9867c2-aa13-47fe-ae44-3a8a37511a40" />

El resto de la configuración debe dejarse con los valores predeterminados. Tu base de datos pasará al estado de aprovisionamiento y se abrirá automáticamente la siguiente pantalla.

<img width="1878" height="392" alt="image-8" src="/images/0f304048-0c35-408a-adf0-823242352de9" />

Espera hasta que el aprovisionamiento finalice y la base de datos quede activa.

<img width="1883" height="431" alt="image-9" src="/images/0df54478-57a7-4591-bc1c-fc7a4896d838" />

<a id="sec-1.2"></a>
#### 1.2 Creación de AI Data Platform

Al igual que con la base de datos, vamos a iniciar la creación de la plataforma de datos. En ella podremos crear un catálogo, administrar volúmenes, manipular datos e incluso crear aplicaciones inteligentes. La plataforma de datos es el eje central de una buena estrategia de datos. Para iniciar la creación, entra al menú lateral y busca `Analytics & AI`; al hacer clic en esa opción, accede al servicio `AI Data Platform Workbench`.

<img width="1912" height="975" alt="image-10" src="/images/4a8a4382-9635-441b-b0f8-95ca3b210718" />

Confirma que estás en el compartimento correcto; igual que en Autonomous, este servicio puede crearse en cualquier compartimento de tu elección. Luego haz clic en el botón de creación.

<img width="1896" height="392" alt="image-12" src="/images/8d6231f0-e820-4efa-b269-2ca27855da3a" />

En la pantalla de creación vamos a completar algunas propiedades. Primero agregaremos un nombre para el AIDP y otro para el espacio de trabajo interno (workspace). Usaremos `DeepDiveAIDP` y `DeepDiveWorkspace`.

<img width="1892" height="977" alt="image-14" src="/images/db01d12a-662e-4b65-9446-9c2a7f60f388" />

Baja en la pantalla, selecciona la opción ALH para crear uno nuevo y agrega una contraseña para la plataforma. Recomendamos usar la misma del Autonomous para facilitar el proceso.

<img width="1447" height="558" alt="image-15" src="/images/0d92b281-53e8-4f68-88f6-9f353f161077" />

Para finalizar, selecciona la opción Standard en políticas de seguridad y haz clic en el botón de creación para confirmar la configuración.

<img width="1906" height="885" alt="image-16" src="/images/e11fe0a0-6f83-4f4c-b8de-1a45506f9f9f" />

En ese momento serás redirigido a la pantalla inicial del servicio, con tu AIDP en estado de creación.

<img width="1457" height="328" alt="image-17" src="/images/ece11ab6-2d0d-46e8-a58c-fc60d5402375" />


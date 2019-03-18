---

copyright:
  years: 2018,2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Cómo interactuar con la red con las API de Swagger
{: #ibp-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform ofrece varias API REST en Swagger que puede utilizar para gestionar los nodos, canales, iguales y miembros de la red. Las aplicaciones pueden utilizar estas API para controlar los recursos de red importantes sin utilizar el supervisor de red.

{:shortdesc}

Antes de empezar, debe crear una [instancia de servicio de {{site.data.keyword.blockchain}} Platform ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) en {{site.data.keyword.cloud_notm}} y crear o unir una red blockchain del Plan inicial <!--or Enterprise Plan -->.


## Recuperación de credenciales de red
{: #ibp-swagger-retrieving-network-credentials}

Entre en el supervisor de red de la red blockchain y abra la pantalla "API" desde el navegador izquierdo. Verá sus credenciales de red para la API REST. Luego autorizará a las API mediante los valores "key" y "secret" que se muestran aquí y ejecutará las API con el valor "network_id" como parámetro. Pulse **Mostrar secreto** para revelar el valor del campo de secreto. Copie los valores de los campos de clave, secreto e id de red, que podrá utilizar más tarde en la IU de Swagger.

La **Figura 1** muestra la pantalla "API":
![Pantalla API](../images/API_screen_starter.png "Pantalla API")
*Figura 1. API*

Si utiliza el Plan inicial, puede cambiar de organización en el supervisor de red. Con el plan inicial, hay dos organizaciones configuradas de forma predeterminada. Cambiar de una organización a otra puede resultar útil para probar las API REST desde la perspectiva de cada organización. Para obtener las credenciales correspondientes a otra organización de la red, pulse su nombre en la esquina superior derecha de la consola del supervisor de red. En el menú que se abre, pulse la flecha desplegable que hay junto a la organización para ver todas las organizaciones. Seleccione la organización a la que desea cambiar y cuyas credenciales de red asociadas desea ver.

**Figura 2** muestra cómo cambiar de organización:

![Cambiar de organización](../images/switch_orgs_starter.gif "Cambiar de organización")  
*Figura 2. Cambiar de organización*


## Autorización de las API de Swagger
{: #ibp-swagger-authorizing-swagger}

Pulse el enlace **IU de Swagger** de la pantalla "API" para abrir la IU de Swagger.  

En la IU de Swagger, pulse el botón **Autorizar** y aparecerá la ventana emergente de ventana de autorización. Escriba el valor de "key" y "secret" de las credenciales de red como nombre de usuario y contraseña y pulse **Autorizar** luego **Finalizado**. Ahora está preparado para ejecutar las API. Tenga en cuenta que, si se renueve el navegador, deberá volver a autorizar con sus credenciales.

Con la autenticación Basic Auth, las credenciales que especifique en la ventana Autorizar se guardan después de que pulse los botones **Autorizar** y **Finalizado** y se pasan en cada llamada de la API REST.

La **Figura 3** muestra el proceso para autorizar las API de Swagger:

![Autorizar API](../images/swaggerUIAuthorize.gif "Autorizar API")  
*Figura 3. Autorizar API*


## Prueba de las API
{: #ibp-swagger-try-out}

Pulse la API REST que desea ejecutar y pulse el botón **Pruébelo**.

La **Figura 4** muestra el botón "Pruébelo" en la "IU de Swagger":

![Botón Pruébelo en la IU de Swagger](../images/swaggerUITryItOut.png "Botón Pruébelo en la IU de Swagger")  
*Figura 4. Botón "Pruébelo" en la "IU de Swagger"*

Después de pulsar el botón **Pruébelo**, puede especificar los parámetros necesarios para utilizar la API. Puede buscar `networkID` en las credenciales de red y buscar otros parámetros en el Supervisor de red. Después de especificar los parámetros, pulse **Ejecutar** para ejecutar la llamada de API REST en la red.

La **Figura 5** muestra parámetros en la "IU de Swagger":

![Parámetros de la IU de Swagger](../images/swaggerUIParams.png "Parámetros de la IU de Swagger")  
*Figura 5. Especificar parámetros*  

Después de pulsar **Ejecutar**, puede ver la respuesta de la llamada de API en la red. También puede ver un mandato CURL que puede invocar la API directamente desde la línea de mandatos.

La **Figura 6** muestra el cuerpo de respuesta de la API, el URL y el mandato CURL:

![Respuesta de API en la IU de Swagger](../images/swaggerUICurlResponse.png "Respuesta de API en la IU de Swagger")  
*Figura 6. Respuesta de API*    

## Consejos para la resolución de problemas
{: #ibp-swagger-troubleshooting}

### 401 No autorizado  
{: #ibp-swagger-401}

  Asegúrese de que tiene autorización sobre la API REST proporcionando sus credenciales de red. Para obtener más información, consulte [Autorización de las API de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-authorizing-swagger).

### Error 400: Solicitud errónea
{: #ibp-swagger-400}

  Algunas API acepten un argumento en el cuerpo de la solicitud que actúa como filtro para mostrar solo los resultados correspondientes a un determinado igual. Se proporciona un fragmento de código de ejemplo en el cuerpo que, si se utiliza, se debe editar para especificar el igual o la lista de iguales según los que desea filtrar. Para evitar este error, edite el fragmento de código para especificar un igual en la red o elimine el fragmento por completo.

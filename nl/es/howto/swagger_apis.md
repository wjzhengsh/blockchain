---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Cómo interactuar con la red con las API de Swagger

La plataforma {{site.data.keyword.blockchainfull}} ofrece varias API REST para facilitar el desarrollo de aplicaciones. Puede probarlas en las redes blockchain mediante la interfaz de usuario de Swagger.
{:shortdesc}

Antes de empezar, debe crear una [instancia de servicio de la plataforma {{site.data.keyword.blockchain}} ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) en {{site.data.keyword.Bluemix_notm}} y crear o unir una red de blockchain del Plan inicial<!--or Enterprise Plan -->.


## Recuperación de credenciales de red

{: #retrieving-network-credentials}

Entre en el supervisor de red de la red de blockchain y abra la pantalla "API" desde el navegador izquierdo. Verá sus credenciales de red para la API REST. Luego autorizará a las API mediante los valores "key" y "secret" que se muestran aquí y ejecutará las API con el valor "network_id" como parámetro. Pulse **Mostrar secreto** para revelar el valor del campo de secreto. Copie los valores de los campos de clave, secreto e id de red, que podrá utilizar más tarde en la IU de Swagger.

<!-- Removing this code snippet so people don't try to use these values
```
},
   "x-api": {
       "url": "https://ibmblockchain.bluemix.net",
       "key": "PeerOrg1",
       "network_id": "e1f5b3341b1d483bbaf829f601144023",
       "secret": "71a329aabde9ff20de0aa4bfafd72a4466d78c87f637e7ff92c2534b5ce81cc0"
   }
```
-->

**Figura 1** muestra la pantalla "API":

![Pantalla de API](../images/API_screen_starter.png "Pantalla de API")
*Figura 1. API*

Si utiliza el Plan inicial, puede cambiar de organización en el supervisor de red. Con el plan inicial, hay dos organizaciones configuradas de forma predeterminada. Cambiar de una organización a otra puede resultar útil para probar las API REST desde la perspectiva de cada organización. Para obtener las credenciales correspondientes a otra organización de la red, pulse su nombre en la esquina superior derecha de la consola del supervisor de red. En el menú que se abre, pulse la flecha desplegable que hay junto a la organización para ver todas las organizaciones. Seleccione la organización a la que desea cambiar y cuyas credenciales de red asociadas desea ver.

**Figura 2** muestra cómo cambiar de organización:

![Cambiar de organización](../images/switch_orgs_starter.gif "Cambiar de organización")  
*Figura 2. Cambiar de organización*


## Autorización de las API de Swagger

Pulse el enlace **IU de Swagger** de la pantalla "API" para abrir la IU de Swagger.  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

En la IU de Swagger, pulse el botón **Autorizar** y aparecerá la ventana emergente de ventana de autorización. Escriba el valor de "key" y "secret" de las credenciales de red como nombre de usuario y contraseña y pulse **Autorizar** luego **Finalizado**. Ahora está preparado para ejecutar las API. Tenga en cuenta que, si se renueve el navegador, deberá volver a autorizar con sus credenciales.

Con la autenticación Basic Auth, las credenciales que especifique en la ventana Autorizar se guardan después de que pulse los botones **Autorizar** y **Finalizado** y se pasan en cada llamada de la API REST.

**Figura 3** muestra el proceso para autorizar las API de Swagger:

![Autorizar API](../images/swaggerUIAuthorize.gif "Autorizar API")  
*Figura 3. Autorizar API*


## Prueba de las API

Pulse la API REST que desea ejecutar y pulse el botón **Pruébelo**. Especifique los parámetros necesarios y pulse **Ejecutar**. La llamada de API REST se ejecuta sobre la red.

**Figura 4** muestra la "IU de Swagger":

![IU de Swagger](../images/swaggerUITryItOut.png "IU de Swagger")  
*Figura 4. IU de Swagger*


## Consejos para la resolución de problemas

### 401 No autorizado  
  Asegúrese de que tiene autorización sobre la API REST proporcionando sus credenciales de red. Para obtener más información, consulte [Autorización de las API de Swagger](#authorizing-swagger-apis).

### Error 400: Solicitud errónea
  Algunas API acepten un argumento en el cuerpo de la solicitud que actúa como filtro para mostrar solo los resultados correspondientes a un determinado igual. Se proporciona un fragmento de código de ejemplo en el cuerpo que, si se utiliza, se debe editar para especificar el igual o la lista de iguales según los que desea filtrar. Para evitar este error, edite el fragmento de código para especificar un igual en la red o elimine el fragmento por completo.

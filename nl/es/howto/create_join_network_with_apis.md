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

# Crear o unirse a una red con API de Swagger

La plataforma {{site.data.keyword.blockchainfull}} ofrece varias API REST que puede utilizar para crear o unirse a una red blockchain en {{site.data.keyword.cloud_notm}}. Puede probar estas API utilizando la [IU de Swagger](swagger_apis.html) asociada con la red.
{:shortdesc}


## Recuperación de la credencial de autenticación básica para la API
{: #retrieve-id-token}

Antes de empezar, debe crear una [instancia de servicio de la plataforma {{site.data.keyword.blockchainfull_notm}} ![icono de enlace externo](../images/external_link.svg "icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) con el Plan inicial o el Plan empresarial en {{site.data.keyword.cloud_notm}}.

Para utilizar API de Swagger para crear o unirse a una red, necesita una credencial de autenticación básica para asegurarse de que tiene acceso a la instancia de servicio en {{site.data.keyword.cloud_notm}}.

1. En el panel de control de [{{site.data.keyword.cloud_notm}} ![icono de enlace externo](../images/external_link.svg "icono de enlace externo")](https://console.bluemix.net/dashboard/apps/), abra la instancia de servicio que ha creado.
2. Pulse **Credenciales de servicio** en el navegador izquierdo.
3. Pulse el botón "Nueva credencial" en la página **Credenciales de servicio** para crear una credencial nueva. Dé un nombre a la credencial, por ejemplo, *CreateJoin*, y pulse el botón **Añadir**. No tiene que especificar nada en el campo "Añadir parámetro de configuración en línea".
    ![Recuperar credenciales de servicio](../images/service_credentials.gif "Recuperar credenciales de servicio")
4. Una vez que se cree la nueva credencial, pulse **Ver credenciales** bajo la cabecera **ACTIONS** de esta credencial. El contenido de la credencial es similar al ejemplo siguiente:

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```
    {:codeblock}

    `service_instance_id` sirve como el ID de usuario de autenticación básica y `service_instance_token` sirve como la contraseña de autenticación básica. Utilice estos valores como la credencial de autenticación básica al invocar a las API **Crear red** o **Unirse a la red**.

    Si crea una red de blockchain antes de seguir los pasos anteriores para crear una credencial de servicio, el contenido de la credencial también incluye la información de red y será similar al ejemplo siguiente:

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```
    {:codeblock}

    **Nota**: Para la API **Invitar**, la `clave` sirve como ID de usuario de autenticación básico y el `secreto` sirve como la contraseña de autenticación básica.


## Comprobando ubicaciones de red disponibles
{: #check-location}

Puede utilizar API para crear redes de blockchain solo en ubicaciones de red disponibles. Antes de crear una red, utilice la API siguiente para comprobar las ubicaciones de red disponibles.

```
/network-locations/available
```
{:codeblock}


## Creación de una red

**Nota**: Si utiliza el Plan inicial, no necesita crear una red con API porque se proporciona una red predeterminada al crear la instancia de servicio de blockchain en {{site.data.keyword.cloud_notm}}.

Si utiliza el Plan empresarial, debe realizar dos pasos para crear una red con API.

1. Cree una instancia de servicio de blockchain en {{site.data.keyword.cloud_notm}} con el Plan empresarial<!-- or Enterprise Plus Plan-->.  Recupere el ID de instancia de servicio y el token como nombre de usuario y contraseña de autenticación básica. Para obtener más información, consulte [Recuperación de credenciales de autenticación básica para API](#retrieve-id-token).

2. Invoque la API **Crear red** utilizando estas credenciales de servicio.

```
/networks
```
{:codeblock}

**Parámetros**:
- `id_ubicación`: El ID de una ubicación de red disponible. Para obtener más información, consulte [Comprobación de ubicaciones de red disponibles](check-location).
- `nombre_empresa`: El identificador como miembro en la red.
- `correo electrónico`: Su dirección de correo electrónico para recibir notificaciones.
- `iguales`: Número de iguales que desea crear para este miembro. Los valores válidos son del 0 al 6. También puede crear iguales para el miembro más adelante en la IU del Supervisor de red.
- `tipo_libromayor`: El tipo de libro mayor de esta red. Los valores válidos son levelDB y couchDB. **levelDB** es el valor predeterminado.


## Invitar a nuevos miembros a una red

Después de crear una red blockchain, puede invitar a otros miembros a unirse a su red. Debe especificar el ID de la red a la que desea invitar a unirse a los nuevos miembros. La credencial de autenticación básica necesaria para invitar a un miembro es distinta a la que se utiliza en la API **Crear red**. <!--In order to get the basic auth information you will need to follow the same steps in "Retrieving basic auth information for API". --> Puede obtener la credencial para invitar a un miembro con la API **Recuperar credenciales de red** en la [IU de Swagger ](swagger_apis##retrieving-network-credentials) o [recuperar información de autenticación básica para API](#retrieve-id-token) desde la instancia de servicio en {{site.data.keyword.cloud_notm}}.

```
/networks/{networkID}/invite
```
{:codeblock}

**Parámetros**:
- `correo electrónico`: La dirección de correo electrónico del miembro que se invitará a la red.
- `nombre_empresa`: El identificador que defina para el miembro que se unirá a la red. Los miembros invitados pueden cambiar sus identificadores al unirse a la red.

El miembro invitado recibirá una invitación por correo electrónico con instrucciones sobre cómo unirse a la red.


## Cómo unirse a una red

Si se le invita a unirse a una red de blockchain, recibirá una invitación por correo electrónico.  Puede seguir las instrucciones del correo electrónico o utilizar la API siguiente para unirse a la red.

**Nota**: Antes de unirse a una red, debe crear una instancia de servicio de la plataforma {{site.data.keyword.blockchainfull_notm}} y recuperar su ID y su señal de instancia de servicio como nombre de usuario y contraseña de autenticación básica. Para obtener más información, consulte [Recuperación de información de autenticación básica para API](#retrieve-id-token). Debe especificar el ID de la red a la que desea unirse.

```
/networks/{networkID}/join
```
{:codeblock}

**Parámetros**:
- `nombre_empresa`: El identificador como miembro en la red. Esto sustituirá el nombre del invitador asignado.
- `correo electrónico`: Su dirección de correo electrónico para recibir notificaciones.  Esto debería coincidir con la dirección de correo electrónico de la notificación de invitación.
- `iguales`: Número de iguales que desea crear para este miembro. Los valores válidos van del 0 al 6. También puede crear iguales para su miembro más adelante en el Supervisor de red.


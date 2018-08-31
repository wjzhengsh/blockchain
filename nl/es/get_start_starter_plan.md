---

copyright:
  years: 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Gobierno de la red del Plan inicial
{: #getting-started-with-starter-plan}

El Plan inicial (Starter Plan) de la plataforma {{site.data.keyword.blockchainfull}} le ofrece una red blockchain preconfigurada con una simple pulsación. Proporciona <!--offers you a free trial of 30 days and -->una red autorizada con la configuración de dos [organizaciones](glossary.html#organization), un [igual](glossary.html#peer) por cada organización y un [canal](glossary.html#channel) de forma predeterminada. Una vez creada la red, puede extenderla y añadir más organizaciones e iguales a la red. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**Nota**: El Plan inicial de la plataforma {{site.data.keyword.blockchainfull}} es un entorno de desarrollo y prueba, y no es adecuado para cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](enterprise_plan.html).

El Plan inicial le permite aprender y desarrollar habilidades con la plataforma {{site.data.keyword.blockchainfull_notm}}, ejecutar aplicaciones de ejemplo, probar sus propias aplicaciones y simular un escenario de varias organizaciones.  Esta guía de aprendizaje de iniciación contiene una introducción a los requisitos previos y a los pasos que hay que seguir para crear y utilizar una red del Plan inicial.

Si es la primera vez que utiliza la plataforma {{site.data.keyword.blockchainfull_notm}} y blockchain, puede leer el [Glosario](glossary.html) para familiarizarse con los términos de esta documentación y la [documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html) para obtener más información sobre blockchain.


## Creación de una red
Puede obtener una [red](glossary.html#network) del Plan inicial con la configuración predeterminada inmediatamente después de crear una instancia de servicio de la plataforma {{site.data.keyword.blockchainfull_notm}}.

1. Localice el [servicio Blockchain ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) en el catálogo de {{site.data.keyword.cloud_notm}}.   
    **Nota**: Debe iniciar una sesión con su cuenta de pago de {{site.data.keyword.cloud_notm}}. Si no tiene una cuenta, pulse el botón **Regístrese para crear**. Después de crear una cuenta de prueba gratuita, actualícela a un tipo **Pago según uso** yendo a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}} y pulsando **Añadir tarjeta de crédito**.
2. Seleccione la región de {{site.data.keyword.cloud_notm}} para crear la red.
3. Seleccione su organización y espacio de Cloud Foundry para crear la red.
4. Seleccione **Plan de suscripción inicial** en la tabla de planes de precios.
5. Pulse el botón **Crear**. Tenga en cuenta que, si está invitado a unirse a una red, puede ver un panel emergente de bienvenida. Para crear una red, elija **Seguir con su red** y pulse **Continuar**. Para unirse a una red, consulte el paso 5 del apartado [Cómo unirse a una red](#joining-a-network).
  Ahora está listo para utilizar su red del Plan inicial con la configuración predeterminada. La red se ejecuta con un clasificador (conocido como servicio de ordenación "SOLO"), dos organizaciones, una entidad emisora de certificados (CA) y un igual por cada organización. También se crea un canal predeterminado.
6. Pulse el botón **Iniciar**.

Encontrará su instancia del servicio blockchain en el [ panel de control del servicio {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.Bluemix_notm}}").


## Invitación a miembros
Puede invitar a otros [organizaciones](glossary.html#organization) a que se unan a su red del Plan inicial como [miembros](glossary.html#member) para poder realizar [transacciones](glossary.html#transaction) entre sí. Además, si desea utilizar el Plan inicial para aprender y realizar pruebas, puede simular una red de varias organizaciones añadiendo miembros a su red.

1. En la pantalla "Miembros" del supervisor de red, pulse el botón **Invitar miembros**.
2. Se abre la ventana "Invitar miembro".
    - Si desea invitar a otra organización, elija "Invitar a un miembro".  Especifique el nombre y la dirección de correo electrónico del operador de la organización que desea invitar.  También puede especificar información adicional que desee incluir en la invitación en el campo "Añadir una nota".  Pulse el botón **Enviar invitación**.  La organización invitada recibirá una invitación por correo electrónico y podrá seguir las instrucciones del correo para unirse a su red.
    - Si desea añadir organizaciones adicionales que se puedan añadir a un canal, elija "Añadir un miembro".  Especifique un nombre para la nueva organización. También puede añadir iguales a la nueva organización o puede hacerlo más adelante en el supervisor de red.  Pulse el botón **Crear**. Tenga en cuenta que si añade iguales a su nueva organización, debe cambiar a esta nueva organización para ver sus iguales. Para obtener más información sobre cómo cambiar de organización, consulte [Cambiar de organización](dashboard.html#switch-organizations).


## Cómo unirse a una red
Si está invitado por un iniciador de red, recibirá una notificación por correo electrónico con instrucciones sobre cómo unirse a la red. Siga las instrucciones del correo electrónico y se convertirá en uno de los miembros de la red.

Debe crear una instancia de servicio de la plataforma [{{site.data.keyword.blockchain}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) con la opción de suscripción al Plan inicial en {{site.data.keyword.cloud_notm}}.

1. Inicie una sesión con su cuenta de {{site.data.keyword.cloud_notm}}. Si no tiene una cuenta, pulse el botón **Regístrese para crear**.
2. Seleccione la organización de Cloud Foundry en la que desea almacenar su red de {{site.data.keyword.blockchain}}.
3. Seleccione **Plan de suscripción inicial** en la tabla de planes de precios.
4. Pulse el botón **Crear**. Se abre la página de la instancia del servicio con un panel de bienvenida emergente. Observe que puede elegir entre unirse a una red o continuar para crear la suya propia. Para crear una red, consulte el paso 4 del apartado [Creación de una red](#creating-a-network).
5. En el panel de bienvenida, seleccione **Unirse a red existente**, seleccione la red a la que desea unirse en la lista desplegable y pulse **Continuar**.

Encontrará la instancia del servicio blockchain en el [panel de control del servicio {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.cloud_notm}}").

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
-->


## Recuperación de credenciales de red y del perfil de conexión
Después de crear una red del Plan inicial en {{site.data.keyword.cloud_notm}}, puede recuperar las credenciales de red y el perfil de conexión desde la página de la instancia del servicio o en el supervisor de red.

### Recuperación desde la página de la instancia del servicio
Se encontrará en la página de la instancia del servicio justo después de crear una instancia del servicio. También puede pulsar el servicio en el [panel de control del servicio {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.cloud_notm}}") para abrir la página de su instancia del servicio.

Para recuperar sus credenciales de servicio, siga los pasos siguientes:
1. En la página de la instancia del servicio, pulse **Credenciales de servicio** en el navegador izquierdo para ver la pantalla "Credenciales de servicio".
2. Pulse **Nueva credencial** en la pantalla "Credenciales de servicio".
3. En la pantalla "Añadir nueva credencial", asigne un nombre a la credencial y especifique **{"type": "service_instance_token"}** en el campo "Añadir parámetro de configuración en línea". Pulse **Añadir**. La nueva credencial se añade a la tabla. Puede pulsar **Ver credenciales** en la columna "Acciones" para ver los detalles de la credencial. Esta credencial contiene la clave y el secreto de API, que puede utilizar para autorizar las API.  

![Recuperación de credenciales de red](images/service_credentials.gif "Recuperación de credenciales de red")

### Recuperación en el supervisor de red
Encontrará las credenciales de red en la pantalla "API" del supervisor de red. Para obtener más información sobre cómo utilizar las API, consulte [Cómo interactuar con la red con las API de Swagger](howto/swagger_apis.html).

Puede recuperar el perfil de conexión en la pantalla "Visión general" del supervisor de red. Pulse el botón **Perfil de conexión** en la pantalla "Visión general" y aparecerá el perfil de conexión en una página nueva.


## Despliegue de aplicaciones de ejemplo
El Plan inicial le permite desplegar aplicaciones de ejemplo en la red con solo unas pulsaciones. Con los ejemplos, puede aprender fácilmente cómo funcionan el código de encadenamiento y las aplicaciones en una red.

Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).


## Desarrollo y despliegue de redes empresariales personalizadas
El Plan inicial integra el entorno para el desarrollador IBM Blockchain Platform: Develop y el conjunto de herramientas para el desarrollador Hyperledger Composer. Puede desarrollar su red blockchain en función de los requisitos de su empresa.  Después de desarrollar una red para su empresa, puede desplegar su red empresarial en la red del Plan inicial.

Para obtener más información, consulte [Desarrollar la red](develop.html) y [Despliegue de una red empresarial con el Plan inicial](develop_starter.html).


## Supervisión de recursos de red
Si la aplicación solicita una transacción, podrá ver información de estado de la transacción en el Supervisor de red. Para obtener más información sobre la supervisión de red, consulte [Supervisión de una red](howto/monitor_network.html).


## Restablecimiento de una red
Si desea borrar las configuraciones personalizadas, el código de encadenamiento en ejecución o las aplicaciones desplegadas, puede restablecer la red a la configuración predeterminada inicial.  Para obtener más información, consulte [Restablecer la red](dashboard.html#reset-network).


## Migración de Plan inicial al Plan empresarial
{: #migrate}

Puede desplegar un `.bna`, código de encadenamiento y aplicaciones que se han probado en una red del Plan inicial en una red del Plan empresarial.

Si tiene un archivo de archivado de red empresarial (`.bna`), siga las instrucciones para [desplegar una red empresarial en el Plan empresarial](./develop_enterprise.html). Si no dispone de un archivo `.bna`, utilice el mandato `composer network download` para recuperarlo de la instancia del Plan inicial. Para obtener más información sobre el mandato `composer network download`, consulte la [documentación de línea de mandatos de Hyperledger Composer ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}.

El código de encadenamiento, que es similar a los archivos `.bna`, se desarrolla externamente. Para desplegar un código de encadenamiento que se prueba en una red del Plan inicial en el Plan empresarial, siga las instrucciones de [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html#installchaincode).

<!--
As you can see in [Deploying sample applications](howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](howto/prebuilt_samples.html#deploy_sample_applications_manually).
-->

Si despliega cualquier aplicación de ejemplo en la red del Plan inicial y desea desplegar esta aplicación en una red del Plan empresarial, puede copiar el repositorio bifurcado de aplicaciones en un nuevo repositorio y luego seguir las instrucciones del apartado [Despliegue manual de aplicaciones de ejemplo](howto/prebuilt_samples.html#deploy_sample_applications_manually).


## Supresión o abandono de una red
Si desea suprimir o abandonar una red, puede suprimir la instancia de servicio de blockchain desde el panel de control de {{site.data.keyword.cloud_notm}}.

**Nota**: Antes de abandonar una red, asegúrese de que no es miembro de ninguno de los canales de la red. De lo contrario, recibirá errores cuando abandone la red. Una eliminación de un miembro de un canal debe completar el proceso de actualización del canal. Para obtener más información sobre el proceso de actualización de un canal, consulte [Actualización de un canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->

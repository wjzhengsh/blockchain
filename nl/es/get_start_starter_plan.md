---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: blockchain network, Starter Plan, getting started tutorial

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Iniciación al Plan inicial
{: #getting-started-with-starter-plan}

El Plan inicial (Starter Plan) de {{site.data.keyword.blockchainfull}} Platform le ofrece una red blockchain preconfigurada con una simple pulsación. Proporciona una red autorizada con la configuración de dos [organizaciones](/docs/services/blockchain/glossary.html#glossary-organization), un [igual](/docs/services/blockchain/glossary.html#glossary-peer) por cada organización y un [canal](/docs/services/blockchain/glossary.html#glossary-channel) de forma predeterminada. Una vez creada la red, puede extenderla y añadir más organizaciones e iguales a la red. Estas redes están pensadas para usuarios novatos que son nuevos en la plataforma
{{site.data.keyword.blockchainfull_notm}}. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

El Plan inicial le permite aprender y desarrollar habilidades con la plataforma {{site.data.keyword.blockchainfull_notm}}, ejecutar aplicaciones de ejemplo, probar sus propias aplicaciones y simular un escenario de varias organizaciones. Esta guía de aprendizaje de iniciación le enseñará a utilizar el Plan inicial para empezar a desarrollar y realizar transacciones en una red blockchain.

Si es nuevo en {{site.data.keyword.blockchainfull_notm}} Platform y en blockchain, puede obtener más información acerca de blockchain consultando la [visión general de los componentes fundamentales](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview) de las redes creadas sobre
[Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html#hyperledger-fabric) de código abierto. También puede consultar la
[documentación de Hyperledger Fabric
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "Introducción a Blockchain").

**Nota**: el Plan inicial de {{site.data.keyword.blockchainfull_notm}} Platform es un entorno de desarrollo y prueba, y no es adecuado para cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## Visión general
{: #getting-started-with-starter-plan-overview}

**Cree el consorcio**

El primer paso de la construcción con blockchain es formar el consorcio de organizaciones que desea que utilicen blockchain para realizar transacciones, e invitarlas a la red. Los usuarios del Plan inicial que estén experimentando con la tecnología pueden simular un consorcio de varias organizaciones creando nuevas organizaciones por sí mismos.

- [Crear una red del Plan inicial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network)
- [Invitar a las organizaciones a la red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-inviting-members)
- [Unión a una red del Plan inicial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network)

Dentro del consorcio de organizaciones que se han unido a las redes, puede realizar transacciones de manera privada con grupos de dichas organizaciones mediante la creación de canales.

- [Crear canales](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-create-channels)

**Desarrollar su red y sus aplicaciones**

Tras formar el consorcio, debe escribir el código de encadenamiento, también conocido como contratos inteligentes, que contendrá la lógica empresarial de la red y le permitirá interactuar con los datos del libro mayor de blockchain. A continuación, deberá utilizar los SDK de Fabric junto con los contratos inteligentes para enviar transacciones a la red desde su aplicación del lado del cliente.

- [Desarrollo de código de encadenamiento](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-develop-chaincode)

**Operar y controlar la red**

{{site.data.keyword.blockchainfull_notm}} Platform proporciona herramientas y API que puede utilizar para gestionar la pertenencia, el ciclo de vida y el estado de su red blockchain. Muchas de estas funciones son accesibles a través de la interfaz de usuario del supervisor de red.

- [Supervisión de recursos de red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-monitoring-resources)
- [Recuperación de credenciales de red y del perfil de conexión](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-retrieving-network-credentials)
- [Gestión de la red utilizando las API de Swagger](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-swagger)
- [Restablecimiento de una red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-resetting-network)
- [Migración de Plan inicial al Plan empresarial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-migrate)
- [Supresión o abandono de una red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network)


## Creación de una red
{: #getting-started-with-starter-plan-creating-a-network}

Puede obtener una [red](/docs/services/blockchain/glossary.html#glossary-network) del Plan inicial con la configuración predeterminada inmediatamente después de crear una instancia de servicio de la plataforma {{site.data.keyword.blockchainfull_notm}}.

1. Localice el [servicio Blockchain ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) en el catálogo de {{site.data.keyword.cloud_notm}}.
    **Nota**: Debe iniciar una sesión con su cuenta de pago de {{site.data.keyword.cloud_notm}}. Si no tiene una cuenta, pulse el botón **Regístrese para crear**. Después de crear una cuenta de prueba gratuita, actualícela a un tipo **Pago según uso** yendo a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}} y pulsando **Añadir tarjeta de crédito**.
2. Seleccione la región de {{site.data.keyword.cloud_notm}} para crear la red.
3. Seleccione su organización y espacio de Cloud Foundry para crear la red.
4. Seleccione **Plan de suscripción inicial** en la tabla de planes de precios.
5. Pulse el botón **Crear**. Tenga en cuenta que, si está invitado a unirse a una red, puede ver un panel emergente de bienvenida. Para crear una red, elija **Seguir con su red** y pulse **Continuar**. Para unirse a una red, consulte el paso 5 del apartado [Cómo unirse a una red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network).
  Ahora está listo para utilizar su red del Plan inicial con la configuración predeterminada. La red se ejecuta con un clasificador (conocido como servicio de ordenación "SOLO"), dos organizaciones, una entidad emisora de certificados (CA) y un igual por cada organización. También se crea un canal predeterminado.
6. Pulse el botón **Iniciar**.

Encontrará una instancia de servicio de blockchain en el [panel de control de servicio de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/resources "panel de control de servicio de {{site.data.keyword.cloud_notm}}").


## Invitación a miembros
{: #getting-started-with-starter-plan-inviting-members}

Puede invitar a otros [organizaciones](/docs/services/blockchain/glossary.html#glossary-organization) a que se unan a su red del Plan inicial como [miembros](/docs/services/blockchain/glossary.html#glossary-member) para poder realizar [transacciones](/docs/services/blockchain/glossary.html#glossary-transaction) entre sí. Además, si desea utilizar el Plan inicial para aprender y realizar pruebas, puede simular una red de varias organizaciones añadiendo miembros a su red.

1. En la pantalla "Miembros" del supervisor de red, pulse el botón **Invitar miembros**.
2. Se abre la ventana "Invitar miembro".
    - Si desea invitar a otra organización, elija "Invitar a un miembro".  Especifique el nombre y la dirección de correo electrónico del operador de la organización que desea invitar.  También puede especificar información adicional que desee incluir en la invitación en el campo "Añadir una nota".  Pulse el botón **Enviar invitación**.  La organización invitada recibirá una invitación por correo electrónico y podrá seguir las instrucciones del correo para unirse a su red.
    - Si desea añadir organizaciones adicionales que se puedan añadir a un canal, elija "Añadir un miembro".  Especifique un nombre para la nueva organización. También puede añadir iguales a la nueva organización o puede hacerlo más adelante en el supervisor de red.  Pulse el botón **Crear**. Tenga en cuenta que si añade iguales a su nueva organización, debe cambiar a esta nueva organización para ver sus iguales. Puede cambiar de organización pulsando la esquina superior derecha y seleccionando la organización de destino en la lista desplegable bajo la sección **CAMBIAR DE ORGANIZACIÓN**.


## Cómo unirse a una red
{: #getting-started-with-starter-plan-joining-a-network}

Si está invitado por un iniciador de red, recibirá una notificación por correo electrónico con instrucciones sobre cómo unirse a la red. Siga las instrucciones del correo electrónico y se convertirá en uno de los miembros de la red.

Debe crear una [instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) con la opción de pertenencia del Plan inicial en {{site.data.keyword.cloud_notm}}.

1. Inicie una sesión con su cuenta de {{site.data.keyword.cloud_notm}}. Si no tiene una cuenta, pulse el botón **Regístrese para crear**.
2. Seleccione la organización de Cloud Foundry en la que desea almacenar su red de {{site.data.keyword.blockchain}}.
3. Seleccione **Plan de suscripción inicial** en la tabla de planes de precios.
4. Pulse el botón **Crear**. Se abre la página de la instancia del servicio con un panel de bienvenida emergente. Observe que puede elegir entre unirse a una red o continuar para crear la suya propia. Para crear una red, consulte el paso 4 del apartado [Creación de una red](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network).
5. En el panel de bienvenida, seleccione **Unirse a red existente**, seleccione la red a la que desea unirse en la lista desplegable y pulse **Continuar**.

Encontrará una instancia de servicio de blockchain en el [panel de control de servicio de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/resources "panel de control de servicio de {{site.data.keyword.cloud_notm}}").

## Crear canales
{: #getting-started-with-starter-plan-create-channels}

Los grupos de organizaciones utilizan canales para realizar transacciones sin exponer los datos a otras organizaciones. Puede crear un
[canal](/docs/services/blockchain/glossary.html#glossary-channel) con miembros seleccionados de su red de Plan inicial, y políticas sobre quién puede actualizar el canal.

Para obtener más información, consulte [Creación de un canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel). Si otra organización le invita a un canal, también encontrará instrucciones sobre cómo aceptar la invitación y hacer que sus iguales se unan al canal.

## Desarrollo de código de encadenamiento
{: #getting-started-with-starter-plan-develop-chaincode}

El [código de encadenamiento](/docs/services/blockchain/glossary.html#glossary-chaincode), al que a veces se hace referencia también como contratos inteligentes, es software que le permite leer y actualizar datos en el libro mayor de blockchain. El código de encadenamiento puede convertir la lógica empresarial en un programa ejecutable acordado y verificado por todos los miembros de la red blockchain.

Para obtener más información, consulte la guía de aprendizaje de [desarrollo de código de encadenamiento](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts) para obtener información sobre cómo empezar a escribir código de encadenamiento, así como de las características de Fabric que son accesibles a través del código de encadenamiento.

## Instalación y creación de instancias del código de encadenamiento
{: #getting-started-with-starter-plan-install-instantiate-chaincode}
Tras unirse a canales y desarrollar la lógica empresarial, deberá instalar código de encadenamiento en los iguales de la red. Puede utilizar el supervisor de red para instalar y crear una instancia del código de encadenamiento en los iguales de su organización, así como actualizar el código de encadenamiento para facilitar el desarrollo continuo.

Para obtener más información sobre la instalación y creación de instancias de código de encadenamiento, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).


## Supervisión de recursos de red
{: #getting-started-with-starter-plan-monitoring-resources}

Si la aplicación solicita una transacción, podrá ver información de estado de la transacción en el Supervisor de red. Para obtener más información sobre la supervisión de red, consulte [Supervisión de una red](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).


## Recuperación de credenciales de red y del perfil de conexión
{: #getting-started-with-starter-plan-retrieving-network-credentials}

Después de crear una red del Plan inicial en {{site.data.keyword.cloud_notm}}, puede recuperar las credenciales de red y el perfil de conexión desde la página de la instancia del servicio o en el supervisor de red.

### Recuperación desde la página de la instancia del servicio
{: #getting-started-with-starter-plan-retrieve-service-instance}

Se encontrará en la página de la instancia del servicio justo después de crear una instancia del servicio. También puede pulsar el servicio en el [panel de control de servicio de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/resources "panel de control de servicio de {{site.data.keyword.cloud_notm}}") para abrir la página de la instancia de servicio.

Para recuperar sus credenciales de servicio, siga los pasos siguientes:
1. En la página de la instancia del servicio, pulse **Credenciales de servicio** en el navegador izquierdo para ver la pantalla "Credenciales de servicio".
2. Pulse **Nueva credencial** en la pantalla "Credenciales de servicio".
3. En la pantalla "Añadir nueva credencial", asigne un nombre a la credencial y especifique **{"type": "service_instance_token"}** en el campo "Añadir parámetro de configuración en línea". Pulse **Añadir**. La nueva credencial se añade a la tabla. Puede pulsar **Ver credenciales** en la columna "Acciones" para ver los detalles de la credencial. Esta credencial contiene la clave y el secreto de API, que puede utilizar para autorizar las API.

![Recuperación de credenciales de red](images/service_credentials.gif "Recuperación de credenciales de red")

### Recuperación en el supervisor de red
{: #getting-started-with-starter-plan-network-creds}

Encontrará las credenciales de red en la pantalla "API" del supervisor de red. Para obtener más información sobre cómo utilizar las API, consulte [Cómo interactuar con la red con las API de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

Puede recuperar el perfil de conexión en la pantalla "Visión general" del supervisor de red. Pulse el botón **Perfil de conexión** en la pantalla "Visión general" y aparecerá el perfil de conexión en una página nueva.

## Gestión de la red utilizando las API de Swagger
{: #getting-started-with-starter-plan-swagger}

{{site.data.keyword.blockchainfull_notm}} Platform ofrece varias API REST en Swagger que puede utilizar para gestionar los nodos, canales, iguales y miembros de la red. Las aplicaciones pueden utilizar estas API para controlar los recursos de red importantes sin utilizar el Supervisor de red.

Para obtener más información, consulte [Cómo interactuar con la red con las API de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger)

## Restablecimiento de una red
{: #getting-started-with-starter-plan-reset-nw}

Si desea borrar las configuraciones personalizadas, el código de encadenamiento en ejecución o las aplicaciones desplegadas, puede restablecer la red a la configuración predeterminada inicial. Para obtener más información, consulte [Restablecer la red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network).


## Migración de Plan inicial al Plan empresarial
{: #getting-started-with-starter-plan-migrate}

Puede desplegar un código de encadenamiento y aplicaciones y probarlos en una red del Plan inicial en una red del Plan empresarial. Para desplegar un código de encadenamiento que se prueba en una red del Plan inicial en el Plan empresarial, siga las instrucciones de [Instalación, creación de instancias y actualización de un código de encadenamiento](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc).

Únicamente se puede migrar el código de encadenamiento y las aplicaciones; los datos no se pueden migrar entre las redes de los planes inicial y empresarial.

## Supresión o abandono de una red
{: #getting-started-with-starter-plan-delete-network}

Si desea suprimir o abandonar una red, puede suprimir la instancia de servicio de blockchain desde el panel de control de {{site.data.keyword.cloud_notm}}.

Antes de abandonar una red, asegúrese de que no es miembro de ninguno de los canales de la red. De lo contrario, recibirá errores cuando abandone la red. Una eliminación de un miembro de un canal debe completar el proceso de actualización del canal. Para obtener más información sobre el proceso de actualización de un canal, consulte [Actualización de un canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).{:note}

---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Gobierno de la red del Plan empresarial
{: #getting-started-with-blockchain}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


El Plan empresarial de la plataforma {{site.data.keyword.blockchainfull}} proporciona una red blockchain con alta seguridad, integridad, escalabilidad y rendimiento. Puede suministrar rápidamente una red plenamente funcional y utilizar el supervisor de red, que es un panel de control de la GUI, para ejecutar con inmediatez [código de encadenamiento](glossary.html#chaincode) y aplicaciones sin tener que diseñar ni configurar una red partiendo de cero.
{:shortdesc}

**Nota**: el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}} proporciona un entorno de producción. Si necesita un entorno de desarrollo y pruebas, consulte [Acerca del Plan inicial](starter_plan.html).

En esta guía de aprendizaje encontrará una introducción a los requisitos previos y los pasos a seguir para obtener una red del Plan empresarial alojada en el entorno altamente disponible y seguro de IBM.  

En los siguientes pasos se indica el flujo básico para iniciar una red del Plan empresarial con varios [miembros](glossary.html#member) de la red:
1. Un **iniciador de red**, como tipo especial de miembro de red, crea la red y define las políticas de gobierno. El iniciador de red puede invitar a otras [organizaciones](glossary.html#organization) a unirse a esta red como miembros de la red.  Para obtener más información, consulte [Creación de una red](#creating-a-network).
2. Los **miembros de la red** invitados reciben una notificación por correo electrónico con las instrucciones que deben seguir para unirse a una red de {{site.data.keyword.blockchain}}. Además de las instrucciones de la notificación de correo electrónico, también puede seguir los pasos descritos en [Cómo unirse a una red](#joining-a-network).
3. Todos los **miembros de red**, después de crear o unirse a una red, pueden acceder al supervisor de red para configurar y gestionar sus recursos de red. Puede configurar [canales](glossary.html#channel) con un grupo de miembros de red para ejecutar transacciones privadas en un libro mayor específico de un canal, al que solo pueden acceder los miembros de ese canal. En el supervisor de red, también puede unirse a sus iguales en el canal e instalar y luego instalar y crear en los mismos una instancia de código de encadenamiento. Para ver más información, consulte [Configuración de recursos de red y entorno](#configuring-network-resources-and-environment).
4. Los **desarrolladores de aplicaciones**, después de desarrollar aplicaciones, permiten la interacción entre sus aplicaciones y la red. Para obtener más información, consulte [Habilitación de aplicaciones para que interactúen con la red](#enabling-applications-to-interact-with-the-network).
5. Los **operadores de red** supervisan las transacciones de sus canales en el supervisor de red. Para obtener más información, consulte [Supervisión de recursos de red](#monitoring-network-resources).


## Creación de una red
Antes de empezar, debe crear una instancia de servicio de la plataforma [{{site.data.keyword.blockchain}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) en {{site.data.keyword.Bluemix_notm}}. Debe iniciar una sesión con su ID de {{site.data.keyword.Bluemix_notm}}. Si no dispone de ID, pulse el botón **Regístrese para crear**.  Cambie el nombre del servicio y los nombres de las credenciales para su instancia de manera que pueda recordarlas fácilmente en el futuro. Seleccione la región, la organización y el espacio de {{site.data.keyword.Bluemix_notm}} donde desplegar la red de {{site.data.keyword.blockchain}}. A continuación, seleccione **Plan de suscripción empresarial** en la tabla de precios de planes y pulse el botón **Crear**.  

Encontrará su instancia del servicio {{site.data.keyword.blockchain}} en el [panel de control del servicio {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.Bluemix_notm}}").  

Si es un iniciador de red, pulse el botón **Crear red** para iniciar una red de {{site.data.keyword.blockchain}}.  Siga el asistente para completar la configuración básica de la red y los recursos.  
![Asistente Crear red](images/create_network_name.png "Asistente Crear red")  

1. En la pantalla "Empecemos", asigne un nombre a la red, elija la ubicación de su organización de {{site.data.keyword.Bluemix_notm}} y añada el nombre de la institución. Cuando invite a otros miembros, estos buscarán este nombre de red para unirse a ella. Pulse **Siguiente**.
2. (Opcional) En la pantalla "Invitar miembros", especifique el nombre de la institución y la dirección de correo electrónico del miembro que desea invitar a la red. El nombre de la institución que designe no es un título oficial. Simplemente permite que la institución se reconozca fácilmente y puede cambiarse cuando se unen a la red. Tenga en cuenta que una red puede tener hasta 15 miembros, incluido usted. Este paso es opcional y puede invitar a miembros a la red posteriormente en el Supervisor de red.  Pulse **Siguiente**.
	Los miembros que invite recibirán una notificación por correo electrónico sobre la invitación después de completar todos los pasos necesarios para crear la red.
3. En la pantalla "Definir reglas de gobierno", establezca las políticas de pertenencia, creación de canal y código de encadenamiento. De forma predeterminada, todos los miembros de red pueden invitar a otros miembros a unirse a la red, crear canales y crear instancias de código de encadenamiento. Actualmente, su red utiliza las políticas de gobierno predeterminadas.  Pulse **Siguiente**.
4. En la pantalla "Revise el resumen", verifique la configuración de red. Si desea realizar modificaciones, pulse **Editar** junto a la cabecera de sección o pulse el botón **Anterior** para volver a las pantallas anteriores. Cuando haya completado la configuración de red, pulse **Finalizado**.  
5. En la pantalla "Red creada", se le notificará que la red se ha creado satisfactoriamente. Puede pulsar **Añadir [iguales](glossary.html#peer)** para configurar los recursos de red o puede pulsar **Entrar en el supervisor** directamente para abrir el supervisor de red. También puede añadir iguales en el supervisor de red más adelante.  Para obtener más información sobre los iguales, consulte [Añadir iguales](v10_dashboard.html#peers).

Ahora ha desplegado correctamente una red de {{site.data.keyword.blockchain}} que puede dar soporte a los recursos de red siguientes:  
* Una entidad emisora de certificados (CA) específica de un miembro
* Políticas de gobierno predeterminadas
* Hasta 15 miembros de red  
* Tres clasificadores y dos nodos de CA intermedios
* Hasta tres iguales pequeños en cada miembro  
* Un servicio de ordenación tolerante a errores de caída
* Hasta 150 canales
* Hasta 10 instanciaciones de código de encadenamiento por miembro en el Supervisor de red


## Cómo unirse a una red
De forma parecida a la creación de una red, debe crear una instancia de servicio de la plataforma [{{site.data.keyword.blockchain}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) en {{site.data.keyword.Bluemix_notm}}. Debe iniciar una sesión con su ID de {{site.data.keyword.Bluemix_notm}}. Si no dispone de ID, pulse el botón **Regístrese para crear**.  Cambie el nombre del servicio y los nombres de las credenciales para su instancia de manera que pueda recordarlas fácilmente en el futuro. Seleccione la región, la organización y el espacio de {{site.data.keyword.Bluemix_notm}} donde desplegar la red de {{site.data.keyword.blockchain}}. A continuación, seleccione **Plan de suscripción empresarial** en la tabla de precios de planes y pulse el botón **Crear**.

Encontrará su instancia del servicio {{site.data.keyword.blockchain}} en el [panel de control del servicio {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.Bluemix_notm}}").

Si es un miembro de red invitado, pulse el botón **Invitación pendiente->**, seleccione la red desde la que desea unirse en la lista desplegable y pulse el botón **Unirse a la red** . Siga las instrucciones del asistente para ver la configuración básica de la red y configurar sus propios recursos de la red.  
![Asistente Unirse a la red](images/join_network_name.png "Asistente Unirse a la red")  

1. En la pantalla "Empecemos", escriba el nombre de su organización y pulse **Siguiente**.
2. En la pantalla "Revise las reglas de gobierno", revise las políticas de gobierno de la red referentes a pertenencia, creación de canal y código de encadenamiento. Pulse **Siguiente**.
3. (Opcional) En la pantalla "Añadir iguales", elija la cantidad de iguales que desea añadir. Pulse **Siguiente**. Cada miembro de la red puede añadir hasta tres iguales. Este paso es opcional y puede añadir iguales posteriormente en el Supervisor de red. Para obtener más información sobre los iguales, consulte [Añadir iguales](v10_dashboard.html#peers).
4. En la pantalla "Revise el resumen de la red", verifique la configuración de red. Si desea realizar modificaciones, pulse el botón **Anterior** para volver a las pantallas anteriores. Cuando haya completado la configuración de recurso, pulse **Finalizado**. Se le notificará que se ha unido correctamente a la red. Después puede pulsar **Entrar en el supervisor** para abrir el supervisor de red.

<!-- or click **Create a Channel** to initiate a channel creation request. You can create channels later in the Network Monitor. For more information, see [Channels](v10_dashboard.html#channels).  -->


## Configuración de recursos de red y de entorno

1. Especifique el Supervisor de red después de crear la red de {{site.data.keyword.blockchain}} o unirse a una. El supervisor de red es un panel de control de la GUI donde se puede gestionar la información de estado de red y realizar un seguimiento de la misma. Para obtener más información, consulte [Supervisor de red](v10_dashboard.html).
2. Añada sus iguales a la red. Si ya ha agregado suficientes iguales, sáltese este paso. Los iguales ejecutan código de encadenamiento y son el punto final para interactuar con las aplicaciones. Pulse **Añadir iguales** en la pantalla "Visión general" y seleccione la cantidad y el tamaño de los iguales. Para obtener más información, consulte [Visión general](v10_dashboard.html#overview).
3. Configure un canal. Se suministra un libro mayor específico del canal a todos los miembros de un mismo canal; este libro mayor presenta datos de aislamiento y confidencialidad. Para obtener información sobre cómo crear un canal, consulte [Creación de un canal](howto/create_channel.html#creating-a-channel).  
    Si es un miembro del canal a quien se ha invitado a unirse a un canal, recibirá una notificación por correo electrónico con un enlace al asistente que le permitirá unirse al canal.
4. Una iguales al canal.  Solo los iguales que estén asociados con el canal podrán acceder a su libro mayor. Para obtener más información, consulte [Canales](v10_dashboard.html#channels).
5. Instale y cree una instancia de código de encadenamiento. Todos los miembros del canal deben instalar el mismo código de encadenamiento con el mismo nombre y versión en cada igual que ejecutará dicho código de encadenamiento. Después de instalar el código de encadenamiento, debe crear una instancia del mismo en el canal para poder utilizarlo. Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html).  

**Nota**: para conseguir una alta disponibilidad, cada organización debe adquirir como mínimo dos iguales, y, dentro de un canal, cada miembro participante debe unir al menos a dos iguales.


## Recuperación de credenciales de red y del perfil de conexión
Después de crear una red del Plan empresarial en {{site.data.keyword.cloud_notm}}, puede recuperar las credenciales de red y el perfil de conexión desde la página de la instancia del servicio o en el supervisor de red.

### Recuperación desde la página de la instancia del servicio
Se encontrará en la página de la instancia del servicio justo después de crear una instancia del servicio. También puede pulsar el servicio en el [panel de control del servicio {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control del servicio {{site.data.keyword.cloud_notm}}") para abrir la página de su instancia del servicio.

Para recuperar sus credenciales de servicio, siga los pasos siguientes:
1. En la página de la instancia del servicio, pulse **Credenciales de servicio** en el navegador izquierdo para ver la pantalla "Credenciales de servicio".
2. Pulse **Nueva credencial** en la pantalla "Credenciales de servicio".
3. En la pantalla "Añadir nueva credencial", asigne un nombre a la credencial y especifique **{"type": "service_instance_token"}** en el campo "Añadir parámetro de configuración en línea". Pulse **Añadir** y la nueva credencial se añade a la tabla. Puede pulsar **Ver credenciales** en la columna "Acciones" para ver los detalles de la credencial. Esta credencial contiene la clave y el secreto de API, que puede utilizar para autorizar las API.

### Recuperación en el supervisor de red
Encontrará las credenciales de red en la pantalla "API" del supervisor de red. Para obtener más información sobre cómo utilizar las API, consulte [Cómo probar API con Swagger](howto/swagger_apis.html).

Puede recuperar el perfil de conexión en la pantalla "Visión general" del supervisor de red. Pulse el botón **Perfil de conexión** en la pantalla "Visión general" y aparecerá el perfil de conexión en una página nueva.


## Desarrollo y despliegue de redes empresariales personalizadas
Puede desarrollar redes empresariales en función de las necesidades de su empresa con el entorno para el desarrollador IBM {{site.data.keyword.blockchainfull_notm}} Platform: Develop y con el conjunto de herramientas para el desarrollador Hyperledger Composer. Después de desarrollar una red para su empresa, puede desplegar su red empresarial en la red del Plan empresarial.

Para obtener más información, consulte [Desarrollar la red](develop.html) y [Despliegue de una red empresarial en el Plan empresarial](develop_enterprise.html).


## Habilitación de aplicaciones para que interactúen con la red
Las aplicaciones utilizan las API de SDK para interactuar con los recursos de la red de {{site.data.keyword.blockchain}}. Debe añadir la información de punto final de API de los recursos de red en su aplicación, de modo que la aplicación pueda dirigir las solicitudes de transacción a los iguales. A continuación, puede añadir la información de punto final de API del Supervisor de red. Las aplicaciones pueden alojarse en el sistema de archivos local o en {{site.data.keyword.Bluemix_notm}}. Para obtener más información, consulte [Desarrollo de aplicaciones](v10_application.html).

## Supervisión de recursos de red  
Después de que se desencadene una transacción desde la aplicación, podrá ver información de estado de la transacción en el Supervisor de red. Para obtener más información sobre la supervisión de red, consulte [Supervisión de una red](howto/monitor_network.html).

## Cómo abandonar a una red
Si desea abandonar una red, suprima la instancia del servicio blockchain del panel de control de {{site.data.keyword.Bluemix_notm}}.  

**Nota**: Antes de abandonar una red, asegúrese de que no es miembro de ninguno de los canales de la red. De lo contrario, recibirá errores cuando abandone la red. Una eliminación de un miembro de un canal debe completar el proceso de actualización del canal. Para obtener más información sobre el proceso de actualización de un canal, consulte [Actualización de un canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/){:new_window}.
-->

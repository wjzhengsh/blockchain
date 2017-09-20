---

copyright:
  years: 2017
lastupdated: "2017-09-04"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Gobierno de red
{: #getting-started-with-blockchain}

{{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}} proporciona una red de {{site.data.keyword.blockchain}} con alta seguridad, integridad, escalabilidad y rendimiento. Puede suministrar rápidamente una red de {{site.data.keyword.blockchain}} plenamente funcional así como utilizar el Supervisor de red para ejecutar con inmediatez código de encadenamiento y aplicaciones, sin tener que diseñar y configurar una red de blockchain privada.
{:shortdesc}
 
En esta guía de iniciación, se presentan los requisitos previos y los pasos que deberá seguir para obtener una red de {{site.data.keyword.blockchain}} alojada en el entorno altamente disponible y seguro de IBM.  

Los siguientes pasos indican el flujo básico para iniciar una red de {{site.data.keyword.blockchain}} con varias organizaciones miembro:
1. Un **iniciador de red**, como tipo especial de miembro de red, crea la red de {{site.data.keyword.blockchain}} y define las políticas de gobierno. El iniciador de red puede invitar a otras instituciones a unirse a esta red de {{site.data.keyword.blockchain}} como miembros de la red. Para ver más detalles, consulte [Creación de una red](#creating-a-network).
2. Los **miembros de la red** reciben una notificación por correo electrónico con las instrucciones que deben seguir para unirse a una red de {{site.data.keyword.blockchain}}. Además de las instrucciones en la notificación de correo electrónico, también puede seguir los pasos descritos en [Cómo unirse a una red](#joining-a-network).
3. Todos los **miembros de red**, después de crear o unirse a una red, pueden acceder al Supervisor de red, que es un panel de GUI, para configurar y gestionar sus recursos de red. Puede configurar canales con un grupo de miembros de red para ejecutar transacciones privadas en un libro mayor específico de un canal, al que solo pueden acceder los miembros de ese canal. En el Supervisor de red, también puede unirse a sus iguales en el canal e instalar y crear una instancia de código de encadenamiento en ellos. Para ver más detalles, consulte [Configuración de recursos de red y entorno](#configuring-network-resources-and-environment).
4. Los **desarrolladores de aplicaciones** crean aplicaciones que habilitan la interacción con la red de {{site.data.keyword.blockchain}}. Para más información, consulte [Habilitación de aplicaciones para que interactúen con la red](#enabling-applications-to-interact-with-the-network).
5. Los **operadores de red** supervisan las transacciones de su canal con el Supervisor de red. Para más información, consulte [Supervisión de recursos de red](#monitoring-network-resources).

## Creación de una red
Antes de empezar, debe crear una instancia de servicio de [{{site.data.keyword.blockchain}} Platform ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) on {{site.data.keyword.Bluemix_notm}}. Necesitará iniciar sesión con su ID de {{site.data.keyword.Bluemix_notm}}. Si no dispone de ID, pulse el botón **Regístrese para crear**. Cambie el nombre del servicio y los nombres de las credenciales para su instancia de manera que pueda recordarlas fácilmente en el futuro. Seleccione la región, la organización y el espacio de {{site.data.keyword.Bluemix_notm}} donde desplegar la red de {{site.data.keyword.blockchain}}. A continuación, seleccione **Plan Empresarial** en la tabla de precios de planes y pulse el botón **Crear**.  

Puede encontrar la instancia de servicio de {{site.data.keyword.blockchain}} Platform en el [ panel de control de servicio de {{site.data.keyword.Bluemix_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control de servicio de Bluemix").  

Si es un iniciador de red, pulse el botón **Crear red** para iniciar una red de {{site.data.keyword.blockchain}}. Siga el asistente para completar la configuración básica de la red y los recursos.  
![Asistente Crear red](images/create_network_name.png "Asistente Crear red")  

1. En la pantalla "Empecemos", asigne un nombre a la red, elija la ubicación de su organización de {{site.data.keyword.Bluemix_notm}} y añada el nombre de la institución. Cuando invite a otros miembros, estos buscarán este nombre de red para unirse a ella. Pulse **Siguiente**. 
2. (Opcional) En la pantalla "Invitar miembros", especifique el nombre de la institución y la dirección de correo electrónico del miembro que desea invitar a la red. El nombre de la institución que designa no es un título oficial. Simplemente permite que la institución se reconozca fácilmente y puede cambiarse cuando se unen a la red. Tenga en cuenta que una red puede tener hasta 15 miembros, incluido usted. Este paso es opcional y puede invitar a miembros a la red posteriormente en el Supervisor de red. Pulse **Siguiente**. Los miembros que invite recibirán una notificación por correo electrónico sobre la invitación después de completar todos los pasos necesarios para crear la red.
3. En la pantalla "Definir reglas de gobierno", establezca las políticas de pertenencia, creación de canal y código de encadenamiento. De forma predeterminada, todos los miembros de red pueden invitar a otros miembros a unirse a la red, crear canales y crear instancias de código de encadenamiento. Para esta GA, su red utiliza las políticas de gobierno predeterminadas. Pulse **Siguiente**. 
4. En la pantalla "Revise el resumen", verifique la configuración de red. Si desea realizar modificaciones, pulse **Editar** junto a la cabecera de sección o pulse el botón **Anterior** para volver a las pantallas anteriores. Cuando haya completado la configuración de red, pulse **Finalizado**.  
5. En la pantalla "Red creada", se le notificará que la red se ha creado satisfactoriamente. Puede pulsar **Añadir iguales** para configurar los recursos de red o pulse **Entrar en el supervisor** directamente para abrir el Supervisor de red. Si no añade iguales en primer lugar, podrá agregarlos más adelante en el Supervisor de red. Para obtener más información sobre los iguales, consulte [Visión general](v10_dashboard.html#overview).
    
Ahora está listo para desplegar una red de {{site.data.keyword.blockchain}} que puede dar soporte a los recursos de red siguientes:  
* Una entidad emisora de certificados (CA) específica de un miembro
* Políticas de gobierno predeterminadas
* Hasta 15 miembros de red  
* Tres clasificadores y dos nodos de CA intermedios
* Hasta seis iguales pequeños en cada miembro  
* Un servicio de ordenación tolerante a errores de caída
* Hasta 150 canales
* Hasta 10 instanciaciones de código de encadenamiento


## Cómo unirse a una red
De forma parecida a la creación de una red, debe crear una instancia de servicio de [{{site.data.keyword.blockchain}} Platform ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain) en {{site.data.keyword.Bluemix_notm}}. Necesitará iniciar sesión con su ID de {{site.data.keyword.Bluemix_notm}}. Si no dispone de ID, pulse el botón **Regístrese para crear**. Cambie el nombre del servicio y los nombres de las credenciales para su instancia de manera que pueda recordarlas fácilmente en el futuro. Seleccione la región, la organización y el espacio de {{site.data.keyword.Bluemix_notm}} donde desplegar la red de {{site.data.keyword.blockchain}}. A continuación, seleccione **Plan Empresarial** en la tabla de precios de planes y pulse el botón **Crear**.  

Podrá encontrar la instancia de servicio de {{site.data.keyword.blockchain}} Platform en el [ panel de control de servicio de {{site.data.keyword.Bluemix_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/services "panel de control de servicio de Bluemix"). 

Si es un miembro de red invitado, pulse el botón **Invitación pendiente->**, seleccione la red a la que desea unirse en la lista desplegable y pulse el botón **Unirse a la red**. Siga el asistente para ver la configuración básica de la red y configurar los componentes propios de la red.  
![Asistente Unirse a la red](images/join_network_name.png "Asistente Unirse a la red")  

1. En la pantalla "Empecemos", añada el nombre de la institución y verifique la ubicación de su organización de {{site.data.keyword.Bluemix_notm}}. Pulse **Siguiente**. 
2. En la pantalla "Revise las reglas de gobierno", visualice las políticas de gobierno de la red referentes a pertenencia, creación de canal y código de encadenamiento. Pulse **Siguiente**. 
3. (Opcional) En la pantalla "Añadir iguales", elija el tamaño y la cantidad de iguales que desea añadir. Pulse **Siguiente**. Cada miembro de una red puede añadir hasta 3 iguales; en este momento solo hay disponibles iguales "pequeños". Este paso es opcional y puede añadir iguales posteriormente en el Supervisor de red. Para obtener más información sobre los iguales, consulte [Visión general](v10_dashboard.html#overview).
4. En la pantalla "Revise el resumen de la red", verifique la configuración de red. Si desea realizar modificaciones, pulse **Editar** junto a la cabecera de sección o pulse el botón **Anterior** para volver a las pantallas anteriores. Cuando haya completado la configuración de recurso, pulse **Finalizado**. Se le notificará que se ha unido a la red correctamente. Después puede pulsar **Entrar en el supervisor** para abrir el Supervisor de red o pulsar **Crear un canal** para iniciar una solicitud de creación de canal. Se pueden crear canales en el Supervisor de red más adelante. Para más información, consulte [Canales](v10_dashboard.html#channels).
 

## Configuración de recursos de red y de entorno

1. Especifique el Supervisor de red después de crear la red de {{site.data.keyword.blockchain}} o unirse a una. El Supervisor de red es un panel de control de la GUI donde se puede gestionar la información de estado de red y realizar un seguimiento de la misma. Para más información, consulte [Supervisor de red](v10_dashboard.html).
2. Añada sus iguales a la red. Si ya ha agregado suficientes iguales, sáltese este paso. Los iguales ejecutan código de encadenamiento y son el punto final para interactuar con las aplicaciones. Pulse **Añadir iguales** en la pantalla "Visión general" y seleccione la cantidad y el tamaño de los iguales. Para obtener más información, consulte [Visión general](v10_dashboard.html#resources). 
3. Configure un canal. Se suministra un libro mayor específico del canal a todos los miembros de un mismo canal; este libro mayor presenta datos de aislamiento y confidencialidad. Para obtener información sobre la creación de un canal, consulte [Creación de un canal](howto/create_channel.html#creating-a-channel).  
	
	Si es un miembro del canal a quien se ha invitado a unirse a un canal, recibirá una notificación por correo electrónico con un enlace al asistente que le permitirá unirse al canal. 4. Una iguales al canal. Solo los iguales que estén asociados con el canal podrán acceder a su libro mayor. Para más información, consulte [Canales](v10_dashboard.html#channels).
5. Instale y cree una instancia de código de encadenamiento. Todos los miembros del canal deben instalar el mismo código de encadenamiento con el mismo nombre y versión en cada igual que ejecutará dicho código de encadenamiento. Tras la instalación, el código de encadenamiento debe ser una instancia en el canal antes de poder utilizarlo. Para más información, consulte [Instalación y creación de una instancia de código de encadenamiento](howto/install_instantiate_chaincode.html).  

**Nota**: para conseguir una alta disponibilidad, cada institución debe comprar como mínimo 2 iguales, y dentro de un canal, cada institución participante debe unirse al menos a 2 iguales.

## Habilitación de aplicaciones para que interactúen con la red
Las aplicaciones utilizan las API de SDK para interactuar con los componentes de la red de {{site.data.keyword.blockchain}}. Debe añadir la información de punto final de API de los componentes de red en su aplicación, de modo que la aplicación pueda dirigir las solicitudes de transacción a los iguales. A continuación, puede añadir la información de punto final de API del Supervisor de red. Las aplicaciones pueden alojarse en el sistema de archivos local o en {{site.data.keyword.Bluemix_notm}}. Para obtener más información, consulte [Desarrollo de aplicaciones](v10_application.html).

## Supervisión de recursos de red  
Después de desencadenarse una transacción desde la aplicación, podrá ver información de estado de transacción en el Supervisor de red. Para obtener más información sobre la supervisión de red, consulte [Supervisión de una red](howto/monitor_network.html).

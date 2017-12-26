---

copyright:
  years: 2017
lastupdated: "2017-12-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Funcionamiento de red
{: #v10_dashboard}


El Supervisor de red proporciona una visión general del entorno de blockchain, incluidos componentes de red, miembros, canales unidos, datos de rendimiento y códigos de encadenamiento desplegados.
{:shortdesc}

El Supervisor de red expone las pantallas siguientes:
* En la pantalla "Visión general", puede ver las credenciales de servicio de red así como la información de estado del componente y puede añadir iguales.
* En la pantalla "Miembros", puede gestionar los miembros de red y los certificados.
* En la pantalla "Canales", puede crear nuevos canales y ver información sobre los canales existentes.
* En la pantalla "Código de encadenamiento", puede instalar y crear una instancia de código de encadenamiento en los iguales.
* En la pantalla "Notificaciones", puede gestionar aprobaciones pendientes y ver las aprobaciones completadas.
* En la pantalla "Soporte", puede encontrar enlaces para recursos de referencia y ver funciones nuevas y modificadas en cada release.


## Visión general

En la pantalla "Visión general", se muestra información de estado en tiempo real sobre los componentes de blockchain como el clasificador, la CA y los iguales. Cada componente se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Durante la creación de la red de blockchain, se crean automáticamente tres nodos clasificadores y dos nodos de CA.  Las CA son específicas del miembro y los nodos de ordenación son puntos finales comunes compartidos a lo largo de la red.

En la **Figura 1**, se muestra la pantalla "Visión general":

![Pantalla Visión general](images/myresources.png "Visión general de red")
*Figura 1. Visión general de red*

- Acciones de nodo

  La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los componentes. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

  También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros contienen llamadas a procedimientos producidas entre los distintos componentes de red y resultan muy útiles para la depuración y resolución de problemas. Por ejemplo, experimente deteniendo un igual e intentando convertirlo en el destino de una transacción; verá errores de conectividad gRPC. Cuando reinicia el igual y vuelve a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el similar, detectará una sincronización del libro mayor a través del protocolo de rumores ("gossip protocol"). Tan pronto como haya sincronizado por completo el igual con el libro mayor, podrá realizar invocaciones y consultas normales.  
- Credenciales de servicio  
  Puede ver en el archivo JSON la información de red de bajo nivel de cada componente pulsando el botón **Credenciales de servicio** en la parte superior derecha del separador "Recursos". Se presenta toda la información de configuración que necesitará para una aplicación. Observe, sin embargo, que este archivo solo contiene las direcciones de componentes específicos y nodos de ordenación compartidos. Si necesita más iguales como destinos, deberá obtener sus puntos finales.
  La cabecera que contiene "url" muestra el punto final de API de cada componente. Estos puntos finales se necesitan para marcar el destino de componentes de red específicos de una aplicación del cliente y sus definiciones suelen encontrarse en un archivo de configuración de tipo JSON que acompaña a la app. Si está personalizando una aplicación que necesita aprobación de iguales que no forman parte de la organización, deberá recuperar las direcciones IP de esos iguales con los operadores relevantes en una operación externa. Los clientes deben poder conectarse a cualquier igual del que necesiten una respuesta.  
- Adición de iguales  
  Pulse el botón **Añadir iguales** en la parte superior derecha para añadir nodos iguales a la red. Cada miembro puede añadir hasta tres iguales a una red. Puede añadir nodos iguales por primera vez al crear una red o unirse a ella o posteriormente en el Supervisor de red.   
  En el panel emergente "Añadir iguales", seleccione el número y tamaño de los nodos iguales que desea agregar.  Actualmente solo se pueden adquirir iguales "pequeños"; sin embargo, al final habrá iguales "medianos" y "grandes" para ayudar a acomodar las cargas más grandes y aumentar el rendimiento de las transacciones.  Próximamente se ofrecerá información detallada sobre el tamaño de los iguales y las métricas de rendimiento...
  
## Miembros

La pantalla "Miembros" contiene dos separadores para visualizar información de miembros de red en el separador "Miembros" y la información de certificado en el separador "Certificados".

En la **Figura 2**, se muestra la pantalla inicial "Miembros" con los miembros de su red en el separador "Miembros":

![Separador Miembros de la pantalla Miembros](images/monitor_members.png "Miembros de red")
*Figura 2. Miembros de red*

Además de los miembros que invite al crear la red, puede invitar a otros miembros en el separador "Miembros". Para invitar a un miembro a la red, especifique el nombre de la institución y la dirección de correo electrónico del operador y pulse **Añadir miembro**. Una red puede tener un total de 15 miembros (incluido el iniciador de red). Para eliminar un miembro de la red, pulse el símbolo de "eliminación" al final de la fila del miembro.

En la **Figura 3**, se muestra la pantalla inicial "Miembros" con los certificados de los miembros en el separador "Certificados":

![Separador Certificados de la pantalla Miembros](images/monitor_certificates.png "Certificados")
*Figura 3. Certificados*

Los operadores pueden gestionar los certificados de miembros de la misma institución en el separador "Certificados". Pulse **Añadir certificado** para abrir el panel "Añadir certificado". Indique un nombre para el certificado, pegue los certificados del lado de cliente en formato PEM en el campo "Clave" y pulse **Enviar**. Debe reiniciar sus iguales antes de que certificados del lado de cliente entren en vigor.

Para obtener información sobre la generación de la clave de certificado, consulte [Generación de certificados del lado de cliente](v10_application.html#generating-the-client-side-certificates).

## Canales

Puede segregar su red en canales, de manera que cada canal represente a un subconjunto de miembros con autorización para ver los datos correspondientes a los códigos de encadenamiento de dicho canal. Cada red debe tener al menos un canal para que se lleven a cabo las transacciones. Cada canal tiene un libro mayor único y los usuarios deben estar correctamente autenticados para poder realizar operaciones de lectura/escritura en este libro mayor. Si no está en un canal, no podrá ver los datos.

En la **Figura 4**, se muestra la pantalla inicial del panel de control con una visión general de todos los canales de la red:

![Canales](images/channels.png "Canales")
*Figura 4. Canales*

La creación de un canal generará un libro mayor específico del canal. Para más información, consulte [Creación de un canal](howto/create_channel.html).

También puede seleccionar un canal existente para ver más detalles precisos sobre el canal, la pertenencia y los códigos de encadenamiento activos. Para más información, consulte [Supervisión de una red](howto/monitor_network.html).  


## Código de encadenamiento

El código de encadenamiento define la lógica empresarial y las instrucciones transaccionales para crear y modificar activos.

En la **Figura 5**, se muestra la pantalla inicial del panel de control de códigos de encadenamiento:

![Códigos de encadenamiento](images/chaincode_install_overview.png "Códigos de encadenamiento")
*Figura 5. Códigos de encadenamiento*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del nodo igual y luego se crea una instancia en un canal.  Para más información, consulte [Instalación y creación de una instancia de código de encadenamiento](howto/install_instantiate_chaincode.html).


## Notificaciones

Puede gestionar solicitudes pendientes y visualizar las solicitudes completadas en la pantalla "Notificaciones". 

En la **Figura 6**, se muestra la pantalla "Notificaciones":

![Notificaciones](images/notifications.png "Notificaciones")
*Figura 6. Notificaciones*

Cuando crea un canal o se le invita a un nuevo canal, recibirá una notificación en el Supervisor de red. 

Las solicitudes se agrupan en los subseparadores "Todas", "Pendientes" y "Completadas". Los números tras la cabecera del subseparador indican el número de solicitudes de cada subseparador.
   * Puede encontrar todas las solicitudes en el subseparador "Todas".
   * Las solicitudes que no ha aceptado ni rechazado, o que no ha visto, se encuentran en el subseparador "Pendientes". Pulse el botón **Revisar solicitud** para ver la solicitud, que incluye la política de canal y los miembros, y el estado de votación. Si es un operador de canal, puede **Aceptar** o **Rechazar** la solicitud, o bien puede gestionarla en otro momento pulsando **Más tarde**. Si la solicitud es aceptada por suficientes operadores de canal, puede pulsar **Enviar solicitud** para activar la actualización del canal. 
   * La solicitud enviada se mostrará en el subseparador "Completadas".  Puede pulsar **Revisar solicitud** para ver sus detalles.
  
Si tiene una lista larga de solicitudes, puede buscar una en el campo de búsqueda situado en la parte superior. 

Para suprimir solicitudes pendientes, seleccione los recuadros situados delante de las mismas y pulse **Suprimir solicitud**. Tenga en cuenta que no se pueden suprimir solicitudes completadas.


## Soporte

La pantalla "Soporte" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

Utilice los enlaces y recursos de esta página para acceder a los foros sobre resolución de problemas y soporte. Si no puede depurar el problema ni determinar una respuesta a su pregunta, pulse el enlace **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}** y siga las instrucciones para enviar incidencias. 

En la **Figura 7**, se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support.png "Soporte")
*Figura 7. Soporte de Blockchain*

* [Documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html), que es este sitio de documentación, ofrece orientación sobre cómo empezar a trabajar con {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}}. Puede consultar los temas correspondientes desde el navegador o buscando un término con la función de búsqueda situada en la parte superior.  
* [IBM Developer Works ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/blockchain/) en la **Ayuda de la comunidad** contiene recursos e información para desarrolladores.  
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.   
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} utilizando la opción [Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]().  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.  
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]() en **Aplicaciones de muestra de {{site.data.keyword.blockchain}}** ofrece instrucciones y fragmentos de código de muestra para el desarrollo de aplicaciones.  
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/) y la [Comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]() en **Hyperledger Fabric** proporcionan más detalles sobre la pila Hyperledger Fabric.  
  Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.   
  
  
En la **Figura 8**, se muestra la pantalla inicial "Miembros" con las funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release](images/releasenotes.png "Notas del release")
*Figura 8. Notas del release*


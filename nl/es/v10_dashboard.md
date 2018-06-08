---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Operación de la red del Plan empresarial
{: #v10_dashboard}

La plataforma {{site.data.keyword.blockchainfull}} incorpora un supervisor de red proporciona una visión general del entorno de blockchain, incluidos recursos de red, miembros, canales unidos, datos de rendimiento de las transacciones y códigos de encadenamiento desplegados. El supervisor de red también le ofrece un punto de partida para ejecutar las API de Swagger y desarrollar una red con la función de la plataforma {{site.data.keyword.blockchainfull_notm}} para desarrollar y probar aplicaciones de ejemplo.
{:shortdesc}

Puede [cambiar el nombre de la red del Plan empresarial](#ep-network-name) en el Supervisor de red.

El Supervisor de red expone las pantallas siguientes en tres secciones. Puede ir a cada pantalla desde el navegador izquierdo del supervisor de red.
- La sección **Mi red** contiene las pantallas "[Visión general](#overview)", "[Miembros](#members)", "[Canales](#channels)", "[Notificaciones](#notifications)" y "[API](#apis)".
- La sección **Mi código** contiene las pantallas "[Desarrollar código](#write_code)", "[Instalar código](#chaincode)" y "[Probar ejemplos](#samples)".
- La pantalla "[Obtener ayuda](#support)" muestra información de soporte, así como las notas del release para helios e Hyperledger Fabric (la base de código en la que se basa la plataforma {{site.data.keyword.blockchainfull_notm}}).

Puede [comprobar y configurar las preferencias de red](#network-preferences) desde el menú desplegable de la parte superior derecha del Supervisor de red.

En esta guía de aprendizaje se describe cada una de las pantallas y funciones anteriores.

## Actualizar el nombre de red
{: #ep-network-name}

Al crear una red de Plan empresarial, la Plataforma de {{site.data.keyword.blockchainfull_notm}} asigna un nombre a la red. Sin embargo, puede actualizar este nombre de red en cualquier momento en el Supervisor de red.

En la parte superior izquierda del navegador del Supervisor de red, pulse en el nombre de red y el campo se volverá editable. Escriba el nuevo nombre de red que desee utilizar y pulse la tecla **Intro**. El nombre de red se actualizará en pocos segundos.

**Figura 1** muestra los pasos para actualizar el nombre de red del Plan inicial del nombre asignado a "Red del plan inicial".

![Actualizar nombre de red](images/update_network_name_ep.gif "Actualizar nombre de red")
*Figura 1. Actualizar nombre de red*


## Visión general
{: #overview}

En la pantalla "Visión general" se muestra información de estado en tiempo real sobre los recursos de blockchain, como el clasificador, la CA y los nodos iguales. Cada recurso se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Durante la creación de la red de blockchain, se crean automáticamente tres nodos clasificadores y dos nodos de CA. Las CA son específicas del miembro y los nodos de ordenación son puntos finales comunes compartidos en la red.

**Figura 2** muestra la pantalla "Visión general":

![Pantalla Visión general](images/myresources.png "Visión general de red")
*Figura 2. Visión general de red*

### Acciones de nodo
La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los recursos. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

Tenga en cuenta que las acciones Detener e Iniciar no están disponibles para un nodo clasificador. En general, no es necesario detener ni iniciar los nodos iguales o CA en una red. Las acciones Detener e Iniciar se proporcionan en caso de que sea necesario reiniciar un igual, por ejemplo para activarlo en un estado limpio.

También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros muestran las llamadas entre los distintos recursos de red y resultan muy útiles para la depuración y resolución de problemas. Por ejemplo, si detiene un igual e intenta convertirlo en el destino de una transacción, verá errores de conectividad. Cuando reinicie el igual y vuelva a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el igual, detectará una sincronización del libro mayor a medida que recibe los bloques confirmados mientras estaba inactivo. Cuando se haya sincronizado por completo el libro mayor, podrá realizar invocaciones y consultas normales sobre el mismo.

### Perfil de conexión
{: #enterprise-connection-profile}
Puede ver en el archivo JSON la información de red de bajo nivel de cada recurso pulsando el botón **Perfil de conexión**. El perfil de conexión contiene toda la información de configuración que necesita para una aplicación. Sin embargo, debido a que este archivo solo contiene las direcciones de sus componentes específicos y del clasificador, si necesita más iguales como destinos, deberá obtener sus puntos finales. La cabecera que contiene "url" muestra el punto final de API de cada componente. Estos puntos finales se necesitan para marcar el destino de componentes de red específicos de una aplicación del cliente y sus definiciones suelen encontrarse en un archivo de configuración de tipo JSON que acompaña a la app. Si está personalizando una aplicación que necesita aprobación de iguales que no forman parte de la organización, deberá recuperar las direcciones IP de esos iguales de los operadores relevantes en una operación externa. Los clientes deben poder conectarse a cualquier igual del que necesiten una respuesta.

### Adición de iguales
{: #peers}
Pulse el botón **Añadir iguales** para añadir nodos iguales a la red. En el Plan inicial, se añadirán automáticamente dos iguales en el momento de crear la red. En el Plan empresarial, puede añadir nodos iguales por primera vez al crear una red o unirse a ella o posteriormente en el supervisor de red. Puede estar en distintos escenarios cuando necesite más iguales.  Por ejemplo, es posible que desee que varios iguales se unan al mismo canal por motivos de redundancia. Cada igual procesa las transacciones del canal y escribe en sus respectivas copias del libro mayor. En el caso de que uno de los iguales falle, los otros iguales pueden seguir procesando transacciones y solicitudes de aplicación.  Podría equilibrar la carga de forma simétrica de todas las solicitudes de aplicaciones entre los iguales, o podría elegir como destino diferentes iguales para diferentes funciones. Por ejemplo, puede utilizar un igual para consultar el libro mayor y utilizar otro igual para procesar las aprobaciones de actualizaciones del libro mayor.

  En el panel emergente "Añadir iguales", seleccione el número de nodos iguales que desea agregar.
<!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->

## Miembros
{: #members}
La pantalla "Miembros" contiene dos separadores para visualizar información de miembros de red en el separador "Miembros" y la información de certificado en el separador "Certificados".

### Miembros
{: #members_tab}
**Figura 3** muestra la pantalla inicial "Miembros" que muestra los miembros de la red en el separador "Miembros":

![Separador Miembros de la pantalla Miembros](images/monitor_members.png "Miembros de red")
*Figura 3. Miembros de la red*

Además de los miembros que invite al crear la red, puede invitar a otros miembros en el separador "Miembros". Para invitar a un miembro a la red, especifique el nombre de la institución y la dirección de correo electrónico del operador y pulse **Añadir miembro**. Una red puede tener un total de 15 miembros (incluido el iniciador de red). Para eliminar un miembro de la red, pulse el símbolo de "eliminación" al final de la fila del miembro.

### Certificados
En la **Figura 4** se muestra la pantalla inicial "Miembros" con los certificados de los miembros en el separador "Certificados":

![Separador Certificados de la pantalla Miembros](images/monitor_certificates.png "Certificados")
*Figura 4. Certificados*

Los operadores pueden gestionar los certificados de miembros de la misma institución en el separador "Certificados". Pulse **Añadir certificado** para abrir el panel "Añadir certificado". Indique un nombre para el certificado, pegue los certificados del lado de cliente en formato PEM en el campo "Clave" y pulse **Enviar**. Debe reiniciar sus iguales antes de que certificados del lado de cliente entren en vigor.

Para obtener información sobre cómo generar su clave de certificado, consulte [Generación de certificados del lado de cliente](v10_application.html#generating-the-client-side-certificates).

## Canales
{: #channels}

Puede segregar su red en canales, de manera que cada canal represente a un subconjunto de miembros con autorización para ver los datos correspondientes al código de encadenamiento para el que se ha de crear instancias en dicho canal. Cada red debe tener al menos un canal para que se lleven a cabo las transacciones. Cada canal tiene un libro mayor único y los usuarios deben estar correctamente autenticados para poder realizar operaciones de lectura/escritura en este libro mayor. Si no está en un canal, no podrá ver los datos.

**Figura 5** muestra la pantalla inicial del panel de control con una visión general de todos los canales de la red:

![Canales](images/channels.png "Canales")
*Figura 5. Canales*

La creación de un canal genera un libro mayor específico del canal. Para obtener más información, consulte [Creación de un canal](howto/create_channel.html).

También puede seleccionar un canal existente para ver detalles más precisos sobre el canal, la pertenencia y el código de encadenamiento activo. Para obtener más información, consulte [Supervisión de una red](howto/monitor_network.html).

## Notificaciones
{: #notifications}

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

## API
{: #apis}

Para facilitar el desarrollo de aplicaciones, la plataforma {{site.data.keyword.blockchainfull_notm}} expone las API que puede probar en la red en una IU de Swagger.

En la **Figura 7** se muestra la pantalla "API":

![API](images/API_screen.png "API")
*Figura 7. API*

Pulse el enlace **IU de Swagger** para abrir la IU de Swagger. Tenga en cuenta que debe autorizar la IU de Swagger con sus credenciales de red (que encontrará en esta página de API) para poder ejecutar las API. Para obtener más información, consulte [Cómo interactuar con la red con las API de Swagger](howto/swagger_apis.html).

## Desarrollar código
{: #write-code}

El Plan empresarial integra la función de desarrollo de la plataforma {{site.data.keyword.blockchainfull_notm}} y proporciona un entorno de desarrollo con herramientas y tecnologías estándar de la industria. Puede desarrollar su red en el entorno en línea o de forma local. Después de desarrollar una red, puede desplegarla en la red del Plan empresarial.

**Figura 8** muestra la pantalla "Desarrollar código":

![Desarrollar código](images/write_code.png "Desarrollar código")
*Figura 8. Desarrollar código*

Para obtener más información sobre cómo desarrollar y desplegar su código con el Plan empresarial, consulte [Desarrollo de redes de empresa en el Plan empresarial](develop_enterprise.html).

## Instalar código
{: #chaincode}

El código de encadenamiento, que también se conoce como "contrato inteligente", son fragmentos de software que contienen un conjunto de funciones para consultar y actualizar el libro mayor. Se instalan en iguales y se crean instancias de los mismos en un canal.

En la **Figura 9** se muestra la pantalla "Instalar código":

![Instalar código](images/chaincode_install_overview.png "Instalar código")
*Figura 9. Instalar código*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html).

## Probar ejemplos
{: #samples}

Las aplicaciones de ejemplo le ayudan a comprender mejor una red de blockchain y el desarrollo de aplicaciones. Siga el enlace al repositorio de Canicas del supervisor de red para obtener información sobre cómo instalar la aplicación de ejemplo Canicas. Para obtener más información sobre cómo desarrollar y desplegar sus propios ejemplos, consulte el apartado [Desarrollo de aplicaciones](v10_application.html).

**Figura 10** muestra la pantalla "Probar ejemplos":

![Probar ejemplos](images/sample_overview_ep.png "Probar ejemplos")
*Figura 10. Ejemplos*

## Obtener ayuda
{: #support}

La pantalla "Obtener ayuda" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

En la **Figura 11** se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support.png "Soporte")
*Figura 11. Soporte de Blockchain*

Utilice los enlaces y recursos de esta página para acceder a los foros sobre resolución de problemas y soporte.

* La [documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html) bajo **Cómo empezar**, que es un sitio de documentación, ofrece una guía sobre cómo comenzar a utilizar la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.Bluemix_notm}}. Encontrará los temas correspondientes en el navegador izquierdo o buscando un término con la función de búsqueda situada en la parte superior.
* [IBM Developer Works ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/blockchain/) en la **Ayuda de la comunidad** contiene recursos e información para desarrolladores.
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} con la opción **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}**.  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/ibm-blockchain) bajo **Aplicaciones de ejemplo de Blockchain** contiene una guía y fragmentos de código de ejemplo que le ayudarán en el desarrollo de aplicaciones.
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/) y la [Comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) en **Hyperledger Fabric** proporcionan más detalles sobre la pila Hyperledger Fabric.
  Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.

Si no puede depurar su problema ni determinar una respuesta a su pregunta, envíe un caso de soporte en el portal de servicio de IBM Cloud. Para obtener más información, consulte [Obtención de soporte](ibmblockchain_support.html).

En las figuras 12 y 13 se muestra la pantalla "Obtener ayuda" inicial con las funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release de helios](images/releasenotes_helios.png "Notas del release de helios")
*Figura 12. Notas del release para Helios*

![Notas del release de Fabric](images/releasenotes_Fabric.png "Notas del release de Fabric")
*Figura 13. Notas del release para Fabric*


## Preferencias de red
{: #network-preferences}

Pulse la esquina superior derecha y abra el menú desplegable y luego las **Preferencias de red**. Se abre la ventana Preferencias de red. La ventana Preferencias de red muestra la información básica de la red, como el nombre de la red, la versión de Fabric, la ubicación de la red en {{site.data.keyword.cloud_notm}}, y el tipo de base de datos del libro mayor.

Las redes del Plan empresarial se actualizan en Fabric v1.1 pronto<!-- May 15th, 2018 will run on Hyperledger Fabric v1.1-->. Si crea redes después de la actualización, también puede gestionar el tiempo de espera de inactividad web y TLS mutuo para la red en la ventana Preferencias de red. Estos valores puede cambiarlos solo el iniciador de la red.

<!--
Enterprise Plan networks that are created after May 15th, 2018 will run on Hyperledger Fabric v1.1. If you create networks after the upgrade, you can also manage web inactivity timeout, mutual TLS, and switch your ledger to CouchDB for your network in the Network preferences window. These settings can be changed by the network initiator only.
-->

### Tiempo de espera de inactividad web
{: #web-inactivity-timeout}

**Nota**: Solo el **iniciador de red** puede cambiar el valor de tiempo de espera de inactividad web. Este es un valor de nivel de red y afectará a todos los miembros de la red.

El tiempo de espera de inactividad web está establecido en **Desactivado** de forma predeterminada. Si coloca el tiempo de espera de inactividad web en **Activado**, cualquier miembro de la red será desconectado automáticamente tras 10 minutos de inactividad. Cuando el temporizador de inactividad web llegue a los 10 minutos, la función de tiempo de espera de inactividad web terminará las sesiones web inactivas para garantizar la seguridad de la cuenta del miembro de la red. Al pulsar un enlace o al renovar el Supervisor de red se restablece el temporizador de inactividad web. Antes de llegar a 10 minutos, el cierre de la ventana o del separador del navegador también termina la sesión web.

### TLS mutuo
{: #mutual-tls}

TLS mutuo protege la comunicación entre la aplicación y la red, y solo garantiza que pueda comunicarse con la red.

**Nota**: Solo un **iniciador de red** puede habilitar o inhabilitar TLS mutuo. Este es un valor de nivel de red y afectará a todos los miembros de la red.

El botón TLS mutuo está establecido en **Desactivado** de forma predeterminada. Si habilita TLS mutuo, debe actualizar las aplicaciones para dar soporte a esta función. De lo contrario, las aplicaciones no podrán comunicarse con la red.

Para una red de Plan empresarial de Fabric 1.1, cada organización tiene su propia entidad emisora de certificados (CA) de TLS mutuo. La información necesaria para conectarse a la CA de TLS mutuo está disponible en el [Perfil de conexión ](##enterprise-connection-profile) accesible desde la pantalla **Visión general** del Supervisor de red pulsando el botón **Perfil de conexión**.  El perfil de conexión contiene la información necesaria para conectarse a la CA y para obtener los certificados que necesite para conectarse a la red.

En el Perfil de conexión, localice la sección `certificateAuthorities` donde encontrará los siguientes atributos que son necesarios para registrarse y obtener los certificados para comunicarse con la red utilizando TLS mutuo.

- `url`: URL para conectarse a la CA que puede proporcionar certificados de TLS mutuo
- `enrollId`: ID de inscripción a utilizar para obtener un certificado
- `enrollSecret`: Secreto de inscripción a utilizar para obtener un certificado
- `x-tlsCAName`: Nombre de la CA a utilizar para obtener el certificado que permitirá que la aplicación se comunique con TLS mutuo.

Para obtener más información sobre cómo actualizar las aplicaciones para dar soporte a TLS mutuo, consulte [Cómo configurar TLS mutuo ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html)

<!--
### CouchDB ledger type
{: #couchdb}
**Note**: Only the **network initiator** can switch the ledger database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.
Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your ledger database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.
To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.
If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up index, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/couchdb_as_state_database.html). For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode).
-->

**Figura 14** muestra la ventana "Preferencias de la red":

<!-- ![Network preferences](images/network_preferences_ep.gif "Network preferences") -->
![Preferencias de la red](images/network_preferences_ep_tmp.png "Preferencias de la red")  
*Figura 14. Preferencias de la red*

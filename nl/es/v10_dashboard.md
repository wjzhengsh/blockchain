---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Operación de la red del Plan empresarial
{: #v10_dashboard}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


La plataforma {{site.data.keyword.blockchainfull}} incorpora un supervisor de red proporciona una visión general del entorno de blockchain, incluidos recursos de red, miembros, canales unidos, datos de rendimiento de las transacciones y códigos de encadenamiento desplegados. El supervisor de red también le ofrece un punto de partida para ejecutar las API de Swagger y desarrollar una red con la función de la plataforma {{site.data.keyword.blockchainfull_notm}} para desarrollar y probar aplicaciones de ejemplo.
{:shortdesc}

Puede [cambiar el nombre de la red del Plan empresarial](#ep-network-name) en el Supervisor de red.

El Supervisor de red expone las pantallas siguientes en tres secciones. Puede ir a cada pantalla desde el navegador izquierdo del supervisor de red.
- La sección **Mi red** contiene las pantallas "[Visión general](#overview)", "[Miembros](#members)", "[Canales](#channels)", "[Notificaciones](#notifications)", "[Entidad emisora de certificados](#ca)" y "[API](#apis)".
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

En la pantalla "Visión general" se muestra información de estado en tiempo real sobre los recursos de blockchain, como el clasificador, la CA y los nodos iguales. Cada recurso se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Durante la creación de la red blockchain, se crean automáticamente tres nodos clasificadores y dos nodos de CA. Las CA son específicas del miembro y los nodos de ordenación son puntos finales comunes compartidos en la red.

**Figura 2** muestra la pantalla "Visión general":

![Pantalla Visión general](images/myresources.png "Visión general de red")
*Figura 2. Visión general de red*

### Acciones de nodo
La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los recursos. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

Tenga en cuenta que las acciones Detener e Iniciar no están disponibles para un nodo clasificador. En general, no es necesario detener ni iniciar los nodos iguales o CA en una red. Las acciones Detener e Iniciar se proporcionan en caso de que sea necesario reiniciar un igual, por ejemplo para activarlo en un estado limpio.

También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros muestran las llamadas entre los distintos recursos de red y resultan muy útiles para la depuración y resolución de problemas. Por ejemplo, si detiene un igual e intenta convertirlo en el destino de una transacción, verá errores de conectividad. Cuando reinicie el igual y vuelva a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el igual, detectará una sincronización del libro mayor a medida que recibe los bloques confirmados mientras estaba inactivo. Cuando se haya sincronizado por completo el libro mayor, podrá realizar invocaciones y consultas normales sobre el mismo.

### Configuración de igual remoto  

Si despliega un igual remoto fuera de {{site.data.keyword.cloud_notm}}, tiene que proporcionar la información de punto final de API de la red al igual remoto durante la configuración. Pulse el botón ** Configuración de igual remoto** para recuperar la información de punto final de API de la red para configurar el igual remoto. La ventana emergente proporciona la información de punto final de API del ID de red, el MSP de la organización, el nombre de CA, el URL de CA y el certificado de CA TLS. Puede pulsar el icono de copia al final de cada campo para copiar el valor de dicho campo, o bien pulsar el botón **Descargar** para guardar valores de todos los campos en un archivo JSON. Para obtener más información acerca de los iguales remotos, consulte [Acerca de los iguales remotos](howto/remote_peer.html).

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
<!--
For more information about generating your certificate key, see [Generating the client-side certificates](v10_application.html#generating-the-client-side-certificates).-->

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

## Entidad
emisora de certificados
{: #ca}

La tabla de la pantalla "Entidad emisora de certificados" (CA) muestra todas las identidades que se han registrado con su organización, incluidas las aplicaciones de administrador, de iguales y de cliente. También puede utilizar esta pantalla para registrar una nueva identidad.

En la **Figura 7** se muestra la pantalla "Entidad emisora de certificados":

![Entidad emisora de certificados](images/CA_screen.png "Entidad emisora de certificados")
*Figura 7. Entidad emisora de certificados*

Pulse el botón **Generar certificado** para obtener un nuevo certificado público y una clave privada de la CA. Este panel se puede utilizar como alternativa a [generar un par de claves pública y privada](v10_application.html#register-app) para una aplicación cliente que utiliza Fabric SDK. El campo **Certificado** contiene el certificado público, al que también se denomina signCert o certificado de inscripción, justo encima de la **Clave privada**. Puede pulsar el icono de copia que hay al final de cada campo para copiar el valor. **Tenga en cuenta** que la plataforma {{site.data.keyword.blockchainfull_notm}} no almacena estos certificados. Tiene que guardarlos y almacenarlos de forma segura. Consulte [Información de MSP](certificates.html#msp) para ver más detalles.

Pulse el botón **Añadir usuario** para registrar una nueva identidad en la organización. En la ventana emergente **Añadir usuario**, rellene los campos siguientes y luego pulse **Enviar**.
  - **ID:** el nombre de la nueva identidad, al que a veces se denomina `ID de inscripción`. **Guarde este valor** para cuando configure un igual remoto o inscriba una nueva aplicación.
  - **Secreto:** la contraseña de la identidad, que a veces recibe el nombre de `Secreto de inscripción`.  **Guarde este valor** para cuando configure un igual remoto o inscriba una nueva aplicación.  
  - **Tipo:** seleccione el tipo de identidad que desea registrar, que puede ser una aplicación igual o de cliente.
  - **Afiliación** la afiliación dentro de la organización, como por ejemplo `org1`, a la que pertenece la identidad.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si deja el campo en blanco, el valor predeterminado es un número ilimitado de inscripciones.

Encontrará más información acerca de su CA si visita la guía de aprendizaje [Gestión de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}](certificates.html).

## API
{: #apis}

La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece varias API REST en Swagger que puede utilizar para gestionar los nodos, canales, iguales y miembros de la red. Las aplicaciones pueden utilizar estas API para controlar los recursos de red importantes sin utilizar el supervisor de red.

En la **Figura 8** se muestra la pantalla "API":

![API](images/API_screen.png "API")
*Figura 8. API*

Pulse el enlace **IU de Swagger** para abrir la IU de Swagger. Tenga en cuenta que debe autorizar la IU de Swagger con sus credenciales de red (que encontrará en esta página de API) para poder ejecutar las API. Para obtener más información, consulte [Cómo interactuar con la red con las API de Swagger](howto/swagger_apis.html).

## Desarrollar código
{: #write-code}

El Plan empresarial integra la función de desarrollo de la plataforma {{site.data.keyword.blockchainfull_notm}} y proporciona un entorno de desarrollo con herramientas y tecnologías estándar de la industria. Puede desarrollar su red en el entorno en línea o de forma local. Después de desarrollar una red, puede desplegarla en la red del Plan empresarial.

En la **Figura 9** se muestra la pantalla "Desarrollar código":

![Desarrollar código](images/write_code.png "Desarrollar código")
*Figura 9. Desarrollar código*

Para obtener más información sobre cómo desarrollar y desplegar su código con el Plan empresarial, consulte [Desarrollo de redes de empresa en el Plan empresarial](develop_enterprise.html).

## Instalar código
{: #chaincode}

El código de encadenamiento, que también se conoce como "contrato inteligente", son fragmentos de software que contienen un conjunto de funciones para consultar y actualizar el libro mayor. Se instalan en iguales y se crean instancias de los mismos en un canal.

En la **Figura 10** se muestra la pantalla "Instalar código":

![Instalar código](images/chaincode_install_overview.png "Instalar código")
*Figura 10. Instalar código*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html).

## Probar ejemplos
{: #samples}

Las aplicaciones de ejemplo le ayudan a comprender mejor una red blockchain y el desarrollo de aplicaciones. Siga los enlaces **Ver en GitHub** para obtener más información sobre cómo utilizar las muestras y desplegarlas en la plataforma de {{site.data.keyword.blockchainfull_notm}}. Para obtener más información sobre cómo desarrollar y desplegar los ejemplos, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).

En la **Figura 11** se muestra la pantalla "Probar ejemplos":

![Probar ejemplos](images/sample_overview_ep.png "Probar ejemplos")
*Figura 11. Ejemplos*

## Obtener ayuda
{: #support}

La pantalla "Obtener ayuda" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

En la **Figura 12** se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support.png "Soporte")
*Figura 12. Soporte de Blockchain*

### Foros de soporte y recursos de Blockchain
{: #support-forums}

Utilice los enlaces y recursos de esta página para acceder a los foros sobre resolución de problemas y soporte.

* La [documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html) bajo **Cómo empezar**, que es un sitio de documentación, ofrece una guía sobre cómo comenzar a utilizar la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.Bluemix_notm}}. Encontrará los temas correspondientes en el navegador izquierdo o buscando un término con la función de búsqueda situada en la parte superior.
* El [Código de IBM ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/code/technologies/blockchain/) contiene patrones de código e información para los desarrolladores.
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} con la opción **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}**.  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/ibm-blockchain) bajo **Aplicaciones de ejemplo de Blockchain** contiene una guía y fragmentos de código de ejemplo que le ayudarán en el desarrollo de aplicaciones.
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/) y la [comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) bajo **Hyperledger Fabric** contienen más detalles sobre la pila Hyperledger Fabric. Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.
* [Plataforma {{site.data.keyword.blockchainfull_notm}}: desarrollo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://ibm-blockchain.github.io/develop/) bajo **Hyperledger Composer** contiene más detalles sobre Hyperledger Composer, es decir, Plataforma {{site.data.keyword.blockchainfull_notm}}: desarrollo. También puede encontrar respuestas o formular preguntas en la [comunidad de Hyperledger Composer ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y en [StackOverflow ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://stackoverflow.com/questions/tagged/hyperledger-composer).

Si no puede depurar su problema ni determinar una respuesta a su pregunta, envíe un caso de soporte en el portal de servicio de {{site.data.keyword.cloud_notm}}. Para obtener más información, consulte [Obtención de soporte](ibmblockchain_support.html).

En la **Figura 13** y en la **Figura 14** se muestra la pantalla inicial "Obtener ayuda" que muestra funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release de helios](images/releasenotes_helios.png "Notas del release de la interfaz de usuario de Network Monitor")
*Figura 13. Notas del release de la interfaz de usuario de Network Monitor*

![Notas del release de Fabric](images/releasenotes_Fabric.png "Notas del release de Fabric")
*Figura 14. Notas del release para Fabric*


## Preferencias de red
{: #network-preferences}

Pulse la esquina superior derecha y abra el menú desplegable y luego las **Preferencias de red**. Se abre la ventana Preferencias de red. La ventana Preferencias de red muestra la información básica de la red, como por ejemplo el nombre de red, la versión de Fabric, la ubicación de la red en {{site.data.keyword.cloud_notm}} y el tipo de base de datos de estado.

Las redes del Plan empresarial que se creen después del 15 de mayo de 2018 se ejecutarán en Hyperledger Fabric v1.1. Si crea redes después de la actualización, también puede gestionar el tiempo de espera de inactividad web y TLS mutuo para la red en la ventana Preferencias de red. Estos valores puede cambiarlos solo el iniciador de la red.

### Tiempo de espera de inactividad web
{: #web-inactivity-timeout}

**Nota**: Solo el **iniciador de red** puede cambiar el valor de tiempo de espera de inactividad web. Este es un valor de nivel de red y afectará a todos los miembros de la red.

El tiempo de espera de inactividad web está establecido en **Desactivado** de forma predeterminada. Si coloca el tiempo de espera de inactividad web en **Activado**, cualquier miembro de la red será desconectado automáticamente tras 10 minutos de inactividad. Cuando el temporizador de inactividad web llegue a los 10 minutos, la función de tiempo de espera de inactividad web terminará las sesiones web inactivas para garantizar la seguridad de la cuenta del miembro de la red. Al pulsar un enlace o al renovar el Supervisor de red se restablece el temporizador de inactividad web. Antes de llegar a 10 minutos, el cierre de la ventana o del separador del navegador también termina la sesión web.

### TLS mutuo
{: #mutual-tls}

TLS mutuo protege la comunicación entre la aplicación y la red.

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

### CouchDB state database
{: #couchdb}

**Note**: Only the **network initiator** can switch the state database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.

Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your state database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.

To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.

If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up index, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/couchdb_as_state_database.html) in the Hyperledger Fabric documentation. You can also find an example that uses an index with chaincode in this [Fabric tutorial![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}. For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode).

-->

En la **Figura 15** se muestra la ventana "Preferencias de la red":

![Preferencias de la red](images/network_preferences_ep_tmp.png "Preferencias de la red")
*Figura 15. Preferencias de la red*

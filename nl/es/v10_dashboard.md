---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

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

El Supervisor de red expone las pantallas siguientes en tres secciones. Puede ir a cada pantalla desde el navegador izquierdo del supervisor de red.
- La sección **Mi red** contiene las pantallas "[Visión general](#overview)", "[Miembros](#members)", "[Canales](#channels)", "[Notificaciones](#notifications)" y "[API](#apis)".
- La sección **Mi código** contiene las pantallas "[Escribir código](#write_code)", "[Instalar código](#chaincode)" y "[Probar ejemplos](#samples)".
- La pantalla "[Obtener ayuda](#support)" muestra información de soporte, así como las notas del release para helios e Hyperledger Fabric (la base de código en la que se basa la plataforma {{site.data.keyword.blockchainfull_notm}}).


## Visión general
{: #overview}

En la pantalla "Visión general" se muestra información de estado en tiempo real sobre los recursos de blockchain, como el clasificador, la CA y los nodos iguales. Cada recurso se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Durante la creación de la red de blockchain, se crean automáticamente tres nodos clasificadores y dos nodos de CA. Las CA son específicas del miembro y los nodos de ordenación son puntos finales comunes compartidos en la red.

En la **Figura 1** se muestra la pantalla "Visión general":

![Pantalla Visión general](images/myresources.png "Visión general de red")
*Figura 1. Visión general de red*

### Acciones de nodo
  La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los recursos. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

  Tenga en cuenta que las acciones Detener e Iniciar no están disponibles para un nodo clasificador. En general, no es necesario detener ni iniciar los nodos iguales o CA en una red. Las acciones Detener e Iniciar se proporcionan en caso de que sea necesario reiniciar un igual, por ejemplo para activarlo en un estado limpio.

  También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros muestran las llamadas entre los distintos recursos de red y resultan muy útiles para la depuración y resolución de problemas. Por ejemplo, si detiene un igual e intenta convertirlo en el destino de una transacción, verá errores de conectividad. Cuando reinicie el igual y vuelva a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el igual, detectará una sincronización del libro mayor a medida que recibe los bloques confirmados mientras estaba inactivo. Cuando se haya sincronizado por completo el libro mayor, podrá realizar invocaciones y consultas normales sobre el mismo.

### Perfil de conexión
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
En la **Figura 2** se muestra la pantalla "Miembros" inicial con los miembros de su red en el separador "Miembros":

![Separador Miembros de la pantalla Miembros](images/monitor_members.png "Miembros de red")
*Figura 2. Miembros de red*

Además de los miembros que invite al crear la red, puede invitar a otros miembros en el separador "Miembros". Para invitar a un miembro a la red, especifique el nombre de la institución y la dirección de correo electrónico del operador y pulse **Añadir miembro**. Una red puede tener un total de 15 miembros (incluido el iniciador de red). Para eliminar un miembro de la red, pulse el símbolo de "eliminación" al final de la fila del miembro.

### Certificados
En la **Figura 3** se muestra la pantalla inicial "Miembros" con los certificados de los miembros en el separador "Certificados":

![Separador Certificados de la pantalla Miembros](images/monitor_certificates.png "Certificados")
*Figura 3. Certificados*

Los operadores pueden gestionar los certificados de miembros de la misma institución en el separador "Certificados". Pulse **Añadir certificado** para abrir el panel "Añadir certificado". Indique un nombre para el certificado, pegue los certificados del lado de cliente en formato PEM en el campo "Clave" y pulse **Enviar**. Debe reiniciar sus iguales antes de que certificados del lado de cliente entren en vigor.

Para obtener información sobre cómo generar su clave de certificado, consulte [Generación de certificados del lado de cliente](v10_application.html#generating-the-client-side-certificates).

## Canales
{: #channels}

Puede segregar su red en canales, de manera que cada canal represente a un subconjunto de miembros con autorización para ver los datos correspondientes a los códigos de encadenamiento de dicho canal. Cada red debe tener al menos un canal para que se lleven a cabo las transacciones. Cada canal tiene un libro mayor único y los usuarios deben estar correctamente autenticados para poder realizar operaciones de lectura/escritura en este libro mayor. Si no está en un canal, no podrá ver los datos.

En la **Figura 4** se muestra la pantalla inicial del panel de control con una visión general de todos los canales de la red:

![Canales](images/channels.png "Canales")
*Figura 4. Canales*

La creación de un canal genera un libro mayor específico del canal. Para obtener más información, consulte [Creación de un canal](howto/create_channel.html).

También puede seleccionar un canal existente para ver más detalles precisos sobre el canal, la pertenencia y los códigos de encadenamiento activos. Para obtener más información, consulte [Supervisión de una red](howto/monitor_network.html).


## Notificaciones
{: #notifications}

Puede gestionar solicitudes pendientes y visualizar las solicitudes completadas en la pantalla "Notificaciones".

En la **Figura 5** se muestra la pantalla "Notificaciones":

![Notificaciones](images/notifications.png "Notificaciones")
*Figura 5. Notificaciones*

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

![API](images/API_screen.png "API")
*Figura 6. API*

Pulse el enlace **IU de Swagger** para abrir la IU de Swagger. Tenga en cuenta que debe autorizar la IU de Swagger con sus credenciales de red (que encontrará en esta página de API) para poder ejecutar las API. Para obtener más información, consulte [Utilización de las API de Swagger](howto/swagger_apis.html).


## Escribir código
{: #write-code}

El Plan empresarial integra la función de desarrollo de la plataforma {{site.data.keyword.blockchainfull_notm}} y proporciona un entorno de desarrollo con herramientas y tecnologías estándar de la industria. Puede desarrollar su red en el entorno en línea o de forma local. Después de desarrollar una red, puede desplegarla en la red del Plan empresarial.

![Escribir código](images/write_code.png "Escribir código")
*Figura 7. Escribir código*

Para obtener más información sobre cómo desarrollar y desplegar su código con el Plan empresarial, consulte [Desarrollo de redes de empresa en el Plan empresarial](develop_enterprise.html).


## Instalar código
{: #chaincode}

Los códigos de encadenamiento, también conocidos como "contratos digitales", son fragmentos de software que contienen un conjunto de funciones para consultar y actualizar el libro mayor. Se instalan en iguales y se crean instancias de los mismos en un canal.

![Instalar código](images/chaincode_install_overview.png "Instalar código")
*Figura 8. Instalar código*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. Para obtener más información, consulte [Instalación y creación de una instancia de código de encadenamiento](howto/install_instantiate_chaincode.html).


## Probar ejemplos
{: #samples}

Las aplicaciones de ejemplo le ayudan a comprender mejor una red de blockchain y el desarrollo de aplicaciones. Siga el enlace al repositorio de Canicas del supervisor de red para obtener información sobre cómo instalar la aplicación de ejemplo Canicas. Para obtener más información sobre cómo desarrollar y desplegar sus propios ejemplos, consulte el apartado [Desarrollo de aplicaciones](v10_application.html).

En la **Figura 9** se muestra la pantalla "Ejemplos":

![Probar ejemplos](images/sample_overview.png "Probar ejemplos")
*Figura 9. Ejemplos*


## Obtener ayuda
{: #support}

La pantalla "Obtener ayuda" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

En la **Figura 10** se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support.png "Soporte")
*Figura 10. Soporte de Blockchain*

Utilice los enlaces y recursos de esta página para acceder a los foros sobre resolución de problemas y soporte.

* La [documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html) bajo **Cómo empezar**, que es un sitio de documentación, ofrece una guía sobre cómo comenzar a utilizar la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.Bluemix_notm}}. Encontrará los temas correspondientes en el navegador izquierdo o buscando un término con la función de búsqueda situada en la parte superior.
* [IBM Developer Works ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/blockchain/) en la **Ayuda de la comunidad** contiene recursos e información para desarrolladores.
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} con la opción **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}**.  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/ibm-blockchain) bajo **Aplicaciones de ejemplo de Blockchain** contiene una guía y fragmentos de código de ejemplo que le ayudarán en el desarrollo de aplicaciones.
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/) y la [Comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) en **Hyperledger Fabric** proporcionan más detalles sobre la pila Hyperledger Fabric.
  Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.

Si no puede depurar su problema ni determinar una respuesta a su pregunta, envíe un caso de soporte en el portal de servicio de IBM Cloud. Para obtener más información, consulte [Obtención de soporte](ibmblockchain_support.html).

En las figuras 11 y 12 se muestra la pantalla "Obtener ayuda" inicial con las funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release de helios](images/releasenotes_helios.png "Notas del release de helios")  
*Figura 11. Notas del release para Helios*

![Notas del release de Fabric](images/releasenotes_Fabric.png "Notas del release de Fabric")  
*Figura 12. Notas del release para Fabric*

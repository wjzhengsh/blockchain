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

# Operación de la red del Plan inicial
{: #operate-starter-plan-network}

La plataforma {{site.data.keyword.blockchainfull}} incorpora un supervisor de red proporciona una visión general del entorno de blockchain, incluidos recursos de red, miembros, canales unidos, datos de rendimiento de las transacciones y códigos de encadenamiento desplegados. El supervisor de red también le ofrece un punto de partida para ejecutar las API de Swagger y desarrollar una red con la función de la plataforma {{site.data.keyword.blockchainfull_notm}} para desarrollar y probar aplicaciones de ejemplo.
{:shortdesc}

El Supervisor de red expone las pantallas siguientes en tres secciones. Puede ir a cada pantalla desde el navegador izquierdo del supervisor de red.
- La sección **Mi red** contiene las pantallas "[Visión general](#overview)", "[Miembros](#members)", "[Canales](#channels)", "[Notificaciones](#notifications)" y "[API](#apis)".
- La sección **Mi código** contiene las pantallas "[Escribir código](#write_code)", "[Instalar código](#chaincode)" y "[Probar ejemplos](#samples)".
- La pantalla "[Obtener ayuda](#support)".

Puede [cambiar entre las organizaciones](#switch_organizations) de las que es propietario y [restablecer la red](#reset_network) desde el menú desplegable de la parte superior derecha del supervisor de red.

En esta guía de aprendizaje se describe cada una de las pantallas y funciones anteriores.

## Visión general
{: #overview}

En la pantalla "Visión general" se muestra información de estado en tiempo real sobre los recursos de blockchain como el clasificador, la CA y los iguales. Cada recurso se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Cuando se inicia la red, hay un clasificador, una CA y un igual en ejecución. La CA es específica de la organización, mientras que el clasificador es un punto final común que se comparte a través de la red.

En la **Figura 1** se muestra la pantalla "Visión general":

![Pantalla Visión general](images/myresources_starter.png "Visión general de red")
*Figura 1. Visión general de red*

### Acciones de nodo
  La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los componentes. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

  También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros muestran las llamadas entre los distintos recursos de red y resultan muy útiles para la depuración y resolución de problemas. Por ejemplo, si detiene un igual e intenta convertirlo en el destino de una transacción, verá errores de conectividad. Cuando reinicie el igual y vuelva a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el igual, detectará una sincronización del libro mayor a medida que recibe los bloques confirmados mientras estaba inactivo. Cuando se haya sincronizado por completo el libro mayor, podrá realizar invocaciones y consultas normales sobre el mismo.

### Perfil de conexión
  Puede ver en el archivo JSON la información de red de bajo nivel de cada recurso pulsando el botón **Perfil de conexión**. El perfil de conexión contiene toda la información de configuración que necesita para una aplicación. Sin embargo, debido a que este archivo solo contiene las direcciones de sus componentes específicos y del clasificador, si necesita más iguales como destinos, deberá obtener sus puntos finales. La cabecera que contiene "url" muestra el punto final de API de cada componente. Estos puntos finales se necesitan para marcar el destino de componentes de red específicos de una aplicación del cliente y sus definiciones suelen encontrarse en un archivo de configuración de tipo JSON que acompaña a la app. Si está personalizando una aplicación que necesita aprobación de iguales que no forman parte de la organización, deberá recuperar las direcciones IP de esos iguales de los operadores relevantes en una operación externa. Los clientes deben poder conectarse a cualquier igual del que necesiten una respuesta.

### Adición de iguales
{: #peers}
  Los miembros de la red deben tener iguales para almacenar sus copias del libro mayor de la red y para ejecutar código de encadenamiento para consultar o actualizar el libro mayor. Si la política de aprobación define un igual como igual de aprobación, el igual también devuelve resultados de aprobación a las aplicaciones.

  El Plan inicial crea un igual para cada uno de las dos organizaciones de forma predeterminada. Puede añadir más iguales a sus organizaciones, en función de sus propios requisitos. Puede estar en distintos escenarios cuando necesite más iguales. Por ejemplo, es posible que desee que varios iguales se unan al mismo canal por motivos de redundancia. Cada igual procesa las transacciones del canal y escribe en sus respectivas copias del libro mayor. Si uno de los iguales falla, el otro igual (u otros iguales) pueden continuar procesando transacciones y solicitudes de aplicación. También puede equilibrar la carga de forma simétrica de todas las solicitudes de aplicaciones entre los iguales, o puede elegir como destino diferentes iguales para diferentes funciones. Por ejemplo, puede utilizar un igual para consultar el libro mayor y utilizar otro igual para procesar las aprobaciones de actualizaciones del libro mayor.

  Pulse el botón **Añadir iguales** de la parte superior derecha para añadir nodos iguales a la red. En el panel emergente "Añadir iguales", seleccione el número y tamaño de los nodos iguales que desea agregar.


## Miembros
{: #members}

La pantalla "Miembros" contiene dos separadores para visualizar información de miembros de red en el separador "Miembros" y la información de certificado en el separador "Certificados".

### Miembros
{: #members_tab}
En la **Figura 2** se muestra la pantalla "Miembros" inicial con los miembros de su red en el separador "Miembros":

![Separador Miembros de la pantalla Miembros](images/monitor_members_starter.png "Miembros de red")
*Figura 2. Miembros de red*

Pulse **Añadir miembro** para invitar a más miembros a la red. En el Plan inicial, tiene dos opciones:
- **Invitar a un miembro**. Puede invitar a otras organizaciones a que pasen a ser miembros de la red. Luego las organizaciones invitadas pueden unirse y colaborar con usted en la red.
- **Crear miembro**. También puede crear un miembro utilizando su propia dirección de correo electrónico. Tendría el mismo control sobre dicho miembro que el que tiene sobre las dos organizaciones que recibe de forma predeterminada con el Plan inicial.

En la **Figura 3** se muestra la ventana "Añadir miembro".

![Añadir miembro](images/invite_member_starter.png "Añadir miembro")
*Figura 3. Añadir miembro*

### Certificados
En la **Figura 4** se muestra la pantalla inicial "Miembros" con los certificados de los miembros en el separador "Certificados":

![Separador Certificados de la pantalla Miembros](images/monitor_certificates_starter.png "Certificados")
*Figura 4. Certificados*

Los operadores pueden gestionar los certificados de miembros de la misma institución en el separador "Certificados". Pulse **Añadir certificado** para abrir el panel "Añadir certificado". Indique un nombre para el certificado, pegue los certificados del lado de cliente en formato PEM en el campo "Clave" y pulse **Enviar**. Debe reiniciar sus iguales antes de que certificados del lado de cliente entren en vigor.

Para obtener información sobre cómo generar su clave de certificado, consulte [Generación de certificados del lado de cliente](v10_application.html#generating-the-client-side-certificates).


## Canales
{: #channels}

Los canales, consistentes en un subconjunto de miembros de red que desean realizar transacciones privadas, proporcionan aislamiento de datos y confidencialidad permitiendo que los miembros de un canal establezcan normas específicas y un libro mayor separado, al que solo pueden acceder los miembros del canal. Cada red debe tener al menos un canal para que se lleven a cabo las transacciones. Cada canal tiene un libro mayor único y los usuarios deben estar correctamente autenticados para poder realizar operaciones de lectura/escritura en este libro mayor. Si no está en un canal, no podrá ver los datos.

En la **Figura 5** se muestra la pantalla inicial del panel de control con una visión general de todos los canales de la red:

![Canales](images/channels_starter.png "Canales")
*Figura 5. Canales*

La creación de un canal genera un libro mayor específico del canal. Para obtener más información, consulte [Creación de un canal](howto/create_channel.html).

También puede seleccionar un canal existente para ver más detalles precisos sobre el canal, la pertenencia y los códigos de encadenamiento activos. Para obtener más información, consulte [Supervisión de una red](howto/monitor_network.html).


## Notificaciones
{: #notifications}

Puede gestionar solicitudes pendientes y visualizar las solicitudes completadas en la pantalla "Notificaciones".

En la **Figura 6** se muestra la pantalla "Notificaciones":

![Notificaciones](images/notifications_starter.png "Notificaciones")
*Figura 6. Notificaciones*

Cuando cree un canal o se le invite a un nuevo canal, recibirá una notificación en el Supervisor de red.

Las solicitudes se agrupan en los subseparadores "Todas", "Pendientes" y "Completadas". Los números tras la cabecera indican el número de solicitudes de cada subseparador.
   * Puede encontrar todas las solicitudes en el subseparador "Todas".
   * Las solicitudes que no ha aceptado ni rechazado, o que no ha visto, se encuentran en el subseparador "Pendientes". Pulse el botón **Revisar solicitud** para ver la solicitud, que incluye la política de canal y los miembros, y el estado de votación. Si es un operador de canal, puede **Aceptar** o **Rechazar** la solicitud, o bien puede gestionarla en otro momento pulsando **Más tarde**. Si la solicitud es aceptada por suficientes operadores de canal, puede pulsar **Enviar solicitud** para activar la actualización del canal.
   * La solicitud enviada se mostrará en el subseparador "Completadas".  Puede pulsar **Revisar solicitud** para ver sus detalles.

Si tiene una lista larga de solicitudes, puede buscar una en el campo de búsqueda situado en la parte superior.

Para suprimir solicitudes pendientes, seleccione los recuadros situados delante de las mismas y pulse **Suprimir solicitud**.


## API
{: #apis}

Para facilitar el desarrollo de aplicaciones, la plataforma {{site.data.keyword.blockchainfull_notm}} expone las API que puede probar en la red en una IU de Swagger.

En la **Figura 7** se muestra la pantalla "API":

![API](images/API_screen_starter.png "API")
*Figura 7. API*

Pulse el enlace **IU de Swagger** para abrir la IU de Swagger. Tenga en cuenta que debe autorizar la IU de Swagger con sus credenciales de red (que encontrará en esta página de API) para poder ejecutar las API. Para obtener más información, consulte [Utilización de las API de Swagger](howto/swagger_apis.html).


## Escribir código
{: #write-code}

El Plan inicial integra la función de desarrollo de la plataforma {{site.data.keyword.blockchainfull_notm}} y proporciona un entorno de desarrollo con herramientas y tecnologías estándar de la industria. Puede desarrollar su red en el entorno en línea o de forma local. Después de desarrollar una red, puede desplegarla en la red del Plan inicial.

En la **Figura 8** se muestra la pantalla "Escribir código":

![Escribir código](images/write_code_starter.png "Escribir código")
*Figura 8. Escribir código*

Para obtener más información sobre cómo desarrollar y desplegar su código con el Plan inicial, consulte [Desarrollo de redes de empresa con el Plan inicial](develop_starter.html).


## Instalar código
{: #chaincode}

Los códigos de encadenamiento, también conocidos como "contratos digitales", son fragmentos de software que contienen un conjunto de funciones para consultar y actualizar el libro mayor. Se instalan en iguales y se crean instancias de los mismos en un canal.

En la **Figura 9** se muestra la pantalla "Instalar código":

![Instalar código](images/chaincode_install_overview_starter.png "Instalar código")
*Figura 9. Código de encadenamiento*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. Para obtener más información, consulte [Instalación y creación de una instancia de código de encadenamiento](howto/install_instantiate_chaincode.html).


## Probar ejemplos
{: #samples}

Las aplicaciones de ejemplo le ayudan a comprender mejor una red de blockchain y el desarrollo de aplicaciones. El Plan inicial le permite desplegar e iniciar las aplicaciones de ejemplo en el supervisor de red.

En la **Figura 10** se muestra la pantalla "Ejemplos":

![Probar ejemplos](images/sample_overview_starter.png "Probar ejemplos")
*Figura 10. Ejemplos*

El despliegue de ejemplo utiliza el [servicio DevOps Toolchain ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/devops/toolchains) para automatizar el proceso de control de origen, conducto de entrega y habilitación del código de encadenamiento. Elija una aplicación de ejemplo y pulse **Desplegar mediante Toolchain**. Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).


## Obtener ayuda
{: #support}

La pantalla "Obtener ayuda" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

En la **Figura 11** se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support_starter.png "Soporte")
*Figura 11. Soporte de Blockchain*

Utilice los enlaces y recursos de esta pantalla para acceder a los foros sobre resolución de problemas y soporte.

* La [documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html) bajo **Cómo empezar**, que es un sitio de documentación, ofrece una guía sobre cómo comenzar a utilizar la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.Bluemix_notm}}. Encontrará los temas correspondientes en el navegador izquierdo o buscando un término con la función de búsqueda situada en la parte superior.
* [IBM Developer Works ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/blockchain/) en la **Ayuda de la comunidad** contiene recursos e información para desarrolladores.
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} con la opción **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}**.  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/ibm-blockchain) bajo **Aplicaciones de ejemplo de Blockchain** contiene una guía y fragmentos de código de ejemplo que le ayudarán en el desarrollo de aplicaciones.
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/) y la [Comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) en **Hyperledger Fabric** proporcionan más detalles sobre la pila Hyperledger Fabric.
  Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.

Si no consigue depurar el problema ni determinar una respuesta a su pregunta, envíe un caso de soporte en el portal de servicio de IBM Cloud. Para obtener más información, consulte [Obtención de soporte](ibmblockchain_support.html).

En la **Figura 12** se muestra la pantalla inicial "Soporte" con las funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release de helios](images/releasenotes_helios_starter.png "Notas del release de helios")
![Notas del release de Fabric](images/releasenotes_Fabric_starter.png "Notas del release de Fabric")
*Figura 12. Notas del release*


## Cambiar de organización
{: #switch-organizations}

Si decide simular una red de blockchain de varias organizaciones, puede cambiar a cualquiera de las organizaciones que posee, por ejemplo a la Organización A. A continuación, puede ver y gestionar los recursos de red de la Organización A, como los iguales, canales y códigos de encadenamiento, en el supervisor de red. Esta característica le permite crear un canal siguiendo las políticas del canal y añadir al canal iguales procedentes de varias organizaciones.

Pulse la esquina superior derecha de la IU, donde debería ver su nombre. En el menú desplegable que hay bajo **CAMBIAR DE ORGANIZACIÓN**, elija el nombre de la organización a la que desea cambiar. La Organización A está seleccionada de forma predeterminada. Después de seleccionar una organización a la que cambiar, el supervisor de red se renueva automáticamente y puede ver la red como dicha organización.

En la **Figura 13** se muestra la función "Cambiar de organización":

![Cambiar de organización](images/switch_orgs.png "Cambiar de organización")
*Figura 13. Cambiar de organización*


## Restablecer la red
{: #reset-network}

El Plan inicial ofrece la posibilidad de editar la configuración de la red sin suprimir ni volver a crear la red. Se restablece la configuración inicial de la red, que incluye dos organizaciones, un igual por cada organización y un canal predeterminado. Esto resulta útil, por ejemplo, cuando se ejecutan rondas de pruebas en la red de blockchain, ya que le permite volver a empezar desde una red relativamente limpia.

**Atención**: después de restablecer la red, los puntos finales de API de los iguales, el clasificador y la CA se modifican. Debe ajustar la información sobre puntos finales de API en las aplicaciones.

Pulse la esquina superior derecha para abrir el menú desplegable. Pulse el botón **Restablecer red** en el menú. Si está preparado para restablecer la red, pulse **Aceptar** para continuar. El supervisor de red se renovará para reflejar los valores nuevos.

En la **Figura 14** se muestra la función "Cambiar de organización":

![Restablecer red](images/reset_network.png "Restablecer red")
*Figura 14. Restablecer red*

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

# Operación de la red del Plan inicial
{: #operate-starter-plan-network}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


La plataforma {{site.data.keyword.blockchainfull}} incorpora un supervisor de red, que es una GUI que ofrece una visión general del entorno de blockchain, incluidos recursos de red, miembros, canales unidos, datos de rendimiento de las transacciones y códigos de encadenamiento desplegados. El supervisor de red también le ofrece un punto de partida para ejecutar las API de Swagger y desarrollar una red con la función de la plataforma {{site.data.keyword.blockchainfull_notm}} para desarrollar y probar aplicaciones de ejemplo.
{:shortdesc}

Puede [cambiar el nombre de la red del Plan inicial](#sp-network-name) o [conmutar entre distintas redes de Planes iniciales que cree](#switch-sp-network) en el Supervisor de red.

El Supervisor de red expone las pantallas siguientes en tres secciones. Puede ir a cada pantalla desde el navegador izquierdo del supervisor de red.
- La sección **Mi red** contiene las pantallas "[Visión general](#overview)", "[Miembros](#members)", "[Canales](#channels)", "[Notificaciones](#notifications)", "[Entidad emisora de certificados](#ca)" y "[API](#apis)".
- La sección **Mi código** contiene las pantallas "[Desarrollar código](#write-code)", "[Instalar código](#chaincode)" y "[Probar ejemplos](#samples)".
- La pantalla "[Obtener ayuda](#support)".

Puede [comprobar las preferencias de red](#network-preferences), [cambiar de organización](#switch-organizations) de su propiedad y [restablecer la red](#reset-network) desde el menú desplegable de la esquina superior derecha del supervisor de red.

En esta guía de aprendizaje se describe cada una de las pantallas y funciones anteriores.

## Actualizar el nombre de red
{: #sp-network-name}

Al crear una red de Plan inicial, la Plataforma de {{site.data.keyword.blockchainfull_notm}} asigna un nombre a la red. Sin embargo, puede actualizar este nombre de red en cualquier momento en el Supervisor de red.

En la parte superior izquierda del navegador del Supervisor de red, pulse en el nombre de red y el campo se volverá editable. Escriba el nuevo nombre de red que desee utilizar y pulse **Intro**. El nombre de red se actualizará en pocos segundos.

**Figura 1** muestra los pasos para actualizar el nombre de red del Plan inicial del nombre asignado a "Red del plan inicial".

![Actualizar nombre de red](images/update_network_name.gif "Actualizar nombre de red")
*Figura 1. Actualizar nombre de red*


## Conmutar entre redes del Plan inicial
{: #switch-sp-network}

Si crea más de una red con el Plan inicial, puede cambiar entre las redes en el Supervisor de red.

En la parte superior izquierda del navegador del Supervisor de red, pulse el icono de flecha junto a su nombre de red. Seleccione y pulse el nombre de red al que desea cambiar desde la lista desplegable. El navegador web se renovará y abrirá el Supervisor de red de la red a la que desea cambiar.

**Figura 2** muestra los pasos para cambiar a otra red del Plan inicial.

![Cambiar de red](images/switch_network.gif "Cambiar de red")
*Figura 2. Cambiar de red*


## Visión general
{: #overview}

La pantalla "Visión general" contiene dos separadores. En el separador "Visión general" se muestra información de estado en tiempo real sobre los recursos de blockchain como el clasificador, la CA y los iguales. Cada recurso se muestra bajo cuatro cabeceras distintas: **Tipo**, **Nombre**, **Estado** y **Acciones**. Cuando se inicia la red, hay un clasificador, una CA y un igual en ejecución. La CA es específica de la organización, mientras que el clasificador es un punto final común que se comparte a través de la red. El separador "Almacenamiento" muestra el estado de uso de almacenamiento de los recursos de red solicitados y otros recursos de red.

En la **Figura 3** se muestra el separador "Visión general" de la pantalla "Visión general":

![Visión general](images/myresources_starter.png "Visión general")
*Figura 3. Visión general*

### Acciones de nodo
La cabecera **Acciones** de la tabla proporciona botones para iniciar o detener los componentes. También puede iniciar o detener un grupo de nodos mediante la selección de varios nodos para luego pulsar el botón **Iniciar seleccionados** o **Detener seleccionados**. El botón **Iniciar seleccionados** o **Detener seleccionados** aparece en la parte superior de la tabla cuando se seleccionan uno o varios nodos.

También puede comprobar los registros de componente pulsando **Ver registros** en la lista desplegable en cabecera **Acciones**. Los registros muestran las llamadas entre los distintos recursos de red y resultan muy útiles para la depuración y resolución de problemas.

**Nota**: en Starter Network Monitor, cuando se pulsa la acción **Ver registros** en los nodos que se muestran en la pantalla "Visión general", se abre la interfaz de kibana de registro de {{site.data.keyword.cloud_notm}}. De forma predeterminada, kibana estará preconfigurado para mostrar registros de los últimos 15 minutos de actividad. Si no ha habido actividad en los últimos 15 minutos, verá un mensaje que indica 'No results found'. Para ver todos los registros, puede tan solo pulsar el icono de temporizador de la esquina superior derecha bajo el nombre de usuario y establezca un rango de tiempo más amplio, como por ejemplo ‘Esta semana’ o ‘Este mes’.

Para entender los efectos de iniciar y detener un igual, puede experimentar deteniendo un igual e intentando convertirlo en el destino de una transacción, y verá errores de conectividad en los registros. Cuando reinicie el igual y vuelva a intentar la transacción, verá una conexión correcta. También puede dejar un igual inactivo durante un largo periodo de tiempo mientras sus canales siguen realizando transacciones. Cuando se reactive el igual, detectará una sincronización del libro mayor a medida que recibe los bloques confirmados mientras estaba inactivo. Cuando se haya sincronizado por completo el libro mayor, podrá realizar invocaciones y consultas normales sobre el mismo.

### Configuración de igual remoto  

Si despliega un igual remoto fuera de {{site.data.keyword.cloud_notm}}, tiene que proporcionar la información de punto final de API de la red al igual remoto durante la configuración. Pulse el botón ** Configuración de igual remoto** para recuperar la información de punto final de API de la red para configurar el igual remoto. La ventana emergente proporciona la información de punto final de API del ID de red, el MSP de la organización, el nombre de CA, el URL de CA y el certificado de CA TLS. Puede pulsar el icono de copia al final de cada campo para copiar el valor de dicho campo, o bien pulsar el enlace **Descargar** para guardar valores de todos los campos en un archivo JSON. Para obtener más información acerca de los iguales remotos, consulte [Acerca de los iguales remotos](howto/remote_peer.html).

### Perfil de conexión

Puede ver en el archivo JSON la información de red de bajo nivel de cada recurso pulsando el botón **Perfil de conexión**. El perfil de conexión contiene toda la información de configuración que necesita para una aplicación. Sin embargo, debido a que este archivo solo contiene las direcciones de sus componentes específicos y del clasificador, si necesita más iguales como destinos, deberá obtener sus puntos finales. La cabecera que contiene "url" muestra el punto final de API de cada componente. Estos puntos finales se necesitan para marcar el destino de componentes de red específicos de una aplicación del cliente y sus definiciones suelen encontrarse en un archivo de configuración de tipo JSON que acompaña a la app. Si está personalizando una aplicación que necesita aprobación de iguales que no forman parte de la organización, deberá recuperar las direcciones IP de esos iguales de los operadores relevantes en una operación externa. Los clientes deben poder conectarse a cualquier igual del que necesiten una respuesta.

### Adición de iguales
{: #peers}

Los miembros de la red deben tener iguales para almacenar sus copias del libro mayor de la red y para ejecutar código de encadenamiento para consultar o actualizar el libro mayor. Si la política de aprobación define un igual como igual de aprobación, el igual también devuelve resultados de aprobación a las aplicaciones.

El Plan inicial crea un igual para cada uno de las dos organizaciones de forma predeterminada. Puede añadir más iguales a sus organizaciones, en función de sus propios requisitos. Puede estar en distintos escenarios cuando necesite más iguales. Por ejemplo, es posible que desee que varios iguales se unan al mismo canal por motivos de redundancia. Cada igual procesa las transacciones del canal y escribe en sus respectivas copias del libro mayor. Si uno de los iguales falla, el otro igual (u otros iguales) pueden continuar procesando transacciones y solicitudes de aplicación. También puede equilibrar la carga de forma simétrica de todas las solicitudes de aplicaciones entre los iguales, o puede elegir como destino diferentes iguales para diferentes funciones. Por ejemplo, puede utilizar un igual para consultar el libro mayor y utilizar otro igual para procesar las aprobaciones de actualizaciones del libro mayor.

Pulse el botón **Añadir iguales** de la parte superior derecha para añadir nodos iguales a la red. En el panel emergente "Añadir iguales", seleccione el número y tamaño de los nodos iguales que desea agregar.

### Almacenamiento

En la **Figura 4** se muestra el separador "Almacenamiento" que muestra el estado de uso del almacenamiento:

![Separador Almacenamiento de la pantalla Visión general](images/monitor_storage_starter.png "Almacenamiento")
*Figura 4. Almacenamiento*

## Miembros
{: #members}

La pantalla "Miembros" contiene dos separadores para visualizar información de miembros de red en el separador "Miembros" y la información de certificado en el separador "Certificados".

### Miembros
{: #members_tab}
En la **Figura 5** se muestra la pantalla inicial "Miembros" que muestra los miembros de la red en el separador "Miembros":

![Separador Miembros de la pantalla Miembros](images/monitor_members_starter.png "Miembros de red")
*Figura 5. Miembros de la red*

Pulse **Añadir miembro** para invitar a más miembros a la red. En el Plan inicial, tiene dos opciones:
- **Invitar a un miembro**. Puede invitar a otras organizaciones a que pasen a ser miembros de la red. Luego las organizaciones invitadas pueden unirse y colaborar con usted en la red.
- **Crear miembro**. También puede crear un miembro utilizando su propia dirección de correo electrónico. Tendría el mismo control sobre dicho miembro que el que tiene sobre las dos organizaciones que recibe de forma predeterminada con el Plan inicial.

En la **Figura 6** se muestra la ventana "Añadir miembro".

![Añadir miembro](images/invite_member_starter.png "Añadir miembro")
*Figura 6. Añadir miembro*

### Certificados
En la **Figura 7** se muestra la pantalla inicial "Miembros" con los certificados de los miembros en el separador "Certificados":

![Separador Certificados de la pantalla Miembros](images/monitor_certificates_starter.png "Certificados")
*Figura 7. Certificados*

Los operadores pueden gestionar los certificados de miembros de la misma institución en el separador "Certificados". Pulse **Añadir certificado** para abrir el panel "Añadir certificado". Indique un nombre para el certificado, pegue los certificados del lado de cliente en formato PEM en el campo "Clave" y pulse **Enviar**. Debe reiniciar sus iguales antes de que certificados del lado de cliente entren en vigor.

Para obtener información sobre cómo generar su clave de certificado, consulte [Generación de certificados del lado de cliente](v10_application.html#enroll-app).


## Canales
{: #channels}

Los canales, consistentes en un subconjunto de miembros de red que desean realizar transacciones privadas, proporcionan aislamiento de datos y confidencialidad permitiendo que los miembros de un canal establezcan normas específicas y un libro mayor separado, al que solo pueden acceder los miembros del canal. Cada red debe tener al menos un canal para que se lleven a cabo las transacciones. Cada canal tiene un libro mayor único y los usuarios deben estar correctamente autenticados para poder realizar operaciones de lectura/escritura en este libro mayor. Si no está en un canal, no podrá ver los datos.

En la **Figura 8** se muestra la pantalla de panel de control inicial que muestra una visión general de todos los canales de la red:

![Canales](images/channels_starter.png "Canales")
*Figura 8. Canales*

La creación de un canal genera un libro mayor específico del canal. Para obtener más información, consulte [Creación de un canal](howto/create_channel.html).

También puede seleccionar un canal existente para ver detalles más precisos sobre el canal, la pertenencia y el código de encadenamiento activo. Para obtener más información, consulte [Supervisión de una red](howto/monitor_network.html).


## Notificaciones
{: #notifications}

Puede gestionar solicitudes pendientes y visualizar las solicitudes completadas en la pantalla "Notificaciones".

En la **Figura 9**, se muestra la pantalla "Notificaciones":

![Notificaciones](images/notifications_starter.png "Notificaciones")
*Figura 9. Notificaciones*

Cuando cree un canal o se le invite a un nuevo canal, recibirá una notificación en el Supervisor de red.

Las solicitudes se agrupan en los subseparadores "Todas", "Pendientes" y "Completadas". Los números tras la cabecera indican el número de solicitudes de cada subseparador.
   * Puede encontrar todas las solicitudes en el subseparador "Todas".
   * Las solicitudes que no ha aceptado ni rechazado, o que no ha visto, se encuentran en el subseparador "Pendientes". Pulse el botón **Revisar solicitud** para ver la solicitud, que incluye la política de canal y los miembros, y el estado de votación. Si es un operador de canal, puede **Aceptar** o **Rechazar** la solicitud, o bien puede gestionarla en otro momento pulsando **Más tarde**. Si la solicitud es aceptada por suficientes operadores de canal, puede pulsar **Enviar solicitud** para activar la actualización del canal.
   * La solicitud enviada se mostrará en el subseparador "Completadas".  Puede pulsar **Revisar solicitud** para ver sus detalles.

Si tiene una lista larga de solicitudes, puede buscar una en el campo de búsqueda situado en la parte superior.

Para suprimir solicitudes pendientes, seleccione los recuadros situados delante de las mismas y pulse **Suprimir solicitud**.


## Entidad
emisora de certificados
{: #ca}

La tabla de la pantalla "Entidad emisora de certificados" (CA) muestra todas las identidades que se han registrado con su organización, incluidas las aplicaciones de administrador, de iguales y de cliente. También puede utilizar esta pantalla para registrar una nueva identidad.

En la **Figura 10** se muestra la pantalla "Entidad emisora de certificados":

![Entidad emisora de certificados](images/CA_screen_starter.png "Entidad emisora de certificados")
*Figura 10. Entidad emisora de certificados*

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

La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece varias API REST en Swagger que puede utilizar para gestionar los nodos, canales, iguales y miembros de la red. Las aplicaciones pueden utilizar estas API para controlar los recursos de red importantes sin utilizar el Supervisor de red.

En la **Figura 11** se muestra la pantalla "API":

![API](images/API_screen_starter.png "API")
*Figura 11. API*

Pulse el enlace **IU de Swagger** para abrir la IU de Swagger. Tenga en cuenta que debe autorizar la IU de Swagger con sus credenciales de red (que encontrará en esta página de API) para poder ejecutar las API. Para obtener más información, consulte [Utilización de las API de Swagger](howto/swagger_apis.html).


## Desarrollar código
{: #write-code}

El Plan inicial integra la función de desarrollo de la plataforma {{site.data.keyword.blockchainfull_notm}} y proporciona un entorno de desarrollo con herramientas y tecnologías estándar de la industria. Puede desarrollar su red empresarial en el entorno en línea o de forma local. Después de desarrollar una red empresarial, puede desplegarla de nuevo en la red del Plan inicial.

En la **Figura 12** se muestra la pantalla "Desarrollar código":

![Desarrollar código](images/write_code_starter.png "Desarrollar código")
*Figura 12. Desarrollar código*

Para obtener más información sobre el desarrollo de las redes empresarial y su despliegue de nuevo en la red del Plan inicial, consulte [Desarrollo de redes de empresa con el Plan inicial](develop_starter.html).


## Instalar código
{: #chaincode}

El código de encadenamiento, que también se conoce como "contrato inteligente", son fragmentos de software que contienen un conjunto de funciones para consultar y actualizar el libro mayor. Se instalan en iguales y se crean instancias de los mismos en un canal.

En la **Figura 13** se muestra la pantalla "Instalar código":

![Instalar código](images/chaincode_install_overview_starter.png "Instalar código")
*Figura 13. Código de encadenamiento*

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html).


## Probar ejemplos
{: #samples}

Las aplicaciones de ejemplo le ayudan a comprender mejor una red blockchain y el desarrollo de aplicaciones.  <!--Starter Plan enables you to deploy and launch sample applications in the Network Monitor. --> Siga los enlaces **Ver en GitHub** para obtener más información sobre cómo utilizar las muestras y desplegarlas en la plataforma de {{site.data.keyword.blockchainfull_notm}}. Para obtener más información sobre cómo desarrollar y desplegar los ejemplos, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).

En la **Figura 14** se muestra la pantalla "Probar ejemplos":

![Probar ejemplos](images/sample_overview_starter.png "Probar ejemplos")
*Figura 14. Ejemplos*

<!--
The sample deployment leverages the [DevOps Toolchain service ![External link icon](images/external_link.svg "External link icon")](https://console.bluemix.net/devops/toolchains) to automate your process of source control, delivery pipeline, and chaincode enablement. Choose a sample application, and click the **Deploy via Toolchain**. For more information, see [Deploying sample applications](howto/prebuilt_samples.html).
-->


## Obtener ayuda
{: #support}

La pantalla "Obtener ayuda" contiene dos separadores que ofrecen información de soporte dentro del separador "Soporte" y en el separador "Notas del release" se describen las funciones nuevas y modificadas de cada release.

En la **Figura 15** se muestra la pantalla "Soporte" inicial con la información de soporte en el separador "Soporte":

![Soporte](images/support_starter.png "Soporte")
*Figura 15. Soporte de Blockchain*

### Foros de soporte y recursos de Blockchain
{: #support-forums}

Utilice los enlaces y recursos de esta pantalla para acceder a los foros sobre resolución de problemas y soporte.

* La [documentación del servicio {{site.data.keyword.blockchainfull_notm}}](index.html) bajo **Cómo empezar**, que es un sitio de documentación, ofrece una guía sobre cómo comenzar a utilizar la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.Bluemix_notm}}. Encontrará los temas correspondientes en el navegador izquierdo o buscando un término con la función de búsqueda situada en la parte superior.
* El [Código de IBM ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/code/technologies/blockchain/) contiene patrones de código e información para los desarrolladores.
* [IBM dWAnswers ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/answers/smartspace/blockchain/) en **Incidencia de soporte** sirve como plataforma para preguntas y respuestas. Puede buscar respuestas de preguntas realizadas anteriormente o puede enviar una pregunta nueva. Asegúrese de incluir la palabra clave **blockchain** en la pregunta.
  También puede enviar una incidencia al equipo de soporte de {{site.data.keyword.blockchainfull_notm}} con la opción **Abrir una incidencia de soporte de {{site.data.keyword.Bluemix_notm}}**.  Comparta detalles y fragmentos de código desde la instancia de {{site.data.keyword.Bluemix_notm}} específica.
* [Aplicaciones de ejemplo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/ibm-blockchain) bajo **Aplicaciones de ejemplo de Blockchain** contiene una guía y fragmentos de código de ejemplo que le ayudarán en el desarrollo de aplicaciones.
* [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/) y la [comunidad de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) bajo **Hyperledger Fabric** contienen más detalles sobre la pila Hyperledger Fabric. Converse con un [experto en Hyperledger ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y realice preguntas sobre el código de Hyperledger Fabric.
* [Plataforma {{site.data.keyword.blockchainfull_notm}}: desarrollo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://ibm-blockchain.github.io/develop/) bajo **Hyperledger Composer** contiene más detalles sobre Hyperledger Composer, es decir, Plataforma {{site.data.keyword.blockchainfull_notm}}: desarrollo. También puede encontrar respuestas o formular preguntas en la [comunidad de Hyperledger Composer ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://chat.hyperledger.org/channel/general) y en [StackOverflow ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://stackoverflow.com/questions/tagged/hyperledger-composer).

Si no consigue depurar el problema ni determinar una respuesta a su pregunta, envíe un caso de soporte en el portal de servicio de {{site.data.keyword.cloud_notm}}. Para obtener más información, consulte [Obtención de soporte](ibmblockchain_support.html).

En la **Figura 16** y en la **Figura 17** se muestra la pantalla inicial "Soporte" que muestra funciones nuevas y modificadas de cada release en el separador "Notas del release":

![Notas del release de helios](images/releasenotes_helios_starter.png "Notas del release de helios")
*Figura 16. Notas del release de Helios

![Notas del release de Fabric](images/releasenotes_Fabric_starter.png "Notas del release de Fabric")
*Figura 17. Notas del release de Fabric*


## Preferencias de red
{: #network-preferences}

Pulse la esquina superior derecha y abra el menú desplegable y luego el botón **Preferencias de red** en el menú. Se abre la ventana Preferencias de red. La ventana Preferencias de red muestra la información básica de la red, como por ejemplo el nombre de red, la versión de Fabric, la ubicación de la red en {{site.data.keyword.cloud_notm}} y el tipo de base de datos de estado. Si usted es el iniciador de la red, también puede gestionar el tiempo de espera de inactividad web en la ventana Preferencias de la red.

### Tiempo de espera de inactividad web
{: #web-inactivity-timeout}

**Nota**: Solo el **iniciador de red** puede cambiar el valor de tiempo de espera de inactividad web. Este es un valor de nivel de red y afectará a todos los miembros de la red.

El tiempo de espera de inactividad web está establecido en **Desactivado** de forma predeterminada. Si coloca el tiempo de espera de inactividad web en **Activado**, cualquier miembro de la red será desconectado automáticamente tras 10 minutos de inactividad. Cuando el temporizador de inactividad web llegue a los 10 minutos, la función de tiempo de espera de inactividad web terminará las sesiones web inactivas para garantizar la seguridad de la cuenta del miembro de la red. Al pulsar un enlace o al renovar el Supervisor de red se restablece el temporizador de inactividad web. Antes de llegar a 10 minutos, el cierre de la ventana o del separador del navegador también termina la sesión web.

En la **Figura 18** se muestra la ventana "Preferencias de la red":

![Preferencias de la red](images/network_preferences.gif "Preferencias de la red")
*Figura 18. Preferencias de la red*


## Cambiar de organización
{: #switch-organizations}

Si decide simular una red blockchain de varias organizaciones, puede cambiar a cualquiera de las organizaciones que posee, por ejemplo a la Organización A. A continuación, puede ver y gestionar los recursos de red de la Organización A, como los iguales, canales y códigos de encadenamiento, en el supervisor de red. Esta característica le permite crear un canal siguiendo las políticas del canal y añadir al canal iguales procedentes de varias organizaciones.

Pulse la esquina superior derecha de la IU, donde debería ver su nombre. En el menú desplegable que hay bajo **CAMBIAR DE ORGANIZACIÓN**, elija el nombre de la organización a la que desea cambiar. La Organización A está seleccionada de forma predeterminada. Después de seleccionar una organización a la que cambiar, el supervisor de red se renueva automáticamente y puede ver la red como dicha organización.

En la **Figura 19** se muestra la función "Cambiar de organización":

![Cambiar de organización](images/switch_orgs_starter.gif "Cambiar de organización")   
*Figura 19. Cambiar de organización*


## Restablecer la red
{: #reset-network}

El Plan inicial ofrece la posibilidad de editar la configuración de la red sin suprimir ni volver a crear la red. Se restablece la configuración inicial de la red, que incluye dos organizaciones, un igual por cada organización y un canal predeterminado. Esto resulta útil, por ejemplo, cuando se ejecutan rondas de pruebas en la red blockchain, ya que le permite volver a empezar desde una red relativamente limpia.

**Atención**: después de restablecer la red, los puntos finales de API de los iguales, el clasificador y la CA se modifican. Debe ajustar la información sobre puntos finales de API en las aplicaciones.

Pulse la esquina superior derecha para abrir el menú desplegable. Pulse el botón **Restablecer red** en el menú. Si está preparado para restablecer la red, pulse **Aceptar** para continuar. El supervisor de red se renovará para reflejar los valores nuevos.

En la **Figura 20** se muestra la función "Restablecer red":

![Restablecer red](images/reset_network.png "Restablecer red")
*Figura 20. Restablecer red*

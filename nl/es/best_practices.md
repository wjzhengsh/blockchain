---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Prácticas recomendadas para el desarrollo de aplicaciones
{: #best-practices-app}

Esta guía está destinada a usuarios que ya conocen los aspectos básicos del desarrollo de aplicaciones y están preparados para escalar su solución. Siga estas prácticas recomendadas para maximizar el rendimiento de su red y evitar tiempo de inactividad de las aplicaciones.
{:shortdesc}

## Conectividad y disponibilidad de las aplicaciones
{: #best-practices-app-connectivity-availability}

El [flujo de transacciones ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "flujo de transacciones"){:new_window} de Hyperledger Fabric abarca varios componentes, entre los que las aplicaciones cliente juegan un rol exclusivo. El SDK envía las propuestas de transacción a los iguales para su aprobación. Luego recopila las propuestas aprobadas que se van a enviar al servicio de ordenación, que a continuación envía bloques de transacciones a los iguales para que se añadan a los libros mayores de canal. Los desarrolladores de aplicaciones de producción deben estar preparados para gestionar las interacciones entre el SDK y sus redes para aumentar su eficiencia y disponibilidad.

### Gestión de transacciones
{: #best-practices-app-managing-transactions}

Los clientes de aplicaciones deben asegurarse de que sus propuestas de transacción se validan y de que las propuestas se completan correctamente. Una propuesta se puede retrasar o perder por varios motivos, como una interrupción de la red o una anomalía de un componente. Debe preparar el código para [alta disponibilidad](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) para que pueda gestionar una anomalía en un componente. También puede [aumentar los valores de tiempo de espera](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk) en la aplicación para evitar que las propuestas excedan el tiempo de espera antes de que la red pueda responder.

Si un código de encadenamiento no se está ejecutando, la primera propuesta que se envía a dicho código de encadenamiento lo inicia. Mientras se inicia el código de encadenamiento, todas las demás propuestas se rechazan con un error que indica que el código de encadenamiento se está iniciando. Esto difiere de la invalidación de la transacción. Si una propuesta se rechaza mientras se está iniciando el código de encadenamiento, los clientes de la aplicación deben volver a enviar las propuestas rechazadas después de que se inicie el código de encadenamiento. Los clientes de la aplicación pueden utilizar una cola de mensajes para evitar que se pierdan las propuestas de transacción.

Puede utilizar un servicio de sucesos basado en canal para supervisar las transacciones y crear colas de mensajes. La clase [channelEventHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} puede registrar escuchas basados en transacción, bloque o sucesos de código de encadenamiento. Los escuchas basados en canal del concentrador de sucesos (eventhub) de canal pueden escalarse a varios canales y distinguir entre el tráfico de los distintos canales.

Se recomienda que utilice channelEventHub, en lugar de la antigua clase EventHub. EventHub es de una sola hebra y contiene sucesos de todos los canales que podrían ralentizar o incluso colgar los escuchas en los canales. La clase eventHub tampoco proporciona ninguna garantía de que se entregue un suceso, ni tampoco una manera de recuperar sucesos desde un punto determinado, como un número de bloque, para realizar el seguimiento de sucesos que se hayan perdido.

**Nota:** la clase EventHub del igual quedará en desuso en un release posterior del SDK de Fabric. Si tiene aplicaciones existentes que utilicen el EventHub de igual, actualice las aplicaciones para que en su lugar utilicen el EventHub de canal. Para obtener más información, consulte [Cómo utilizar el servicio de sucesos basados en canal ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Cómo utilizar el servicio de sucesos basados en canal"){:new_window} en la documentación del SDK de Node.

### Apertura y cierre de conexiones de red
{: #best-practices-app-connections}

Al crear objetos de igual o clasificador con el SDK antes de enviar propuestas de transacción, abrirá una conexión gRPC entre la aplicación y el componente de red. Por ejemplo, el mandato siguiente abre una conexión con `org1-peer1`. Esta conexión sigue activa mientras se ejecute la aplicación.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

Al gestionar las conexiones entre la aplicación y la red, puede tener en cuenta las recomendaciones siguientes.

- Reutilice los objetos de igual y clasificador cuando interactúe con la red, en lugar de abrir nuevas conexiones para enviar transacciones. La reutilización de objetos de igual y clasificador puede ahorrar recursos y mejorar el rendimiento.  
- Para mantener una conexión persistente con los componentes de red, utilice [estados activos de gRPC ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "Estados activos de gRPC"). Los estados activos (keepalives) mantienen activa la conexión gRPC y evitan que se cierre una conexión "no utilizada". El ejemplo siguiente de conexión de igual añade opciones de gRPC al objeto de [opciones de conexión ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Conexión"). Las opciones de gRPC están establecidas en los valores que recomienda la plataforma {{site.data.keyword.blockchainfull_notm}}.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  También puede encontrar estas variables con los valores recomendados en la sección `"peers"` del perfil de conexión de la red. Las opciones recomendadas se importarán en la aplicación de forma automática si utiliza el [perfil de conexión con el SDK](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile) para conectarse a los puntos finales de red.

- Si una conexión deja de ser necesaria, utilice los mandatos `peer.close()` y `orderer.close()` para liberar recursos y evitar que se degrade el rendimiento. Para obtener más información, consulte las clases [peer close ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") y [orderer close![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") en la documentación del SDK de Node. Si ha utilizado un perfil de conexión para añadir iguales y clasificadores a un objeto de canal, puede cerrar todas las conexiones que estén asignadas a dicho canal utilizando un mandato `channel.close()`.

### Aplicaciones de alta disponibilidad
{: #best-practices-app-ha-app}

Como práctica recomendada de alta disponibilidad, es muy recomendable desplegar un mínimo de dos iguales por organización para la migración tras error. También necesita adaptar sus aplicaciones a la alta disponibilidad. Instale el código de encadenamiento en ambos iguales y añádalos a los canales. Luego prepárese para enviar propuestas de transacciones a los puntos finales de ambos iguales cuando configure la red y cree su lista de destinos de iguales. Las redes del Plan empresarial tienen varios clasificadores para la migración tras error, lo que permite que la aplicación cliente pueda enviar transacciones aprobadas a un clasificador distinto si uno no está disponible. Si utiliza el [perfil de conexión](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) en su lugar para añadir puntos finales de red manualmente, asegúrese de que el perfil esté actualizado y de que los iguales y clasificadores adicionales se hayan añadido al canal correspondiente en la sección `channels` del perfil. A continuación, el SDK puede añadir los componentes que se han unido al canal utilizando el perfil de conexión.

## Habilitación de TLS mutuo
{: #best-practices-app-mutual-tls}

Si ejecuta redes del plan empresarial que estén al nivel de Fabric V1.1, tiene la opción de [habilitar TLS mutuo](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) para las aplicaciones. Si habilita TLS mutuo, debe actualizar las aplicaciones para dar soporte a esta función. De lo contrario, las aplicaciones no podrán comunicarse con la red.

En el Perfil de conexión, localice la sección `certificateAuthorities`, donde encontrará los siguientes atributos que son necesarios para inscribir y obtener los certificados para comunicarse con la red utilizando TLS mutuo.

- `url`: URL para conectarse a la CA que puede proporcionar certificados de TLS mutuo
- `enrollId`: ID de inscripción a utilizar para obtener un certificado
- `enrollSecret`: Secreto de inscripción a utilizar para obtener un certificado
- `x-tlsCAName`: Nombre de la CA a utilizar para obtener el certificado que permitirá que la aplicación se comunique con TLS mutuo.

Para obtener más información sobre cómo actualizar aplicaciones para que den soporte a TLS mutuo, consulte [Cómo configurar TLS mutuo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls mutuo"){:new_window}.


## (Opcional) Establecimiento de valores de tiempo de espera en los SDK de Fabric
{: #best-practices-app-set-timeout-in-sdk}

Los SDK de Fabric establecen valores de tiempo de espera predeterminados en aplicaciones cliente para los sucesos de la red blockchain. Consulte el ejemplo siguiente sobre los valores de tiempo de espera excedido predeterminados en el SDK de Java de Fabric. La vía de acceso de archivo es `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. For internal cleanup only.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

Sin embargo, es posible que tenga que cambiar los valores predeterminados de tiempo de espera en su propia aplicación. Por ejemplo, cuando la aplicación invoca una transacción que necesita más de 5000 ms, que es el valor de tiempo de espera predeterminado para que la conexión de concentrador de sucesos responda, es posible que reciba un error porque el suceso de invocación termine en 5000 ms antes de que se complete la transacción. Puede establecer la propiedad del sistema para sobrescribir los valores predeterminados de la aplicación cliente. Dado que los valores predeterminados se han inicializado antes de establecer la propiedad de sistema, esta no tendrá efecto. Por lo tanto, debe establecer la propiedad del sistema para tiempo de espera excedido en una construcción estática en la aplicación cliente. Consulte el ejemplo siguiente sobre cómo cambiar el valor de tiempo de espera para la conexión del concentrador de sucesos a 15000 ms en el SDK de Java de Fabric. La vía de acceso de archivo es `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}

Si está utilizando el SDK de nodo, puede especificar los valores de tiempo de espera directamente en el método llamado. Como ejemplo, podría utilizar la línea siguiente para aumentar el valor de tiempo de espera para [crear una instancia de un código de encadenamiento ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") a 5 minutos.
```
channel.sendInstantiateProposal(request, 300000);
```
{:codeblock}

## Prácticas recomendadas al utilizar CouchDB
{: #best-practices-app-couchdb-indices}

Si utiliza CouchDB como base de datos de estado, puede realizar consultas de datos JSON desde su código de encadenamiento sobre los datos de estado del canal. Se recomienda encarecidamente crear índices para las consultas de JSON y utilizarlos en el código de encadenamiento. Los índices permiten que sus aplicaciones puedan recuperar datos de forma eficiente cuando la red añade bloques adicionales de transacciones y entradas en el escenario mundial.

Para obtener más información sobre CouchDB y cómo configurar índices, consulte
[CouchDB como base de datos de estado ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como base de datos de estado"){:new_window} en la documentación de Hyperledger Fabric. También encontrará un ejemplo que utiliza un índice con código de encadenamiento en la [guía de aprendizaje de CouchDB de Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html).

Evite el uso del código de encadenamiento en consultas que vayan a resultar en una exploración de toda la base de datos CouchDB. Las exploraciones de la base de datos de longitud completa tendrán tiempos de respuesta largos y degradarán el rendimiento de la red. Puede realizar algunos de los pasos siguientes para evitar y gestionar consultas largas:
- Configure índices con el código de encadenamiento.
- Todos los campos del índice también deben estar en el selector o clasifique las secciones de su consulta para el índice que se va a utilizar.
- Las consultas más complejas tendrán un rendimiento inferior y será menos probable que utilicen un índice.
- Debería intentar evitar los operadores que den como resultado una exploración de tabla completa o una exploración de índice completa, como `$or`, `$in` y `$regex`.

Puede encontrar ejemplos que muestran cómo las consultas utilizan los índices y qué tipo de consultas tendrá el mejor rendimiento en la [guía de aprendizaje de CouchDB de Fabric ![Icono de enlace externo](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

Los iguales de {{site.data.keyword.blockchainfull_notm}} Platform tienen un conjunto queryLimit establecido y solo devolverán 10.000 entradas de la base de datos de estado. Si la consulta alcanza el queryLimit, puede utilizar varias consultas para obtener los resultados restantes. Si necesita más resultados de una consulta de rango, inicie las consultas posteriores con la última clave devuelta por la consulta anterior. Si necesita más resultados de las consultas JSON, ordene la consulta utilizando una de las variables de los datos y luego utilice el último valor de la consulta anterior en un filtro 'mayor que' para la siguiente consulta.

No consulte la base de datos completa con fines de agregación o de elaboración de informes. Si desea crear un panel de control o recopilar gran cantidad de datos como parte de la aplicación, puede consultar una base de datos fuera de cadena que replique los datos de la red blockchain. Esto le permitirá entender los datos de blockchain sin que se degrade el rendimiento de la red ni se interrumpan las transacciones.

Puede utilizar el cliente de servicios de sucesos basados en canal que proporciona el SDK de Fabric para crear un almacén de datos fuera de cadena. Por ejemplo, puede utilizar un escucha de bloques para obtener las transacciones más recientes que se añaden a un libro mayor de canal. Los conjuntos de lectura y escritura procedentes de las transacciones válidas se pueden utilizar para actualizar una copia del escenario mundial que se haya almacenado en una base de datos independiente. Para obtener más información, consulte [Cómo utilizar el servicio de sucesos basados en canal ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Cómo utilizar el servicio de sucesos basados en canal"){:new_window} en la documentación del SDK de Node.

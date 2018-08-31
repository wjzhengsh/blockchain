---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Desarrollo de aplicaciones
{: #dev_app}
Con las aplicaciones, puede invocar el código de encadenamiento para consultar o actualizar un libro mayor específico del canal en la red blockchain.
{:shortdesc}

## Configuración del entorno de desarrollo de aplicaciones
Debe instalar los requisitos previos de software para desarrollar aplicaciones que puedan interactuar con la red blockchain de {{site.data.keyword.cloud}}.
{:shortdesc}

*	Git ([Página de descargas de Git ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://git-scm.com/downloads){:new_window})  
	Git es una herramienta de control de versiones con la que se puede familiarizar tanto para el desarrollo de código de encadenamiento como para el desarrollo de software en general. Además, git bash, que se instala junto git en Windows, es una excelente alternativa al indicador de mandatos de Windows.

*	GoLang ([Página de descargas de GoLang ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/dl){:new_window})  
	La instalación de GoLang instala un conjunto de herramientas de CLI de GO, que son muy útiles para escribir código de encadenamiento. Por ejemplo, el mandato `go build` le permite verificar realmente el código de encadenamiento antes de intentar desplegarlo en una red.

	Siga las [instrucciones de instalación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/doc/install){:new_window} para definir correctamente las variables de entorno. Compruebe `GOPATH` con el mandato siguiente. Tenga en cuenta que no es necesario que `GOPATH` coincida con el ejemplo, solo importa que tenga esta variable definida en un directorio válido del sistema de archivos.

	```
	$ echo $GOPATH
C:\gopath
	```
	{:codeblock}

	A continuación, puede verificar la instalación de GoLang compilando el código de GoLang con el ejemplo [hello world ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/doc/install#testing){:new_window}.

* Java ([Página de descargas de Java ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://java.com/en/download/){:new_window}).
*	Node.js ([Página de descargas de Node.js ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://nodejs.org/en/download/){:new_window}).

El desarrollo del código de encadenamiento está soportado actualmente en Node.js, Go y Java.

## Generación de certificados del lado de cliente
No ahondaremos en las minucias de x509 y la infraestructura de clave pública; hay muchos recursos externos para ello. Basta decir que los flujos de comunicación en Fabric utilizan operaciones sign/verify en cada punto de encuentro. De este modo, cualquier cliente que envíe llamadas (es decir, consultas o actualizaciones de libro mayor) a la red deberá firmar cargas útiles y adjuntar un certificado x509 firmado correctamente para fines de verificación. La clave privada y el certificado firmado junto con un identificador de MSP y el certificado raíz de la entidad emisora de certificados (CA) conforman lo que se conoce como objeto de "contexto de usuario".

Lo que es importante aquí es el proceso de "inscripción", en el que las claves y los certificados que permiten que se forme el objeto se recuperen de la entidad emisora de certificados correspondiente. Después de formar el objeto de contexto de usuario, puede llamar a una API desde la aplicación para "establecer" u "obtener" este contexto de usuario. En este punto, la aplicación (es decir, el cliente) está equipada con todos los artefactos necesarios y está lista para comunicarse con la red. Veremos dos enfoques de recuperación de claves y certificados: línea de mandatos y SDK.

### Línea de mandatos
Utilizar la línea de mandatos es lo más sencillo de los dos enfoques.

1. Siga las instrucciones para crear el [cliente Fabric CA ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Este paso permite establecer comunicación con un servidor CA y recibir claves y certificados con el formato adecuado.

2. Descargue los certificados TLS de {{site.data.keyword.cloud_notm}} dependiendo del plan de servicio, la ubicación y el clúster que utilice.
  - Certificado TLS raíz para el Plan inicial  
    - EEUU: [us01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sídney: [aus01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Certificado TLS raíz para el Plan empresarial ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Guarde el contenido en una carpeta, por ejemplo ``$HOME/tls``. Este paso permite que se cifre el flujo de datos en la conexión.

3. Abra el archivo JSON de **Perfil de conexión** desde la pantalla **Visión general** del Supervisor de red y busque las variables siguientes:
  * URL para la CA: `url` en `certificateAuthorities`
  * ID de usuario administrador: ``enrollId``
  * Contraseña de administración: ``enrollSecret``
  * Nombre de la CA: ``caName``

4. Mediante el cliente Fabric CA, podemos enviar una llamada de "enroll" a nuestra entidad emisora de certificados pasando los certificados TLS y las cuatro series anteriores con el mandato siguiente:
  ```
  $GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  El binario ``fabric-ca-client`` se sitúa en ``$GOPATH/bin``, por lo que deberá encontrarse en la ubicación adecuada de la máquina local cuando ejecute este mandato.  Una llamada real es similar al siguiente mandato de ejemplo:
  ```
  ./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
  ```
  {:codeblock}

5. Busque el certificado de administración en `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red.

  También puede encontrar el certificado raíz de la CA y la clave privada de administración en los directorios siguientes:
  * Certificado raíz de la CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Clave privada de administración: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Existen algunos repositorios de Fabric que contienen excelentes recursos y scripts para comprender cómo interactuar mediante programación con una entidad emisora de certificados. El repositorio ``fabric-samples`` contiene el ejemplo ``balance transfer`` y el repositorio ``fabric-sdk-node`` dispone de una serie de pruebas de servicios de CA. Si tiene previsto emitir sus solicitudes de inscripción en el lado de la aplicación, debe comprender en profundidad las API que se deben exponer en los paquetes ``fabric-ca-client`` y ``fabric-client``. Utilice estos scripts y repositorios como base para estructurar la app.

Veamos algunos fragmentos clave del ejemplo `balance transfer`:

En primer lugar, tenemos que crear nuestro objeto de cliente y establecer una instancia de almacén clave-valor donde se almacenarán nuestros certificados y claves. Para ello, utilizaremos un método de fábrica sencillo, ``newCryptoSuite``, que amplía la clase ``Client`` de ``BaseClient``. A continuación, se muestra el código:

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```
{:codeblock}

La práctica habitual sería exportar una variable de entorno que defina la vía de acceso clave/valor en la máquina y pasarla a la función anterior. Ahora que hemos definido nuestra KVS, vamos a utilizar algunos métodos de la clase ``FabricCAServices``. Esta clase es una implementación del cliente Fabric CA y, por lo tanto, habilitará la comunicación con el servidor CA. Primero debemos pasar información a nuestro cliente CA, concretamente el URL de la CA:

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```
{:codeblock}

A continuación, enviaremos la llamada "enroll" al servidor CA. Esta acción devolverá al cliente una clave privada y una clave pública envueltas en un certificado x509 y firmadas por la CA; lo denominamos signcert o certificado de inscripción. Este certificado de inscripción o "eCert" es la pieza clave, porque permite que las entidades de red verifiquen transacciones y llamadas provenientes del cliente:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```
{:codeblock}

Y la tarea final consiste en establecer la suite de cifrado y crear el contexto de usuario:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```
{:codeblock}

Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red.

## Desarrollo de aplicaciones
{: #developing-applications}

Una manera fácil de empezar a desarrollar aplicaciones es utilizar código de encadenamiento y aplicaciones de ejemplo como plantilla para crear su propia solución empresarial. Puede encontrar aplicaciones de ejemplo para las redes de blockchain en {{site.data.keyword.cloud_notm}} en [Aplicaciones de ejemplo](howto/prebuilt_samples.html). También puede empezar a desarrollar aplicaciones desde cero basándose en sus propias necesidades empresariales.

Puede desarrollar la aplicación en JavaScript o Java y aprovechar las API disponibles en los SDK de cliente de Hyperledger Fabric para habilitar la interacción entre la aplicación y la red.  Una aplicación debe incluir al menos la información siguiente:
* Nombre y versión del código de encadenamiento que se va a invocar.
* Información de punto final de API de los recursos de red, incluidos clasificadores, CA e iguales.
* Funciones para consultar o actualizar el libro mayor en la red.  Si desea alta disponibilidad, debe considerar la migración tras error de nodos en la aplicación.

### Uso de SDK de Fabric
{: #use-sdks}

Fabric ofrece actualmente SDK de Node.js y de Java y dará soporte a más lenguajes de programación, como Python, REST o GO, en releases futuros. Para obtener más información sobre los SDK de Fabric y cómo utilizarlos, consulte la documentación siguiente:

- [Documentación de SDK de nodo de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/){:new_window}
- [Documentación de SDK de Java de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric-sdk-java){:new_window}

### Establecimiento de valores de tiempo de espera en los SDK de Fabric
{: #set-timeout-in-sdk}

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

Sin embargo, es posible que tenga que cambiar los valores predeterminados de tiempo de espera en su propia aplicación. Por ejemplo, cuando la aplicación invoca una transacción que necesita más de 5000 ms, que es el valor de tiempo de espera predeterminado para la conexión de concentrador de sucesos, para responder, obtendrá un error porque el suceso de invocación termina en 5000 ms antes de que se complete la transacción. Puede establecer la propiedad del sistema para sobrescribir los valores predeterminados de la aplicación cliente. Dado que los valores predeterminados se han inicializado antes de establecer la propiedad de sistema, esta no tendrá efecto. Por lo tanto, debe establecer la propiedad del sistema para tiempo de espera excedido en una construcción estática en la aplicación cliente. Consulte el ejemplo siguiente sobre cómo cambiar el valor de tiempo de espera para la conexión del concentrador de sucesos a 15000 ms en el SDK de Java de Fabric. La vía de acceso de archivo es `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty(EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE);
 }
```
{:codeblock}


## Adición de puntos finales de API a la aplicación
Debe añadir puntos finales de API de los recursos de red a la aplicación de modo que pueda interactuar con la red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix_short}}.  Si no dispone de una red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix_short}}, puede crear una con el Plan inicial o con el Plan empresarial. Para obtener más información, consulte [Gobierno de la red del Plan inicial](get_start_starter_plan.html) y [Gobierno de la red del Plan empresarial](get_start.html).

Encontrará los puntos finales de API en el perfil de conexión de su red. El perfil de conexión está en formato JSON y contiene la información de punto final de API y los ID de inscripción/secretos de los recursos de red, incluidos clasificadores, CA y nodos iguales. La aplicación interactuará con sus iguales y otros recursos de red a través de estos puntos finales de API.

1. Recupere la información de punto final de API de los recursos de red desde el supervisor de red con uno de los métodos siguientes:
	* Para obtener la información del punto final de la API específica de un código encadenamiento, en la pantalla del canal específico, en el que se está ejecutando el código de encadenamiento, localice el código de encadenamiento y pulse el botón **JSON**. ![Puntos finales de API por código de encadenamiento](images/channel_chaincode.png "Puntos finales de API por código de encadenamiento")
	* Para obtener un conjunto completo de la información de punto final de API sobre todos los recursos de la red, pulse el botón **Perfil de conexión** en la pantalla "Visión general". ![Perfil de conexión en el supervisor de red](images/service_credentials.png "Perfil de conexión en el supervisor de red")

2. Localice la información de punto final de API de los recursos de red, que se parece a la del siguiente ejemplo:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	{:codeblock}
	**Nota**: si desea más iguales como destinos en la red, por ejemplo, necesita la aprobación de un igual que no pertenezca a su organización y después debe obtener la información de punto final de API correcta de esos iguales.  También tiene que guardar el certificado de la CA de la otra organización para verificar las respuestas que se devuelvan a su aplicación. Esta información no se expone en el perfil de conexión, por lo que debe ponerse en contacto con el administrador correspondiente a la organización de Cloud Foundry y obtener esta información en una operación externa. El URL del servicio de ordenación es de acceso común en la red; no necesita ninguna información específica de miembro para este servicio de ordenación.

3. Conecte la información de punto final de API a un archivo de configuración de la aplicación, como se muestra en el ejemplo siguiente:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```
	{:codeblock}

## Utilización de índices de CouchDB

Si la red utiliza CouchDB y desea aprovechar la característica de indexación de CouchDB (lo que mejora el rendimiento de CouchDB), los índices deben empaquetarse con el código de encadenamiento.

Para obtener más información sobre CouchDB y cómo establecer índices, consulte la [documentación de Fabric en CouchDB ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html)

## Alojamiento de aplicaciones
Puede alojar la aplicación en el sistema de archivos local o enviarla a {{site.data.keyword.Bluemix_notm}}. Para enviar la aplicación a {{site.data.keyword.Bluemix_notm}}, siga estos pasos:
1. Instale el [instalador de línea de mandatos de Cloud Foundry ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/cloudfoundry/cli/releases).  Pruebe la instalación con el mandato `cf`.
    * Si la instalación es satisfactoria, debería ver salida de texto en el terminal.
    * Si ve "command not found", significa que la instalación no se ha realizado correctamente o que CF no se ha añadido a la vía de acceso del sistema.
2. Configure el punto final de API e inicie sesión con su ID y contraseña de {{site.data.keyword.Bluemix_notm}} emitiendo los mandatos siguientes:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. Examine el directorio de la aplicación y envíe la aplicación emitiendo el siguiente mandato. Esto puede tardar varios minutos dependiendo del tamaño de la aplicación. Verá los registros de {{site.data.keyword.Bluemix_notm}} en el terminal; cesarán cuando la aplicación se haya iniciado correctamente.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	Puede comprobar los registros de la aplicación emitiendo uno de los mandatos siguientes:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## Cómo desconectar la aplicación de la red
{: #disconnect}
Efectúe los pasos siguientes para eliminar la conexión entre la aplicación y la red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix_short}}.
1. Elimine la información de punto final de API del archivo de configuración de la aplicación. Para obtener más información, consulte [Adición de puntos finales de API de red a la aplicación](#adding-network-api-endpoints-to-your-application).
2. Suprima el contenedor de código de encadenamiento.
	1. En la pantalla "Canal" del Supervisor de red, localice el canal en el que desea instalar el código de encadenamiento.
	2. En la pantalla del canal específico, localice el código de encadenamiento que desea inhabilitar.
	3. Pulse el botón **Suprimir** y haga clic en **Enviar** en el panel de supresión del código de encadenamiento. Se eliminará el contenedor del código de encadenamiento. ![Suprimir un código de encadenamiento](images/channel_chaincode.png "Suprimir un código de encadenamiento")

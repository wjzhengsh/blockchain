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

# Desarrollo de aplicaciones
{: #dev_app}
Con las aplicaciones, puede invocar el código de encadenamiento para consultar o actualizar un libro mayor específico del canal en la red de {{site.data.keyword.blockchain}}.
{:shortdesc}

## Configuración del entorno de desarrollo de aplicaciones
Debe instalar los requisitos previos de software para desarrollar aplicaciones que puedan interactuar con la red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Página de descargas de Git ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://git-scm.com/downloads){:new_window})

	Git es una herramienta de control de versiones con la que se puede familiarizar tanto para el desarrollo de código de encadenamiento como para el desarrollo de software en general. Además, git bash, que se instala junto git en Windows, es una excelente alternativa al indicador de mandatos de Windows.

	Utilice el mandato siguiente para verificar la instalación de Git.  Debería ver una salida similar a la siguiente:
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([Página de descargas de GoLang ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/dl){:new_window})

	La instalación de GoLang instala un conjunto de herramientas de CLI de GO, que son muy útiles para escribir código de encadenamiento.  Por ejemplo, el mandato `go build` le permite verificar realmente el código de encadenamiento antes de intentar desplegarlo en una red.  En el momento de escribir, puede saber si este código de encadenamiento se compila correctamente con la versión `1.8.3`.

	Utilice el siguiente mandato para verificar la versión GoLang.  Debería ver una salida similar a la siguiente:
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}

	Siga las [instrucciones de instalación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/doc/install){:new_window} para definir correctamente las variables de entorno. Compruebe `GOPATH` con el mandato siguiente. Tenga en cuenta que no es necesario que `GOPATH` coincida con el ejemplo, solo importa que tenga esta variable definida en un directorio válido del sistema de archivos.
	```
	$ echo $GOPATH
C:\gopath
	```
	{:screen}

	A continuación, puede verificar la instalación de GoLang compilando el código de GoLang con el ejemplo [hello world ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://golang.org/doc/install#testing){:new_window}.

*	Node.js ([Página de descargas de Node.js ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://nodejs.org/en/download/){:new_window}).  Seleccione una versión comprendida entre 6.9.5 y 7. Las versiones de nodo superiores a la 7 pueden generar errores al descargar módulos de SDK.

	Utilice los mandatos siguientes para verificar la instalación de Node.js.  Debería ver una salida similar a la siguiente:
	```
	$ node -v
	v6.10.1

	$ npm -v
	3.10.10
	```
	{:screen}

## Generación de certificados del lado de cliente
No ahondaremos en las minucias de x509 y la infraestructura de clave pública; hay muchos recursos externos para ello. Basta decir que los flujos de comunicación en Fabric utilizan operaciones sign/verify en cada punto de encuentro. De este modo, cualquier cliente que envíe llamadas (es decir, transacciones) a la red deberá firmar cargas útiles (clave privada) y adjuntar un certificado x509 firmado correctamente para fines de verificación (certificado firmado). La clave privada y el certificado firmado junto con un identificador de MSP y el certificado raíz de la entidad emisora de certificados (CA) conforman lo que se conoce como objeto de "contexto de usuario". De nuevo, no se necesitan detalles adicionales. Simplemente nos comunicaremos con la entidad emisora de certificados correspondiente y recuperaremos las claves y certificados que permiten que se forme el objeto; este proceso se conoce como "inscripción". Después de formar el objeto de contexto de usuario, es tan fácil como llamar a una API desde la aplicación para "establecer" u "obtener" este contexto de usuario. En este punto, la aplicación (es decir, el cliente) está equipada con todos los artefactos necesarios y está lista para comunicarse con la red. Veremos dos enfoques de recuperación de claves y certificados.

### Línea de mandatos
Se trata del enfoque más sencillo. En primer lugar, siga las instrucciones para crear el [cliente Fabric CA ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Este paso permite establecer comunicación con un servidor CA y recibir claves y certificados con el formato adecuado.

En segundo lugar, descargue los certificados TLS de [su {{site.data.keyword.Bluemix_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) y guarde el contenido en una carpeta; por ejemplo, ``$HOME/tls``.  Este paso permite que se cifre el flujo de datos en la conexión.

Por último, abra el archivo JSON de **Perfil de conexión** desde la pantalla **Visión general** del Supervisor de red y busque las variables relevantes:
* URL para la CA: `url` en `certificateAuthorities`
* ID de usuario administrador: ``enrollId``
* Contraseña de administración: ``enrollSecret``
* Nombre de la CA: ``caName``

Mediante el cliente Fabric CA, podemos enviar una llamada de "enroll" a nuestra entidad emisora de certificados pasando los certificados TLS y las cuatro series anteriores con el mandato siguiente:
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

El binario ``fabric-ca-client`` se sitúa en ``$GOPATH/bin``, por lo que deberá encontrarse en la ubicación adecuada de la máquina local cuando ejecute este mandato.  Una llamada real es similar al siguiente mandato de ejemplo:
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

Busque el certificado de administración en `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Luego podrá subir el certificado de administración a la red de blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red.

También puede encontrar el certificado raíz de la CA y la clave privada de administración en los directorios siguientes:
* Certificado raíz de la CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* Clave privada de administración: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Existen dos repositorios de Hyperledger que contienen excelentes recursos y scripts para comprender cómo interactuar mediante programación con una entidad emisora de certificados. El repositorio ``fabric-samples`` contiene el ejemplo "balance transfer" y el repositorio ``fabric-sdk-node`` dispone de una serie de pruebas de servicios de CA. Si tiene previsto emitir sus solicitudes de inscripción en el lado de la aplicación, debe comprender en profundidad las API que se deben exponer en los paquetes ``fabric-ca-client`` y ``fabric-client``.  Utilice estos scripts y repositorios como base para estructurar la app.

Veamos unos pocos fragmentos clave del ejemplo "balance transfer":

En primer lugar, tenemos que crear nuestro objeto de cliente y establecer una instancia de almacén clave/valor donde se almacenarán nuestros certificados y claves. Para ello, utilizaremos un método de fábrica sencillo, ``newCryptoSuite``, que amplía la clase ``Client`` de ``BaseClient``. A continuación, se muestra el código:

```
# <PUBLIC_PRIVATE_KEY_PATH> denotes the path on your local machine where you wish to store your key and cert
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

La práctica habitual sería exportar una variable de entorno que defina la vía de acceso clave/valor en la máquina y pasarla a la función anterior. Ahora que hemos definido nuestra KVS, vamos a utilizar algunos métodos de la clase ``FabricCAServices``. Esta clase es una implementación del cliente Fabric CA y, por lo tanto, habilitará la comunicación con el servidor CA. Primero debemos pasar información a nuestro cliente CA, concretamente el URL de la CA:

```
# the caURL can be defined manually or by setting an environment variable
# the copService variable is defined at the top of the program 
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

A continuación, enviaremos la llamada "enroll" al servidor CA. Esta acción devolverá al cliente una clave privada y una clave pública envueltas en un certificado x509 y firmadas por la CA; lo denominamos signcert o certificado de inscripción. Este certificado de inscripción o "eCert" es la pieza clave, porque permite que las entidades de red verifiquen transacciones y llamadas provenientes del cliente:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

Y la tarea final consiste en establecer la suite de cifrado y crear el contexto de usuario:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

Luego podrá subir el certificado de administración a la red de blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red.

## Desarrollo de aplicaciones
{: #developing-applications}
Puede desarrollar la aplicación en JavaScript o Java y aprovechar las API disponibles en los SDK de cliente de Hyperledger Fabric para habilitar la interacción entre la aplicación y la red.  Una aplicación debe incluir al menos la información siguiente:
* Nombre y versión del código de encadenamiento que se va a invocar.
* Información de punto final de API de los recursos de red, incluidos clasificadores, CA e iguales.
* Funciones para consultar o actualizar el libro mayor en la red.  Si desea alta disponibilidad, debe considerar la migración tras error de nodos en la aplicación.

Puede encontrar aplicaciones de ejemplo para el **Plan Empresarial** en {{site.data.keyword.Bluemix_short}} en [Aplicaciones de ejemplo](howto/sample_applications.html).  Utilice los códigos de encadenamiento y las aplicaciones de ejemplo como plantilla para crear su propia solución empresarial.

## Adición de puntos finales de API a la aplicación
Debe añadir puntos finales de API de los recursos de red a la aplicación de modo que pueda interactuar con la red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix_short}}.  Si no dispone de una red de {{site.data.keyword.blockchain}} en {{site.data.keyword.Bluemix_short}}, puede crear una con el Plan inicial o con el Plan empresarial. Para obtener más información, consulte [Gobierno de la red del Plan inicial](get_start_starter_plan.html) y [Gobierno de la red del Plan empresarial](get_start.html.

Encontrará los puntos finales de API en el perfil de conexión de su red. El perfil de conexión está en formato JSON y contiene la información de punto final de API y los ID de inscripción/secretos de los recursos de red, incluidos clasificadores, CA y nodos iguales. La aplicación interactuará con sus iguales y otros recursos de red a través de estos puntos finales de API.

1. Recupere la información de punto final de API de los recursos de red desde el supervisor de red con uno de los métodos siguientes:
	* Para obtener la información del punto final de la API específica de un código encadenamiento, en la pantalla del canal específico, en el que se está ejecutando el código de encadenamiento, localice el código de encadenamiento y pulse el botón **JSON**. ![Puntos finales de API por código de encadenamiento](images/channel_chaincode_detail.png "Puntos finales de API por código de encadenamiento")
	* Para obtener un conjunto completo de la información de punto final de API sobre todos los recursos de la red, pulse el botón **Perfil de conexión** en la pantalla "Visión general". ![Perfil de conexión en el supervisor de red](images/service_credentials.png "Perfil de conexión en el supervisor de red")

2. Localice la información de punto final de API de los recursos de red, que se parece a la del siguiente ejemplo:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```

	**Nota**: si desea más iguales como destinos en la red, por ejemplo, necesita la aprobación de un igual que no pertenezca a su organización y después debe obtener la información de punto final de API correcta de esos iguales.  También tiene que guardar el certificado de la CA de la otra organización para verificar las respuestas que se devuelvan a su aplicación. Esta información no se expone en el perfil de conexión, por lo que debe ponerse en contacto con el administrador correspondiente a la organización de Cloud Foundry y obtener esta información en una operación externa. El URL del servicio de ordenación es de acceso común en la red; no necesita ninguna información específica de miembro para este servicio de ordenación.

3. Conecte la información de punto final de API a un archivo de configuración de la aplicación, como se muestra en el ejemplo siguiente:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

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
3. Examine el directorio de la aplicación y envíe la aplicación emitiendo el siguiente mandato. Esto puede tardar varios minutos dependiendo del tamaño de la aplicación. Verá los registros de {{site.data.keyword.Bluemix_notm}} en el terminal; cesarán cuando la aplicación se haya iniciado correctamente.
	```
	> cf push YOUR_APP_NAME_HERE
	```
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
	3. Pulse el botón **Suprimir** y haga clic en **Enviar** en el panel de supresión del código de encadenamiento. Se eliminará el contenedor del código de encadenamiento. ![Suprimir un código de encadenamiento](images/channel_chaincode_detail.png "Suprimir un código de encadenamiento")

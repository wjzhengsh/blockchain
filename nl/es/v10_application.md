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

# Desarrollo de aplicaciones
{: #dev_app}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


La plataforma {{site.data.keyword.blockchainfull}} proporciona API que puede utilizar para conectar aplicaciones con la red blockchain. Puede utilizar los puntos finales de API en el perfil de conexión para invocar el código de encadenamiento y actualizar o consultar el libro mayor específicos del canal en sus iguales. También puede utilizar las API en [la interfaz de usuario de Swagger](howto/swagger_apis.html) para gestionar nodos, canales y miembros de la red.
{:shortdesc}

Puede utilizar esta guía de aprendizaje para obtener información sobre cómo acceder a las API de la plataforma {{site.data.keyword.blockchainfull_notm}} y utilizarlas para inscribirse y registrar la aplicación con la red. También aprenderá a interactuar con la red y a enviar transacciones desde la aplicación. La guía de aprendizaje se basa en la guía de aprendizaje sobre [Cómo escribir su primera aplicación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) de la documentación de Hyperledger Fabric. Utilizará muchos de los archivos y mandatos de la guía de aprendizaje **Cómo escribir su primera aplicación**, pero los utilizará para interactuar con una red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En esta guía de aprendizaje se describe cada paso del desarrollo de aplicaciones utilizando el SDK de nodo de Fabric Manager Hyperledger. También aprenderá a inscribir y a registrar usuarios utilizando el Cliente CA de Fabric como alternativa a la utilización del SDK.

Además de esta guía de aprendizaje, puede utilizar aplicaciones de ejemplo y código de encadenamiento que proporciona la plataforma {{site.data.keyword.blockchainfull}} como plantillas cuando crea sus propias soluciones de negocio. Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).

## Requisitos previos
Debe cumplir los siguientes requisitos previos para poder utilizar la guía de aprendizaje **Cómo escribir su primera aplicación** en la plataforma {{site.data.keyword.blockchainfull_notm}}.

- Si no tiene una red blockchain en {{site.data.keyword.cloud_notm}}, debe crear una con un plan inicial o empresarial. Para obtener más información, consulte [Creación de la red del Plan inicial](get_start_starter_plan.html#creating-a-network) y [Creación de la red del Plan empresarial](get_start.html#creating-a-network).

  Después de entrar en el supervisor de red de su red, añada al menos un igual para su organización en la pantalla "Visión general". A continuación, cree al menos un canal en la red. Para obtener más información, consulte [Creación de un canal](howto/create_channel.html#creating-a-channel). **Tenga en cuenta** que si utiliza una red de plan inicial, la red ya tiene un canal llamado `defaultchannel` que puede utilizar para desplegar el código de encadenamiento.

- Instale las herramientas necesarias para descargar los ejemplos de Hyperledger Fabric y para utilizar el SDK de nodo.
  * [Curl ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") o [Git ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- Instale los ejemplos de Hyperledger Fabric descargando el directorio `fabric-samples`. Puede seguir la [guía de iniciación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html) de la documentación de Hyperledger Fabric.

  Después de descargar el directorio `fabric-samples` en la máquina local, vaya a `fabric-samples/fabcar`. Puede hacer una copia de este directorio y cambiarle el nombre para poder probar la aplicación de ejemplo en un directorio limpio.

  En el directorio `fabcar`, ejecute el mandato `npm install` para instalar los paquetes necesarios para utilizar el SDK de Fabric Manager, que incluyen `fabric-client` y `fabric-ca-client`.

- Instale y cree una instancia del código de encadenamiento fabcar en su canal mediante el [supervisor de red](howto/install_instantiate_chaincode.html#installchaincode). Encontrará el código de encadenamiento fabcar en la carpeta `fabric-samples` bajo `fabric-samples > chaincode > fabcar > go`.

- Recupere el perfil de conexión de red en la pantalla "Visión general" del supervisor de red. Guarde el perfil de conexión en el directorio `fabcar` y cámbiele el nombre por `creds.json`.

## Utilización de los SDK de Fabric
{: #using-the-fabric-sdks}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista actualizada de los lenguajes soportados en la [Documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"){:new_window}. Se recomienda utilizar el SDK de nodo o el SDK de Java con la plataforma {{site.data.keyword.blockchainfull_notm}}. Encontrará más información sobre las API que proporcionan los SDK en los repositorios individuales de los SDK.

En esta guía de aprendizaje se utiliza el [SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de nodo"){:new_window} para registrar e inscribir la aplicación y luego utilizar la aplicación para emitir transacciones invocando y consultando el código de encadenamiento. En esta guía de aprendizaje se describe la información que debe proporcionar al SDK para que la aplicación pueda conectarse a la red blockchain. También contiene una introducción a algunas de las API que puede utilizar y cómo el SDK interactúa y envía las transacciones a la red blockchain.

## Adición de puntos finales de API a la aplicación
{: #api-endpoints}

Debe proporcionar la aplicación los puntos finales de API de recursos de red específicos, incluidos solicitante, CA y nodos iguales, y la red blockchain en {{site.data.keyword.cloud_notm}}. La aplicación puede interactuar con la red a través de estos puntos finales de API. Encontrará los puntos finales de API en el perfil de conexión de su red. El perfil de conexión está en formato JSON y contiene la información de punto final de la API y los ID de inscripción/secretos de los recursos de red.

1. Recupere la información de punto final de API de los recursos de red desde el supervisor de red con uno de los métodos siguientes:
  * En la pantalla "Visión general", pulse **Perfil de conexión**. El perfil de conexión contiene un conjunto completo de información de punto final de API de todos los recursos de red. ![Perfil de conexión en el supervisor de red](images/service_credentials.png "Perfil de conexión en el supervisor de red")

  * Si tiene código de encadenamiento en ejecución en la red, puede obtener la información de punto final de la API que es específica del código de encadenamiento. En la pantalla "Canales", pulse la fila del canal en la que se ejecuta el código de encadenamiento para abrir la pantalla de canal específica. Luego localice el código de encadenamiento y pulse el botón **JSON**.
    ![Puntos finales de API por código de encadenamiento](images/channel_chaincode.png "Puntos finales de API por código de encadenamiento")

2. Localice la información de punto final de API de los recursos de red, que es similar al URL de la fila `peer1-org1` en el ejemplo siguiente:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **Nota**: es posible que desee dirigir los recursos de red fuera de su organización con la aplicación. Por ejemplo, si una [política de aprobación](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies) de código de encadenamiento requiere aprobaciones de otras organizaciones del canal, debe obtener la información de punto final de sus iguales y los certificados TLS correspondientes. Encontrará esta información en la sección peers del perfil de conexión. Sin embargo, tiene que ponerse en contacto con el administrador de las otras organizaciones para saber qué iguales se han unido a canales particulares.

3. Conecte la información de punto final de API a un archivo de configuración de la aplicación, como se muestra en el ejemplo siguiente:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  También puede enviar [solicitudes HEAD](howto/monitor_network.html#monitor-nodes) a estos puntos finales para comprobar la disponibilidad de los recursos de red.

  Si utiliza los SDK de Fabric Manager, también puede conectarse a la red utilizando el perfil de conexión. Esta guía de aprendizaje proporciona la información de punto final de la red al SDK manualmente. Sin embargo, encontrará una guía de aprendizaje y directrices en el apartado sobre [utilización del perfil de conexión con el SDK](#using-your-connection-profile-with-the-sdk) en una sección posterior.

## Inscripción de la aplicación
{: #enroll-app}

Para poder conectar una aplicación a la red en la plataforma {{site.data.keyword.blockchainfull_notm}}, tiene que probar la autenticidad de la aplicación en la red. No entraremos en detalles sobre x509 y la infraestructura de claves públicas ya que puede encontrar mucha información en [recursos externos ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window}. Pero, en pocas palabras, los flujos de comunicación en Fabric utilizan operaciones sign/verify en cada punto de encuentro. Por lo tanto, cualquier aplicación que envíe llamadas, como consultas o actualizaciones de libro mayor, a la red deberá debe firmar cargas útiles y con su clave privada y adjuntar un certificado x509 firmado adecuadamente para fines de verificación. **Inscripción** es el proceso de generación de las claves y certificados necesarios de la entidad emisora de certificados adecuada. Después de la inscripción, la aplicación está lista para comunicarse con la red.

En esta sección se explica cómo recuperar las claves y los certificados utilizando el SDK de Fabric como parte de la guía de aprendizaje sobre **Cómo escribir su primera aplicación**. También aprenderá a utilizar el cliente de CA de Fabric desde la línea de mandatos para inscribir la aplicación.

<!-- this last sentance when the certificates article goes live --->

### Inscripción mediante el SDK de Fabric
{: #enroll-app-sdk}

Desde el directorio `fabcar` de la carpeta `fabric-samples`, abra el archivo `enrollAdmin.js` en un editor de texto.

1. En primer lugar, el archivo crea una instancia del cliente de Fabric.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. Luego el archivo crea un almacén de claves y valores (KVS) para gestionar los certificados. El SDK utiliza la clase [KeyValueStore ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} para crear el  almacén de claves y valores y la clase [CryptoSuite ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} para realizar los cálculos criptográficos. A continuación encontrará el bloque de código correspondiente.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. Después de definir el KVS, puede utilizar varios métodos de la clase [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") y la API Fabric-CA-Client <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> para comunicar con el servidor de CA. Tendrá que especificar el SDK, el nombre y el URL de la entidad emisora de certificados. Abra el archivo JSON de **Perfil de conexión** desde la pantalla **Visión general** del Supervisor de red y busque las variables siguientes bajo la sección `certificateAuthorites`:
  * URL para la CA: `url` en `certificateAuthorities`
  * ID de usuario administrador: ``enrollId``
  * Contraseña de administración: ``enrollSecret``
  * Nombre de la CA: `caName`

  **Edite** las líneas relevantes en el archivo `enrollAdmin.js` con esta información del siguiente modo:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  Por ejemplo:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  Luego Fabric Client comprueba si la aplicación ya está inscrita. **Edite** la línea siguiente que contiene `enrollID` desde el perfil de conexión:
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. Debe enviar la llamada "enroll" al servidor de CA. `admin` ya está registrado con la red. La llamada enroll recupera una clave privada y una clave pública contenidas en un certificado x509 y firmadas por la CA de destino. Este certificado contenido y firmado se llama signCert. El signCert permite a los miembros de la red verificar las llamadas que se han originado en el cliente. Debe proporcionar el nombre de organización, la contraseña y el ID de MSP del archivo de credenciales. En la sección `certificateAuthorities` de las credenciales de red, busque `enrollID`, `enrollSecret` y `x-mspid`. **Edite** el bloque de código siguiente con dichos valores y sustituya la sección correspondiente de su archivo.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Guarde el archivo `enrollAdmin.js`.

En el directorio `fabcar`, inscriba admin con el siguiente mandato:
```
node enrollAdmin.js
```
{:codeblock}

El mandato enrollment genera el signCert y lo exporta a una carpeta llamada `hfc-key-store`. Los futuros archivos de esta guía de aprendizaje buscarán sus certificados en esta carpeta. Si encuentra los certificados de admin en la carpeta `hfc-key-store`, significa que el mandato enroll funciona.

Si desea [trabajar con la red utilizando el SDK](#operate-sdk), tiene que cargar el signCert de admin en la plataforma {{site.data.keyword.blockchainfull}}. Encontrará signCert de admin en la carpeta `hfc-key-store`. Abra el archivo `admin` y copie el certificado dentro de las comillas después del campo `certificate`. Utilice una herramienta o un editor de texto para convertir el certificado al formato PEM. Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red. Esto no es necesario si solo utiliza el SDK para invocar o consultar el código de encadenamiento.

<!-- you can eliminate this section when the certificates article goes live --->

### Inscripción mediante el cliente de CA de Fabric
{: #enroll-app-caclient}

1. Descargue los [binarios fabric-ca](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) en la máquina local y extráigalos. Mueva el cliente `fabric-ca` ejecutable del directorio en el que se ha descargado a un directorio en el que lo pueda utilizar para ejecutar mandatos.

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

4. Puede utilizar el cliente de CA de Fabric para enviar una llamada `enroll` a la entidad emisora de certificados pasando la vía de acceso de certificados TLS y las cuatro series anteriores con el mandato siguiente:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Debe ejecutar este mandato en el directorio al que ha movido el cliente CA de Fabric. Una llamada real es similar al siguiente mandato de ejemplo:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. Busque el certificado de administración en `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](v10_dashboard.html#members) en el Supervisor de red.

  También puede encontrar el certificado raíz de la CA y la clave privada de administración en los directorios siguientes:
  * Certificado raíz de la CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * Clave privada de administración: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## Registro de la aplicación
{: #register-app}

Después de generar los certificados del lado del cliente, tiene que registrar la aplicación en la entidad emisora de certificados. Después de registrarla, la aplicación se añadirá a la lista de componentes que la red reconoce. Una práctica recomendada consiste en registrar la aplicación como una identidad independiente en lugar de utilizar `admin` para firmar solicitudes.

### Registro mediante el SDK
{: #register-app-sdk}

Puede utilizar el archivo `registerUser.js` para registrar e inscribir la aplicación como `user1` utilizando el signCert de `admin`. Abra `registerUser.js` en un editor de texto.

1. Proporcione el URL y el nombre de la CA a una instancia nueva del cliente CA de Fabric.

2. Proporcione el enrollID al método `getUserContext` desde [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html) para comprobar si `admin` se ha inscrito y tiene permiso para enviar esta solicitud. **Edite** el bloque de código correspondiente del archivo siguiendo el ejemplo siguiente:
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then((user_from_store) => {
  if (user_from_store && user_from_store.isEnrolled()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error('Failed to get admin.... run enrollAdmin.js');
  }
  ```
  {:codeblock}

3. Utilice el **cliente de CA de Fabric** para registrar e inscribir el usuario con la CA y luego utilice **Fabric Client** para crear el nuevo signCert. **Edite** el siguiente bloque con su ID de MSP y la afiliación de su organización. Encontrará sus `x-affiliations` en la sección de entidades emisoras de certificados de sus credenciales de red y puede utilizar cualquier afiliación que aparezca en la lista. Añada el nombre del usuario que desea crear. En el ejemplo fabcar se utiliza `user1` de forma predeterminada.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Guarde el archivo `registerUser.js`.

Ejecute el mandato `node registerUser.js` para registrar e inscribir `user1`. Si encuentra los certificados de `user1` en la carpeta `hfc-key-store`, significa que el mandato funciona. Solo puede registrar una identidad una vez. Si tiene algún problema, intente ejecutar `registerUser.js` con un nuevo nombre de usuario.

<!-- you can eliminate this section when the certificates article goes live --->

### Registro mediante el cliente de CA de Fabric
{: #register-app-caclient}

1. Emita el mandato siguiente para encontrar su afiliación y el nombre de su organización en la red blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles pathToPem
  ```
  {:codeblock}

  Debería ver información parecida a la del siguiente ejemplo:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Anote el segundo valor de **affiliation**, por ejemplo `ibp.PeerOrg1`. Debe utilizar este valor en el mandato siguiente.

2. Ejecute el mandato siguiente para registrar el igual.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Especifique un nombre y una contraseña para el igual y sustituya `name` y `password` por el nombre y la contraseña del igual. Anote esta información. La necesitará cuando configure el igual. Por ejemplo:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  Solo puede registrar una identidad una vez. Si tiene algún problema, intente un mandato con un nuevo nombre de usuario y contraseña.

  Cuando el mandato finalice correctamente, debería ver información parecida a la del siguiente ejemplo:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### Registro mediante el supervisor de red

Como alternativa, puede registrar e inscribir la aplicación cliente utilizando el separador **Entidad emisora de certificados** del supervisor de red. Consulte esta [información](v10_dashboard.html#ca) para ver más instrucciones.

## Envío de transacciones mediante la invocación y consulta de código de encadenamiento
{: #invoke-query}

La aplicación tiene que interactuar con la red completa de blockchain para enviar una transacción.

1. La aplicación envía una propuesta de transacción para que la aprueben los iguales del canal.
2. Las iguales que la aprueban devuelven la transacción aprobada a la aplicación.
3. La aplicación envía la transacción aprobada al servicio de ordenación para añadir la transacción al libro mayor.

Para obtener más información sobre el flujo completo de la transacción, consulte [Flujo de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Flujo de transacción"){:new_window} en la documentación de Hyperledger Fabric. Después de consultar esta guía de aprendizaje, visite la sección sobre [conectividad y disponibilidad de las aplicaciones](#app-connectivity-availability) para ver consejos de gestión sobre cómo el SDK interactúa con la red.

En los ejemplos siguientes se muestra cómo el SDK de nodo configura la topología de red, define la propuesta de transacción y luego envía la transacción a la red. Puede utilizar el archivo `invoke.js` para invocar funciones dentro del código de encadenamiento `fabcar`. Estas funciones le permiten crear y transferir activos en el libro mayor de blockchain. En esta guía de aprendizaje se utiliza la función `initLedger` para
añadir datos nuevos a su canal y luego se utiliza el archivo `query.js` para consultar los datos.

### Invocando código de encadenamiento
{: #invoke}

Abra el archivo `invoke.js` en un editor de texto.

1. Añada `var creds = require('./creds.json')` al principio del archivo. Esta línea de código permite que el archivo `invoke.js` lea la información del archivo de credenciales `creds.json`.

2. Utilice la clase [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} para configurar la red fabric utilizando los puntos finales de API de sus recursos de red. En este paso define el canal y los iguales a los que el cliente enviará la propuesta y el servicio de ordenación al que el SDK enviará la transacción aprobada. **Edite** el bloque de código correspondiente de abajo. La línea `creds.peers["org1-peer1"].url` importa el url del igual desde el archivo de credenciales.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  Cuando añade el URL del igual al método `fabric_client.newPeer` , también importa los certificados TLS relevantes del perfil de conexión utilizando el fragmento de código siguiente. Hizo lo mismo cuando añadió el URL de servicio de ordenación. Debe utilizar estos certificados TLS para autenticar la comunicación con la red.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  Si la política de aprobación requiere que las transacciones sean aprobadas por otras organizaciones del canal, tendrá que añadir los iguales de dichas organizaciones mediante los métodos `newPeer()` y `channel.addPeer()` cuando configure la red. Las organizaciones tienen que enviarle la lista de los iguales que se han unido a un determinado canal. La información de punto final y los certificados TLS estarán disponibles en el perfil de conexión. El SDK enviará la transacción a todos los iguales añadidos al canal.

  También puede añadir iguales adicionales pertenecientes a su organización que se unan al canal como un paso del proceso para [hacer que la aplicación esté altamente disponible](#ha-app). Esto proporcionará el SDK con una función de migración tras error en el caso de que uno de sus iguales se interrumpa.

3. Después de configurar la red fabric y de importar la identidad de la aplicación y el signCert en el paso de registro, el archivo `invoke.js` define la propuesta que enviará a la red. Puede utilizar la función `initLedger` en el código de encadenamiento `fabcar` para añadir algunos datos iniciales a su libro mayor. También puede editar el bloque de código para invocar otras funciones que encontrará en el código de encadenamiento `fabcar`.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Después de definir la solicitud, puede enviar una [propuesta de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") a los iguales del canal. Una vez que los iguales devuelvan la propuesta, puede [enviar la transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") al servicio de ordenación.

4. Puede añadir un servicio de sucesos para hacer que el flujo de transacciones sea más eficiente. **Edite** la siguiente sección:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Aunque en el ejemplo se utiliza un servicio de sucesos basado en iguales, debe utilizar un escucha basado en canal. Encontrará más información en la sección sobre [gestión de transacciones](#managing-transactions) y en la [documentación de SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "servicio de sucesos basado en canal"){:new_window}.

5. De forma predeterminada, `invoke.js` envía la transacción como `user1`. Puede editar el archivo `invoke.js` si ha registrado un nombre distinto.

Cuando termine de editar el archivo, emita el mandato `node invoke.js` para enviar la transacción a la red. Debería ver lo siguiente como confirmación de que la invocación ha sido satisfactoria.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Esto indica que la aplicación ha invocado correctamente el código de encadenamiento y ha añadido los datos al libro mayor.

### Consulta del código de encadenamiento
{: #query}

Ahora puede utilizar `query.js` para leer el libro mayor. Abra el archivo `query.js` en un editor de texto.

1. Añada `var creds = require('./creds.json')` al principio del archivo.
2. Actualice el archivo con el nombre de canal y la información de punto final del igual. Debido a que esta operación solo lee los datos almacenados en el igual, no es necesario añadir la información de punto final del servicio de ordenación. `query.js` da por supuesto que envía la propuesta como `user1`.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

Cuando termine de editar el archivo, emita el mandato `node query.js`; debería ver la lista de coches en el libro mayor, que es similar al ejemplo siguiente.
```
Query has completed, checking results
Response is
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

Para obtener más información sobre la aplicación fabcar y las funciones que utiliza, visite la guía de aprendizaje sobre [Cómo escribir su primera aplicación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "cómo escribir su primera aplicación"){:new_window} en la documentación de Hyperledger Fabric.

## Utilización del perfil de conexión con el SDK
{: #using-your-connection-profile-with-the-sdk}

En lugar de importar la información de punto final a la red manualmente, puede hacer que el SDK se conecte a la red utilizando el **Perfil de conexión** desde la pantalla **Visión general** del supervisor de red. Esto agiliza el proceso de conectar con la entidad emisora de certificados para el proceso de inscripción y registro. También elimina la necesidad de definir la red fabric antes de enviar una transacción. El SDK encontrará los iguales y solicitantes en el canal relevante directamente desde el perfil de conexión. Encontrará información sobre cómo utilizar un perfil de conexión en la [documentación del SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "guía de aprendizaje del perfil de conexión"){:new_window}.

Vamos a utilizar el archivo `invoke.js` como ejemplo para ver la eficacia de utilizar un perfil de conexión en lugar de puntos finales manuales. Puede establecer una nueva instancia del cliente de fabric utilizando la clase `loadFromConfig`. Sustituya `var fabric_client = new Fabric_Client();` por el código siguiente.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

En lugar de configurar la red fabric mediante la creación de iguales y solicitantes, y luego añadiéndolos a un canal, puede utilizar la línea siguiente para crear un nuevo canal.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

A continuación, el SDK añade los iguales y el servicio de ordenación que definidos en el canal utilizando el perfil de conexión. Esto hace que la escritura de aplicaciones sea más eficientes y facilita la actualización de las aplicaciones a medida que los miembros de la red se unen, se marchan e inician nuevos canales. Consulte la [guía de aprendizaje del perfil de conexión ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "guía de aprendizaje del perfil de conexión"){:new_window} de la documentación del SDK de nodo para obtener más información sobre los pasos adicionales a seguir. Puede utilizar esta [versión de la guía de aprendizaje de fabcar ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} que utiliza un perfil de conexión en lugar de conexiones de punto final manuales.

Puede enviar transacciones a iguales que están fuera de su organización para su aprobación editando el perfil de conexión. El perfil de conexión ya contiene la información de punto final y los certificados TLS de los iguales de otras organizaciones de la red de la plataforma {{site.data.keyword.blockchainfull}}. Añada el nombre del igual al canal relevante en la sección `channels` del perfil para añadir el igual al canal.

## Conectividad y disponibilidad de las aplicaciones
{: #app-connectivity-availability}

El [flujo de transacciones ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "flujo de transacciones"){:new_window} de Hyperledger Fabric abarca varios componentes, entre los que las aplicaciones cliente juegan un rol exclusivo. El SDK envía las propuestas de transacción a los iguales para su aprobación. Luego recopila las propuestas aprobadas que se van a enviar al servicio de ordenación, que a continuación envía bloques de transacciones a los iguales para que se añadan a los libros mayores de canal. Los desarrolladores de aplicaciones de producción deben estar preparados para gestionar las interacciones entre el SDK y sus redes para aumentar su eficiencia y disponibilidad.

### Gestión de transacciones
{: #managing-transactions}

Los clientes de aplicaciones deben asegurarse de que sus propuestas de transacción se validan y de que las propuestas se completan correctamente. Una propuesta se puede retrasar o perder por varios motivos, como una interrupción de la red o una anomalía de un componente. Debe preparar la aplicación para [alta disponibilidad](#ha-app) para que pueda gestionar una anomalía en un componente. También puede [aumentar los valores de tiempo de espera](#set-timeout-in-sdk) en la aplicación para evitar que las propuestas excedan el tiempo de espera antes de que la red pueda responder.

Si un código de encadenamiento no se está ejecutando, la primera propuesta que se envía a dicho código de encadenamiento lo iniciará. Mientras se inicia el código de encadenamiento, todas las demás propuestas se rechazan con un error que indica que el código de encadenamiento se está iniciando. Esto difiere de la invalidación de la transacción. Si una propuesta se rechaza mientras se está iniciando el código de encadenamiento, los clientes de la aplicación deben volver a enviar las propuestas rechazadas después de que se inicie el código de encadenamiento. Los clientes de la aplicación pueden utilizar una cola de mensajes para evitar que se pierdan las propuestas de transacción.

Puede utilizar un servicio de sucesos basado en canal para supervisar las transacciones y crear colas de mensajes. Se deben utilizar escuchas basados en canal en lugar de servicios basados en iguales debido a su capacidad para escalar a varios canales y de distinguir entre el tráfico de los diferentes canales. La clase [channelEventHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} puede registrar escuchas basados en transacción, bloque o sucesos de código de encadenamiento. Encontrará información sobre cómo configurar un [servicio de sucesos basado en canal ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "servicio de sucesos basado en canal"){:new_window} en la documentación del SDK de nodo.

### Cierre de conexiones de red

Cuando añade iguales y solicitantes a su SDK antes de enviar una propuesta de transacción, está abriendo una conexión en modalidad continua grpc con el componente de red. Por ejemplo, el mandato
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
abrirá una conexión con `org1-peer1`. Si tiene una aplicación que ejecuta continuamente, debe cerrar las conexiones cuando no sean necesarias con los mandatos `peer.close()` y `orderer.close()` para liberar recursos y evitar la degradación del rendimiento. Encontrará más información en las clases [peer](https://fabric-sdk-node.github.io/Peer.html#close__anchor) y [orderer](https://fabric-sdk-node.github.io/Orderer.html#close__anchor) de la documentación del SDK de Node.

### Aplicaciones de alta disponibilidad
{: #ha-app}

Como práctica recomendada de alta disponibilidad, es muy recomendable desplegar un mínimo de dos iguales por organización para la migración tras error. También tendrá que adaptar sus aplicaciones a la alta disponibilidad. Instale el código de encadenamiento en ambos iguales y añádalos a los canales. Luego prepárese para [enviar propuestas de transacciones](#invoke) a los puntos finales de ambos iguales cuando configure la red y cree su lista de destinos de iguales. Si tiene una red del plan empresarial, también puede añadir otro solicitante a la red. Si utiliza su [perfil de conexión](#using-your-connection-profile-with-the-sdk) en lugar de añadir puntos finales de red manualmente, asegúrese de que el perfil esté actualizado y que los iguales y solicitantes adicionales hayan añadido el canal relevante a su sección `channels` del perfil. Luego el SDK añadirá los componentes unidos al canal mediante el perfil de conexión.

## Habilitación de TLS mutuo
{: #mutual-tls}

Si ejecuta redes del plan empresarial que estén al nivel de Fabric V1.1, tiene la opción de [habilitar TLS mutuo](v10_dashboard.html#network-preferences) para las aplicaciones. Si habilita TLS mutuo, debe actualizar las aplicaciones para dar soporte a esta función. De lo contrario, las aplicaciones no podrán comunicarse con la red.

En el Perfil de conexión, localice la sección `certificateAuthorities`, donde encontrará los siguientes atributos que son necesarios para inscribir y obtener los certificados para comunicarse con la red utilizando TLS mutuo.

- `url`: URL para conectarse a la CA que puede proporcionar certificados de TLS mutuo
- `enrollId`: ID de inscripción a utilizar para obtener un certificado
- `enrollSecret`: Secreto de inscripción a utilizar para obtener un certificado
- `x-tlsCAName`: Nombre de la CA a utilizar para obtener el certificado que permitirá que la aplicación se comunique con TLS mutuo.

Para obtener más información sobre cómo actualizar aplicaciones para que den soporte a TLS mutuo, consulte [ Cómo configurar TLS mutuo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls mutuo"){:new_window}.

## (Opcional) Funcionamiento de la red mediante el SDK
{: #operate-sdk}

También puede utilizar el SDK para hacer funcionar la red blockchain. En esta guía de aprendizaje se explica cómo puede utilizar el SDK para unir a sus iguales a los canales, instalar el código de encadenamiento en sus iguales y crear una instancia del código de encadenamiento en los canales. Estos pasos son opcionales porque también puede realizar estas operaciones con el supervisor de red o con las API en la [interfaz de usuario de Swagger](howto/swagger_apis.html) si todos los iguales se ejecutan en la plataforma {{site.data.keyword.blockchainfull}}.

Debe cargar el signCert de administrador en la plataforma {{site.data.keyword.blockchainfull}} para poder llevar a cabo estos pasos. Encontrará instrucciones sobre cómo cargar el signCert al final de la [sección sobre inscripción](#enroll-app-sdk)

### Cómo unir un canal
{: #join-channel-sdk}

Después de crear un canal utilizando el supervisor de red o las API, puede utilizar el SDK para unir su igual al canal.

1. [Capte el bloque de origen ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "captar bloque de origen"){:new_window} del canal desde el servicio de ordenación.
2. Pase el bloque de origen al método [unir canal ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} para unir el su igual al canal.

Para utilizar el ejemplo `fabcar` para unirse a un canal, utilice el archivo `invoke.js` como punto de partida. Tiene que enviar esta solicitud como administrador en lugar de la aplicación, por lo que debe sustituir `user1` por `admin` en el método `getUserContext`. Comenzando por donde define la solicitud de invocación del código de encadenamiento en `var request = {`, sustituya el flujo de transacción por una solicitud de unir canal basada en el fragmento de código siguiente de la [documentación de SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "tls mutuo"){:new_window}.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if(results && results[0].response && results[0].response.status == 200) {
    // good
    } else {
      // not good
    }
  });
  ```
### Instalación de código de encadenamiento
{: #install-cc-sdk}

Puede utilizar el método para [instalar código de encadenamiento ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} de la clase [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} para instalar el código de encadenamiento en el igual.

Para utilizar el ejemplo `fabcar` para instalar el código de encadenamiento `fabcar` en el igual, utilice el archivo `query.js` como base y edítelo. Tiene que enviar esta solicitud como administrador en lugar de la aplicación, por lo que debe sustituir `user1` por `admin` en el método `getUserContext`. Sustituya el objeto de propuesta de transacción por una solicitud de instalación de código de encadenamiento utilizando el ejemplo siguiente:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Envíe este objeto a `return fabric_client.installChaincode(request);`, en lugar de la línea `return channel.queryByChaincode (request);` que aparece actualmente en el archivo.

### Creación de una instancia de un código de encadenamiento
{: #instantiate-cc-sdk}

Para crear una instancia del código de encadenamiento, tiene que enviar una [propuesta de creación de instancia ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} al igual y luego enviar una [solicitud de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} al servicio de ordenación.

Para utilizar el ejemplo `fabcar` para crear una instancia del código de encadenamiento, utilice el archivo `invoke.js` como punto de partida. Tiene que enviar esta solicitud como administrador en lugar de la aplicación, por lo que debe sustituir `user1` por `admin` en el método `getUserContext`. Sustituya el objeto de propuesta de transacción por una solicitud de instalación de código de encadenamiento utilizando el ejemplo siguiente:
```
var request = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

Envíe esta solicitud a `return channel.sendInstantiateProposal(request);` en lugar de `return channel.sendTransactionProposal(request);` que aparece actualmente en el archivo. Después de enviar la solicitud de creación de instancia al canal, deberá enviar la propuesta de aprobación como una transacción al servicio de ordenación. Este utiliza los mismos métodos que para enviar una transacción, por lo que puede dejar el resto del archivo sin cambios. Es posible que desee [aumentar el valor de tiempo de espera](#set-timeout-in-sdk) en la propuesta de creación de instancia. De lo contrario, puede que la solicitud exceda el tiempo de espera antes de que la plataforma pueda iniciar el contenedor de código de encadenamiento.

## (Opcional) Establecimiento de valores de tiempo de espera en los SDK de Fabric
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

Sin embargo, es posible que tenga que cambiar los valores predeterminados de tiempo de espera en su propia aplicación. Por ejemplo, cuando la aplicación invoca una transacción que necesita más de 5000 ms, que es el valor de tiempo de espera predeterminado para la conexión de concentrador de sucesos, para responder, es posible que reciba un error porque el suceso de invocación termine en 5000 ms antes de que se complete la transacción. Puede establecer la propiedad del sistema para sobrescribir los valores predeterminados de la aplicación cliente. Dado que los valores predeterminados se han inicializado antes de establecer la propiedad de sistema, esta no tendrá efecto. Por lo tanto, debe establecer la propiedad del sistema para tiempo de espera excedido en una construcción estática en la aplicación cliente. Consulte el ejemplo siguiente sobre cómo cambiar el valor de tiempo de espera para la conexión del concentrador de sucesos a 15000 ms en el SDK de Java de Fabric. La vía de acceso de archivo es `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

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

## (Opcional) Utilización de índices de CouchDB
{: #couchdb-indices}

Si la red utiliza CouchDB y desea aprovechar la característica de indexación de CouchDB para mejorar el rendimiento de CouchDB, debe empaquetar los índices con el código de encadenamiento.

Para obtener más información sobre CouchDB y sobre cómo configurar índices, consulte [ CouchDB como base de datos de estado ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como base de datos de estado"){:new_window} en la documentación de Hyperledger Fabric. También encontrará un ejemplo que utiliza un índice con código de encadenamiento en esta [guía de aprendizaje de Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}.

<!--# Next steps-->

## Alojamiento de aplicaciones
{: #host-app}

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
3. Examine el directorio de la aplicación y envíe la aplicación emitiendo el siguiente mandato. Esto puede tardar varios minutos dependiendo del tamaño de la aplicación. Puede ver los registros de {{site.data.keyword.Bluemix_notm}} en el terminal. Los registros cesan cuando la aplicación se inicia correctamente.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	Puede comprobar los registros de la aplicación emitiendo uno de los mandatos siguientes:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`

## Cómo desconectar la aplicación de la red
{: #disconnect-app}

Siga los pasos siguientes para eliminar la conexión entre la aplicación y la red blockchain en {{site.data.keyword.cloud_notm}}.
1. Elimine la información de punto final de API del archivo de configuración de la aplicación. Para obtener más información, consulte [Adición de puntos finales de API de red a la aplicación](#api-endpoints).
2. Suprima el contenedor de código de encadenamiento.
  1. En la pantalla "Canal" del Supervisor de red, localice el canal en el que desea instalar el código de encadenamiento.
  2. En la pantalla del canal específico, localice el código de encadenamiento que desea inhabilitar.
  3. Pulse el botón **Suprimir** y haga clic en **Enviar** en el panel de supresión del código de encadenamiento. Se eliminará el contenedor del código de encadenamiento. ![Suprimir un código de encadenamiento](images/channel_chaincode.png "Suprimir un código de encadenamiento")

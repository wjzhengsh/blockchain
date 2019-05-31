---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: Fabric SDKs, client application, enroll, register, chaincode

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

# Desarrollo de aplicaciones con los SDK de Fabric
{: #dev-app}

{{site.data.keyword.blockchainfull}} Platform proporciona API que puede utilizar para conectar aplicaciones con la red blockchain. Puede utilizar los puntos finales de API en el perfil de conexión para invocar el código de encadenamiento y actualizar o consultar el libro mayor específicos del canal en sus iguales. También puede utilizar las API en [la interfaz de usuario de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) para gestionar nodos, canales y miembros de la red.
{:shortdesc}

Puede utilizar esta guía de aprendizaje para obtener información sobre cómo acceder a las API de {{site.data.keyword.blockchainfull_notm}} Platform y utilizarlas para inscribirse y registrar la aplicación con la red. También aprenderá a interactuar con la red y a enviar transacciones desde la aplicación. La guía de aprendizaje está basada en la guía de aprendizaje
[Cómo escribir su primera aplicación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "Cómo escribir su primera aplicación"){:new_window} de la documentación de Hyperledger Fabric. Utilizará muchos de los archivos y mandatos de la guía de aprendizaje **Cómo escribir su primera aplicación**, pero los utilizará para interactuar con una red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En esta guía de aprendizaje se describe cada paso del desarrollo de aplicaciones utilizando el SDK de nodo de Fabric Manager Hyperledger. También aprenderá a inscribir y a registrar usuarios utilizando el cliente de CA de Fabric como alternativa a la utilización del SDK.

Además de esta guía de aprendizaje, puede utilizar aplicaciones de ejemplo y código de encadenamiento que proporciona {{site.data.keyword.blockchainfull_notm}} Platform como plantillas cuando crea sus propias soluciones de negocio. Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).

Cuando esté preparado para escalar su aplicación, visite [Prácticas recomendadas para el desarrollo de aplicaciones](/docs/services/blockchain/best_practices.html#best-practices-app).

## Requisitos previos
{: #dev-app-prerequisites}

Debe cumplir los siguientes requisitos previos para poder utilizar la guía de aprendizaje **Cómo escribir su primera aplicación** en la plataforma {{site.data.keyword.blockchainfull_notm}}.

- Si no tiene una red blockchain en {{site.data.keyword.cloud_notm}}, debe crear una con un plan inicial o empresarial. Para obtener más información, consulte [Creación
de una red del Plan inicial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network) o [Creación de una red del Plan empresarial](/docs/services/blockchain/get_start.html

  Después de entrar en el supervisor de red de su red, añada al menos un igual para su organización en la pantalla "Visión general". A continuación, cree al menos un canal en la red. Para obtener más información, consulte [Creación de un canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel). **Tenga en cuenta** que si utiliza una red de plan inicial, la red ya tiene un canal llamado `defaultchannel` que puede utilizar para desplegar el código de encadenamiento.

- Instale las herramientas necesarias para descargar los ejemplos de Hyperledger Fabric y para utilizar el SDK de nodo.
  * [Curl ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") o [Git ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

- Instale los ejemplos de Hyperledger Fabric descargando el directorio `fabric-samples`. Puede seguir la
[guía de iniciación
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/install.html "Guía de iniciación"){:new_window} en la documentación de Hyperledger Fabric.

- Vaya al directorio `fabric-samples` de la máquina local.
  * Utilice el mandato `git checkout` para utilizar la rama que corresponda a la versión de Hyperledger Fabric de sus redes. Puede encontrar la versión de Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) en el supervisor de red.
    - Si la red está en Fabric versión 1.2, puede utilizar la rama principal.
    - Si la red está en Fabric versión 1.1, ejecute `git checkout v1.1.0`.
    - Si la red está en Fabric versión 1.0, ejecute `git checkout v1.0.6`.

  * Vaya a `fabric-samples/fabcar`. Puede hacer una copia de este directorio y cambiarle el nombre para poder probar la aplicación de ejemplo en un directorio limpio.

  * En el directorio `fabcar`, ejecute el mandato `npm install` para instalar los paquetes necesarios para utilizar el SDK de Fabric Manager, que incluyen `fabric-client` y `fabric-ca-client`.

- Instale y cree una instancia del código de encadenamiento de fabcar en el canal utilizando el
[Supervisor de red](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc). Encontrará el código de encadenamiento fabcar en la carpeta `fabric-samples` bajo `fabric-samples > chaincode > fabcar > go`.

- Recupere el perfil de conexión de red en la pantalla "Visión general" del supervisor de red. Guarde el perfil de conexión en el directorio `fabcar` y cámbiele el nombre por `creds.json`.

## Utilización de los SDK de Fabric
{: #dev-app-fabric-sdks}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista actualizada de los lenguajes soportados en la [Documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"){:new_window}. Se recomienda utilizar el SDK de nodo o el SDK de Java con la plataforma {{site.data.keyword.blockchainfull_notm}}. Encontrará más información sobre las API que proporcionan los SDK en los repositorios individuales de los SDK.

En esta guía de aprendizaje se utiliza el [SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de nodo"){:new_window} para registrar e inscribir la aplicación y luego utilizar la aplicación para emitir transacciones invocando y consultando el código de encadenamiento. En esta guía de aprendizaje se describe la información que debe proporcionar al SDK para que la aplicación pueda conectarse a la red blockchain. También contiene una introducción a algunas de las API que puede utilizar y cómo el SDK interactúa y envía las transacciones a la red blockchain.

## Adición de puntos finales de API a la aplicación
{: #dev-app-api-endpoints}

Debe proporcionar la aplicación los puntos finales de API de recursos de red específicos, incluidos clasificador, CA y nodos iguales, y la red blockchain en {{site.data.keyword.cloud_notm}}. La aplicación puede interactuar con la red a través de estos puntos finales de API. Encontrará los puntos finales de API en el perfil de conexión de su red. El perfil de conexión está en formato JSON y contiene la información de punto final de la API y los ID de inscripción/secretos de los recursos de red.

1. Recupere la información de punto final de API de los recursos de red desde el supervisor de red con uno de los métodos siguientes:
  * En la pantalla "Visión general", pulse **Perfil de conexión**. El perfil de conexión contiene un conjunto completo de información de punto final de API de todos los recursos de red.
    ![Perfil de conexión en el supervisor de red](images/service_credentials.png "Perfil de conexión en el supervisor de red")

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

Es posible que desee dirigir los recursos de red fuera de su organización con la aplicación. Por ejemplo, si una [política de aprobación](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-endorsement-policy) de código de encadenamiento requiere aprobaciones de otras organizaciones del canal, tiene que enviar la transacción para que los iguales de dichas organizaciones puedan cumplir con la política. [Service Discovery ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service Discovery") en Hyperledger Fabric no recibe soporte para el Plan inicial ni para el Plan empresarial. Tendrá que obtener la información de punto final de los iguales y los certificados TLS adjuntos de otras organizaciones utilizando la sección "peers" de su perfil de conexión. Puede ponerse en contacto con los administradores de las otras organizaciones para saber qué iguales se han unido a canales particulares.{:note}

3. Conecte la información de punto final de API a un archivo de configuración de la aplicación, como se muestra en el ejemplo siguiente:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```

  También puede enviar [solicitudes HEAD](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-nodes) a estos puntos finales para comprobar la disponibilidad de los recursos de red.

  Si utiliza los SDK de Fabric Manager, también puede conectarse a la red utilizando el perfil de conexión. Esta guía de aprendizaje proporciona la información de punto final de la red al SDK manualmente. Sin embargo, encontrará una guía de aprendizaje y directrices en el apartado sobre [utilización del perfil de conexión con el SDK](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) en una sección posterior.

## Inscripción de la aplicación
{: #dev-app-enroll}

Para poder conectar una aplicación a la red en la plataforma {{site.data.keyword.blockchainfull_notm}}, tiene que probar la autenticidad de la aplicación en la red. No profundizaremos en los detalles de los certificados x509 y de la infraestructura de claves públicas, pero puede obtener más información en la guía de aprendizaje sobre [Gestión de certificados en {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates). Pero, en pocas palabras, los flujos de comunicación en Fabric utilizan operaciones sign/verify en cada punto de encuentro. Por lo tanto, cualquier aplicación que envíe llamadas, como consultas o actualizaciones de libro mayor, a la red deberá debe firmar cargas útiles y con su clave privada y adjuntar un certificado x509 firmado adecuadamente para fines de verificación. **Inscripción** es el proceso de generación de las claves y certificados necesarios de la entidad emisora de certificados adecuada. Después de la inscripción, la aplicación está lista para comunicarse con la red.

En esta sección se explica cómo recuperar las claves y certificados con el SDK de nodo de Fabric utilizando el código de ejemplo que forma parte de la guía de aprendizaje **Cómo escribir su primera aplicación**. Solo puede generar certificados utilizando una identidad que se haya registrado con su entidad emisora de certificados. En la guía de aprendizaje siguiente se realiza primero la inscripción utilizando una identidad de administrador que ya se haya registrado con la CA. A continuación, utiliza estos certificados para registrar una nueva identidad de cliente. La guía de aprendizaje realiza de nuevo la inscripción utilizando la nueva identidad, y utiliza estos certificados para enviar transacciones a la red.
<!---You can find an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

También puede utilizar la pantalla "Entidad emisora de certificados" del supervisor de red para generar certificados, y utilizar dichos certificados para interactuar con la red. Para aprender cómo hacerlo, consulte [Generación de certificados utilizando el supervisor de red](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel). También puede aprender a utilizar el [cliente de CA de Fabric](/docs/services/blockchain/certificates.html#managing-certificates-enroll-register-caclient) desde la línea de mandatos para generar certificados y registrar usuarios en la guía de aprendizaje
[Gestión de certificados](/docs/services/blockchain/certificates.html#managing-certificates).

### Inscripción mediante el SDK de Fabric
{: #dev-app-enroll-sdk}

Desde el directorio `fabcar` de la carpeta `fabric-samples`, abra el archivo `enrollAdmin.js` en un editor de texto.

1. En primer lugar, el archivo crea una instancia del cliente de Fabric.
  ```
  var fabric_client = new Fabric_Client();
  ```
  {:codeblock}

2. Luego el archivo crea un almacén de claves y valores (KVS) para gestionar los certificados. El SDK utiliza la clase [KeyValueStore ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} para crear el almacén de claves y valores y la clase [CryptoSuite ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} para realizar los cálculos criptográficos. A continuación encontrará el bloque de código correspondiente.
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

3. Después de definir el KVS, puede utilizar varios métodos de la clase [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} y la API Fabric-CA-Client <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> para comunicar con el servidor de CA. Tendrá que especificar el SDK, el nombre y el URL de la entidad emisora de certificados. Abra el archivo JSON de **Perfil de conexión** desde la pantalla **Visión general** del Supervisor de red y busque las variables siguientes bajo la sección `certificateAuthorites`:
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

  Luego el cliente de Fabric comprueba si la aplicación ya está inscrita. **Edite** la línea siguiente que contiene `enrollID` desde el perfil de conexión:
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

Si desea [trabajar con la red utilizando el SDK](/docs/services/blockchain/v10_application.html#dev-app-operate-sdk), tiene que cargar el signCert de admin en la plataforma {{site.data.keyword.blockchainfull_notm}}. Encontrará signCert de admin en la carpeta `hfc-key-store`. Abra el archivo `admin` y copie el certificado dentro de las comillas después del campo `certificate`. Utilice una herramienta o un editor de texto para convertir el certificado al formato PEM. Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" de la pantalla "Miembro"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) en el Supervisor de red. Esto no es necesario si solo utiliza el SDK para invocar o consultar el código de encadenamiento.

## Registro de la aplicación
{: #dev-app-register}

Después de generar los certificados del lado del cliente, tiene que registrar la aplicación en la entidad emisora de certificados. Después de registrarla, la aplicación se añadirá a la lista de componentes que la red reconoce. Una práctica recomendada consiste en registrar la aplicación como una identidad independiente en lugar de utilizar `admin` para firmar solicitudes.

### Registro mediante el SDK
{: #dev-app-register-sdk}

Puede utilizar el archivo `registerUser.js` para registrar e inscribir la aplicación como `user1` utilizando el signCert de `admin`. Abra `registerUser.js` en un editor de texto.

1. Proporcione el URL y el nombre de la CA a una instancia nueva del cliente de CA de Fabric.

2. Proporcione el enrollID al método `getUserContext` de la
[clase del cliente de Fabric
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Clase del cliente de Fabric"){:new_window} para comprobar si se ha inscrito el administrador (admin) y se tiene permitido emitir esta solicitud. Edite el bloque de código correspondiente del archivo siguiendo el ejemplo siguiente:
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

3. Utilice el **cliente de CA de Fabric** para registrar e inscribir el usuario con la CA y luego utilice el **cliente de Fabric** para crear el nuevo signCert. **Edite** el siguiente bloque con su ID de MSP y la afiliación de su organización. Encontrará sus `x-affiliations` en la sección de entidades emisoras de certificados de sus credenciales de red y puede utilizar cualquier afiliación que aparezca en la lista. Añada el nombre del usuario que desea crear. En el ejemplo fabcar se utiliza `user1` de forma predeterminada.
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

### Registro mediante el supervisor de red

Como alternativa, puede registrar e inscribir la aplicación cliente utilizando el separador **Entidad emisora de certificados** del supervisor de red. Consulte esta [información](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-ca) para ver más instrucciones.

## Envío de transacciones mediante la invocación y consulta de código de encadenamiento
{: #dev-app-invoke-query}

La aplicación tiene que interactuar con la red completa de blockchain para enviar una transacción.

1. La aplicación envía una propuesta de transacción para que la aprueben los iguales del canal.
2. Las iguales que la aprueban devuelven la transacción aprobada a la aplicación.
3. La aplicación envía la transacción aprobada al servicio de ordenación para añadir la transacción al libro mayor.

Para obtener más información sobre el flujo completo de la transacción, consulte [Flujo de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flujo de transacción"){:new_window} en la documentación de Hyperledger Fabric. Después de consultar esta guía de aprendizaje, visite la sección sobre [conectividad y disponibilidad de las aplicaciones](/docs/services/blockchain/best_practices.html#best-practices-app-connectivity-availability) para ver consejos de gestión sobre cómo el SDK interactúa con la red.

En los ejemplos siguientes se muestra cómo el SDK de nodo configura la topología de red, define la propuesta de transacción y luego envía la transacción a la red. Puede utilizar el archivo `invoke.js` para invocar funciones dentro del código de encadenamiento `fabcar`. Estas funciones le permiten crear y transferir activos en el libro mayor de blockchain. En esta guía de aprendizaje se utiliza la función `initLedger` para
añadir datos nuevos a su canal y luego se utiliza el archivo `query.js` para consultar los datos.

### Invocando código de encadenamiento
{: #dev-app-invoke}

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

  Las nuevas variables de igual y clasificador abre conexiones GRPC con la red blockchain. Para obtener más información sobre la gestión de estas conexiones, consulte [Apertura y cierre de conexiones de red](/docs/services/blockchain/best_practices.html#best-practices-app-connections).

  Cuando añade el URL del igual al método `fabric_client.newPeer`, también importa los certificados TLS relevantes del perfil de conexión utilizando el fragmento de código siguiente. Hizo lo mismo cuando añadió el URL de servicio de ordenación. Debe utilizar estos certificados TLS para autenticar la comunicación con la red.
  ```
  { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null}
  ```
  {:codeblock}

  Si la política de aprobación requiere que las transacciones sean aprobadas por otras organizaciones del canal, debe añadir los iguales de dichas organizaciones mediante los métodos `newPeer()` y `channel.addPeer()` cuando configure la red. Las organizaciones tienen que enviarle la lista de los iguales que se han unido a un determinado canal. La información de punto final y los certificados TLS estarán disponibles en el perfil de conexión. El SDK enviará la transacción a todos los iguales añadidos al canal.

  También puede añadir iguales adicionales pertenecientes a su organización que se unan al canal como un paso del proceso para [hacer que la aplicación esté altamente disponible](/docs/services/blockchain/best_practices.html#best-practices-ha-app). Esto proporcionará el SDK con una función de migración tras error en el caso de que uno de sus iguales se interrumpa.

3. Después de configurar la red fabric y de importar la identidad de la aplicación y el signCert en el paso de registro, el archivo `invoke.js` define la propuesta que enviará a la red. Puede utilizar la función `initLedger` en el código de encadenamiento `fabcar` para añadir algunos datos iniciales a su libro mayor. También puede editar el bloque de código para invocar otras funciones que encontrará en el código de encadenamiento `fabcar`.
  ```
  var request = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'defaultchannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Después de definir la solicitud, puede enviar una [propuesta de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") a los iguales del canal. Una vez que los iguales devuelvan la propuesta, puede [enviar la transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") al servicio de ordenación.

4. Puede añadir un servicio de sucesos para hacer que el flujo de transacciones sea más eficiente. **Edite** la siguiente sección:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Aunque en el ejemplo se utiliza un servicio de sucesos basado en iguales, debe utilizar un escucha basado en canal. Encontrará más información en la sección sobre [gestión de transacciones](/docs/services/blockchain/best_practices.html#best-practices-managing-transactions) y en la [documentación de SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "servicio de sucesos basado en canal"){:new_window}.

5. De forma predeterminada, `invoke.js` envía la transacción como `user1`. Puede editar el archivo `invoke.js` si ha registrado un nombre distinto.

Cuando termine de editar el archivo, emita el mandato `node invoke.js` para enviar la transacción a la red. Debería ver lo siguiente como confirmación de que la invocación ha sido satisfactoria.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Esto indica que la aplicación ha invocado correctamente el código de encadenamiento y ha añadido los datos al libro mayor.

### Consulta del código de encadenamiento
{: #dev-app-query}

Ahora puede utilizar `query.js` para leer el libro mayor. Abra el archivo `query.js` en un editor de texto.

1. Añada `var creds = require('./creds.json')` al principio del archivo.
2. Actualice el archivo con el nombre de canal y la información de punto final del igual. Debido a que esta operación solo lee los datos almacenados en el igual, no es necesario añadir la información de punto final del servicio de ordenación. `query.js` da por supuesto que envía la propuesta como `user1`.
  ```
  var channel = fabric_client.newChannel('defaultchannel');
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

Para obtener más información sobre la aplicación fabcar y las funciones que utiliza, visite la guía de aprendizaje sobre [Cómo escribir su primera aplicación ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "cómo escribir su primera aplicación"){:new_window} en la documentación de Hyperledger Fabric.

## Utilización del perfil de conexión con el SDK
{: #dev-app-connection-profile}

En lugar de importar la información de punto final a la red manualmente, puede hacer que el SDK se conecte a la red utilizando el **Perfil de conexión** desde la pantalla **Visión general** del supervisor de red. Esto agiliza el proceso de conectar con la entidad emisora de certificados para el proceso de inscripción y registro. También elimina la necesidad de definir la red fabric antes de enviar una transacción. El SDK encontrará los iguales y clasificadores en el canal relevante directamente desde el perfil de conexión. Encontrará información sobre cómo utilizar un perfil de conexión en la [documentación del SDK de nodo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "guía de aprendizaje del perfil de conexión"){:new_window}.

Podemos utilizar el archivo `invoke.js` como ejemplo para ver la eficacia de utilizar un perfil de conexión en lugar de puntos finales manuales. Puede establecer una nueva instancia del cliente de Fabric utilizando la clase `loadFromConfig`. Sustituya `var fabric_client = new Fabric_Client();` por el código siguiente.
```
var fabric_client = Fabric_Client.loadFromConfig(path.join(__dirname, './connection-profile.json'));
```
{:codeblock}

En lugar de configurar la red fabric mediante la creación de iguales y clasificadores, y luego añadiéndolos a un canal, puede utilizar la línea siguiente para crear un nuevo canal.

```
var channel = fabric_client.newChannel('defaultchannel');
```
{:codeblock}

A continuación, el SDK añade los iguales y el servicio de ordenación que definidos en el canal utilizando el perfil de conexión. Esto hace que la escritura de aplicaciones sea más eficientes y facilita la actualización de las aplicaciones a medida que los miembros de la red se unen, se marchan e inician nuevos canales. Consulte la [guía de aprendizaje del perfil de conexión ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "guía de aprendizaje del perfil de conexión"){:new_window} de la documentación del SDK de nodo para obtener más información sobre los pasos adicionales a seguir. Puede utilizar esta [versión de la guía de aprendizaje de fabcar ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/tutorials/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/){:new_window} que utiliza un perfil de conexión en lugar de conexiones de punto final manuales.

[Service Discovery ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Service Discovery") para una política de aprobación en Hyperledger Fabric no recibe soporte para el Plan inicial ni para el Plan empresarial. Sin embargo, puede enviar transacciones a iguales que están fuera de su organización para su aprobación editando el perfil de conexión. El perfil de conexión ya contiene la información de punto final y los certificados TLS de los iguales de otras organizaciones de la red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Añada el nombre del igual al canal relevante en la sección "channels" del perfil para añadir el igual al canal. Se tendrá que poner en contacto con los administradores de las otras organizaciones para saber qué iguales se han unido a canales particulares.

## Generación de certificados mediante el supervisor de red
{: #dev-app-enroll-panel}

Puede utilizar el supervisor de red para generar certificados utilizando la identidad de administrador y, a continuación, pasar dichos certificados directamente al SDK. Esto implica que puede empezar a interactuar con la red rápidamente, sin tener que generar certificados utilizando el SDK.

Vaya al panel "Entidad emisora de certificados" del supervisor de red. Pulse el botón **Generar certificado** situado junto a la identidad de administrador para obtener un nuevo signCert y una clave privada de la entidad emisora de certificados. El campo **Certificado** contiene el signCert, justo encima de la **Clave privada**. Puede pulsar el icono de copia que hay al final de cada campo para copiar el valor. Guarde estos certificados en un lugar desde el que pueda proporcionarlos a la aplicación. **Tenga en cuenta** que {{site.data.keyword.blockchainfull_notm}} Platform no almacena estos certificados. Tiene que guardarlos y almacenarlos de forma segura.

El signCert y la clave privada son suficientes para formar un contexto de usuario que pueda firmar solicitudes dentro del SDK de Node. Utilice el método [createUser ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html#createUser__anchor "Crear usuario"){:new_window} de la clase de cliente (Client) para crear el objeto de contexto de usuario. Dentro del método `creatUser`, pase el nombre de identidad y el mspid al objeto de [usuario ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/global.html#UserOpts "Usuario"){:new_window}, junto con las vías de acceso a la clave privada y al signCert al objeto [cryptoContent ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/global.html#CryptoContent "Contenido criptográfico"){:new_window}.

Podemos utilizar el panel "Entidad emisora de certificados" y la clase `createUser` como parte de la guía de aprendizaje de desarrollo de aplicaciones como ejemplo. Si ya ha seguido la guía de aprendizaje, habrá instalado y creado una instancia del código de encadenamiento de `fabcar`, y habrá añadido algunos datos al libro mayor. Podemos utilizar los certificados para consultar el libro mayor como usuario `admin`. Siga las instrucciones anteriores para generar certificados utilizando el supervisor de red si no lo ha hecho aún.

Guarde la clave privada como privateKey.pem y el signCert como certificate.pem en el mismo directorio que `query.js`. Abra `query.js` en un editor de texto. Añada la línea siguiente al principio del archivo:
```
var fs = require('fs');
```
Sustituya la línea siguiente que importa el contexto de usuario de la persistencia,
```
return fabric_client.getUserContext('user1', true);
```
por el código siguiente que crea un nuevo contexto de usuario que utiliza nuestro signCert y la clave privada.
```
return fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
El fragmento de código anterior lee los certificados directamente en la clase `cryptoContent` como archivos PEM. El nombre de usuario será `admin`, ya que los certificados se han generado utilizando la identidad `admin`. Puede encontrar el mspid en la sección `certificateAuthorites` del perfil de conexión. Guarde el archivo y emita el mandato `node query.js`. Si tiene éxito, la consulta devolverá los mismos resultados que antes.

## (Opcional) Funcionamiento de la red mediante el SDK
{: #dev-app-operate-sdk}

También puede utilizar el SDK para hacer funcionar la red blockchain. En esta guía de aprendizaje se explica cómo puede utilizar el SDK para unir a sus iguales a los canales, instalar el código de encadenamiento en sus iguales y crear una instancia del código de encadenamiento en los canales. Estos pasos son opcionales porque también puede realizar estas operaciones con el supervisor de red o con las API en la [interfaz de usuario de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) si todos los iguales se ejecutan en la plataforma {{site.data.keyword.blockchainfull_notm}}.

Debe cargar el signCert de administrador en {{site.data.keyword.blockchainfull_notm}} Platform para poder llevar a cabo estos pasos. Encontrará instrucciones sobre cómo cargar el signCert al final de la [sección sobre inscripción](/docs/services/blockchain/v10_application.html#dev-app-enroll-sdk)

### Cómo unir un canal
{: #dev-app-join-channel-sdk}

Después de que la organización cree o se una a un canal utilizando el supervisor de red o las API, puede utilizar el SDK para unir el igual al canal.

1. [Capte el bloque de origen ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "captar bloque de origen"){:new_window} del canal desde el servicio de ordenación.
2. Pase el bloque de origen al método [unir canal ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} para unir su igual al canal.

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

Es necesario añadir el signCert al canal antes de recuperar el bloque de origen. Si ha generado certificados después de que la organización se uniera al canal, deberá cargar el signCert en la plataforma y, a continuación, pulsar el botón **Sincronizar certificados** en la pantalla "Canales". Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir el mandato para unirse al canal (join channel). Para obtener más información, consulte [Carga de certificados de firma en {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs)
en la guía de aprendizaje sobre [Gestión de certificados](/docs/services/blockchain/certificates.html#managing-certificates).

### Instalación de código de encadenamiento
{: #dev-app-install-cc-sdk}

Puede utilizar el método para [instalar código de encadenamiento ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} de la clase [Fabric Client ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} para instalar el código de encadenamiento en el igual.

Para utilizar el ejemplo `fabcar` para instalar el código de encadenamiento `fabcar` en el igual, utilice el archivo `query.js` como base y edítelo. Tiene que enviar esta solicitud como administrador en lugar de la aplicación, por lo que debe sustituir `user1` por `admin` en el método `getUserContext`. Sustituya el objeto de propuesta de transacción por una solicitud de instalación de código de encadenamiento utilizando el ejemplo siguiente:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel'
	 };
```
{:codeblock}

Envíe este objeto a `return fabric_client.installChaincode(request);`, en lugar de la línea `return channel.queryByChaincode (request);` que aparece actualmente en el archivo.

### Creación de una instancia de un código de encadenamiento
{: #dev-app-instantiate-cc-sdk}

Para crear una instancia del código de encadenamiento, tiene que enviar una [propuesta de creación de instancia ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} al igual y luego enviar una [solicitud de transacción ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} al servicio de ordenación.

Para utilizar el ejemplo `fabcar` para crear una instancia del código de encadenamiento, utilice el archivo `invoke.js` como punto de partida. Tiene que enviar esta solicitud como administrador en lugar de la aplicación, por lo que debe sustituir `user1` por `admin` en el método `getUserContext`. Sustituya el objeto de propuesta de transacción por una solicitud de instalación de código de encadenamiento utilizando el ejemplo siguiente:
```
var request = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'defaultchannel',
    txId : tx_id
};
```
{:codeblock}

Envíe esta solicitud a `return channel.sendInstantiateProposal(request);` en lugar de `return channel.sendTransactionProposal(request);` que aparece actualmente en el archivo. Después de enviar la solicitud de creación de instancia al canal, deberá enviar la propuesta de aprobación como una transacción al servicio de ordenación. Este utiliza los mismos métodos que para enviar una transacción, por lo que puede dejar el resto del archivo sin cambios. Es posible que desee [aumentar el valor de tiempo de espera](/docs/services/blockchain/best_practices.html#best-practices-set-timeout-in-sdk) en la propuesta de creación de instancia. De lo contrario, puede que la solicitud exceda el tiempo de espera antes de que la plataforma pueda iniciar el contenedor de código de encadenamiento.

Es necesario añadir el signCert al canal para poder crear una instancia del código de encadenamiento. Si ha generado certificados después de unirse al canal, deberá cargar el signCert en la plataforma y, a continuación, pulsar el botón **Sincronizar certificados** en la pantalla "Canales". Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir el mandato para crear una instancia del código de encadenamiento (instantiate chaincode). Para obtener más información, consulte [Carga de certificados de firma en {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates-upload-certs) en la guía de aprendizaje sobre [Gestión de certificados](/docs/services/blockchain/certificates.html#managing-certificates).

## Cómo desconectar la aplicación de la red
{: #dev-app-disconnect-app}

Siga los pasos siguientes para eliminar la conexión entre la aplicación y la red blockchain en {{site.data.keyword.cloud_notm}}.
1. Elimine la información de punto final de API del archivo de configuración de la aplicación. Para obtener más información, consulte [Adición de puntos finales de API de red a la aplicación](/docs/services/blockchain/v10_application.html#dev-app-api-endpoints).
2. Suprima el contenedor de código de encadenamiento.
  1. En la pantalla "Canal" del Supervisor de red, localice el canal en el que desea instalar el código de encadenamiento.
  2. En la pantalla del canal específico, localice el código de encadenamiento que desea inhabilitar.
  3. Pulse el botón **Suprimir** y haga clic en **Enviar** en el panel de supresión del código de encadenamiento. Se eliminará el contenedor del código de encadenamiento. ![Suprimir un código de encadenamiento](images/channel_chaincode.png "Suprimir un código de encadenamiento")

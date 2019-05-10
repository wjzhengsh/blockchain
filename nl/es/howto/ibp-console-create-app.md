---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Creación de aplicaciones
{: #ibp-console-app}

Después de instalar contratos inteligentes y de desplegar nodos, puede utilizar aplicaciones cliente para realizar transacciones con otros miembros de la red. Las aplicaciones pueden invocar la lógica empresarial contenida en los contratos inteligentes para crear, transferir o actualizar activos en el libro mayor de blockchain. Utilice esta guía de aprendizaje para aprender a utilizar las aplicaciones cliente para interactuar con las redes que gestiona desde la consola de {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}

**Audiencia de destino:** este tema está diseñado para los desarrolladores de aplicaciones que estén interesados en aprender más sobre cómo crear una aplicación cliente que interactúe con una red blockchain.

## Recursos de aprendizaje
{: #ibp-console-app-learning-resources}

Puede obtener más información sobre cómo funcionan juntos las aplicaciones y los contratos inteligentes si visita el [tema sobre desarrollo de aplicaciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "desarrollo de aplicaciones") de la documentación de Hyperledger Fabric. El tema explora un caso de uso hipotético en el que los bancos y las empresas intercambian un documento comercial y describe cómo se codifican las transacciones en los contratos inteligentes. La [guía de aprendizaje del documento comercial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "guía de aprendizaje de documento comercial") amplía este tema y le permite desplegar los contratos inteligentes en la red de Fabric y utilizar el código de aplicación de ejemplo para crear y transferir el documento comercial entre los miembros de la red.

**Ejemplos de aplicaciones y guías de aprendizaje**

|  Ejemplo  |  Descripción    |  Dificultad    | Lenguaje | Ubicación |
| -----------------|---------|---------|---------|
| [FabCar ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/patterns/write-a-smart-contract-for-the-fabcarcommercial-paper-or-iks-cluster-with-saas-v2-beta-network-think/ "Blockchain")| Utilización de un clúster IKS para desplegar un contrato inteligente sencillo de la red Fabric en Blockchain Platform. | Intermedia | Node.js | Documentación de DeveloperWorks|
| [Documento comercial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "Documento comercial")| Utilización de un contrato inteligente de documento comercial para comercializar en una instancia local de Hyperledger Fabric. | Intermedia | Node.js | Documentación de Hyperledger Fabric|
| **Próximamente:** Documento comercial avanzado.  | Muestra avanzada con prácticas recomendadas sobre propiedad y privacidad utilizando colecciones de datos privados y recomendaciones basadas en estado. | Avanzada | Node.js | Documentación de Hyperledger Fabric|
| [Ejecución de un contrato inteligente de documento comercial con la extensión VSCode de {{site.data.keyword.blockchainfull_notm}} ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://developer.ibm.com/tutorials/run-commercial-paper-smart-contract-with-ibm-blockchain-vscode-extension/ "Ejecución de un contrato inteligente de documento comercial con VSCode")|  Aprenda a utilizar la extensión VScode para instalar e invocar el contrato inteligente de documento comercial en una instancia local de Hyperledger Fabric. | Intermedia | Node.js | IBM Developer|
| [ejecución de una muestra de documento comercial en {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper)| Aprenda a desplegar e invocar el contrato de documento comercial en una red de {{site.data.keyword.blockchainfull_notm}} Platform 2.0. | Intermedia |  Node.js | Documentación de {{site.data.keyword.blockchainfull_notm}} Platform|  

El desarrollo de una aplicación puede requerir la coordinación entre dos usuarios distintos de la red, el operador de la red y el desarrollador de aplicaciones:
- **El operador de la red** es el administrador que utiliza la consola de {{site.data.keyword.blockchainfull_notm}} Platform para desplegar los nodos de la organización e instala los contratos inteligentes en la red.
- **El desarrollador de aplicaciones** crea la aplicación cliente que consumirán los usuarios finales. El desarrollador utiliza los [SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html#hyperledger-fabric-sdks "SDK de Hyperledger Fabric"){:new_window} para invocar las transacciones escritas en los contratos inteligentes.

Si es el **operador de la red**, tendrá que seguir los siguientes pasos para que el desarrollador de aplicaciones pueda interactuar con la red:
1. Utilice la CA de la organización para [registrar una identidad de aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities).
2. [Descargue el perfil de conexión](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) desde el panel de contratos inteligentes.
3. Envíe al desarrollador de aplicaciones los siguientes objetos e información:
  - El ID y el secreto de inscripción de la identidad de la aplicación.
  - El perfil de conexión.
  - El nombre del contrato inteligente.
  - El nombre del canal en el que se ha creado la instancia del contrato inteligente.  

Si es el **desarrollador de aplicaciones**, utilice la información proporcionada por el operador de la red para completar los pasos siguientes:
1. Genere un par de claves pública y privada utilizando el ID y el secreto de inscripción de la identidad de la aplicación, junto con la información de punto final de CA dentro del perfil de conexión.
2. Utilice el perfil de conexión, el nombre de canal, el nombre del contrato inteligente y las claves de la aplicación para invocar el contrato inteligente.  

El desarrollador de aplicaciones puede utilizar dos modelos de programación para interactuar con la red:

**API de SDK de Fabric de alto nivel**

A partir de Fabric v1.4, los usuarios pueden beneficiarse de una aplicación simplificada y de un modelo de programación de contratos inteligentes. El nuevo modelo reduce el número de pasos y la cantidad de código que se necesita para enviar una transacción. Este modelo solo recibe soporte para las aplicaciones escritas en **Node.js**. Si desea aprovechar el nuevo modelo, puede utilizar esta guía de aprendizaje para completar las acciones siguientes en una red de {{site.data.keyword.blockchainfull_notm}} Platform 2.0:

- [Genere certificados para la aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-enroll) utilizando el SDK.
- [Invoque un contrato inteligente desde el SDK](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-invoke).
- Utilice la [extensión de VScode de IBM Blockchain](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) para escribir, desplegar y desarrollar y probar de forma iterativa sus contratos inteligentes.
- Dispondrá de más información sobre el desarrollo de aplicaciones si despliega la [guía de aprendizaje de documento comercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) en los nodos gestionados desde la consola. En esta guía de aprendizaje se proporciona más información sobre cómo utilizar pasarelas y carteras de Fabric.


**API de SDK de Fabric de bajo nivel**

Si desea seguir utilizando su contrato inteligente y su código de aplicación existente, o si desea utilizar otros lenguajes de SDK de Fabric que proporciona la comunidad de Hyperledger, puede utilizar las [API de SDK de Fabric de bajo nivel](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level) para conectar a la red.

## Registro de una identidad de aplicación
{: #ibp-console-app-identities}

Las aplicaciones deben firmar las transacciones que envían a los nodos de {{site.data.keyword.blockchainfull_notm}} y adjuntar una clave pública que utilizan los nodos para verificar que las transacciones están siendo enviadas por la parte adecuada. Esto garantiza que envían transacciones las organizaciones que tienen permiso para participar.

El operador de la red debe utilizar la CA de la organización para registrar una identidad de aplicación, que el desarrollador de aplicaciones puede utilizar para generar una clave pública y privada. El operador puede proporcionar el ID y el secreto de inscripción de la identidad, junto con la información de punto final de CA, que utilizará el SDK para generar certificados. Mediante la inscripción en el lado del cliente, el desarrollador de aplicaciones garantiza que ninguna otra parte tiene acceso a la clave privada de la aplicación. Durante el registro, el operador de la red puede establecer un límite de inscripción de uno para reforzar la seguridad. Una vez que el desarrollador de aplicaciones se inscribe, no se puede utilizar el ID ni el secreto de inscripción para generar otra clave privada.

Si no está tan preocupado por la seguridad, el operador de la red puede inscribir una identidad de aplicación utilizando el [separador CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll). A continuación, el operador puede descargar la identidad o exportarla a la cartera de la consola. Para poder utilizar los certificados del SDK, las claves se deben decodificar de base64 en formato PEM. Puede decodificar certificados con el mandato siguiente en la máquina local:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

## Descarga del perfil de conexión
{: #ibp-console-app-profile}

Las aplicaciones solo pueden enviar transacciones a los contratos inteligentes de los que se han creado instancias en los canales. Como resultado, la información que necesita para conectar e interactuar con un contrato inteligente se encuentra en la lista de contratos inteligentes de los que se han creado instancias en la consola. Esto significa que ya debe haber instalado y creado una instancia del contrato inteligente.

El [flujo de transacciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Flujo de transacciones"){:new_window} de Hyperledger Fabric abarca varios componentes; las aplicaciones cliente recopilan aprobaciones de los iguales y envían transacciones aprobadas al servicio de ordenación. El perfil de conexión proporciona a la aplicación los puntos finales de los iguales y los nodos de ordenación que necesita para enviar una transacción. También contiene información sobre la organización, como las entidades emisoras de certificados y el ID de MSP. Los SDK de Fabric pueden leer el perfil de conexión directamente, sin tener que escribir código que gestione el flujo de transacciones y aprobaciones.

Para aprovechar el servicio de la característica [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery") de Hyperledger Fabric, debe configurar iguales de ancla. Service Discovery permite a la aplicación saber qué iguales del canal externo a la organización necesitan aprobar una transacción. Sin el descubrimiento de servicios, tendrá que obtener la información de punto final de estos iguales fuera de banda de otras organizaciones y añadirla al perfil de conexión. Para obtener más información sobre cómo configurar iguales de ancla, consulte el paso tres del [tema sobre datos privados](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) de la guía de aprendizaje sobre cómo desplegar un contrato inteligente.

Vaya al separador de contratos inteligentes de la consola de la plataforma. Junto a cada contrato inteligente del que se ha creado una instancia, vaya al menú de desbordamiento. Pulse el botón **Conectar con SDK**. Se abrirá un panel lateral que le permitirá crear y descargar el perfil de conexión. En primer lugar, debe seleccionar la CA de la organización que ha utilizado para registrar la identidad de la aplicación. También tendrá que seleccionar la definición de MSP de la organización. Luego podrá descargar el perfil de conexión que puede utilizar para generar certificados y para invocar el contrato inteligente.

## Inscripción mediante el SDK
{: #ibp-console-app-enroll}

Una vez que el operador de red proporciona el ID y el secreto de inscripción de la identidad de la aplicación y del perfil de conexión de red, un desarrollador de aplicaciones puede utilizar los SDK de Fabric o el cliente de CA de Fabric para generar certificados del lado del cliente. Puede seguir los pasos siguientes para inscribir una identidad de aplicación utilizando el [SDK de Fabric para Node.js ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de Fabric para Node.js").

1. Guarde el perfil de conexión en la máquina local y cámbiele el nombre por `connection.json`.
2. Guarde el bloque de código siguiente como `enrollUser.js` en el mismo directorio que el perfil de conexión:

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, 'connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

      // Crear un nuevo cliente de CA para interactuar con la CA.
      const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
      const ca = new FabricCAServices(caURL);

      // Crear un nuevo sistema de archivos basado en la cartera para gestionar identidades.
      const walletPath = path.join(process.cwd(), 'wallet');
      const wallet = new FileSystemWallet(walletPath);
      console.log(`Wallet path: ${walletPath}`);

      // Comprobar si ya se ha inscrito el usuario administrador.
      const userExists = await wallet.exists('user1');
      if (userExists) {
        console.log('An identity for "user1" already exists in the wallet');
        return;
      }

      // Inscribir el usuario administrador e importar la nueva identidad en la cartera.
      const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
      const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
      await wallet.import('user1', identity);
      console.log('Successfully enrolled client "user1" and imported it into the wallet');

      } catch (error) {
        console.error(`Failed to enroll "user1": ${error}`);
        process.exit(1);
      }
    }

    main();
    ```
    {:codeblock}

3. Edite `enrollUser.js` para sustituir los valores siguientes:
  - Sustituya ``<CA_Name>`` por el nombre de la CA de la organización. Encontrará el nombre de la CA en la sección "organizations" del perfil de conexión bajo "Certificate Authorities". No utilice "caName" en la sección "Certificate Authorities".
  - Sustituya ``<app_enroll_id>`` por el ID de inscripción de la aplicación proporcionado por el operador de la red.
  - Sustituya ``<app_enroll_secret>`` por el secreto de inscripción de la aplicación proporcionado por el operador de la red.
  - Sustituya ``<msp_id>`` por el ID de MSP de la organización. Encontrará su ID de MSP en la sección "organizations" del perfil de conexión.
4. Vaya a `enrollUser.js` utilizando un terminal y ejecute `node enrollUser.js`. Si el mandato se ejecuta correctamente, debería ver la salida siguiente:

  ```
  Successfully enrolled client "user1" and imported it into the wallet
  ```
  El SDK creará y almacenará los certificados en el directorio `wallet/user1/` que crea el mandato. Este directorio es la cartera del sistema de archivos que se utiliza para enviar futuras transacciones.

Las carteras que utilizan los SDK de Fabric son distintas de la cartera de la consola de {{site.data.keyword.blockchainfull_notm}} Platform. El SDK no puede utilizar directamente las identidades almacenadas en la cartera de la consola.
{: note}

## Invocación de un contrato inteligente mediante el SDK
{: #ibp-console-app-invoke}

Después de haber generado la clave pública y privada de la aplicación y de haberlas almacenado en una cartera, estará listo para enviar una transacción. Debe conocer el nombre del contrato inteligente y el nombre del canal en el que se ha creado la instancia. Puede seguir los pasos siguientes para invocar un contrato inteligente mediante el [SDK de Fabric para Node.js ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de Fabric para Node.js").


1. Guarde el archivo siguiente en la máquina local como `invoke.js`. Guarde el archivo en el mismo directorio que `enrollUser.js`.

    ```
    'use strict';

    const { FileSystemWallet, Gateway } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    async function main() {
      try {

        // Analizar el archivo de conexión. Será la vía de acceso al archivo descargado
        // desde la consola operativa de IBM Blockchain Platform.
        const ccpPath = path.resolve(__dirname, 'connection.json');
        const ccp = JSON.parse(fs.readFileSync(ccpPath, 'utf8'));

        // Configurar una cartera. Esta cartera debe estar preparada con una identidad que la
        // aplicación pueda utilizar para interactuar con el nodo igual.
        const walletPath = path.resolve(__dirname, 'wallet');
        const wallet = new FileSystemWallet(walletPath);

        // Crear una nueva pasarela y conectar con los nodos iguales de la pasarela. La identidad
        // especificada debe existir en la cartera especificada.
        const gateway = new Gateway();
        await gateway.connect(ccp, { wallet, identity: 'user1' , discovery: {enabled: true, asLocalhost:false }});

        // Obtener el canal de red en el que se ha desplegado el contrato inteligente.
        const network = await gateway.getNetwork('<channel_name>');

        // Obtener el contrato inteligente del canal de red.
        const contract = network.getContract('<smart_contract_name>');

        // Enviar la transacción 'createCar' al contrato inteligente y esperar a que se
        // confirme en el libro mayor.
        await contract.submitTransaction('createCar', 'CAR12', 'Honda', 'Accord', 'Black', 'Tom');
        console.log('Transaction has been submitted');

        await gateway.disconnect();

        } catch (error) {
          console.error(`Failed to submit transaction: ${error}`);
          process.exit(1);
        }
      }
    main();
    ```
    {:codeblock}

2. Edite `invoke.js` para sustituir los valores siguientes:
  - Sustituya ``<channel_name>`` por el nombre del canal en el que se ha creado la instancia del contrato inteligente. Encontrará el nombre de su CA en la sección "Certificate Authorities" del perfil de conexión.
  - Sustituya ``<smart_contract_name>`` por el nombre del contrato inteligente del que se ha creado una instancia. Obtenga este valor del operador de la red.
  - Edite el contenido de `submitTransaction` para invocar una función dentro de su contrato inteligente. El archivo `invoke.js`
se ha escrito para invocar el [contrato inteligente fabcar ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric-samples/tree/release-1.4/chaincode/fabcar). Si desea ejecutar el archivo siguiente para enviar una transacción, instale fabcar y cree una instancia del contrato inteligente en uno de los canales.

3. Vaya a `invoke.js` utilizando un terminal y ejecute `node invoke.js`. Si el mandato se ejecuta correctamente, debería ver la salida siguiente:

  ```
  Transaction has been submitted
  ```
  {:codeblock}
  Si accede al canal mediante la consola, podrá ver otro bloque añadido por la transacción.


## Conexión con la extensión VScode de {{site.data.keyword.blockchainfull_notm}}
{: #ibp-console-app-vscode}

La extensión de Visual Studio Code de {{site.data.keyword.blockchainfull_notm}} Platform proporciona un entorno dentro de Visual Studio Code para desarrollar, empaquetar y desplegar paquetes de contratos inteligentes. Asegúrese de que tiene el perfil de conexión y el conjunto de archivos de claves generados mediante la CA. Luego puede utilizar la extensión VScode para conectarse a una red que esté gestionando mediante la consola.

Siga las instrucciones para descargar la [extensión VScode ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "extensión VScode") en Visual Studio Code Marketplace. Una vez que se haya instalado la extensión, abra VScode y podrá acceder a la extensión pulsando **Ver > Paleta de mandatos**. Especifique el mandato *IBM Blockchain Platform: Create Smart Contract Project* para crear un nuevo proyecto de contrato inteligente.

Una vez que haya creado el nuevo proyecto, puede conectarse directamente a la red desde el código de Visual Studio utilizando la información del contrato inteligente del que ha creado una instancia desde la consola. Utilice la tabla **Contratos inteligentes instanciados** en el separador Contratos inteligentes para descargar el [perfil de conexión](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) en el sistema de archivos local. A continuación, [cree una identidad de aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) y utilice la CA para crear y descargar un certificado (clave pública) y una clave privada. Siga las instrucciones anteriores para convertir la clave privada y el certificado al formato PEM. Una vez que haya descargado el perfil de conexión y las claves, siga los pasos siguientes para conectarse a la red.

1. Abra el separador _{{site.data.keyword.blockchainfull_notm}} Platform_ en Visual Studio Code.
2. En el panel _{{site.data.keyword.blockchainfull_notm}} Platform_, pulse **Añadir conexión nueva**.
3. Especifique el nombre de la conexión. Este nombre se visualizará en el panel _{{site.data.keyword.blockchainfull_notm}} Platform_.
4. Especifique la vía de acceso de archivo completa del perfil de conexión.
5. Especifique la vía de acceso de archivo completa del certificado (clave pública) en formato PEM.
6. Especifique la vía de acceso de archivo completa de la clave privada en formato PEM.
7. La conexión debería aparecer en la lista de conexiones bajo `local_fabric`. Realice una doble pulsación sobre el nombre de conexión para conectarse.

Una vez que esté conectado desde VScode, podrá ver la lista de iguales de la organización y los canales a los que se han unido en el panel de conexiones de blockchain. Podrá ver la lista de contratos inteligentes instalados bajo cada uno de los iguales. Para instalar un nuevo contrato inteligente desde el proyecto de la red, pulse en primer lugar con el botón derecho del ratón sobre un igual y seleccione **Instalar contrato inteligente**. Luego puede crear una instancia de dicho contrato inteligente pulsando con el botón derecho del ratón en un canal y seleccionando **Instalar contrato inteligente**. Encontrará más información sobre cómo utilizar la extensión de {{site.data.keyword.blockchainfull_notm}} Platform si visita la documentación de [Visual Studio Code Marketplace ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform#overview "extensión de VScode").


## Ejecución del ejemplo de documento comercial
{: #ibp-console-app-commercial-paper}

La [guía de aprendizaje de documento comercial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "guía de aprendizaje de documento comercial") de la documentación de Hyperledger Fabric muestra a los desarrolladores un caso de uso en el que varias partes compran, venden y canjean un documento comercial. Amplía el [tema sobre desarrollo de aplicaciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "desarrollo de aplicaciones") ofreciendo un código de aplicación y un contrato inteligente de ejemplo que le permiten crear y comercializar activos en una instancia local de Fabric.

También puede desplegar el código de ejemplo de la guía de aprendizaje de documento comercial en una red de {{site.data.keyword.blockchainfull_notm}} Platform 2.0. Esto le permitirá empezar a interactuar rápidamente con la red y utilizar el ejemplo para descargar las dependencias necesarias. El código de ejemplo también incluye ejemplos de cómo importar certificados en una [cartera ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "cartera") y cómo utilizar el perfil de conexión para crear una [pasarela de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "pasarela de Fabric"). La guía de aprendizaje puede ser utilizada por dos organizaciones diferentes para completar diferentes transacciones con un solo activo. Si ha utilizado la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) para desplegar dos organizaciones iguales conectadas a un canal, puede interactuar con la guía de aprendizaje utilizando las dos organizaciones.

Siga los pasos siguientes para desplegar el ejemplo en la red. Puede consultar la [guía de aprendizaje de documento comercial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "guía de aprendizaje de documento comercial") de la documentación de Fabric para obtener más detalles sobre los contratos inteligentes y la estructura de la aplicación.

### Requisitos previos

Para poder desplegar el ejemplo de documento comercial, tendrá instalar las herramientas necesarias en la máquina local:
  * [Git ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git"){:new_window}
  * [Node.js ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#node-js-runtime-and-npm "Node.js"){:new_window}

También tendrá que utilizar un editor de texto para editar y guardar los archivos del ejemplo. Puede utilizar muchos de los editores de alta calidad que están disponibles de forma gratuita, como [Atom ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://atom.io/ "atom"), [Sublime Text ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://www.sublimetext.com/ "Git") o [Brackets ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://brackets.io/ "Brackets").

### Paso uno: descargar el ejemplo

Para descargar el ejemplo de papel comercial, haga un clon del [repositorio de ejemplos de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric-samples "Repositorio de ejemplos de Fabric"):

```
git clone https://github.com/hyperledger/fabric-samples.git
```
{:codeblock}

Una vez que haya descargado los ejemplos de Fabric, ejecute los siguientes mandatos para asegurarse de que está utilizando la versión de los ejemplos compatible con Fabric v1.4.

```
cd fabric-samples
git checkout v1.4.0
```
{:codeblock}

Encontrará el ejemplo en la carpeta `commercial paper` de `fabric-samples`.

```
cd $HOME/fabric-samples/commercial-paper
```
{:codeblock}

La guía de aprendizaje contiene dos organizaciones de ejemplo, **magnetocorp** y **digibank**. Cada organización tiene su propio código de aplicación de ejemplo, almacenado en dos carpetas independientes bajo el directorio `organization`.

```
cd organization && ls
digibank	magnetocorp
```
{:codeblock}

En esta guía de aprendizaje, primero utilizará el código de la aplicación magnetocorp para emitir un documento comercial. Luego puede utilizar el código de aplicación de digibank para comprar y canjear el documento. Ambos directorios contienen el mismo contrato inteligente.

Vaya al código de la aplicación dentro del directorio magnetocorp.

```
cd magnetocorp/application
```
{:codeblock}

Cuando esté en el directorio, puede descargar las dependencias de la aplicación con el mandato siguiente:

```
npm install
```
{:codeblock}

### Paso dos: instalar y crear una instancia de un contrato inteligente

Encontrará el contrato inteligente del documento comercial dentro de la carpeta `contract` del directorio `digibank` y del directorio `magnetocorp`. Debe instalar este contrato inteligente en todos los iguales de las organizaciones siguiendo la guía de aprendizaje. A continuación, tendrá que crear una instancia del contrato del documento comercial en un canal. El contrato inteligente se debe empaquetar en [formato .cds ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "empaquetado de contratos inteligentes") para que se pueda instalar mediante la consola.

Puede utilizar la [extensión VScode de IBM Blockchain](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode) para empaquetar el contrato inteligente. Después de instalar la extensión, utilice Visual Studio Code para abrir la carpeta `contracts` en el espacio de trabajo. Abra el separador _{{site.data.keyword.blockchainfull_notm}} Platform_. En el panel _{{site.data.keyword.blockchainfull_notm}} Platform_, vaya a la sección de paquetes de contratos inteligentes y pulse **Empaquetar un proyecto de contrato inteligente**. La extensión VScode utilizará los archivos de la carpeta `contracts` para crear un nuevo paquete llamado `papernet-js@.0.0.1.cds`. Pulse con el botón derecho del ratón en este paquete para exportarlo al sistema de archivos local. Luego podrá utilizar la consola para [instalar los contratos inteligentes en sus iguales](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install) y, a continuación, [crear una instancia del contrato inteligente en un canal](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).

### Paso tres: generar certificados para la cartera

Las aplicaciones tienen que firmar las solicitudes que envían a los componentes de Fabric. Si los componentes no reconocen las organizaciones que envían las transacciones, las transacciones se rechazarán y se devolverán con un error. El ejemplo de documento comercial crea una cartera del sistema de archivos que almacenará los certificados y firmará las transacciones. Para obtener más información sobre la forma en que las aplicaciones utilizan las carteras, consulte el tema [cartera ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/wallet.html "Cartera") de la documentación de Fabric. Las carteras que utilizan los SDK de Fabric son distintas de la cartera de la consola de {{site.data.keyword.blockchainfull_notm}} Platform. El SDK no puede utilizar directamente las identidades almacenadas en la cartera de la consola.

El ejemplo original utiliza el archivo `addToWallet.js` para crear una cartera del sistema de archivos utilizando certificados de la carpeta de ejemplos de Fabric. Vamos a crear un nuevo archivo que utilice el SDK para generar certificados del lado del cliente y almacenarlos directamente dentro de una nueva cartera.

Elija la CA de la organización que desea utilizar para trabajar con la guía de aprendizaje como magnetocorp. Por ejemplo, puede utilizar Org1 si ha completado la guía de aprendizaje sobre cómo crear una red. Utilice la CA para [crear una identidad de aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities). **Guarde** el ID y el secreto de inscripción.

Utilice la consola para [descargar el perfil de conexión](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). Guarde el perfil de conexión en el sistema de archivos local y cámbiele el nombre por `connection.json`. Luego utilice el mandato siguiente para trasladar el perfil de conexión a un directorio al que se hará referencia en futuros mandatos.

  ```
  mv $HOME/<path_to_creds>/connection.json /gateway/connection.json
  ```
  {:codeblock}

Vaya al directorio `/magnetocorp/application` y guarde el siguiente bloque de código como `enrollUser.js`.

    ```
    'use strict';

    const FabricCAServices = require('fabric-ca-client');
    const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
    const fs = require('fs');
    const path = require('path');

    const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
    const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
    const ccp = JSON.parse(ccpJSON);

    async function main() {
      try {

        // Crear un nuevo cliente de CA para interactuar con la CA.
        const caURL = ccp.certificateAuthorities['<CA_Name>'].url;
        const ca = new FabricCAServices(caURL);

        // Crear un nuevo sistema de archivos basado en la cartera para gestionar identidades.
        const wallet = new FileSystemWallet('../identity/user/isabella/wallet');

        // Comprobar si ya se ha inscrito el usuario administrador.
        const userExists = await wallet.exists('user1');
        if (userExists) {
          console.log('An identity for "user1" already exists in the wallet');
          return;
        }

        // Inscribir el usuario administrador e importar la nueva identidad en la cartera.
        const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
        const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
        await wallet.import('user1', identity);
        console.log('Successfully enrolled client "user1" and imported it into the wallet');

        } catch (error) {
          console.error(`Failed to enroll "user1": ${error}`);
          process.exit(1);
        }
    }
    main();
    ```
    {:codeblock}

Nos detendremos a estudiar cómo funciona este archivo antes de editarlo. En primer lugar, `enrollUser.js` importa las clases `FileSystemWallet` y `X509WalletMixin` desde la biblioteca `fabric-network`.

```
const FabricCAServices = require('fabric-ca-client');
const { FileSystemWallet, X509WalletMixin } = require('fabric-network');
```
{:codeblock}

A continuación, el archivo utiliza la clase `FileSystemWallet` para crear una cartera en el sistema de archivos local. Puede editar la línea siguiente para almacenar la cartera en otra ubicación.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet')
```
{:codeblock}

Después de crear la cartera, el fragmento de código utiliza el ID y el secreto de inscripción para realizar la inscripción utilizando la CA de la organización. Luego crea una identidad para el par de claves privada y pública y las importa en la cartera. Observe cómo el archivo también pasa el ID de MSP de la organización a la cartera.

```
// Inscribir el usuario administrador e importar la nueva identidad en la cartera.
const enrollment = await ca.enroll({ enrollmentID: '<app_enroll_id>', enrollmentSecret: '<app_enroll_secret>' });
const identity = X509WalletMixin.createIdentity('<msp_id>', enrollment.certificate, enrollment.key.toBytes());
wallet.import('user1', identity);
console.log('Successfully enrolled client "user1" and imported it into the wallet');
```
{:codeblock}

**Edite** `enrollUser.js` para sustituir los siguientes valores:
- Sustituya `'<CA_Name>'` por el nombre de la CA de la organización. Encontrará el nombre de la CA en la sección "organizations" del perfil de conexión bajo "Certificate Authorities". No utilice "caName" en la sección "Certificate Authorities".
- Sustituya `'<app_enroll_id>` por el ID de inscripción de la aplicación proporcionado por el operador de la red.
- Sustituya `'<app_enroll_secret>'` por el secreto de inscripción de la aplicación proporcionado por el operador de la red.
- Sustituya `'<msp_id>'` por el ID de MSP de la organización. Encontrará este ID de MSP en la sección "organizations" del perfil de conexión.

Guarde `enrollUser.js` y ciérrelo. En el directorio `magnetocorp`, ejecute el mandato siguiente:
```
node enrollUser.js
```
{:codeblock}

Cuando el mandato finalice correctamente, debería ver la salida siguiente:

```
Successfully enrolled client "user1" and imported it into the wallet
```
{:codeblock}

Puede encontrar la cartera que se ha creado en la carpeta `identity` del directorio `magnetocorp`.

### Paso cuatro: utilizar el perfil de conexión para crear una pasarela de Fabric

El [flujo de transacciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "flujo de transacciones"){:new_window} de Hyperledger Fabric abarca varios componentes, entre los que las aplicaciones cliente juegan un rol exclusivo. La aplicación tiene que conectarse a los iguales que necesitan aprobar la transacción y al servicio de ordenación que ordenará la transacción y la añadirá a un bloque. Puede proporcionar los puntos finales de estos nodos a la aplicación utilizando el perfil de conexión para crear una pasarela de Fabric. A continuación, la pasarela realiza las interacciones de bajo nivel con la red de Fabric. Para obtener más información, visite el tema [Pasarela de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/gateway.html "Pasarela de Fabric") en la documentación de Fabric.

Ya ha descargado el perfil de conexión y lo ha utilizado para conectarse a la entidad emisora de certificados de la organización. Ahora utilizaremos el perfil de conexión para crear una pasarela.

Abra el archivo `issue.js` en un editor de texto. Antes de editar el archivo, observe que importa las clases `FileSystemWallet` y `Gateway` de la biblioteca de la red de Fabric.

```
const { FileSystemWallet, Gateway } = require('fabric-network')
```
{:codeblock}

La clase `Gateway` se utiliza para construir una pasarela que se utilizará para enviar la transacción.

```
const gateway = new Gateway()
```
{:codeblock}

La clase `FileSystemWallet` se utiliza para cargar la cartera que ha creado en el paso anterior. **Edite** la línea siguiente si ha cambiado la ubicación de la cartera en el sistema de archivos.

```
const wallet = new FileSystemWallet('../identity/user/isabella/wallet');
```
{:codeblock}

Después de importar la cartera, utilice el código siguiente para pasar el perfil de conexión y la cartera a la nueva pasarela. Necesitará realizar las siguientes **ediciones** en el código para que se parezca al fragmento de código siguiente. Las líneas que muestran registros se han eliminado para abreviar.
- Actualice el valor de `userName` para que coincida con el valor que ha seleccionado para `identityLabel`
en `enrollUser.js`.
- Actualice las opciones de descubrimiento para aprovechar la función de descubrimiento de servicios en la red. Establezca `discovery: { enabled: true, asLocalhost: false }`.  
- Actualice la sección que importa el perfil de conexión. El perfil de conexión de la consola está en formato JSON en lugar de en un archivo YAML utilizado en el ejemplo.  

```
const userName = 'user1';

// Load connection profile; will be used to locate a gateway
const ccpPath = path.resolve(__dirname, '../gateway/connection.json');
const ccpJSON = fs.readFileSync(ccpPath, 'utf8');
const connectionProfile = JSON.parse(ccpJSON);

// Establecer opciones de conexión; identidad y cartera
let connectionOptions = {
  identity: userName,
  wallet: wallet,
  discovery: { enabled: true, asLocalhost: false }
};

await gateway.connect(connectionProfile, connectionOptions);
```
{:codeblock}

Este fragmento de código utiliza la pasarela para abrir las conexiones de GRPC con los nodos igual y clasificador y para interactuar con la red.

### Paso cinco: invocar el contrato inteligente

Después de configurar la pasarela para conectarse a la red gestionada por la consola, editaremos la parte del archivo `issue.js` que
se conecta con el contrato inteligente del documento comercial. Tendrá que proporcionar a la pasarela el nombre del contrato y el canal en el que ha creado la instancia del contrato inteligente.

**Edite** la línea siguiente, sustituyendo `mychannel` por el nombre del canal.

```
const network = await gateway.getNetwork('mychannel');
```
{:codeblock}

Después de una línea de código que imprime un mensaje en la consola, encontrará una línea que proporciona a la pasarela el nombre del contrato inteligente. **Edite** la línea siguiente para cambiar el nombre `papercontract` por el nombre del contrato que ha instalado.

```
const contract = await network.getContract('papercontract-js', 'org.papernet.commercialpaper');
```
{:codeblock}

Ahora la pasarela tiene toda la información que necesita para enviar una transacción. En la línea siguiente se invoca la función `issue`
en el contrato del documento comercial, con los argumentos que definen el nuevo activo del documento comercial.

```
const issueResponse = await contract.submitTransaction('issue', 'MagnetoCorp', '00001', '2020-05-31', '2020-11-30', '5000000');
```
{:codeblock}

Después de enviar la transacción utilizando la pasarela, el archivo `issue.js` desconecta la conexión de pasarela.

```
gateway.disconnect();
```
{:codeblock}

Este mandato cierra las conexiones GRPC abiertas por la pasarela. Al cerrar las conexiones se guardarán los recursos de red y se mejorará el rendimiento.

Después de completar las ediciones de este paso y del **Paso cuatro**, guarde `issue.js` y ciérrelo. Envíe la transacción que crea el nuevo documento comercial con el mandato siguiente:

```
node issue.js
```
{:codeblock}

Si la transacción se ejecuta correctamente, debería ver la siguiente información en el terminal:

```
Transaction complete.
Disconnect from Fabric gateway.
Issue program complete.
```

### Paso seis: trabajar con el ejemplo como digibank

Después de crear el documento comercial que funciona como magnetocorp, puede comprar y canjear el documento comercial trabajando con la guía de aprendizaje como digibank. Puede utilizar el código de la aplicación digibank utilizando la misma organización que magnetocorp, o puede utilizar la CA, los iguales y el perfil de conexión de otra organización. Si ha completado la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), esta es una buena oportunidad para trabajar con la guía de aprendizaje como Org2.

Vaya al directorio `digibank/application`. Siga las directrices del **Paso tres** para generar los certificados y la cartera que firmarán la transacción como digibank. Luego puede utilizar el archivo `buy.js` para adquirir el documento comercial de magnetocorp y el archivo `redeem.js` para canjear el documento. Siga el **Paso cuatro** y el **Paso cinco** para editar estos archivos para que apunten al perfil de conexión, al canal y al contrato inteligente correctos.

## Conexión a la red utilizando las API de SDK de Fabric de bajo nivel
{: #ibp-console-app-low-level}

Si está interesado en conservar el código de aplicación existente o en utilizar los SDK de Fabric para lenguajes que no sean Node.js, aún puede conectarse a la red utilizando las API de SDK de Fabric de bajo nivel. Utilice la consola para [descargar el perfil de conexión](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile). A continuación, puede importar los puntos finales de los iguales y de los nodos de ordenación del canal directamente desde el perfil de conexión, o puede utilizar la información de punto final de nodo para añadir manualmente objetos de igual y de clasificador. También tendrá que utilizar la CA para [crear una identidad de aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) y, a continuación, utilizar la información de punto final de CA del lado del cliente, o bien generar certificados utilizando la consola.

La documentación del [SDK de nodo de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io "SDK de nodo de Fabric") contiene una guía de aprendizaje sobre cómo [conectar con la red utilizando un perfil de conexión ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "guía de aprendizaje de perfil de conexión"){:new_window}. En la guía de aprendizaje se utiliza la información de punto final de CA en el perfil de conexión para generar claves utilizando el SDK. También puede utilizar la consola para generar una clave pública y privada y convertir las claves al formato PEM. Luego puede establecer un contexto de usuario pasando las claves directamente a la [clase de cliente de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Client.html "clase de cliente de Fabric") del SDK con el código siguiente:

```
fabric_client.createUser({
		username: 'admin',
		mspid:  'org1',
		cryptoContent: {
			privateKeyPEM: fs.readFileSync(path.join(__dirname,'./privateKey.pem')),
			signedCertPEM: fs.readFileSync(path.join(__dirname,'./certificate.pem'))
		}});
```
{:codeblock}

Si utiliza las API de SDK de bajo nivel para conectarse a la red, hay pasos adicionales que puede realizar para gestionar el rendimiento y la disponibilidad de la aplicación. Para obtener más información, consulte [Prácticas recomendadas para la conectividad y la disponibilidad de aplicaciones](/docs/services/blockchain/best_practices.html#best-practices-app-connectivity-availability).


## Utilización de índices con CouchDB
{: #console-app-couchdb}

Si utiliza CouchDB como base de datos de estado, puede realizar consultas de datos JSON desde sus contratos inteligentes sobre los datos de estado del canal. Se recomienda encarecidamente crear índices para las consultas de JSON y utilizarlos en los contratos inteligentes. Los índices permiten que sus aplicaciones puedan recuperar datos de forma eficiente cuando la red añade bloques adicionales de transacciones y entradas en el escenario mundial. Para obtener información sobre cómo utilizar índices con sus contratos inteligentes y sus aplicaciones, consulte [Prácticas recomendadas al utilizar CouchDB](/docs/services/blockchain/best_practices.html#best-practices-app-couchdb-indices).

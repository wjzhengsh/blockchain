---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Funcionamiento de iguales en {{site.data.keyword.cloud_notm}} privado con una red multinube
{: #peer-operate_icp}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Después de configurar una plataforma {{site.data.keyword.blockchainfull}} el igual de {{site.data.keyword.cloud_notm}} privado (ICP), tiene que llevar a cabo varios pasos operativos para que el igual pueda enviar transacciones para consultar e invocar el libro mayor de la red blockchain. Los pasos incluyen añadir su organización a un canal, unir el igual al canal, instalar el código de encadenamiento en el igual,
crear una instancia del código de encadenamiento en el canal y conectar aplicaciones al igual. Si desea conectar el igual a una red de Plan inicial o de Plan empresarial, consulte
[Operación de iguales en {{site.data.keyword.cloud_notm}} privado con el Plan inicial o el Plan empresarial](peer_operate_ibp.html#peer-operate_icp)
{:shortdesc}

Es necesario completar algunos pasos de requisito previo desde el clúster ICP para trabajar con el igual.

**Requisitos previos:**
- [Configure las CLI](#peer-kubectl-configure)
- [Recupere la información de punto final de igual](#peer-endpoint)
- [Descargue el certificado TLS del igual](#peer-tls)

Luego puede utilizar uno de los métodos siguientes para utilizar el igual.

**Métodos de funcionamiento:**
- [Utilización de los SDK de Fabric](#peer-operate-with-sdk)
- [Utilización de la línea de mandatos](#peer-cli-operate)

Los SDK de Fabric son el método recomendado, aunque en las instrucciones se presupone que está familiarizado con el funcionamiento del SDK. Si prefiere utilizar la línea de mandatos, puede utilizar los binarios de igual de Fabric.

Si la organización aún no es miembro de un consorcio o canal, puede utilizar estos pasos para [crear un canal](#create-channel). Las instrucciones le indicarán cómo [preparar la definición de una organización](#organization-definition). Esta definición se utilizará para convertirle en miembro del consorcio al añadirle a un canal de sistema de clasificador. Más adelante, podrá formar un nuevo canal mediante la
[creación de una transacción de canal](#peer-icp-channeltx)
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

## Requisitos previos

Independientemente de si tiene previsto utilizar los SDK o la línea de mandatos, debe completar los pasos de esta sección para poder utilizar el igual. Puede realizar todos estos pasos antes de comenzar.

### Configuración de las CLI
{: #peer-kubectl-configure}

Necesitará utilizar la herramienta de línea de mandatos **kubectl** para conectarse al contenedor de igual que se ejecuta en ICP.

1. Inicie sesión en la interfaz de usuario del clúster de ICP. Vaya al separador **Herramientas de línea de mandatos** y pulse **CLI de Cloud Private**. Verá las herramientas siguientes, las cuales puede descargar.

   * Instalar la CLI y los plugins de IBM Cloud Private
   * Instalar la CLI de Kubectl
   * Instalar Helm
   * Instalar la CLI de Istio

  Para operar con un igual, necesitará utilizar las primeras **tres** herramientas, entre las que Helm es opcional. Pulse sobre cada una de ellas y ejecute los mandatos de `curl` para el tipo de máquina que utilice. A continuación, emita los mandatos
`chmod` y `sudo mv` para cada herramienta. El mandato `chmod` cambiará el permiso de la CLI en cuestión para hacerla ejecutable y el mandato `sudo mv` moverá el archivo y lo renombrará.

  Los mandatos de la primera herramienta **cloudctl** pueden tener un aspecto similar al del ejemplo siguiente:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Los mandatos de la segunda herramienta **kubectl** pueden tener un aspecto similar al del ejemplo siguiente:

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Tras configurar **kubectl**, ejecute el mandato siguiente:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Si tiene éxito, el mandato devuelve una respuesta similar al ejemplo siguiente, donde
`peer-6cf85f6687-hcxpl` es el nombre del contenedor del igual.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Ahora está listo para utilizar la herramienta **kubectl** para obtener la información de punto final de igual.

3. Si lo prefiere, si desea utilizar **Helm**, realice algunos pasos más. Tenga en cuenta que la instalación de Helm es opcional y no es necesario utilizarla en estas instrucciones. No obstante, puede resultar útil para gestionar los releases de Helm y crear sus propios archivados para su despliegue en ICP.

  1. Pulse "Instalar Helm" y ejecute el mandato `curl` desde la interfaz de usuario de ICP.
  2. Desempaquete el archivo `tar` ejecutando el mandato siguiente:

    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}

  3. Ejecute el mandato `sudo mv` añadiendo `/helm` a `darwin-amd64`. Tenga en cuenta que no es necesario que ejecute el mandato `chmod` para Helm. Por ejemplo:

    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Puede ejecutar el mandato `helm help` para confirmar que Helm se ha instalado correctamente.

### Recuperación de la información de punto final de igual
{: #peer-endpoint}

Debe establecer como objetivo el punto final de igual desde el SDK o el cliente de CA de Fabric para unirse al canal o instalar contratos inteligentes. Puede encontrar el punto final del igual utilizando la interfaz de usuario de la consola de ICP. Necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para realizar los pasos siguientes:

1. Inicie sesión en la consola de ICP y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue del igual.
5. Siga los pasos para configurar la [CLI kubeclt](#peer-kubectl-configure) si aún no lo ha hecho. Utilizará kubectl para interactuar con el contenedor del igual.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **1. Obtenga el URL de aplicación ejecutando estos mandatos:** Este mandato imprimirá el URL y el puerto del igual, que son similares a los del ejemplo siguiente. Guarde este URL para su uso en mandatos posteriores.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Nota:** si despliega el igual detrás de un cortafuegos, necesitará abrir un paso a través para habilitar la red en la plataforma para completar un reconocimiento de TLS con el igual. Solo necesita habilitar un paso a través para el puerto de nodo enlazado con el puerto 7051 del igual. Para obtener más información, consulte [Cómo encontrar la información de punto final de igual](peer_operate_ibp.html#peer-endpoint).

### Descargar el certificado TLS del igual
{: #peer-tls}

Necesita descargar el certificado TLS del igual y pasarlo a los mandatos para comunicarse con el igual desde un cliente remoto.

1. Inicie sesión en la consola de ICP y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue del igual.
5. Siga los pasos para configurar la [CLI kubeclt](#peer-kubectl-configure) si aún no lo ha hecho. Utilizará kubectl para interactuar con el contenedor del igual.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **3. Obtenga el archivo de certificado raíz TLS del igual**. Este mandato guardará el certificado TLS como el archivo `cacert.pem` en la máquina local.
7. Mueva el certificado a una ubicación en la que pueda hacer referencia a él en mandatos posteriores, y cambie su nombre a
`peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Utilización de los SDK de Fabric para trabajar con el igual
{: #peer-operate-with-sdk}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista más reciente de lenguajes soportados y una lista de las API disponibles en los SDK de Fabric en la [documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"). Puede utilizar los SDK de Fabric para unir su igual a un canal en la plataforma {{site.data.keyword.blockchainfull_notm}}, instalar un código de encadenamiento en el igual y crear una instancia del código de encadenamiento en un canal.

En las siguientes instrucciones se utiliza el [Node SDK de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK de Fabric") para trabajar con el igual y se da por supuesto que está familiarizado con el SDK. Puede utilizar la [guía de aprendizaje sobre desarrollo de aplicaciones](../v10_application.html) para aprender a utilizar Node SDK antes de empezar y como guía para desarrollar aplicaciones con el igual cuanto esté listo para invocar el código de encadenamiento de la consulta.

### Instalación de Node SDK

Puede utilizar NPM para instalar [Node SDK ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK"):

```
npm install fabric-client@1.2
```
{:codeblock}

Se recomienda utilizar la versión 1.2 de Node SDK.

### Preparación del SDK para que trabaje con el igual
{: #peer-node-sdk}

El igual se ha desplegado con el signCert del administrador de igual incluido. Esto le permitirá utilizar los certificados del administrador de igual y la carpeta de MSP para trabajar con el igual.

Localice los certificados que ha creado al [inscribir el administrador de igual](CA_operate.html#enroll-admin). Si ha utilizado los mandatos de ejemplo, puede encontrar la carpeta de MSP del administrador de igual en `$HOME/fabric-ca-client/peer-admin`.
  - Puede crear el contexto de usuario del administrador de igual con el SDK utilizando el signCert (clave pública) y la clave privada en la carpeta de MSP. Puede encontrar dichas claves en las ubicaciones siguientes:
    - El signCert se puede encontrar en la carpeta **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La clave privada se puede encontrar en la carpeta **keystore**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Para ver un ejemplo de cómo formar un contexto de usuario y trabajar con el SDK utilizando únicamente las claves pública y privada, consulte
[Generación de certificados utilizando el supervisor de red](../v10_application.html#enroll-panel).

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](CA_operate.html#register-admin). -->

### Cómo pasar el certificado TLS del igual al SDK
{: #icp-peer-download-tlscert}

Necesita hacer referencia al certificado TLS del igual para autenticar la comunicación desde el SDK. Localice el certificado TLS que
[ha descargado desde el contenedor del igual](#peer-tls) y guárdelo en un lugar donde la aplicación pueda hacer referencia a él. Si ha utilizado los mandatos de ejemplo, puede encontrar el certificado TLS del igual en `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Luego puede importar el certificado TLS en la aplicación con un sencillo mandato de lectura de archivo.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Cómo proporcionar la información de punto final del igual al SDK
{: #peer-SDK-endpoints}

Busque la [información de punto final del igual](#peer-endpoint) y proporciónela al SDK, declarando una nueva variable de igual (peer). En el ejemplo siguiente se define el igual como un punto final en la red de fabric y se pasa el certificado TLS que ha importado.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

Si tiene una red grande, con varios iguales que pertenecen a distintas organizaciones que deben aprobar las transacciones, es posible que desee trabajar con el consorcio para [crear un perfil de conexión común ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "perfil de conexión común")

### Cómo pasar el certificado TLS del clasificador al SDK
{: #icp-orderer-download-tlscert}

También necesita el certificado TLS del clasificador del consorcio para unirse a los canales y enviar transacciones. Si es el administrador del clasificador, siga las instrucciones para [descargar el certificado TLS del clasificador](orderer_operate.html#orderer-tls).  Si ha utilizado los mandatos de ejemplo, puede encontrar el certificado TLS del igual en `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`. Si el clasificador está controlado por otra organización, deberán proporcionarle el certificado TLS en una operación fuera de banda. A continuación, puede importar el certificado TLS en la aplicación.

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### Cómo proporcionar la información del clasificador al SDK
{: #orderer-SDK-endpoints}

Para utilizar el SDK, necesitará también la información de punto final de los clasificadores del consorcio. Si es el administrador del clasificador, puede utilizar las instrucciones para [Recuperar la información del punto final del clasificador](orderer_operate.html#orderer-endpoint). Si el clasificador está controlado por otra organización, deberán proporcionarle el URL del clasificador en una operación fuera de banda. En el ejemplo siguiente se define un clasificador como punto final y se pasa el certificado TLS del clasificador.

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### Utilización del SDK para unirse a un canal
{: #peer-join-channel-sdk}

La organización necesita ser miembro de un canal para que pueda unirse al canal con el igual. Si no es miembro de un canal, puede seguir las instrucciones para [crear un nuevo canal](#create-channel).

Después de que la organización pase a ser un miembro de un canal, siga las instrucciones para hacer que
[los iguales se unan a un canal](../v10_application.html#join-channel-sdk) utilizando el SDK.

### Utilización del SDK para instalar el código de encadenamiento en el igual
{: #peer-install-cc-sdk}

Siga las instrucciones siguientes para utilizar el SDK para [instalar un código de encadenamiento](../v10_application.html#install-cc-sdk) en el igual.

### Utilización del SDK para crear una instancia de código de encadenamiento en el canal
{: #peer-instantiate-cc-sdk}

Sólo un miembro del canal necesita crear una instancia o actualizar el código de encadenamiento. Por lo tanto, cualquier miembro del canal que tenga el código de encadenamiento instalado en los iguales puede crear una instancia del código de encadenamiento y especificar política de aprobación. Sin embargo, si desea utilizar el igual para crear una instancia del código de encadenamiento en un canal, puede utilizar el SDK y seguir las instrucciones para [crear una instancia de un código de encadenamiento](../v10_application.html#instantiate-cc-sdk).

## Utilización de la CLI para trabajar con el igual
{: #peer-cli-operate}

También puede trabajar con el igual desde la línea de mandatos utilizando los binarios de iguales de Fabric.

El igual se ha desplegado con el signCert del administrador de igual incluido, permitiendo que dicha identidad pueda trabajar con el igual. Las instrucciones siguientes utilizarán la carpeta de MSP del administrador de igual que se generó cuando
[desplegó el igual](CA_operate.html#register-admin) para hacer que el igual se una a un canal, instalar un código de encadenamiento en el igual y, a continuación, crear una instancia del código de encadenamiento en un canal.

### Descarga del cliente de igual de Fabric
{: #peer-client}

Para interactuar con el igual desde un cliente remoto, necesitará descargar el
[cliente de igual de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "Mandato de igual de Fabric").

La forma más fácil de obtener el cliente de igual es descargar los binarios de la herramienta de Fabric desde Hyperledger Project. Cree un directorio en el que desee descargar los binarios con la línea de mandatos, y obténgalos emitiendo el mandato que se indica a continuación. Necesitará instalar [Curl ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") en primer lugar.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

El mandato instalará los binarios en un directorio `bin/`. Establezca la vía de acceso a las herramientas, donde se han descargado las herramientas de Fabric anteriores:
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

Por ejemplo, si ha instalado los binarios en el directorio de inicio, debe establecer la variable `PATH` como:

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

La descarga de los binarios creará una carpeta de configuración que contiene los archivos core.yaml, orderer.yaml y configtx.yaml. Establezca la vía de acceso de configuración de Fabric en este directorio de configuración:

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

Por ejemplo:
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}

### Gestión de los certificados en el sistema local
{: #manage-certs}

Cambie el directorio donde se genera la carpeta de MSP del administrador de igual. Si ha seguido los pasos de ejemplo de esta documentación, podrá encontrar la carpeta de MSP en un directorio similar al que se indica a continuación:

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Para poder trabajar con el igual, necesita realizar algunas gestiones sobre los certificados de la máquina local. También debe asegurarse de que puede acceder a los certificados TLS desde el igual. Para obtener más información sobre los certificados a utilizar, consulte
[Proveedores de servicios de pertenencia](CA_operate.html#msp) en
[Funcionamiento de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} privado](CA_operate.html).

1. Mueva el signCert del administrador de igual a una carpeta nueva denominada `admincerts`:

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp
    mkdir admincerts
    cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. Asegúrese de [descargar el certificado TLS del igual](#peer-tls) y de que puede hacer referencia a él desde la línea de mandatos. Si ha seguido los mandatos de ejemplo de esta documentación, puede encontrar este certificado TLS en el archivo
`$HOME/fabric-ca-client/peer-tls/peertls.pem`.

3. También necesita hacer referencia al certificado TLS del clasificador. Si es el administrador del clasificador, siga las instrucciones para [descargar el certificado TLS del clasificador](orderer_operate.html#orderer-tls). Si el clasificador está controlado por otra organización, deberán proporcionarle el certificado TLS en una operación fuera de banda. Guarde este certificado en una ubicación en la que pueda hacer referencia a él en mandatos posteriores.

Puede ejecutar un mandato de árbol (tree) para verificar que se han completado estos pasos. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-tls
│   └── orderertls.pem
├── peer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Establecimiento de variables de entorno de CLI
{: #environment-variables}

Después de mover todos los certificados a la ubicación necesaria, se necesitan algunas variables de entorno que utilicen los mandatos de CLI del igual. A continuación, estaremos listos para utilizar el cliente de igual de Fabric para trabajar con el igual.

1. Asegúrese de haber establecido las [variables de entorno en la CLI](#environment-variables)

2. Establezca la vía de acceso de configuración de Fabric en este directorio de configuración que se ha creado al [descargar los binarios de la herramienta de Fabric](#peer-client):

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    Al establecer esta variable, podrá ejecutar los mandatos utilizando el cliente de igual en cualquier directorio.

3. Necesita la información de punto final del clasificador. Si es el administrador del clasificador, puede utilizar las instrucciones para [Recuperar la información del punto final del clasificador](orderer_operate.html#orderer-endpoint). Si el clasificador está controlado por otra organización, deberán proporcionarle el URL del clasificador en una operación fuera de banda.

4. [Recupere la información de punto final de los iguales](#peer-endpoint). Este URL se utilizará para establecer la variable de entorno `PEERADDR`. Necesita omitir la parte `http://` al principio.

5. Ejecute los mandatos siguientes para establecer las variables de entorno:

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Sustituya los campos por su propia información.
    - Sustituya `<CHANNEL_NAME>` por el nombre del canal al que se une el igual.
    - Sustituya `<CC_NAME>` por cualquier nombre que haga referencia a su código de encadenamiento.
    - Sustituya `<PEERADDR>` por el nombre de host y puerto del punto final de igual del paso anterior.
    - Sustituya `<ORDERER_URL>` por el nombre de host y puerto del clasificador del consorcio.
    - Sustituya `<PATH_TO_ADMIN_MSP>` por la vía de acceso a la carpeta de MSP del administrador de igual.
    - Sustituya `<MSPID_OF_PEER_BEING_USED>` por el MSPID de la organización del igual que utilice para obtener un bloque de origen, unirse a un canal o instalar el código de encadenamiento. Esta valor se ha proporcionado durante el despliegue del diagrama de Helm.

  Por ejemplo:

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  También querrá habilitar TLS, lo que puede hacer emitiendo:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Utilización de la CLI para unir el igual al canal
{: #icp-cli-join-peer-to-channel}

La organización necesita ser miembro de un canal para que pueda unirse al canal con el igual. Si no es miembro de un canal, puede seguir las instrucciones para [crear un nuevo canal](#create-channel).

1. Asegúrese de haber establecido las [variables de entorno en la CLI](#environment-variables).

2. Capte el bloque de origen del canal para crear el libro mayor del canal en el igual. Establezca `CORE_PEER_TLS_ROOTCERT_FILE` en la vía de acceso del certificado TLS del clasificador y ejecute el mandato siguiente:

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Si se ejecuta con éxito, verá resultados similares a:
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **Nota:** es posible que cuando ejecute alguno de estos mandatos de la CLI reciba el siguiente aviso, que puede pasar por alto sin problema.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```
  {:codeblock}

  Ejecute el mandato siguiente para verificar que el bloque de origen se ha añadido correctamente al contenedor del igual:

  ```
  ls *.block
  ```
  {:codeblock}

  Sabrá que el bloque de origen se ha añadido correctamente cuando vea algo parecido al ejemplo siguiente:

  ```
  defaultchannel_0.block
  ```

3. Una vez que haya recuperado el bloque de origen del canal, puede unir el igual al canal.  Establezca
`CORE_PEER_TLS_ROOTCERT_FILE` en la vía de acceso del certificado TLS del igual y únalo al canal utilizando el mandato siguiente:

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  Cuando esto funcione, debería ver información parecida a la del siguiente ejemplo:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilización de la CLI para instalar el código de encadenamiento en el igual
{: #icp-toolcontainer-install-cc}

Ahora estamos listos para instalar y crear una instancia de código de encadenamiento en el igual. En estas instrucciones, instalaremos el código de encadenamiento `fabcar` desde el repositorio `fabric-samples`. Asegúrese de haber
[configurado GOPATH ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Configure GOPATH") con anterioridad y, a continuación, descargue el código de encadenamiento `fabric-samples` desde github utilizando los mandatos siguientes:

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Ejecute el siguiente mandato de CLI del igual para instalar el código de encadenamiento `fabcar` en el igual. Establezca
`CORE_PEER_TLS_ROOTCERT_FILE` en la vía de acceso del certificado TLS del igual.

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Cuando este mandato finalice correctamente, verá algo parecido a esto:

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

<!--
**Note:** If this chaincode has already been installed and instantiated on another peer running in IBP Starter or Enterprise Plan, there are additional steps that must be performed before installing the chaincode on the peer. For more instructions about how to avoid errors that are associated with how this chaincode is installed, see the [troubleshooting topic](#icp-cc-install-error).
-->

### Utilización de la CLI para crear una instancia de código de encadenamiento en un canal
{: #icp-toolcontainer-instantiate-cc}

Dado que solo un igual tiene que crear una instancia del código de encadenamiento en un canal, no tiene que utilizar su igual para crear una instancia del código de encadenamiento. Los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}} pueden hacerlo. Sin embargo, si desea que el igual cree una instancia del código de encadenamiento en el canal, puede hacerlo ejecutando el mandato que se indica a continuación. Establezca el valor de
`CORE_PEER_TLS_ROOTCERT_FILE` en la vía de acceso del certificado TLS del igual. Establezca el valor de
`--cafile` en el certificado TLS del clasificador.

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

Cuando este mandato finalice correctamente, verá algo parecido a esto:

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

Después de que se haya creado una instancia del código de encadenamiento, puede utilizar los mandatos de consulta e invocación del código de encadenamiento para leer y escribir datos en el libro mayor del canal. Para obtener más información, consulte los mandatos de
[código de encadenamiento de igual
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) en la documentación de Hyperledger Fabric. Necesitará pasar el punto final del clasificador a los mandatos de invocación utilizando la IP de proxy y el puerto de clasificador externo. Solo tiene que pasar el punto final de igual a un mandato de consulta.

## Visualización de los registros del igual en {{site.data.keyword.cloud_notm}} privado
{: #peer-log-icp}

Puede acceder a los registros del igual desde la consola de ICP y comprobar los registros en la interfaz Kibana.

1. En la consola de ICP, pulse el icono **Menú** en la esquina superior izquierda.
2. En la lista de menús, pulse **Cargas de trabajo** > **Releases de Helm**.
3. En la tabla Releases de Helm, pulse el nombre de su release de helm.
4. Desplácese hacia abajo hasta la sección **Pod** y pulse el enlace **Ver registros** al final de la fila de la tabla. Los registros del igual se abren en la interfaz Kibana.

## Actualización del código de encadenamiento

Con el tiempo, es probable que tenga que modificar el código de encadenamiento que se ejecuta en el igual. Puesto que el código de encadenamiento se instala en los iguales y se crea una instancia del mismo en el canal, tiene que actualizar el código de encadenamiento en todos los iguales del canal.

Siga los pasos siguientes para actualizar el código de encadenamiento:

1. Para actualizar el código de encadenamiento en cada igual, simplemente vuelva a ejecutar el proceso que ha utilizado para instalar el código de encadenamiento en los iguales, utilizando una aplicación cliente o un mandato de CLI. Asegúrese de especificar el nombre de código de encadenamiento que se ha utilizado originalmente. Sin embargo, esta vez incremente la `Versión` del código de encadenamiento. Todos los iguales necesitan utilizar el mismo nombre y versión de código de encadenamiento.

2. Después de instalar el nuevo código de encadenamiento en todos los iguales del canal, utilice el mandato de [actualización de código de encadenamiento del igual ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para actualizar el canal para que utilice el nuevo código de encadenamiento.

## Reinicio de un igual que se ejecuta en ICP
{: #peer-restart}

Siempre que se añade un certificado al igual, el nodo igual debe reiniciarse para que reconozca el nuevo certificado.

Puede utilizar los mandatos **kubectl** para reiniciar el igual que se ejecuta en ICP. El mandato requiere el nombre del **pod** del igual y su **contenedor** como parámetros del mandato. Para obtener más información sobre cómo utilizar los mandatos kubectl, consulte [configuración de la CLI de kubectl](#peer-kubectl-configure).

1. En la consola de ICP, busque el nombre del **pod** y el nombre del **contenedor** para el despliegue del igual.

  1. Pulse el icono de menú en la esquina superior izquierda de la consola y luego pulse **Cargas de trabajo > Despliegues**.
  2. Localice y pulse en el release de igual en la tabla para abrirlo.
  3. Desplácese hacia abajo para ver el nombre de **pod** asociado. Anote el nombre de **pod**.
  4. Pulse el pod para abrirlo.
  5. Pulse el separador **Contenedores**. Anote el nombre de **contenedor** del igual.

2. En la línea de mandatos, ejecute el siguiente mandato para reiniciar el igual, sustituyendo `<PEER_POD_NAME>` y `<PEER_CONTAINER_NAME>` por los valores de arriba.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. Para verificar que el igual se ha reiniciado, ejecute el mandato `kubectl get pods` y examine la salida del recuento de **RESTART** correspondiente al pod.

## Creación de un canal
{: #create-channel}

Si la organización aún no es miembro de un consorcio o canal, puede utilizar los pasos que se indican a continuación para crear un canal. También puede utilizar estos pasos para actualizar un canal existente. Las instrucciones le indicarán cómo [preparar la definición de una organización](#organization-definition). Esta definición se utilizará para convertirle en [miembro del consorcio](orderer_operate.html#add-organizations-to-consortium) al añadirle a un canal de sistema de clasificador. Más adelante, podrá formar un nuevo canal mediante la
[creación de una transacción de canal](#peer-icp-channeltx)

Si ya se ha unido al consorcio, deberá completar únicamente el paso de preparación de la transacción del canal. Otro miembro del consorcio también puede formar un nuevo canal con su organización como miembro. Puede actualizar la definición de la organización cuando necesite enviar actualizaciones al consorcio, como la definición de nuevos iguales de ancla, la adición de nuevos certificados de administrador o la actualización del material criptográfico.

### Descarga de la herramienta configtxgen
{: #peer-icp-configtxgen}

Si la organización necesita unirse a un consorcio o canal, deberá descargar la herramienta
[configtxgen ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen").

La forma más fácil de obtener configtxgen es descargar todos los binarios de herramientas de Fabric desde Hyperledger Project. Vaya al directorio en el que desee descargar los binarios con la línea de mandatos, y obténgalos emitiendo el mandato que se indica a continuación. Necesitará instalar [Curl ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") en primer lugar.

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

El mandato instala configtxgen en un directorio `bin/`. Establezca la vía de acceso de `PATH` en el directorio donde haya descargado los binarios de herramientas de Fabric:

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

Por ejemplo, si ha instalado los binarios en el directorio de inicio, debe establecer la variable `PATH` como:

```
export PATH=$PATH:$HOME/bin
```

## Preparación de una definición de organización
{: #organization-definition}

Una vez que haya desplegado los componentes, la organización se puede unir a un consorcio preparando un archivo de definición de organización. La definición contiene toda la información que necesita para participar en el gobierno del consorcio, como por ejemplo, creando o uniéndose a nuevos canales, añadiendo sus iguales a canales o creando una instancia del código de encadenamiento. La definición contiene el nombre de su organización, el MSPID y los certificados MSP. Todas las organizaciones deben completar este paso.

### Preparar el material criptográfico

Antes de preparar una definición de organización, necesita registrar e inscribir el [administrador de los iguales](CA_operate.html#register-admin). Vaya al directorio donde ha creado la carpeta de MSP del administrador de igual. Los pasos de ejemplo crearon esta carpeta en `$HOME/fabric-ca-client/peer-admin/msp`. Debe llevar a cabo algunos pasos adicionales dentro de esta carpeta para que la herramienta
`configtxgen` pueda utilizar MSP.

1. Copie el certificado TLS de la entidad emisora de certificados y colóquelo en una nueva carpeta denominada
`tlscacerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  A continuación, deberá copiar el certificado en el directorio `tlscacerts` que ha creado:

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  El mandato puede tener un aspecto similar al siguiente:

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. Mueva el signCert de la carpeta `signcerts` a una nueva carpeta denominada `admincerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

La herramienta `configtxgen` crea la definición mediante el consumo de un archivo configtx.yaml. Puede encontrar una versión de ejemplo de este archivo a continuación:

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

Este archivo contiene la información que define la organización dentro del consorcio. También hay disponible una versión más compleja de este archivo en la carpeta `/config` del [cliente de igual de Fabric que ha descargado](peer_operate_icp.html#peer-client). Puede optar por editar dicho archivo, o sustituirlo por el del ejemplo anterior. Tenga en cuenta la ubicación de esta carpeta
`/config` para establecer el valor de la variable
`FABRIC_CFG_PATH` siguiente. Edite la sección `Organizations` de este archivo y establezca los valores siguientes:

- `Name:` puede ser cualquier valor que desee utilizar para la organización.

- Los valores de `ID:` del MSPID, que se convierten en el identificador exclusivo de la organización. Este valor de MSP de organización se ha especificado al desplegar el diagrama de Helm del igual. También es visible dentro del contenedor del igual ejecutando los mandatos siguientes y sustituyendo `<peer pod name>` por el valor del pod del igual:

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  La salida puede tener un aspecto similar a:
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  Por lo tanto, el `ID` de MSP es org1.

- `MSPDir:` es la vía de acceso completa al MSP del administrador, por ejemplo:
`/Users/chandra/fabric-ca-client/peer-admin/msp`

- El campo `AnchorPeers:` es el igual que su organización selecciona para que sea el igual de referencia para la comunicación dentro de la organización mediante rumores. Esto se utiliza para características como el descubrimiento de servicios o los datos privados;
no obstante, el diagrama de Helm del igual no tiene soporte actualmente para el descubrimiento de servicios y datos privados, aunque el
campo sigue siendo necesario. Los valores de `Host` y `Port` están disponibles en la página Release de Helm del igual de ICP bajo la sección de Notas `1. Obtenga el URL de aplicación ejecutando estos mandatos`. La salida será similar a:

```
http://9.30.250.70:30481
```

Donde `Host:` = `9.30.250.70` y `Port:` = `30481`

A continuación, utilice el mandato siguiente para imprimir la definición de organización:

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

La llamada puede ser similar al siguiente mandato de ejemplo:

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

Si el mandato se ejecuta correctamente, `configtxgen` imprimirá la definición de organización en formato JSON. Es necesario enviar este archivo a la organización del clasificador en una operación fuera de banda para unirse al consorcio. A continuación, la organización del clasificador puede [formar un consorcio o incorporarse a un consorcio existente](orderer_operate.html#consortium) añadiendo la definición al canal del sistema, lo que le permite crear nuevos canales y que otros miembros del consorcio le añadan a canales.

## Creación de la transacción del canal
{: #peer-icp-channeltx}

Para poder crear un nuevo canal, la organización debe haber preparado una [definición de organización](#organization-definition) y haberse convertido en miembro del consorcio. Siga estas instrucciones si necesita [formar un consorcio o incorporarse a uno](orderer_operate.html#consortium). También se posible que se añadan fácilmente miembros del consorcio a nuevos canales, si su organización se ha añadido ya al canal del sistema. Las organizaciones que no sean miembro del canal del sistema solo se puede unir a un canal de forma manual, añadiendo su definición de organización al canal utilizando una [solicitud de actualización de canal
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html). También puede utilizar estos pasos para actualizar un canal existente.

### Formación de un nuevo canal
{: #peer-icp-create-channel}

Para formar un nuevo canal, una organización tiene que crear una propuesta de transacción de creación de canal utilizando la
[herramienta configtxgen
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen"). Esta herramienta consume un archivo configtx.yaml que define los miembros del nuevo canal. A continuación se proporciona un archivo
`configtx.yaml` de ejemplo. También hay disponible una versión más compleja de este archivo en la carpeta `/config` del [cliente de igual de Fabric que ha descargado](peer_operate_icp.html#peer-client). Puede optar por editar dicho archivo, o sustituirlo por el del ejemplo. Tenga en cuenta la ubicación de esta carpeta
`/config` para establecer el valor de la variable
`FABRIC_CFG_PATH` siguiente.
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

Las tres secciones relevantes para la creación de un nuevo canal son
**Organizations**, **Capabilities** y **Profiles**:

- **Organizations:** esta sección define todos los miembros del consorcio. Cada organización tiene un ancla, por ejemplo
`&Org1`. Bajo esta ancla puede encontrar el nombre de la organización, el MSPID, el directorio a su carpeta de MSP y los iguales de ancla de su organización para los rumores entre iguales. Puede rellenar el perfil de organización para cada miembro del consorcio con los pasos siguientes:
  1. Especifique los valores de `Name` e `ID` de la organización utilizando su MSPID. La organización que crea el canal necesita saber el MSPID que se ha especificado al desplegar el diagrama de Helm del igual.
  2. En `MSPDir`, especifique la vía de acceso completa a la carpeta de MSP que ha utilizado para crear la
[definición de organización](#organization-definition). Tenga en cuenta que su material criptográfico no se ha utilizado en la transacción de creación del canal. La herramienta *configtxgen* pasará por alto el contenido real del MSP. No obstante, espera una carpeta de MSP en dicha ubicación, con la subestructura de carpetas adecuada.
  3. El parámetro `AnchorPeers` se utiliza para identificar el igual de referencia que utiliza cada organización para la comunicación dentro de la organización mediante rumores. Especifique el nombre de host y el puerto del igual que actuará como
[igual de ancla
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) para esta organización. Este valor es importante para el uso de características como el descubrimiento de servicios o los datos privados;
no obstante, el diagrama de Helm del igual no tiene soporte actualmente para el descubrimiento de servicios y datos privados.

- **Capabilities:** es necesario que esta sección esté en el archivo `configtx.yaml`, pero no se necesita realizar ningún cambio.

- Sección **Profiles:**. Esta sección contiene la información necesaria para crear un nuevo canal. El perfil contiene la información siguiente:
  1. El nombre del nuevo canal.
  2. El nombre del consorcio definido dentro del canal del sistema y que se utilizará para crear el canal.
  3. La lista de organizaciones que se añadirán al canal. Los nombres de organización que aparecen en la lista utilizarán los iguales de ancla que aparecen en la sección `Organizations` para buscar los MSPID de las organizaciones de la lista y los iguales de ancla asociados.

  La herramienta *configtxgen* utiliza esta sección `Profiles` para crear la propuesta de creación de canal.

Tras actualizar el archivo de configuración, genere la propuesta de transacción de actualización de canal utilizando el mandato siguiente:

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

Un mandato real puede tener un aspecto similar al del ejemplo siguiente:
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


Si tiene éxito, verá un resultado similar a la salida que se muestra a continuación. El mandato escribirá la propuesta de transacción de actualización de canal en el directorio `FABRIC_CFG_PATH`.

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

Si la política de aprobación no requiere firmas adicionales de otros miembros del consorcio, puede enviar la propuesta directamente al clasificador para formar el nuevo canal. Ejecute el mandato siguiente desde la misma ubicación en la que ha generado la transacción de actualización de canal para crear el canal.

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

Donde `<ORG_MSPID>` es el MSPID de la organización y `<PATH_TO_ADMIN_MSP>` es la vía de acceso a la carpeta de MSP del administrador de igual. Dentro del mandato, `<orderer_url>` es el URL del clasificador que creará el canal y
`<orderer_tls_cert>` es la vía de acceso al certificado TLS del clasificador. Un mandato real puede tener un aspecto similar al ejemplo siguiente:
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## Aprobación de la propuesta y envío de la solicitud

Cuando la propuesta de creación del nuevo canal la firma el número suficiente de organizaciones para cumplir la política de aprobación del canal del sistema, puede enviar la transacción de actualización de canal al clasificador para formar el nuevo canal

Puede utilizar API de SDK de Node Fabric para completar la firma y el envío en una sola transacción. Para obtener más información, consulte la guía de aprendizaje [Cómo crear un canal de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) en la documentación del SDK de Node.

## Visualización de los registros de igual
{: #peer-icp-view-logs}

Los registros de componentes se pueden visualizar desde la línea de mandatos utilizando los
[`mandatos de la CLI kubectl`](#peer-kubectl-configure) o a través de
[Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que se incluye en el clúster de ICP.

- Utilice el mandato `kubectl logs` para ver los registros de contenedor dentro del pod. Si no está seguro de cuál es el nombre del pod, ejecute el mandato siguiente para ver la lista de pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  A continuación, ejecute el mandato siguiente para recuperar los registros del contenedor de igual que se encuentra dentro del pod sustituyendo
`<pod_name>` por el nombre del pod en la salida del mandato anterior:

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Para obtener más información sobre el mandato `kubectl logs`, consulte la
[Documentación de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, puede acceder a los registros utilizando la
[consola de gestión de clúster de ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre los registros en Kibana.

  **Nota:** al visualizar los registros en Kibana, es posible que reciba la respuesta `No results found`. Esta condición se puede producir si ICP utiliza la dirección IP del nodo trabajador como su nombre de host. Para resolver este problema, elimine el filtro que comienza por `node.hostname.keyword` al principio del panel y los registros se volverán visibles.


## Resolución de problemas
{: #peer-icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```
{:codeblock}
2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the selected version is compatible with ICP 2.1.0.3. `v2.7.2 ` is recommended.

3.  After the helm installation completes, [login](#peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```
 {:codeblock}
 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```
 {:codeblock}

5. Check helm version:

  ```
  helm version --tls
  ```
  {:codeblock}

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problema:** error al iniciar sesión en el contenedor del igual
{: #icp-bash-error}

Este problema se produce cuando se utiliza la herramienta kubectl para ejecutar en el igual con el mandato siguiente:

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

El mandato falla y genera un mensaje de error similar al ejemplo siguiente:

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solución:**

Este error se produce en sistemas big endian, como por ejemplo IBM System Z, cuando dos sesiones de bash de Docker se abren en el mismo contenedor de Docker. Es posible que la segunda sesión de bash no pueda conectarse al contenedor en ejecución. Para resolver este problema, [reinicie el contenedor del igual](#peer-restart) y vuelva a intentar el mandato `kubectl exec` después de que el igual se reinicie.

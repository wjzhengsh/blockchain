---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
# Funcionamiento de iguales en {{site.data.keyword.cloud_notm}} Private con el Plan inicial o el Plan empresarial
{: #ibp-peer-operate}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Después de configurar un {{site.data.keyword.blockchainfull}} Platform en el igual de {{site.data.keyword.cloud_notm}} Private, debe completar varios pasos operativos para que el igual pueda enviar transacciones a una red del Plan inicial o el Plan empresarial. Los pasos incluyen añadir su organización a un canal, unir el igual al canal, instalar el código de encadenamiento en el igual, crear una instancia del código de encadenamiento en el canal y conectar aplicaciones al igual.
{:shortdesc}

Es necesario completar algunos pasos de requisito previo desde el clúster de {{site.data.keyword.cloud_notm}} Private para trabajar con el igual.

**Requisitos previos:**
- [Configure las CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)
- [Recupere la información de punto final de igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)
- [Descargue el certificado TLS del igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)

Luego puede utilizar uno de los métodos siguientes para utilizar el igual.

**Métodos de funcionamiento:**
- [Utilización de los SDK de Fabric](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-operate-with-sdk)
- [Utilización de la línea de mandatos](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-cli-operate)

Los SDK de Fabric son el método recomendado, aunque en las instrucciones se presupone que está familiarizado con el funcionamiento del SDK. Si prefiere utilizar la línea de mandatos, puede utilizar el cliente de igual de Fabric.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**Nota**: un igual de {{site.data.keyword.blockchainfull_notm}} Platform no tiene acceso a toda la funcionalidad ni al soporte de los iguales alojados en {{site.data.keyword.blockchainfull_notm}} Platform. Como resultado, no puede utilizar el supervisor de red para trabajar con un igual en {{site.data.keyword.cloud_notm}} Private. Antes de empezar a ejecutar iguales, asegúrese de revisar las [consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Requisitos previos
{: #ibp-peer-operate-prerequisites}

Independientemente de si tiene previsto utilizar los SDK o la línea de mandatos, debe completar los pasos de esta sección para poder utilizar el igual. Puede realizar todos estos pasos antes de comenzar.

### Configuración de las CLI
{: #ibp-peer-operate-kubectl-configure}

Necesitará utilizar la herramienta de línea de mandatos **kubectl** para conectarse al contenedor del igual que se ejecuta en {{site.data.keyword.cloud_notm}} Private.

1. Inicie sesión en la interfaz de usuario del clúster de {{site.data.keyword.cloud_notm}} Private. Vaya al separador **Herramientas de línea de mandatos** y pulse **CLI de Cloud Private**. Verá las herramientas siguientes, las cuales puede descargar.

   * Instalar la CLI y los plugins de IBM Cloud Private
   * Instalar la CLI de Kubectl
   * Instalar Helm
   * Instalar la CLI de Istio

  Para operar con un igual, necesitará utilizar las primeras **tres** herramientas, entre las que Helm es opcional. Pulse sobre cada una de ellas y ejecute los mandatos de `curl` para el tipo de máquina que utilice. A continuación, emita los mandatos `chmod` y `sudo mv` para cada herramienta. El mandato `chmod` cambiará el permiso de la CLI en cuestión para hacerla ejecutable y el mandato `sudo mv` moverá el archivo y lo renombrará.

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

  Si tiene éxito, el mandato devuelve una respuesta similar al ejemplo siguiente, donde `peer-6cf85f6687-hcxpl` es el nombre del contenedor del igual.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  peer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Ahora está listo para utilizar la herramienta **kubectl** para obtener la información de punto final de igual.

3. Si lo prefiere, si desea utilizar **Helm**, realice algunos pasos más. Tenga en cuenta que la instalación de Helm es opcional y no es necesario utilizarla en estas instrucciones. No obstante, puede resultar útil para gestionar los releases de Helm y crear sus propios archivados para su despliegue en {{site.data.keyword.cloud_notm}} Private.

  1. Pulse "Instalar Helm" y ejecute el mandato `curl` desde la interfaz de usuario de {{site.data.keyword.cloud_notm}} Private.
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
{: #ibp-peer-operate-endpoint}

Debe establecer como objetivo el punto final de igual desde el SDK o el cliente de CA de Fabric para unirse al canal o instalar contratos inteligentes. Puede encontrar el punto final del igual utilizando la interfaz de usuario de la consola de {{site.data.keyword.cloud_notm}} Private. Necesitará ser un [administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para realizar los pasos siguientes:

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue del igual.
5. Siga los pasos para configurar la [CLI kubeclt](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) si aún no lo ha hecho. Utilizará kubectl para interactuar con el contenedor del igual.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **1. Obtenga el URL de aplicación ejecutando estos mandatos:** Este mandato imprimirá el URL y el puerto del igual, que son similares a los del ejemplo siguiente. Guarde este URL para su uso en mandatos posteriores.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Nota:** si despliega el igual detrás de un cortafuegos, necesitará abrir un paso a través para habilitar la red en la plataforma para completar un reconocimiento de TLS con el igual. Solo necesita habilitar un paso a través para el puerto de nodo enlazado con el puerto 7051 del igual. Para obtener más información, consulte [Cómo encontrar la información de punto final de igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint).

### Descargar el certificado TLS del igual
{: #ibp-peer-operate-tls-cert}

Necesita descargar el certificado TLS del igual y pasarlo a los mandatos para comunicarse con el igual desde un cliente remoto.

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue del igual.
5. Siga los pasos para configurar la [CLI kubeclt](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) si aún no lo ha hecho. Utilizará kubectl para interactuar con el contenedor del igual.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **3. Obtenga el archivo de certificado raíz TLS del igual**. Este mandato guardará el certificado TLS como el archivo `cacert.pem` en la máquina local.
7. Mueva el certificado a una ubicación en la que pueda hacer referencia al mismo en mandatos posteriores, y cambie su nombre a `peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls
  cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Utilización de los SDK de Fabric para trabajar con el igual
{: #ibp-peer-operate-operate-with-sdk}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista más reciente de lenguajes soportados y una lista de las API disponibles en los SDK de Fabric en la [documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"). Puede utilizar los SDK de Fabric para unir su igual a un canal en {{site.data.keyword.blockchainfull_notm}} Platform, instalar un código de encadenamiento en el igual y crear una instancia del código de encadenamiento en un canal.

En las siguientes instrucciones se utiliza el [Node SDK de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK de Fabric") para trabajar con el igual y se da por supuesto que está familiarizado con el SDK. Puede utilizar la [guía de aprendizaje sobre desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html#dev-app) para aprender a utilizar Node SDK antes de empezar y como guía para desarrollar aplicaciones con el igual cuando esté listo para invocar el código de encadenamiento de la consulta.

### Instalación de Node SDK
{: #ibp-peer-operate-install-sdk}

Puede utilizar NPM para instalar [Node SDK ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK"):

```
npm install fabric-client@1.2
```
{:codeblock}

Se recomienda utilizar la versión 1.2 de Node SDK.

### Preparación del SDK para que trabaje con el igual
{: #ibp-peer-operate-prepare-node-sdk}

El igual se despliega con el signCert del administrador de igual incluido. Esto le permite utilizar los certificados del administrador de igual y la carpeta de MSP para trabajar con el igual.

Localice los certificados que ha creado al [inscribir el administrador de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin). Si ha utilizado los mandatos de ejemplo, puede encontrar la carpeta de MSP del administrador de igual en `$HOME/fabric-ca-client/peer-admin`.
  - Puede crear el contexto de usuario del administrador de igual con el SDK utilizando el signCert (clave pública) y la clave privada en la carpeta de MSP. Puede encontrar dichas claves en las ubicaciones siguientes:
    - El signCert se puede encontrar en la carpeta **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - La clave privada se puede encontrar en la carpeta **keystore**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    Puede ver un ejemplo de cómo formar un contexto de usuario y trabajar con el SDK utilizando únicamente las claves pública y privada en [esta sección de la guía de aprendizaje de desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel).

También puede utilizar el SDK para generar el signCert del administrador de igual y la clave privada utilizando la información de punto final de la entidad emisora de certificados en el Plan inicial o el Plan empresarial y su [nombre de usuario y contraseña de administrador de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin).

### Carga del signCert de administrador de igual en el Plan inicial o el Plan empresarial
{: #ibp-peer-operate-upload-sdk}

Es necesario cargar el signCert del administrador de igual en la red en {{site.data.keyword.blockchainfull_notm}} Platform para que la aplicación tenga permiso para trabajar en la red.

- Puede encontrar el signCert en el directorio en el que el SDK ha generado el material criptográfico, en el archivo denominado como el administrador de igual. Copie el certificado que hay entre comillas tras el campo `certificate`, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE-----`. Puede utilizar la CLI para convertir el certificado al formato PEM con el mandato `echo -e "<CERT>" > admin.pem`. Luego puede pegar el contenido del certificado en la red blockchain utilizando el supervisor de red. Inicie una sesión en la red en {{site.data.keyword.blockchainfull_notm}} Platform. En la pantalla "Miembros" del supervisor de red, pulse **Certificados** > **Añadir certificado**. Especifique un nombre para el certificado y pegue el contenido en el campo **Certificado**. Pulse **Reiniciar** cuando se le pregunte si desea reiniciar los iguales. Esta acción se debe llevar a cabo antes de que la organización se una al canal.

### Cómo pasar el certificado TLS del igual al SDK
{: #ibp-peer-operate-download-tlscert}

Debe hacer referencia al certificado TLS de los iguales para autenticar la comunicación con el SDK. Localice el certificado TLS que [ha descargado desde el contenedor del igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) y guárdelo en un lugar donde la aplicación pueda hacer referencia al mismo. Si ha utilizado los mandatos de ejemplo, puede encontrar el certificado TLS del igual en `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Luego puede importar el certificado TLS en la aplicación con un sencillo mandato de lectura de archivo.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Cómo proporcionar la información de punto final del igual al SDK
{: #ibp-peer-operate-SDK-endpoints}

Busque la [información de punto final del igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint) y proporciónela al SDK, declarando una nueva variable peer o actualizando el perfil de conexión. En el ejemplo siguiente se define el igual como un punto final en la red de fabric y se pasa el certificado TLS que ha importado.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**Nota:** como el igual está fuera de {{site.data.keyword.cloud_notm}}, otras organizaciones de la red de {{site.data.keyword.blockchainfull_notm}} Platform no podrán encontrar la información de punto final de su igual en el perfil de conexión. Si otras organizaciones necesitan enviar al igual una transacción para su aprobación, deberá proporcionarles el URL de igual en una operación fuera de banda.

### Utilización del SDK para unirse a un canal
{: #ibp-peer-operate-peer-join-channel-sdk}

La organización necesita ser miembro de un canal para que pueda unirse al canal con el igual.

  - Puede iniciar un nuevo canal para el igual. Si es el iniciador del canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

    Una vez que la organización se haya añadido a un canal, deberá añadir el signCert del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual carga su signCert durante la instalación, lo que implica que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal. Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir los mandatos `join channel`.

    **Nota:** solo podrá ver los bloques nuevos que se añaden al canal, el código de encadenamiento del que se está creando una instancia y otras actualizaciones del canal en la pantalla "Canales" del supervisor de red si el igual que está añadiendo a {{site.data.keyword.cloud_notm}} Private y conectando a una red de Plan inicial o Plan empresarial forma parte de la misma organización que un igual que se haya añadido con el supervisor de red. Esto se debe a que el supervisor de red recopila información en la pantalla "Canales" de su igual y no tiene visibilidad para los iguales que estén fuera de {{site.data.keyword.cloud_notm}}. Si ninguno de sus iguales utiliza la característica de datos privados, la información del supervisor de red será tanto para un igual de una organización como para el otro.

Después de que la organización pase a ser un miembro de un canal, siga las instrucciones para hacer que [el igual se una a un canal](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk) utilizando el SDK. Debe especificar URL del servicio de ordenación y el nombre del canal.

### Utilización del SDK para instalar el código de encadenamiento en el igual
{: #ibp-peer-operate-install-cc-sdk}

Siga las instrucciones siguientes para utilizar el SDK para [instalar un código de encadenamiento](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk) en el igual.

### Utilización del SDK para crear una instancia de código de encadenamiento en el canal
{: #ibp-peer-operate-instantiate-cc-sdk}

Sólo un miembro del canal necesita crear una instancia o actualizar el código de encadenamiento. Por lo tanto, cualquier miembro de red del canal con iguales en {{site.data.keyword.blockchainfull_notm}} Platform puede utilizar el supervisor de red para crear una instancia de código de encadenamiento y especificar políticas de aprobación. Sin embargo, si desea utilizar el igual para crear una instancia del código de encadenamiento en un canal, puede utilizar el SDK y seguir las instrucciones para [crear una instancia de un código de encadenamiento](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk).

## Utilización de la CLI para trabajar con el igual
{: #ibp-peer-operate-cli-operate}

También puede trabajar con el igual desde la línea de mandatos utilizando el cliente de igual (`peer`) de Fabric.

El igual se ha desplegado con el signCert del administrador de igual incluido, permitiendo que dicha identidad pueda trabajar con el igual. Las instrucciones siguientes utilizarán la carpeta de MSP del administrador de igual que se generó cuando
[desplegó el igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) para hacer que el igual se una a un canal, instalar un código de encadenamiento en el igual y, a continuación, crear una instancia del código de encadenamiento en un canal.

### Descarga del cliente de igual de Fabric
{: #ibp-peer-operate-download-fabric-client}

La forma más fácil de obtener el cliente de igual es descargar todos los binarios de herramientas de Fabric desde Hyperledger. Vaya al directorio en el que desee descargar los binarios con la línea de mandatos y obténgalos emitiendo el mandato que se indica a continuación. Si no ha instalado [Curl ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl"), necesitará instalarlo en primer lugar.

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

La descarga de los binarios creará una carpeta `config` que contiene los archivos core.yaml, orderer.yaml y configtx.yaml. Establezca la vía de acceso de configuración de Fabric en este directorio
`config`:

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

Para poder trabajar con el igual, necesita realizar algunas gestiones sobre los certificados de la máquina local. Por ejemplo, deberá cargar el signCert del administrador de igual en el Plan inicial o el Plan empresarial y asegurarse de que puede acceder a los certificados TLS desde el igual y la red en {{site.data.keyword.cloud_notm}}. Para obtener más información sobre los certificados que hay que utilizar y las tareas que hay que realizar, consulte [Gestión de certificados en {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

1. Mueva el signCert del administrador de igual a una carpeta nueva denominada `admincerts`:

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Cargue este signCert en la red en {{site.data.keyword.cloud_notm}} para que una aplicación o una CLI remota pueda realizar operaciones de canal, como la recuperación del bloque de origen del canal o la creación de una instancia de código de encadenamiento.
    1. En la máquina local, abra el archivo `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` y cópielo en el portapapeles.
    2. Entre en el supervisor de su red en {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Pulse **Miembros** en el navegador de la izquierda y pulse el separador **Certificados**.
    4. Pulse el botón **Añadir certificado**.
    5. En la ventana **Añadir certificado**, asigne un nombre al certificado, como por ejemplo "peer-admin", pegue el certificado que acaba de copiar en el portapapeles y pulse **Enviar**.
    6. Se le preguntará si desea reiniciar los iguales. Pulse **Reiniciar**.
    7. Pulse **Canales** en el navegador de la izquierda y localice el canal al que se unirá el igual.
    8. Pulse los tres puntos bajo la cabecera **Acciones** y pulse **Sincronizar certificado**. En la ventana **Sincronizar certificado**, pulse **Enviar**.

    **Nota**: es importante sincronizar el certificado de canal antes de que el igual se una al canal o cree una instancia de código de encadenamiento en el canal. Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir los mandatos de unión al canal o de creación de una instancia de código de encadenamiento.

3. Asegúrese de haber [descargado el certificado TLS del igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) y de que puede hacer referencia al mismo desde la línea de mandatos. Si ha seguido los mandatos de ejemplo, puede encontrar este certificado TLS en el archivo
`$HOME/fabric-ca-client/peer-tls/peertls.pem`.

4. También deberá hacer referencia al certificado TLS que ha utilizado para comunicarse con la entidad emisora de certificados del Plan inicial o el Plan empresarial al [inscribir el administrador de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin). Si ha seguido los mandatos de ejemplo de esta documentación, puede encontrar el certificado TLS en el archivo `$HOME/fabric-ca-client/tls-ibp/tls.pem`.

Puede ejecutar un mandato de árbol (tree) para verificar que se han completado estos pasos. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Establecimiento de variables de entorno de CLI
{: #ibp-peer-operate-environment-variables}

Después de mover todos los certificados a la ubicación necesaria, es necesario establecer algunas variables de entorno para que los mandatos las utilicen. A continuación, estará listo para utilizar el cliente de igual de Fabric para trabajar con el igual.

1. Recupere la información de configuración del Plan inicial o el Plan empresarial del archivo de **Perfil de conexión** que está disponible en la pantalla **Visión general** del supervisor de red. Pulse **Perfil de conexión** y luego **Descargar**.

  - Localice el URL de clasificador buscando **orderers**, que se encuentra bajo `orderers > url`. Anote el URL que comienza por el nombre de la red. El URL se parece al del siguiente ejemplo:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localice el nombre de su organización buscando **organizations**. Debería se la misma organización que utiliza para registrar el igual. Encontrará el nombre de la organización junto con su `mspid` asociado. Anote el valor del `mspid`.

2. [Busque la información de punto final del igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint). Necesita utilizar el punto final de igual para establecer la variable de entorno `PEERADDR`. Asegúrese de excluir la parte `http://` al principio.

3. Ejecute los mandatos siguientes para establecer las variables de entorno.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Sustituya los campos por su propia información.
    - Sustituya `<full_path_to_config_folder>` por la carpeta de configuración que se ha creado al
[descargar el cliente igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-download-fabric-client).
    - Sustituya `<CHANNEL_NAME>` por el nombre del canal al que se une el igual.
    - Sustituya `<CC_NAME>` por cualquier nombre que haga referencia a su código de encadenamiento.
    - Sustituya `<PEERADDR>` por el punto final de igual del paso anterior para el igual que esté utilizando actualmente.
    - Sustituya `<ORDERER_URL>` por el nombre de host y el puerto del clasificador del perfil de conexión.
    - Sustituya `<PATH_TO_ADMIN_MSP>` por la vía de acceso a la carpeta de MSP del administrador de igual.
    - Sustituya `<PATH_TO_PEER_TLS_CERT>` por la vía de acceso al certificado TLS de igual que ha descargado.
    - Sustituya `<MSPID_OF_PEER_BEING_USED>` por el MSPID de la organización del igual que utilice para obtener un bloque de origen, unirse a un canal o instalar el código de encadenamiento.

  Por ejemplo:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  También querrá habilitar TLS, lo que puede hacer emitiendo:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Utilización de la CLI para unir el igual al canal
{: #ibp-peer-operate-cli-join-channel}

Para poder ejecutar los mandatos de CLI para unir el igual a un canal, la organización se debe añadir a un canal de la red de una de las maneras siguientes.

  - Puede iniciar un nuevo canal para el igual. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).
  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

    Una vez que la organización se haya añadido a un canal, deberá añadir el signCert del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual carga su signCert durante la instalación, por lo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal.

    **Nota:** si el igual de {{site.data.keyword.cloud_notm}} Private que se conecta a una red de Plan inicial o Plan empresarial forma parte de la misma organización que otro igual que se ha añadido con el supervisor de red, solo podrá ver los bloques nuevos que se añaden al canal, el código de encadenamiento del que se crea una instancia y otras actualizaciones del canal en la pantalla "Canales" del supervisor de red. Esto se debe a que el supervisor de red recopila información en la pantalla "Canales" de su igual y no tiene visibilidad para los iguales que estén fuera de {{site.data.keyword.cloud_notm}}. Si ninguno de sus iguales utiliza la característica de datos privados, la información del supervisor de red será la misma tanto para un igual de una organización como para el otro.

1. Asegúrese de haber establecido las [variables de entorno en la CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-environment-variables).

2. Capte el bloque de origen del canal para crear el libro mayor del canal en el igual. Tenga en cuenta que
`$HOME/fabric-ca-client/tls-ibp/tls.pem` representa la vía de acceso al certificado TLS del Plan inicial o el Plan empresarial. Si su vía de acceso es distinta, asegúrese de establecer la correcta.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **Nota:** es posible que cuando ejecute alguno de estos mandatos de la CLI reciba el siguiente aviso, que puede pasar por alto sin problema.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  Ejecute el mandato siguiente para verificar que el bloque de origen se ha añadido correctamente al contenedor del igual:

  ```
  ls *.block
  ```
  {:codeblock}

  Sabrá que el bloque de origen se ha añadido correctamente cuando vea algo parecido al ejemplo siguiente:

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. Una vez que haya recuperado el bloque de origen del canal, puede unir el igual al canal utilizando el mandato siguiente:

  ```
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
{: #ibp-peer-operate-toolcontainer-install-cc}

Ahora estamos listos para instalar y crear una instancia de código de encadenamiento en el igual. En estas instrucciones, instalaremos el código de encadenamiento `fabcar` desde el repositorio `fabric-samples`. Asegúrese de haber
[configurado GOPATH ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Configure GOPATH") con anterioridad y, a continuación, descargue el código de encadenamiento `fabric-samples` desde github utilizando los mandatos siguientes:

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

Ejecute el siguiente mandato de CLI del igual para instalar el código de encadenamiento `fabcar` en el igual:

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

Cuando este mandato finalice correctamente, verá algo parecido a esto:

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**Nota:** si ya se ha instalado este código de encadenamiento y se ha creado una instancia del mismo en otro igual que se ejecuta en un Plan inicial o Plan empresarial, existen pasos adicionales que deberá realizar antes de instalar el código de encadenamiento en el igual. Consulte este [tema de resolución de problemas](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-troubleshooting) para obtener más instrucciones sobre cómo evitar errores asociados con el modo en que se instala este código de encadenamiento.

### Utilización de la CLI para crear una instancia de código de encadenamiento en un canal
{: #ibp-peer-operate-toolcontainer-instantiate-cc}

Dado que solo un igual tiene que crear una instancia del código de encadenamiento en un canal, no tiene que utilizar su igual para crear una instancia del código de encadenamiento. Los iguales alojados en {{site.data.keyword.blockchainfull_notm}} Platform pueden hacerlo. Sin embargo, si desea que el igual cree una instancia del código de encadenamiento en el canal, puede hacerlo ejecutando el mandato que se indica a continuación. Asegúrese de haber establecido el valor de
`CORE_PEER_TLS_ROOTCERT_FILE` en la vía de acceso del certificado TLS de la entidad emisora de certificados en el Plan inicial o Plan empresarial.

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

Cuando este mandato finalice correctamente, verá algo parecido a esto:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

Después de que se haya creado una instancia del código de encadenamiento, puede utilizar los mandatos de consulta e invocación del código de encadenamiento para leer y escribir datos en el libro mayor del canal. Para obtener más información, consulte los mandatos de
[código de encadenamiento de igual
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) en la documentación de Hyperledger Fabric. Necesitará pasar el punto final del clasificador a los mandatos de invocación utilizando la IP de proxy y el puerto de clasificador externo. Solo tiene que pasar el punto final de igual a un mandato de consulta.

## Actualización del código de encadenamiento
{: #ibp-peer-operate-update-chaincode}

Con el tiempo, es probable que tenga que modificar el código de encadenamiento que se ejecuta en el igual. Puesto que el código de encadenamiento se instala en los iguales y se crea una instancia del mismo en el canal, tiene que actualizar el código de encadenamiento en todos los iguales del canal.

Siga los pasos siguientes para actualizar el código de encadenamiento:

1. Para actualizar el código de encadenamiento en cada igual, simplemente vuelva a ejecutar el proceso que ha utilizado para instalar el código de encadenamiento en los iguales, utilizando una aplicación cliente o un mandato de CLI. Asegúrese de especificar el nombre de código de encadenamiento que se ha utilizado originalmente. Sin embargo, esta vez incremente la `Versión` del código de encadenamiento. Todos los iguales necesitan utilizar el mismo nombre y versión de código de encadenamiento.

2. Después de instalar el nuevo código de encadenamiento en todos los iguales del canal, utilice el supervisor de red o el mandato de [actualización de código de encadenamiento del igual ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para utilizar el nuevo código de encadenamiento.

Consulte el paso dos de estas [instrucciones](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) para obtener más información sobre cómo utilizar el panel "Instalar código" del supervisor de red para actualizar el código de encadenamiento en el canal.

## Visualización de los registros de igual
{: #ibp-peer-operate-view-logs}

Los registros de los componentes se pueden consultar desde la línea de mandatos mediante [mandatos de CLI `kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que está incluido en su clúster de {{site.data.keyword.cloud_notm}} Private.

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
[consola de gestión de clúster de {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre los registros en Kibana.

   **Nota:** al visualizar los registros en Kibana, es posible que reciba la respuesta `No results found`. Esta
condición se puede producir si {{site.data.keyword.cloud_notm}} Private utiliza la dirección IP del nodo trabajador como su nombre de host. Para resolver este problema, elimine el filtro que comienza por `node.hostname.keyword` al principio del panel y los registros se volverán visibles.

## Resolución de problemas
{: #ibp-peer-operate-troubleshooting}

### **Problema:** el mandato de invocación falla en el igual con un error `chaincode fingerprint mismatch`
{: #ibp-peer-operate
-install-error}

Es posible que reciba un error `chaincode fingerprint mismatch` al ejecutar una solicitud `peer chaincode invoke` en un igual que se ejecuta en {{site.data.keyword.cloud_notm}} Private:

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

Este error se puede producir si existe una incoherencia entre los iguales que ejecutan el código de encadenamiento en una de las áreas siguientes:

- Nombre de código de encadenamiento
- Versión de código de encadenamiento
- Vía de acceso de código de encadenamiento
- Binario de código de encadenamiento

Este error se puede producir si se ha utilizado la interfaz de usuario del supervisor de red para instalar y crear una instancia del código de encadenamiento en un igual que se ejecuta en un Plan inicial o Plan empresarial y, a continuación, se instala el código de encadenamiento en un igual que se ejecuta en {{site.data.keyword.cloud_notm}} Private. El error se produce en la solicitud
`invoke` debido a que las vías de acceso resultantes del código de encadenamiento en los iguales son distintas.

**Solución:** si desea ejecutar código de encadenamiento en iguales tanto en
{{site.data.keyword.cloud_notm}}, como el Plan inicial o empresarial, como en {{site.data.keyword.cloud_notm}} Private, no utilice la interfaz de usuario del supervisor de red para instalar el código de encadenamiento. En su lugar, empaquete el código de encadenamiento con el mandato [`peer chaincode package`![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) y, a continuación, instale el paquete en todos los iguales ejecutando el mandato [`peer chaincode install`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-toolcontainer-install-cc).

Si el código de encadenamiento ya se ha instalado y se ha creado una instancia del mismo en un canal antes de intentar instalar el código de encadenamiento en un igual de {{site.data.keyword.cloud_notm}} Private, deberá realizar los pasos siguientes para evitar el problema:

1. Empaquete el código de encadenamiento con el mandato [`peer chaincode package`![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package).
2. Instale el paquete del código de encadenamiento en el igual que se ejecuta en
{{site.data.keyword.cloud_notm}} Private ejecutando el mandato `peer chaincode install`.
3. Si tiene los binarios específicos de la plataforma, puede ejecutar el mandato
[`peer chaincode upgrade`![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) para actualizar el código de encadenamiento que se ejecuta en el igual del Plan inicial o el Plan empresarial, que utiliza el paquete de código de encadenamiento.
4. Cree una instancia del código de encadenamiento recién instalado en el canal utilizando la interfaz de usuario del supervisor de red o la CLI.

El proceso para actualizar el código de encadenamiento también se puede encontrar en
[`Código de encadenamiento para operadores`![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) en la documentación de Hyperledger Fabric.

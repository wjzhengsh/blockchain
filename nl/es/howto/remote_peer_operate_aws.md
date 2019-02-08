---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Funcionamiento de iguales en AWS
{: #remote-peer-operate-aws}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Después de configurar iguales de la plataforma {{site.data.keyword.blockchainfull}} en AWS, tiene que llevar a cabo varios pasos operativos para que el igual pueda enviar transacciones para consultar e invocar el libro mayor de la red blockchain. Los pasos incluyen añadir su organización a un canal, unir el igual al canal, instalar el código de encadenamiento en el igual,
crear una instancia del código de encadenamiento en el canal y conectar aplicaciones al igual. Puede utilizar los [SDK de Fabric](#aws-peer-operate-with-sdk) o la [línea de mandatos](#aws-peer-cli-operate) para realizar estos pasos operativos. Los SDK de Fabric son el método recomendado, aunque en las instrucciones se presupone que está familiarizado con el funcionamiento del SDK.

**Nota**: un igual de la plataforma {{site.data.keyword.blockchainfull_notm}} en AWS no tiene acceso a toda la funcionalidad ni al soporte de los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}}. Como resultado, no puede utilizar el supervisor de red para trabajar con el igual. Antes de empezar a ejecutar iguales en AWS, asegúrese de revisar las [consideraciones](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Utilización de los SDK de Fabric para trabajar con el igual
{: #aws-peer-operate-with-sdk}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista más reciente de lenguajes soportados y una lista de las API disponibles en los SDK de Hyperledger Fabric en la [documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"). Puede utilizar los SDK de Fabric para unir su igual a un canal en la plataforma {{site.data.keyword.blockchainfull_notm}}, instalar un código de encadenamiento en el igual y crear una instancia del código de encadenamiento en un canal.

En las siguientes instrucciones se utiliza el [Node SDK de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de Hyperledger Fabric para node.js") para trabajar con el igual y se da por supuesto que está familiarizado con el SDK. Puede utilizar la [guía de aprendizaje sobre desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) para aprender a utilizar Node SDK antes de empezar y como guía para desarrollar aplicaciones con el igual cuanto esté listo para invocar el código de encadenamiento de la consulta.

El Inicio rápido del igual de la plataforma {{site.data.keyword.blockchainfull_notm}} en AWS crea dos iguales para obtener alta disponibilidad. Por lo tanto, tiene que seguir los pasos operativos para cada igual. Una vez que esté listo para consultar e invocar código de encadenamiento desde su aplicación, haga que el SDK se conecte a ambos iguales para garantizar que las [aplicaciones tengan alta disponibilidad](/docs/services/blockchain/v10_application.html#ha-app).

### Instalación de Node SDK

Puede utilizar NPM para instalar [Node SDK ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "SDK de Hyperledger Fabric para node.js"):
```
npm install fabric-client@1.2
```
{:codeblock}

Se recomienda utilizar la versión 1.2 de Node SDK.

### Preparación del SDK para que trabaje con el igual
{: #remote-peer-node-sdk}

Antes de utilizar el SDK para trabajar con el igual, debe generar los certificados necesarios (inscripción) que permitirán que la aplicación se comunique con la red en la plataforma {{site.data.keyword.blockchainfull_notm}} y su igual. Siga los pasos para [inscribir el SDK](/docs/services/blockchain/v10_application.html#enroll-app-sdk) utilizando la identidad **admin**. En la guía de aprendizaje sobre [Desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) también se inscribe como **admin**, de modo que no tiene que modificar el código de ejemplo.

### Carga de un signcert en la plataforma IBM Blockchain
{: #remote-peer-upload-SDK}

Es necesario subir el certificado de firma de SDK a la red en la plataforma {{site.data.keyword.blockchainfull_notm}} para que los demás miembros puedan reconocer su firma digital.

- Encontrará el certificado de firma en el directorio en el que su SDK ha generado el material criptográfico, en el archivo denominado admin. Copie el certificado que hay entre comillas tras el campo `certificate`, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE-----`. Puede utilizar la CLI para convertir el certificado al formato PEM con el mandato `echo -e "<CERT>" > admin.pem`. Luego puede pegar el contenido del certificado en la red blockchain utilizando el supervisor de red. Inicie una sesión en la red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla "Miembros" del supervisor de red, pulse **Certificados ** > **Añadir certificado**. Especifique un nombre para el certificado y pegue el contenido en el campo **Certificado**. Pulse ** Reiniciar ** cuando se le pregunte si desea reiniciar los iguales. Esta acción se debe llevar a cabo antes de que la organización se una al canal.

### Carga de un signcert en el igual
{: #remote-peer-upload-signcert}

También es necesario cargar el certificado de firma del SDK en el igual remoto y reiniciarlo. Debe instalar el mismo certificado de firma que el que haya [cargado en la plataforma IBM Blockchain](#remote-peer-upload-SDK) dentro del contenedor del igual remoto.

Utilice SSH en la instancia de VPC seleccionando la instancia den la consola de AWS (pulse **Servicios > EC2 > Instancias**) y, a continuación, pulsando el botón Conectar. Siga las instrucciones de AWS para emitir el mandato ssh.

Ejecute los mandatos siguientes desde dentro del contenedor del igual para cargar el certificado.  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- Sustituya `<PEER_ENROLL_ID>` por el ID de inscripción que se especifica en la plantilla de Inicio rápido y que está asociado con esta instancia del igual remoto.  
- Sustituya `<CERT.PEM>` por su signcert, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE-----`.    

  **Nota:** si el archivo `cert.pem` ya existe, cree un nuevo archivo en lugar de sobrescribirlo, por ejemplo, `cert2.pem`.

Como ha añadido un nuevo certificado, tiene que reiniciar el contenedor para que el igual adopte el certificado. Siga estas [instrucciones](#remote-peer-aws-restart) para reiniciar el igual.

### Cómo pasar el certificado TLS del igual al SDK
{: #aws-remote-peer-download-tlscert}

Tiene que copiar el contenido del archivo `cacert.pem` de TLS del contenedor del igual en la aplicación para autenticar la comunicación procedente del SDK. Ejecute el mandato siguiente en el contenedor de la CLI del igual (Peer). Sustituya `<PEER_ENROLL_ID>` por el nombre de pila del igual remoto que ha especificado en la plantilla de Inicio rápido. (Recuerde que se crean dos instancias de VPC).
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

Copie todo el texto del archivo `ca.crt` en el portapapeles, que empieza por el primer `-----BEGIN CERTIFICATE-----` y termina con el último `-----END CERTIFICATE-----`. Guarde este certificado en el sistema de archivos local en formato PEM. Luego puede importar el certificado TLS en la aplicación con un sencillo mandato de lectura de archivo.
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### Cómo proporcionar la información de punto final del igual al SDK
{: #remote-peer-SDK-endpoints}

Para recuperar la información de punto final del igual, localice la instancia del igual en la consola de AWS. Tome nota del valor de
`AWS EC2 dashboard Public DNS (IPv4)` para la instancia de EC2 y proporciónelo en el SDK declarando una nueva variable de igual o actualizando el perfil de conexión. Concatene la dirección DNS pública con el puerto `7051`. En el ejemplo siguiente se define el igual en la red de Fabric y se pasa el certificado TLS que ha importado.

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**Nota:** como el igual es remoto, otras organizaciones de la red de la plataforma {{site.data.keyword.blockchainfull_notm}} no podrán encontrar la información de punto final de si igual en el perfil de conexión. Si otras organizaciones necesitan enviar a su igual una transacción para que se apruebe, debe proporcionar los certificados IP y TLS públicos en otra operación.

### Utilización del SDK para unirse a un canal
{: #remote-peer-join-channel-sdk}

Como miembro de la red blockchain, su organización debe añadirse a un canal de la red para que pueda unir su igual al canal.

  - Puede iniciar un nuevo canal para el igual. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Una vez que la organización se haya añadido a un canal, deberá añadir el certificado de firma del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal. Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir los mandatos join channel.

Una vez que su organización forme parte del canal, siga las instrucciones del apartado sobre [Cómo unirse a un canal](/docs/services/blockchain/v10_application.html#join-channel-sdk). Debe especificar URL del servicio de ordenación y el nombre del canal.

### Utilización del SDK para instalar el código de encadenamiento en el igual
{: #remote-peer-install-cc-sdk}

Siga las instrucciones para utilizar el SDK para [instalar un código de encadenamiento](/docs/services/blockchain/v10_application.html#install-cc-sdk) en el igual.

### Utilización del SDK para crear una instancia de código de encadenamiento en el canal
{: #remote-peer-instantiate-cc-sdk}

Sólo un miembro del canal necesita crear una instancia o actualizar el código de encadenamiento. Por lo tanto, cualquier miembro de red del canal con iguales en la plataforma {{site.data.keyword.blockchainfull_notm}} puede utilizar el supervisor de red para crear una instancia de código de encadenamiento y especificar políticas de aprobación. Sin embargo, si desea utilizar el igual para crear una instancia del código de encadenamiento en un canal, puede utilizar el SDK y seguir las instrucciones para [crear una instancia de un código de encadenamiento](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).


## Utilización de la CLI para trabajar con el igual
{: #aws-peer-cli-operate}

También puede trabajar con el igual desde la línea de mandatos utilizando el contenedor de herramientas de Fabric CA y el cliente de Fabric CA. En estas instrucciones, primero generaremos los certificados necesarios utilizando el cliente de CA de Fabric. A continuación, utilizaremos el contenedor de herramientas de Fabric para trabajar con el igual uniéndolo a un canal, instalando un código de encadenamiento y, a continuación, creando una instancia del código de encadenamiento en un canal.

### Inscripción mediante el cliente de CA de Fabric
{: #peer-client-enroll}

El primer paso consiste en generar los certificados necesarios (inscripciones) utilizando el cliente CA de Fabric. Primero tiene que instalar el cliente de CA de Fabric. Descargue los [binarios fabric-ca v1.2.0 para su plataforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) en la máquina local, extráigalos y muévalos a una carpeta, como por ejemplo `$HOME/fabric-ca-remote/`.

1.  Prepare el entorno para que utilice el cliente CA de Fabric. Vaya al directorio al que ha movido los binarios del cliente para poder hacer referencia a los mismos directamente en sus mandatos.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Establezca la vía de acceso en la que el cliente puede crear las claves.  Si no ha ejecutado el mandato `enroll` anteriormente, la carpeta `msp` y el archivo `.yaml` no existen. Si ha ejecutado el mandato `enroll` anteriormente, es importante que suprima el material de configuración de intentos anteriores de inscripción con los mandatos `rm` siguientes:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Deberá recuperar la información de punto final de la entidad emisora de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla "Visión general" del supervisor de red, pulse el botón **Perfil de conexión** y abra el archivo JSON que contiene las credenciales de red. Desplácese hacia abajo hasta la sección `certificateAuthorities` y anote la información siguiente.
    ```
    Nombre de host y puerto para CA: url (sin prefijo https)
    ID de usuario admin: enrollId
    Contraseña de admin: enrollSecret
    Nombre de CA: caName
    ```

4.  También tendrá que recuperar un certificado TLS de la plataforma {{site.data.keyword.blockchainfull_notm}} para comunicarse con la CA. Las credenciales de red anteriores incluyen el certificado necesario. En la sección `certificateAuthorities` del archivo de perfil de conexión, copie el certificado que hay tras `"pem:"`, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE----- `. **No incluya las comillas**.

    Ejecute el siguiente mandato desde una ventana de terminal, sustituyendo `<certificate>` por el certificado que ha copiado.
    ```
    echo -e "<certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Guarde este certificado en un lugar al que pueda hacer referencia en futuros mandatos.  

5.  Ahora está preparado para generar los certificados necesarios. Para ello, ejecute el siguiente mandato:
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    Por ejemplo:
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

   Cuando el mandato finalice correctamente, verá una respuesta parecida a la del siguiente ejemplo:
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    El mandato generará los certificados necesarios y los guardará en una carpeta llamada `msp` en `$FABRIC_CA_CLIENT_HOME`. Esta carpeta y los certificados que contiene serán importantes en los pasos siguientes.

### Gestión de los certificados en el sistema local
{: #manage-certs}

Para poder trabajar con el igual, tenemos que realizar ciertas tareas de gestión de los certificados en la máquina local y cargar algunos de los certificados que el cliente de CA de Fabric ha generado en la plataforma {{site.data.keyword.blockchainfull_notm}} y en el igual. También tenemos que descargar los certificados TLS de la plataforma y del igual. Si desea obtener más información acerca de los certificados con los que va a trabajar y las tareas que va a realizar, consulte [Gestión de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/certificates.html).

En la máquina local, abra un terminal de mandatos y vaya al directorio al ha movido los binarios Fabric-CA-Client y ha almacenado la carpeta de MSP.

1. Copie el archivo `cert.pem` de la carpeta `signcerts` en una nueva carpeta `admincerts`.
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. Copie el certificado TLS de los clasificadores de la plataforma {{site.data.keyword.blockchainfull_notm}} a la carpeta de MSP. El perfil de conexiones contiene el certificado necesario. En la pantalla "Visión general" del supervisor de red, pulse el botón **Perfil de conexión** y abra el archivo JSON que contiene las credenciales de red. Vaya a la sección `clasificadores`. Copie el certificado que sigue a `"pem:"`, que comienza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE----- `. No incluya las comillas.

    Desde la ventana de terminal, ejecute los mandatos siguientes:
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. También debe copiar el certificado TLS del igual desde el contenedor del igual en AWS a la máquina local.

    - [Siga estas instrucciones](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test) para iniciar sesión en el contenedor del igual y ejecutar el mandato siguiente, sustituyendo <PEER_ENROLL_ID> por el nombre de pila del igual, que ha especificado en la plantilla de Inicio rápido, seguido por su número. (Recuerde que se crean dos instancias de VPC).
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
      ```
      {:codeblock}

      Copie todo el texto del archivo `cacert.pem` en el portapapeles, que empieza por el primer `-----BEGIN CERTIFICATE-----` y termina con el último `-----END CERTIFICATE-----`.

    - Vuelva a la máquina local y ejecute los siguientes mandatos. Sustituya el texto `<CACERT.PEM>` por el texto del portapapeles.
      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
      ```
      {:codeblock}

      **Nota:** de forma predeterminada, la plantilla de Inicio rápido de AWS crea dos instancias del igual en dos zonas de disponibilidad distintas. Si ya ha realizado estos pasos para uno de estos iguales, cuando realice estos pasos para la segunda instancia, el archivo cacert.pem ya existirá. Continúe y sustitúyalo por el certificado del segundo igual.

4. Para otorgar a la CLI la autorización para instalar código de encadenamiento en el igual, cargue el signCert generado por el cliente de CA de Fabric en la carpeta de administración del igual y reinicie el igual. Por lo tanto, copie el certificado
`admincert/cert.pem` de la máquina local en el directorio `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` del contenedor del igual como `cert2.pem`.

    - En la máquina local, ejecute los mandatos siguientes:

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copie el texto del archivo `cert.pem` en el portapapeles, desde `-----BEGIN CERTIFICATE-----` hasta `-----END CERTIFICATE-----`.

    - Vuelva al contenedor del igual y ejecute los mandatos siguientes para añadir el archivo cert.pem a los certificados de los iguales. Sustituya el texto `<CERT.PEM>` por el texto del portapapeles.

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** ya existe un archivo cert.pem en este directorio. No lo sobrescriba.

    - Dado que hemos añadido un nuevo certificado, será necesario reiniciar el contenedor para que el igual adopte el certificado. Siga estas instrucciones para [reiniciar el contenedor del igual](#remote-peer-aws-restart).

5. Cargue el mismo certificado `admincert/cert.pem` de la máquina local en la plataforma {{site.data.keyword.blockchainfull_notm}} para que una aplicación o CLI remota pueda realizar operaciones de canal, tales como capturar el bloque de origen del canal y crear una instancia del código de encadenamiento.
    1. En la máquina local, localice `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` y cópielo en el portapapeles.
    2. Entre en el supervisor de su red en la plataforma {{site.data.keyword.blockchainfull_notm}}.
    3. Pulse **Miembros** en el navegador de la izquierda y pulse el separador **Certificados**.
    4. Pulse el botón **Añadir certificado**.
    5. En la ventana **Añadir certificado**, asigne un nombre al certificado, como por ejemplo `fabrictools.pem`, pegue el certificado que acaba de copiar en el portapapeles y pulse **Enviar**.
    6. Se le preguntará si desea reiniciar los iguales. Pulse **Reiniciar**.
    7. Pulse **Canales** en el navegador de la izquierda y localice el canal al que se unirá el igual.
    8. Pulse los tres puntos bajo la cabecera **Acciones** y pulse **Sincronizar certificado**. En la ventana **Sincronizar certificado**, pulse **Enviar**.

    **Nota**: es importante sincronizar el certificado de canal antes de que el igual se una al canal o cree una instancia de código de encadenamiento en el canal. Es posible que tenga que esperar unos minutos a que finalice la sincronización del canal antes de emitir los mandatos de unión al canal o de creación de una instancia de código de encadenamiento.   

### Configuración del contenedor de herramientas de Fabric
{: #setup-fabric-cli}

Después de mover todos los certificados a la ubicación necesaria, puede instalar y utilizar el contenedor de herramientas de Fabric desde Docker. Asegúrese de ejecutar estos mandatos desde el directorio donde se ha guardado la carpeta de MSP.

1.  Descargue la imagen de Docker de las herramientas de Fabric con el mandato siguiente:
    ```
    docker pull ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

2.  Ejecute los mandatos siguientes para iniciar el contenedor de herramientas de Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

    Este mandato montará el directorio de MSP dentro del contenedor de herramientas.

<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### Utilización de la CLI de herramientas de Fabric para unir el igual al canal
{: #fabric-cli-join-peer-to-channel}

Para poder ejecutar los mandatos de CLI para unir el igual a un canal, la organización se debe añadir a un canal de la red.

  - Puede iniciar un nuevo canal para el igual. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Una vez que la organización se haya añadido a un canal, deberá añadir el certificado de firma del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal.

1. Recupere la información de configuración del `Perfil de conexión` disponible en el panel Visión general del supervisor de red. Pulse **Perfil de conexión** y luego **Descargar**.

  - Localice el URL de clasificador buscando **orderers**, que se encuentra bajo `orderers > url`. Anote el URL que comienza por el nombre de la red. El URL se parece al del siguiente ejemplo:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localice el nombre de su organización buscando **organizations**. Debería se la misma organización que utiliza para registrar el igual. Encontrará el nombre de la organización junto con su `mspid` asociado. Anote el valor del `mspid`.

2. Localice la información de punto final del igual de VPC de AWS (la dirección IPv4 pública de DNS) en el DNS público del panel de control de EC2 de AWS (IPv4). Tendrá un aspecto similar al siguiente:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  Utilice esta información para crear la variable de entorno `PEERADDR` concatenando la dirección con el puerto '7051', de manera que el valor de `PEERADDR` sería similar a:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. Ejecute los siguientes mandatos para definir las variables de entorno en el contenedor de herramientas de Fabric.

    ```
    export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
    ```
    {:codeblock}

    Sustituya los campos por su propia información.
      - Sustituya `<ORDERER_URL>` por el nombre de host y el puerto del clasificador que encontrará en el archivo `creds.json`.
      - Sustituya `<CHANNEL_NAME>` por el nombre del canal al que se une el igual.
      - Sustituya `<CC_NAME>` por cualquier nombre que haga referencia a su código de encadenamiento.
      - Sustituya `<ORGANIZATION_MSP_ID>` por el nombre de la organización que encontrará en el archivo `creds.json`.
      - Sustituya `<PEER_ADDR>` por el valor que ha construido en el paso anterior.  

  Por ejemplo:

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. Capte el bloque de origen del canal para crear el libro mayor del canal en el igual. Primero vaya (`cd`) al directorio root y luego ejecute el mandato para captar el bloque de origen.

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
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

  Cuando se haya añadido correctamente el bloque de origen, debería ver algo similar al ejemplo siguiente:
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. Ahora una el igual al canal pasando el bloque de origen y utilizando el mandato siguiente:

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Cuando finalice correctamente, debería ver algo similar a:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilización del contenedor de herramientas de Fabric para instalar el código de encadenamiento en el igual
{: #aws-toolcontainer-install-cc}

Ahora estamos listos para instalar y crear una instancia de código de encadenamiento en el igual. En estas instrucciones, instalaremos el código de encadenamiento `fabcar` desde el repositorio `fabric-samples`. Descargue el código de encadenamiento `fabric-samples` de GitHub utilizando los mandatos siguientes:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Ejecute el siguiente mandato de CLI del igual para instalar el código de encadenamiento `fabcar` en el igual.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Cuando este mandato finalice correctamente, verá algo parecido a esto:

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilización del contenedor de herramientas de Fabric para crear una instancia del código de encadenamiento en el canal
{: #aws-toolcontainer-instantiate-cc}

Dado que solo un igual tiene que crear una instancia del código de encadenamiento en un canal, no tiene que utilizar su igual para crear una instancia del código de encadenamiento. Los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}} pueden hacerlo. Sin embargo, si desea que el igual cree una instancia del código de encadenamiento en el canal, puede ejecutar el mandato siguiente en el contenedor de herramientas de Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

Cuando este mandato finalice correctamente, verá algo parecido a esto:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Reinicio de un igual que se ejecuta en AWS
{: #remote-peer-aws-restart}

Puede iniciar, detener o reiniciar el igual en el entorno donde lo despliegue. Siempre que se añada un certificado al igual, será necesario reiniciar el igual para que se reconozca el nuevo certificado. Hay varias maneras de hacerlo:

- En la consola de AWS, rearranque la instancia de VPC del igual.
- Ejecute el mandato siguiente desde el contenedor del igual:

 ```
 docker restart peer
 ```
 {:codeblock}  

También puede utilizar la [solicitud HEAD](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes) para comprobar la disponibilidad del igual.

## Visualización de los registros de igual

Utilice SSH en la instancia del igual de VPC de AWS y, a continuación, ejecute el mandato siguiente para ver los registros del igual:

```
docker logs peer
```
{:codeblock}

## Actualización del código de encadenamiento

Con el tiempo, es probable que tenga que modificar el código de encadenamiento que se ejecuta en el igual. Puesto que el código de encadenamiento se instala en los iguales y se crea una instancia del mismo en el canal, tiene que actualizar el código de encadenamiento en todos los iguales del canal.

Siga los pasos siguientes para actualizar el código de encadenamiento:

1. Para actualizar el código de encadenamiento en cada igual en AWS, simplemente vuelva a ejecutar el proceso que ha utilizado para instalar el código de encadenamiento en los iguales mediante una aplicación cliente o un mandato de CLI. Asegúrese de especificar el nombre de código de encadenamiento que se ha utilizado originalmente. Sin embargo, esta vez incremente el número de `Versión` del código de encadenamiento.

2. Después de instalar el nuevo código de encadenamiento en todos los iguales del canal, utilice el supervisor de red o el mandato de [actualización de código de encadenamiento del igual ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para utilizar el nuevo código de encadenamiento.

Consulte el paso dos de estas [instrucciones](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) para obtener más información sobre cómo utilizar el panel Instalar código del supervisor de red para actualizar el código de encadenamiento en el canal.

## Resolución de problemas

### **Problema:** el igual remoto no se puede conectar a la red blockchain

LA creación de la pila finaliza correctamente, pero los registros de Docker contienen el error:

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

**Solución:**  
Este error puede venir provocado por la omisión de la especificación de un puerto en el CAUrl cuando se desplegó la plantilla de Inicio rápido.
El CAUrl debe tener un aspecto similar a `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`.
Vuelva a desplegar la plantilla de Inicio rápido, teniendo cuidado de especificar el valor correcto para el CAUrl.

### **Problema:** la creación de la instancia del código de encadenamiento falla con un error

La creación de la instancia del código de encadenamiento falla con el error:
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**Solución:**   
Asegúrese de que, una vez que se haya cargado el certificado de administrador en el supervisor de red, los certificados se sincronicen luego en el canal. Consulte el paso cinco de estas [instrucciones](#manage-certs) para obtener más información. Tenga en cuenta que es importante sincronizar el certificado del canal antes de que el igual se una al canal.

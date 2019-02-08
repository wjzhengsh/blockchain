---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Funcionamiento de iguales remotos en {{site.data.keyword.cloud_notm}} privado
{: #remote-peer-operate}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Después de configurar iguales remotos de la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.cloud_notm}} privado (ICP), tiene que llevar a cabo varios pasos operativos para que el igual pueda enviar transacciones para consultar e invocar el libro mayor de la red blockchain. Los pasos incluyen añadir su organización a un canal, unir el igual remoto al canal, instalar el código de encadenamiento en el igual remoto, crear una instancia del código de encadenamiento en el canal y conectar aplicaciones al igual remoto.
{:shortdesc}

Es necesario completar algunos pasos de requisito previo desde el clúster ICP para trabajar con el igual remoto.

**Requisitos previos:**
- [Recuperación de la información de punto final del igual remoto](#remote-peer-retrieve-endpoint-info)
- [Inicio de sesión en el contenedor del igual mediante kubectl](#remote-peer-kubectl-configure)
- [Reinicio del igual remoto](#remote-peer-restart)

Luego puede utilizar uno de los métodos siguientes para utilizar el igual remoto.

**Métodos de funcionamiento:**
- [Utilización de los SDK de Fabric](#remote-peer-operate-with-sdk)
- [Utilización de la línea de mandatos](#remote-peer-cli-operate)

Los SDK de Fabric son el método recomendado, aunque en las instrucciones se presupone que está familiarizado con el funcionamiento del SDK.

Se recomienda desplegar al menos dos instancias del diagrama helm del igual remoto para obtener [alta disponibilidad](/docs/services/blockchain/howto/remote_peer_icp.html#high-availability). Por lo tanto, tiene que seguir estos pasos operativos para cada igual. Cuando esté preparado para invocar y consultar código de encadenamiento desde la aplicación, conecte ambos iguales y asegúrese de que [las aplicaciones tienen alta disponibilidad](/docs/services/blockchain/v10_application.html#ha-app).

**Nota**: un igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} no tiene acceso a toda la funcionalidad ni al soporte de los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}}. Como resultado, no puede utilizar el supervisor de red para trabajar con un igual remoto. Antes de empezar a ejecutar iguales remotos, asegúrese de revisar las [consideraciones](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Requisitos previos

Independientemente de si tiene previsto utilizar los SDK o la línea de mandatos, debe completar los pasos de esta sección para poder utilizar el igual. Puede completar los dos primeros pasos, recuperar el punto final del igual y configurar kubectl antes de empezar.

### Recuperación de la información de punto final del igual remoto
{: #remote-peer-retrieve-endpoint-info}

Inicie una sesión en la consola de ICP. Pulse el icono **Menú** en la esquina superior izquierda. Pulse **Carga de trabajo** > **Despliegues**. A continuación, busque y pulse el nombre de la instancia del igual remoto para abrir la pantalla de visión general de despliegue. Desplácese hacia abajo hasta la sección **Exponer detalles** y busque la `IP de Ingress` y los `Puertos` del igual remoto.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Tome nota de la dirección IP de Ingress y de los puertos de nodo para conectarse a su igual remoto. Deberá utilizar esta información al instalar y crear una instancia del código de encadenamiento en el igual remoto. En el ejemplo siguiente, la dirección del igual es `9.46.126.89:30162`, `9.46.126.89:30260` o `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### Configuración de la CLI de kubectl
{: #remote-peer-kubectl-configure}

Tiene que utilizar la herramienta de línea de mandatos **kubectl** para realizar algunas operaciones necesarias dentro del contenedor del igual que se ejecuta en ICP. Inicie una sesión en el clúster utilizando la herramienta de CLI de {{site.data.keyword.cloud_notm}} privado:

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

A continuación, inicie una sesión en el panel de control de su clúster ICP. Pulse en el avatar de usuario en la esquina superior derecha de la interfaz de usuario de ICP y luego pulse **Configurar cliente**. Copie, pegue y ejecute los mandatos en la ventana de terminal de la máquina local. Para verificar que la configuración ha funcionado correctamente, ejecute el siguiente mandato:

```
kubectl -n <namespace> get pods
```
{:codeblock}

donde `-n <namespace>` se debe especificar cuando no se utiliza el espacio de nombres `default`. Sustituya `<namespace>` por el valor de su espacio de nombres en el clúster ICP.


El mandato debe dar como resultado una respuesta parecida a la del ejemplo siguiente. `remotepeer-bd65c4769-95rmm` es el nombre del contenedor del igual.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

Si no reconoce el nombre de la pod de los iguales remotos de la respuesta, encontrará el nombre de su pod en el panel **Pods** del panel de despliegue.

Ejecute en el contenedor del igual remoto con el mandato siguiente, sustituyendo el valor de `<PEER_CONTAINER_NAME>` por el nombre del pod de arriba.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
donde `-n <namespace>` se debe especificar cuando no se utiliza el espacio de nombres `default`. Sustituya `<namespace>` por el valor de su espacio de nombres en el clúster ICP.

Por ejemplo:

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

Ahora ya está preparado para utilizar la CLI de contenedor del igual. Es necesario utilizar la CLI para descargar el certificado TLS del igual y cargar el certificado de firma del igual si utiliza los SDK o la línea de mandatos.

## Reinicio de un igual remoto que se ejecuta en ICP
{: #remote-peer-restart}

Siempre que se añade un certificado al igual remoto, el nodo igual debe reiniciarse para que reconozca el nuevo certificado.

Puede utilizar los mandatos **kubectl** para reiniciar el igual remoto que se ejecuta en ICP. El mandato requiere el nombre del **pod** del igual remoto y su **contenedor** como parámetros del mandato. Para obtener más información sobre cómo utilizar los mandatos kubectl, consulte [configuración de la CLI de kubectl](#remote-peer-kubectl-configure).

1. En la consola de ICP, busque el nombre del **pod** y el nombre del **contenedor** para el despliegue del igual remoto.

  1. Pulse el icono de menú en la esquina superior izquierda de la consola y luego pulse **Cargas de trabajo > Despliegues**.
  2. Localice y pulse en el release de igual remoto en la tabla para abrirlo.
  3. Desplácese hacia abajo para ver el nombre de **pod** asociado. Anote el nombre de **pod**.
  4. Pulse el pod para abrirlo.
  5. Pulse el separador **Contenedores**. Anote el nombre de **contenedor** del igual remoto.

2. En la línea de mandatos, ejecute el siguiente mandato para reiniciar el igual, sustituyendo `<REMOTE_PEER_POD_NAME>` y `<REMOTE_PEER_CONTAINER_NAME>` por los valores de arriba.

  ```
  kubectl exec <REMOTE_PEER_POD_NAME> -c <REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. Para verificar que el igual remoto se ha reiniciado, ejecute el mandato `kubectl get pods` y examine la salida del recuento de **RESTART** correspondiente al pod.

También puede utilizar la [solicitud HEAD](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes) para comprobar la disponibilidad del igual remoto.


## Utilización de los SDK de Fabric para trabajar con el igual remoto
{: #remote-peer-operate-with-sdk}

Los SDK de Hyperledger Fabric ofrecen un potente conjunto de API que permiten a las aplicaciones interactuar con las redes blockchain. Encontrará la lista más reciente de lenguajes soportados y una lista de las API disponibles en los SDK de Fabric en la [documentación de la comunidad de SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentación de la comunidad de SDK de Hyperledger Fabric"). Puede utilizar los SDK de Fabric para unir su igual remoto a un canal en la plataforma {{site.data.keyword.blockchainfull_notm}}, instalar un código de encadenamiento en el igual y crear una instancia del código de encadenamiento en un canal.

En las siguientes instrucciones se utiliza el [Node SDK de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK de Fabric") para trabajar con el igual remoto y se da por supuesto que está familiarizado con el SDK. Puede utilizar la [guía de aprendizaje sobre desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) para aprender a utilizar Node SDK antes de empezar y como guía para desarrollar aplicaciones con el igual remoto cuanto esté listo para invocar el código de encadenamiento de la consulta.

### Instalación de Node SDK

Puede utilizar NPM para instalar [Node SDK ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/ "Node SDK"):
```
npm install fabric-client@1.1
```
{:codeblock}

Se recomienda utilizar la versión 1.1 de Node SDK.

### Preparación del SDK para que trabaje con el igual remoto
{: #remote-peer-node-sdk}

Antes de utilizar el SDK para trabajar con el igual remoto, debe generar los certificados necesarios (inscripción) que permitirán que la aplicación se comunique con la red en la plataforma {{site.data.keyword.blockchainfull_notm}} y su igual remoto. Siga los pasos para [inscribir el SDK](/docs/services/blockchain/v10_application.html#enroll-app-sdk) utilizando la identidad **admin**. En la guía de aprendizaje sobre [Desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) también se inscribe como **admin**, de modo que no tiene que modificar el código de ejemplo.

### Carga de un signCert en la plataforma {{site.data.keyword.blockchainfull_notm}}
{: #remote-peer-upload-sdk}

Es necesario subir el certificado de firma de SDK a la red en la plataforma {{site.data.keyword.blockchainfull_notm}} para que la aplicación tenga permiso para trabajar en la red.

- Encontrará el certificado de firma en el directorio en el que su SDK ha generado el material criptográfico, en el archivo denominado admin. Copie el certificado que hay entre comillas tras el campo `certificate`, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE-----`. Puede utilizar la CLI para convertir el certificado al formato PEM con el mandato `echo -e "<CERT>" > admin.pem`. Luego puede pegar el contenido del certificado en la red blockchain utilizando el supervisor de red. Inicie una sesión en la red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla "Miembros" del supervisor de red, pulse **Certificados ** > **Añadir certificado**. Especifique un nombre para el certificado y pegue el contenido en el campo **Certificado**. Pulse ** Reiniciar ** cuando se le pregunte si desea reiniciar los iguales. Esta acción se debe llevar a cabo antes de que la organización se una al canal.

### Carga de un signCert en el igual remoto
{: #remote-peer-upload-signcert}

También es necesario cargar el certificado de firma del SDK en el igual remoto y reiniciarlo. Tiene que instalar el mismo certificado de firma que ha [cargado en la plataforma {{site.data.keyword.blockchainfull_notm}}](#remote-peer-upload-sdk) en el contenedor del igual remoto.

Ejecute los siguientes mandatos desde [dentro del contenedor del igual](#remote-peer-kubectl-configure) para cargar el certificado.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Sustituya el texto `<CERT.PEM>` por su signCert, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE-----`. **Tenga en cuenta** que ya existe un archivo cert.pem en este directorio. Llame al nuevo certificado `cert2.pem` o algo parecido para no sobrescribir el certificado que ya está en el contenedor.

Como ha añadido un nuevo certificado, tiene que reiniciar el contenedor para que el igual adopte el certificado. Siga estas [instrucciones](#remote-peer-restart) para reiniciar el igual.

### Cómo pasar el certificado TLS del igual remoto al SDK
{: #icp-remote-peer-download-tlscert}

Tiene que copiar el contenido del archivo `cacert.pem` de TLS del contenedor del igual en la aplicación para autenticar la comunicación procedente del SDK. En el [contenedor de CLI del igual](#remote-peer-kubectl-configure), ejecute el siguiente mandato:
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Copie todo el texto del archivo `cacert.pem` en el portapapeles, que empieza por el primer `-----BEGIN CERTIFICATE-----` y termina con el último `-----END CERTIFICATE-----`. Guarde este certificado en el sistema de archivos local en formato PEM. Luego puede importar el certificado TLS en la aplicación con un sencillo mandato de lectura de archivo.
```
var caCert = fs.readFileSync(path.join(__dirname, './cacert.pem'));
```
{:codeblock}

### Cómo proporcionar la información de punto final del igual remoto al SDK
{: #remote-peer-SDK-endpoints}

Busque la [información de punto final del igual remoto](#remote-peer-retrieve-endpoint-info) y proporciónela al SDK, declarando una nueva variable peer o actualizando el perfil de conexión. En el ejemplo siguiente se define el igual como un punto final en la red de fabric y se pasa el certificado TLS que ha importado.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

Tiene que especificar un `ssl-target-name-override` de `<something>.blockchain.com` para que el igual resuelva la solicitud DNS.

**Nota:** como el igual es remoto, otras organizaciones de la red de la plataforma {{site.data.keyword.blockchainfull_notm}} no podrán encontrar la información de punto final de si igual en el perfil de conexión. Si otras organizaciones necesitan enviar a su igual remoto una transacción para que se apruebe, debe proporcionar los certificados IP y TLS públicos en otra operación.

### Utilización del SDK para unirse a un canal
{: #remote-peer-join-channel-sdk}

Como miembro de la red blockchain, su organización debe añadirse a un canal de la red para que pueda unir su igual remoto al canal.

  - Puede iniciar un nuevo canal para el igual remoto. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel). Tenga en cuenta que debe tener al menos un igual en la plataforma {{site.data.keyword.blockchainfull_notm}} para poder crear un canal en el supervisor de red.  

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). Un miembro del canal con iguales en la plataforma {{site.data.keyword.blockchainfull_notm}} puede utilizar el supervisor de red para añadir su organización al canal aunque no aloje ningún igual en la plataforma.

    Una vez que la organización se haya añadido a un canal, deberá añadir el certificado de firma del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual remoto carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal.

Cuando su organización forme parte del canal, siga las instrucciones del apartado sobre [cómo unirse a un canal](/docs/services/blockchain/v10_application.html#join-channel-sdk). Debe especificar URL del servicio de ordenación y el nombre del canal.

### Utilización del SDK para instalar el código de encadenamiento en el igual
{: #remote-peer-install-cc-sdk}

Siga las instrucciones siguientes para utilizar el SDK para [instalar un código de encadenamiento](/docs/services/blockchain/v10_application.html#install-cc-sdk) en el igual remoto.

### Utilización del SDK para crear una instancia de código de encadenamiento en el canal
{: #remote-peer-instantiate-cc-sdk}

Sólo un miembro del canal necesita crear una instancia o actualizar el código de encadenamiento. Por lo tanto, cualquier miembro de red del canal con iguales en la plataforma {{site.data.keyword.blockchainfull_notm}} puede utilizar el supervisor de red para crear una instancia de código de encadenamiento y especificar políticas de aprobación. Sin embargo, si desea utilizar el igual remoto para crear una instancia del código de encadenamiento en un canal, puede utilizar el SDK y seguir las instrucciones para [crear una instancia de un código de encadenamiento](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).


## Utilización de la CLI para trabajar con el igual remoto
{: #remote-peer-cli-operate}

También puede trabajar con el igual remoto desde la línea de mandatos utilizando el contenedor de herramientas de Fabric CA y el cliente de Fabric CA. En estas instrucciones, primero se generan los certificados necesarios utilizando el Cliente CA de Fabric y, a continuación, se utiliza el contenedor de herramientas de Fabric para trabajar con el igual, uniéndolo a un canal, instalando un código de encadenamiento y luego creando una instancia del código de encadenamiento en un canal.

### Inscripción mediante el cliente de CA de Fabric
{: #peer-client-enroll}

El primer paso consiste en generar los certificados necesarios (inscripciones) utilizando el cliente CA de Fabric. Primero tiene que instalar el cliente de CA de Fabric. Descargue los [binarios fabric-ca v1.1.0 para su plataforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) en la máquina local, extráigalos y muévalos a una carpeta, como por ejemplo `$HOME/fabric-ca-remote/`.

1.  Prepare el entorno para que utilice el cliente CA de Fabric. Vaya al directorio al que ha movido los binarios del cliente para poder hacer referencia a los mismos directamente en sus mandatos.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Establezca la vía de acceso en la que el cliente puede crear las claves. Si es la primera vez que ejecuta el mandato `enroll`, la carpeta `msp` y el archivo `.yaml` no existente. Si ha ejecutado el mandato `enroll` anteriormente, es importante que suprima el material de configuración de intentos anteriores de inscripción con los mandatos `rm` siguientes:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Tiene que recuperar la información de punto final de la entidad emisora de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla "Visión general" del supervisor de red, pulse el botón **Perfil de conexión** y abra el archivo JSON que contiene las credenciales de red. Desplácese hacia abajo hasta la sección `certificateAuthorities` y anote la información siguiente.
    ```
    Nombre de host y puerto para CA: url (sin prefijo https)
    ID de usuario admin: enrollId
    Contraseña de admin: enrollSecret
    Nombre de CA: caName
    ```

4.  También tiene que recuperar un certificado TLS de la plataforma {{site.data.keyword.blockchainfull_notm}} para comunicarse con la CA. Las credenciales de red anteriores incluyen el certificado necesario. En la sección `certificateAuthorities` del archivo de perfil de conexión, copie el certificado que hay tras `"pem:"`, que empieza por `-----BEGIN CERTIFICATE-----` y termina por `-----END CERTIFICATE----- `. **No incluya las comillas**.

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

Para poder trabajar con el igual remoto, tenemos que realizar ciertas tareas de gestión de los certificados en la máquina local y cargar algunos de los certificados que el cliente de CA de Fabric ha generado en la plataforma {{site.data.keyword.blockchainfull_notm}} y en el igual. También tenemos que descargar los certificados TLS de la plataforma y del igual. Si desea obtener más información acerca de los certificados con los que va a trabajar y las tareas que va a realizar, consulte [Gestión de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/certificates.html).

En la máquina local, abra un terminal de mandatos y vaya al directorio al ha movido los binarios Fabric-CA-Client y ha almacenado la carpeta de MSP.
1. Copie el archivo `cert.pem` de la carpeta `signcerts` en una nueva carpeta `admincerts`.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. También tendrá que copiar el certificado TLS del igual remoto del contenedor del igual del ICP en la máquina local.

  - [Inicie una sesión en el contenedor del igual](#remote-peer-kubectl-configure) y ejecute el siguiente mandato:
    ```
    cat /mnt/certs/tls/cacert.pem
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

3. Para otorgar a la CLI la autorización para instalar código de encadenamiento en el igual, debe cargar el signCert generado por el cliente de CA de Fabric en la carpeta de administración de los iguales y reiniciar el igual. Por lo tanto, copie el certificado `admincert/cert.pem` de la máquina local en el directorio `/mnt/crypto/peer/peer/msp/admincerts/` del contenedor del igual como `cert2.pem`.

    - En la máquina local, ejecute los mandatos siguientes:

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copie el texto del archivo `cert.pem` en el portapapeles, desde `-----BEGIN CERTIFICATE-----` hasta `-----END CERTIFICATE-----`.

    - Vuelva al contenedor del igual y ejecute los mandatos siguientes para añadir el archivo cert.pem a los certificados de los iguales. Sustituya el texto `<CERT.PEM>` por el texto del portapapeles.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** ya existe un archivo cert.pem en este directorio. No lo sobrescriba.

    - Como ha añadido un nuevo certificado, tiene que reiniciar el contenedor para que el igual adopte el certificado. Siga estas [instrucciones](#remote-peer-restart) para reiniciar el igual.

4. Debe cargar el mismo certificado `admincert/cert.pem` de la máquina local en la plataforma {{site.data.keyword.blockchainfull_notm}} para que una aplicación o CLI remota pueda realizar operaciones de canal, tales como capturar el bloque de origen del canal y crear una instancia del código de encadenamiento.
    1. En la máquina local, localice `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` y cópielo en el portapapeles.
    2. Entre en el supervisor de su red en la plataforma {{site.data.keyword.blockchainfull_notm}}.
    3. Pulse **Miembros** en el navegador de la izquierda y pulse el separador **Certificados**.
    4. Pulse el botón **Añadir certificado**.
    5. En la ventana **Añadir certificado**, asigne un nombre al certificado, como por ejemplo `fabrictools.pem`, pegue el certificado que acaba de copiar en el portapapeles y pulse **Enviar**.
    6. Se le preguntará si desea reiniciar los iguales. Pulse **Reiniciar**.
    7. Pulse **Canales** en el navegador de la izquierda y localice el canal al que se unirá el igual remoto.
    8. Pulse los tres puntos bajo la cabecera **Acciones** y pulse **Sincronizar certificado**. En la ventana **Sincronizar certificado**, pulse **Enviar**.

    **Nota**: es importante sincronizar el certificado de canal antes de que el igual se una al canal siguiente.

### Configuración del contenedor de herramientas de Fabric

Después de mover todos los certificados a la ubicación necesaria, estamos preparados para instalar y utilizar el contenedor de herramientas de Fabric desde el docker. Una vez más, tenemos que ejecutar estos mandatos desde el directorio donde se ha guardado la carpeta MSP.

1.  Descargue la imagen de docker de las herramientas de Fabric con el mandato siguiente:
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Ejecute los mandatos siguientes para iniciar el contenedor de herramientas de Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    Este mandato monta el directorio MSP dentro del contenedor de herramientas.

3.  La dirección del igual remoto debe resolverse en un nombre de dominio con el sufijo `blockchain.com`. Puede hacerlo a través de su DNS o bien puede modificar su archivo /etc/hosts. En estas instrucciones, modificaremos el archivo `/etc/hosts` en el contenedor de herramientas de Fabric. Inicie una sesión en la consola ICP y busque la [información de punto final](#remote-peer-retrieve-endpoint-info) de los iguales remotos. Una vez que tenga los iguales `INGRESS_IP`, ejecute el mandato siguiente con la dirección de su igual remoto.
    ```
    echo -e "<INGRESS_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}

### Utilización del contenedor de herramientas de Fabric para unir el igual remoto al canal
{: #icp-cli-join-peer-to-channel}

Para poder ejecutar los mandatos de CLI para unir el igual remoto a un canal, la organización se debe añadir a un canal de la red.

  - Puede iniciar un nuevo canal para el igual remoto. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel). Tenga en cuenta que debe tener al menos un igual en la plataforma {{site.data.keyword.blockchainfull_notm}} para poder crear un canal en el supervisor de red.  

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). Un miembro del canal con iguales en la plataforma {{site.data.keyword.blockchainfull_notm}} puede utilizar el supervisor de red para añadir su organización al canal aunque no aloje ningún igual en la plataforma.

    Una vez que la organización se haya añadido a un canal, deberá añadir el certificado de firma del igual al canal para que otros miembros puedan verificar la firma digital durante las transacciones. El igual remoto carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal.

1. Recupere la información de configuración del archivo `creds.json`, disponible en el panel **Visión general** del supervisor de red. Pulse **Perfil de conexión** y luego **Descargar**.

  - Localice el URL solicitado buscando **clasificadores**, que se encuentra bajo `clasificadores > url`. Anote el URL que comienza por el nombre de la red. El URL se parece al del siguiente ejemplo:
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localice el nombre de su organización buscando **organizaciones**. Debería se la misma organización que utiliza para registrar el igual remoto. Encontrará el nombre de la organización junto con su `mspid` asociado. Anote el valor del `mspid`.

2. [Localice la información de punto final de los iguales remotos](#remote-peer-retrieve-endpoint-info), esta vez anotando los `Puertos` que se muestran junto a la `IP Ingress`.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Anote los puertos en el rango de 30K. Estos son los puertos direccionables desde el contenedor de herramientas de Fabric, que se utilizará para definir la variable de entorno `PEERADDR`.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Por lo tanto, el `PEERADDR` que utilizaremos está formado por el valor que hemos especificado en el archivo `/etc/hosts` junto con el puerto vinculado a `7051`, que es `30162` en nuestro ejemplo. El valor resultante de `PEERADDR`
sería `remotepeer.blockchain.com:30162`.

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
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Capte el bloque de origen del canal para crear el libro mayor del canal en el igual. Primero vaya (`cd`) al directorio root y luego ejecute el mandato para captar el bloque de origen.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
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

5. Una el igual al canal con el siguiente mandato:

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Cuando esto funcione, debería ver información parecida a la del siguiente ejemplo:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Utilización del contenedor de herramientas de Fabric para instalar el código de encadenamiento en el igual remoto
{: #icp-toolcontainer-install-cc}
Ahora estamos listos para instalar y crear una instancia de código de encadenamiento en el igual remoto. En estas instrucciones, instalaremos el código de encadenamiento `fabcar` desde el repositorio `fabric-samples`. Descargue el código de encadenamiento `fabric-samples` desde `github.com` con los mandatos siguientes:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Ejecute el siguiente mandato de CLI del igual para instalar el código de encadenamiento `fabcar` en el igual remoto.

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
{: #icp-toolcontainer-instantiate-cc}

Dado que solo un igual tiene que crear una instancia del código de encadenamiento en un canal, no tiene que utilizar su igual remoto para crear una instancia del código de encadenamiento. Los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}} pueden hacerlo. Sin embargo, si desea que el igual remoto cree una instancia del código de encadenamiento en el canal, puede ejecutar el mandato siguiente en el contenedor de herramientas de Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

Cuando este mandato finalice correctamente, verá algo parecido a esto:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Visualización de los registros del igual remoto en {{site.data.keyword.cloud_notm}} privado
{: #remote-peer-log-icp}

Puede acceder a los registros del igual remoto desde la consola de ICP y comprobar los registros en la interfaz Kibana.

1. En la consola de ICP, pulse el icono **Menú** en la esquina superior izquierda.
2. En la lista de menús, pulse **Cargas de trabajo** > **Releases de Helm**.
3. En la tabla Releases de Helm, pulse el nombre de su release de helm.
4. Desplácese hacia abajo hasta la sección **Pod** y pulse el enlace **Ver registros** al final de la fila de la tabla. Los registros del igual remoto se abren en la interfaz Kibana.

## Actualización del código de encadenamiento

Con el tiempo, es probable que tenga que modificar el código de encadenamiento que se ejecuta en el igual remoto. Puesto que el código de encadenamiento se instala en los iguales y se crea una instancia del mismo en el canal, tiene que actualizar el código de encadenamiento en todos los iguales del canal.

Siga los pasos siguientes para actualizar el código de encadenamiento:

1. Para actualizar el código de encadenamiento en cada igual remoto, simplemente vuelva a ejecutar el proceso que ha utilizado para instalar el código de encadenamiento en los iguales, utilizando una aplicación cliente o un mandato de CLI. Asegúrese de especificar el nombre de código de encadenamiento que se ha utilizado originalmente. Sin embargo, esta vez incremente la `Versión` del código de encadenamiento. Todos los iguales necesitan utilizar el mismo nombre y versión de código de encadenamiento.

2. Después de instalar el nuevo código de encadenamiento en todos los iguales del canal, utilice el supervisor de red o el mandato de [actualización de código de encadenamiento del igual ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para utilizar el nuevo código de encadenamiento.

Consulte el paso dos de estas [instrucciones](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) para obtener más información sobre cómo utilizar el panel "Instalar código" del supervisor de red para actualizar el código de encadenamiento en el canal.

## Resolución de problemas
{: #icp-troubleshooting}

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

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

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

5. Check helm version:

  ```
  helm version --tls
  ```

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
falla y genera un mensaje de error similar al ejemplo siguiente:
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solución:**  

Este error se produce en sistemas big endian, como por ejemplo IBM System Z, cuando dos sesiones de bash de docker se abren en el mismo contenedor de docker. Es posible que la segunda sesión de bash no pueda conectarse al contenedor en ejecución. Para resolver este problema, [reinicie el contenedor del igual](#remote-peer-restart) y vuelva a intentar el mandato `kubectl exec` después de que el igual se reinicie.

---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Funcionamiento de un clasificador en {{site.data.keyword.cloud_notm}} privado
{: #orderer-operate}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Después de instalar el clasificador de la plataforma {{site.data.keyword.blockchainfull}} en ICP, se crea un mapa de configuración que contiene los valores predeterminados de las variables de entorno. A continuación, puede cambiar o añadir variables de entorno para el clasificador con el fin de configurar su comportamiento.

Como regla general, los administradores del clasificador se encargan del arranque y el mantenimiento de los clasificadores, pero esto no es necesario en un despliegue SOLO en el que solo existe un clasificador. En un despliegue SOLO, el administrador del clasificador tendrá la responsabilidad de añadir nuevas organizaciones al canal del sistema del clasificador.

## Requisitos previos

Es necesario completar algunos pasos de requisito previo desde el clúster ICP para trabajar con el clasificador.

**Requisitos previos:**
- [Configure las CLI](#orderer-kubectl-configure)
- [Recupere la información de punto final del clasificador](#orderer-endpoint)
- [Descargue el certificado TLS del clasificador](#orderer-tls)

A continuación, utilice las instrucciones de este tema para trabajar con el clasificador. Tenga en cuenta que trabajará con el clasificador a través de la línea de mandatos, que requiere obtener el binario de la CLI de igual (`peer`). El binario de la CLI
`peer` se descarga junto con los demás binarios, como `configtxlator`. Por lo tanto, muchos de los mandatos de este tema comienzan por la palabra "peer". En realidad no utiliza el igual, sino que se trata simplemente del nombre del binario.

### Configuración de las CLI
{: #orderer-kubectl-configure}

Necesitará utilizar la herramienta de línea de mandatos **kubectl** para conectarse al contenedor de clasificador que se ejecuta en ICP.

1. Inicie sesión en la interfaz de usuario del clúster de ICP. Vaya al separador **Herramientas de línea de mandatos** y pulse **CLI de Cloud Private**. Verá las herramientas siguientes, las cuales puede descargar.

   * Instalar la CLI y los plugins de IBM Cloud Private
   * Instalar la CLI de Kubectl
   * Instalar Helm
   * Instalar la CLI de Istio

  Para trabajar con clasificadores, necesitará utilizar las primeras **tres** herramientas, entre las que Helm es opcional. Pulse sobre cada una de ellas y ejecute los mandatos de `curl` para el tipo de máquina que utilice. A continuación, emita los mandatos
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

  Si tiene éxito, el mandato debe devolver una respuesta similar al ejemplo siguiente, donde
`orderer-6cf85f6687-hcxpl` es el nombre del contenedor del clasificador.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Ahora está listo para utilizar la herramienta **kubectl** para obtener la información de punto final de clasificador.

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

### Recuperación de la información de punto final de clasificador
{: #orderer-endpoint}

Debe tener como objetivo el punto final de clasificador para realizar actualizaciones en el canal del sistema del clasificador. Necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para realizar los pasos siguientes:

1. Inicie sesión en la consola de ICP y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas** se incluye un conjunto de mandatos que le ayudarán a trabajar con el despliegue del clasificador.
5. Si no lo ha hecho aún, siga las instrucciones para configurar la [CLI kubeclt](#orderer-kubectl-configure). Deberá utilizarla para interactuar con el contenedor del clasificador.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **1. Obtenga el URL de aplicación ejecutando estos mandatos:** Este mandato imprimirá el URL y el puerto del clasificador, que son similares a los del ejemplo siguiente. Guarde este URL, ya que necesitará utilizarlo en mandatos posteriores para establecer la dirección de proxy y el puerto de nodo externo.

  ```
  http://9.30.94.174:30159
  ```

En este ejemplo, la dirección IP de proxy es `9.30.94.174` y el puerto de nodo externo que corresponde a 7050 es
`30159`.  

**Nota:** si despliega el clasificador detrás de un cortafuegos, necesitará abrir un paso a través para habilitar la red en la plataforma para completar esta tarea.

### Descarga del certificado TLS del clasificador
{: #orderer-tls}

Necesita descargar el certificado TLS del clasificador y pasarlo a los mandatos para comunicarse con el clasificador desde un cliente remoto. Necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para realizar los pasos siguientes:

1. Inicie sesión en la consola de ICP y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas** se incluye un conjunto de mandatos que le ayudarán a trabajar con el despliegue del clasificador.
5. Si no lo ha hecho aún, siga las instrucciones para configurar la [CLI kubeclt](#ca-kubectl-configure). Deberá utilizarla para interactuar con el contenedor del clasificador.
6. En la CLI, ejecute el tercer mandato de la nota, que sigue a **3. Obtenga el archivo de certificado raíz TLS del clasificador**.  Este mandato guardará el certificado TLS como el archivo `cert.pem` en la máquina local.

  **Nota:** antes de ejecutar los mandatos, puede eliminar `app=orderer` tal como se muestra en el mandato siguiente:

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. Mueva el certificado generado a una ubicación en la que pueda hacer referencia a él en mandatos posteriores, y cambie su nombre a
`orderertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### Gestión de los certificados en el sistema local
{: #manage-certs}

Cambie al directorio donde se genera la carpeta de MSP del administrador de clasificador. En función de cómo haya seguido los pasos de ejemplo de esta documentación, o de cuántos componentes vaya a desplegar, puede encontrar la carpeta de MSP en
`$HOME/fabric-ca-client/orderer-admin/msp` o en `$HOME/fabric-ca-client/peer-admin/msp`

Para poder trabajar con el clasificador, necesita realizar algunas gestiones sobre los certificados de la máquina local. También debe asegurarse de que puede acceder a los certificados TLS desde el clasificador. Para obtener más información sobre los certificados a utilizar, consulte
[Proveedores de servicios de pertenencia](/docs/services/blockchain/howto/CA_operate.html#msp) en
[Funcionamiento de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} privado](/docs/services/blockchain/howto/CA_operate.html).

1. Mueva el signCert del administrador de clasificador a una carpeta nueva denominada `admincerts`:

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Asegúrese de [descargar el certificado TLS del clasificador](#orderer-tls) y de que puede hacer referencia a él desde la línea de mandatos. Si ha seguido los mandatos de ejemplo de esta documentación, puede encontrar este certificado TLS en el archivo
`$HOME/fabric-ca-client/orderer-tls/orderertls.pem`.

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
├── orderer-admin
│   └── msp
│       ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│   └── orderertls.pem
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

## Adición de organizaciones al canal del sistema del clasificador
{: #add-organizations-to-consortium}

**Nota:** si tiene pensado desplegar un igual y conectarlo a una red de Plan inicial o Plan empresarial, puede omitir este paso.

Al desplegar un clasificador utilizando el diagrama de Helm de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP, se crea automáticamente el bloque de origen con la organización del clasificador como único administrador del canal del sistema. Esto implica que el administrador de la organización del clasificador tiene la capacidad única de añadir organizaciones al consorcio. La política que rige esto, conocida como `mod_policy` para el canal del sistema del clasificador, no se debe cambiar después de que se añadan nuevas organizaciones al canal del sistema del clasificador.

La adición de organizaciones al canal del sistema del clasificador las convierte en parte del "consorcio", que es la lista de miembros que pueden, de forma predeterminada, crear canales. Ser miembro del consorcio facilita también la adición a un canal mediante el uso de los certificados y la información que aparece en el canal del sistema.

La actualización del canal del sistema del clasificador se realiza a través de "transacciones de actualización de configuración", en las cuales la información de MSP relevante de las organizaciones se añade a la configuración del canal del sistema del clasificador.

La adición de organizaciones al canal del sistema del clasificador es básicamente el mismo flujo que la actualización de la configuración de cualquier canal para añadir una organización. No obstante, necesita realizar algunos cambios, ya que el canal a actualizar no es un canal de aplicación y el administrador apropiado es el administrador del clasificador en lugar de una organización de igual.

Tenga en cuenta que puede añadir una organización a un canal sin unirse al canal del sistema en primer lugar. Para obtener más información, consulte la [Guía de aprendizaje para añadir una organización a un canal](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html) en la documentación de Hyperledger Fabric.

En la lista siguiente se muestran los pasos y tareas generales que realizarán distintos grupos de organizaciones del consorcio.

1. Es necesario que cada organización que se vaya a unir al consorcio [prepare una definición de organización](/docs/services/blockchain/howto/peer_operate_icp.html#organization-definition).
2. El administrador de la organización del clasificador [forma el consorcio](#consortium) añadiendo organizaciones al canal del sistema del clasificador.
3. Cualquier organización del consorcio puede [crear un nuevo canal](/docs/services/blockchain/howto/peer_operate_icp.html#peer-icp-channeltx) mediante la preparación de una transacción de configuración de canal.

## Obtención de las herramientas de Fabric
{: #get-fabric-tools}

Debe descargar las herramientas de Hyperledger Fabric siguientes para actualizar el canal del sistema.
- [peer](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html), que le permitirá recuperar el bloque de origen y actualizar el canal del sistema.
- [configtxlator](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html), que convierte el formato protobuf de una configuración de canal al formato JSON, que se puede leer y actualizar con mayor facilidad.

1. Decida dónde desea almacenar las herramientas y, a continuación, ejecute este mandato:

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. Establezca la vía de acceso a las herramientas, donde se han descargado las herramientas de Fabric anteriores:

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  Por ejemplo, si ha instalado los binarios en el directorio de inicio, debe establecer la variable `PATH` como:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. La descarga de los binarios creará una carpeta de configuración que contiene los archivos core.yaml, orderer.yaml y configtx.yaml. Establezca la vía de acceso de configuración de Fabric en este directorio de configuración:

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  Por ejemplo:
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## Creación de una definición de organización
{: #org-definition}

La **definición** de una organización contiene el nombre de organización (ID de MSP) y los certificados pertinentes. El canal del sistema y los canales de aplicación utilizarán esta definición para incluir su organización en las políticas que controlan la creación, las actualizaciones y la aprobación de la transacción del canal. Cada organización que desee unirse al consorcio deberá completar este paso. Para obtener más información sobre, consulte [Preparación de una definición de organización](/docs/services/blockchain/howto/peer_operate_icp.html#organization-definition).

## Formación del consorcio
{: #consortium}

Recupere el flujo de alto nivel para formar un consorcio:

1. El canal del sistema del clasificador se forma únicamente con la organización del clasificador como miembro del canal del sistema. Dicha organización puede realizar actualizaciones sin necesidad de firmas adicionales.
2. El administrador de la organización del clasificador recibe definiciones de organización de los miembros que desean unirse al consorcio. La organización del clasificador utiliza las definiciones de organización para actualizar la configuración del canal del sistema del clasificador.

### Obtención de las definiciones de organización

El clasificador necesita recibir las [definiciones de organización](/docs/services/blockchain/howto/peer_operate_icp.html#organization-definition) de los miembros que deseen unirse al consorcio. Esto debe realizarse en una operación fuera de banda en la que los demás miembros le envían los archivos JSON que incluyen su ID de MSP y su material criptográfico. Como referencia en los mandatos siguientes, supondremos que ha creado una carpeta denominada
`org-definitions` y que ha colocado todos los archivos pertinentes en dicho directorio.

### Recuperación del bloque de origen del canal del sistema

1. Establezca las variables de entorno siguientes utilizando el nombre de MSP de organización del clasificador que se ha establecido durante el despliegue, la vía de acceso al MSP de clasificador y la vía de acceso al certificado de CA de TLS del clasificador.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  Sustituya los campos por su propia información.

    - Sustituya `<CORE_PEER_LOCALMSPID>` por el ID de MSP de la organización del clasificador. También es visible dentro del contenedor del clasificador ejecutando los mandatos siguientes y sustituyendo `<orderer pod name>` por el valor del pod del clasificador:

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      La salida puede tener un aspecto similar a:

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      Por lo tanto, el `CORE_PEER_LOCALMSPID` es ordererOrg.

    - Sustituya `<CORE_PEER_MSPCONFIGPATH>` por la vía de acceso a la carpeta de MSP de administrador de la organización del clasificador.
    - Sustituya `<CORE_PEER_TLS_ROOTCERT_FILE>` por la vía de acceso al certificado de CA de TLS.
    - Sustituya `<PROXY_IP>` por la dirección IP de proxy de la [información de punto final de clasificador](#orderer-endpoint)
    - Sustituya `<EXTERNAL_NODE_PORT>` por el puerto de nodo externo de la [información de punto final de clasificador](#orderer-endpoint)

  Las variables de entorno pueden tener un aspecto similar al del ejemplo siguiente:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  Puede comprobar estas variables de entorno en cualquier momento emitiendo los mandatos siguientes:

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. Establezca el nombre del canal como una variable de entorno. El nombre del canal del sistema del clasificador siempre será
`test-system-channel-name`.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. Recupere el bloque de origen del canal del sistema. En primer lugar, cree un directorio denominado
`configupdate` dentro de `org-definitions` para almacenar los archivos de configuración generados durante el proceso de actualización de configuración. Puede llamar a este directorio como desee, pero los mandatos del ejemplo siguiente utilizarán `configupdate.`

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  Sustituya `<orderer_TLS_root_cert_file>` por la vía de acceso al archivo `orderertls.pem` que ha creado en este
[paso](#orderer-tls). Por ejemplo, el mandato puede tener un aspecto similar al del ejemplo siguiente:

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Si tiene éxito, este mandato generará la salida siguiente.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  También puede encontrar el bloque de origen (`genesis.pb`) en el sistema de archivos más tarde.

### Creación de la actualización de canal del sistema
{: #system-channel-update}

La [herramienta de Fabric](#get-fabric-tools) `configtxtlator` descargada convierte el formato protobuf de una configuración de canal al formato JSON, y viceversa.

Estos pasos siguen el flujo general de la guía de aprendizaje de actualización de canal acerca de
[convertir el bloque al formato JSON]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down). Deberá realizar algunos cambios en los mandatos de la guía de aprendizaje para reflejar el hecho de que está actualizando el canal del sistema del clasificador en lugar de un canal de aplicación. Puede visitar la guía de aprendizaje para obtener más detalles sobre este proceso. En esta sección simplemente se proporcionan los mandatos.

1. Copie el archivo JSON de la definición de organización de la carpeta donde haya [creado la organización](/docs/services/blockchain/howto/peer_operate_icp.html#organization-definition) a la carpeta `configupdate`. En el mandato de ejemplo que se muestra a continuación, el archivo JSON de la definición de organización es `org1definition.json`:

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. Convierta el bloque de origen al formato JSON. Ejecute los mandatos siguientes:

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. Ejecute el mandato siguiente para añadir el material criptográfico de una organización a la configuración del consorcio. Sustituya
<NEWORGMSP> por el ID de MSP de organización de la [organización que haya creado](/docs/services/blockchain/howto/peer_operate_icp.html#organization-definition).

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  El mandato puede tener un aspecto similar al del ejemplo siguiente:

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. Repita este mandato para cada organización que necesite unir al consorcio. Asegúrese de cambiar el archivo
`./orgdefinition.json` por el archivo JSON de la nueva organización.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. Ejecute los mandatos siguientes para convertir el archivo `modified_config.json` de la actualización de la configuración al formato JSON.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. Tras completar estos mandatos, coloque un sobre en `config_update`.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. Ejecute el mandato siguiente para volver a convertir la actualización de la configuración al formato protobuf:

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### Envío de la actualización al canal del sistema

Antes de realizar estos pasos, asegúrese de que `FABRIC_CFG_PATH`, `$PROXY` y `ORDERER_PORT` se hayan establecido correctamente. Ejecute el mandato siguiente:

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](#orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](#orderer-endpoint)>
```
{:codeblock}

Las variables de entorno debe ser similares a las del ejemplo siguiente:

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

Tras crear la actualización de la configuración de canal, que es
`config_update_in_envelope.json`, la organización del clasificador puede enviar la actualización de canal utilizando el mandato siguiente:

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

Este mandato firma la solicitud de actualización y la envía al clasificador de forma simultánea. Debido a que la organización del clasificador es el único administrador del canal del sistema, solo se necesita una firma para que esta actualización sea una solicitud válida. Si la actualización de canal del sistema se realiza correctamente, verá la salida siguiente:

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

Es posible incluir varias definiciones de organización en una única actualización de configuración de canal del sistema del clasificador, pero se recomienda actualizar el canal con una organización al mismo tiempo y comprobar que la actualización se haya realizado correctamente.

## Visualización de los registros de clasificador
{: #orderer-view-logs}

Los registros de componentes se pueden visualizar desde la línea de mandatos utilizando los
[`mandatos de la CLI kubectl`](#ca-kubectl-configure) o a través de
[Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que se incluye en el clúster de ICP.

- Utilice el mandato `kubectl logs` para ver los registros de contenedor dentro del pod. Si no está seguro de cuál es el nombre del pod, ejecute el mandato siguiente para ver la lista de pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  A continuación, ejecute el mandato siguiente para recuperar los registros del contenedor de clasificador que se encuentra dentro del pod sustituyendo
`<pod_name>` por el nombre del pod en la salida del mandato anterior:

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  Para obtener más información sobre el mandato `kubectl logs`, consulte la
[Documentación de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, puede acceder a los registros utilizando la
[consola de gestión de clúster de ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre los registros en Kibana.

  **Nota:** al visualizar los registros en Kibana, es posible que reciba la respuesta `No results found`. Esta condición se puede producir si ICP utiliza la dirección IP del nodo trabajador como su nombre de host. Para resolver este problema, elimine el filtro que comienza por `node.hostname.keyword` al principio del panel y los registros se volverán visibles.

## Resolución de problemas
{: #orderer-troubleshooting}

### **Problema:** el mandato `peer channel update` falla y devuelve un error.
{: #orderer-peer-channel-update-error}

Es posible que se reciba el error siguiente al ejecutar un mandato `peer channel update`:

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

Este error puede deberse a que el igual utiliza el mismo ID de MSP de organización que el clasificador.  

**Solución:**  

Para resolver este problema, debe volver a desplegar el igual especificando un ID de MSP de organización distinto al que se ha utilizado al desplegar el clasificador.

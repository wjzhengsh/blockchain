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

# Despliegue de iguales remotos en {{site.data.keyword.cloud_notm}} privado
{: #remote-peer-icp}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


En las siguientes instrucciones se describe cómo instalar un igual remoto de la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.cloud_notm}} privado (ICP) y conectarlo a una red en la plataforma {{site.data.keyword.blockchainfull_notm}}.
{:shortdesc}

{{site.data.keyword.cloud_notm}} privado es una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas locales. Los iguales remotos de la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} privado constituyen un producto de {{site.data.keyword.blockchainfull_notm}} que se suministra con ICP para que los clientes desplieguen iguales en su entorno local. Después de instalar el diagrama Helm del igual remoto, lo encontrará como aplicación en el catálogo de ICP. El igual remoto para ICP aprovecha los servicios de almacenamiento, seguridad, registro y soporte de ICP para la gestión de iguales. Para obtener más información sobre ICP, consulte la [{{site.data.keyword.cloud_notm}} privado ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentación de {{site.data.keyword.cloud_notm}} privado").

Antes de instalar iguales remotos en ICP, revise las [consideraciones sobre iguales remotos](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Requisitos previos
{: #prerequisites-icp}

Antes de instalar iguales remotos en ICP, debe llevar a cabo los siguientes pasos en {{site.data.keyword.cloud_notm}} privado, su sistema local y la plataforma {{site.data.keyword.blockchainfull_notm}}.

1. El sistema local

  Tiene que instalar la CLI de {{site.data.keyword.cloud_notm}} privado [2.1.0.3 ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Instalación de la CLI de {{site.data.keyword.cloud_notm}} privado").  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}} privado

  Necesita un clúster de [{{site.data.keyword.cloud_notm}} privado ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentación de {{site.data.keyword.cloud_notm}} privado v2.1.0.3") versión 2.1.0.3. SI utiliza el igual remoto para desarrollo, prueba o experimentación, puede instalar [{{site.data.keyword.cloud_notm}} Private Community Edition ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Instalación de {{site.data.keyword.cloud_notm}}e Private-CE") de forma gratuita.

  **Notas**:
    - Debe determinar el almacenamiento que utilizará el igual. Si utiliza los valores predeterminados, el diagrama Helm crea una nueva reclamación de volumen persistente de 8 Gi con el nombre `my-data-pvc` para los datos del igual. Si selecciona CouchDB como base de datos de libro mayor, el diagrama Helm crea otra reclamación de volumen persistente de 8 Gi llamada `statedb-pvc` para la base de datos de libro mayor.
    - Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* storageClass durante la instalación de ICP; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.
    - El [suministro dinámico ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "suministro de volumen dinámico") solo está disponible para los nodos amd64 en ICP. Por lo tanto, si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.
    - Si no utiliza el suministro dinámico, los [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "volúmenes persistentes") se deben crear y configurar con etiquetas que se puedan utilizar para adaptar el proceso de enlace de reclamaciones de volumen persistente (PVC).

  Además de los requisitos de hardware de ICP, el igual remoto requiere al menos:
    - 2x vCPU
    - 2 GB de RAM
    - 4 GB de espacio para el código de encadenamiento
    - 10 GB de espacio para el libro mayor con capacidad para crecer a medida que se expanda el libro mayor

3. Plataforma {{site.data.keyword.blockchainfull_notm}}  

  Para utilizar un igual remoto, debe tener una organización que sea miembro de una red de blockchain alojada en la plataforma {{site.data.keyword.blockchainfull_notm}}. Es necesario utilizar el supervisor de red en {{site.data.keyword.cloud_notm}} para acceder a las credenciales de red y a los puntos finales de API de la red. Si no es miembro de ninguna red blockchain, tiene que crear o unirse a una red. Para obtener más información, consulte [Creación de una red](/docs/services/blockchain/get_start.html#creating-a-network) o [Cómo unirse a una red](/docs/services/blockchain/get_start.html#joining-a-network).

## Importar el diagrama Helm en ICP

El paquete del igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} está disponible en [Github ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer"). El diagrama Helm se importa en la consola de ICP mediante la línea de mandatos. Desde la máquina local, siga los pasos siguientes para importar el diagrama Helm:

1. Descargue el archivo del diagrama Helm `.tgz` desde [Github ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "diagrama Helm de IBM Blockchain Remote Peer").<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. Si aún no ha iniciado una sesión, inicie una en el clúster de ICP desde la CLI de ICP y luego inicie una sesión en el registro de imágenes privadas de Docker.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  En el código, `<cluster_CA_domain>` es el dominio de la entidad emisora de certificados (CA). Si no ha especificado un dominio de CA, el valor predeterminado será `mycluster.icp`.

3. Desde el directorio en el que ha almacenado el diagrama Helm descargado, ejecute el mandato siguiente en la CLI de ICP para importar el diagrama Helm en el clúster de ICP. Sustituya `<helm_chart_from_github>` por el nombre del archivo `.tgz` que ha descargado de GitHub.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  Cuando este mandato finalice correctamente, verá algo parecido a la información siguiente:

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  El mosaico del diagrama Helm del igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} debería estar visible en la página del catálogo de ICP. Pulse el botón **Catálogo** en la consola de ICP para verificar que la importación se ha realizado correctamente.

### Despliegue el igual remoto

En los pasos siguientes se describe el proceso necesario para desplegar el igual remoto en ICP. Antes de configurar el igual remoto en ICP, recupere información de punto final acerca de la plataforma {{site.data.keyword.blockchainfull_notm}} y cree una identidad de usuario para el igual remoto. Tendrá que utilizar esta información en pasos posteriores.

## Paso uno: recupere la información de configuración del igual remoto
{: #network-endpoints}

Debe proporcionar los puntos finales de API de la red a su igual remoto durante la configuración. Estos puntos finales permiten que un igual remoto encuentre y se conecte a la red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla **Visión general** de su supervisor de red, pulse el botón **Configuración de igual remoto**.

![Configuración de igual remoto](../images/myresources_starter.png "Configuración de igual remoto")
*Figura 1. Configuración de igual remoto*

Se abre una ventana emergente que muestra los valores de los campos siguientes. Guarde estos valores para cuando configure el igual remoto.

  - **ID de red**
  - **MSP de la organización**
  - **Nombre de la entidad emisora de certificados (CA)**
  - **URL de la entidad emisora de certificados (CA)**
  - **Certificado TLS de la entidad emisora de certificados (CA)**

Puede copiar y pegar los campos individualmente o guardarlos como un archivo JSON pulsando el enlace **Descargar**. **Tenga en cuenta** que si descarga la información en JSON, tiene que convertir el certificado TLS en formato PEM antes de proporcionarlo al igual remoto. Puede utilizar la CLI para convertir el certificado al formato PEM con el mandato `echo -e "<CERT>" > catls.pem`.

## Paso: registre un igual remoto
{: #register-peer}

Tiene que añadir una nueva identidad de igual a la organización en la plataforma {{site.data.keyword.blockchainfull_notm}} para que el igual remoto se pueda unir a la red. Siga los pasos siguientes para inscribir y registrar un igual remoto.

1. Inicie una sesión en la plataforma {{site.data.keyword.blockchainfull_notm}} y acceda a su supervisor de red. En la pantalla "Entidad emisora de certificados", puede ver todas las identidades que se han registrado con su organización, como las aplicaciones de administrador o cliente.  ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
  *Figura 2. Pantalla CA*

2. Pulse el botón **Añadir usuario** de la parte superior de la pantalla. Se abre una pantalla emergente que le permite registrar el igual remoto después de rellenar los campos siguientes. Guarde los valores de ID y secreto; tendrá que utilizarlos cuando configure el igual.
  - **ID:** el nombre de uso del igual, al que se hace referencia como `ID de inscripción` cuando se configura el igual. **Guarde este valor** para utilizarlo en el futuro cuando despliegue el igual remoto.
  - **Secreto:** la contraseña del igual, a la que se hace referencia como `secreto de inscripción` cuando se configura el igual. **Guarde este valor** para utilizarlo en el futuro cuando despliegue el igual remoto.
  - **Tipo:** seleccione `igual` para este campo.
  - **Afiliación:** es la afiliación bajo su organización, por ejemplo `org1`, a la que pertenecerá el igual remoto. Seleccione una afiliación existente de la lista desplegable o escriba una nueva.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si no se especifica, el valor predeterminado es un número ilimitado de inscripciones.

  Una vez que haya completado los campos, pulse **Enviar** para registrar el igual remoto. A continuación, el igual registrado aparece en la tabla como una identidad de la organización.

## Paso tres: configure e instale un igual remoto

Después de registrar la identidad del igual en el supervisor de red, puede configurar e instalar el igual remoto en ICP siguiendo las instrucciones de la consola ICP que se muestran a continuación.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Inicie una sesión en la consola ICP y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Localice el diagrama Helm correspondiente al igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} y pulse para abrirlo. Verá un archivo readme que incluye información sobre la instalación y configuración del diagrama Helm.
3. Pulse **Configurar**. En la página de configuración, complete los valores de configuración para el diagrama Helm y confirme que ha leído y acepta el acuerdo de licencia.
4. Pulse **Instalar**.

### Parámetros de configuración
{: #icp-configuration-parms}

Tiene que proporcionar la información que necesita el igual remoto para conectarse a la red en la plataforma {{site.data.keyword.blockchainfull_notm}}.
En la tabla siguiente se muestran los parámetros configurables del diagrama del igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} y sus valores predeterminados.


|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
|**Configuración** | **Configure el nombre de release y el espacio de nombres de destino del igual remoto** |  ||
| `Nombre de release`| El nombre del release de helm. | ninguno | sí |
| `Espacio de nombres de destino`| El espacio de nombres de Kubernetes. | predeterminado | sí |
| | | | |
|**Configuración del clúster** |**Información de configuración del clúster** | ||
| `Arquitectura del nodo trabajador`| Seleccione la arquitectura de la plataforma de nube (amd64 o S390x).| Arquitectura autodetectada basada en el nodo maestro | sí |
| `Imagen`| Vía de acceso al diagrama Helm. | Se rellena automáticamente con la vía de acceso instalada, no cambie este valor | sí |
| | | | |
|**Red de blockchain** | **La información de configuración de red que necesita el igual remoto**| | |
| `ID de red`| Valor del ID de red que se encuentra en el supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Pulse el botón `Configuración de igual remoto` en la pantalla "Visión general" y luego copie y pegue aquí dicha información.| ninguno | sí |
| `MSP de la organización`| Valor del MSP de la organización que se encuentra en el supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Pulse el botón `Configuración de igual remoto` en la pantalla "Visión general" y luego copie y pegue aquí dicha información.|ninguno|sí|
| `Nombre de la entidad emisora de certificados (CA)`| Valor del nombre de CA que se encuentra en el supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Pulse el botón `Configuración de igual remoto` en la pantalla "Visión general" y luego copie y pegue aquí dicha información.|ninguno|sí|
| `URL de la entidad emisora de certificados (CA)`| Valor del URL de CA que se encuentra en el supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Pulse el botón `Configuración de igual remoto` en la pantalla "Visión general" y luego copie y pegue aquí dicha información. | ninguno | sí |
| `Certificado TLS de la entidad emisora de certificados (CA)` | La serie de certificado TLS de CA del supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Pulse el botón `Configuración de igual remoto` en la pantalla "Visión general" y luego copie y pegue aquí dicha información. | ninguno | sí |
| | | | |
|**Identidad de igual remoto** | **El id y el secreto de inscripción que se utilizan para registrar el igual remoto**| | |
| `ID de inscripción del igual`| Es el ID de inscripción que ha especificado en la pantalla "Entidad emisora de certificados" del supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}. | ninguno | sí |
| `Secreto de inscripción del igual`| Es el secreto de inscripción que ha especificado en la pantalla "Entidad emisora de certificados" del supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}}.| ninguno | sí |
| | | | |
|**Base de datos de igual remoto** | **Tipo de base de datos de libro mayor**| | |
| `Base de datos de libro mayor`| La [base de datos de estado](/docs/services/blockchain/glossary.html#state-database) utilizada para almacenar el libro mayor del canal. El igual remoto tiene que utilizar la misma base de datos que la [red blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | ninguno | sí |
| `Imagen de CouchDB`| Solo se aplica si se ha seleccionado CouchDB como base de datos de libro mayor. | Se rellena automáticamente con la vía de acceso instalada, no cambie este valor. | sí |
|**Persistencia de datos** | Habilita la posibilidad de guardar permanentemente los datos después de que el clúster se reinicie o falle. Consulte [almacenamiento en Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/ "Volúmenes") para obtener más información. | | |
| `Persistencia de datos habilitada`| Los datos de estado estarán disponibles cuando se reinicie el contenedor. *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.* | marcada | no |
| `Utilizar suministro dinámico`| Utilizar almacenamiento dinámico de Kubernetes. | marcada | no |
| | | | |
|**Configuración de volumen persistente** | **Reclamación de volumen persistente que utilizará el igual** |  |  |
| `Reclamación de volumen persistente`| Especifique un nombre para la nueva reclamación de volumen persistente (PVC) que se creará. | my-data-pvc | no |
| `Nombre de clase de almacenamiento`| Elija el nombre de la clase de almacenamiento. | En blanco si se crea una nueva PVC; de lo contrario, especifique la clase de almacenamiento que está asociada con la PVC existente. | no |
| `Reclamación de volumen existente`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | nuevo nombre de reclamación | no |
| `Modalidad de acceso al almacenamiento`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC.  | ReadOnlyMany| no |
| `Etiqueta de selector`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC.| predeterminado | no |
| `Valor de selector`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | predeterminado | no |
| `Tamaño de reclamación de volumen`| Tamaño de la reclamación de volumen, que debe ser mayor que 2 Gi. | 8 Gi  | sí |
| | | | |
|**Persistencia de datos de base de datos de estado** | **Datos persistentes después de que el contenedor se reinicie o falle. Consulte [almacenamiento en Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/ "Volúmenes") para obtener más información** | | |
| `Persistencia de datos habilitada`| Los datos del libro mayor estarán disponibles cuando se reinicie el contenedor. *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.*| marcada | no |
| `Utilizar suministro dinámico`| Utilizar almacenamiento dinámico de Kubernetes.| marcada | no |
| | | | |
|**Configuración de volumen persistente de base de datos de estado** |**Reclamación de volumen persistente que se utilizará para la base de datos de estado. Consulte [almacenamiento en Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/ "Volúmenes") para obtener más información.** |||
| `Reclamación de volumen persistente`| Especifique un nombre o utilice el valor predeterminado para la nueva reclamación de volumen persistente de CouchDB que se creará. | statedb-pvc | no |
| `Nombre de clase de almacenamiento de base de datos de estado`|Elija el nombre de la clase de almacenamiento.| ninguno | no |
| `Reclamación de volumen existente de base de datos de estado`| Para utilizar una reclamación de volumen existente, especifique el nombre de pvc y deje en blanco todos los demás campos. | ninguno | no |
| `Modalidad de acceso al almacenamiento de base de datos de estado`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC. | ReadOnlyMany| no |
| `Etiqueta de selector`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC.| predeterminado | no |
| `Valor de selector`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC.| predeterminado | no |
| `Tamaño de reclamación de volumen de base de datos de estado`| Tamaño de la reclamación de volumen persistente. | 8 Gi | no |
| | | | |
| **Recursos del igual** | **Memoria y CPU mín. y máx. para el contenedor del igual** | | |
| `Solicitud de CPU del igual` | Número mínimo de CPU que se asignarán al igual. | 2 | sí |
| `Límite de CPU del igual` | Número máximo de CPU que se asignarán al igual.| 2 | sí |
| `Solicitud de memoria del igual` | Cantidad mínima de memoria que se asignará al igual. | 4 Gi | sí |
| `Límite de memoria del igual` | Cantidad máxima de memoria que se asignará al igual. | 4 Gi | sí |
| | | | |
|**Recursos CouchDB** | **Memoria y CPU mín. y máx. que se asignan al contenedor CouchDB. Esta sección sólo se aplica si se ha seleccionado CouchDB como base de datos de estado**| | | |
| `Solicitud de CPU de CouchDB` | Número mínimo de CPU que se asignarán a CouchDB.| 2 | sí |
| `Límite de CPU de CouchDB` | Número máximo de CPU que se asignarán a CouchDB. | 2 | sí |
| `Solicitud de memoria de CouchDB` | Cantidad mínima de memoria que se asignará a CouchDB.| 4 Gi | sí |
| `Límite de memoria de CouchDB` | Cantidad máxima de memoria que se asignará a CouchDB. | 4 Gi | sí |

Cuando se selecciona CouchDB como base de datos de igual remota, se crean dos contenedores en el pod, uno para el igual y otro para CouchDB.
El contenedor del igual incluye un montaje de un solo volumen en el PVC del igual que almacena los bloques y las transacciones en el sistema de archivos. El contenedor de CouchDB monta la PVC de la base de datos de estado del igual que contiene los datos del libro mayor.

Cuando se selecciona LevelDB como base de datos del igual remoto, se crea un único contenedor en el pod para ejecutar tanto los procesos de igual como los de LevelDB. Este contenedor tiene dos montajes de volumen, uno para la PVC del igual y el segundo montaje de volumen es para la PVC de base de datos de estado del igual que contiene los datos del libro mayor.

| Selección de base de datos de igual remoto  | Contenido del contenedor 1  | Contenido del contenedor 2 |
| --------------|-----------------|---------------|
| CouchDB | Igual remoto que monta la PVC del igual| CouchDB que monta la PVC de base de datos de estado |
| LevelDB | Igual remoto y LevelDB que montan la PVC del igual y la PVC de la base de datos de estado | n/d |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](/docs/services/blockchain/howto/remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  Este mandato genera el archivo `peer-cert.pem`, que es el certificado TLS del igual.

--->

## Paso cuatro: verifique la instalación del igual remoto
{: #verify-installation-icp}

Después de instalar el igual remoto, puede ver el registro del igual para comprobar si la instalación del igual se ha realizado correctamente.

1. Después de hacer pulsar **Instalar**, pulse el botón **Ver release de Helm** en la ventana emergente. Desplácese hacia abajo hasta la sección **Pods**. Cuando el estado de pods muestre **En ejecución**, puede encontrar la instancia del igual remoto en la sección **Despliegues**.

2. En la consola de ICP, pulse el icono **Menú** en la esquina superior izquierda. En la lista de menús, pulse **Cargas de trabajo** > **Despliegues**. En la tabla de despliegues, pulse la instancia del igual remoto que ha creado y se abrirá la pantalla de visión general del despliegue. Pulse el separador **Registros** para ver los registros del igual remoto. Puede especificar la serie `Iniciado` en el campo de búsqueda.

  -  Si el igual remoto se ha iniciado correctamente, podrá ver registros similares a los del ejemplo siguiente:
```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - Si no ve ningún registro en la pantalla, significa que el igual remoto no se ha iniciado correctamente. Para ver más registros para resolver el problema, pulse el icono **Menú** en la esquina superior izquierda y luego pulse **Cargas de trabajo** > **Releases de Helm**. Pulse el release del helm para abrirlo. Pulse el enlace **Ver registros** que hay junto al **Pod** asociado para ver los registros de diagrama adicionales en la interfaz Kibana. 

3. Para comprobar que el igual remoto se puede conectar a la red de la plataforma {{site.data.keyword.blockchainfull}}, puede ejecutar el mandato de la CLI peer desde dentro del contenedor del igual remoto. Ejecute el mandato de la CLI `peer channel fetch` para capturar el bloque de origen de un canal:

   1. Si aún no ha conectado con el clúster ICP, siga estas [instrucciones](/docs/services/blockchain/howto/remote_peer_operate_icp.html#remote-peer-kubectl-configure) para conectar con el mismo y utilizar la cli desde dentro del contenedor del igual.

   2. Es necesario añadir la organización a un canal de la red para poder capturar el bloque de origen.

     - Puede iniciar un nuevo canal para el igual remoto. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel). Tenga en cuenta que debe tener al menos un igual en la plataforma {{site.data.keyword.blockchainfull_notm}} para poder crear un canal en el supervisor de red.   

     - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel). Un miembro del canal con iguales en la plataforma {{site.data.keyword.blockchainfull_notm}} puede utilizar el supervisor de red para añadir su organización al canal aunque no aloje ningún igual en la plataforma.

      El igual remoto carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal. 

   3. Recupere información de configuración del archivo `creds.json` disponible en la pantalla "Visión general" de su supervisor de red. Pulse **Perfil de conexión** y luego **Descargar**.

     - Localice el URL solicitado buscando **clasificadores**, que se encuentra bajo `clasificadores > url`. Anote el URL que comienza por el nombre de la red. El URL se parece al del siguiente ejemplo:

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Localice el nombre de su organización buscando **organizaciones**. Debería se la misma organización que utiliza para registrar el igual remoto. Encontrará el nombre de la organización junto con su `mspid` asociado. Anote el valor del `mspid`.

    4. Ejecute los siguientes mandatos para definir las variables de entorno en el contenedor del igual.

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
         - Sustituya `<PEER_ADDR>` por `localhost:7051`

       Por ejemplo:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Ejecute el siguiente mandato peer de la CLI para capturar el bloque de origen del canal.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Nota:** es posible que cuando ejecute alguno de estos mandatos de la CLI reciba el siguiente aviso, que puede pasar por alto sin problema.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     Compruebe que el mandato ha funcionado bien y que el bloque de origen se ha añadido al contenedor del igual ejecutando el siguiente mandato:

     ```
     ls *.block
     ```
     {:codeblock}

     Sabrá que el bloque de origen se ha añadido correctamente cuando vea algo parecido a lo del siguiente ejemplo:

     ```
     defaultchannel_0.block
     ```

## Qué hacer a continuación
{: #whats-next-icp}

Después de configurar el igual remoto en ICP, puede completar varios pasos operativos antes para enviar transacciones y leer el libro mayor distribuido de la red blockchain. Para obtener más información, consulte [Funcionamiento de un igual remoto](/docs/services/blockchain/howto/remote_peer_operate_icp.html).

## Alta disponibilidad
{: #high-availability}

Como práctica recomendada de alta disponibilidad, se recomienda desplegar un mínimo de dos iguales por organización.

Siga estos pasos para desplegar iguales remotos de alta disponibilidad en ICP:

1. revise cómo [configurar el clúster ICP para alta disponibilidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "clústeres privados de {{site.data.keyword.cloud_notm}} de alta disponibilidad").

2. Instale el igual varias veces en el clúster para desplegar iguales en nodos trabajadores diferentes. El diagrama Helm contiene una política antiafinidad que se basa en el ID de red y el ID de organización. El clúster de ICP detecta si sus iguales se conectan a la misma red desde la misma organización e intenta organizar a esos iguales en distintos nodos trabajadores.  
**Nota:**  aunque la política "prefiere" colocar los iguales en distintos nodos trabajadores, no lo impone. Por ejemplo, si solo tiene un nodo trabajador, todos los iguales se despliegan en este mismo nodo.

Si despliega al menos dos iguales, también tiene que configurar sus [aplicaciones para alta disponibilidad](/docs/services/blockchain/v10_application.html#ha-app).

## Consideraciones sobre seguridad
{: #icp-security}

Los iguales remotos se despliegan fuera de la plataforma {{site.data.keyword.blockchainfull}}; por lo tanto, es responsable de gestionar la seguridad del igual remoto. Esto incluye áreas importantes de seguridad que proporcionan las redes de plan de empresa, como la gestión de claves y el cifrado de datos. Examine los temas siguientes cuando tenga en cuenta la seguridad para los iguales remotos.

### Control de identidades y de accesos
{: #icp-security-node}

Fuera de {{site.data.keyword.cloud_notm}}, es necesario configurar la gestión de identidades, la autenticación y el control de accesos para los usuarios que interactúan con los iguales remotos. Puede utilizar la [gestión de usuarios de ICP ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Seguridad") para configurar estas funciones.

### Seguridad de los datos
{: #icp-security-data}

El plan de empresa de la plataforma {{site.data.keyword.blockchainfull}} utiliza el cifrado de disco completo que se basa en el [cifrado de claves simétricas ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cifrado simétrico") para proteger todos los datos que utilizan las redes. Debe seguir pasos similares en su propio entorno para proteger los datos de los iguales remotos.

### Residencia de los datos
{: #icp-data-residency}

La residencia de los datos impone que el proceso y el almacenamiento de todos los datos del libro mayor de blockchain permanezcan dentro de los límites de un solo país.
Para obtener más información sobre cómo se puede conseguir la residencia de los datos, consulte [Residencia de los datos](/docs/services/blockchain/howto/remote_peer.html#data-residency).

### Gestión de claves
{: #icp-security-key-management}

La gestión de claves es un aspecto crítico de la seguridad del igual remoto. Si una clave privada se ve comprometida o se pierde, es posible que usuarios hostiles accedan a los datos y a las funciones del igual remoto. El plan de empresa de la plataforma {{site.data.keyword.blockchainfull_notm}} utiliza [Módulos de seguridad de hardware](/docs/services/blockchain/glossary.html#hsm) (HSM) para almacenar las claves privadas de la red. HSM es un dispositivo físico que evita que otro accedan a su clave privada. 

Cuando despliega un igual remoto en ICP, es el responsable de gestionar las claves privadas. Aunque la plataforma {{site.data.keyword.blockchainfull_notm}} genera sus claves privadas, dichas claves no se almacenan en la plataforma. Resulta esencial asegurarse de que las claves se almacenan
en una ubicación segura para que no se vean comprometidas. Encontrará la clave privada de su igual remoto en la carpeta de almacén de claves de MSP del igual, en el directorio `/mnt/crypto/peer/peer/msp/keystore/` dentro del contenedor del igual. Para obtener más información sobre los certificados del igual remoto, consulte la sección sobre [Proveedor de servicios de pertenencia](/docs/services/blockchain/certificates.html#msp) de la guía de aprendizaje sobre [Gestión de certificados de la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/certificates.html).

Puede utilizar Key Escrow para recuperar claves privadas perdidas. Esto hay que hacerlo antes de perder ninguna clave. Si una clave privada no se puede recuperar, tiene que obtener nuevas claves privadas registrando una nueva identidad con la entidad emisora de certificados. También debe eliminar y sustituir signCert de cualquier canal al que se haya unido.

<!---
En IBP, cuando se crea una clave privadas, se generan dos conjuntos de materiales de claves independientes, en custodia de dos entidades distintas. Estos dos conjuntos de materiales de claves se combinan para crear la clave privada.
--->

### TLS
{: #icp-security-tls}

[La seguridad de capa de transporte ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una visión general del reconocimiento SSL o TLS") (TLS) está integrada en el modelo de confianza de Hyperledger Fabric. Todos los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizan TLS para autenticarse y comunicarse entre sí. Por lo tanto, los componentes de red de la plataforma {{site.data.keyword.blockchainfull_notm}} deben ser capaces de completar un reconocimiento de TLS con sus iguales remotos. Una implicación de este enfoque es que necesitas activar el paso a través, usando por ejemplo una lista blanca, en el cortafuego de web entre las apps cliente a su igual.

Puede utilizar TLS mutuo, que requiere dos sentidos (servidor-cliente) en lugar de una autenticación de un solo sentido (servidor), para proteger la comunicación entre la aplicación y las redes del plan de empresa. Puede utilizar el supervisor de red [para habilitar el TLS mutuo](/docs/services/blockchain/v10_dashboard.html#mutual-tls) para los iguales de la plataforma {{site.data.keyword.blockchainfull_notm}}. Para habilitar TLS mutuo en el igual remoto, siga las instrucciones para [habilitar el TLS mutuo para los nodos iguales ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Protección de la comunicación con seguridad de capa de transporte") de la documentación de Hyperledger Fabric. Se recomienda encarecidamente habilitar el TLS mutuo para las aplicaciones.

### Configuración del proveedor de servicios de pertenencia
{: #icp-security-MSP}

Los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} consumen identidades a través de proveedores de servicios de pertenencia (MSP). Los MSP asocian los certificados que emiten las entidades emisoras de certificados con roles de red y de canal. Para obtener más información sobre cómo funcionan los MSP con el igual remoto, consulte [Proveedores de servicios de pertenencia (MSP)](/docs/services/blockchain/certificates.html#msp).

### Seguridad de las aplicaciones
{: #icp-security-appl}

Puesto que todas las invocaciones de código de encadenamiento están firmadas, Fabric gestiona la seguridad de las aplicaciones. Además, Fabric también incluye comprobaciones de nivel de aplicación basadas en ACL.

## Licencias y precios
{: #license-pricing-icp}

El igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} privado es una edición Beta gratuita apta para evaluación y experimentación, y se despliega como una aplicación ICP. Puede acceder al [paquete Github ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} igual remoto de la plataforma")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->y descargar el [diagrama Helm ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} diagrama Helm de igual remoto de la plataforma").

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->

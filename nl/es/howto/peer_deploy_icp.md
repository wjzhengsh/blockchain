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

# Despliegue de iguales en {{site.data.keyword.cloud_notm}} privado
{: #peer-icp}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

En las instrucciones siguientes se describe cómo desplegar un igual de la plataforma
{{site.data.keyword.blockchainfull}} en {{site.data.keyword.cloud_notm}} privado (ICP). Estas instrucciones le permiten conectarse a una plataforma {{site.data.keyword.blockchainfull_notm}} en ICP. Si desea conectar un igual a una red de Plan inicial o Plan empresarial en
{{site.data.keyword.cloud_notm}}, consulte [Despliegue de iguales para conectarse al Plan inicial o el Plan empresarial](peer_deploy_ibp.html).
{:shortdesc}

Antes de desplegar un igual, revise las [Consideraciones y limitaciones](../ibp-for-icp-about.html#ibp-icp-considerations).

## Recursos necesarios
{: #peer-resources-required}

Asegúrese de que el sistema ICP cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| Igual | 2 | 2 GB | 50 GB con posibilidad de ampliación |
| CouchDB para igual | 2| 2 GB |50 GB con posibilidad de ampliación |

 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una
máquina virtual o a un núcleo de procesador físico si el servidor no está particionado
para máquinas virtuales. Necesita tener en cuenta los requisitos de vCPU al decidir el núcleo de procesador virtual (VPC) para su despliegue en ICP. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; 250 GB para el igual como ejemplo. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el igual o clasificador, debe desplegar un nuevo igual o clasificador con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

## Almacenamiento
{: #storage}

Debe determinar el almacenamiento que utilizará el igual. Si utiliza los valores predeterminados, el diagrama de Helm creará una nueva reclamación de volumen persistente (PVC) de 8 Gi con el nombre `my-data-pvc` para los datos del igual, y otra PVC de 8 Gi con el nombre
`statedb-pvc` para la base de datos de estado.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de ICP; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.

Puede optar por desplegar el igual en las plataformas amd64 o s390x. No obstante, debe tener en cuenta que el
[suministro dinámico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) solo está disponible para los nodos amd64 en ICP. Si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, deberán crearse [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y configurarse con etiquetas que se puedan utilizar para refinar el proceso de enlace de PVC de Kubernetes.

## Requisitos previos para desplegar un igual
{: #prerequisites-peer-icp}

1. Para poder instalar un igual en ICP, primero debe [instalar ICP](../ICP_setup.html) e
[instalar el diagrama de Helm de la plataforma {{site.data.keyword.blockchainfull_notm}}](helm_install_icp.html).

2. Si utiliza Community Edition y desea ejecutar este diagrama de Helm en un clúster de ICP sin conexión a Internet, debe crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de ICP. Para obtener más información, consulte
[Adición de aplicaciones destacadas a clústeres sin conexión a Internet
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adición de aplicaciones destacadas a clústeres sin conexión a Internet"){:new_window}. Tenga en cuenta que puede encontrar el archivo de especificación manifest.yaml en ibm-blockchain-platform-dev/ibm_cloud_pak en el diagrama de Helm.

3. En primer lugar, debe [desplegar una CA](CA_deploy_icp.html) en ICP. Debe utilizar la CA para crear un
[archivo de configuración de igual y almacenarlo como secreto de Kubernetes en ICP](#peer-config-file).

4. Recupere el valor de la dirección IP de proxy de clúster de la CA desde la consola de ICP. **Nota:** necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para acceder a la IP de proxy. Inicie sesión en el clúster de ICP. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla. **Importante:** guarde este valor, ya que lo utilizará cuando configure el campo `Proxy IP` del diagrama de Helm.


## Creación del secreto de configuración de igual
{: #peer-config-file}

Para desplegar un igual, debe crear un archivo de configuración que contenga información importante sobre la identidad del igual y su entidad emisora de certificados. A continuación, debe pasar este archivo al diagrama de Helm durante la configuración utilizando un objeto de [secreto de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Este archivo permitirá que el igual pueda obtener los certificados que necesita de la entidad emisora de certificados para unirse a una red blockchain. También contiene un certificado de administrador que le permitirá trabajar con el igual. Siga las instrucciones para
[utilizar la CA para desplegar un clasificador o igual](CA_operate.html#deploy-orderer-peer) antes de la configuración del igual.

Debe proporcionar los nombres de host de CSR en el archivo de configuración. Esto incluye el `nombre de host de servicio`, que tendrá el mismo valor que el `nombre de release de Helm` que especifique durante el despliegue. Por ejemplo, si especifica un
`nombre de release de Helm` de `org1peer1`, recuerde insertar el valor siguiente en la sección
`"csr"` del archivo:
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

Tras guardar el archivo de configuración, deberá codificarlo en el formato base64 antes de proporcionárselo a ICP como objeto de secreto. Un secreto de Kubernetes le permite proteger y compartir información sin tener que pasarla directamente al despliegue.

1. Para codificar el archivo de configuración en el formato base64, ejecute los mandatos siguientes desde una ventana de terminal:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <config_file> | base64 $FLAG
```
{:codeblock}

**Nota:** es importante que la serie que genera el mandato anterior tenga un formato de una sola línea. Debe tener un aspecto similar a:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```

Pero no similar a:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

Guarde la salida resultante para el Paso 4.

2. Inicie sesión en la consola de ICP. En el panel de navegación de la izquierda, pulse
**Configuración** y, a continuación, pulse **Secretos**. Pulse el botón **Crear secreto** para abrir un panel emergente que le permite generar un nuevo objeto de secreto.

3. En el separador **General**, complete los campos siguientes:
  - **Nombre:** proporcione un nombre exclusivo dentro del clúster para el secreto. Utilizará este nombre cuando despliegue el igual. El nombre debe estar en minúsculas.  
  **Nota:** al desplegar un igual, el despliegue genera automáticamente un nuevo secreto con el nombre `<helm_release_name>-secret`. Por lo tanto, cuando establezca el nombre del secreto, asegúrese de que sea distinto a `<helm_release_name>-secret`. De lo contrario, el despliegue del diagrama de Helm fallará debido a que el secreto que intenta crear ya existe.
  - **Espacio de nombres:** el espacio de nombres para añadir el secreto. Seleccione el `espacio de nombres` en el que desee desplegar el igual.
  - **Tipo:** especifique el valor `Opaque`.

4. Pulse el separador **Datos** del panel de navegación de la izquierda de esta ventana.
  - En los campos de `Nombre`, especifique `secret.json` y, en el campo de valor, pegue la serie codificada en
`base64` del archivo de configuración.

5. (Opcional) Si tiene pensado utilizar `CouchDB` como base de datos de estado, debe añadir dos valores adicionales a este objeto de secreto. En el separador **Datos**, pulse el botón **Añadir datos** para añadir el ID de usuario y la
contraseña de CouchDB que se utilizará para la base de datos cuando se despliegue el contenedor.
  1. Cree el nombre de usuario y la contraseña y codifique los valores en el formato base64. Ejecute los mandatos siguientes en una ventana de terminal y sustituya `admin` y `adminpw` por los valores que desee utilizar.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. En el campo **Nombre**, especifique el valor `couchdbuser`. En el campo **Valor** correspondiente, especifique el resultado de `echo -n 'couch' | base64 $FLAG` del paso uno anterior.
  3. Pulse el botón **Añadir datos** para añadir un segundo par de clave-valor.
  4. En el segundo campo de **Nombre**, especifique el valor `couchdbpwd`. En el campo **Valor** correspondiente, especifique el resultado de `echo -n 'couchpw' | base64 $FLAG` del paso uno anterior.

6. Pulse **Crear** para guardar su objeto de secreto.

**Nota:** el secreto de configuración de igual no se eliminará del clúster de ICP cuando suprima el release de Helm. Si suprime el igual, deberá suprimir este secreto también.

## Configuración
{: #peer-configuration}

Después de crear el objeto de secreto de configuración de igual, puede configurar el igual en ICP con los pasos siguientes. Únicamente puede instalar un igual al mismo tiempo.


1. Inicie una sesión en la consola ICP y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico etiquetado como
`ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` si ha descargado Community Edition. Pulse sobre el mosaico para abrirlo y podrá ver un archivo Readme que incluye información sobre la instalación y configuración del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Especifique los valores para los [parámetros de configuración generales](#peer-global-parameters) y acepte el acuerdo de licencia.
5. Abra el triángulo `Todos los parámetros` y especifique el valor de los [parámetros de configuración global](#peer-global-parameters).
6. Desplácese hacia abajo hasta la sección **Configuración de igual**. Marque el recuadro de selección `Instalar igual` y rellene los [valores de configuración](#peer-parameters) del igual.
7. Pulse **Instalar**.

### Parámetros de configuración
{: #icp-peer-configuration-parms}

En la tabla siguiente se muestran los parámetros configurables de la plataforma {{site.data.keyword.blockchainfull_notm}}, **específicos del componente de igual**, y sus valores predeterminados. Si está experimentando con el igual o es la primera vez que lo utiliza, use los valores predeterminados de los parámetros de base de datos y de almacenamiento. Desplácese hasta la sección del componente de igual para marcar el recuadro de selección `Instalar igual` y proporcionar la información de configuración asociada para dicho componente únicamente. **Aunque la interfaz de usuario del diagrama de Helm indica que no se necesita ninguna configuración adicional, debe especificar determinados parámetros para desplegar un igual.**

#### Parámetros de configuración generales y globales
{: #peer-global-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Nombre de release de Helm`| Nombre del release de Helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez que intente instalar un componente. **Importante:** Este valor debe coincidir con el valor que ha utilizado para generar el 'nombre de host de servicio' para el campo "hosts" del [archivo de secreto JSON.](#peer-config-file) | ninguno | sí |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
|**Configuración global**| Parámetros que se aplican a todos los componentes del diagrama de Helm|||
| `Nombre de cuenta de servicio`| Especifique el nombre de la
[cuenta de servicio
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que va a utilizar para ejecutar el pod. | predeterminado | no |

#### Parámetros de configuración de igual
{: #peer-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
|**Configuración del clúster** |**Información de configuración del clúster** | ||
| `Instalar igual` | Selecciónelo para instalar un igual|sin marcar | sí, si desea desplegar un igual |
| `Arquitectura del nodo trabajador de igual`| Seleccione la arquitectura de la plataforma de nube (AMD64 o S390x)| AMD64 | sí |
| `Repositorio de imágenes de igual`| Ubicación del diagrama de Helm de igual. Este campo se rellena automáticamente con la vía de acceso instalada. | ibmcom/ibp-fabric-peer | sí |
| `Etiqueta de imagen de Docker de igual`|Valor de la etiqueta asociada con la imagen de igual. |1.2.1, se rellena automáticamente con el valor correcto.|sí|
| `Configuración de igual`| Puede personalizar la configuración del igual pegando su propio archivo de configuración
`core.yaml` en este campo. Para ver un archivo `core.yaml` de ejemplo, consulte la
[configuración de ejemplo de `core.yaml`
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) Solo para usuarios avanzados. | ninguno | no |
| `Secreto de configuración de igual (obligatorio)`|Nombre del [Secreto de configuración de igual](#peer-config-secret) que ha creado en ICP. | ninguno | sí |
|`MSP de organización (obligatorio)`| Puede crear un nuevo valor de MSPID de organización como 'org1', o especificar un MSP de una organización existente de la que formará parte el igual. Si ha desplegado una organización de clasificador, asegúrese de que los MSPID de los iguales son distintos al MSPID del clasificador. Además, tome nota de este valor, ya que lo necesitará para
`CORE_PEER_LOCALMSPID` y el archivo `configtx.yaml` más adelante. | ninguno | sí |
|`Tipo de servicio de igual`| Se usa para especificar si los
[puertos externos se deben exponer
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release. | NodePort | sí |
| `Base de datos de estado`| La [base de datos de estado](../glossary.html#state-database) utilizada para almacenar el libro mayor del canal. El igual tiene que utilizar la misma base de datos que la [red blockchain](../v10_dashboard.html#network-preferences). | ninguno | sí |
|`Repositorio de imágenes de CouchDB`| Solo es aplicable si se ha seleccionado CouchDB como base de datos de libro mayor. Este campo se rellena automáticamente con la vía de acceso instalada. Si utiliza Community Edition y no tiene acceso a Internet, debe coincidir con el directorio donde ha descargado la imagen de CouchDB de Fabric.| ibmcom/ibp-fabric-couchdb | sí |
| `Etiqueta de imagen de Docker de CouchDB`| Solo es aplicable si se ha seleccionado CouchDB como base de datos de libro mayor. Valor de la etiqueta asociada con la imagen de CouchDB. | Se rellena automáticamente con el valor correcto. | sí |
| `Persistencia de datos de igual habilitada`| Habilita la posibilidad de guardar permanentemente los datos después de que el clúster se reinicie o falle. Consulte [almacenamiento en Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/ "Volúmenes") para obtener más información. *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.* | marcada | no |
| `Utilizar suministro dinámico de igual`| Marcar para habilitar el suministro dinámico para volúmenes de almacenamiento. | marcada | no |
| `Reclamación de volumen persistente de igual`| Solo para una reclamación nueva. Especifique un nombre para la nueva reclamación de volumen persistente (PVC) que se va a crear. | my-data-pvc | no |
| `Nombre de clase de almacenamiento de igual`| Especifique un nombre de clase de almacenamiento para el igual. | En blanco si se crea una nueva PVC; de lo contrario, especifique la clase de almacenamiento que está asociada con la PVC existente. | no |
| `Reclamación de volumen existente de igual`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de igual`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC.| ninguno | no |
| `Valor de selector de igual`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Modalidad de acceso de almacenamiento de igual`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC.  | ReadWriteMany| no |
| `Tamaño de reclamación de volumen de igual`| Tamaño de la reclamación de volumen, que debe ser mayor que 2 Gi. | 8 Gi  | sí |
| `Reclamación de volumen persistente de base de datos de estado`| Solo para una reclamación nueva. Especifique un nombre para la nueva reclamación de volumen persistente (PVC) que se va a crear. | statedb-pvc | no |
| `Nombre de clase de almacenamiento de base de datos de estado`| Especifique un nombre de clase de almacenamiento para la base de datos de estado. | ninguno | no |
| `Reclamación de volumen existente de base de datos de estado`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de base de datos de estado`| [Etiqueta de selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC| ninguno | no |
| `Valor de selector de base de datos de estado`| [Valor de selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC | ninguno | no |
| `Modalidad de acceso al almacenamiento de base de datos de estado`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC. | ReadWriteMany| no |
| `Tamaño de reclamación de volumen de base de datos de estado`| Elija el tamaño de disco a utilizar. | 8 Gi | sí |
| `CouchDB - Persistencia de datos habilitada`| Para el contenedor de CouchDB, los datos del libro mayor estarán disponibles cuando se reinicie el contenedor. *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.*| marcada | no |
| `CouchDB - Utilizar suministro dinámico`| Para el contenedor de CouchDB, utilizar el almacenamiento dinámico de Kubernetes.| marcada | no |
| `Solicitud de CPU del igual` | Número mínimo de CPU que se asignarán al igual. | 1 | sí |
| `Límite de CPU del igual` | Número máximo de CPU que se asignarán al igual.| 2 | sí |
| `Solicitud de memoria del igual` | Cantidad mínima de memoria que se asignará al igual. | 1Gi | sí |
| `Límite de memoria del igual` | Cantidad máxima de memoria que se asignará al igual. | 4 Gi | sí |
| `Solicitud de CPU de CouchDB` | Número mínimo de CPU que se asignarán a CouchDB.| 1 | sí |
| `Límite de CPU de CouchDB` | Número máximo de CPU que se asignarán a CouchDB. | 2 | sí |
| `Solicitud de memoria de CouchDB` | Cantidad mínima de memoria que se asignará a CouchDB.| 1Gi | sí |
| `Límite de memoria de CouchDB` | Cantidad máxima de memoria que se asignará a CouchDB. | 4 Gi | sí |


Cuando se selecciona CouchDB como base de datos de igual, se crean dos contenedores en el pod, uno para el igual y otro para CouchDB.
El contenedor del igual incluye un montaje de un solo volumen en el PVC del igual que almacena los bloques y las transacciones en el sistema de archivos. El contenedor de CouchDB monta la PVC de la base de datos de estado del igual que contiene los datos del libro mayor.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Utilización de la línea de mandatos de Helm para instalar el release de Helm
{: #icp-helm-cli}

Como alternativa, puede utilizar la CLI de `helm` para instalar el release de Helm. Antes de ejecutar el mandato `helm install`, asegúrese de
[añadir el repositorio de Helm del clúster al entorno de CLI de Helm
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adición del repositorio interno de Helm a la CLI de Helm").

Puede establecer los parámetros necesarios para la instalación creando un archivo
`yaml` y pasándolo al mandato `helm install` siguiente.
```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

Donde:
- `<helm_release name>` representa el nombre que desea que tenga el release de Helm.
- `<helm_chart>` representa el nombre del diagrama de Helm importado en el catálogo.
- `<helm_chart_version>` representa la versión del diagrama de Helm importado en el catálogo.
- `<customvalues.yaml>` es el nombre del archivo yaml que contiene los parámetros de configuración.

Por ejemplo:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```

Puede crear un nuevo archivo `yaml` editando el archivo `values.yaml` incluido en el archivo de archivado descargado, que incluye todos los parámetros necesarios con sus valores predeterminados.

## Verificar la instalación del igual
{: #verify-installation-icp}

Tras completar los parámetros de configuración y pulsar el botón **Instalar**, pulse el botón **Ver release de Helm** para ver el despliegue. Si se ha realizado correctamente, debe aparecer el valor 1 en los campos
`DESIRED`, `CURRENT`, `UP TO DATE` y `AVAILABLE` de la tabla Despliegue. Es posible que tenga que pulsar Renovar y esperar a que se actualice la tabla. También puede encontrar la tabla Despliegue pulsando el icono de
**Menú** de la esquina superior izquierda de la consola de ICP. En la lista de menús, pulse
**Cargas de trabajo** y, a continuación, **Releases de Helm**.

Si se desplaza hacia abajo hasta la sección `Notas`, hay información importante que utilizará al
[trabajar con el igual](peer_operate_icp.html).

## Visualización de los registros de igual
{: #peer-deploy-view-logs}

Los registros de igual se pueden visualizar utilizando los [mandatos de la CLI kubectl](peer_operate_icp.html#peer-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"). Para obtener más información, consulte estas
[instrucciones para acceder a los registros](peer_operate_icp.html#peer-icp-view-logs).

## Qué hacer a continuación

Después de haber desplegado el igual, deberá realizar varios pasos operativos para poder enviar transacciones y leer el libro mayor distribuido de la red blockchain. Para obtener más información, consulte [Funcionamiento de iguales con una red multinube](peer_operate_icp.html).

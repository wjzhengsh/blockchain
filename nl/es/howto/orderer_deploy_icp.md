---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Despliegue de un clasificador en {{site.data.keyword.cloud_notm}} privado
{: #orderer-deploy}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Los clasificadores autentican clientes, ordenan transacciones y difunden transacciones en una red blockchain con el componente de clasificador. Para obtener más información sobre los clasificadores y qué papel desempeñan en una red blockchain, consulte [Visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html).
{:shortdesc}

Antes de desplegar un servicio de ordenación, revise las [Consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

## Recursos necesarios
{: #ca-resources-required}

Asegúrese de que el sistema ICP cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| Clasificador | 2 | 512 MB | 100 GB con posibilidad de ampliación |


 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una máquina virtual o a un núcleo de procesador físico si el servidor no está particionado para máquinas virtuales. Necesita tener en cuenta los requisitos de vCPU al decidir el núcleo de procesador virtual (VPC) para su despliegue en ICP. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte [Núcleo de procesador virtual (VPC) ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; 500 GB para el servicio de ordenación como ejemplo. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el clasificador, debe desplegar un nuevo clasificador con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

## Almacenamiento
{: #storage}

Debe determinar el almacenamiento que utilizará el clasificador. Si utiliza los valores predeterminados, el diagrama de Helm creará una nueva reclamación de volumen persistente (PVC) de 8 Gi con el nombre `my-data-pvc` para los datos del clasificador, y otra PVC de 8 Gi con el nombre `statedb-pvc` para la base de datos de estado.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de ICP; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.

Puede optar por desplegar el clasificador en las plataformas amd64 o s390x. No obstante, debe tener en cuenta que el [suministro dinámico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) solo está disponible para los nodos amd64 en ICP. Si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, deberán crearse [volúmenes persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y configurarse con etiquetas que se puedan utilizar para refinar el proceso de enlace de PVC de Kubernetes.


## Requisitos previos para desplegar un clasificador
{: #prerequisites-orderer-icp}

1. Para poder instalar un clasificador en ICP, primero debe [instalar ICP](/docs/services/blockchain/ICP_setup.html) e [instalar el diagrama de Helm de la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/howto/helm_install_icp.html).

2. Si utiliza Community Edition y desea ejecutar este diagrama de Helm en un clúster de ICP sin conexión a Internet, debe crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de ICP. Para obtener más información, consulte [Adición de aplicaciones destacadas a clústeres sin conexión a Internet ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adición de aplicaciones destacadas a clústeres sin conexión a Internet"){:new_window}. Tenga en cuenta que puede encontrar el archivo de especificación `manifest.yaml` en ibm-blockchain-platform-dev/ibm_cloud_pak en el diagrama de Helm.

3. Recupere el valor de la dirección IP de proxy de clúster de la CA desde la consola de ICP. **Nota:** necesitará ser un [administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para acceder a la IP de proxy. Inicie sesión en el clúster de ICP. En el panel de navegación de la izquierda, pulse **Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla. **Importante:** guarde este valor, ya que lo utilizará cuando configure el campo `Proxy IP` del diagrama de Helm.

4. Cree un [archivo de configuración de clasificador y almacénelo como secreto de Kubernetes en ICP](#orderer-config-file).

## Creación de un archivo de configuración de clasificador
{: #orderer-config-file}

Para desplegar un clasificador, primero debe crear un archivo de configuración que contiene información importante sobre la identidad del clasificador y su CA. A continuación, debe pasar este archivo al diagrama de Helm durante la configuración utilizando un objeto de [secreto de Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Este archivo permitirá que el clasificador pueda obtener los certificados que necesita de la CA para unirse a una red blockchain. También contiene un certificado de administrador que le permitirá trabajar con el clasificador como usuario administrador. Siga las instrucciones para [utilizar la CA para desplegar un clasificador o igual](/docs/services/blockchain/howto/CA_operate.html#deploy-orderer-peer) antes de la configuración del clasificador.

Debe proporcionar los nombres de host de CSR en el archivo de configuración. Esto incluye el `nombre de host de servicio`, que estará basado en el `nombre de release de Helm` que especifique durante el despliegue. El `nombre de host de servicio` es el `helm_release_name` que especifique con la serie `-orderer` añadida al final. Por ejemplo, si especifica un `nombre de release de Helm` de `orderer1`, puede insertar el valor siguiente en la sección `"csr"` del archivo:

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

Tras guardar el archivo de configuración, deberá codificarlo en el formato base64 antes de proporcionárselo a ICP como objeto de secreto. Un secreto de Kubernetes le permite proteger y compartir información sin tener que pasarla directamente al despliegue.

1. Codifique el archivo de configuración en el formato base64 ejecutando los mandatos siguientes desde una ventana de terminal:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** es importante que la serie generada al utilizar el mandato anterior tenga un formato de una sola línea. Debe tener un aspecto similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   y no similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Guarde la salida resultante para el Paso 4.

2. Inicie sesión en la consola de ICP. En el panel de navegación de la izquierda, pulse **Configuración** y, a continuación, pulse **Secretos**. Pulse el botón **Crear secreto** para abrir un panel emergente que le permite generar un nuevo objeto de secreto.

3. En el separador **General**, complete los campos siguientes:
  - **Nombre:** proporcione un nombre exclusivo dentro del clúster para el secreto. Utilizará este nombre cuando despliegue el clasificador. El nombre debe estar en minúsculas.  
  **Nota:** al desplegar un clasificador, el despliegue genera automáticamente un nuevo secreto con el nombre `<helm_release_name>-orderer-mspsecret`. Por lo tanto, cuando establezca el nombre del secreto, asegúrese de que sea distinto a `<helm_release_name>-orderer-mspsecret`. De lo contrario, el despliegue del diagrama de Helm fallará debido a que el secreto que intenta crear ya existe.
  - **Espacio de nombres:** el espacio de nombres para añadir el secreto. Seleccione el `espacio de nombres` en el que desee desplegar el clasificador.
  - **Tipo:** especifique el valor `Opaque`.

4. Pulse el separador **Datos** del panel de navegación de la izquierda de esta ventana. En el campo de `Nombre`, especifique `secret.json` y, en el campo de valor, pegue la serie codificada en `base64` del archivo de configuración.

5. Pulse **Crear** para guardar su objeto de secreto.

**Nota:** el secreto de configuración de clasificador no se eliminará del clúster de ICP cuando suprima el release de Helm. Si suprime el clasificador, deberá suprimir este secreto también.

## Configuración
{: #orderer-configuration}

Después de crear el objeto de secreto de configuración de clasificador, puede configurar el clasificador en ICP con los pasos siguientes. Únicamente puede instalar un clasificador al mismo tiempo.

1. Inicie una sesión en la consola ICP y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico etiquetado como `ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` si ha descargado Community Edition. Pulse sobre el mosaico para abrirlo y podrá ver un archivo Readme que incluye información sobre la instalación y configuración del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Especifique los valores para los [parámetros de configuración generales](#orderer-global-parameters) y acepte el acuerdo de licencia.
5. Abra el triángulo `Todos los parámetros` y especifique el valor de los [parámetros de configuración global](#orderer-global-parameters).
6. Desplácese hacia abajo hasta la sección **Configuración de clasificador**. Marque el recuadro de selección `Instalar clasificador` y rellene los [valores de configuración](#orderer-parameters) del clasificador.
7. Pulse **Instalar**.


**Nota:** al desplegar el clasificador en s390x, el contenedor de init podría devolver el error siguiente:

`panic: Error while trying to open DB: no locks available`

Esto se debe a que el clasificador utiliza una base de datos de archivos planos y el sistema de archivos NFS necesita un paquete adicional para permitir que el servicio de ordenación pueda consultar archivos para comprobar si existen bloqueos exclusivos en ellos, así como para crear bloqueos exclusivos. Para arreglar el problema, debe habilitar el paquete `rpc-statd`.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

Esto se puede hacer en el sistema desde el que se sirve el sistema de archivos NFS.

### Parámetros de configuración
{: #icp-orderer-configuration-parms}

En la tabla siguiente se muestran los parámetros configurables de la plataforma {{site.data.keyword.blockchainfull_notm}}, **específicos del componente de clasificador**, y sus valores predeterminados. **Aunque la interfaz de usuario del diagrama de Helm indica que no se necesita ninguna configuración adicional, debe especificar determinados parámetros para desplegar un clasificador.**

#### Parámetros de configuración generales y globales
{: #orderer-global-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Nombre de release de Helm`| El nombre del release de helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez que intente instalar un componente. **Importante:** Este valor debe coincidir con el valor que ha utilizado para generar el 'nombre de host de servicio' para el campo "hosts" del [archivo de secreto JSON.](#orderer-config-file) | ninguno | sí  |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
|**Configuración global**| Parámetros que se aplican a todos los componentes del diagrama de Helm|||
| `Nombre de cuenta de servicio`| Especifique el nombre de la [cuenta de servicio ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que va a utilizar para ejecutar el pod. | predeterminado | no |

#### Parámetros de configuración de clasificador
{: #orderer-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Instalar clasificador`| Selecciónelo para instalar un clasificador. | sin marcar | sí, si desea desplegar un clasificador |
| `Arquitectura del nodo trabajador de clasificador`| Seleccione su arquitectura de nodo trabajador de ICP (AMD64 o S390X). | Arquitectura autodetectada basada en el nodo maestro | sí |
| `Configuración de clasificador`| Puede personalizar la configuración del clasificador pegando su propio archivo de configuración `orderer.yaml` en este campo. Para ver un archivo `orderer.yaml` de ejemplo, consulte la [configuración de ejemplo de `orderer.yaml` ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/orderer.yaml) **Solo para usuarios avanzados**. | ninguno | no |
| `Secreto de MSP de organización (obligatorio)`| Especifique el nombre del objeto de secreto que contiene las claves y los certificados de MSP de organización. | ninguno | sí |
| `Persistencia de datos de clasificador habilitada` | Los datos estarán disponibles cuando se reinicie el contenedor. Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod. | marcada | no |
| `Utilizar suministro dinámico de clasificador` | Marcar para habilitar el suministro dinámico para volúmenes de almacenamiento. | marcada | no |
| `Repositorio de imágenes de clasificador` | Ubicación del diagrama de Helm de clasificador. Este campo se rellena automáticamente con la vía de acceso instalada. Si utiliza Community Edition y no tiene acceso a Internet, cambie este campo a la ubicación donde haya descargado la imagen de clasificador de Fabric. | ibmcom/ibp-fabric-orderer | no |
| `Etiqueta de imagen de Docker de clasificador`| Un registro de la imagen de Docker. Este campo se rellena automáticamente con la versión de la imagen. No lo modifique.| 1.2.1 | sí |
| `Tipo de consenso de clasificador`| El tipo de consenso del servicio de ordenación. | SOLO | sí |
| `Nombre de organización de clasificador`| Especifique el nombre que desee utilizar para la organización del clasificador. Si también tiene pensado desplegar iguales, asegúrese de utilizar un nombre distinto al que vaya a proporcionar para los iguales. Por ejemplo, proporcione a la organización de clasificador un nombre como `ordererOrg` | ninguno | sí |
| `ID de MSP de organización de clasificador`| Especifique el nombre que desee utilizar para el ID de MSP de la organización del clasificador. Debe ser el mismo nombre que proporcione a la organización del clasificador, y se establecerá como una variable de entorno en el proceso de despliegue. Tome nota de este valor, ya que necesitará hacer referencia a él más adelante. | ninguno | sí |
| `Nombre de clase de almacenamiento de clasificador`| Especifique un nombre de clase de almacenamiento para el clasificador. | ninguno | Depende de cómo esté configurado el clúster de ICP. Consulte al administrador del clúster |
| `Reclamación de volumen existente de clasificador`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de clasificador`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC. | ninguno | no |
| `Valor de selector de clasificador`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC. | ninguno | no |
| `Modalidad de acceso de almacenamiento de clasificador`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) al almacenamiento para la PVC. | ReadWriteMany | sí |
| `Tamaño de reclamación de volumen de clasificador`| Elija el tamaño de disco a utilizar, que debe ser al menos de 2 Gi. | 8 Gi | sí |
| Tipo de servicio de clasificador | Se usa para especificar si los [puertos externos se deben exponer ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release | NodePort | sí |
| `Solicitud de CPU de clasificador`| Especifique el número mínimo de CPU a asignar al clasificador. | 1 | sí |
| `Límite de CPU de clasificador`| Especifique el número máximo de CPU a asignar al clasificador. | 2 | sí |
| `Solicitud de memoria de clasificador`| Especifique la cantidad mínima de memoria a asignar al clasificador. | 1Gi | sí |
| `Límite de memoria de clasificador`| Especifique la cantidad máxima de memoria a asignar al clasificador. | 2Gi | sí |

### Utilización de la línea de mandatos de Helm para instalar el release de Helm
{: #icp-helm-cli}

Como alternativa, puede utilizar la CLI de Helm para instalar el release de Helm. Antes de ejecutar el mandato `helm install`, asegúrese de [añadir el repositorio de Helm del clúster al entorno de CLI de Helm ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adición del repositorio interno de Helm a la CLI de Helm").

Puede establecer los parámetros necesarios para la instalación creando un archivo `yaml` y pasándolo al mandato `helm install` siguiente.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

donde:

- `<helm_release name>` representa el nombre que desea que tenga el release de Helm.
- `<helm_chart>` representa el nombre del diagrama de Helm importado en el catálogo.
- `<helm_chart_version>` representa la versión del diagrama de Helm importado en el catálogo.
- `<customvalues.yaml>` es el nombre del archivo yaml que contiene los parámetros de configuración.

Por ejemplo:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values orderer-s390x-values.yaml \
--tls
```

Puede crear un nuevo archivo `yaml` editando el archivo `values.yaml` incluido en el archivo de archivado descargado, que incluye todos los parámetros necesarios con sus valores predeterminados.

## Verificación de la instalación del clasificador

Tras completar los parámetros de configuración y pulsar el botón **Instalar**, pulse el botón **Ver release de Helm** para ver el despliegue. Si se ha realizado correctamente, debe aparecer el valor 1 en los campos `DESIRED`, `CURRENT`, `UP TO DATE` y `AVAILABLE` de la tabla Despliegue. Es posible que tenga que pulsar Renovar y esperar a que se actualice la tabla. También puede encontrar la tabla Despliegue pulsando el icono de **Menú** de la esquina superior izquierda de la consola de ICP. En la lista de menús, pulse **Cargas de trabajo** y, a continuación, **Releases de Helm**.

## Visualización de los registros de clasificador
{: #orderer-view-logs}

Los registros de componentes se pueden visualizar desde la línea de mandatos utilizando los [`mandatos de la CLI kubectl`](/docs/services/blockchain/howto/orderer_operate.html#orderer-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que se incluye en el clúster de ICP. Para obtener más información, consulte estas [instrucciones para acceder a los registros](/docs/services/blockchain/howto/orderer_operate.html#orderer-view-logs).

---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: IBM Blockchain Platform, Helm chart file, local ICP cluster, IBM Cloud Private, upgrade Helm chart

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

# Instalación de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private
{: #helm-install}

{{site.data.keyword.blockchainfull}} Platform para {{site.data.keyword.cloud_notm}} Private se entrega como un archivo de diagrama de Helm que se puede instalar en un clúster de {{site.data.keyword.cloud_notm}} Private local. Después de instalar el diagrama de Helm, encontrará {{site.data.keyword.blockchainfull_notm}} Platform como una plataforma en el catálogo de {{site.data.keyword.cloud_notm}} Private.

Antes de instalar {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private, revise las
[Consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). Para obtener más información sobre cómo desplegar los componentes de blockchain que se incluyen en el diagrama de Helm, los precios y el soporte, consulte
[Acerca de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private ofrece dos ediciones:

- {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private se proporciona a través de Passport Advantage (PPA). Debe tener la licencia necesaria para acceder a
[Passport Advantage Online ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Al realizar la compra, se incluye soporte técnico para la plataforma {{site.data.keyword.blockchainfull_notm}}.

- La edición Community Edition de {{site.data.keyword.blockchainfull_notm}} Platform es una oferta gratuita para exploración, desarrollo y pruebas. Es posible acceder a esta versión gratuita a través de
[GitHub ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Tenga en cuenta que
{{site.data.keyword.IBM_notm}} no proporciona soporte para Community Edition.

## Requisitos previos para la instalación del diagrama de Helm
{: #helm-install-prereqs}

Antes de instalar el diagrama de Helm, debe haber configurado un clúster de {{site.data.keyword.cloud_notm}} Private y haber creado un nuevo espacio de nombres de destino enlazado a una política de seguridad de pod. Consulte las instrucciones para
[configurar un clúster de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Instalación de {{site.data.keyword.blockchainfull_notm}} Platform detrás de un cortafuegos
{: #helm-install-prereqs-firewall}

Puede desplegar componentes de {{site.data.keyword.blockchainfull_notm}} Platform detrás de un cortafuegos sin conectividad de
Internet. El paquete PPA incluye todas las imágenes de Docker de componentes de Fabric que utilizará la plataforma
{{site.data.keyword.blockchainfull_notm}}, por lo que no necesita descargarlas de DockerHub durante el despliegue.

No obstante, el diagrama de Helm de Community Edition no incluye las imágenes de Docker de componentes de Fabric necesarias, ya que esta edición está configurada para descargar dichas imágenes de DockerHub durante el despliegue. El despliegue fallará si no hay conexión a Internet. Por lo tanto, debe realizar pasos adicionales para crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de {{site.data.keyword.cloud_notm}} Private. Las imágenes siguientes son necesarias:
- [Igual de Fabric
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Igual de Fabric")
- [CA de Fabric
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "CA de Fabric")
- [Clasificador de Fabric
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Clasificador de Fabric")
- [Base de datos de Couch de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Base de datos de Couch de Fabric")
- Imagen [`Init` utilizada para arrancar y configurar componentes, incluyendo las carpetas de MSP ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Imagen Docker-in-Docker (DinD) utilizada por los iguales para ejecutar contenedores de código de encadenamiento ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Para obtener más información sobre cómo utilizar estas imágenes, consulte
[Adición de aplicaciones destacadas a clústeres sin conexión a Internet ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html). Tenga en cuenta que puede encontrar el archivo de especificación `manifest.yaml` en el directorio
`ibm-blockchain-platform-dev/ibm_cloud_pak` del diagrama de Helm.

## Importación del diagrama de Helm en {{site.data.keyword.cloud_notm}} Private
{: #helm-install-importing}

1. Descargue el archivo del diagrama de Helm en {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private desde [Passport Advantage Online ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") o para la edición gratuita Community desde [GitHub ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts").  Este paquete de diagramas de Helm contiene tres subdiagramas de Helm para la CA, el clasificador y el igual.

2. Si aún no lo ha hecho, inicie sesión en el clúster de {{site.data.keyword.cloud_notm}} Private.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Asegúrese de configurar la [CLI de Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html). Tras configurar la CLI de Docker, acceda al registro de imágenes del clúster utilizando el mandato siguiente:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Busque el nombre del repositorio en {{site.data.keyword.cloud_notm}} Private para cargar el diagrama de Helm utilizando el mandato siguiente:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Cuando este mandato finalice correctamente, podrá ver una lista de repositorios del clúster. Elija el nombre del repositorio de destino y guárdelo. Necesitará utilizarlo en el mandato siguiente.

5. Importe el diagrama de Helm utilizando la línea de mandatos.
  El mandato que ejecute para importar el diagrama de Helm dependerá de si el diagrama de Helm se ha descargado de Passport Advantage (PPA) o de GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private**

    Siga estas instrucciones si ha descargado el diagrama de Helm de PPA.
    Desde el directorio en el que ha almacenado el diagrama de Helm descargado de PPA, ejecute el mandato siguiente en la CLI de {{site.data.keyword.cloud_notm}} Private para importar el diagrama de Helm en el clúster de {{site.data.keyword.cloud_notm}} Private.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Sustituya los valores siguientes:
    - `<archive-name>` por el nombre del archivo `.tgz` descargado.
    - `<cluster_CA_domain>:8500` por el dominio que utilice para iniciar sesión en el clúster de
{{site.data.keyword.cloud_notm}} Private.
    - `<repo-name>` por el repositorio de Helm donde desee cargar el diagrama. Ejecute 'cloudctl catalog repos' para ver una lista de los repositorios.

    Cuando este mandato finalice correctamente, verá algo parecido a la información siguiente:

    ```  
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
  Synch started
  OK
    ```  
    </details>

  - **Community Edition descargada desde GitHub**
    Siga estas instrucciones si ha descargado el diagrama de Helm de GitHub.

    Desde el directorio en el que ha almacenado el diagrama de Helm descargado de GitHub, ejecute el mandato siguiente en la CLI de {{site.data.keyword.cloud_notm}} Private para importar el diagrama de Helm en el clúster de {{site.data.keyword.cloud_notm}} Private. Para importar el diagrama de Helm descargado de GitHub, ejecute el mandato siguiente:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Sustituya los valores siguientes:
    - `<helm_chart_from_github>` por el nombre del archivo .tgz que ha descargado.
    - `<repo-name>` por el repositorio de Helm donde desee cargar el diagrama. Ejecute 'cloudctl catalog repos' para ver una lista de los repositorios.

    Cuando este mandato finalice correctamente, verá algo parecido a la información siguiente:
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
  Synch started
  OK
    ```

Pulse el botón **Catálogo** de la consola de {{site.data.keyword.cloud_notm}} Private y, a continuación, pulse **Blockchain** en el panel de navegación de la izquierda para verificar que la importación se ha realizado correctamente. Si es así, el mosaico **ibm-blockchain-platform-prod** o el mosaico **ibm-blockchain-platform-dev** debe estar visible en la página Catálogo de {{site.data.keyword.cloud_notm}} Private.

## Despliegue de componentes individuales
{: #helm-install-deploying-components}

Después de instalar el diagrama de Helm, pulse el mosaico **ibm-blockchain-platform-prod** o **ibm-blockchain-platform-dev** en el catálogo de {{site.data.keyword.cloud_notm}} Private para abrirlo. Puede utilizar la página de configuración para desplegar cualquiera de los componentes individuales de la red blockchain. Para obtener más detalles sobre los componentes necesarios para la solución de blockchain y sobre el orden en el que deben desplegarse, consulte [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

A continuación, despliegue los componentes individuales:

- Si va a desplegar un clasificador, en primer lugar debe configurar una entidad emisora de certificados para el clasificador. La CA generará certificados que utilizarán los demás componentes de la organización. Para obtener más información, consulte [Despliegue de una entidad emisora de certificados de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). A continuación, puede desplegar el clasificador, que será el enlace común de la red. Para obtener más información, consulte [Despliegue de un clasificador de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)

- Si va a desplegar un igual, en primer lugar debe configurar una entidad emisora de certificados para el igual. La CA generará los certificados que utilizará el igual. Para obtener más información, consulte [Despliegue de una entidad emisora de certificados de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). A continuación, cuando esté listo para unirse a una red, puede desplegar los iguales que se unirán a canales, aprobarán transacciones y almacenarán los datos. Para obtener más información, consulte [Despliegue de un igual de {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) o [Despliegue de un igual de {{site.data.keyword.blockchainfull_notm}} para una red de Plan inicial o Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy), dependiendo de la red blockchain a la que se vaya a unir el igual.

## Actualización del diagrama de Helm en {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading}

Si utiliza {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private v1.0.1, puede actualizar el diagrama de Helm a {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private v1.0.2 siguiendo las instrucciones de
[Actualización de productos empaquetados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "Actualización de productos empaquetados") de la documentación de {{site.data.keyword.cloud_notm}} Private. Además del mandato `helm upgrade` de las instrucciones, también puede
[utilizar la consola de {{site.data.keyword.cloud_notm}} Private para actualizar el release de Helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui).

Debe tener el rol de **administrador de clúster** o **administrador de equipo** en
{{site.data.keyword.cloud_notm}} Private para poder actualizar un diagrama de Helm.
{:note}

No hay soporte para la retrotracción de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private v1.0.2 a v1.0.1.

### Actualización del release de Helm desde la consola de {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading-ui}

Al actualizar el release de Helm de un componente desde la consola de
{{site.data.keyword.cloud_notm}} Private, debe marcar `Reutilizar valores` y no cambiar ningún parámetro.
{:important}  

Realice los pasos siguientes para actualizar el release de Helm desde la consola de
{{site.data.keyword.cloud_notm}} Private:
1. En la barra de menús, pulse **Cargas de trabajo** > **Releases de Helm**.
2. Seleccione el release de Helm que desee actualizar.
3. Compruebe el número de versión en `Versión disponible`, en la sección **Detalles y actualizaciones**.
4. Pulse **ReadMe** bajo el número de versión para revisar las notas del release para ver si hay cambios importantes.
5. Pulse en **Actualizar** y, a continuación, seleccione el repositorio correcto y la versión adecuada en la lista desplegable.
6. Asegúrese de que `Reutilizar valores` esté seleccionado.
7. Pulse **Actualizar**.

Espere a que aparezca el mensaje de confirmación y la actualización del estado del release junto al nombre del release en la esquina superior izquierda de la consola.

### Realización de pasos adicionales para actualizar un igual
{: #helm-install-upgrading-peer}

Tras actualizar un igual, existen algunos pasos adicionales que debe realizar para completar el proceso de actualización. En la página del release de Helm del igual, puede ver dos pods de igual en la sección **Pod**:
- El nuevo pod de igual con el estado `CrashLoopBackOff`
- El pod de igual original con el estado `Running`

Anote los nombres de igual asociados, ya que deberá utilizarlos en los pasos posteriores.
{:tip}

Realice los pasos siguientes desde la CLI para completar el proceso de actualización del igual:
1. Inicie sesión en el clúster desde la CLI de {{site.data.keyword.cloud_notm}} Private y ejecute el mandato
`kubectl get replicaset`.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. Localice el conjunto de réplicas de igual que corresponda al igual original. El nombre del conjunto de réplicas de igual debe coincidir con la parte del principio del nombre del igual en la sección **Pod** de la consola de {{site.data.keyword.cloud_notm}} Private.
3. Suprima el conjunto de réplicas de igual.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. Suprima el nuevo pod de igual. Este es el pod de igual que tiene el estado `CrashLoopBackOff` en la interfaz de usuario.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

El igual se habrá actualizado correctamente cuando vea que se ha creado un nuevo pod de igual con el estado `Running` (en ejecución).

---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

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

# Despliegue de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} Private
{: #ca-deploy}

Tras importar el diagrama de Helm de {{site.data.keyword.blockchainfull}} Platform en {{site.data.keyword.cloud_notm}} Private, puede desplegar los componentes individuales. La entidad emisora de certificados (CA) es la raíz de confianza de la organización y le permite generar credenciales para los demás componentes que vaya a desplegar. Como consecuencia, debe desplegar una CA antes de desplegar los demás componentes. Cada organización de una red blockchain multinube debe desplegar su propia CA.  Para obtener más información sobre las CA y qué rol desempeñan en una red blockchain, consulte [Entidades emisoras de certificados](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).
{:shortdesc}

Antes de desplegar una entidad emisora de certificados, revise las [Consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Recursos necesarios
{: #ca-deploy-resources-required}

Asegúrese de que el clúster de {{site.data.keyword.cloud_notm}} Private cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |

**Notas:**
- Un vCPU es un núcleo virtual que se asigna a una
máquina virtual o a un núcleo de procesador físico si el servidor no está particionado
para máquinas virtuales. Debe tener en cuenta los requisitos de vCPU cuando decida el núcleo de procesador virtual (VPC) para su despliegue en {{site.data.keyword.cloud_notm}} Private. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Núcleo de procesador virtual").
- El requisito de almacenamiento de datos depende de cuántas identidades y certificados se almacenen. El almacenamiento de CA no es tan pesado como el almacenamiento de iguales o clasificadores, pero esto dependerá del caso de uso. Cuanto mayor sea el número de usuarios, más almacenamiento se necesitará.
- Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con una cantidad elevada de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande para la CA. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si agota el almacenamiento en la CA, debe desplegar una nueva CA con un sistema de archivos mayor y dejar que se sincronice a través de las demás CA en los mismos canales.

## Almacenamiento
{: #ca-deploy-storage}

Debe determinar el almacenamiento que utilizará la CA. Si utiliza los valores predeterminados, el diagrama de Helm creará una reclamación de volumen persistente (PVC) de 2 Gi con el nombre `fabric-ca-pvc` para la CA.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de {{site.data.keyword.cloud_notm}} Private; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de desplegar la CA.

Puede optar por desplegar la CA en las plataformas AMD64 o S390X. Sin embargo, tenga en cuenta que el [suministro dinámico ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) solo está disponible para los nodos AMD64 en {{site.data.keyword.cloud_notm}} Private. Si el clúster incluye una combinación de nodos trabajadores S390X y AMD64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, deberán crearse [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y configurarse con etiquetas que se puedan utilizar para refinar el proceso de enlace de PVC de Kubernetes.

## Requisitos previos para desplegar una CA
{: #ca-deploy-prerequisites}

1. Para poder instalar una CA en {{site.data.keyword.cloud_notm}} Private, debe [instalar {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [instalar el diagrama de Helm de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Si utiliza Community Edition y desea ejecutar este diagrama de Helm en un clúster de {{site.data.keyword.cloud_notm}} Private sin conexión a Internet, debe crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de {{site.data.keyword.cloud_notm}} Private. Para obtener más información, consulte
[Adición de aplicaciones destacadas a clústeres sin conexión a Internet
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adición de aplicaciones destacadas a clústeres sin conexión a Internet"){:new_window}. Tenga en cuenta que puede encontrar el archivo de especificación manifest.yaml en ibm-blockchain-platform-dev/ibm_cloud_pak en el diagrama de Helm.

3. Recupere el valor de la dirección IP de proxy de clúster desde la consola de {{site.data.keyword.cloud_notm}} Private. **Nota:** necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para acceder a la IP de proxy. Inicie sesión en el clúster de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla.

  Guarde este valor, ya que lo utilizará cuando configure el campo `Proxy IP` del diagrama de Helm.
  {:important}

4. Cree un nombre de usuario y una contraseña de administrador de CA y almacénelos dentro de un objeto de secreto en {{site.data.keyword.cloud_notm}} Private. Encontrará los pasos para crear el secreto en la [siguiente sección](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret).

## Creación del secreto del administrador de CA
{: #ca-deploy-admin-secret}

Cuando se inicia por primera vez la CA, contiene una identidad de administrador que se crea para que trabaje con la CA. Debe crear un nombre de usuario y una contraseña para esta identidad de administrador antes de desplegar la CA. Es importante tomar nota de estos valores. Se utilizarán más adelante al trabajar con la CA y desplegar componentes de red adicionales. Cree un [secreto de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Secretos") para almacenar los valores de
`username` y `password` de administrador para el despliegue de CA. Un secreto de Kubernetes le permite proteger y compartir información sin tener que pasarla directamente al despliegue.

1. Cree el nombre de usuario y la contraseña del administrador y codifique los valores en el formato base64. Ejecute los mandatos siguientes en una ventana de terminal y sustituya `admin` y `adminpw` por los valores que desee utilizar.
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **Importante:** guarde los valores de `admin` y `adminpw` que ha creado anteriormente. Guarde también los valores codificados de estos campos, que serán necesarios cuando cree el objeto de secreto de Kubernetes.

2. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Configuración** y, a continuación, pulse **Secretos**. Pulse el botón **Crear secreto** para abrir un panel emergente que le permite generar un nuevo objeto de secreto.

3. En el separador **General**, complete los campos siguientes:
  - **Nombre:** proporcione un nombre exclusivo dentro del clúster para el secreto. Utilizará este nombre para configurar la CA. El nombre debe estar en minúsculas.
  - **Espacio de nombres:** el espacio de nombres para añadir el secreto. Seleccione el `espacio de nombres` en el que desee desplegar la CA.
  - **Tipo:** especifique el valor `Opaque`.

4. Deje el separador **Anotaciones** en blanco.

5. En el separador **Datos**, añada el nombre de usuario y la contraseña como pares de clave-valor.
  1. En el primer campo **Nombre**, especifique `ca-admin-name`.
  2. En el primer campo **Valor**, especifique el resultado de `echo -n admin | base64` del paso uno anterior.
  3. Pulse el botón **Añadir datos** para añadir un segundo par de clave-valor.
  4. En el segundo campo **Nombre**, especifique `ca-admin-password`.
  5. En el segundo **Valor**, especifique el resultado de `echo -n adminpw | base64` del paso uno anterior.  
    La **Figura 1** muestra lo que puede contener el secreto de la consola de {{site.data.keyword.cloud_notm}} Private en función de los valores que haya especificado para las claves `ca-admin-name` y `ca-admin-password`.

    ![Secreto de la consola de {{site.data.keyword.cloud_notm}} Private](../images/CreateCASecret.png "Secreto de la consola de {{site.data.keyword.cloud_notm}} Private")  
    *Figura 1. Secreto de la consola de {{site.data.keyword.cloud_notm}} Private*
  6. Pulse **Crear** para construir un nuevo objeto de secreto.

El diagrama de Helm de CA utiliza los nombres `ca-admin-name` y `ca-admin-password` como valores de clave para desplegar el secreto en el release de Helm.

**Nota:** el secreto del administrador de CA no se eliminará del clúster de {{site.data.keyword.cloud_notm}} Private al suprimir el release de Helm. Usted es responsable de gestionar los secretos de su clúster de {{site.data.keyword.cloud_notm}} Private. Si tiene pensado desplegar otra CA en el futuro, puede reutilizar el secreto de administrador de CA. De lo contrario, deberá suprimirlo del clúster de {{site.data.keyword.cloud_notm}} Private.

## Configuración
{: #ca-deploy-configuration}

Tras crear el secreto de administrador de CA, puede utilizar los pasos siguientes para configurar e instalar la CA. Puede instalar solo una CA al mismo tiempo.

1. Inicie una sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico etiquetado como
`ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` si ha descargado Community Edition. Pulse sobre el mosaico para abrirlo y podrá ver un archivo Readme que incluye información sobre la instalación y configuración del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Especifique los valores para los [parámetros de configuración generales](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters) y acepte el acuerdo de licencia.
5. Abra el triángulo `Todos los parámetros` y especifique el valor de los [parámetros de configuración global](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters).
6. Desplácese hacia abajo hasta la sección **Configuración de CA**. Marque el recuadro de selección `Instalar
CA` y rellene los [valores de configuración](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-parameters) de la entidad emisora de certificados.  
7. Pulse **Instalar**.

### Parámetros de configuración
{: #ca-deploy-configuration-parms}

En la tabla siguiente se muestran los parámetros configurables de la plataforma {{site.data.keyword.blockchainfull_notm}}, **específicos del componente de CA**, y sus valores predeterminados.  

**Aunque la interfaz de usuario del diagrama de Helm indica que no se necesita ninguna configuración adicional, debe especificar determinados parámetros para desplegar una CA.**

#### Parámetros de configuración generales y globales
{: #ca-deploy-global-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Nombre de release de Helm`| Nombre del release de Helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez que intente instalar un componente. | ninguno | sí |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
|**Configuración global**| Parámetros que se aplican a todos los componentes del diagrama de Helm|||
| `Nombre de cuenta de servicio`| Especifique el nombre de la
[cuenta de servicio ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configurar cuentas de servicio para pods") que utilizará para ejecutar el pod. | predeterminado | no |

#### Parámetros de configuración de CA
{: #ca-deploy-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Instalar CA`| Selecciónelo para instalar una CA. | sin marcar | sí, si desea desplegar una CA |
| `Nombre de CA`| Especifique el nombre a utilizar para la entidad emisora de certificados. **Importante:** tome nota de este valor. Se necesitará más adelante al configurar un clasificador o igual. | SampleOrgCA | sí |
| `Arquitectura del nodo trabajador de CA`| Seleccione su arquitectura de nodo trabajador de {{site.data.keyword.cloud_notm}} Private (ADM64 o S390X). | AMD64 | sí|
| `Tipo de base de datos de CA`| El tipo de base de datos para almacenar datos de CA. Solo se admite SQLite. | SQLite | sí |
| `Persistencia de datos de CA habilitada` | Si se marca, los datos estarán disponibles cuando se reinicie el contenedor. De lo contrario, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod. | marcada | no |
| `Utilizar suministro dinámico de CA` | Marcar para habilitar el suministro dinámico para volúmenes de almacenamiento. | marcada | no |
| `Nombre de clase de almacenamiento de CA`| Especifique un nombre de clase de almacenamiento exclusivo. De lo contrario, se utilizará la clase de almacenamiento predeterminada en el clúster. | ninguno | Depende de cómo se haya configurado el clúster de {{site.data.keyword.cloud_notm}} Private. Consulte al administrador del clúster |
| `Reclamación de volumen existente de CA`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de CA`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Valor de selector de CA`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Modalidad de acceso de almacenamiento de CA`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC. | ReadWriteMany | sí |
| `Tamaño de reclamación de volumen de CA`| Elija el tamaño de disco a utilizar. | 2Gi | sí |
| `Repositorio de imágenes de CA`| Ubicación del diagrama de Helm de CA. | ibmcom/ibp-fabric-ca | sí |
| `Etiqueta de imagen de Docker de CA`| Valor de la etiqueta asociada con la imagen de CA. Este campo se rellena automáticamente con la versión de la imagen. No lo modifique.| 1.2.1 | sí |
| `Tipo de servicio de CA` | Se usa para especificar si los
[puertos externos se deben exponer
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release | NodePort | sí |
| `Secreto de CA (obligatorio)`| Especifique el nombre del objeto de secreto de Kubernetes que ha creado para
`ca-admin-name` y `ca-admin-password`. | ninguno | sí |
| `Solicitud de CPU de CA`| Especifique el número mínimo de CPU a asignar a la CA. | 1 | sí |
| `Límite de CPU de CA`| Especifique el número máximo de CPU a asignar a la CA. | 2 | sí |
| `Solicitud de memoria de CA`| Especifique la cantidad mínima de memoria a asignar a la CA. | 1Gi | sí |
| `Límite de memoria de CA`| Especifique la cantidad máxima de memoria a asignar a la CA. | 4 Gi | sí |
| `Nombre de instancia TLS de CA`| Especifique el nombre de la instancia TLS de CA que se utilizará para inscribir un clasificador o un igual. | tlsca | sí |
| `Nombre común de CSR`| Especifique el nombre común (CN) que presentará el certificado raíz de CA generado cuando se establezca contacto con él. | tlsca-common | sí |
| `IP de proxy`| Especifique la
[IP de nodo de proxy para el clúster
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "Instalación de IBM Cloud Private detrás de un proxy HTTP") donde está desplegada la CA. | 127.0.0.1 | sí |


### Utilización de la línea de mandatos de Helm para instalar el release de Helm
{: #ca-deploy-helm-cli}

Como alternativa, puede utilizar la CLI de Helm para instalar el release de Helm. Antes de ejecutar el mandato `helm install`, asegúrese de
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
--values ca-s390x-values.yaml \
--tls
```

Puede crear un nuevo archivo `yaml` editando el archivo `values.yaml` incluido en el archivo de archivado descargado. El archivo `values.yaml` incluye todos los parámetros necesarios con sus valores predeterminados.

## Verificación de la instalación de la CA
{: #verifiying-ca-installation}

Tras completar los parámetros de configuración y pulsar el botón **Instalar**, pulse el botón **Ver release de Helm** para ver el despliegue. Si se ha realizado correctamente, debe aparecer el valor 1 en los campos
`DESIRED`, `CURRENT`, `UP TO DATE` y `AVAILABLE` de la tabla Despliegue. Es posible que tenga que pulsar Renovar y esperar a que se actualice la tabla. También puede encontrar la tabla Despliegue pulsando el icono
**Menú** de la esquina superior izquierda de la consola de {{site.data.keyword.cloud_notm}} Private. En la lista de menús, pulse
**Cargas de trabajo** y, a continuación, **Releases de Helm**.

Si se desplaza hacia abajo hasta la sección `Notas`, puede encontrar información importante que podrá utilizar para
[trabajar con la CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

Tras instalar la CA de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private, se crea un mapa de configuración con los valores predeterminados de las variables de entorno. A continuación, puede cambiar o añadir variables de entorno para el servidor de CA con el fin de configurar su comportamiento. Para obtener más información sobre los parámetros de configuración del servidor de CA, consulte
[la documentación del servidor de CA de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Servidor de CA de Fabric").

Tras configurar el mapa de configuración, debe reiniciar el servidor de CA para que los cambios entren en vigor. Para reiniciar el servidor de CA, puede suprimir el POD del servidor de CA de Fabric. {{site.data.keyword.cloud_notm}} Private creará un nuevo POD que refleje los cambios.

## Visualización de los registros de CA
{: #ca-deploy-view-logs}

Los registros de los componentes se pueden consultar desde la línea de mandatos mediante [`mandatos de CLI kubectl`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que está incluido en su clúster de {{site.data.keyword.cloud_notm}} Private. Para obtener más información, consulte estas
[instrucciones para acceder a los registros](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs).

## Funcionamiento de la CA
{: #ca-deploy-operate}

La CA será la raíz de confianza de la organización. Debe utilizar la CA para generar certificados para los demás componentes.  Como consecuencia, antes de desplegar un clasificador o un igual, debe [configurar la CA y realizar varios pasos operativos](/docs/services/blockchain/howto/CA_operate.html#ca-operate)

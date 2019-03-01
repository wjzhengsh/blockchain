---

copyright:
  years: 2018, 2019
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

# Configuración de {{site.data.keyword.cloud_notm}} Private
{: #icp-setup}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Antes de desplegar componentes de {{site.data.keyword.blockchainfull}} Platform y crear la red blockchain en
{{site.data.keyword.cloud_notm}} Private, debe configurar {{site.data.keyword.cloud_notm}} Private en su propio entorno.
{:shortdesc}

## Requisitos previos
{: #icp-setup-prerequisites}

Complete los requisitos previos siguientes y prepare el entorno para instalar {{site.data.keyword.cloud_notm}} Private.

### Docker
{{site.data.keyword.cloud_notm}} Private requiere que Docker esté instalado. Siga las instrucciones relacionadas de
[Instalación de {{site.data.keyword.cloud_notm}} Private ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "Instalación de {{site.data.keyword.cloud_notm}} privado") para instalar Docker.

### Valores de {{site.data.keyword.cloud_notm}} Private
Antes de instalar {{site.data.keyword.cloud_notm}} Private, las sugerencias siguientes le resultarán útiles para preparar los nodos para la instalación de {{site.data.keyword.cloud_notm}} Private. Encontrará los requisitos previos adicionales de {{site.data.keyword.cloud_notm}} Private en la [documentación de {{site.data.keyword.cloud_notm}} Private ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "Preparación del clúster para la instalación").

#### Actualización del valor de `vm.max_map_count`
{{site.data.keyword.cloud_notm}} Private utiliza Elastic Search para el registro y la calibración. Para evitar excepciones de falta de memoria, Elastic Search requiere que se configure la propiedad del sistema `vm.max_map_count`. Antes de instalar {{site.data.keyword.cloud_notm}} Private, consulte las
[instrucciones de configuración de Elastic Search ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Memoria virtual") para configurar esta propiedad en cada nodo. Puede utilizar los mandatos siguientes para establecer esta propiedad de forma permanente:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configuración del archivo `/etc/hosts` en cada nodo del clúster

- {{site.data.keyword.cloud_notm}} Private utiliza [Kubernetes ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Información básica de Kubernetes") para gestionar aplicaciones contenerizadas. El servidor de nombres de dominio (DNS) de Kubernetes fallará si no se configuran nombres de hosts en el archivo
`/etc/hosts` de cada nodo. [Inserte la dirección IP, nombre de host y nombre abreviado de cada nodo en el clúster ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "Configuración del clúster") dentro del archivo `/etc/hosts` de cada nodo.

- [No hay soporte para IPv6 en {{site.data.keyword.cloud_notm}} Private ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "No hay soporte para IPv6"). Para evitar problemas con el servicio DNS en un clúster de {{site.data.keyword.cloud_notm}} Private, inhabilite los valores de IPv6 en el archivo `/etc/hosts` de cada nodo comentando la línea siguiente con un signo `#` al principio de la línea:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Recursos necesarios
{: #icp-setup-resources}

Asegúrese de que el sistema {{site.data.keyword.cloud_notm}} Private cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| CA | 1 |192 MB | 1 GB |
| Clasificador | 2 | 512 MB | 100 GB con posibilidad de ampliación |
| Igual | 2 | 2 GB | 50 GB con posibilidad de ampliación |
| CouchDB para igual | 2| 2 GB |50 GB con posibilidad de ampliación |

 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una
máquina virtual o a un núcleo de procesador físico si el servidor no está particionado
para máquinas virtuales. Debe tener en cuenta los requisitos de vCPU cuando decida el núcleo de procesador virtual (VPC) para su despliegue en {{site.data.keyword.cloud_notm}} Private. VPC es una unidad de medida que determina el coste de licencias de los productos de {{site.data.keyword.IBM_notm}}. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; 250 GB para el igual y 500 GB para el servicio de ordenación por ejemplo. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el igual o clasificador, debe desplegar un nuevo igual o clasificador con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

#### Consideraciones sobre el almacenamiento
{: #icp-setup-storage-considerations}

* Debe determinar el almacenamiento que utilizarán los componentes. Si utiliza los valores predeterminados, el diagrama de Helm del igual creará una nueva reclamación de volumen persistente con el nombre `my-data-pvc` para los datos del igual. Si selecciona CouchDB como base de datos de libro mayor, el diagrama Helm crea otra reclamación de volumen persistente de llamada `statedb-pvc` para la base de datos de libro mayor.
* Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* storageClass durante la instalación de {{site.data.keyword.cloud_notm}} Private; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.
* El [suministro dinámico ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "suministro de volumen dinámico") solo está disponible para los nodos amd64 en {{site.data.keyword.cloud_notm}} Private. Por lo tanto, si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.
* Si no se utiliza el suministro dinámico, los [volúmenes persistentes ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "volúmenes persistentes") se deben crear y configurar con etiquetas que se puedan utilizar para adaptar el proceso de enlace de reclamaciones de volumen persistente (PVC).
* Si utiliza volúmenes persistentes de NFS v2/v3, debe habilitar el módulo **Supervisor de estado de NFS para bloqueos del sistema de archivos NFSv2/v3**, también conocido como **rpc-statd**, en el sistema host donde existe el sistema de archivos NFS. Este módulo permite que el sistema de archivos NFS pueda comprobar si existen bloqueos exclusivos en archivos que mantienen otros procesos. Ejecute los mandatos siguientes para habilitar este módulo:

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Instalación de {{site.data.keyword.cloud_notm}} Private
{: #icp-setup-install}

Realice los pasos siguientes para instalar y configurar {{site.data.keyword.cloud_notm}} Private en el entorno.

1. Instale un clúster de [{{site.data.keyword.cloud_notm}} Private ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo") ](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) de la versión 3.1.0. Si desea utilizar el diagrama de Helm para desarrollo, prueba o experimentación, puede instalar [{{site.data.keyword.cloud_notm}} Private Community Edition versión 3.1.0 ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE versión 3.1.0") de forma gratuita.

2. Instale la CLI de {{site.data.keyword.cloud_notm}} Private
[3.1.0 ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) para instalar y trabajar con la CA.

Después de instalar {{site.data.keyword.cloud_notm}} Private, puede continuar con la [importación del diagrama de Helm de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) en el clúster de {{site.data.keyword.cloud_notm}} Private.

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

# Acerca de los iguales remotos
{: #remote-peer-overview}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Puede ejecutar iguales remotos de la plataforma {{site.data.keyword.blockchainfull}} en {{site.data.keyword.cloud_notm}} privado (ICP) <!--[AWS]or on AWS Cloud --> después de conectarlos a una red blockchain existente. La ejecución de iguales fuera de {{site.data.keyword.cloud_notm}} proporciona más flexibilidad para crecer o unirse a una red blockchain al tiempo que se aprovecha una red existente dentro de {{site.data.keyword.cloud_notm}}. Los iguales remotos utilizan las entidades emisoras de certificados (CA) y el servicio de ordenación en la plataforma, pero le permiten colocar a su igual con otras aplicaciones fuera de {{site.data.keyword.cloud_notm}}.
{:shortdesc}

<!--[AWS]Move ICP description here.-->{{site.data.keyword.cloud_notm}} privado (ICP) es una plataforma basada en Kubernetes para crear una nube privada en un entorno local. Puede utilizar ICP para ejecutar un igual remoto y conectar el igual remoto a una red blockchain en la plataforma {{site.data.keyword.blockchainfull_notm}}. El igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP aprovecha los servicios de almacenamiento, seguridad, registro y soporte de ICP para que pueda gestionar los iguales remotos en su entorno local. Para obtener más información sobre ICP, consulte la [{{site.data.keyword.cloud_notm}} privado ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentación de {{site.data.keyword.cloud_notm}} privado").  

**Nota:** el igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} es actualmente una oferta Beta adecuada para evaluación y experimentación. **Esta edición Beta no está pensada para su uso en producción.** Para obtener más información, consulte [licencia y precios](#remote-peer-license-pricing).

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

La edición Beta da soporte a la plataforma de nube de {{site.data.keyword.cloud_notm}} privado (ICP), v2.1.0.3. Tenga en cuenta que la red de la plataforma {{site.data.keyword.blockchainfull_notm}} y los nodos iguales remotos de ICP se deben ejecutar con la misma **versión de Fabric 1.1**.

## Consideraciones
{: #remote-peer-limitations}

Un igual remoto no tiene acceso a toda la funcionalidad ni al soporte de los iguales alojados en la plataforma {{site.data.keyword.blockchainfull_notm}}. Antes de ejecutar iguales remotos, asegúrese de que comprende las restricciones y limitaciones siguientes:
- Los iguales remotos que se ejecutan en otros entornos de nube no son visibles en el supervisor de la red en {{site.data.keyword.cloud_notm}}.
- No se puede direccionar a los iguales remotos mediante la interfaz de usuario de Swagger en la interfaz de usuario del supervisor de red.
- El usuario es el responsable de la gestión de la supervisión del estado, la seguridad, el registro y el uso de recursos de sus iguales remotos.
- Solo puede conectar iguales remotos a redes blockchain que están en el nivel de Hyperledger Fabric v1.1.
- El tipo de base de datos del igual remoto debe coincidir con el tipo de base de datos de la red blockchain, ya sea LevelDB o CouchDB.
- La interfaz CouchDB Fauxton no está disponible en el igual remoto.
- Actualmente no se da soporte a [gossip (rumor)](../glossary.html#gossip) para los iguales remotos.


## Requisitos previos
{: #remote-peer-prereq}

Para utilizar un igual remoto, debe tener una organización que sea miembro de una red del Plan inicial o del Plan empresarial en la plataforma {{site.data.keyword.blockchainfull_notm}}. El igual remoto aprovecha los puntos finales de API, las CA de Hyperledger Fabric y el servicio de ordenación de la red del plan de la plataforma {{site.data.keyword.blockchainfull_notm}} para funcionar. Si no es miembro de ninguna red blockchain, tiene que crear o unirse a una red. Para obtener más información, consulte [Creación de una red](../get_start.html#creating-a-network) o [Cómo unirse a una red](../get_start.html#joining-a-network).


## Licencias y precios
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->Para acceder a iguales remotos para que se ejecuten en ICP, consulte [Licencia y precios de iguales remotos en ICP](remote_peer_icp.html#license-pricing-icp "Licencia y precios de iguales remotos en ICP"). Las licencias de la edición Beta de los iguales remotos son gratuitas. <!--[AWS] for both platforms.--> Más adelante, una oferta de disponible a nivel general (GA) sustituirá la versión beta.

No se da soporte a la migración de la versión Beta a GA. Es necesario descargar e instalar nuevos iguales remotos para utilizar la oferta GA, cuando salga al mercado. Luego podrá unir nuevos iguales a los mismos canales de la misma red que sus iguales remotos Beta.

**Nota:** para trabajar con un igual remoto, debe tener una organización que pertenezca a una red del Plan inicial o del Plan empresarial en la plataforma {{site.data.keyword.blockchainfull_notm}}. Esto implica que el cliente u otro miembro de la red debe pagar la [cuota de pertenencia](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) de IBM Blockchain para la organización. Para obtener más información sobre cómo pagar las cuotas, consulte [Modalidad de pago](paying_mode.html).  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## Despliegue de un igual remoto
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

En el diagrama siguiente se describen los pasos para desplegar un igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} en ICP. Para obtener más información sobre cómo desplegar iguales remotos en ICP, consulte [Despliegue de iguales remotos en {{site.data.keyword.cloud_notm}} privado](remote_peer_icp.html "Despliegue de iguales remotos en {{site.data.keyword.cloud_notm}} privado").

![Flujo de despliegue de iguales remotos en ICP](../images/remote_peer_ICP_flow.png "Flujo de despliegue de iguales remotos en ICP")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*Figura 1. Flujo de despliegue de iguales remotos en ICP*


## Funcionamiento de un igual remoto
{: #operate-remote-peer}

Después de desplegar el igual remoto, tiene que completar varios pasos operativos para que el igual pueda enviar transacciones a la red. Los pasos operativos incluyen añadir su organización a un canal, unir el igual remoto al canal, instalar el código de encadenamiento en el igual remoto, crear una instancia del código de encadenamiento en el canal y conectar aplicaciones al igual remoto. Para obtener más información, consulte <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[Funcionamiento de iguales remotos en {{site.data.keyword.cloud_notm}} privado](remote_peer_operate_icp.html#remote-peer-operate).

## Residencia de datos
{: #data-residency}

Los requisitos de residencia de los datos son restricciones que controlan el lugar donde residen los datos. El requisito más común en cuanto a residencia de datos está asociado a las leyes de determinados países, según las que todos los datos de clientes que se procesan y almacenan en un sistema de TI deben permanecer dentro de las fronteras del país específico. Paralelamente, algunas empresas de sectores muy regulados, como las gubernamentales, de sanidad y de servicios financieros, obligan a que todos sus datos se almacenen tras su cortafuegos. Además, algunas compañías tienen una política corporativa por la que ciertos tipos de datos, generalmente la información de identificación personal, deben permanecer en privado y estar completamente detrás de su cortafuegos corporativo. Por lo tanto, para conseguir la residencia de datos, todos los componentes de la red blockchain deben formar parte del mismo [canal](../glossary.html#channel) y deben residen dentro de las fronteras de un solo país.

Para hacer frente a los requisitos de residencia de datos, es importante comprender la arquitectura de Hyperledger Fabric subyacente a la plataforma {{site.data.keyword.blockchainfull_notm}}. La arquitectura se centra en torno a tres componentes clave: servicio de ordenación, entidad emisora de certificados (CA) e igual. Un igual recibe actualizaciones de estado ordenadas en forma de bloques desde el servicio de ordenación y mantiene el estado y el libro mayor. Por lo tanto, un igual y un servicio de ordenación tienen una relación directa. El libro mayor contiene los valores más recientes de todas las claves y los datos que incluyen los registros de transacciones.

Además, las aplicaciones cliente utilizan los [SDK de Fabric](../v10_application.html#using-the-fabric-sdks) para enviar transacciones a los iguales y al servicio de ordenación. Estas transacciones incluyen datos del [conjunto de lectura-escritura ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "semántica del conjunto de lectura-escritura"), que contienen los valores de clave-valor en el libro mayor.

Si la residencia de datos en el país es un requisito para su empresa, el servicio de ordenación, los nodos iguales y la aplicación cliente deben residir en el mismo país. Cuando se crea una red de la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}}, tiene la opción de seleccionar la ubicación de la red. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obtener más información sobre regiones y ubicaciones, consulte [Regiones y ubicaciones de la plataforma {{site.data.keyword.blockchainfull_notm}}](../reference/ibp_regions.html). Para conseguir la residencia de datos en uno de estos países, el igual remoto debe residir en el mismo remoto que la red de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Si la red de la plataforma {{site.data.keyword.blockchainfull_notm}} contiene nodos de igual remoto y del servicio de ordenación entre países, puede utilizar canales para segregar los datos a un subconjunto de iguales de la red. Los nodos de ordenación siempre están ubicados en el centro de datos que ha seleccionado para alojar la red. No es posible tener solicitantes entre países. Sin embargo, los iguales pueden estar ubicados en el centro de datos o en una ubicación remota fuera de {{site.data.keyword.cloud_notm}}. Por lo tanto, para lograr la residencia de datos, se puede crear un canal en el que el solicitante y todos los iguales, ya sean iguales locales para el centro de datos o iguales remotos, residan en el mismo país. De este modo, todos los datos que comparten el solicitante y los iguales están dentro de las fronteras de un solo país. Pueden existir otros canales si se necesitan, en los que el solicitante y los iguales remotos se encuentren entre países y no se requiera residencia de datos.

En el futuro, la nueva tecnología de Hyperledger Fabric mejorará la capacidad de obtener una mejor residencia de datos utilizando [Recopilaciones de datos privados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Recopilaciones de datos privados") y Zero Knowledge Proof.

- Una recopilación de datos privados garantiza que los datos privados se comparten entre igual e igual (mediante el protocolo gossip) solo con los iguales que tienen autorización para verlos, por ejemplo iguales que están dentro de las fronteras del país. Los datos se almacenan en una base de datos privada en el igual.  El servicio de ordenación no está implicado y no ve los datos privados. Se escribe un hash de esos datos en los libros mayores de cada igual del canal. El hash que se utiliza para la validación de estado sirve como prueba de la transacción y se puede utilizar para fines de auditoría.

- Zero-Knowledge Proof (ZKP) permite que un “comprobador” asegure a un “verificador” que conoce un secreto sin tener que revelar el propio secreto. Es una forma de mostrar que sabes algo que satisface una declaración sin mostrar lo que sabes.

Para obtener más información sobre estas tecnologías, consulte el documento técnico sobre [Transacciones privadas y confidenciales con Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transacciones privadas y confidenciales con Hyperledger Fabric").

## Obtención de soporte
{: #remote-peer-support}

La edición Beta del igual remoto de {{site.data.keyword.blockchainfull_notm}} está pensada para exploración, desarrollo y prueba. **No utilice esta edición para producción.** La plataforma {{site.data.keyword.blockchainfull_notm}} no da soporte a esta edición. Si detecta algún problema relacionado con el igual remoto, consulte [Recursos de blockchain y foros de soporte](../v10_dashboard.html#support-forums). También puede consultar los recursos de soporte en la pantalla **Obtener ayuda** del supervisor de red.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

Para problemas relacionados con {{site.data.keyword.cloud_notm}} privado, ICP ofrece [soporte digital gratuito y soporte de Community Edition ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "Soporte digital gratuito de ICP y soporte de Community Edition").

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->

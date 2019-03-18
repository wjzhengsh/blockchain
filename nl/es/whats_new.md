---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Novedades
{: #whats-new}

## 8 de febrero de 2019
{: #whats-new-2-08-2019}

{{site.data.keyword.blockchainfull}} Platform lanza una versión gratuita 2.0 beta, la próxima generación de soluciones de {{site.data.keyword.blockchainfull_notm}} Platform que le permiten desplegar, utilizar y supervisar la red de blockchain. {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta incluye una nueva consola de interfaz de usuario que se puede utilizar para desplegar y gestionar componentes de blockchain en su propio clúster del servicio {{site.data.keyword.IBM_notm}} Kubernetes en {{site.data.keyword.cloud_notm}}. La versión {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta le permitirá:

**Cree su red con mayor rapidez y facilidad, mediante una experiencia sencilla**

*	Facilite la integración entre el desarrollo de contratos inteligentes (VS Code) y la gestión de red
*	DevOps simplificado le permite pasar del entorno de desarrollo al de prueba y al de producción desde una misma consola
*	Soporte para escribir contratos inteligentes en los lenguajes Javascript, Java y Go

**Utilice y gestione redes con control total**

*	Despliegue solo los componentes de blockchain que necesita (igual, servicio de ordenación, autoridad emisora de certificados)
*	La consola rediseñada le permite gestionar los componentes de red en un lugar, sin importar dónde estén desplegados
*	Mantenga el control completo de sus identidades, libro mayor y contratos inteligentes

**Incremente las redes distribuidas con facilidad con la flexibilidad con soporte multinube**

*	Conecte con nodos que se ejecutan en cualquier entorno (local, público, nubes híbridas)
*	Conecte fácilmente un solo igual a varias redes de la industria
*	Comience con un entorno de pequeño tamaño, pague a medida que crezca por lo que utilice sin inversión inicial y actualice fácilmente a través de Kubernetes

- Encontrará más información sobre la versión {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta en [Acerca de {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Encontrará instrucciones sobre cómo desplegar el release gratuito 2.0 beta en un clúster del servicio {{site.data.keyword.IBM_notm}} Kubernetes en [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- Dispone de nuevas guías de aprendizaje para utilizar la versión {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta en la subsección **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** de la categoría **CÓMO SE HACE**.
  * La [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) le ayuda en el proceso de alojar una red mediante la creación de un clasificador y un igual.
  * En la [guía de aprendizaje sobre cómo unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) se explica cómo unirse a una red existente creando un igual y uniéndolo a un canal.
  * La guía para [Desplegar un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) contiene información sobre cómo escribir un contrato inteligente y cómo desplegarlo en la red.
- La oferta {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta se basa en Hyperledger Fabric v1.4 y da soporte al protocolo gossip de igual a igual, al descubrimiento de servicios y a datos privados. Consulte este [tema](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) para obtener más información sobre cómo configurar datos privados en la red.

- La extensión {{site.data.keyword.blockchainfull_notm}} Visual Studio Code está disponible en Visual Studio Code Marketplace. Los desarrolladores pueden utilizar la extensión para crear, probar y desplegar contratos inteligentes en una instancia de Hyperledger Fabric. La extensión es compatible con Hyperledger Fabric 1.3 y superiores. Para obtener más información sobre cómo utiliza la extensión VS Code, consulte [Herramientas para contratos inteligentes](/docs/services/blockchain/vscode-extension.html#develop-vscode).  

La tabla de contenido se ha mejorado con la agrupación de todos los temas de iniciación en una sección denominada **Guías de aprendizaje de iniciación**. Además, todas las ofertas de {{site.data.keyword.blockchainfull_notm}} Platform que se describen en las subsecciones **Acerca de {{site.data.keyword.blockchainfull_notm}} Platform** están ahora contenidas en la sección **INFORMACIÓN**.

**Nota:** los créditos de nube gratuitos ya no están disponibles para los usuarios del Plan inicial.

## 7 de diciembre de 2018
{: #whats-new-12-7-2018}

Los temas *Operación de la red del Plan inicial* y *Operación de la red del Plan empresarial* se han combinado y sustituido por una guía de aprendizaje individual sobre la [Utilización del supervisor de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 de noviembre de 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica {{site.data.keyword.blockchainfull_notm}} Platform para
{{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private es una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas basadas en Kubernetes y permite a los usuarios desplegar entidades emisoras de certificados (CA), clasificadores e iguales en x86, LinuxONE e IBM Z.
{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private se basa en Hyperledger Fabric v1.2.1 y se despliega utilizando diagramas de Helm de Kubernetes. Después de instalar el diagrama de Helm, puede encontrarlo como un servicio empaquetado en el catálogo de {{site.data.keyword.cloud_notm}} Private. Tenga en cuenta que
[esta oferta](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) es más adecuada para usuarios experimentados de Fabric.

Para obtener más información sobre {{site.data.keyword.cloud_notm}} Private, consulte la
[documentación de {{site.data.keyword.cloud_notm}} Private v3.1.0
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} - Documentación de IBM Cloud privado v3.1.0").

El release de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private también marca el fin del programa de igual remoto de la plataforma IBM Blockchain (Beta). Todavía es posible desplegar un igual en {{site.data.keyword.cloud_notm}} Private o en AWS y conectarlo a una red del Plan inicial o el Plan empresarial. Para obtener más información, consulte [cómo desplegar un igual en el Plan inicial o Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) y
[cómo desplegar un igual en AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Estos iguales se consideran iguales **distribuidos** en lugar de iguales remotos, ya que el despliegue está gestionado por el cliente y, como consecuencia, no hay ninguna ubicación central desde la que haya otras remotas.

Este release también presenta algunas mejoras en la tabla de contenidos de la documentación. Si es usuario del Plan inicial o empresarial, el podrá seguir encontrando el contenido en las secciones **APRENDER**, **CÓMO**, **REFERENCIA** y **AYUDA**, y los enlaces serán los mismos. Simplemente podría estar en una subsección distinta de la tabla de contenidos.

## 13 de noviembre de 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica {{site.data.keyword.blockchainfull_notm}} Platform para Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform para AWS le permite ejecutar iguales en la nube de AWS y conectar los iguales a redes blockchain existentes en {{site.data.keyword.cloud_notm}}. Los iguales en AWS pueden hacer uso del perfil de conexión y de otros componentes de blockchain en las redes existentes, al tiempo que le ofrecen más flexibilidad para colocar los iguales con otras aplicaciones fuera de {{site.data.keyword.cloud_notm}}. Para obtener más información, consulte [Acerca de {{site.data.keyword.blockchainfull_notm}} Platform en Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 de octubre de 2018
{: #whats-new-10-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform actualiza el Plan inicial de Hyperledger Fabric v1.1.0 a v1.2.1. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Importante:** Fabric v1.2.1 no es compatible actualmente con la versión beta del igual remoto, que utiliza un igual de v1.1.0. Las redes del Plan inicial, creadas antes del 4 de octubre de 2018 y basadas en Fabric v1.1.0, no se ven afectadas y se pueden seguir utilizando con la versión beta del igual remoto. {{site.data.keyword.blockchainfull_notm}} Platform actualizará la versión beta del igual remoto para que utilice el igual de v1.2.1, que será compatible con las nuevas redes de Plan inicial, que tienen el nivel v1.2.1 de Fabric, y con las redes del Plan empresarial, que tienen el nivel v1.1.0 de Fabric. Hasta que se actualice la versión beta del igual remoto a Fabric v1.2.1, no intente desplegar un igual remoto de v1.1.0 con una red de Plan inicial de v1.2.1.

## 4 de septiembre de 2018
{: #whats-new-9-4-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica la versión beta de la oferta del igual remoto. La oferta del igual remoto se basa en Hyperledger Fabric v1.1.0. Mediante el igual remoto, puede ejecutar nodos iguales de {{site.data.keyword.blockchainfull_notm}} Platform en su propio entorno de nube de {{site.data.keyword.cloud_notm}} Private o Amazon Web Services (AWS). Para obtener más información, consulte [Acerca de los iguales remotos](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

## 15 de junio de 2018
{: #whats-new-6-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica el Plan inicial GA. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 15 de mayo de 2018
{: #whats-new-5-15-2018}

{{site.data.keyword.blockchainfull_notm}} Platform actualiza el Plan empresarial de Hyperledger Fabric v1.0.0 a v1.1.0. Para obtener más información, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## 18 de marzo de 2018
{: #whats-new-3-18-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica el Plan inicial (Beta). El Plan inicial le ofrece un entorno de desarrollo y pruebas para aprender y practicar en una red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 11 de agosto de 2017
{: #whats-new-8-11-2017}

{{site.data.keyword.blockchainfull_notm}} Platform sustituye
{{site.data.keyword.blockchainfull_notm}} en la nube y publica el Plan empresarial. Para obtener más información, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

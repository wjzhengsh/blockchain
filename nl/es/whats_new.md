---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-31"

keywords: IBM Blockchain Platform, release, new features

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

## 31 de mayo de 2019
{: #whats-new-5-31-2019}

{{site.data.keyword.blockchainfull}} Platform de segunda generación, que le permite desplegar, operar y supervisar su red blockchain, pasa a tener disponibilidad general. Este release incluye una nueva consola de interfaz de usuario que se puede utilizar para desplegar y gestionar componentes de blockchain en su propio clúster del servicio Kubernetes de {{site.data.keyword.IBM_notm}} en {{site.data.keyword.cloud_notm}}.

Este release de {{site.data.keyword.blockchainfull_notm}} Platform incluye las siguientes características principales:

**CREAR ---- Experiencia integrada del desarrollador**
- **Codifique fácilmente** sus contratos inteligentes en Node.js, Golang o Java, escriba aplicaciones cliente con la nueva extensión de VS Code de {{site.data.keyword.blockchainfull_notm}}, aproveche la **integración de SDK** con la consola y aprenda con nuestras completas guías de aprendizaje y ejemplos.
- **DevOps simplificado** le permite pasar de la fase de desarrollo a la de prueba y producción en un solo entorno mediante la ampliación de los recursos de Kubernetes para añadir más componentes.
- **Características principales de Fabric actualizadas**. Aproveche las características más recientes de Hyperledger Fabric v1.4.1:
  -  [Servicio de ordenación de Raft ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/orderer/ordering_service.html#raft "Servicio de ordenación de Raft")]
  - **Integración del servicio {{site.data.keyword.cloud_notm}}.** Aproveche los servicios incorporados en {{site.data.keyword.cloud_notm}}, como el panel de control del servicio {{site.data.keyword.cloud_notm}} Kubernetes, {{site.data.keyword.IBM_notm}} Log Analysis con LogDNA e {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM).
  - [Recopilaciones de **datos privados**](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) que mejoran la privacidad de los datos al garantizar que los datos del libro mayor solo se comparten entre iguales autorizados mediante el protocolo gossip.
  - [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery"), que le permite descubrir y actualizar de forma dinámica la forma en que la aplicación interactúa con la red.
  - [Listas de control de accesos a canal ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/access_control.html "Listas de control de accesos") que le ofrecen un control adicional sobre los canales y los contratos inteligentes.

**OPERAR --- Control total de los despliegues**
- **Despliegue solo los componentes que necesite**. Conecte un igual a varios canales y redes, o aloje un servicio de ordenación al que pueden conectarse los socios de la empresa.
- **Mantenga un control completo de sus identidades**. Almacene y gestione las claves que se utilizan para administrar los nodos sin almacenar las claves privadas en {{site.data.keyword.cloud_notm}}.
- **Operación centralizada**. La consola de {{site.data.keyword.blockchainfull_notm}} Platform le permite desplegar y gestionar todas las organizaciones y nodos en **una consola central** sin tener que depender de {{site.data.keyword.IBM_notm}} ni de otros proveedores para gestionar los clasificadores o la entidad emisora de certificados. También puede añadir o eliminar miembros de un consorcio de blockchain, crear y unir canales e instalar y crear instancias de contratos inteligentes desde la consola.
- **Aloje o únase a una red**. Despliegue iguales alojados en el clúster en varios canales en varias nubes, o invite a otras organizaciones a unirse a su consorcio o canales mientras las organizaciones gestionan sus nodos de forma independiente entre infraestructuras.
- **Gestione el acceso** de los usuarios que pueden administrar o supervisar los nodos.
- **Acceso directo a los registros** de los nodos desde el servicio Kubernetes de {{site.data.keyword.IBM_notm}}. Utilice el servicio {{site.data.keyword.cloud_notm}} Log Analysis o un servicio de terceros para extraer y analizar los registros.
- **Interactúe directamente con los pods** mediante el panel de control de Kubernetes. Emita mandatos exec en los pods y contenedores para ejecutar mandatos y actualizar certificados desde la línea de mandatos.
- **Recopilación de firmas dinámica**, que le permite un mejor control sobre la gestión de la colaboración a través de configuraciones de canal.

**CRECER --- Escalabilidad y flexibilidad**
- **Elija su capacidad de cálculo**. Tiene flexibilidad para decidir la cantidad de CPU, de memoria y de almacenamiento que desea suministrar en el clúster de Kubernetes. Para obtener más información, consulte [Cómo interactúa el servicio {{site.data.keyword.cloud_notm}} Kubernetes con la consola](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
- **Escale** al alza o a la baja los recursos del clúster de Kubernetes y pague solo lo que necesite. Para obtener más información, consulte [Tarifas](/docs/services/blockchain/howto/pricing.html#ibp-pricing).
- **Recuperación tras desastre y alta disponibilidad multizona.** Esta opción duplica el despliegue de Kubernetes entre zonas, ofreciendo alta disponibilidad (HA) de sus componentes y recuperación tras desastre (DR).
- **Ejecución en cualquier lugar** (instrucciones próximamente). Gracias al **código base unificado** de la consola de {{site.data.keyword.blockchainfull_notm}} Platform, es posible ejecutar los componentes en {{site.data.keyword.cloud_notm}}, {{site.data.keyword.cloud_notm}} Private y en nubes públicas de terceros.

- Encontrará más información sobre la versión {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta en [Acerca de {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Encontrará instrucciones sobre cómo desplegar el release gratuito 2.0 beta en un clúster del servicio {{site.data.keyword.IBM_notm}} Kubernetes en [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- Dispone de nuevas guías de aprendizaje para utilizar la versión {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta en la subsección **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** de la categoría **CÓMO SE HACE**.
  * La [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) le ayuda en el proceso de alojar una red mediante la creación de un clasificador y un igual.
  * En la [guía de aprendizaje sobre cómo unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) se explica cómo unirse a una red existente creando un igual y uniéndolo a un canal.
  * La guía para [Desplegar un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) contiene información sobre cómo escribir un contrato inteligente y cómo desplegarlo en la red.
- La oferta {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta se basa en Hyperledger Fabric v1.4.1 y da soporte al protocolo gossip de igual a igual, al descubrimiento de servicios y a datos privados. Consulte este [tema](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) para obtener más información sobre cómo configurar datos privados en la red.

## 9 de mayo de 2019
{: #whats-new-5-09-2019}

{{site.data.keyword.blockchainfull_notm}} publica la versión 1.0.0 de la extensión de Visual Studio (VS) Code de {{site.data.keyword.blockchainfull_notm}} Platform. Este kit de herramientas de desarrollador contiene las prestaciones principales siguientes:

**Una interfaz de usuario reconfigurada para una navegación más sencilla**

**Guías de aprendizaje preceptivas y detalladas que abarcan cómo:**
- Desarrollar un contrato inteligente
- Suministrar una instancia del servicio {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}}
- Desplegar y realizar transacciones con su instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform

**Todas las características populares de releases anteriores, incluyendo:**
- La posibilidad de depurar contratos inteligentes
- Código de ejemplo
- Una función de cartera actualizada

Para obtener más información, consulte [Desarrollo de contratos inteligentes con la extensión de Visual Studio Code](/docs/services/blockchain/vscode-extension.html "Desarrollo de contratos inteligentes con la extensión de Visual Studio Code").

## 23 de abril de 2019
{: #whats-new-4-23-2019}

Se publica la v1.0.2 de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private, que utiliza el código base de Hyperledger Fabric v1.4.0 y admite el despliegue en {{site.data.keyword.cloud_notm}} Private v3.1.2.

Si desea actualizar la {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private existente a v1.0.2, consulte [Actualización del diagrama de Helm en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading).

Para obtener más información sobre Hyperledger Fabric v1.4.0, consulte [Documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/ "Documentación de Hyperledger Fabric v1.4"). Para obtener más información sobre {{site.data.keyword.cloud_notm}} Private, consulte [Documentación de {{site.data.keyword.cloud_notm}} Private v3.1.2 ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "Documentación de {{site.data.keyword.cloud_notm}} Private v3.1.2").

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
{: #whats-new-12-07-2018}

Los temas *Operación de la red del Plan inicial* y *Operación de la red del Plan empresarial* se han combinado y sustituido por una guía de aprendizaje individual sobre la [Utilización del supervisor de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 de noviembre de 2018
{: #whats-new-11-27-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private. {{site.data.keyword.cloud_notm}} Private es una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas basadas en Kubernetes y permite a los usuarios desplegar entidades emisoras de certificados (CA), clasificadores e iguales en x86, LinuxONE e IBM Z. {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private se basa en Hyperledger Fabric v1.2.1 y se despliega utilizando diagramas de Helm de Kubernetes. Después de instalar el diagrama de Helm, puede encontrarlo como un servicio empaquetado en el catálogo de {{site.data.keyword.cloud_notm}} Private. Tenga en cuenta que [esta oferta](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) es más adecuada para usuarios experimentados de Fabric.

Para obtener más información sobre {{site.data.keyword.cloud_notm}} Private, consulte la [documentación de {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} - Documentación de IBM Cloud privado v3.1.0").

El release de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private también marca el fin del programa de igual remoto de la plataforma {{site.data.keyword.blockchainfull_notm}} (Beta). Todavía es posible desplegar un igual en {{site.data.keyword.cloud_notm}} Private o en AWS y conectarlo a una red del Plan inicial o el Plan empresarial. Para obtener más información, consulte [cómo desplegar un igual en el Plan inicial o Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) y [cómo desplegar un igual en AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Estos iguales se consideran iguales **distribuidos** en lugar de iguales remotos, ya que el despliegue está gestionado por el cliente y, como consecuencia, no hay ninguna ubicación central desde la que haya otras remotas.

Este release también presenta algunas mejoras en la tabla de contenidos de la documentación. Si es usuario del Plan inicial o empresarial, el podrá seguir encontrando el contenido en las secciones **APRENDER**, **CÓMO**, **REFERENCIA** y **AYUDA**, y los enlaces serán los mismos. Simplemente podría estar en una subsección distinta de la tabla de contenidos.

## 13 de noviembre de 2018
{: #whats-new-11-13-2018}

{{site.data.keyword.blockchainfull_notm}} Platform publica {{site.data.keyword.blockchainfull_notm}} Platform para Amazon Web Services (AWS).

{{site.data.keyword.blockchainfull_notm}} Platform para AWS le permite ejecutar iguales en la nube de AWS y conectar los iguales a redes blockchain existentes en {{site.data.keyword.cloud_notm}}. Los iguales en AWS pueden hacer uso del perfil de conexión y de otros componentes de blockchain en las redes existentes, al tiempo que le ofrecen más flexibilidad para colocar los iguales con otras aplicaciones fuera de {{site.data.keyword.cloud_notm}}. Para obtener más información, consulte [Acerca de {{site.data.keyword.blockchainfull_notm}} Platform en Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 de octubre de 2018
{: #whats-new-10-04-2018}

{{site.data.keyword.blockchainfull_notm}} Platform actualiza el Plan inicial de Hyperledger Fabric v1.1.0 a v1.2.1. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Importante:** Fabric v1.2.1 no es compatible actualmente con la versión beta del igual remoto, que utiliza un igual de v1.1.0. Las redes del Plan inicial, creadas antes del 4 de octubre de 2018 y basadas en Fabric v1.1.0, no se ven afectadas y se pueden seguir utilizando con la versión beta del igual remoto. {{site.data.keyword.blockchainfull_notm}} Platform actualizará la versión beta del igual remoto para que utilice el igual de v1.2.1, que será compatible con las nuevas redes de Plan inicial, que tienen el nivel v1.2.1 de Fabric, y con las redes del Plan empresarial, que tienen el nivel v1.1.0 de Fabric. Hasta que se actualice la versión beta del igual remoto a Fabric v1.2.1, no intente desplegar un igual remoto de v1.1.0 con una red de Plan inicial de v1.2.1.

## 4 de septiembre de 2018
{: #whats-new-9-04-2018}

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

{{site.data.keyword.blockchainfull_notm}} Platform sustituye a {{site.data.keyword.blockchainfull_notm}} on Cloud y publica el Plan empresarial. Para obtener más información, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

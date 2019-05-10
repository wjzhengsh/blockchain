---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp}

{{site.data.keyword.blockchainfull}} Platform proporciona una oferta de blockchain-as-a-service (BaaS) gestionada y de pila completa que le permite desplegar componentes de blockchain en los entornos que elija. El entorno puede ser {{site.data.keyword.cloud_notm}}, local a través de {{site.data.keyword.cloud_notm}} Private y nubes de terceros, como Amazon Web Services (AWS). En esta guía de aprendizaje se describe el proceso general para configurar una red de blockchain básica con {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

**Importante:** antes de utilizar una oferta de {{site.data.keyword.blockchainfull_notm}}, debe leer la información técnica y de soporte de la sección [Descargo de responsabilidad](/docs/services/blockchain/needtoknow.html#disclaimer).


## Antes de empezar
{: #get-started-ibp-prereqs}

{{site.data.keyword.blockchainfull_notm}} Platform proporciona distintas ofertas para ayudar a todos los tipos de usuarios a iniciar su viaje en blockchain y mover sus aplicaciones a producción. Debe decidir el entorno y la oferta que va a utilizar. En la figura 1 se muestran las capacidades, las audiencias de destino, los precios y las plataformas de nube para distintas ofertas. Para obtener más información sobre cada oferta, pulse la oferta en la tabla siguiente.

| **Ofertas** | **Qué se incluye** | **Política de facturación** | **Plataforma de nube** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**{{site.data.keyword.blockchainfull_notm}} Platform Extension para VSCode**](/docs/services/blockchain?topic=blockchain-develop-vscode#develop-vscode) | Los desarrolladores pueden comenzar con el IDE que proporciona un explorador y mandatos accesibles desde la paleta de mandatos para desarrollar contratos inteligentes rápidamente. | Gratuito | Se ejecuta en la máquina local |
| [**Plan inicial**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | Red gestionada por {{site.data.keyword.IBM_notm}} con niveles básicos de servicio y entorno de desarrollo y prueba | Suscripción mensual | {{site.data.keyword.cloud_notm}} |
| [**Plan empresarial**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | Red gestionada por {{site.data.keyword.IBM_notm}} con niveles avanzados de servicio y entorno listo para producción de empresa | Suscripción mensual | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | Consola de {{site.data.keyword.blockchainfull_notm}} Platform para desplegar y gestionar componentes de blockchain en el clúster Kubernetes de {{site.data.keyword.cloud_notm}} | Gratuito para Beta | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | CA desplegable, clasificador y diagramas de Helm de igual | [Precios de VPC](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) y Community Edition gratuita | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform para AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | Plantilla de inicio rápido de AWS para desplegar iguales remotos que están fuera de {{site.data.keyword.cloud_notm}} | Gratuito | AWS |

*Figura 1. Ofertas de {{site.data.keyword.blockchainfull_notm}} Platform*

No utilice el Plan inicial ni la versión gratuita 2.0 beta para producción. Es un entorno de desarrollo y de prueba, y no es adecuado para las cargas de trabajo de producción.
{: important}

## Paso uno: obtener una oferta
{: #get-started-ibp-step1}

Asegúrese de que tiene la cuenta de nube o la licencia PPA para obtener una oferta de {{site.data.keyword.blockchainfull_notm}} Platform.

* **{{site.data.keyword.blockchainfull_notm}} Platform Extension para VSCode**

  Esta extensión de VSCode está disponible de forma gratuita en
[Visual Studio Marketplace ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform Extension para VSCode") y se puede utilizar para desarrollar, depurar y probar contratos inteligentes para su despliegue eventual en
{{site.data.keyword.blockchainfull_notm}}.

* **Plan inicial**, **Plan empresarial** y **{{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta**

  Estas ofertas están disponibles en {{site.data.keyword.cloud_notm}} y las encontrará en el [panel de control del catálogo ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog "Catálogo") de {{site.data.keyword.cloud_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private**

  Esta oferta se entrega como un diagrama de Helm desplegable y tiene tanto edición de pago como edición Community Edition gratuita. Puede descargar la Edición de empresa desde [Passport Advantage Online ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html) o puede descargar el [GitHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz).

* **{{site.data.keyword.blockchainfull_notm}} Platform para AWS**

  Esta oferta está disponible en AWS y puede desplegar un igual de blockchain en AWS utilizando la [plantilla de inicio rápido ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Paso dos: desplegar {{site.data.keyword.blockchainfull_notm}} Platform
{: #get-started-ibp-step2}

* **Plan inicial**, **Plan empresarial** y **{{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta**

  Inicie la sesión en {{site.data.keyword.cloud_notm}} y cree una instancia de servicio con la oferta. Si utiliza el Plan inicial, puede obtener una red de blockchain con la configuración predeterminada inmediatamente después de [crear la instancia de servicio](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan). Si utiliza el plan empresarial o {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta, tiene que seguir al asistente para completar la configuración inicial de la red. Para obtener más información, consulte [Creación de una red del plan empresarial](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) o [Despliegue de {{site.data.keyword.blockchainfull_notm}} Platform en el servicio Kubernetes de {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

* **{{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private**

  Antes de desplegar una red, tiene que [importar el diagrama de Helm en su {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). A continuación, puede [desplegar los componentes de red a partir de una CA](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#step-three-set-up-your-cas) para crear la red.

* **{{site.data.keyword.blockchainfull_notm}} Platform para AWS**

  Esta oferta está disponible en AWS y puede desplegar un igual de blockchain en AWS utilizando la [plantilla de inicio rápido ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Siguientes pasos
{: #get-started-ibp-next-steps}

Después de desplegar {{site.data.keyword.blockchainfull_notm}} Platform en el entorno que elija, puede configurar la red con consorcio, nodos, canales y contratos inteligentes e iniciar las transacciones en la misma. Para obtener más información, consulte los temas de la tarea bajo la sección **CÓMO SE HACE** en el panel de navegación de la izquierda de esta documentación.

## Obtención de soporte
{: #get-started-ibp-getting-support}

{{site.data.keyword.IBM_notm}} ofrece diversas opciones de soporte en las soluciones de blockchain implementadas por {{site.data.keyword.IBM_notm}}. Para obtener más información sobre el soporte de {{site.data.keyword.blockchainfull_notm}} Platform, consulte [Obtención de soporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

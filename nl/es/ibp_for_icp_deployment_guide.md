---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: root CA, network components, ICP deployment guide, getting started tutorial, IBM Cloud Private

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

# Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #get-started-icp}

{{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private permite a los usuarios desplegar entidades emisoras de certificados (CA), clasificadores e iguales en x86, LinuxONE e IBM Z mediante diagramas de Helm de Kubernetes en un entorno local, como {{site.data.keyword.cloud_notm}} Private, y conectarlos a componentes alojados en varios entornos de nube. {{site.data.keyword.cloud_notm}} Private es una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas y locales. Se trata de un entorno integrado para gestionar contenedores que incluyen el coordinador de contenedores Kubernetes, un registro de imágenes privadas, una consola de gestión e infraestructuras de supervisión.
{:shortdesc}

Las redes blockchain que se basan en [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/) se pueden desplegar en una matriz de configuraciones casi ilimitada para dar soporte a un gran número de casos de uso. A pesar de esta maleabilidad, existe una serie de métodos recomendados, especialmente en torno a la **secuencia** correcta para configurar y desplegar componentes de red.

En esta guía de despliegue se muestra la secuencia adecuada para configurar una red funcional de {{site.data.keyword.blockchainfull}} Platform en {{site.data.keyword.cloud_notm}} Private, así como los métodos recomendados y puntos a tener en cuenta durante el despliegue. No obstante, se siguen aplicando las reglas básicas si tiene pensado configurar únicamente una CA, un clasificador o un igual funcional. Tenga en cuenta que el servicio de ordenación SOLO, que despliega un único nodo de clasificador, no está pensado para entornos de producción. Las redes o canales que ejecuten SOLO no se podrán considerar como un entorno de "producción". Sin embargo, es posible desplegar el igual y la CA en un entorno de producción, especialmente cuando tienen alta disponibilidad.

El proceso para desplegar {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private es difícil y requiere un alto nivel de experiencia en Fabric. Si es un nuevo usuario de Fabric, de {{site.data.keyword.blockchainfull_notm}} Platform o de {{site.data.keyword.cloud_notm}} Private y su objetivo es configurar un entorno de desarrollo o una prueba de concepto, plantéese utilizar en su lugar el [Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about). Tenga también en cuenta que no se admiten todas las configuraciones de despliegue potenciales. Debido a la dificultad, se presupone que será un experto en Fabric quien llevará a cabo el proceso de despliegue y de conexión de los componentes antes de dejar las tareas administrativas en manos de terceros. Estos expertos son el público objetivo de esta guía, así como de la documentación para los componentes de diagrama de Helm en general.
{:important}

## Paso uno: decidir la configuración de la red
{: #get-started-icp-step-one-decide-network-config}

La estructura de una red blockchain debe estar determinada por el caso de uso. Estas decisiones empresariales fundamentales variarán en distintas situaciones, pero vamos a considerar algunas de ellas.

* Configuración de un **entorno de desarrollo**

  Este es el caso para el que se ha creado el [Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about), pero si desea gestionar sus propios componentes en un entorno de desarrollo, necesitará una configuración básica, que consta de un clasificador y un igual (con una CA para cada organización). Para cada organización, puede optar por tener un solo clasificador y un solo igual, similar a la configuración de la [red fabcar ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/understand_fabcar_network.html). Del mismo modo, podría decidir que solo necesita un único usuario administrador para todos estos componentes, en lugar de crear usuarios independientes para cada componentes.

* Despliegue de **componentes de producción** para una red nueva o una red existente

  Los componentes de producción y las redes de producción tienen necesidades diferentes que los entornos de desarrollo o pruebas de concepto. Por nombrar alguna, la alta disponibilidad pasa a ser una prioridad. Por definición, un servicio de ordenación SOLO, que incluye únicamente un clasificador individual (y, por lo tanto, es un punto único de anomalía), no está pensado para producción.

Esta guía de despliegue no trata cada iteración y cada configuración de red potencial, sino que proporciona reglas y directrices comunes a tener en cuenta.
{:note}

## Paso dos: configurar un clúster de Kubernetes en {{site.data.keyword.cloud_notm}} Private
{: #get-started-icp-step-two-set-up-k8s-on-icp}

Después de decidir la estructura de la red, configure un clúster de Kubernetes en {{site.data.keyword.cloud_notm}} Private para ajustarse a los casos de uso. Para obtener más información, consulte [Configuración de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

También puede utilizar {{site.data.keyword.IBM_notm}} Secure Service Container como host para {{site.data.keyword.cloud_notm}} Private para aprovechar las ventajas de la seguridad de Secure Service Container para proteger datos críticos de amenazas internas y externas. Para obtener más información, consulte [Utilización de {{site.data.keyword.IBM_notm}} Secure Service Container para {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/ibp-ssc-for-icp.html "Utilización de {{site.data.keyword.IBM_notm}} Secure Service Container para {{site.data.keyword.cloud_notm}} Private").
{:note}

## Paso tres: configurar las CA
{: #get-started-icp-step-three-set-up-cas}

En redes blockchain basadas en Fabric, el primer componente que se debe desplegar es una entidad emisora de certificados (CA). Esto se debe a que la configuración de un componente debe incluir al menos una identidad de usuario con autorización para trabajar con él antes del despliegue del componente.

A veces, esto puede ser difícil de entender conceptualmente. Con frecuencia, cualquiera puede acceder a un dispositivo electrónico como, por ejemplo, un portátil, cuando es nuevo. Se presupone que la primera persona que enciende el dispositivo es el "propietario" y, por lo tanto, tiene permitido registrar y establecer contraseñas que cada usuario posterior debe conocer. No obstante, no es así como se configuran los componentes en las redes blockchain basadas en Fabric. Los iguales y los clasificadores deben tener información administrativa incorporada cuando se desplieguen por primera vez.

**Se recomienda desplegar una CA por organización.** Por ejemplo, si despliega tres iguales que están asociados a una organización y un clasificador asociado a otra organización, necesitará desplegar dos CA. Una CA es para la organización de los iguales y la otra es para la organización del clasificador.

Para evitar una regresión infinita de CA (en la que cada CA debe estar enlazada con otra CA, y así infinitamente), las CA tienen un par de nombre de usuario y contraseña predeterminado asociado a ellas. Especifique este nombre de usuario y contraseña cuando se despliegue la CA. Esta CA es la "CA raíz", la raíz de confianza del componente. En entornos de producción, se recomienda desplegar al menos otra CA que obtenga su certificado de esta CA raíz y utilizar dicha CA, conocida como "CA intermedia", para emitir certificados a usuarios y componentes. Sin embargo, en este release, no se da soporte al uso de CA intermedias.

Cada organización debe tener una CA para la inscripción y una CA de TLS.  Al desplegar una CA en {{site.data.keyword.cloud_notm}} Private, también se despliega una CA de TLS en el mismo contenedor de forma predeterminada. Esta CA de TLS genera y gestiona los certificados TLS. La CA en una red de Plan inicial o de Plan empresarial no contiene una CA de TLS, pero sí que contiene una CA que se utiliza para la inscripción. Por lo tanto, si tiene pensado conectar el igual a una red de Plan inicial o Plan empresarial, antes de desplegar el igual, también debe [desplegar una CA en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy), que servirá como CA de TLS para dicho igual. Consulte también los [requisitos previos para conectar un igual de {{site.data.keyword.cloud_notm}} Private a {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-prerequisites) Platform. Tenga en cuenta que la CA de TLS solo se utiliza para emitir certificados, y se puede concluir cuando termine dicha actividad.

Para obtener más información sobre TLS, consulte [Protección de la comunicación con la seguridad de la capa de transporte (TLS) ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/enable_tls.html "Protección de la comunicación con la seguridad de la capa de transporte (TLS)") en la documentación de Hyperledger Fabric.

### Preparación de los MSP para el clasificador y los iguales
{: #get-started-icp-prepare-msp-orderer-peer}

Debido a que el proceso de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private es tan elaborado, se recomienda utilizar una única identidad de usuario administrador como administrador para todos los nodos de componentes de red durante la configuración inicial. Esto debe mitigar los errores de despliegue y de conexión al asegurar que un usuario pueda definir la configuración y configurar las conexiones entre diversos componentes para asegurarse de que funcionan correctamente. No obstante, es extremadamente importante que cada componente tenga certificados distintos. En ocasiones, es fácil pasar por alto esta distinción. La entidad que firma una propuesta de transacción no es el administrador del igual, sino el **propio igual**. Por lo tanto, el igual debe inscribirse y tener un certificado que adjuntar para cualquier acción que lleve a cabo, así como una clave privada que se puede utilizar para generar determinados tipos de firmas. Para obtener más información sobre las identidades y permisos en una red blockchain basada en Fabric, consulte [Identidad ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "Identidad") y [Pertenencia ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Pertenencia") en la documentación de Fabric.

El cliente de CA de Fabric, que se debe instalar siguiendo las instrucciones de [Funcionamiento de una CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client), se utiliza para registrar e inscribir identidades. Esta identidad es el administrador de los componentes de red que vaya a desplegar y le permite cambiar configuraciones y conectar componentes entre sí. Si desea transferir la administración de estos componentes a otros usuarios más adelante, puede registrar e inscribir nuevos administradores para estos componentes y eliminarse a sí mismo como administrador si fuera necesario.

Cuando se despliega el clasificador o el igual, un contenedor `init` que está asociado con el clasificador o el igual utilizará un objeto de secreto de Kubernetes para crear el MSP para el componente. Para obtener información sobre cómo crear un objeto de secreto, consulte [Funcionamiento de una CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate). Como se ha dicho anteriormente, recuerde que necesita configurar una CA y repetir este flujo para cada organización.

## Paso cuatro: desplegar los clasificadores e iguales
{: #get-started-icp-step-four-deploy-order-peer}

Una vez que se haya creado un secreto de Kubernetes, estará listo para desplegar un componente. Si su plan es establecer canales, se recomienda que despliegue el clasificador antes que los iguales. Asegúrese de utilizar nombres de organización distintos para todos los componentes.

- **[Despliegue el clasificador](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)**. Tenga en cuenta que, a día de hoy, solo se admite el servicio de ordenación SOLO. Tiene opciones en el proceso de despliegue relacionadas con el cálculo.

- **[Despliegue el igual que se vaya a conectar a un clasificador de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy)**. Hay una serie de opciones para desplegar el igual, incluyendo el tipo de base de datos, que se pueden especificar en el diagrama de Helm. Asegúrese de que el ID de MSP de organización del igual sea distinto al ID de MSP de organización del clasificador.

- **[Despliegue el igual que se vaya a conectar a una red de {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) Platform**. El proceso para desplegar un igual y conectarlo a una red de [Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about) o de [Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise_plan-about) en {{site.data.keyword.cloud_notm}} es distinto, ya que utiliza perfiles de conexión y la interfaz de usuario del supervisor de red. Tenga en cuenta que la organización a la que pertenece el igual ya se debe haber unido a un canal de la red. Una vez más, asegúrese de que el ID de MSP de organización del igual sea distinto al ID de MSP de organización del clasificador.

## Siguientes pasos
{: #get-started-icp-next-steps}

Después de desplegar todos los nodos, puede empezar a trabajar con ellos y a enviar transacciones. Para obtener más información, consulte los enlaces siguientes:

- [Funcionamiento de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate)
- [Funcionamiento de un clasificador en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate)
- [funcionamiento de iguales en {{site.data.keyword.cloud_notm}} Private con una red multinube](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)
- [Funcionamiento de iguales en {{site.data.keyword.cloud_notm}} Private con el plan inicial o el plan empresarial](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate)

## Cómo expandir su red
{: #get-started-icp-grow-network}

Si su objetivo es configurar un entorno de desarrollo o prueba de concepto, deberá añadir organizaciones de igual al canal del sistema del clasificador que se crea durante del despliegue del clasificador. Este es un proceso de varios pasos que utiliza cada componente y que está documentado en los temas operativos relevantes.

1. Para poder añadir una organización, deberá crearla y configurar su ID de MSP. Consulte [Funcionamiento de una CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer).

2. Una vez que se haya creado la organización, se puede añadir al canal del sistema del clasificador. Para obtener más información, consulte [Funcionamiento de un clasificador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium).

3. Cuando una organización aparece en el canal del sistema del clasificador, es miembro del "consorcio" y tiene permitido crear un canal de aplicación, el tipo de canal en el que se realizan las transacciones. Para obtener información sobre cómo crear un canal, consulte [Creación de un canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel).

## Referencias
{: #get-started-icp-ref}

- [Guía de aprendizaje: Cómo operar un igual distribuido en {{site.data.keyword.blockchainfull_notm}} Platform](https://developer.ibm.com/tutorials/operate-distributed-peer-on-ibm-blockchain-platform/)
- [Página inicial de {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/cloud/private "{{site.data.keyword.cloud_notm}} Página inicial de Private")
- [Documentación de {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/kc_welcome_containers.html)

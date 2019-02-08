---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Novedades
{: #whatsnew}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 7 de diciembre de 2018

Los temas *Operación de la red del Plan inicial* y *Operación de la red del Plan empresarial* se han combinado y sustituido por una guía de aprendizaje individual sobre la [Utilización del supervisor de red](/docs/services/blockchain/v10_dashboard.html).

## 27 de noviembre de 2018

La plataforma {{site.data.keyword.blockchainfull}} publica la plataforma {{site.data.keyword.blockchainfull_notm}} para
{{site.data.keyword.cloud_notm}} privado (ICP). ICP es una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas basadas en Kubernetes y permite a los usuarios desplegar entidades emisoras de certificados (CA), clasificadores e iguales en x86, LinuxONE e IBM Z.
La plataforma {{site.data.keyword.blockchainfull_notm}} para ICP se basa en Hyperledger Fabric v1.2.1 y se despliega utilizando diagramas de Helm de Kubernetes. Después de instalar el diagrama de Helm, puede encontrarlo como un servicio empaquetado en el catálogo de ICP. Tenga en cuenta que
[esta oferta](/docs/services/blockchain/ibp-for-icp-about.html) es más adecuada para usuarios experimentados de Fabric.

Para obtener más información sobre ICP, consulte la
[documentación de {{site.data.keyword.cloud_notm}} privado v3.1.0
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} - Documentación de IBM Cloud privado v3.1.0").

El release de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP también marca el fin del programa de igual remoto de la plataforma IBM Blockchain (Beta). Todavía es posible desplegar un igual en ICP o en AWS y conectarlo a una red del Plan inicial o el Plan empresarial. Para obtener más información, consulte [cómo desplegar un igual en el Plan inicial o Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html) y
[cómo desplegar un igual en AWS](/docs/services/blockchain/howto/remote_peer_aws.html). Estos iguales se consideran iguales **distribuidos** en lugar de iguales remotos, ya que el despliegue está gestionado por el cliente y, como consecuencia, no hay ninguna ubicación central desde la que haya otras remotas.

Este release también presenta algunas mejoras en la tabla de contenidos de la documentación. Si es usuario del Plan inicial o empresarial, el podrá seguir encontrando el contenido en las secciones **APRENDER**, **CÓMO**, **REFERENCIA** y **AYUDA**, y los enlaces serán los mismos. Simplemente podría estar en una subsección distinta de la tabla de contenidos.

## 13 de noviembre de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} publica la plataforma {{site.data.keyword.blockchainfull_notm}} para Amazon Web Services (AWS).

La plataforma {{site.data.keyword.blockchainfull_notm}} para AWS le permite ejecutar iguales en la nube de AWS y conectar los iguales a redes blockchain existentes en {{site.data.keyword.cloud_notm}}. Los iguales en AWS pueden hacer uso del perfil de conexión y de otros componentes de blockchain en las redes existentes, al tiempo que le ofrecen más flexibilidad para colocar los iguales con otras aplicaciones fuera de {{site.data.keyword.cloud_notm}}. Para obtener más información, consulte [Acerca de la plataforma {{site.data.keyword.blockchainfull_notm}} en Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html).

## 4 de octubre de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} actualiza el Plan inicial de Hyperledger Fabric v1.1.0 a v1.2.1. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html).

**Importante:** Fabric v1.2.1 no es compatible actualmente con la versión beta del igual remoto, que utiliza un igual de v1.1.0. Las redes del Plan inicial, creadas antes del 4 de octubre de 2018 y basadas en Fabric v1.1.0, no se ven afectadas y se pueden seguir utilizando con la versión beta del igual remoto. La plataforma {{site.data.keyword.blockchainfull_notm}} actualizará la versión beta del igual remoto para que utilice el igual de v1.2.1, que será compatible con las nuevas redes de Plan inicial, que tienen el nivel v1.2.1 de Fabric, y con las redes del Plan empresarial, que tienen el nivel v1.1.0 de Fabric. Hasta que se actualice la versión beta del igual remoto a Fabric v1.2.1, no intente desplegar un igual remoto de v1.1.0 con una red de Plan inicial de v1.2.1.

## 4 de septiembre de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} publica la versión beta de la oferta del igual remoto. La oferta del igual remoto se basa en Hyperledger Fabric v1.1.0. Al utilizar el igual remoto, puede ejecutar nodos de igual de la plataforma {{site.data.keyword.blockchainfull_notm}} en su propio entorno de nube de IBM Cloud privado (ICP) o de Amazon Web Services (AWS). Para obtener más información, consulte [Acerca de los iguales remotos](/docs/services/blockchain/howto/remote_peer.html).

## 15 de junio de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} publica el Plan inicial GA. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html).

## 15 de mayo de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} actualiza el Plan empresarial de Hyperledger Fabric v1.0.0 a v1.1.0. Para obtener más información, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html).

## 18 de marzo de 2018

La plataforma {{site.data.keyword.blockchainfull_notm}} publica el Plan inicial (Beta). El Plan inicial le ofrece un entorno de desarrollo y pruebas para aprender y practicar en una red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Para obtener más información, consulte [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html).

## 11 de agosto de 2017

La plataforma {{site.data.keyword.blockchainfull_notm}} sustituye
{{site.data.keyword.blockchainfull_notm}} en la nube y publica el Plan empresarial. Para obtener más información, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html).

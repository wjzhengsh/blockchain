---

copyright:
  years: 2019
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
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# Preguntas más frecuentes
{: #ibp-v2-faq}

## ¿Qué versión de Hyperledger Fabric se está utilizando con {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta?
{: #ibp-v2-faq-fabric-version}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta utiliza Hyperledger Fabric v1.4 e incluye soporte para el protocolo gossip, el descubrimiento de servicios y los datos privados.

## ¿Tengo que pagar algo por {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta?
{: #ibp-v2-faq-cost}
{: faq}

El release beta es gratuito. Pero es posible que incurra en cargos por el clúster de Kubernetes de {{site.data.keyword.cloud_notm}}, a menos que haya seleccionado un clúster gratuito.  Sin embargo, tenga en cuenta que el clúster gratuito se suprime automáticamente transcurridos 30 días por política del servicio Kubernetes de {{site.data.keyword.cloud_notm}} y que el rendimiento y la capacidad están limitados.  Si desea conservar el servicio transcurridos los 30 días, deberá pagar una instancia estándar del servicio Kubernetes. 

## ¿Puedo utilizar mi clúster existentes del servicio Kubernetes de {{site.data.keyword.cloud_notm}}?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Su clúster de Kubernetes funcionará con {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta siempre que cumpla las siguientes condiciones:
- Esté en la región Dallas de {{site.data.keyword.cloud_notm}}.
- Ejecute Kubernetes v1.11 o posterior. Consulte estas instrucciones para obtener información sobre cómo [actualizar la versión de Kubernetes de un clúster](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- Haya suficientes recursos disponibles en el clúster. Consulte este tema sobre
[requisitos mínimos de recursos](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required) para obtener más información.

## ¿Qué base de datos utilizan los iguales para su libro mayor?
{: #ibp-v2-faq-couchDB}
{: faq}

Todos los iguales que se despliegan con {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta utilizan la base de datos CouchDB. Sin embargo, CouchDB no se puede consultar directamente.

## ¿Qué lenguajes se admiten para los contratos inteligentes?
{: #ibp-v2-faq-cc-langs}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform admite contratos inteligentes escritos en Go, Node.js y Java. El nuevo modelo de programación de Hyperledger Fabric actualmente solo admite Node.js, y próximamente admitirá más. Si está interesado en conservar el código de aplicación existente o en utilizar los SDK de Fabric para lenguajes distintos de Node.js, puede conectarse a la red de {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta utilizando la API de SDK de Fabric de bajo nivel.

## ¿Puedo migrar desde la versión V2.0 beta a la versión v2.0 GA? 
{: #ibp-v2-faq-migrate}
{: faq}

Los clientes del Plan empresarial podrán migrar a IBM Blockchain Platform 2.0 cuando esté disponible a nivel general (GA). Sin embargo, no podrá migrar la oferta 2.0 beta a la oferta 2.0 GA. Tampoco podrá migrar una instancia del Plan inicial a la versión 2.0 beta ni a la versión 2.0 GA del producto cuando esté disponible.

## ¿Tenemos acceso a servicios de registro y qué registros tengo disponibles?
{: #ibp-v2-faq-logs}
{: faq}

Con {{site.data.keyword.blockchainfull_notm}} Platform 2.0, ahora puede acceder directamente a los registros del igual, de la CA y del clasificador desde el panel de control de Kubernetes. Le recomendamos que aproveche el servicio [{{site.data.keyword.cloud_notm}} LogDNA ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Log Analysis con LogDNA"), que le permite analizar fácilmente los registros en tiempo real.

## ¿Cuál es la ventaja de utilizar {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta sobre Hyperledger Fabric nativo?
{: #ibp-v2-faq-native-fabric}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta permite a los clientes desplegar fácilmente una red blockchain personalizada. La plataforma incluye una consola intuitiva para el despliegue rápido de una red y la capacidad de instalar fácilmente y de crear una instancia de los contratos inteligentes. Con la plataforma también recibe soporte de expertos de {{site.data.keyword.IBM_notm}} que le ayudarán a desarrollar su contrato inteligente y a crear la red.

## ¿Cómo puedo maximizar el rendimiento y escalar mi solución {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-perf-scale}
{: faq}

Consulte esta [serie de blogs ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Respuesta a sus preguntas sobre rendimiento y escalado de Hyperledger Fabric") para ver los factores que afectan al rendimiento de la solución e información sobre cómo escalar blockchain.

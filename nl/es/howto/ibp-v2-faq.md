---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: FAQs, can I, upgrade, what version, peer ledger database, supported languages, why do I, regions

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

- [¿Qué base de datos utilizan los iguales para su libro mayor?](#ibp-v2-faq-v2-IBP-Overview-1-3)
- [¿Qué lenguajes se admiten para los contratos inteligentes?](#ibp-v2-faq-v2-IBP-Overview-1-4)
- [¿Puedo actualizar a partir de V1.0 a la nueva versión de {{site.data.keyword.blockchainfull_notm}} Platform? ](#ibp-v2-faq-v2-IBP-Overview-1-5)
- [¿Cuál es la ventaja de utilizar {{site.data.keyword.blockchainfull_notm}} Platform sobre Hyperledger Fabric nativo?](#ibp-v2-faq-v2-IBP-Overview-1-7)
- [¿Qué ocurrirá cuando suprima mi servicio de {{site.data.keyword.blockchainfull_notm}} Platform? ](#ibp-v2-faq-v2-IBP-Overview-1-8)
- [¿Qué regiones están disponibles para el servicio de blockchain que se ejecuta en {{site.data.keyword.cloud_notm}}? ](#ibp-v2-faq-v2-IBP-Overview-1-9)
- [¿Qué versión de Hyperledger Fabric se está utilizando con {{site.data.keyword.blockchainfull_notm}} Platform?](#ibp-v2-faq-v2-Hyperledger-Fabric-3-1)
- [¿Puedo utilizar mi clúster existente del servicio Kubernetes de {{site.data.keyword.cloud_notm}}?](#ibp-v2-faq-v2-Infrastructure-4-2)
- [¿Tenemos acceso a servicios de registro y qué registros tengo disponibles?](#ibp-v2-faq-v2-Logging-and-Monitoring-11-6)


## ¿Qué base de datos utilizan los iguales para su libro mayor?
{: #ibp-v2-faq-v2-IBP-Overview-1-3}
{: faq}

Todos los iguales que se despliegan con {{site.data.keyword.blockchainfull_notm}} Platform utilizan CouchDB como base de datos para el libro mayor.

## ¿Qué lenguajes se admiten para los contratos inteligentes?
{: #ibp-v2-faq-v2-IBP-Overview-1-4}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform admite contratos inteligentes escritos en Go y Node.js. El nuevo modelo de programación de Hyperledger Fabric solo tiene soporte actualmente para Node.js, pero próximamente se incluirán más. Si está interesado en conservar su código de aplicación existente o en utilizar los SDK de Fabric para otros lenguajes que no sean Node.js, podrá seguir conectándose a la red de {{site.data.keyword.blockchainfull_notm}} Platform utilizando las API del SDK de Fabric de un nivel más bajo.

## ¿Puedo actualizar a partir de V1.0 a la nueva versión de {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-5}
{: faq}

No para el Plan inicial. No puede actualizar del Plan inicial a la nueva versión de {{site.data.keyword.blockchainfull_notm}} Platform.
Para el Plan empresarial, podrá actualizar a la nueva versión de {{site.data.keyword.blockchainfull_notm}} Platform en el futuro. El propietario de la cuenta recibirá un correo electrónico del equipo de {{site.data.keyword.blockchainfull_notm}} Platform con instrucciones de ayuda.

## ¿Cuál es la ventaja de utilizar {{site.data.keyword.blockchainfull_notm}} Platform sobre Hyperledger Fabric nativo?
{: #ibp-v2-faq-v2-IBP-Overview-1-7}
{: faq}

{{site.data.keyword.blockchainfull_notm}} Platform permite que los clientes puedan desplegar con facilidad una red blockchain personalizada. Puede utilizar una interfaz de usuario de consola intuitiva para desplegar la red, instalar e instanciar contratos inteligentes con facilidad y supervisar las transacciones.

## ¿Qué ocurrirá cuando suprima mi servicio de {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-IBP-Overview-1-8}
{: faq}

Al suprimir una instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform, solo se suprimirá la consola. No se suprimirá ninguno de los componentes que se hayan creado con la consola ni su almacenamiento asociado. El clúster Kubernetes y los componentes importados tampoco se suprimirán. Seguirá asumiendo el coste de sus nodos de Kubernetes y del almacenamiento hasta que los suprima usted mismo utilizando el panel de control de Kubernetes o los mandatos de CLI.

## ¿Qué regiones están disponibles para el servicio de blockchain que se ejecuta en {{site.data.keyword.cloud_notm}}?
{: #ibp-v2-faq-v2-IBP-Overview-1-9}
{: faq}

Las regiones disponibles para {{site.data.keyword.blockchainfull_notm}} Platform aparecen listadas en [Ubicaciones de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain?topic=blockchain-ibp-regions-locations). Tenga en cuenta que debe crear un clúster de servicio Kubernetes de {{site.data.keyword.cloud_notm}} en la misma región que el servicio de blockchain para reconcer el clúster. Pronto estarán disponibles regiones adicionales.

## ¿Qué versión de Hyperledger Fabric se está utilizando con {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-v2-Hyperledger-Fabric-3-1}
{: faq}

A partir de febrero de 2019, se ha puesto en marcha la beta utilizando Hyperledger Fabric 1.4.0.
A partir del 29 de mayo de 2019, se ha presentado la versión de disponibilidad general (GA) utilizando Hyperledger Fabric 1.4.1. Hyperledger Fabric 1.4.0 no se admite en el producto GA.

## ¿Puedo utilizar mi clúster existente del servicio Kubernetes de {{site.data.keyword.cloud_notm}}?
{: #ibp-v2-faq-v2-Infrastructure-4-2}
{: faq}

Su clúster de Kubernetes funcionará con {{site.data.keyword.blockchainfull_notm}} Platform siempre que cumpla las siguientes condiciones:
- Ejecuta Kubernetes v1.11 o una versión estable superior.
- Haya suficientes recursos disponibles en el clúster.

## ¿Tenemos acceso a servicios de registro y qué registros tengo disponibles?
{: #ibp-v2-faq-v2-Logging-and-Monitoring-11-6}
{: faq}

Con {{site.data.keyword.blockchainfull_notm}} Platform, ahora puede acceder directamente a los registros del igual, de la CA y del clasificador desde el panel de control de Kubernetes. Se recomienda que haga uso del servicio LogDNA de {{site.data.keyword.cloud_notm}} que le permite analizar fácilmente los registros en tiempo real.

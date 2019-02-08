---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Aplicaciones de ejemplo

Puede desplegar y probar aplicaciones de ejemplo para conocer mejor el despliegue de aplicaciones y red de {{site.data.keyword.blockchainfull}}.
{:shortdesc}

## Antes de empezar

Asegúrese de instalar todos los requisitos previos de software en el sistema de archivos local.  Para más información, consulte [Configuración del entorno de desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html#setting-up-application-development-environment).

También debe tener configurada una red de {{site.data.keyword.blockchain}} on {{site.data.keyword.Bluemix_short}} con un canal y sus iguales.  Para obtener más información, consulte [Gobierno de red](/docs/services/blockchain/get_start.html).  Cuando la red esté lista para utilizarse, recupere los puntos finales de API de los recursos de red a los que accederá la aplicación.  Para obtener más información, consulte [Adición de credenciales de servicio de red a la aplicación](/docs/services/blockchain/v10_application.html#adding-network-service-credentials-to-your-application).


## Aplicación Marbles

En la aplicación Marbles, varios usuarios pueden crear canicas con diferentes propiedades y transferirlas a otros.  La aplicación Marbles se escribe en Javascript y el código de encadenamiento se escribe en Go.

Puede consultar código de ejemplo y las instrucciones en [Marbles en GitHub ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/IBM-Blockchain/marbles).


## Aplicación Fabric car

En la aplicación Fabric car, puede realizar **consultas** y **actualizaciones** de registros de coches en el libro mayor.  La aplicación Fabric car se escribe en Javascript y el código de encadenamiento se escribe Go.

Puede consultar el código de ejemplo de [Fabric car en GitHub ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) y las instrucciones sobre [cómo escribir su primer aplicación ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->

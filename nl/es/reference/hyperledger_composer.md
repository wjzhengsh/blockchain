---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Composer
{: #hyperledger-composer}

**IBM recomienda el uso de Hyperledger Composer únicamente para demostraciones y pruebas de concepto. IBM no proporciona soporte para redes que utilicen Hyperledger Composer en producción, incluyendo la CLI de Composer, las API de JavaScript, el servidor REST y Web Playground.**

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform: Develop se basa en el conjunto de herramientas de código abierto Hyperledger Composer, uno de los proyectos de Hyperledger Project de Linux Foundation. Hyperledger Composer permite a los arquitectos y desarrolladores crear rápidamente soluciones {{site.data.keyword.blockchain}} con las API REST que exponen la lógica empresarial en aplicaciones web o móviles, además de integrar blockchain con los sistemas de registros existentes de la empresa.
{:shortdesc}

Hyperledger Composer se compone de un entorno de ejecución, una interfaz de línea de mandatos, un servidor REST, un conector LoopBack, una interfaz de usuario de recinto de pruebas, un generador de código Yeoman y los plug-ins de editor de Atom y VSCode. Para obtener más información sobre Hyperledger Composer, consulte la [documentación de Hyperledger Composer ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)


## Redes empresariales

Hyperledger Composer le permite modelar rápidamente su red empresarial actual, que contiene los activos existentes y las transacciones relacionadas con los mismos; los activos son bienes, servicios o propiedades tangibles o intangibles. Como parte de su modelo de red empresarial, defina las transacciones que pueden interactuar con activos. Las redes empresariales también incluyen los participantes que interactúan con las mismas, cada uno de los cuales se puede asociar a una identidad exclusiva, entre varias redes empresariales.

Para obtener más información sobre cómo crear redes empresariales, consulte [Desarrollo de redes empresariales](../develop.html) o la [documentación de Hyperledger Composer ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).

## Consultas

Las consultas se utilizan para devolver datos sobre el estado general de blockchain. Las consultas se definen dentro de una red empresarial y pueden incluir parámetros variables para facilitar su personalización. Mediante las consultas se pueden extraer fácilmente datos de la red de {{site.data.keyword.blockchain}}. Las consultas se envían mediante la [API de Hyperledger Composer ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/api/api-doc-index).

Para obtener más información sobre cómo utilizar consultas en la red empresarial, consulte [Consulta y filtrado de datos de la red empresarial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/tutorials/queries).

## Identidades y tarjetas de red empresarial

En una red empresarial, los participantes están asociadas con una identidad. Las tarjetas de red empresarial son una combinación de una identidad, un perfil de conexión y metadatos, y se utilizan para conectar con una red empresarial y demostrar la identidad para las transacciones. Las tarjetas de red empresarial simplifican el proceso de conectarse a una red empresarial y amplían el concepto de identidad, más allá de la red empresarial, a una 'cartera' de identidades, cada una de ellas asociada a una red empresarial específica y a un perfil de conexión.

Para obtener más información sobre las identidades y las tarjetas de red empresarial, consulte [Participantes e identidades ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/managing/participantsandidentities).

## Servidor REST de Hyperledger Composer REST

El servidor REST de Hyperledger Composer genera automáticamente una API REST de Swagger para una red empresarial. El servidor REST se basa en LoopBack y convierte el modelo de red empresarial en una definición de Open API. En el momento de la ejecución, el servidor REST implementa el soporte para las funciones Crear, Leer, Actualizar y Suprimir para activos y participantes y permite enviar transacciones para que se procesen o se recuperen con consultas.

Para obtener más información sobre el servidor REST de Hyperledger Composer, consulte [Generación de una API REST ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).

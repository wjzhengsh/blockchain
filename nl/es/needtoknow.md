---

copyright:
  years: 2017
lastupdated: "2017-07-19"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Descargo de responsabilidad
{: #etn_overview}

**ATENCIÓN:** Debe revisar la información siguiente antes de utilizar un plan de {{site.data.keyword.blockchainfull}}.

## Declaración de soporte de IBM

La larga historia de liderazgo en innovación de IBM continúa con los planes de la oferta {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}}. {{site.data.keyword.blockchain}} es una tecnología de progreso rápido proyectada para interrumpir la industria financiera, las cadenas de suministro locales y globales y el soporte logístico en cualquier número de espacios empresariales. Mediante varios programas de adopción anteriores, los clientes y los socios empresariales de IBM han estado gestionando una estructura de Blockchain como solución empresarial. {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} es uno de estos programas. **Igual que con cualquier nueva tecnología, los usuarios de {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} deben estar preparados para un cambio rápido y fundamental**.  
{:shortdesc}

La arquitectura que hay detrás de {{site.data.keyword.blockchainfull_notm}} es el proyecto Hyperledger Fabric de Linux Foundation. Cada contribución a la comunidad de código abierto mejora Hyperledger Fabric, pero esto puede representar retos de adopción. **IBM advierte sobre la definición o intercambio de activos financiero, o cualquier activo de valor, directamente en cualquier red de tipo Blockchain de Hyperledger Fabric. **.  

## Sentencia de código abierto

Los planes de la oferta {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} se han creado a partir de la pila de Hyperledger Fabric V1.0 de Linux Foundation. Los miembros de Hyperledger Project, incluido IBM, siguen realizando aportaciones a los varios subproyectos recogidos en Hyperledger.  La comunidad revisa, evalúa y prueba todas las aportaciones. 

Hyperledger Fabric anunció que había alcanzado el *estado de nivel de producción* de la versión 1.0 el 11 de julio de 2017. 

## Sentencia de soporte de chaincode

Las siguientes prácticas de codificación NO reciben soporte en las redes de {{site.data.keyword.blockchainfull_notm}}:

1. Uso de matrices asociativas con iteración (el orden es aleatorio en Go).
2. Lectura de una lista de elementos desde una tabla KVS (el orden no está garantizado).
3. Grabación del código de encadenamiento de hebras no seguras (se pueden invocar la consulta y la invocación en paralelo).
4. Sustitución de la memoria global o del almacenamiento de memoria caché para variables de estado de libro mayor en el código de encadenamiento.
5. Acceso a servicios externos, como por ejemplo bases de datos, directamente desde el código de encadenamiento.
6. Uso de bibliotecas o de variables globales que podrían introducir el no determinismo (como utilizar "random" o "time").  

Además, no se recomienda escribir código de encadenamiento no determinista, lo que representa un riesgo en la coherencia e integridad de los datos.  Cabe señalar que la arquitectura de Hyperledger Fabric está diseñada para contrarrestar el código de encadenamiento no determinista a través de una serie de comprobaciones de aprobación y validación; sin embargo, se sigue recomendando encarecidamente la codificación de funciones deterministas que no sean dependientes de variables globales no estáticas (por ejemplo, "time").  

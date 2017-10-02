---

copyright:
  years: 2017
lastupdated: "2017-09-20"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Plan Empresarial de {{site.data.keyword.blockchainfull_notm}} Platform

El Plan Empresarial de {{site.data.keyword.blockchainfull}} Platform es la primera opción de pertenencia disponible para las organizaciones que quieran crear una red empresarial blockchain o unirse a una. Este plan proporciona la infraestructura clave junto con las herramientas y el soporte para desplegar con facilidad una red blockchain altamente segura lista para la producción.

Para los miembros que van a iniciar la red, IBM proporciona una interfaz gráfica de usuario para guiar al iniciador de la red a través de los pasos clave para establecer y suministrar la red. Ello incluye invitar a otros miembros y definir las reglas de gobierno. Una vez desplegada la red, hay disponible una interfaz gráfica de usuario interactiva para supervisar la actividad y el estado de la red; gestionar las actividades de red clave como nuevos despliegues de red, adición/eliminación de miembros y gestión tanto del ciclo de vida de encadenamiento como del canal; y buscar soporte técnico. Obtenga más información sobre cómo obtener [soporte](ibmblockchain_support.html).

{{site.data.keyword.blockchainfull_notm}} Platform se crea con componentes clave de Hyperledger Fabric, incluyendo una entidad emisora de certificados (CA) y al menos 1 igual (máx. de 6).  IBM también proporciona un servicio de ordenación Kafka tolerante a errores de caída (CFT) para los miembros de la red. 

Fabric CA es la entidad emisora de certificados que se proporciona con el plan Empresarial. Se suministran dos CA intermedias por miembro, que otorgan pertenencia a la red. Mediante la CA, el miembro también puede proporcionar pertenencias (certificados) a los usuarios finales de la red.

Es importante comprender que, en la adición de una transacción al libro mayor, se producen tres fases:  
1. Simulación de transacción y aprobación (igual)
2. Ordenación (servicio de ordenación)
3. Validación y confirmación (igual)

Los iguales de Fabric propiedad de los miembros son la interfaz o pasarela para las aplicaciones que ejecutan el código de encadenamiento y, con ello, proporcionan la lógica empresarial para la ejecución de transacciones del libro mayor.  Deben aprobarse todas las transacciones. Los otros miembros de la red llevan a cabo esta aprobación. Tras la aprobación, las transacciones se envían a un servicio de ordenación de IBM (Kafka).

Además de los componentes principales de blockchain, la opción de pertenencia Empresarial proporciona una infraestructura con almacenamiento de datos y comunicaciones seguras (TLS) así como alta disponibilidad.  Aunque las redes de Fabric comparten estos recursos de infraestructura, se proporciona aislamiento en los nodos de los componentes de Fabric de una red y cada nodo se ejecuta en un contenedor Docker seguro que protege el entorno de ejecución.

El único aspecto que se debe determinar es el tamaño de los iguales necesarios para la red. Esta decisión se basa en el número de canales necesarios, además de la carga por canal, el uso de memoria y el espacio en disco (almacenamiento). A continuación, encontrará los tamaños disponibles con el plan de servicio Empresarial y orientación para la elección del igual adecuado.

Debería utilizarse la plataforma IBM Blockchain para despliegues más estables, a nivel de producción o de casi producción. Para finalidades de pruebas, utilice el servicio IBM Container o imágenes descargables locales.

El plan Empresarial proporciona el servicio de ordenación y CA. La tarifa de miembro es de 1.000 $ y una tarifa por igual de 1.000 $ que está asociada a la red. Si desea disponer de alta disponibilidad (HA), debe adquirir un igual adicional para proporcionar capacidades de HA. Por ejemplo, una organización (tarifa de miembro asociada de 1.000 $) de dos iguales (1.000 $ X 2 iguales) con HA (1.000 $ X 2 iguales HA) requiere un cargo mensual de 5.000 $.

Regístrese ahora para ser [miembro de {{site.data.keyword.blockchainfull_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

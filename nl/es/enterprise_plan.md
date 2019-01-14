---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Acerca del Plan empresarial


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


El Plan empresarial de la plataforma {{site.data.keyword.blockchainfull}} es una oferta lista para producción disponible para las organizaciones que quieran crear una red empresarial blockchain o unirse a una. Este plan proporciona la infraestructura clave junto con las herramientas y el soporte necesarios para iniciar con facilidad una red muy segura y lista para producción. El Plan empresarial se ha actualizado de Hyperledger Fabric V1.0 a V1.1 el 15 de mayo de 2018. Todas las redes creadas después del 15 de mayo de 2018 están en el nivel de Fabric V1.1. Sin embargo, las redes creadas anteriormente permanecerán en el nivel V1.0 de fábrica.
{:shortdesc}

**Notas:**
- El Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}} proporciona un entorno de producción. Si necesita un entorno de desarrollo y pruebas, consulte [Acerca del Plan inicial](starter_plan.html).
- La plataforma {{site.data.keyword.blockchainfull_notm}} es un servicio de plataforma de {{site.data.keyword.cloud_notm}} y todas las ofertas pertenecientes siguen
los [{{site.data.keyword.cloud_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "términos de servicio de {{site.data.keyword.cloud_notm}}") de los contratos de nivel de servicio (SLA). Las redes del plan de empresa se suministran en **varios entornos** en centros de datos separados geográficamente.

Para los miembros que van a iniciar la red, IBM proporciona una interfaz gráfica de usuario para guiar al iniciador de la red a través de los pasos clave para establecer y suministrar la red. Ello incluye invitar a otros miembros y definir las reglas de gobierno. Para obtener más información, consulte [Gobierno de la red del Plan empresarial](get_start.html). Una vez desplegada la red, dispondrá de una interfaz gráfica de usuario, el supervisor de red, para supervisar la actividad y el estado de la red, gestionar las actividades de red clave (que incluyen nuevos despliegues, adición o eliminación de miembros, ciclo de vida del código de encadenamiento y gestión de canales) y buscar soporte técnico. Para obtener más información, consulte [Utilización del supervisor de red](v10_dashboard.html).

Regístrese ahora para ser [miembro de {{site.data.keyword.blockchainfull_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

La plataforma {{site.data.keyword.blockchainfull_notm}} se crea con componentes clave de Hyperledger Fabric, que incluyen una entidad emisora de certificados (CA) y al menos 1 igual (con un máximo de 6).  El Plan empresarial también proporciona un servicio de ordenación Kafka tolerante a errores de caída (CFT) para los miembros de la red.

La CA de Fabric es la entidad emisora de certificados que se proporciona con el Plan empresarial. Se suministran dos CA intermedias por miembro, que otorgan pertenencia a la red. Mediante la CA, el miembro también puede proporcionar suscripciones (certificados) a los usuarios de la red.

Es importante comprender que en el proceso de adición de una transacción al libro mayor intervienen tres fases:  
1. Simulación de transacción y aprobación (igual)
2. Ordenación (servicio de ordenación)
3. Validación y confirmación (igual)

Los iguales de Fabric propiedad de los miembros son la interfaz o pasarela para las aplicaciones que ejecutan el código de encadenamiento y, con ello, proporcionan la lógica empresarial para la ejecución de transacciones del libro mayor.  Deben aprobarse todas las transacciones. Los otros miembros de la red llevan a cabo esta aprobación. Tras la aprobación, las transacciones se envían a un servicio de ordenación de IBM (Kafka).

Además de los componentes principales de blockchain, la opción de pertenencia Empresarial proporciona una infraestructura con almacenamiento de datos y comunicaciones seguras (TLS) así como alta disponibilidad.  Aunque las redes de Fabric comparten estos recursos de infraestructura, se proporciona aislamiento en los nodos de los componentes de Fabric de una red y cada nodo se ejecuta en un contenedor de Docker seguro que protege el entorno de ejecución.

El único aspecto que se debe determinar es el tamaño de los iguales que necesita la red. Esta decisión se basa en el número de canales necesarios, además de la carga por canal, el uso de memoria y el espacio en disco (almacenamiento).

Debe utilizar el Plan empresarial para despliegues más estables, de producción o casi de nivel de producción. Para realizar pruebas, utilice el [Plan inicial](starter_plan.html), [realice el desarrollo en IBM Container Service](https://ibm-blockchain.github.io/) o [instale imágenes de Docker localmente](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Precios  
Para utilizar el Plan empresarial, los miembros de la red deben pagar 1.000 $ al mes como cuota de suscripción y otros 1.000 $ al mes por cada uno de los iguales de la red.  Las cuotas mensuales se facturan prorrateadas por día.  Por ejemplo, un miembro (cuota de suscripción asociada de 1.000 $) con dos iguales (la cuota por igual es de 1.000 $ X 2 iguales) pagaría 3.000 $ al mes.  Si el mes tiene 30 días, el miembro paga 100 $ (3.000 $/30) al día.  Tenga en cuenta que, si necesita alta disponibilidad (HA), debe doblar el número de iguales necesarios para disponer de las funciones de HA.

Los miembros de la red pueden abonar su factura con sus propias cuentas de {{site.data.keyword.cloud_notm}} que contienen el espacio para crear la instancia de la red. Si lo desea, un miembro de la red puede hacerse cargo de la factura de todos los miembros de la red. Para obtener más información sobre cómo abonar las redes blockchain, consulte el apartado [Pago de la red](howto/paying_mode.html).

---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Acerca del Plan inicial
{: #starter-plan-about}

El Plan inicial de {{site.data.keyword.blockchainfull}} Platform es una opción de nivel de entrada que permite a las organizaciones simular redes de blockchain de varias organizaciones, desarrollar aplicaciones rápidamente y trabajar con contratos digitales de ejemplo y redes empresariales. También ofrece la misma experiencia de interfaz de usuario que otras opciones de suscripción, lo que ayuda a eliminar la curva de aprendizaje. Las nuevas redes del Plan inicial creadas después del 4 de octubre de 2018 están basadas en Hyperledger Fabric V1.2.1. Las redes del Plan inicial más antiguas siguen teniendo el nivel de Fabric V1.1.0.
{:shortdesc}

El **Plan inicial** es un entorno para aquellos que desean empezar a desarrollar redes de blockchain o empezar a aprender sobre la tecnología de blockchain. Puede utilizar el Plan inicial como un entorno de desarrollo o de prueba que le permitirá comenzar con un entorno pequeño y escalar la pertenencia o el volumen de transacciones de la red antes de pasar a un entorno de producción.

 Para desplegar una red del Plan inicial, suscríbase como [miembro del plan inicial ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) en {{site.data.keyword.cloud_notm}}. Cuando esté preparado para empezar a desarrollar su red, visite el tema sobre [iniciación al Plan inicial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).


**Notas:**
- El Plan inicial de {{site.data.keyword.blockchainfull_notm}} Platform es un entorno de desarrollo y de prueba, y no es adecuado para las cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
- {{site.data.keyword.blockchainfull_notm}} Platform es un servicio de plataforma de {{site.data.keyword.cloud_notm}} y todas las ofertas de pertenencia a grupo siguen las [condiciones de los servicios de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "condiciones de servicios de {{site.data.keyword.cloud_notm}}") de los acuerdos de nivel de servicio (SLA). Las redes de plan inicial se suministran a través de **varios entornos** en centros de datos separados geográficamente.

## Qué ofrece el Plan inicial
{: #starter-plan-about-what-starter-plan-offers}

- **_Red lista con una pulsación_**
El Plan inicial (Starter Plan) le suministra una red blockchain activa con una simple pulsación. Cada red se proporciona con una CA, un clasificador, dos organizaciones (con un igual por organización), y un canal predeterminado en el que realizar transacciones y desplegar el código de encadenamiento. {{site.data.keyword.blockchainfull_notm}} Platform gestiona la creación y la configuración de esta red (podrá actualizarla cuando la utilice en un entorno activo), permitiéndole centrarse en el desarrollo. Las redes del Plan inicial se crean en Fabric V1.2 y utilizan CouchDB como base de datos de estado.
- **_Eficiencia de coste_**
La opción de pertenencia al Plan inicial ofrece muchas de las funciones de blockchain que ofrecen las opciones de pertenencia al Plan empresarial, pero a un coste menor. Para obtener más información, consulte [Precios del Plan inicial](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing)
- **_Simulación de red de varias organizaciones_**
Puede utilizar el Plan inicial para simular la creación de una red con varias organizaciones. No es necesario que invite realmente a otras organizaciones a su red, ya que puede actuar usted mismo como otras organizaciones. Este mecanismo le permite aprender cómo se puede unir a la red una nueva organización, cómo funcionan juntas en la red varias organizaciones, etc. Puede cambiar entre organizaciones desde el supervisor de red para ver y gestionar la red desde la vista de diferentes organizaciones.

{{site.data.keyword.IBM_notm}} no proporciona soporte para redes que utilicen Hyperledger Composer en producción, incluyendo la CLI de Composer, las API de JavaScript, el servidor REST y Web Playground.
{:note}

## ¿Resulta adecuado para usted el plan inicial?
{: #starter-plan-about-is-starter-suitable-for-you}

Si encaja en una de las siguientes situaciones, el Plan inicial resulta adecuado para iniciar su andadura en blockchain.
- **_Aprendizaje de la plataforma {{site.data.keyword.blockchainfull_notm}}._**
    Si siente curiosidad sobre {{site.data.keyword.blockchainfull_notm}} Platform o si nunca ha utilizado blockchain, el Plan inicial constituye una excelente opción para aprender a desarrollar y a gobernar una red blockchain real. Descubrirá los componentes de los que consta una red, a gestionar la suscripción, aprenderá a desplegar y a gestionar código de encadenamiento (también denominado "contratos digitales"), aprenderá a añadir canales (y a gestionar permisos de canales) y realizará un seguimiento de la generación de un nuevo bloque, igual que si estuviera en una red de producción.
- **_Creación de soluciones de demostración en una red activa._**
    El Plan inicial proporciona un potente entorno para demostrar las aplicaciones de blockchain. La red blockchain lista para ser utilizada permite realizar presentaciones rápidas a compañeros, directores y socios mediante las herramientas operativas y de gestión que ofrece el supervisor de red.
- **_Traslado más allá del desarrollo de organización único._**
    El Plan inicial le permite actuar como varias organizaciones, lo que le permite ver cómo gestiona {{site.data.keyword.blockchainfull_notm}} Platform las tareas de colaboración, como la creación de un canal y la creación de instancias del código de encadenamiento, así como la prueba de aplicaciones y la invocación de transacciones. También puede invitar a otros a colaborar en una red del Plan inicial (como en los Planes empresariales). Esto le ayuda a crear en un entorno más realista, con varias partes aprobando código de encadenamiento y transacciones.
- **_Desarrollo iterativo y prueba de aplicaciones de blockchain._**
    El Plan inicial le ofrece un área de transferencia para desarrollar y probar continuamente su código en una red blockchain. El traslado a la nube permite a los desarrolladores y verificadores centrarse en las funciones y pasar rápidamente de una prueba de unidad a una prueba funcional. El Plan inicial proporciona las mismas funciones de red blockchain y herramientas operativas y de gestión que el Plan empresarial. Cuando esté listo para desplegar el proyecto en uno de los planes empresariales, podrá trabajar de la misma manera que con el Plan inicial, pero con más oportunidades para hacer crecer su red.

## Consideraciones sobre el Plan inicial
{: #starter-plan-about-considerations}

El Plan inicial constituye un punto de entrada en {{site.data.keyword.blockchainfull_notm}} Platform y es para propósito de desarrollo y de prueba.  Consulte los elementos siguientes antes de utilizar el Plan inicial.

- **Requisito de cuenta de {{site.data.keyword.cloud_notm}}**
Debe tener una cuenta de pago de {{site.data.keyword.cloud_notm}}, por ejemplo, un tipo de **Pago según uso**. Todos los planes de pertenencia que ofrece {{site.data.keyword.blockchainfull_notm}} Platform requieren una cuenta de pago de {{site.data.keyword.cloud_notm}}. Para actualizar su cuenta a un tipo Pago según uso, vaya a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}}, y pulse **Añadir tarjeta de crédito**.
- **Diferencias del Plan empresarial**
    - [CA](/docs/services/blockchain/glossary.html#glossary-CA) y el servicio de ordenación no son tolerantes a errores porque cada organización tiene solo una CA y una red solo tiene un [clasificador](/docs/services/blockchain/glossary.html#glossary-orderer).
    - El servicio de ordenación solo utiliza el [consenso](/docs/services/blockchain/glossary.html#glossary-consensus) [SOLO](/docs/services/blockchain/glossary.html#glossary-solo). Una red del Plan inicial solo consta de un clasificador que realiza el consenso para todos los iguales.
    - El [Módulo de seguridad de hardware (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) no está disponible para proteger y gestionar las claves digitales para la autenticación segura y el proceso de cifrado.
- **Versiones del Plan inicial y actualización**
    - Las nuevas redes del Plan inicial creadas después del 4 de octubre de 2018 están basadas en Hyperledger Fabric V1.2.1. Las redes del Plan inicial más antiguas siguen teniendo el nivel de Fabric V1.1.0.
    - Los iguales nuevos que se añadan a versiones anteriores de redes del Plan inicial se crearán en Fabric v1.2.1. El rendimiento de la red no se ve afectado debido a la compatibilidad con versiones anteriores.
    - Tiene la opción de utilizar valores más avanzados de
[configuración de canal
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Configuración de canal") y [listas de control de acceso
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-v1.2/access_control.html "Listas de control de acceso") al crear o actualizar un canal.
    - Las características de [descubrimiento de servicios ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descubrimiento de servicios") y de [datos privados ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "datos privados") de Hyperledger Fabric v1.2 no reciben soporte en el Plan inicial.
    - Si [restablece](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network) una red más antigua del Plan inicial que esté en Fabric V1.1.0, la nueva red estará en el nivel V1.2 de Fabric. Si restablece la red, deberá instalar el código de encadenamiento o los archivos .bna en la nueva red, así como volver a invitar a los miembros de su anterior red.
- **Limitación de recursos de red**
El Plan inicial asigna 1 CPU y 4 Gi de RAM a cada igual, y 20 Gi de almacenamiento para cada instancia de servicio de {{site.data.keyword.cloud_notm}}. Los contenedores de código de encadenamiento y los bloques del libro mayor son los componentes de red que más recursos utilizan. Los usuarios que tienen muchos iguales en su red, que generan muchos bloques o que utilizan archivos de código de gran tamaño, pueden experimentar el efecto de las limitaciones de recursos en el rendimiento. Puede ver el uso de almacenamiento de sus redes en la [pantalla "Visión general" del supervisor de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage).
- **Mantenimiento y actualización**
    El mantenimiento del Plan inicial y las actualizaciones de red se realizan según una planificación fija. Durante el periodo de mantenimiento, no puede proporcionar nuevas redes y es posible que observe breves periodos de interrupción de la red.
- **Retención de datos**
El Plan inicial no garantiza la retención de datos con actualizaciones de release.
- **Consideraciones sobre migración**
La migración de los datos desde una red de Plan inicial a otros planes de suscripción no está soportada en este momento. Sin embargo, se pueden migrar archivos `.bna`, código de encadenamiento y aplicaciones que se han probado en el Plan inicial. Para obtener más información, consulte [Migración del Plan inicial al Plan empresarial](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise).

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

# Acerca del Plan inicial
{: #overview}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


El Plan inicial de la plataforma de {{site.data.keyword.blockchainfull}} es una opción de nivel de entrada que permite a las organizaciones simular redes de blockchain de varias organizaciones, desarrollar aplicaciones rápidamente y trabajar con contratos digitales de ejemplo y redes empresariales. También ofrece la misma experiencia de interfaz de usuario que otras opciones de suscripción, lo que ayuda a eliminar la curva de aprendizaje. Las nuevas redes del Plan inicial creadas después del 4 de octubre de 2018 están basadas en Hyperledger Fabric V1.2.1. Las redes del Plan inicial más antiguas siguen teniendo el nivel de Fabric V1.1.0.
{:shortdesc}

**Notas:**
- El Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}} es un entorno de desarrollo y de prueba, y no es adecuado para las cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](enterprise_plan.html). Puede comprobar las [consideraciones del Plan inicial](#considerations) antes de utilizar el Plan inicial.
- La plataforma {{site.data.keyword.blockchainfull_notm}} es un servicio de plataforma de {{site.data.keyword.cloud_notm}} y todas las ofertas pertenecientes siguen
los [{{site.data.keyword.cloud_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "términos de servicio de {{site.data.keyword.cloud_notm}}") de los contratos de nivel de servicio (SLA). Las redes de plan inicial se suministran a través de **varios entornos** en centros de datos separados geográficamente.

## Qué ofrece el Plan inicial

- **_Red lista con una pulsación_**
El Plan inicial (Starter Plan) le suministra una red blockchain activa con una simple pulsación. Cada red se proporciona con una CA, un clasificador, dos organizaciones (con un igual por organización), y un canal predeterminado en el que realizar transacciones y desplegar el código de encadenamiento. La plataforma de {{site.data.keyword.blockchainfull_notm}} gestiona la creación y la configuración de esta red (podrá actualizarla cuando la utilice en un entorno activo), permitiéndole centrarse en el desarrollo. Las redes del Plan inicial se crean en Fabric V1.2 y utilizan CouchDB como base de datos de estado. <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiencia de coste_**
La opción de pertenencia al Plan inicial ofrece muchas de las funciones de blockchain que ofrecen las opciones de pertenencia al Plan empresarial, pero a un coste menor. La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece 500 dólares de créditos de nube a los usuarios nuevos del plan inicial. Estos créditos le permiten suministrar una red blockchain con recursos de red básicos de forma gratuita durante un mes. Consulte las [consideraciones del plan inicial](#starter-plan-considerations) para ver más detalles.
- **_Simulación de red de varias organizaciones_**
Puede utilizar el Plan inicial para simular la creación de una red con varias organizaciones. No es necesario que invite realmente a otras organizaciones a su red, ya que puede actuar usted mismo como otras organizaciones. Este mecanismo le permite aprender cómo se puede unir a la red una nueva organización, cómo funcionan juntas en la red varias organizaciones, etc. Puede cambiar entre organizaciones desde el supervisor de red para ver y gestionar la red desde la vista de diferentes organizaciones.
    **Nota**: Puede provocar un coste adicional la adición de más recursos que los básicos de prueba gratuitos de dos organizaciones y dos iguales.
<!-- - **_Easy to deploy sample applications_**
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_Integración con aplicaciones de la plataforma {{site.data.keyword.blockchainfull_notm}}: Develop_**
El Plan inicial le permite desplegar redes empresariales que ha desarrollado en la plataforma [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Para obtener más información, consulte [Despliegue de una red empresarial en el Plan inicial](develop_starter.html).

## ¿Resulta adecuado para usted el plan inicial?

Si encaja en una de las siguientes situaciones, el Plan inicial resulta adecuado para iniciar su andadura en blockchain.
- **_Aprendizaje de la plataforma {{site.data.keyword.blockchainfull_notm}}._**
    Si siente curiosidad sobre la plataforma {{site.data.keyword.blockchainfull_notm}} o si nunca ha utilizado blockchain, el Plan inicial constituye una excelente opción para aprender a desarrollar y a gobernar una red blockchain real. Descubrirá los componentes de los que consta una red, a gestionar la suscripción, aprenderá a desplegar y a gestionar código de encadenamiento (también denominado "contratos digitales"), aprenderá a añadir canales (y a gestionar permisos de canales) y realizará un seguimiento de la generación de un nuevo bloque, igual que si estuviera en una red de producción.
- **_Creación de soluciones de demostración en una red activa._**
    El Plan inicial proporciona un potente entorno para demostrar las aplicaciones de blockchain. La red blockchain lista para ser utilizada permite realizar presentaciones rápidas a compañeros, directores y socios mediante las herramientas operativas y de gestión que ofrece el supervisor de red.
- **_Traslado más allá del desarrollo de organización único._**
    El Plan inicial le permite actuar como varias organizaciones, lo que le permite ver cómo gestiona la plataforma {{site.data.keyword.blockchainfull_notm}} las tareas de colaboración, como la creación de un canal y la creación de instancias del código de encadenamiento, así como la prueba de aplicaciones y la invocación de transacciones. También puede invitar a otros a colaborar en una red del Plan inicial (como en los Planes empresariales). Esto le ayuda a crear en un entorno más realista, con varias partes aprobando código de encadenamiento y transacciones.
- **_Desarrollo iterativo y prueba de aplicaciones de blockchain._**
    El Plan inicial le ofrece un área de transferencia para desarrollar y probar continuamente su código en una red blockchain. El traslado a la nube permite a los desarrolladores y verificadores centrarse en las funciones y pasar rápidamente de una prueba de unidad a una prueba funcional. El Plan inicial proporciona las mismas funciones de red blockchain y herramientas operativas y de gestión que el Plan empresarial. Cuando esté listo para desplegar el proyecto en uno de los planes empresariales, podrá trabajar de la misma manera que con el Plan inicial, pero con más oportunidades para hacer crecer su red.


## Consideraciones sobre el Plan inicial
{: #considerations}

El Plan inicial constituye un punto de entrada en la plataforma {{site.data.keyword.blockchainfull_notm}} y es para propósito de desarrollo y de prueba.  Consulte los elementos siguientes antes de utilizar el Plan inicial.

- **Requisito de cuenta de {{site.data.keyword.cloud_notm}}**
Debe tener una cuenta de pago de {{site.data.keyword.cloud_notm}}, por ejemplo, un tipo de **Pago según uso**. Todos los planes de pertenencia que ofrece la plataforma de {{site.data.keyword.blockchainfull_notm}} requieren una cuenta de pago de {{site.data.keyword.cloud_notm}}. Para actualizar su cuenta a un tipo Pago según uso, vaya a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}}, y pulse **Añadir tarjeta de crédito**.
- **Créditos de nube de la plataforma {{site.data.keyword.blockchainfull_notm}}**
La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece 500 dólares de créditos de nube a los usuarios nuevos del Plan inicial. Estos créditos ayudan a los usuarios a empezar a cubrir el coste de una configuración de red predeterminada durante un mes.
    - Los nuevos usuarios tienen que registrarse para obtener créditos en la nube. No se aplican automáticamente. [Regístrese ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "registrarse") para reclamar los créditos si aún no lo ha hecho. Espere 24 horas para asegurarse de que los créditos estén en su [cuenta ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "cuenta") antes de acceder al plan inicial. De lo contrario, es posible que se le facture antes de que se apliquen los créditos.
    - Los créditos de nube se aplican a todos los servicios de {{site.data.keyword.cloud_notm}}. Se pueden consumir en productos que no sean la plataforma {{site.data.keyword.blockchainfull_notm}} que utiliza en {{site.data.keyword.cloud_notm}}.
    - La oferta proporciona créditos de por vida. Puede conservar sus créditos mientras mantenga su cuenta de {{site.data.keyword.cloud_notm}}. No es necesario que los utilice durante el primer mes.
    Obtenga más información sobre los créditos de prueba en la [guía de precios](howto/pricing.html#starter-plan-pricing).
- **Diferencias del Plan empresarial**
    - La [CA](glossary.html#ca) y el [servicio de ordenación](glossary.html#orderer) no son tolerantes a errores porque cada organización solo tiene una CA y una red solo tiene un [clasificador](glossary.html#orderer).
    - El servicio de ordenación solo utiliza el [consenso](glossary.html#consensus) [SOLO](glossary.html#solo). Una red del Plan inicial solo consta de un [clasificador](glossary.html#orderer) que realiza el consenso para todos los iguales.
    - El [Módulo de seguridad de hardware (HSM)](glossary.html#hsm) no está disponible para proteger y gestionar las claves digitales para la autenticación segura y el proceso de cifrado.
- **Versiones del Plan inicial y actualización**
    - Las nuevas redes del Plan inicial creadas después del 4 de octubre de 2018 están basadas en Hyperledger Fabric V1.2.1. Las redes del Plan inicial más antiguas siguen teniendo el nivel de Fabric V1.1.0.
    - Los iguales nuevos que se añadan a versiones anteriores de redes del Plan inicial se crearán en Fabric v1.2.1. El rendimiento de la red no se ve afectado debido a la compatibilidad con versiones anteriores.
    - Puede utilizar la característica de [datos privados
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "datos privados") de Hyperledger Fabric v1.2 en redes nuevas del Plan inicial. Para obtener más información sobre cómo utilizar los datos privados con el código de encadenamiento, consulte
[Datos privados](howto/develop_chaincode.html#private-data).
    - Tiene la opción de utilizar valores más avanzados de
[configuración de canal
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "Configuración de canal") y [listas de control de acceso
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/access_control.html "Listas de control de acceso") al crear o actualizar un canal.
    - La característica [Descubrimiento de servicios
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "Descubrimiento de servicios") de Hyperledger Fabric v1.2 no se admite en {{site.data.keyword.blockchainfull_notm}}.
    - Si [restablece](v10_dashboard.html#reset-network) una red más antigua del Plan inicial que esté en Fabric V1.1.0, la nueva red estará en el nivel V1.2 de Fabric. Si restablece la red, deberá instalar el código de encadenamiento o los archivos .bna en la nueva red, así como volver a invitar a los miembros de su anterior red.
- **Limitación de recursos de red**
El Plan inicial asigna 1 CPU y 4 Gi de RAM a cada igual, y 20 Gi de almacenamiento para cada instancia de servicio de {{site.data.keyword.cloud_notm}}. Los contenedores de código de encadenamiento y los bloques del libro mayor son los componentes de red que más recursos utilizan. Los usuarios que tienen muchos iguales en su red, que generan muchos bloques o que utilizan archivos de código de gran tamaño, pueden experimentar el efecto de las limitaciones de recursos en el rendimiento. Puede ver el uso de almacenamiento de sus redes en la [pantalla "Visión general" del supervisor de red](v10_dashboard.html#storage).
- **Mantenimiento y actualización**
    El mantenimiento del Plan inicial y las actualizaciones de red se realizan según una planificación fija. Durante el periodo de mantenimiento, no puede proporcionar nuevas redes y es posible que observe breves periodos de interrupción de la red.
- **Retención de datos**
El Plan inicial no garantiza la retención de datos con actualizaciones de release.
- **Consideraciones sobre migración**
La migración de los datos desde una red de Plan inicial a otros planes de suscripción no está soportada en este momento. Sin embargo, se pueden migrar archivos `.bna`, código de encadenamiento y aplicaciones que se han probado en el Plan inicial. Para obtener más información, consulte [Migración del Plan inicial al Plan empresarial](howto/migrate_sp_ep.html).


<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](v10_application.html#adding-network-api-endpoints-to-your-application).
-->

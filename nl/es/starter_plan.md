---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Acerca del Plan inicial
{: #overview}

El Plan inicial de la plataforma de {{site.data.keyword.blockchainfull}} es una opción de nivel de entrada que permite a las organizaciones simular redes de blockchain de varias organizaciones, desarrollar aplicaciones rápidamente y trabajar con contratos digitales de ejemplo y redes empresariales. También ofrece la misma experiencia de interfaz de usuario que otras opciones de suscripción, lo que ayuda a eliminar la curva de aprendizaje. Las redes del Plan inicial se basan en Hyperledger Fabric V1.1.
{:shortdesc}

**Notas**:
1. El Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}} es un entorno de desarrollo y de prueba, y no es adecuado para las cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](enterprise_plan.html). Puede comprobar las [consideraciones del Plan inicial](#considerations) antes de utilizar el Plan inicial.
2. El Plan inicial se traslada de Beta a GA el 14 de junio de 2018. Si tiene redes Beta, puede leer las [consideraciones de migración](#beta-to-ga).

Regístrese para la [suscripción a {{site.data.keyword.blockchainfull_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) y pruebe el Plan inicial con los [créditos de prueba](howto/pricing.html#starter-plan-pricing) ahora. <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## Lo que ofrece el Plan inicial

- **_Red lista con una pulsación_**  
    El Plan inicial (Starter Plan) le suministra una red blockchain activa con una simple pulsación. Cada red se proporciona con un servicio de ordenación, CA, dos organizaciones (con un igual por organización), y un canal predeterminado en el que transaccionar y desplegar el código de encadenamiento. La plataforma de {{site.data.keyword.blockchainfull_notm}} gestiona la creación y la configuración de esta red (podrá actualizarla cuando la utilice en un entorno activo), permitiéndole centrarse en el desarrollo. Las redes del Plan inicial se crean en Hyperledger Fabric V1.1, y utilizan CouchDB como base de datos de libro mayor. <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiencia de coste_**  
    La opción de pertenencia al Plan inicial ofrece muchas de las funciones de blockchain que ofrecen las opciones de pertenencia al Plan empresarial, pero a un coste menor. Durante un periodo de prueba del Plan inicial, puede proporcionar una red blockchain con recursos de red básicos de forma gratuita.
- **_Simulación de red de varias organizaciones_**  
    Puede utilizar el Plan inicial para simular la creación de una red con varias organizaciones. No es necesario que invite realmente a otras organizaciones a su red, ya que puede actuar usted mismo como otras organizaciones. Este mecanismo le permite aprender cómo se puede unir a la red una nueva organización, cómo funcionan juntas en la red varias organizaciones, etc. Puede cambiar entre organizaciones desde el supervisor de red para ver y gestionar la red desde la vista de diferentes organizaciones.
    **Nota**: Puede provocar un coste adicional la adición de más recursos que los básicos de prueba gratuitos de dos organizaciones y dos iguales.
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_Integración de la aplicación {{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    El Plan inicial le permite desplegar redes empresariales que ha desarrollado en [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Para obtener más información, consulte [Importación de aplicaciones IBP: Develop](link).

## Destinatarios

Si encaja en una de las siguientes situaciones, el Plan inicial resulta adecuado para iniciar su andadura en blockchain.
- **_Fase de aprendizaje de la plataforma {{site.data.keyword.blockchainfull_notm}}. _**  
    Si siente curiosidad sobre la plataforma {{site.data.keyword.blockchainfull_notm}} o si nunca ha utilizado blockchain, el Plan inicial constituye una excelente opción para aprender a desarrollar y a gobernar una red blockchain real. Descubrirá los componentes de los que consta una red, a gestionar la suscripción, aprenderá a desplegar y a gestionar código de encadenamiento (también denominado "contratos digitales"), aprenderá a añadir canales (y a gestionar permisos de canales) y realizará un seguimiento de la generación de un nuevo bloque, igual que si estuviera en una red de producción.
- **_Creación de soluciones de demostración en una red activa._**  
    El Plan inicial proporciona un potente entorno para demostrar las aplicaciones de blockchain. La red blockchain lista para ser utilizada permite realizar presentaciones rápidas a compañeros, directores y socios mediante las herramientas operativas y de gestión que ofrece el supervisor de red.
- **_Traslado más allá del desarrollo de organización único._**  
    El Plan inicial le permite actuar como varias organizaciones, lo que le permite ver cómo gestiona la plataforma de IBM Blockchain Platform tareas de colaboración como la creación de un canal y la creación de instancias del código de encadenamiento, así como la prueba de aplicaciones y la invocación de transacciones. También puede invitar a otros a colaborar en una red del Plan inicial (como en los Planes empresariales). Esto le ayuda a crear en un entorno más realista, con varias partes aprobando código de encadenamiento y transacciones.
- **_Desarrollo iterativo y prueba de aplicaciones de blockchain._**  
    El Plan inicial le ofrece un área de transferencia para desarrollar y probar continuamente su código en una red blockchain. El traslado a la nube permite a los desarrolladores y verificadores centrarse en las funciones y pasar rápidamente de una prueba de unidad a una prueba funcional. El Plan inicial proporciona las mismas funciones de red blockchain y herramientas operativas y de gestión que el Plan empresarial. Cuando esté listo para desplegar el proyecto en uno de los planes empresariales, podrá trabajar de la misma manera que con el Plan inicial, pero con más oportunidades para hacer crecer su red.


## Consideraciones sobre el Plan inicial
{: #considerations}

El Plan inicial constituye un punto de entrada en la plataforma {{site.data.keyword.blockchainfull_notm}} y es para propósito de desarrollo y de prueba.  Consulte los elementos siguientes antes de utilizar el Plan inicial.

- **Requisito de la cuenta de {{site.data.keyword.cloud_notm}}**  	
    Debe tener una cuenta de pago de {{site.data.keyword.cloud_notm}}, por ejemplo, un tipo de **Pago según uso**. Todos los planes de pertenencia que ofrece la plataforma de {{site.data.keyword.blockchainfull_notm}} requieren una cuenta de pago de {{site.data.keyword.cloud_notm}}. Para actualizar su cuenta a un tipo Pago según uso, vaya a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}}, y pulse **Añadir tarjeta de crédito**.  
- **Diferencias del Plan empresarial**
    - La [CA](glossary.html#ca) y el [servicio de ordenación](glossary.html#orderer) no son tolerantes a errores porque cada organización solo tiene una CA y una red solo tiene un [clasificador](glossary.html#orderer).
    - El servicio de ordenación solo utiliza el [consenso](glossary.html#consensus) [SOLO](glossary.html#SOLO). Una red del Plan inicial solo consta de un [clasificador](glossary.html#orderer) que realiza el consenso para todos los iguales.
    - El [Módulo de seguridad de hardware (HSM)](glossary.html#hsm) no está disponible para proteger y gestionar las claves digitales para la autenticación segura y el proceso de cifrado.
- **Limitación de recursos de red**  
    El Plan inicial asigna 1 CPU y 2 Gi de RAM para cada igual, y 20 Gi de almacenamiento para cada instancia de servicio de {{site.data.keyword.cloud_notm}}. 
- **Supresión de red inactiva**  
    Después de crear una red del Plan inicial, si no visita la red ni emite ninguna transacción en la misma durante 15 días, {{site.data.keyword.blockchainfull_notm}} Platform suprimirá la red.
    - Si ya no necesita la red, no tendrá que realizar ninguna acción y su red se suprimirá automáticamente.
    - Si sigue necesitando utilizar la red, puede mantenerla activa emitiendo transacciones en la misma. Puede encontrar la red del Plan inicial en el [Panel de control de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/apps/){:new_window}. Si no tiene transacciones regulares para emitir, siga las [instrucciones](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) para probar las aplicaciones de muestra.
- **Mantenimiento y actualización**
    El mantenimiento del Plan inicial y las actualizaciones de red se realizan según una planificación fija. Durante el periodo de mantenimiento, no puede proporcionar nuevas redes y es posible que observe breves periodos de interrupción de la red.
- **Retención de datos**
    El Plan inicial no garantiza la retención de datos con actualizaciones de release, incluido el paso de Beta a GA.
- **Consideraciones sobre migración**
    - La migración de los datos desde una red de Plan inicial a otros planes de suscripción no está soportada en este momento. Sin embargo, se pueden migrar archivos `.bna`, código de encadenamiento y aplicaciones que se han probado en el Plan inicial. Para obtener más información, consulte [Migración del Plan inicial al Plan empresarial](get_start_starter_plan.html#migrate).

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## Migración de Beta a GA
{: #beta-to-ga}

El Plan inicial se traslada a la etapa GA el 14 de junio de 2018. En GA, IBM Blockchain Platform ofrece 500 dólares de créditos de prueba para cada cuenta de IBM Cloud para crear redes de blockchain con el Plan inicial. Para obtener más información sobre los créditos de prueba, el Plan inicial se traslada a la etapa GA el 14 de junio de 2018. En GA, IBM Blockchain Platform ofrece 500 dólares de créditos de prueba para cada cuenta de IBM Cloud para crear redes de blockchain con el Plan inicial. Para obtener más información sobre los créditos de prueba, consulte la sección *Plan inicial de prueba* en [Tarifas del Plan inicial](howto/pricing.html#starter-plan-pricing). Asegúrese de tener una cuenta de pago de IBM Cloud, por ejemplo, un tipo **Pago según uso**.
. Asegúrese de tener una cuenta de pago de IBM Cloud, por ejemplo, un tipo **Pago según uso**.

Las redes de blockchain creadas con el Plan inicial Beta seguirán siendo **gratuitas** hasta que se supriman **30 días** después del GA del Plan inicial. La migración de datos no está soportada desde las redes del Plan inicial Beta a las redes de GA. **Los datos de las redes Beta se perderán.**  Sin embargo, puede migrar el código de encadenamiento, las redes empresariales y las aplicaciones manualmente.
- Si está ejecutando el código de encadenamiento en redes Beta, instale y cree instancias del código de encadenamiento en redes de GA. Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](howto/install_instantiate_chaincode.html).
- Si ha desplegado una red empresarial en redes Beta, despliegue la red empresarial con el archivo `.bna` en redes de GA. Para obtener más información, consulte [Despliegue de una red empresarial en el Plan inicial](develop_starter.html).
- Si ha ejecutado las aplicaciones autodesarrolladas en redes Beta, actualice los puntos finales de API de las aplicaciones para que apunten a los nodos de red de GA. Para obtener más información, consulte [Adición de puntos finales de API de red a la aplicación](v10_application.html#adding-network-api-endpoints-to-your-application).

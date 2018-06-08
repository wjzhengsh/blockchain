---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Acerca del Plan inicial
{: #overview}

El Plan inicial de la plataforma de {{site.data.keyword.blockchainfull}} es una opción de nivel de entrada que permite a las organizaciones simular redes de blockchain de varias organizaciones, desarrollar aplicaciones rápidamente y trabajar con los ejemplos proporcionados. También ofrece la misma experiencia de interfaz de usuario que otras opciones de suscripción, lo que ayuda a eliminar la curva de aprendizaje. Las redes del Plan inicial se basan en Hyperledger Fabric V1.1.
{:shortdesc}

**Nota**: El Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}} es un entorno de desarrollo y prueba, y no es adecuado para cargas de trabajo de producción. Si necesita un entorno de producción, consulte [Acerca del Plan empresarial](enterprise_plan.html). Puede comprobar las [consideraciones del Plan inicial](#considerations) antes de utilizar el Plan inicial.

Regístrese para convertirse en [miembro de {{site.data.keyword.blockchainfull_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps). Tenga en cuenta que debe elegir **EE.UU. Sur** como región en {{site.data.keyword.cloud_notm}} para crear redes de blockchain con el Plan inicial Beta.


## Destinatarios

Si encaja en una de las siguientes situaciones, el Plan inicial resulta adecuado para iniciar su andadura en blockchain.
- **_Aprender sobre la plataforma de {{site.data.keyword.blockchainfull_notm}}._**  
    Si siente curiosidad sobre la plataforma {{site.data.keyword.blockchainfull_notm}} o si nunca ha utilizado blockchain, el Plan inicial constituye una excelente opción para aprender a desarrollar y a gobernar una red de blockchain real. Descubrirá los componentes de los que consta una red, aprenderá a desplegar y a gestionar código de encadenamiento (también denominado "contratos digitales"), aprenderá a añadir canales (y a gestionar permisos de canales) y realizará un seguimiento de la generación de un nuevo bloque, igual que si estuviera en una red de producción.
- **_Desea actuar como varias organizaciones para facilitar el desarrollo de la red._**  
    El Plan inicial le permite actuar como varias organizaciones, lo que le permite ver cómo gestiona la plataforma de IBM Blockchain Platform (IBP) tareas de colaboración como la creación de un canal y la creación de instancias del código de encadenamiento, así como la prueba de aplicaciones y la invocación de transacciones. También puede invitar a otros a colaborar en una red del Plan inicial (como en los Planes empresariales).
- **_Crear soluciones de demostración en una red activa._**  
    El Plan inicial proporciona un potente entorno para probar definiciones de red (integrando un archivo `.bna` desarrollado mediante la aplicación {{site.data.keyword.blockchainfull_notm}} Platform: Develop) y aplicaciones de blockchain. La red de blockchain lista para ser utilizada permite realizar presentaciones rápidas a compañeros, directores y socios mediante las herramientas operativas y de gestión que ofrece el supervisor de red.
- **_Desarrollo iterativo y prueba de aplicaciones de blockchain._**  
    El Plan inicial le ofrece un área de transferencia para desarrollar y probar continuamente su código en una red de blockchain. Puede desarrollar de forma iterativa el código y desplegarlo en su arquitectura de integración y despliegue continuo. El Plan inicial proporciona las mismas funciones de red de blockchain y herramientas operativas y de gestión que el Plan empresarial. Cuando esté listo para desplegar el proyecto en uno de los planes empresariales, podrá trabajar de la misma manera que con el Plan inicial, pero con más oportunidades para hacer crecer su red.
- **_Prueba de los proyectos antes de lanzarlos a producción._**  
    El Plan inicial proporciona un entorno para que los desarrolladores y verificadores pasen rápidamente de su entorno local a un entorno de blockchain de nube real.  Este mecanismo permite a los desarrolladores y verificadores centrarse en las funciones y pasar rápidamente de una prueba de unidad a una prueba funcional. Los equipos de pruebas de sistemas, soluciones y rendimiento también pueden utilizar el entorno sin el esfuerzo adicional que supone configurar una red localmente.
- **_Plataforma {{site.data.keyword.blockchainfull_notm}} operativa de formación._**  
    La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece prácticamente la misma interfaz de usuario<!--the same user interface--> en el Plan inicial que en los planes empresariales, lo que garantiza que el equipo de formación personalizado pueda utilizar los mismos flujos de trabajo que utilizará la organización en un Plan empresarial.
- **_Despliegue rápido de aplicaciones de ejemplo mediante Toolchain._**  
    El Plan inicial le permite desplegar aplicaciones de ejemplo mediante Toolchain con unas pocas pulsaciones. Estos ejemplos ayudarán a los desarrolladores proporcionándoles un conjunto creciente de ejemplos con código que pueden modificar y personalizar.


## Especializaciones del Plan inicial

El Plan inicial ofrece la posibilidad de gestionar la pertenencia a grupos con la Autoridad emisora de certificado (CA), de realizar la aprobación de transacciones, de proporcionar servicios de ordenación, de crear canales privados, de gestionar ciclos de vida del código de encadenamiento y de colaborar con otros en una red activa, como si utilizara un Plan empresarial.

En concreto, el Plan inicial ofrece una red de blockchain preconfigurada que puede desarrollar, gobernar y operar mediante el supervisor de red. También ofrece enfoques sencillos para desplegar aplicaciones de ejemplo e integra las aplicaciones que se desarrollan con la herramienta {{site.data.keyword.blockchainfull_notm}} Platform: Develop.

- **_Red lista con una pulsación_**  
    El Plan inicial le proporciona una red con un servicio de ordenación, CA, un canal predeterminado y dos organizaciones (con un igual por organización) con una sola pulsación. La plataforma {{site.data.keyword.blockchainfull_notm}} gestiona la creación y configuración de esta red (podrá actualizarla cuando la utilice en un entorno activo). <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiencia de coste_**  
    La opción de pertenencia al Plan inicial ofrece muchas de las funciones de blockchain que ofrecen las opciones de pertenencia al Plan empresarial, pero a un coste menor.  <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->En la fase Beta, puede utilizar el Plan inicial de forma gratuita.
- **_Simulación de red de varias organizaciones_**  
    Puede utilizar el Plan inicial para simular la creación de una red con varias organizaciones. No es necesario que invite realmente a otras organizaciones a su red, ya que puede actuar usted mismo como otras organizaciones. Este mecanismo le permite aprender cómo se puede unir a la red una nueva organización, cómo funcionan juntas en la red varias organizaciones, etc. Puede cambiar entre organizaciones desde el supervisor de red para ver y gestionar la red desde la vista de diferentes organizaciones.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_API de Swagger_**  
    El Plan inicial expone varias API REST que puede probar desde una interfaz Swagger. Para obtener más información, consulte [Utilización de las API de Swagger](swagger_apis.html).
- **_Aplicaciones de ejemplo_**  
    El Plan inicial utiliza el servicio Toolchain en {{site.data.keyword.cloud_notm}} y permite el despliegue de ejemplo con simples pulsaciones.  Después de desplegar e iniciar un ejemplo, el código de encadenamiento y las aplicaciones se ejecutan automáticamente para su red de blockchain.  Para obtener más información sobre las aplicaciones de ejemplo, consulte [Despliegue de aplicaciones de ejemplo](howto/prebuilt_samples.html).
- **_Integración de la aplicación {{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    El Plan inicial le permite desplegar aplicaciones {{site.data.keyword.blockchainfull_notm}} Platform: Develop en su red.  Para obtener más información, consulte [Importación de aplicaciones IBP: Develop](link).

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## Consideraciones sobre el Plan inicial
{: #considerations}

El Plan inicial constituye un punto de entrada en la plataforma {{site.data.keyword.blockchainfull_notm}} y es para propósito de desarrollo y de prueba.  Consulte los elementos siguientes antes de utilizar el Plan inicial.

- **Requisito de la cuenta de {{site.data.keyword.cloud_notm}}**  
    El Plan inicial es gratuito en su versión Beta.  Sin embargo, debe actualizar su cuenta de {{site.data.keyword.cloud_notm}} a una cuenta de pago, por ejemplo, un tipo de **Pago según uso**. Todos los planes de pertenencia que ofrece la plataforma de {{site.data.keyword.blockchainfull_notm}} requieren una cuenta de pago de {{site.data.keyword.cloud_notm}}. Para actualizar su cuenta a un tipo Pago según uso, vaya a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}}, y pulse **Añadir tarjeta de crédito**.
- **Diferencias del Plan empresarial**
    - La [CA](glossary.html#ca) y el [servicio de ordenación](glossary.html#orderer) no son tolerantes a errores porque cada organización solo tiene una CA y una red solo tiene un [clasificador](glossary.html#orderer).
    - El servicio de ordenación solo utiliza el [consenso](glossary.html#consensus) [SOLO](glossary.html#SOLO). Una red del Plan inicial solo consta de un [clasificador](glossary.html#orderer) que realiza el consenso para todos los iguales.
    - El [Módulo de seguridad de hardware (HSM)](glossary.html#hsm) no está disponible para proteger y gestionar las claves digitales para la autenticación segura y el proceso de cifrado.
- **Limitación de recursos de red**  
    El Plan inicial asigna 1 CPU, 2 GB de RAM, y 20 Gi de almacenamiento de hardware constantemente para cada instancia de servicio de {{site.data.keyword.cloud_notm}}. Si añade más recursos de red a la misma red, los recursos compartirán las asignaciones de hardware. Sin embargo, al invitar a un miembro a unirse a la red, el miembro creará una nueva instancia de servicio. Por lo tanto, el Plan inicial asigna otro conjunto de hardware de 1 CPU, 2 GB de RAM y 20 Gi de almacenamiento al nuevo miembro.
- **Supresión de red inactiva**  
    Después de crear una red del Plan inicial (Beta), si no visita la red ni emite ninguna transacción en la misma durante 15 días, {{site.data.keyword.blockchainfull_notm}} Platform suprimirá la red.
    - Si ya no necesita la red, no tendrá que realizar ninguna acción y su red se suprimirá automáticamente.
    - Si sigue necesitando utilizar la red, puede mantenerla activa emitiendo transacciones en la misma. Puede encontrar la red del Plan inicial (Beta) en el [Panel de control de {{site.data.keyword.cloud_notm}} ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://console.bluemix.net/dashboard/apps/){:new_window}. Si no tiene transacciones regulares para emitir, siga las [instrucciones](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) para probar las aplicaciones de muestra.
- **Mantenimiento y actualización**  
    El mantenimiento del Plan inicial y las actualizaciones de red se realizan según una planificación fija. Durante el periodo de mantenimiento, no puede proporcionar nuevas redes y es posible que observe breves periodos de interrupción de la red.
- **Retención de datos**  
    El Plan inicial no garantiza la retención de datos con actualizaciones de release, incluido el paso de Beta a GA.
- **Consideraciones sobre migración**  
    La migración de los datos desde una red de Plan inicial a otros planes de suscripción no está soportada en este momento. Sin embargo, se pueden migrar archivos `.bna`, código de encadenamiento y aplicaciones que se han probado en el Plan inicial. Para obtener más información, consulte [Migración del Plan inicial al Plan empresarial](get_start_starter_plan.html#migrate).
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

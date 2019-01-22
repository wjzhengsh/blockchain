---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Plataforma {{site.data.keyword.blockchainfull_notm}}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**ATENCIÓN:** Antes de utilizar una oferta de {{site.data.keyword.blockchainfull}}, debe leer la información técnica y de soporte de la sección [Descargo de responsabilidad](needtoknow.html).
{:shortdesc}

La plataforma {{site.data.keyword.blockchainfull_notm}} (IBP) es la única plataforma integrada lista para la empresa que aborda el ciclo de vida completo de una red blockchain de varias organizaciones. Está diseñada para acelerar, mediante la colaboración en cada fase, la creación de redes de blockchain globales "creadas para la empresa" con el rendimiento y la seguridad para los casos de uso y los sectores regulados más exigentes. Las ofertas de redes gestionadas por {{site.data.keyword.IBM_notm}} en {{site.data.keyword.cloud_notm}} son adecuadas para clientes que son nuevos en blockchain. Los clientes que tengan experiencia en el uso de Hyperledger Fabric pueden desplegar componentes o una red en su propia infraestructura a través de {{site.data.keyword.cloud_notm}} privado (ICP) o de Amazon Web Services (AWS).

La plataforma {{site.data.keyword.blockchainfull_notm}} proporciona distintas ofertas para ayudar a todos los tipos de usuarios a iniciar su viaje en blockchain y mover sus aplicaciones a producción.

|       | [**Plan inicial**](#starter-enterprise)      | [ **Plan empresarial**](#starter-enterprise)  | [**IBP para ICP**](#ibp-for-icp) | [**IBP para AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **Qué se incluye** | **Niveles de servicio básicos; entorno de desarrollo y de prueba** | **Niveles de servicio avanzado y entorno listo para producción empresarial** |**Diagramas de Helm desplegables de CA, clasificador e igual** | **Igual de Inicio rápido de AWS**  |
| **Política de facturación** | **Suscripción mensual con [créditos en nube disponibles](howto/pricing.html#starter-plan-pricing)** | **Suscripción mensual** |  **[Precios de VPC](ibp-for-icp-about.html#ibp-icp-pricing) y Community Edition gratuita** | **Gratis** |
| **Plataforma Cloud**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud privado**| **AWS**|

**Precaución:** No utilice el **Plan inicial** para uso en producción. Es un entorno de desarrollo y de prueba, y no es adecuado para las cargas de trabajo de producción.

## Prestaciones de la plataforma {{site.data.keyword.blockchainfull_notm}}

Las ofertas de {{site.data.keyword.blockchainfull_notm}} parten del código base de [Hyperledger Fabric](reference/v10_fabric.html) que utiliza una arquitectura modular para alcanzar los niveles de seguridad empresarial, la integridad de datos, la escalabilidad y el rendimiento necesarios para satisfacer sus necesidades empresariales.

La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece una red blockchain segura y con permisos sobre la que los miembros autenticados pueden definir fácilmente activos y crear soluciones empresariales para modificarlos e intercambiarlos. Con las ofertas de la plataforma
{{site.data.keyword.blockchainfull_notm}}, puede aprovechar un marco de orquestación que le permita
**organizar de forma rápida el consorcio en una red blockchain activa**. La plataforma {{site.data.keyword.blockchainfull_notm}} proporciona herramientas fáciles de usar diseñadas para que varias instituciones puedan unirse y crear una red gobernada democráticamente. Las tareas de creación, gobierno y funcionamiento de red son intuitivas y transparentes gracias al supervisor de panel de control incorporado y los programas de utilidad suministrados para ello. En lugar del anterior proceso difícil de creación de red e implementación de gobierno, los miembros del consorcio pueden **centrarse en el despliegue de contratos digitales y la transferencia de activos e información**.

La **alta disponibilidad** de los recursos integrantes de la red (iguales, clasificadores y entidades emisoras de certificados) elimina el potencial efecto devastador de puntos únicos de anomalía. Un supervisor integrado de panel de control facilita la gestión de estos recursos y proporciona un potente mecanismo para visualizar activos y contratos beneficiosos. *Solo se admite en las ofertas de Plan inicial y empresarial*.

La **modularidad** de la arquitectura Hyperledger Fabric y la separación de roles de red proporcionan una infraestructura que permite la escalabilidad y adaptabilidad en una gran variedad de casos de uso. *Disponible y admitida en todas las ofertas de la plataforma {{site.data.keyword.blockchainfull_notm}}*.

Las comprobaciones y compensaciones que se producen durante el ciclo de vida de una transacción garantizan resultados coherentes y rigurosos, y los libros mayores siempre están sincronizados durante la implementación del protocolo de rumores ("gossip"). Se aplican controles de identidad y de acceso mediante operaciones de tipo **sign/verify** que se ejecutan constantemente en la red, que están *disponibles y se admiten en todas las ofertas de la plataforma {{site.data.keyword.blockchainfull_notm}}*.

Se proporcionan **herramientas de gobierno** para que los miembros puedan administrar y gestionar las reglas empresariales de sus redes. Por ejemplo, es posible que un cliente desee implementar una política que defina el número de miembros de una red que deben estar de acuerdo para que se una un nuevo miembro. O quizás exista un activo que requiera la aprobación de cada participante para que entre en vigor una modificación. Las reglas de gobierno son fundamentales para cualquier tipo de red empresarial y, en ocasiones, son muy elaboradas. Las herramientas de control (como por ejemplo los editores de políticas) simplifican en gran medida este proceso. *Solo están disponibles en las ofertas de Plan inicial y empresarial*.

Las redes del Plan empresarial se ejecutan en un entorno **aislado y de alta seguridad** sin acceso externo (incluido el acceso raíz) a los recursos de la red. Los datos se cifran al momento y en reposo y los módulos de soporte para módulos de seguridad de hardware permiten proteger las claves digitales según la regulación de la industria. La virtualización de hardware se utiliza para ejecutar cada nodo en un entorno aislado, protegiendo así otros nodos en la red de iguales con código de encadenamiento malicioso o que falla. La tarea de troceado ("hashing"), las operaciones sign/verify y las comunicaciones entre nodos se aceleran gracias a las avanzadas implementaciones criptográficas. *Disponible solo con el Plan empresarial*.

Para obtener más detalles sobre todas las características y funciones de Hyperledger Fabric, consulte la
[Documentación de Hyperledger Fabric
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Documentación de Hyperledger Fabric").

En la **Figura 1** se muestra un ejemplo de una red blockchain desplegada que consta de cuatro miembros (cada uno de los cuales posee dos nodos iguales), entidades emisoras de certificados responsables de distribuir el material de identidad criptográfico, un servicio de ordenación que define políticas y participantes en la red. El canal azul contiene los cuatro miembros de red y el canal amarillo está restringido solo a tres miembros: los bancos B, C y D. Además, vemos que el Banco A ejerce el rol de iniciador de red y que el Banco D existe solo como miembro en el contexto del canal amarillo. Por último, un usuario o una aplicación en posesión de un certificado x509 firmado adecuadamente puede enviar llamadas a iguales de la red.

![red blockchain](images/blockchain_network_2-01.png "Ejemplo de red blockchain")

*Figura 1. Una red blockchain de ejemplo con cuatro miembros que aprovechan los canales para aislar los datos*

## Desarrollo de aplicaciones para ejecutarlas en la red

Explore y acelere el desarrollo de blockchain utilizando las mejores prácticas derivadas de la colaboración de más de 400 clientes:

* Garantizar una alineación completa de la empresa con la tecnología que permite reducir significativamente el tiempo destinado al desarrollo de aplicaciones de blockchain (aplicaciones que de otro modo necesitarían seis semanas de desarrollo pueden crearse en menos de dos días).
* Crear rápidamente habilidades de blockchain en el equipo de programadores existente mediante el uso de las herramientas de desarrollador de {{site.data.keyword.blockchainfull_notm}}.
* Dotar a los desarrolladores de flexibilidad para que aprendan y desarrollen en su entorno preferido con un conjunto de herramientas abiertas y modernas.

Como propietario de negocio, puede desarrollar su caso de uso con la ayuda de expertos de IBM en blockchain y en el sector reunidos en {{site.data.keyword.blockchainfull_notm}} Garage para aprovechar la potencia de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Como desarrollador, puede nivelar con rapidez y facilidad los requisitos empresariales y agilizar el desarrollo de aplicaciones de blockchain en el entorno de red de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizando su base interactiva para desarrollar, iterar y probar redes empresariales. Estas herramientas están diseñadas para convertir las reglas empresariales en código de red empresarial en su entorno preferido:

* **Explorar en línea**
Aproveche la plataforma [{{site.data.keyword.blockchainfull_notm}}: desarrolle](./develop.html)<!--, which is powered by an open source development tool--> para aprender los conceptos clave de blockchain, cree definiciones de red y aproveche los modelos reutilizables del sector y las bibliotecas de contratos digitales.

  Cuando haya desarrollado su red empresarial, la podrá desplegar en una red real que se ejecute en la plataforma {{site.data.keyword.blockchainfull_notm}}. Para obtener más información, consulte [Despliegue de redes empresariales con el Plan inicial](./develop_starter.html) y [Despliegue de redes de empresa con el Plan empresarial](./develop_enterprise.html).

* **Instalar localmente**
  Aproveche Hyperledger Fabric para desarrollar y hacer pruebas directamente en su portátil. Para obtener más información, consulte [Creación de su primera red](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

* **Colaborar en un entorno de nube**
Utilice las redes reales listas para ser utilizadas con las opciones de Plan inicial y Plan empresarial para desarrollar código y compartirlo con otros. Para obtener más información, consulte [Acerca del Plan inicial](starter_plan.html) y [Acerca del Plan empresarial](enterprise_plan.html).

## Soporte de {{site.data.keyword.IBM_notm}}

{{site.data.keyword.IBM_notm}} ofrece diversas opciones de soporte para las soluciones de blockchain implementadas por {{site.data.keyword.IBM_notm}}. Para obtener más información sobre el soporte de {{site.data.keyword.blockchainfull_notm}}, consulte [Obtención de soporte](ibmblockchain_support.html).


## Plan inicial y Plan empresarial
{: #starter-enterprise}

Los planes inicial y empresarial le permiten desplegar y trabajar con redes blockchain descentralizadas con un servicio seguro listo para la producción. El **Plan inicial** es un entorno para aprender o empezar a desarrollar redes de blockchain y el **Plan empresarial** es un entorno de producción que ofrece altos niveles de seguridad y de soporte. Empiece por abajo y escale la red gradualmente a medida que su pertenencia y los volúmenes de transacción aumentan aprovechando las siguientes características:

* Un entorno altamente seguro con muchas características de seguridad de software, firmware y hardware.
* Arquitectura reforzada para la escalabilidad, la capacidad de recuperación y la disponibilidad.
* Optimización del rendimiento y la ejecución en el sistema Linux más rápido del mundo.

El funcionamiento de la red en la plataforma {{site.data.keyword.blockchainfull_notm}} incluye herramientas y prestaciones que facilitan las tareas administrativas:

* Paneles de control para la supervisión y gestión de los recursos de la red.
* Actualizaciones sencillas de la pila de código completa.
* Soporte técnico 24/7 integrado en el portal.
* Pila de seguridad reforzada sin acceso privilegiado, malware y a prueba de manipulación, cifrada completamente y con muchas más características para redes con datos sensibles en los sectores regulados.

El Plan inicial y el Plan empresarial son servicios de plataforma en {{site.data.keyword.cloud_notm}} y ambos planes siguen los [Términos de servicios de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} - Términos de servicios") de los contratos de nivel de servicio (SLA). Tenga en cuenta que las redes de Plan inicial y empresarial se suministran en **varios entornos** en centros de datos separados geográficamente.

Para obtener más información sobre el Plan inicial y el Plan empresarial, consulte [Acerca del Plan inicial](starter_plan.html) y [Acerca del Plan empresarial](enterprise_plan.html). Si está listo para empezar, regístrese ahora para ser [miembro del Plan inicial o empresarial de IBP ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) en {{site.data.keyword.cloud_notm}}.

## Plataforma {{site.data.keyword.blockchainfull_notm}} para ICP
{: #ibp-for-icp}

La plataforma {{site.data.keyword.blockchainfull_notm}} para ICP proporciona los componentes que necesita para ejecutar una red blockchain en su propia infraestructura a través de ICP. Los componentes incluyen Hyperledger Fabric, una entidad emisora de certificados (CA), un clasificador y un igual, que puede desplegar, gestionar y configurar utilizando diagramas de Helm de Kubernetes. **Esta oferta está dirigida a los clientes que tengan un nivel alto de experiencia con Hyperledger Fabric.**

IBP para ICP permite el despliegue de redes blockchain en una nube privada para abordar los requisitos de residencia de datos, las normativas del mercado y las preferencias de infraestructura. Simplifica el despliegue de elementos esenciales de una red blockchain en su propia infraestructura a través de {{site.data.keyword.cloud_notm}} privado, una plataforma de aplicaciones basada en Kubernetes para desarrollar y gestionar aplicaciones contenerizadas locales.

 * Permite que los clientes puedan gestionar redes de IBP con su propia infraestructura. Una edición Community Edition gratuita permite a los clientes realizar la ejecución en sus propios entornos aislados y seguros, pero no se proporciona soporte.
 * Permite que los clientes puedan configurar Fabric en Kubernetes utilizando diagramas de Helm y documentación detallada para las operaciones.
 * Concede a los clientes un soporte técnico avanzado, a menos que utilice Community Edition.

 Para obtener más información sobre IBP para ICP, consulte [Acerca de la plataforma {{site.data.keyword.blockchainfull_notm}} para {{site.data.keyword.cloud_notm}} privado](ibp-for-icp-about.html).

## Plataforma {{site.data.keyword.blockchainfull_notm}} para AWS
{: #ibp-for-aws}

La plataforma {{site.data.keyword.blockchainfull_notm}} para AWS permite que los iguales puedan aprovechar el perfil de conexión, las entidades emisoras de certificados (CA) de Hyperledger Fabric y el servicio de ordenación de una red existente del Plan inicial o empresarial en {{site.data.keyword.cloud_notm}} para procesar transacciones a través de una plantilla de Inicio rápido de AWS. El Inicio rápido le permite desplegar iguales utilizando plantillas de AWS CloudFormation. Esta plantilla está pensada para los responsables de la toma de decisiones sobre la infraestructura de TI y los administradores del sistema que deseen configurar, desplegar y ejecutar rápidamente iguales de IBP alojados en AWS que estén conectados a una red de Plan inicial o Plan empresarial. Puede utilizar la plantilla para crear una nueva nube privada virtual (VPC) en AWS, o desplegar el igual en una VPC existente.

El Inicio rápido completa las configuraciones siguientes:
 * Una arquitectura de alta disponibilidad que abarca dos zonas de disponibilidad.
 * Una VPC configurada con subredes públicas de acuerdo con las prácticas recomendadas por AWS. Esto le proporciona su propia red virtual en AWS.
 * Una pasarela de Internet para permitir el acceso a Internet.
 * En las subredes públicas, dos iguales en dos zonas de disponibilidad (un igual en cada subred).
 * En cada subred pública, un contenedor de igual con una base de datos LevelDB incorporada o un contenedor de CouchDB secundario.

Para obtener más información sobre IBP para AWS, consulte [Acerca de la plataforma {{site.data.keyword.blockchainfull_notm}} en Amazon Web Services](howto/remote_peer.html).

---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Plataforma {{site.data.keyword.blockchainfull_notm}}

**ATENCIÓN:** Antes de utilizar una oferta de {{site.data.keyword.blockchainfull}}, debe leer la información técnica y de soporte de la sección [Descargo de responsabilidad](needtoknow.html).  
{:shortdesc}

La plataforma {{site.data.keyword.blockchainfull_notm}} es la única plataforma integrada lista para la empresa que aborda el ciclo de vida completo (**desarrollo**, **gobierno** y **funcionamiento**) de una red de blockchain entre diversas organizaciones. Está diseñada para acelerar, mediante la colaboración en cada fase, la creación de redes de blockchain globales "creadas para la empresa" con el rendimiento y la seguridad para los casos de uso y los sectores regulados más exigentes.

|  Función     | Actividades       | [Roles](#participating-in-a-blockchain-network)  |
| ------------------------- |--------------------------|-----|
| [Desarrollo](develop.html) | Desarrollar redes empresariales, desarrollar aplicaciones, desarrollar código de encadenamiento | Desarrolladores de red, desarrolladores de aplicaciones |
| [Gobierno](get_start.html)| Invitar a miembros, generar credenciales, proponer modelos de gobierno, distribuir certificados e información de punto final | Operadores de la red, miembros de la red |
| [Funcionamiento](v10_dashboard.html)| Supervisar estado y actividad, gestionar nuevos despliegues, añadir o eliminar miembros, gestionar el ciclo de vida del código de encadenamiento, gestionar canales, soporte | Operadores de la red, participantes en la red |

**Nota**: La tabla no está pensada para representar una progresión lineal. Tareas como el desarrollo y la gestión de red se producen repetidamente a medida que evolucionan las aplicaciones y la pertenencia.

## Ofertas de pertenencia de {{site.data.keyword.IBM_notm}}

La plataforma IBM Blockchain Platform proporciona distintos planes de pertenencia para ayudar a todos los tipos de usuarios a empezar su viaje en blockchain y a mover sus aplicaciones en producción.

|       | [Plan inicial](starter_plan.html)      | [Plan empresarial](enterprise_plan.html)  | Plan empresarial Plus | Plan autogestionado
| ------------------------- |--------------------------|-----|-----|------|
| **Qué se incluye** | **Niveles de servicio básicos; entorno de desarrollo y de prueba** | **Niveles de servicio avanzado; listo para producción empresarial** | **Cálculo dedicado para rendimiento y aislamiento; listo para producción empresarial** | Pila de software para instalar en su propia infraestructura; conexión a red alojada en este servicio en la nube |
| **Política de facturación** | **Gratuita para beta<!--trial of 30 days, followed by monthly charge-->** | **Suscripción mensual** | **Suscripción mensual** | Suscripción mensual |
| **Disponibilidad** | **Beta** | **Ya disponible** | **Al comprar** | Próximamente |


<!--
- **The IBM Blockchain Starter Plan** is ideal for those who are learning or getting started developing their blockchain network.
- **Enterprise Plan** is a production environment offering high levels of security and support.
- **Enterprise Plus Plan** offers a dedicated production environment for additional performance and isolated compute and storage resources to protect critical data.
-->

**Precaución:** No utilice el **Plan inicial** para producción. Es un entorno de desarrollo y de prueba.

Las ofertas de {{site.data.keyword.blockchainfull_notm}} parten del código base de [Hyperledger Fabric](./v10_fabric) que utiliza una arquitectura modular para alcanzar los niveles de seguridad empresarial, la integridad de datos, la escalabilidad y el rendimiento necesarios para satisfacer sus necesidades empresariales.
- Las redes del **Plan inicial** se basan en Hyperledger Fabric V1.1.
- El **Plan empresarial** se actualiza a Hyperledger Fabric V1.1. Todas las redes recién creadas estén en el nivel V1.1 de fábrica.

Regístrese ahora para ser [miembro de {{site.data.keyword.blockchainfull_notm}}![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).


## **Desarrollo** de red
Explore y acelere el desarrollo de blockchain utilizando las mejores prácticas derivadas de la colaboración de más de 400 clientes para:
* Garantizar una alineación completa de la empresa con la tecnología que permite reducir significativamente el tiempo destinado al desarrollo de aplicaciones de blockchain (aplicaciones que de otro modo necesitarían seis semanas de desarrollo pueden crearse en menos de dos días).
* Crear rápidamente habilidades de blockchain en el equipo de programadores existente mediante el uso de las herramientas de desarrollador de {{site.data.keyword.blockchainfull_notm}}.
* Dotar a los desarrolladores de flexibilidad para que aprendan y desarrollen en su entorno preferido con un conjunto de herramientas abiertas y modernas.  

Como propietario de negocio, puede desarrollar su caso de uso con la ayuda de expertos de IBM en blockchain y en el sector reunidos en {{site.data.keyword.blockchainfull_notm}} Garage para aprovechar la potencia de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Como desarrollador, puede nivelar con rapidez y facilidad los requisitos empresariales y agilizar el desarrollo de aplicaciones de blockchain en el entorno de red de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizando su base interactiva para desarrollar, iterar y probar redes empresariales. Estas herramientas están diseñadas para convertir las reglas empresariales en código de red empresarial en su entorno preferido:
* **Explorar en línea**  
  Aproveche la plataforma [{{site.data.keyword.blockchainfull_notm}}: desarrolle](./develop.html)<!--, which is powered by an open source development tool--> para aprender los conceptos clave de blockchain, cree definiciones de red y aproveche los modelos reutilizables del sector y las bibliotecas de contratos digitales.  
  Cuando haya desarrollado su red empresarial, la podrá desplegar en una red real que se ejecute en la plataforma IBM Blockchain. Para obtener más información, consulte [Despliegue de redes empresariales con el Plan inicial](./develop_starter.html) y [Despliegue de redes de empresa con el Plan empresarial](./develop_enterprise.html).
* **Instalar localmente**  
  Utilice las imágenes certificadas por IBM de Hyperledger Fabric y Composer, que constituyen una infraestructura de código abierto y una herramienta para crear una red empresarial con el fin de desarrollar y realizar pruebas directamente en su portátil. Para obtener más información, consulte [Creación de su primera red](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html).
* **Colaborar en un entorno de nube**  
  Utilice las redes reales listas para ser utilizadas con las opciones de Plan inicial y Plan empresarial para desarrollar código y compartirlo con otros. Para obtener más información, consulte [Acerca del Plan inicial](starter_plan.html) y [Acerca del Plan empresarial](enterprise_plan.html).


## **Gobierno** de la red
Existen dos opciones para crear el entorno back-end de la red. En primer lugar, puede utilizar las imágenes publicadas de Hyperledger Docker, que le proporcionan la opción de implementar la biblioteca Composer para crear sus aplicaciones e interactuar con la red. O bien, puede escribir código de encadenamiento nativo y desarrollar el código del lado de servidor para dirigir las transacciones. La ejecución local es la oportunidad perfecta para experimentar con configuraciones de red, explorar posibles casos de uso y empezar a construir pruebas de concepto. Cuando las pruebas de concepto empiecen a tomar forma, podrá ampliar la implementación alojando su red en la nube.

Con un despliegue de nube, se le proporcionará un conjunto de recetas y scripts fáciles que le permitirán desplegar una red Hyperledger Fabric que se ejecute en Kubernetes. Utilice esta fase para examinar el comportamiento y la estabilidad de su prueba de concepto en un entorno alojado. Puede considerarse [{{site.data.keyword.blockchainfull_notm}} Container Service ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://ibm-blockchain.github.io/) como un mecanismo de prueba de la funcionalidad y capacidad de recuperación de la aplicación y como un precursor natural del plan Empresarial.

Una vez creada la red, {{site.data.keyword.blockchainfull_notm}} se ha diseñado para crear una experiencia de gestión de red que ofrece a los miembros parte de control, sin que ningún miembro disponga de control completo. La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece el primer conjunto de herramientas integradas para que los equipos impongan la gestión de cambios de la red mediante políticas personalizables.

En la lista siguiente, se muestran las características clave de este modelo de gobierno:

* Herramientas de gestión democrática que permiten la gestión colectiva.
* Editor de políticas para definir políticas flexibles y democráticas para gobernar los cambios en la red.
* Herramientas y políticas preconfiguradas que habilitan la incorporación rápida, la personalización y la activación.
* Una herramienta de flujo de trabajo entre varias partes con notificaciones integradas, panel de actividades de miembros y recopilación de firmas seguras.


## **Funcionamiento** de red
Despliegue y opere redes descentralizadas con un servicio seguro listo para la producción. Empiece por abajo y escale la red gradualmente a medida que su pertenencia y los volúmenes de transacción aumentan aprovechando las siguientes características:

* Un entorno altamente seguro con muchas características de seguridad de software, firmware y hardware.
* Arquitectura reforzada para la escalabilidad, la capacidad de recuperación y la disponibilidad.
* Optimización del rendimiento y la ejecución en el sistema Linux más rápido del mundo.


El funcionamiento de la red en la plataforma {{site.data.keyword.blockchainfull_notm}} incluye herramientas y prestaciones que facilitan las tareas administrativas:

* Paneles de control para la supervisión y gestión de los recursos de la red.
* Actualizaciones sencillas de la pila de código completa.
* Soporte técnico 24/7 integrado en el portal.
* Pila de seguridad reforzada sin acceso privilegiado, malware y a prueba de manipulación, cifrada completamente y con muchas más características para redes con datos sensibles en los sectores regulados.

## Servicios básicos de red

Para que blockchain esté operativo, los miembros forman una base de confianza ejecutando uno o varios servicios básicos de red:

- **Servicio de ordenación**: ordenación y sincronización de transacciones  
  Básicamente, el servicio de ordenación es la definición de la red.  Contiene información de identidad de cada miembro, información sobre canales y un conjunto de políticas que dictan qué miembros pueden realizar determinadas tareas (por ejemplo, invitar a otros miembros, crear canales, etc.). Cada transacción y configuración de operación fluirá a través del servicio de ordenación, por lo que se trata de una pieza más que fundamental en el esquema global.  Dada la importancia del servicio de ordenación, es fácil observar los inconvenientes de una orquestación autoritaria en la que quizás solo un miembro extrae las series.  Para contrarrestar esto, los miembros de la red gestionan conjuntamente el servicio de ordenación y actúan conjuntamente en las implementaciones de gobierno.  Es decir, las decisiones se toman colectivamente y NO unilateralmente. Todos los miembros participan en la red y por extensión, tienen voto en las operaciones de configuración y personalización de su postura en la red.  Estas nociones de “democracia” y la toma de decisiones conjunta son los componentes inherentes de una red descentralizada y fiable.  IBM actúa como "operador" del servicio de ordenación para redes desplegadas en la plataforma IBM Blockchain.

- **Entidad emisora de certificados**: emisión de certificados a participantes  
  De forma sencilla, la entidad emisora de certificados (CA) proporciona pertenencia.  Todas las entidades de la red (iguales, clasificadores, clientes, etc.) deben tener una identidad para comunicarse, autenticarse y realizar transacciones. Estas “identidades” existen en forma de certificados x509 (es decir, certificados de inscripción), que son necesarios para cualquier participación directa en la red de blockchain.  Además, existen formas de participación indirecta, pero las veremos más adelante.  La CA es como un sello de goma que aporta testificación y credibilidad a las identidades.  Cada miembro tiene su propia CA y, a través de esta CA, puede firmar certificados no solo de los componentes de su propiedad (iguales), sino que también de aplicaciones y clientes de terceros.  Puede comparar la CA de un miembro con un registro especial o un sello notarial.  Un certificado que firma esta entidad emisora de certificados es un requisito previo para acceder a la red.

- **Igual**: validación/aprobación de transacciones  
  El objetivo del igual es realizar dos funciones principales: ejecutar/validar transacciones y mantener libros mayores.  El igual ejecuta contratos digitales y es el titular del historial de transacciones y del estado actual de los activos en los canales de la red.  Al final del día, se trata de acceder al igual (directa o indirectamente) y realizar lecturas y grabaciones en el libro mayor. Cuando un miembro proporciona a un usuario acceso a la red, en realidad está proporcionando acceso a la funcionalidad del igual.

Cuando un miembro se une a una red a través de la plataforma {{site.data.keyword.blockchainfull_notm}}, la emisión estándar es un igual y una CA.  En una red de producción, los miembros querrán ejecutar varias instancias de estos servicios para garantizar la disponibilidad. De forma predeterminada, IBM ejecuta el Servicio de ordenación para las redes creadas por la plataforma {{site.data.keyword.blockchainfull_notm}}.  

## Participación en una red de blockchain

El término **participante** es la clasificación más amplia para cualquier organización, individuo, aplicación o dispositivo que interactúa con la red de blockchain.  Bajo el término “participante”, se distinguen dos grupos: **miembros** y **usuarios**.   

En otras palabras: un miembro posee un certificado digital válido que le permite emitir y/o validar transacciones en una red de blockchain.  Un usuario no dispone de un certificado, pero puede interactuar con la red de blockchain a través de uno de los miembros de red existentes.   Considere que un certificado de un miembro es como una “tarjeta de pertenencia”, como la de un gimnasio.  Aunque el usuario no disponga de una, puede acceder al gimnasio como “invitado” de un miembro existente.  Veamos estos dos roles un poco más.

### Miembros

La plataforma {{site.data.keyword.blockchainfull_notm}} está respaldada por Hyperledger Fabric, que es una tecnología de “blockchain autorizada”.  Por lo tanto, todos los miembros están inscritos en la red con un certificado que les otorga permisos para utilizar la red como **proveedor** de servicios (es decir, les permite emitir certificados, validar/ordenar transacciones) o como **consumidor** (por ejemplo, para emitir transacciones).   

- **Proveedores: confiamos en los miembros**. Una red de blockchain se basa en sus miembros.   Para que una red de blockchain sea operativa, debe haber un conjunto mínimo de miembros que *proporcionen* servicios de blockchain básicos, lo que incluye la validación de transacciones, la ordenación de transacciones y los servicios de gestión de certificados. Al ejecutar estos servicios, los miembros mantienen la integridad del libro mayor compartido en el epicentro de la red de blockchain. ¿Cuántos miembros se necesitan para que blockchain esté operativo? La respuesta dependerá del requisito de confianza de la red. Algunas redes toleran un modelo de confianza más centralizado que necesite menos miembros que actúen como proveedores.  Otras redes requieren un conjunto de miembros más diverso (es decir, entidades jurídicamente separadas) y disponen de un modelo de confianza más descentralizado.  Un ejemplo de modelo de confianza más centralizado sería una red de visibilidad de la cadena de suministro cuyos miembros sean un minorista global, una empresa de envíos globales e IBM.  En este caso, estos tres miembros actuarían como “base de confianza” de la red y prestarían los servicios básicos de la red de blockchain. Estos miembros pueden emitir certificados a importadores, exportadores, agentes personalizados o minoristas para que estos puedan participar (emitir transacciones) en la red. Esta red puede descentralizar la confianza mediante la habilitación de más miembros para la prestación de servicios básicos, garantizando que todos los miembros tengan control pero que ningún miembro tenga el control exclusivo. Una red típica tiene alrededor de 10 miembros que prestan servicios de base de blockchain.

- **Consumidores: con la confianza establecida, es hora de invocar**. Con una base de confianza ya establecida, la red está lista para crecer.  Es bastante habitual que la mayoría de los miembros de la red solo la utilicen para invocar transacciones en el libro mayor distribuido.  Estos miembros son solo *consumidores* y no participan en la prestación de servicios básicos a la red.  Una red típica tiene entre 10 y 100 miembros con permiso para emitir transacciones dentro de una red de blockchain determinada.


#### Personas miembro

A veces resulta útil pensar en los miembros como una persona que ejerce el rol de miembro en la red empresarial.  Estas son algunas de las más habituales.

- **Iniciador**: un miembro que ha sido seleccionado por otros miembros para poner en marcha la red de Blockchain.    La plataforma IBM Blockchain necesita que un solo miembro inicie sesión en la plataforma IBM Blockchain y realice las tareas de inicio de red.  Estas incluyen denominación de red, invitación a un grupo inicial de miembros y definición del conjunto predeterminado de políticas de operaciones de red.  Se trata de un rol transitorio.  Cuando se ha puesto en marcha la red, el iniciador no conserva privilegios especiales y asume el rol de miembro.  

- **Mantenedor**: un miembro que ejecuta uno o varios iguales de red y CA.   Estos miembros mantienen la integridad del libro mayor distribuido participando en el proceso de consenso, que se encarga de la validación de transacciones en una red de blockchain.   El mantenedor, al ser propietario de una CA, también puede emitir certificados a participantes y concederles acceso a la red.

- **Operador**: un miembro que ejecuta servicios en nombre de otros miembros de red, incluidos el servicio de ordenación de transacciones, entidades emisoras de certificados, pasarelas de transacciones y otros servicios básicos de red.  De forma predeterminada, IBM es el operador de red de las redes desplegadas en la plataforma IBM Blockchain.

- **Auditor**: un miembro al que se le han otorgado permisos para realizar funciones de auditoría en la red.  Algunos ejemplos de funciones de auditoría incluyen la facturación, el seguimiento del cumplimiento o el análisis.  El rol de auditor normalmente se traslada al miembro con acceso a una vista más amplia de transacciones en el libro mayor y/o un mayor número de inscripciones en los canales de transacción.

### Usuarios

Aunque puede haber cientos de miembros de una red de blockchain, puede haber miles de usuarios.   Un usuario es un participante en una red de blockchain que tiene acceso indirecto al libro mayor a través de una “relación de confianza” con un miembro existente.  Por ejemplo, es habitual que algunas aplicaciones móviles empleen su propio esquema de autorización y autenticación de usuarios (OAuth, OpenID) y correlacionen credenciales a uno o varios miembros con credenciales en una red de blockchain.    Se suele crear un servicio de pasarela o proxy para llevar a cabo esta función de correlación que permita correlacionar el mundo exterior con el entorno de blockchain.

## Plataforma {{site.data.keyword.blockchainfull_notm}}

La plataforma {{site.data.keyword.blockchainfull_notm}} ofrece una red de blockchain segura y con permisos sobre la que los miembros autenticados pueden definir fácilmente activos y crear soluciones empresariales para modificarlos e intercambiarlos.  Ya no es un proceso tedioso y complicado para arrancar una red de blockchain de producción. Con la plataforma {{site.data.keyword.blockchainfull_notm}}, puede aprovechar un marco de orquestación que le permita **organizar el consorcio en una red de blockchain activa en poco tiempo**. La plataforma {{site.data.keyword.blockchainfull_notm}} proporciona herramientas de consorcio fáciles de usar diseñadas para que varias instituciones puedan unirse y crear una red gobernada democráticamente. Las tareas de creación, gobierno y funcionamiento de red son intuitivas y transparentes gracias al supervisor de panel de control incorporado y los programas de utilidad suministrados para ello. En lugar del anterior proceso difícil de creación de red e implementación de gobierno, los miembros del consorcio pueden **centrarse en el despliegue de contratos digitales y la transferencia de activos e información**.      
{:shortdesc}

La **alta disponibilidad** de los recursos integrantes de la red (nodos iguales, servicio de ordenación, entidad emisora de certificados, código de encadenamiento) elimina el potencial efecto devastador de puntos únicos de error. Un supervisor integrado de panel de control facilita la gestión de estos recursos y proporciona un potente mecanismo para visualizar activos y contratos beneficiosos.

La **modularidad** de la arquitectura Hyperledger Fabric y la separación de roles de red (aprobador, confirmador, clasificador) proporcionan una infraestructura que permite la escalabilidad y adaptabilidad en una gran variedad de casos de uso.

Las comprobaciones y compensaciones que se producen durante el ciclo de vida de una transacción garantizan resultados coherentes y rigurosos, y los libros mayores siempre están sincronizados durante la implementación del protocolo de rumores ("gossip") conocido. Se aplican controles de identidad y de acceso mediante operaciones de tipo **sign/verify** que se ejecutan constantemente en la red.  

Se proporcionan **herramientas de gobierno** para que los miembros puedan administrar y gestionar las reglas empresariales de sus redes. Por ejemplo, es posible que un cliente desee implementar una política que defina el número de miembros de una red que deben estar de acuerdo para que se una un nuevo miembro. O quizás exista un activo que requiera la aprobación de cada participante para que entre en vigor una modificación. Las reglas de gobierno son fundamentales para cualquier tipo de red empresarial y, en ocasiones, son muy elaboradas. Las herramientas de control (como por ejemplo los editores de políticas) simplifican en gran medida este proceso.

El servicio se ejecuta en un entorno **aislado y de alta seguridad** sin acceso externo (incluido el acceso raíz) a los recursos de la red. Los datos se cifran al momento y en reposo y los módulos de soporte para módulos de seguridad de hardware permiten proteger las claves digitales según la regulación de la industria. La virtualización de hardware se utiliza para ejecutar cada nodo en un entorno aislado, protegiendo así otros nodos en la red de iguales con código de encadenamiento malicioso o que falla. La tarea de troceado ("hashing"), las operaciones sign/verify y las comunicaciones entre nodos se aceleran gracias a las avanzadas implementaciones criptográficas.

Antes de continuar, veamos una configuración simple dentro de la plataforma {{site.data.keyword.blockchainfull_notm}}.  En la **Figura 1** se muestra un ejemplo de una red de blockchain desplegada que consta de cuatro miembros (cada uno de los cuales posee dos nodos iguales), una entidad emisora de certificados responsable de distribuir el material de identidad criptográfico, un servicio de ordenación que define políticas y participantes en la red. El canal azul contiene los cuatro miembros de red y el canal amarillo está restringido solo a tres miembros: los bancos B, C y D. Además, vemos que el Banco A ejerce el rol de iniciador de red y que el Banco D existe solo como miembro en el contexto del canal amarillo. Por último, un usuario o una aplicación en posesión de un certificado x509 firmado adecuadamente es capaz de enviar llamadas a iguales de la red. Como se ha mencionado anteriormente, es muy posible que un usuario ni siquiera conozca la existencia de la red de blockchain.

![Red de blockchain](images/blockchain_network_2-01.png "Ejemplo de red de blockchain")

*Figura 1. Una red de blockchain de ejemplo que consta de cuatro miembros que aprovechan los canales para aislar los datos*

## {{site.data.keyword.IBM_notm}} Soporte

{{site.data.keyword.IBM_notm}} ofrece soporte en soluciones {{site.data.keyword.blockchain}} implementadas por {{site.data.keyword.IBM_notm}}. Para obtener más información sobre el soporte de {{site.data.keyword.blockchainfull_notm}}, consulte [Obtención de soporte](ibmblockchain_support.html).

Para obtener más información sobre todas las características y funciones de Hyperledger Fabric, consulte la [documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/).

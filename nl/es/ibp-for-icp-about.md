---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Acerca de la plataforma {{site.data.keyword.blockchainfull_notm}} para {{site.data.keyword.cloud_notm}} privado
{: #overview}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

La plataforma {{site.data.keyword.blockchainfull}} publica la plataforma IBM Blockchain para {{site.data.keyword.cloud_notm}} privado (ICP), una plataforma de aplicaciones para desarrollar y gestionar aplicaciones contenerizadas basándose en Kubernetes, que permite a los usuarios desplegar entidades emisoras de certificados (CA), clasificadores e iguales en x86, LinuxONE e IBM Z. La plataforma {{site.data.keyword.blockchainfull_notm}} para ICP se basa en Hyperledger Fabric v1.2.1 y se despliega utilizando diagramas de Helm de Kubernetes.
{:shortdesc}

La plataforma {{site.data.keyword.blockchainfull_notm}} para {{site.data.keyword.cloud_notm}} privado es un producto empaquetado para que los clientes de ICP puedan desplegar componentes de blockchain en su entorno local. Tras importar el diagrama de Helm, puede encontrarla como un mosaico de la plataforma {{site.data.keyword.blockchainfull_notm}} en el catálogo de ICP. Para obtener más información sobre ICP, consulte la documentación de [{{site.data.keyword.cloud_notm}} privado versión 3.1.0 ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} privado 3.1.0").

## Qué ofrece la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP

La plataforma {{site.data.keyword.blockchainfull_notm}} para IBM Cloud privado (ICP) le permite desplegar todos los componentes fundamentales de un blockchain de Hyperledger Fabric: una entidad emisora de certificados, un servicio de ordenación e iguales. Proporciona la flexibilidad para desplegar distintos componentes según las necesidades empresariales. Puede utilizar {{site.data.keyword.blockchainfull_notm}} para ICP para iniciar una red blockchain desplegando y configurando un servicio de ordenación que enlaza organizaciones en un consorcio de blockchain. También puede desplegar iguales y unirse a otras redes que utilicen componentes basados en Fabric, incluyendo redes de la plataforma {{site.data.keyword.blockchainfull_notm}} desplegadas en nubes utilizando ICP o redes de Plan inicial o Plan empresarial alojadas en IBM Cloud. Para obtener más información sobre los bloques de creación de las redes de Hyperledger Fabric, consulte [Visión general de componentes de Blockchain](/docs/services/blockchain/blockchain_component_overview.html).

## ¿Es la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP adecuada para usted?

La ejecución de componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} fuera de {{site.data.keyword.cloud_notm}} le ofrece una mayor flexibilidad para crecer o unirse a una red blockchain. Resulta de ayuda para que los creadores de redes hagan crecer sus redes al permitir la unión de nuevos miembros mientras utilizan la plataforma que han elegido. Permitirá que las organizaciones interesadas en unirse a redes blockchain coloquen sus iguales con sus aplicaciones existentes o los integren con sus sistemas de registro.

**Importante:** el proceso para desplegar la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP es complicado y requiere un alto grado de experiencia en Fabric. Si es nuevo en Fabric, ICP o la plataforma {{site.data.keyword.blockchainfull_notm}}, y su objetivo es configurar un entorno de desarrollo o una prueba de concepto, plantéese utilizar el [Plan inicial](/docs/services/blockchain/starter_plan.html) en su lugar. Tenga también en cuenta que no todas las configuraciones de despliegue potenciales se admiten en la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP.

Los usuarios de esta oferta gestionarán su propia seguridad e infraestructura. {{site.data.keyword.cloud_notm}} no ofrece estos servicios. Revise las [Consideraciones y limitaciones](#ibp-icp-considerations) de la sección siguiente antes de comenzar.

## Consideraciones y limitaciones
{: #ibp-icp-considerations}

Antes de empezar, asegúrese de que entiende las **consideraciones** y **limitaciones** siguientes:

- El usuario es responsable de la supervisión del estado, la seguridad, el registro y la gestión del uso de recursos de los componentes.
- Los componentes que se ejecutan en otros entornos de nube no son visibles en el supervisor de red de las redes que se ejecutan en {{site.data.keyword.cloud_notm}}.
- Los diagramas de Helm despliegan una única instancia de un clasificador o igual, o de una CA.
- Puede desplegar varios componentes en un único espacio de nombres en ICP, siempre que tengan distintos nombres de release.
- No se pueden direccionar los componentes utilizando la interfaz de usuario de Swagger en la interfaz de usuario del supervisor de red.
- No hay soporte para TLS mutuo.

**Consideraciones de la entidad emisora de certificados**
- Este diagrama de Helm despliega una única instancia de la CA. Aunque se considera que tener una CA independiente para cada organización es el método recomendado, puede ser necesario desplegar varias CA. Por ejemplo, si tiene pensado desplegar un clasificador y tres iguales, necesitará al menos dos CA (una para la organización del clasificador y otra para la organización del igual).
- Aunque puede optar por ejecutar una base de datos MySQL independiente, esta opción no está presente en el diagrama de Helm. No obstante, el diagrama de Helm desplegará una base de datos SQLite dentro de la CA para gestionar las necesidades de base de datos de la CA, las cuales incluyen la realización del seguimiento del número de inscripciones por usuario y los certificados revocados.

**Consideraciones del clasificador**
- El servicio de ordenación es compatible con cualquier componente en la versión 1.2 de Hyperledger Fabric.
- Este diagrama de Helm despliega una instancia individual del servicio de ordenación SOLO (un clasificador). Tenga en cuenta que no es posible desplegar más de un clasificador SOLO en un canal para hacer que el servicio de ordenación sea de alta disponibilidad. Este es uno de los motivos por los que los servicios de ordenación SOLO son adecuados para entornos de desarrollo, y no para entornos de producción. No obstante, puede desplegar varias instancias del servicio de ordenación SOLO en distintas redes (es decir, con un consorcio independiente).

**Consideraciones del igual**

- Puede conectar los iguales únicamente a redes blockchain que estén en el nivel v1.1 o v1.2.1 de Fabric. Puede encontrar la versión de Hyperledger Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#network-preferences) en el supervisor de red. Siga las [instrucciones](#starter-enterprise-network-endpoints) para recuperar la información de conexión del igual de las redes de Plan inicial o empresarial.
- El tipo de base de datos del igual debe coincidir con el tipo de base de datos de la red blockchain, ya sea LevelDB o CouchDB.
- La interfaz CouchDB Fauxton no está disponible en el igual.
- Actualmente no se da soporte a [gossip (rumor)](/docs/services/blockchain/glossary.html#gossip) para los iguales. Esto implica que tampoco hay soporte para las características de Fabric que dependen de rumores (gossip), como [datos privados ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "datos privados") y [descubrimiento de servicios ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descubrimiento de servicios").

## Requisitos previos del sistema
{: #prerequisites}

La plataforma {{site.data.keyword.blockchainfull_notm}} para ICP admite los sistemas operativos siguientes:
- Red Hat Enterprise Linux (RHEL) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS y 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

El diagrama de Helm de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP se ha validado para su ejecución en clústeres de {{site.data.keyword.cloud_notm}} privado v3.1.0 en Ubuntu Linux utilizando los nodos trabajadores y el almacenamiento de reserva siguientes:

- **LinuxONE e IBM Z**: z/VM y KVM, que utilizan NFS.
- **x86**: Linux de 64 bits que utiliza GlusterFS.

## Licencias y precios
{: #ibp-icp-license-pricing}
La plataforma {{site.data.keyword.blockchainfull_notm}} para {{site.data.keyword.cloud_notm}} privado consta de dos ediciones, una oferta de pago descargable desde Passport Advantage y una edición Community Edition gratuita que está disponible en [GitHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts").

### Licencia
{: #ibp-icp-license}

La plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} privado proporciona los componentes necesarios para ejecutar una red blockchain en su propia infraestructura y se despliega como una aplicación de ICP. Puede acceder a PPA y [descargar el diagrama de Helm](/docs/services/blockchain/howto/helm_install_icp.html). El soporte técnico de {{site.data.keyword.blockchainfull_notm}} viene incluido con la compra.

La edición Community Edition de la plataforma {{site.data.keyword.blockchainfull_notm}} en IBM Cloud privado es una oferta gratuita adecuada para la evaluación y experimentación y se despliega como una aplicación de ICP. No utilice Community Edition para producción. La plataforma {{site.data.keyword.blockchainfull_notm}} no proporciona soporte para Community Edition. Puede acceder al [paquete de GitHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") y descargar el diagrama de Helm.

### Precios
{: #ibp-icp-pricing}

Los precios de la plataforma {{site.data.keyword.blockchainfull_notm}} en ICP se basan en el número de núcleos de procesador virtual (VPC) utilizados. Un VPC puede ser un núcleo virtual que se asigna a un servidor virtual, o un núcleo de procesador físico en un servidor no particionado. Debe obtener una titularidad de licencia para cada VPC que se ponga a disposición para la plataforma {{site.data.keyword.blockchainfull_notm}}. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

Para obtener más información sobre cómo determinar su utilización de VPC, consulte este artículo sobre ["Núcleos de procesador virtual (VPC)" ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Núcleos de procesador virtual (VPC)") en IBM Knowledge Center. Puede utilizar la [Herramienta de métrica de licencias de IBM](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) para configurar y crear un informe que puede utilizar para determinar el número de VPC que necesita incluir en la licencia.


## Instalación de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP
{: #ibp-icp-install}

La plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} privado se entrega como un archivo de diagrama de Helm que se puede instalar en un clúster de ICP local. Después de instalar el diagrama de Helm, puede encontrar la plataforma {{site.data.keyword.blockchainfull_notm}} como una aplicación en el catálogo de ICP.

- La plataforma {{site.data.keyword.blockchainfull_notm}} para ICP se proporciona a través de Passport Advantage. Debe tener la licencia necesaria para acceder a [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html). Al realizar la compra, se incluye soporte técnico para la plataforma {{site.data.keyword.blockchainfull_notm}}.

- La edición Community Edition de la plataforma {{site.data.keyword.blockchainfull_notm}} está pensada para fines de exploración, desarrollo y pruebas. Se puede acceder a esta versión gratuita de la plataforma IBM Blockchain para ICP a través de GitHub. **Nota:** la plataforma {{site.data.keyword.blockchainfull_notm}} no proporciona soporte para Community Edition.

Para obtener instrucciones sobre cómo instalar el diagrama de Helm y los requisitos previos necesarios, consulte [Instalación de la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}} privado](/docs/services/blockchain/howto/helm_install_icp.html).

Si es un usuario nuevo de {{site.data.keyword.cloud_notm}} privado y desea información y sugerencias sobre cómo instalar y desplegar ICP, consulte [Configuración de {{site.data.keyword.cloud_notm}} privado](/docs/services/blockchain/ICP_setup.html).

Tras instalar la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP, deberá desplegar cada componente de la red individualmente. No puede desplegar varios componentes al mismo tiempo. Consulte la [Guía de despliegue de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html) para conocer los métodos recomendados para crear o unirse a una red blockchain antes de empezar. A continuación, revise los pasos para desplegar y trabajar con los componentes individuales en las secciones siguientes.

### Instalación de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP detrás de un cortafuegos
{: #ibp-icp-firewall}

Puede desplegar los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} detrás de un cortafuegos, sin tener que acceder a Internet público. El paquete del diagrama de Helm descargado incluye todas las imágenes de Docker de los componentes de Fabric que utiliza la plataforma {{site.data.keyword.blockchainfull_notm}}, sin obtenerlos de DockerHub durante el despliegue.

El paquete del diagrama de Helm de la edición Community Edition de la plataforma {{site.data.keyword.blockchainfull_notm}} no incluye las imágenes de Docker de los componentes de Fabric necesarios. Está configurado para descargar dichas imágenes desde DockerHub durante el despliegue y fallará si no tiene acceso a Internet público. No obstante, puede realizar algunos pasos adicionales para desplegar los componentes de Community Edition detrás de un cortafuegos. Para obtener más información, consulte
[Instalación de Community Edition detrás de un cortafuegos](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-prereqs-firewall)


## Acerca de las entidades emisoras de certificados en ICP
{: #ibp-icp-ca}

Las entidades emisoras de certificados (CA) proporcionan la identidad en la red. Una CA se puede considerar similar a un notario que actúa como punto de confianza entre varias partes. A todas las entidades de la red se les proporciona un certificado firmado por una CA raíz que encapsula su identidad digital. Este certificado es la raíz de la confianza para todas las operaciones de firma y verificación que se realizan en la red. Para obtener más información sobre las entidades emisoras de certificados y el papel que desempeñan en una red blockchain, consulte [Visión general de componentes de Blockchain](/docs/services/blockchain/blockchain_component_overview.html).

La CA validará la identidad y emitirá certificados para los demás componentes de la red que necesite desplegar. Como consecuencia, debe desplegar una CA para la organización antes de desplegar los demás componentes.

- Para aprender a configurar y a desplegar una CA después de instalar el diagrama de Helm, consulte [Despliegue de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} privado](/docs/services/blockchain/howto/CA_deploy_icp.html).

- Para obtener información sobre cómo utilizar la CA para generar certificados y completar los pasos de requisito previo para desplegar componentes adicionales, consulte [Funcionamiento de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} privado](/docs/services/blockchain/howto/CA_operate.html).

## Acerca de los clasificadores en ICP
{: #ibp-icp-orderer}

Los clasificadores autentican clientes, ordenan transacciones y difunden transacciones en una red blockchain. Constituyen el enlace común de las redes blockchain basadas en Hyperledger Fabric. Como consecuencia, la organización que crea una red debe desplegar e iniciar un "servicio de ordenación" (el nodo o recopilación de nodos que realizan la ordenación) para que las demás organizaciones puedan desplegar sus iguales, unirse a canales e iniciar transacciones en la red. Para obtener más información sobre los clasificadores y el papel que desempeñan en una red blockchain, consulte [Visión general de componentes de Blockchain](/docs/services/blockchain/blockchain_component_overview.html#orderer).

Si va a crear una red blockchain, necesitará desplegar un clasificador. Una vez que se haya desplegado, puede invitar a otras organizaciones a su consorcio, que luego podrán crear canales propios.

- Para obtener información sobre cómo configurar y desplegar un clasificador después de instalar el diagrama de Helm, consulte [Despliegue de un clasificador en ICP](/docs/services/blockchain/howto/orderer_deploy_icp.html).

- Para aprender a construir un consorcio, consulte [Funcionamiento de un clasificador en ICP](/docs/services/blockchain/howto/orderer_operate.html).

## Acerca de los iguales en ICP
{: #ibp-icp-peer}

Los iguales son un elemento fundamental de la red, ya que alojan libros mayores y contratos inteligentes. Los contratos inteligentes y los libros mayores se utilizan para encapsular los procesos compartidos y la información compartida en una red, respectivamente. Para obtener más información sobre los iguales y el papel que desempeñan en una red blockchain, consulte [Visión general de componentes de Blockchain](/docs/services/blockchain/blockchain_component_overview.html).

- Cuando esté listo para unirse a una red, puede desplegar un igual que se unirá a canales, aprobará transacciones y almacenará los libros mayores del canal. Para obtener información sobre cómo desplegar un igual en ICP que se conectará a otros componentes de ICP, consulte [Despliegue de un igual en ICP](/docs/services/blockchain/howto/peer_deploy_icp.html). Para obtener información sobre cómo desplegar un igual en ICP que se conectará a una red de Plan inicial o Plan empresarial, consulte [Despliegue de un igual que se conectará al Plan inicial o empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html).

- Después de configurar el igual, deberá realizar varios pasos operativos para poder enviar transacciones y leer el libro mayor distribuido de la red blockchain.

  - Si va a conectar el igual a una plataforma {{site.data.keyword.blockchainfull_notm}} desplegada en ICP, consulte [Funcionamiento de iguales en {{site.data.keyword.cloud_notm}} privado con una red multinube](/docs/services/blockchain/howto/peer_operate_icp.html).
  - Si va a conectar el igual a una red de Plan inicial o Plan empresarial desplegada en {{site.data.keyword.cloud_notm}}, consulte [Funcionamiento de iguales en {{site.data.keyword.cloud_notm}} privado con el Plan inicial o el Plan empresarial](/docs/services/blockchain/howto/peer_operate_ibp.html).

## Consideraciones sobre seguridad
{: #icp-security}

Debido a que estos componentes se despliegan fuera de {{site.data.keyword.cloud_notm}}, tendrá la responsabilidad de gestionar su seguridad. Esto incluye áreas importantes de seguridad, como la gestión de claves y el cifrado de datos. Examine los temas siguientes cuando tenga en cuenta la seguridad para los componentes.

### Seguridad de datos
{: #icp-security-data}

Los planes inicial y empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizan el cifrado de disco completo que se basa en el [cifrado de claves simétricas ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cifrado simétrico") para proteger todos los datos que utilizan las redes. Debe seguir pasos similares en su propio entorno para proteger los datos de los iguales.

Los datos de la base de datos de estado, independientemente de si utiliza LevelDB o CouchDB, no se cifran. Puede utilizar un cifrado a nivel de aplicación para proteger los datos en reposo en la base de datos de estado.

### Residencia de datos
{: #data-residency}

Los requisitos de residencia de datos pueden imponer que el proceso y el almacenamiento de todos los datos del libro mayor de blockchain permanezcan dentro de los límites de un solo país (o dentro de algún otro límite definido). Para obtener más información sobre cómo se puede conseguir la residencia de los datos, consulte [Residencia de los datos](#icp-data-residency).

### Gestión de claves
{: #icp-security-key-management}

La gestión de claves es un aspecto crítico de la seguridad. Si una clave privada se ve comprometida o se pierde, es posible que usuarios hostiles accedan a los datos y funciones. IBM utiliza dispositivos físicos conocidos como [Módulos de seguridad de hardware](/docs/services/blockchain/glossary.html#hsm) (HSM) para almacenar las claves privadas de las redes del Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Al desplegar un componente en ICP, usted es el responsable de gestionar las claves privadas. Aunque la plataforma {{site.data.keyword.blockchainfull_notm}} genera claves privadas, dichas claves no se almacenan en la plataforma. Es de vital importancia que almacene sus claves de forma segura para que no se vean comprometidas. Encontrará la clave privada del componente en la carpeta de almacén de claves de MSP del igual, en el directorio `/mnt/crypto/peer/peer/msp/keystore/` dentro del componente. Para obtener más información sobre los certificados del igual, consulte la sección [Proveedor de servicios de pertenencia](/docs/services/blockchain/certificates.html#msp) de la guía de aprendizaje [Gestión de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/certificates.html).

Puede utilizar Key Escrow para recuperar claves privadas perdidas. Esto hay que hacerlo antes de perder ninguna clave. Si una clave privada no se puede recuperar, tiene que obtener nuevas claves privadas registrando una nueva identidad con la entidad emisora de certificados. También debe eliminar y sustituir signCert de cualquier canal al que se haya unido.

### TLS
{: #icp-security-tls}

[La seguridad de capa de transporte ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una visión general del reconocimiento SSL o TLS") (TLS) está integrada en el modelo de confianza de Hyperledger Fabric. Todos los componentes del Plan inicial o empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizan TLS para autenticarse y comunicarse entre sí. Por lo tanto, los componentes de red de la plataforma deben ser capaces de completar un reconocimiento de TLS con sus iguales. Una implicación de este enfoque es que necesita activar el paso a través, usando por ejemplo una lista blanca, en el cortafuegos de las apps cliente a su igual.

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](/docs/services/blockchain/v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### Configuración del proveedor de servicios de pertenencia
{: #icp-security-MSP}

Los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} consumen identidades a través de proveedores de servicios de pertenencia (MSP). Los MSP asocian los certificados que emiten las entidades emisoras de certificados con roles de red y de canal. Para obtener más información sobre cómo funcionan los MSP con el igual, consulte [Proveedores de servicios de pertenencia (MSP)](/docs/services/blockchain/certificates.html#msp).

### Seguridad de las aplicaciones
{: #icp-security-appl}

Puesto que todas las invocaciones de código de encadenamiento están firmadas, Fabric gestiona la seguridad de las aplicaciones. Además, Fabric también incluye comprobaciones de nivel de aplicación basadas en ACL.

## Residencia de datos
{: #icp-data-residency}

Debido a que las redes blockchain no conocen qué tipo de datos se procesa, en ocasiones deben realizarse pasos adicionales para mantener seguros determinados tipos de datos. El requisito más común en cuanto a residencia de datos está asociado a las leyes de determinados países, según las cuales todos los datos que se procesan y almacenan en un sistema de TI deben permanecer dentro de las fronteras del país específico. Paralelamente, algunas empresas de sectores muy regulados, como las gubernamentales, de sanidad y de servicios financieros, obligan a que los datos se almacenen tras su cortafuegos. Por lo tanto, para conseguir la residencia de datos, todos los componentes de la red blockchain deben formar parte del mismo [canal](/docs/services/blockchain/glossary.html#channel) y deben residen dentro de las fronteras de un solo país.

Para hacer frente a los requisitos de residencia de datos, es importante comprender la arquitectura de Hyperledger Fabric subyacente a la plataforma {{site.data.keyword.blockchainfull_notm}}. La arquitectura se centra en torno a tres componentes clave: un servicio de ordenación (compuesto por clasificadores), entidades emisoras de certificados (CA) e iguales. Un igual recibe actualizaciones de estado ordenadas en forma de bloques desde el servicio de ordenación y mantiene el estado y el libro mayor. Por lo tanto, un igual y un servicio de ordenación tienen una relación directa. El libro mayor contiene los valores más recientes de todas las claves y los datos que incluyen los registros de transacciones.

Además, las aplicaciones cliente utilizan los [SDK de Fabric](/docs/services/blockchain/v10_application.html#using-the-fabric-sdks) para enviar transacciones a los iguales y al servicio de ordenación. Estas transacciones incluyen datos del [conjunto de lectura-escritura ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "semántica del conjunto de lectura-escritura"), que contienen los valores de clave-valor en el libro mayor.

Si la residencia de datos en el país es un requisito, el clasificador, el igual y las aplicaciones cliente deben residir en el mismo país. Cuando se crea una red de la plataforma {{site.data.keyword.blockchainfull_notm}} en {{site.data.keyword.cloud_notm}}, tiene la opción de seleccionar la ubicación de la red. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obtener más información sobre regiones y ubicaciones, consulte [Regiones y ubicaciones de la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/reference/ibp_regions.html).

### Un caso de uso para la residencia de datos

Tenga en cuenta una red de la plataforma {{site.data.keyword.blockchainfull_notm}} que incluye el clasificador y la entidad emisora de certificados junto con un consorcio de cuatro organizaciones. Las organizaciones tienen uno o más nodos de igual, las cuatro organizaciones forman parte de un único canal y todos los componentes de la red se encuentran en la región (por ejemplo, Frankfurt) donde se ha desplegado la red de la plataforma {{site.data.keyword.blockchainfull_notm}}. Por último, las aplicaciones cliente que interactúan con los iguales también residen en Alemania.

En este ejemplo, se mantiene la residencia de datos.

![Residencia de datos cuando todos los componentes se encuentran en el mismo país](images/remote_peer_data_res_1.png "Residencia de datos cuando todos los componentes se encuentran en el mismo país")
*Figura 1. Residencia de datos cuando todos los componentes se encuentran en el mismo país*

Ahora, tengamos en cuenta las implicaciones de que un **igual distribuido** se una a una de las organizaciones. Un igual distribuido puede residir en la misma región que el resto de la red o en cualquier lugar fuera de la región de la red de la plataforma
{{site.data.keyword.blockchainfull_notm}}:

-	Si el igual reside en el mismo país que el resto de la red, se mantiene la residencia de datos. Todos los datos del libro mayor permanecen dentro de Alemania, como indica la **Figura 1** anterior.
-	En caso de que el igual resida en un país distinto (como EE.UU. por ejemplo), ya no se mantiene la residencia de datos, pues los datos del libro mayor del igual se comparten fuera de la frontera del país.

Para resolver este problema, se pueden utilizar **canales** para aislar los datos en un subconjunto de iguales de la red. Cuando la red de la plataforma {{site.data.keyword.blockchainfull_notm}} contiene un igual distribuido y clasificadores entre fronteras, los canales proporcionan el aislamiento de los datos de libro mayor de organizaciones que tienen iguales fuera de la frontera del país.

**Nota:** las clasificadores siempre están ubicados en la región del centro de datos que haya seleccionado para alojar la red. No es posible tener varios clasificadores entre países. Sin embargo, los iguales pueden estar ubicados en el centro de datos o en una ubicación remota fuera de {{site.data.keyword.cloud_notm}}.

![Residencia de datos cuando los iguales residen fuera del país de la región de la plataforma IBM Blockchain](images/remote_peer_data_res_2.png "Residencia de datos cuando los iguales residen fuera del país de la región de la plataforma IBM Blockchain")
*Figura 2. Residencia de datos cuando los iguales residen fuera del país de la región de la plataforma IBM Blockchain*

En la **Figura 2**, no se requiere residencia de datos para `OrgC` y `OrgD`. De hecho,
`OrgD` incluye ahora dos iguales distribuidos, `OrgD-peer1` y `OrgD-peer2`, que residen en *Estados Unidos*. Por lo tanto, para que `OrgA`, `OrgB` y sus respectivas aplicaciones cliente e iguales que residen en Alemania puedan aislar los datos de libro mayor en el canal `X`, se crea un nuevo canal `Y` para
`OrgC` y `OrgD`.

Para estudiar con mayor detalle el flujo de datos en la red de la plataforma
{{site.data.keyword.blockchainfull_notm}}, consulte la
[documentación de Fabric sobre el flujo de transacciones
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Flujo de transacciones").

En el futuro, la nueva tecnología de Hyperledger Fabric mejorará la capacidad de obtener una mejor residencia de datos utilizando [Recopilaciones de datos privados ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Recopilaciones de datos privados") y Zero Knowledge Proof.

- Una recopilación de datos privados garantiza que los datos privados se comparten entre igual e igual (mediante el protocolo gossip) solo con los iguales que tienen autorización para verlos, por ejemplo iguales que están dentro de las fronteras del país. Los datos se almacenan en una base de datos privada en el igual. El servicio de ordenación no está implicado y no ve los datos privados. Se escribe un hash de esos datos en los libros mayores de cada igual del canal. El hash que se utiliza para la validación de estado sirve como prueba de la transacción y se puede utilizar para fines de auditoría. Los datos privados están disponibles para las redes de la plataforma {{site.data.keyword.blockchainfull_notm}} que se ejecutan en la versión 1.2.1 de Fabric. No obstante, la característica de datos privados no está disponible para los iguales que se ejecutan en ICP, ya que las redes de ICP no utilizan gossip (rumores) actualmente.

- Una ZKP (zero knowledge proof) permite que un "comprobador" asegure a un "verificador" que conoce un secreto sin tener que revelar el propio secreto.

Puede obtener más información sobre estas tecnologías en el documento técnico sobre [Transacciones privadas y confidenciales con Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transacciones privadas y confidenciales con Hyperledger Fabric").

## Obtención de soporte
{: #ibp-icp-support}

Para obtener información sobre las ofertas de soporte digital, consulte los
[recursos y foros de soporte](/docs/services/blockchain/ibmblockchain_support.html#resources) del soporte de la plataforma IBM Blockchain.

### Plataforma {{site.data.keyword.blockchainfull_notm}} para ICP

Si ha adquirido una licencia de la plataforma IBM Blockchain para ICP y desea ponerse en contacto con el servicio de atención al cliente, consulte la información sobre [cómo acceder a la comunidad de soporte de IBM y abrir una incidencia de soporte ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} - Soporte de la plataforma IBM Blockchain para ICP").

### Community Edition de la plataforma {{site.data.keyword.blockchainfull_notm}}

La edición Community Edition está pensada para exploración, desarrollo y pruebas. La plataforma IBM Blockchain no proporciona soporte para la edición Community Edition de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Si detecta algún problema relacionado con los componentes de blockchain, puede utilizar los recursos gratuitos de desarrollador de blockchain y los foros de soporte para obtener ayuda de la comunidad de Fabric y de IBM. Para obtener más información, consulte [Recursos de blockchain y foros de soporte](/docs/services/blockchain/ibmblockchain_support.html#resources).

Para los problemas relacionados con IBM Cloud privado, puede aprovechar el
[soporte digital gratuito y el soporte de pago que ofrece ICP](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us).

---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

keywords: Hyperledger Fabric, confidential channels, Membership Service Provider, Linux Foundation, SDKs, modular architecture, permissioned network

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Hyperledger Fabric
{: #hyperledger-fabric}

La red de {{site.data.keyword.blockchainfull}} se basa en la pila de Hyperledger Fabric, uno de los proyectos de blockchain en Hyperledger Project de Linux Foundation. Es una red "autorizada" donde todos los usuarios y componentes tienen identidades conocidas. La lógica sign/verify se implementa en cada punto de encuentro de la comunicación y las transacciones acceden a través de una serie de verificaciones de aprobación y validación. En este sentido, difiere mucho de las implementaciones de blockchain tradicionales que promueven el anonimato y se ven forzadas a depender de criptomonedas y obligaciones de cálculo intenso para validar las transacciones.
{:shortdesc}

Hyperledger Fabric ofrece una arquitectura modular para ampliar la escalabilidad y el rendimiento. En este tema se presentan algunos componentes clave de Hyperledger Fabric. Para ver una introducción completa de Hyperledger Fabric, consulte la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Iguales
{: #hyperledger-fabric-peer}

A nivel físico, una red blockchain consta principalmente de nodos de igual (o, simplemente, iguales). Los iguales son elementos fundamentales de la red porque alojan los libros mayores y los contratos inteligentes (contenidos en el ["código de encadenamiento" ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "espacio de nombres de código de encadenamiento")). Más exactamente, el igual aloja
**instancias** del libro mayor e **instancias** de contratos inteligentes. Debido a que los contratos inteligentes y los libros mayores se utilizan para encapsular los procesos compartidos y la información compartida en la red, respectivamente, estos aspectos de un igual los convierten en un buen punto de partida para entender lo que hace realmente una red de Fabric.

Para obtener más información sobre los iguales específicamente, consulte [este documento que se centra solo en los iguales ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/peers/peers.html) de la documentación de la comunidad de Fabric.

## Entidad
emisora de certificados
{: #hyperledger-fabric-certificate-authority}

Como plataforma para redes de blockchain **autorizadas**, Hyperledger Fabric incluye un componente **entidad emisora de certificados (CA)** para la gestión de las identidades de red de todas las organizaciones miembro y sus usuarios. El requisito de identidad autorizada para cada usuario habilita el control basado en ACL sobre la actividad de red y garantiza que los usuarios registrados puedan rastrear cada transacción.
* La CA emite un certificado raíz (**rootCert**) a cada **miembro** (organización o individual) con autorización para unirse a la red.
* La CA también emite un certificado de inscripción (**eCert**) a cada componente miembro, a aplicaciones del lado de servidor y ocasionalmente a usuarios.
* También se otorga a cada usuario inscrito una asignación de certificados de transacción (**tCerts**). Cada **tCert** autoriza una transacción de red.

Este control basado en certificados sobre la pertenencia a red y las acciones permite restringir que identidades de usuario específicas accedan a canales privados y confidenciales, aplicaciones y datos.

Para obtener más información sobre el componente de entidad emisora de certificados de Hyperledger Fabric, consulte la [guía del usuario de Fabric CA ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/){:new_window}.

## Proveedor de servicios de pertenencia
{: #hyperledger-fabric-membership-service-provider}

Hyperledger Fabric incluye un componente de **proveedor de servicios de pertenencia (MSP)** que ofrece una abstracción de todos los mecanismos de cifrado y protocolos tras la emisión y validación de certificados y la autenticación de usuario. El componente MSP se instala en cada igual de canal para garantizar que las solicitudes de transacción que se emiten al igual proceden de una identidad de usuario autorizada y autenticada.

Para obtener más información sobre el componente Hyperledger Fabric Membership Services Provider, consulte el tema sobre [pertenencia ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html){:new_window} en la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Servicio de ordenación
{: #hyperledger-fabric-ordering-service}

En otros blockchains distribuidos, como Ethereum y Bitcoin, no hay ninguna autoridad central que ordene las transacciones y las envíe a los iguales. Hyperledger Fabric, el blockchain en el que se basa {{site.data.keyword.blockchainfull_notm}} Platform, funciona de otra forma. Cuenta con un nodo denominado **clasificador**.

Los clasificadores son componentes clave de una red, ya que realizan algunas funciones esenciales:

- Literalmente **clasifican** los bloques de transacciones que se envían a los iguales para que se escriban en sus libros mayores, y este proceso se denomina "clasificación". Si estas transacciones se empaquetaran y se ordenaran en los propios iguales, aumentaría la posibilidad de que un igual escribiera una transacción en su libro mayor mientras otro igual no lo hiciera, lo que generaría una incoherencia de estados.
- Mantienen el **canal del sistema clasificador**, el lugar en el que reside el **consorcio**, la lista de organizaciones de iguales que tienen permiso para crear canales.
- Realizan importantes comprobaciones de validación de identidad. Por ejemplo, si una organización intenta crear un canal cuando no es miembro del consorcio del clasificador, la solicitud se denegará. Los clasificadores también validan los comportamientos en los canales de transacciones, como los permisos para cambiar la configuración de un canal.

Actualmente Hyperledger Fabric admite implementaciones del servicio de clasificación tanto SOLO (un nodo de clasificación) como basadas en Kafka. Para obtener más información sobre el servicio de ordenación de Hyperledger Fabric, consulte la información acerca de la [puesta en marcha de un servicio de ordenación basado en Kafka ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/kafka.html){:new_window} en la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Los SDK de Fabric
{: #hyperledger-fabric-fabric-sdks}

Los SDK de Hyperledger Fabric permiten a los desarrolladores de aplicaciones crear aplicaciones que interactúan con una red blockchain. Estos SDK permiten que las aplicaciones gestionen del ciclo de vida de los canales y del código de encadenamiento.

Hyperledger Fabric proporciona SDK tanto de Node.js como de Java y ofrece las funciones siguientes para la interacción con la red blockchain:

* Registrar e inscribir usuarios
* Crear canales
* Unir iguales a un canal
* Actualizar la configuración del canal de sistema o canal de aplicación
* Instalar código de encadenamiento en iguales
* Crear instancias de código de encadenamiento en un canal
* Actualizar el código de encadenamiento en un canal
* Invocar funciones de código de encadenamiento para actualizar el libro mayor
* Realizar consultar al libro mayor sobre transacciones, bloques o claves específicos
* Supervisar sucesos de un canal (por ejemplo, la correcta confirmación de una transacción)

Para obtener más información sobre los SDK de Fabric, consulte los [SDK de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/fabric-sdks.html){:new_window} en la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

## Flujo de transacciones
{: #hyperledger-fabric-transaction-flow}

Para garantizar la coherencia e integridad de los datos, Hyperledger Fabric implementa varios puntos de comprobación en todo el flujo de transacciones, incluidas la autenticación de cliente, aprobación, ordenación y confirmación en el libro mayor.

En la **Figura 1** se describe el flujo de transacciones de una red blockchain de Hyperledger Fabric: ![Flujo de transacciones](../images/v10_txflow.png "Flujo de transacciones de una red Hyperledger Fabric")
*Figura 1. Flujo de transacciones de una red Hyperledger Fabric*

En una red de Hyperledger Fabric, el flujo de datos para consultas y transacciones se inicia desde una aplicación del lado de cliente mediante el envío de una solicitud de transacción a un igual de un canal. El flujo de datos inicial a través de la red es común para consultas y transacciones:

1. Mediante las API disponibles en el SDK, una aplicación cliente firma y envía una propuesta de transacción a los iguales de aprobación adecuados en el canal especificado. Esta propuesta de transacción inicial es una **solicitud** de aprobación.
2. Cada igual del canal verifica la identidad y la autoridad del cliente que realiza el envío y (si es válido) ejecuta el código de encadenamiento especificado en las entradas proporcionadas. Según los resultados de la transacción y de la política de aprobación del código de encadenamiento invocado, cada igual devuelve a la aplicación una respuesta YES o NO firmada. Cada respuesta YES firmada es una **aprobación** de la transacción.

	En este punto del flujo de transacción, el proceso difiere de las consultas y transacciones. Si la propuesta ha invocado una función de consulta en el código de encadenamiento, la aplicación devuelve los datos al cliente. Si la propuesta ha invocado una función en el código de encadenamiento para actualizar el libro mayor, la aplicación continúa con los pasos siguientes:
3. La aplicación reenvía la transacción, que incluye el conjunto de lectura/escritura y las aprobaciones, al **servicio de ordenación**.
4. Luego la transacción se retransmite al servicio de ordenación. Todos los iguales del canal validan las transacciones del bloque mediante la aplicación de la Política de validación específica del código de encadenamiento y la ejecución de la comprobación de versiones de control de simultaneidad.
	* Las transacciones que no superan el proceso de validación se marcan como no válidas en el bloque y el bloque se añade al libro mayor del canal.
	* Todas las transacciones válidas actualizan la base de datos de estado según los pares clave/valor modificados.

El **protocolo de rumores ("gossip") de diseminación de datos** difunde continuamente los datos del libro mayor a todo el canal para garantizar la sincronización de los libros mayores de los iguales. Para más detalles, consulte información sobre el [protocolo de rumores ("gossip") diseminación de datos ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html){:new_window} en la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

Para obtener una introducción paso a paso sobre el flujo de las transacciones, consulte [Flujo de transacciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html){:new_window} en la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/){:new_window}.

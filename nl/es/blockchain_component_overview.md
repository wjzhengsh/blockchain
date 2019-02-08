---

copyright:
  years: 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Visión general de componentes de Blockchain

Los componentes y la estructura de la plataforma {{site.data.keyword.blockchainfull}} (IBP) se basan en infraestructura y las herramientas subyacentes de [Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), una solución blockchain autorizada de código abierto para la que {{site.data.keyword.IBM_notm}} es un colaborador principal. Las redes basadas en Fabric incluyen varios componentes estándares que se pueden desplegar en una serie de configuraciones para dar soporte a una amplia variedad de casos de uso.

Para obtener una vista más completa de las redes de Fabric y de la interrelación de los componentes que la forman, consulte
[este documento sobre la estructura de una red blockchain](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) en la documentación de la comunidad de Fabric, que muestra cómo se puede iniciar y desarrollar una red.

Para obtener una visión general de alto nivel de los componentes de una red basada en Fabric, mire el siguiente vídeo:

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeos del plan inicial" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Aunque este vídeo se centra en los componentes desde la perspectiva de las redes de Plan inicial o empresarial, la información sigue siendo adecuada en gran medida para la solución gestionada por el cliente de IBP en ICP (plataforma IBM Cloud).*

Para los fines de esta visión general, nos centraremos solo en las entidades emisoras de certificados (CA), clasificadores, iguales, contratos inteligentes y aplicaciones. Como puede ver en el tema [Guía de despliegue de la plataforma {{site.data.keyword.blockchainfull_notm}} para ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html), esta secuencia no es arbitraria; refleja la secuencia en la que desplegarán los componentes de una red basada en Fabric.

## Entidades emisoras de certificados
{: #ca}

Los puntales de una red blockchain basada en Fabric son las identidades y permisos. Las identidades toman la forma de certificados x.509 que emite una CA y son como una tarjeta de crédito en el sentido en que *identifican* a alguien, y pueden incluir atributos sobre ellos. Estos certificados se enlazan luego con permisos mediante su inclusión en carpetas de MSP a nivel de componente o a nivel de canal. Así, por ejemplo, un MSP de igual tendrá una subcarpeta de MSP denominada **admins**. Cualquier usuario cuyo certificado esté dentro de dicha carpeta admin será un administrador del igual, lo que implica que tendrá la capacidad de realizar cualquier acción que el administrador de dicho igual tenga permitido realizar. Un sistema de validación dentro del igual realiza una comprobación siempre que un usuario, identificado por su certificado de firma, intente realizar una acción administrativa. ¿Coincide el certificado con el de la carpeta "admin"? Si es así, se puede realizar la acción. Si no es así, se rechazará la solicitud para realizar la acción.

Las CA de {{site.data.keyword.blockchainfull_notm}} se basan en la
[CA de Hyperledger Fabric](https://hyperledger-fabric-ca.readthedocs.io/en/latest/), aunque es posible utilizar otra CA siempre que utilice una PKI (infraestructura de claves públicas) basada en certificados x.509. Puede haber, y normalmente debe ser así, varios niveles de CA. La "CA raíz" de una red no estará expuesta normalmente, excepto para proporcionar certificados a las "CA intermedias", que emitirán certificados a usuarios y componentes directamente, o a más capas de CA intermedias. Para obtener más detalles sobre cómo se utilizan las entidades emisoras de certificados para establecer la identidad y la pertenencia, consulte la
[documentación de Hyperledger Fabric sobre la identidad
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html) y sobre la
[pertenencia
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html)

## Clasificadores
{: #orderer}

Aunque el servicio de ordenación se conoce con frecuencia como el "corazón" de la red, su función es bastante simple en realidad: clasificar las transacciones en bloques y enviarlas de vuelta a los iguales para que las escriban en sus libros mayores. En versiones anteriores de Fabric, esta funcionalidad se ha empaquetado dentro del igual, pero a partir de Fabric v1.0, se ha separado en un componente independiente para aumentar el rendimiento del igual y los desvíos que puedan resultar en bifurcaciones de estado potenciales.

A nivel físico, esta función de ordenación requiere habitualmente un conjunto de clasificadores que se conoce colectivamente como "servicio de ordenación", aunque en entornos de prueba o pruebas de concepto es posible utilizar un único nodo (conocido como clasificador SOLO).

## Igual
{: #peer}

A nivel físico, una red blockchain consta principalmente de nodos de igual (o, simplemente, iguales). Los iguales son un elemento fundamental de la red, ya que alojan libros mayores y contratos inteligentes (incluidos en los "códigos de encadenamiento"). Más exactamente, el igual aloja
**instancias** del libro mayor e **instancias** de contratos inteligentes. Debido a que los contratos inteligentes y los libros mayores se utilizan para encapsular los procesos compartidos y la información compartida en la red, respectivamente, estos aspectos de un igual los convierten en un buen punto de partida para entender lo que hace realmente una red de Fabric.

Para obtener más información sobre los iguales específicamente, consulte
[este documento que se centra solo en los iguales](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) en la documentación de la comunidad de Fabric.

## Contratos inteligentes

Para que las empresas puedan realizar transacciones unas con otras, primero debe definirse y llegarse a un acuerdo sobre las reglas y los procesos en algún tipo de contrato. En conjunto, estos contratos establecen el "modelo de negocio" que rige todas las interacciones entre socios comerciales.

La misma necesidad existe en las redes blockchain, donde el término del sector para estos modelos de negocio es "contratos inteligentes". Fabric (e IBP) contienen estos contratos en una estructura mayor conocida como "código de encadenamiento", que incluye, no solo la lógica empresarial, sino también la infraestructura subyacente que valida las identidades de los usuarios que intentan invocar el contrato inteligente.

Cuando los contratos del mundo empresarial se firman y se rellenan con firmas legales, se instalan contratos inteligentes en los iguales y se "crea una instancia" en un canal.

## Aplicaciones

Las aplicaciones cliente de una red basada en Fabric, como IBP, aprovechan infraestructuras subyacentes como las API, los SDK y los contratos inteligentes para permitir interacciones de clientes (invocaciones y consultas) a un mayor nivel de abstracción.

Para ver cómo interactúan las aplicaciones con una red basada en Fabric, consulte la
[documentación sobre cómo escribir su primera aplicación
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/master/write_first_app.html "Cómo escribir su primera aplicación") y la
[sección de desarrollo de aplicaciones
![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/master/developapps/developing_applications.html "Desarrollo de aplicaciones").

Tenga en cuenta que este último enlace hace referencia a las funciones de Fabric, como la clase
`Contract`, que no son compatibles con Fabric v1.2, que es en lo que se basan el Plan inicial y el release de IBP en ICP. Sin embargo, la sección contiene información conceptual y otras sugerencias útiles que valen la pena tener en cuenta al desarrollar aplicaciones que vayan a interactuar con componentes de Fabric v1.2.

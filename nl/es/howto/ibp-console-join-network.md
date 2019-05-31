---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: getting started tutorials, create a CA, enroll, register, create an MSP, wallet, create a peer, create ordering service, Raft, join a network, system channel

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Guía de aprendizaje sobre cómo unirse a una red
{: #ibp-console-join-network}

{{site.data.keyword.blockchainfull}} Platform es una oferta de tipo blockchain-as-a-service que le permite desarrollar, desplegar y trabajar con redes y aplicaciones blockchain. Puede obtener más información sobre los componentes de blockchain y sobre cómo funcionan juntos en la [visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Esta guía de aprendizaje es la segunda parte de la [serie de guías de aprendizaje de red de ejemplo](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) y en ella se describe cómo crear nodos en la consola de {{site.data.keyword.blockchainfull_notm}} Platform y cómo conectarlos a un consorcio de blockchain alojado en otro clúster.
{:shortdesc}

Si utiliza la versión de prueba beta de {{site.data.keyword.blockchainfull_notm}} Platform, es probable que algunos paneles de la consola no coincidan con la documentación actual, que se mantiene actualizada con la instancia de servicio con disponibilidad general (GA). Para obtener las ventajas de todas las funciones más recientes, en este momento se recomienda que suministre una nueva instancia de servicio de GA siguiendo las instrucciones de [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.  

Si aún no ha desplegado {{site.data.keyword.blockchainfull_notm}} Platform, consulte [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Puede crear un nuevo clúster de Kubernetes para los componentes de blockchain o puede utilizar un clúster existente en su cuenta de {{site.data.keyword.cloud_notm}}. Después de desplegar {{site.data.keyword.blockchainfull_notm}} Platform, puede iniciar la consola para crear y gestionar los componentes de blockchain.

Tanto si realiza el despliegue en un clúster de Kubernetes de pago como si lo hace en uno gratuito, utilice el panel de control de Kubernetes para descubrir los recursos disponibles cuando elija desplegar nodos y crear canales. Es su responsabilidad gestionar el clúster de Kubernetes y desplegar recursos adicionales si es necesario. Aunque los componentes se desplegarán correctamente en un clúster gratuito, cuantos más componentes añada más lenta será su ejecución. Para obtener más información sobre el dimensionamiento de los componentes y cómo interactúa la consola con el clúster Kubernetes, consulte [Asignación de recursos](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction).
{: note}

## Serie de guías de aprendizajes de red de ejemplo
{: #ibp-console-join-network-structure}

Esta serie de guías de aprendizaje de tres partes le guía por el proceso de creación e interconexión de una red Hyperledger Fabric de varios nodos relativamente sencilla utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 para desplegar una red en el clúster de Kubernetes e instalar y crear una instancia de un contrato inteligente.

* La [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) le ayuda en el proceso de alojar una red mediante la creación de un servicio de ordenación y un igual.
* **Cómo unirse a una red** En esta guía de aprendizaje se explica el proceso de unirse a una red existente creando un igual y uniéndolo a un canal.
* La guía para [Desplegar un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) contiene información sobre cómo escribir un contrato inteligente y cómo desplegarlo en la red.

Puede seguir los pasos de estas guías de aprendizaje para crear una red con varias organizaciones en un clúster con fines de desarrollo y prueba. Utilice la guía de aprendizaje sobre cómo **crear una red** si desea formar un consorcio de blockchain mediante la creación de un servicio de ordenación y la adición de organizaciones. Utilice la guía de aprendizaje sobre cómo **unirse a una red** para conectar un igual a la red. Si sigue las guías de aprendizaje con distintos miembros del consorcio puede crear una red blockchain verdaderamente **distribuida**.  


Esta guía de aprendizaje está pensada para mostrar cómo unir un igual a una red **existente**. Se supone que ya existe un servicio de ordenación, que aloja todos los canales y el consorcio, en el clúster o en otro clúster de {{site.data.keyword.blockchainfull_notm}} Platform. Si no dispone de una red existente a la que unirse, visite [Creación de una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) para aprender a crear una. La guía de aprendizaje sobre cómo **unirse a una red** le guía por el proceso de crear los siguientes componentes de blockchain de `Org2`, resaltados en el recuadro azul: ![Estructura para unirse a una red](../images/ib2-join-network.png "Estructura para unirse a una red")  
*Figura 1. Estructura de unión de red*  
Siga los pasos de la guía de aprendizaje sobre cómo **unirse a una red** para crear los siguientes componentes y realizar las siguientes acciones:

* **Una organización igual** `Org2`  
  Cree la definición del proveedor de servicios de pertenencia (MSP) de Org2, la cual define la organización `Org2`.
* **Un igual** `Org2 igual`   
  El libro mayor, `Ledger x` en la ilustración anterior, se mantiene mediante iguales distribuidos. El igual se despliega con [Couch DB ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) como base de datos de estado.
* **Una autoridad emisora de certificados (CA)** `CA de Org2`
  Una CA es el nodo que emite certificados a todos los administradores de la organización así como a los nodos asociados con una organización. Crearemos una CA para la organización igual `Org2`.
* **Unión de un canal**
  En la guía de aprendizaje se describe cómo unir el canal creado por la guía de aprendizaje [Crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

En esta guía de aprendizaje, suministramos **valores recomendados** para algunos de los campos de la consola. Esto permite reconocer más fácilmente los nombres e identidades en los separadores y listas desplegables. Estos valores no son obligatorios, pero los encontrará útiles, más aún teniendo en cuenta que deberá recordar determinados valores, especialmente los ID y secretos de usuarios registrados que haya especificado en pasos anteriores. Si olvida estos valores, tendrá que registrar usuarios adicionales. Ofrecemos una tabla de valores recomendados tras cada tarea y recomendamos que, si no utiliza los valores de ejemplo, registre los valores que utilice en algún lugar a medida que avanza por la guía de aprendizaje.
{:tip}

## Paso uno: crear una organización adicional y su punto de entrada al blockchain
{: #ibp-console-join-network-create-ca-org2}

Para cada organización que desee crear mediante la consola, debe desplegar al menos una CA. Una CA es el nodo que emite certificados a todos los participantes en la red (iguales, servicios de ordenación, clientes, administradores, etc.). Estos certificados, que incluyen un certificado para firmas y una clave privada, permiten que los participantes de la red se comuniquen, se autentiquen y, en última instancia, realicen transacciones. Estas CA crearán todas las identidades y certificados que pertenecen a su organización, además de definir la propia organización. A continuación, puede utilizar estas identidades para desplegar nodos, crear identidades de administrador y enviar transacciones. Para obtener más información sobre la CA y las identidades que tendrá que crear, consulte [Gestión de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

En esta guía de aprendizaje, crearemos una organización. Por lo tanto, necesitaremos crear **una CA**.

### Creación de la entidad emisora de certificados de la organización igual
{: #ibp-console-join-network-create-CA-org2CA}

Como parte de esta guía de aprendizaje, su CA emite los certificados y claves privadas para sus usuarios y nodos. Estas identidades no están gestionadas por {{site.data.keyword.IBM_notm}} y las claves no se almacenan en la consola. Solo se almacenan en el almacenamiento local del navegador. Por lo tanto, asegúrese de exportar sus identidades y el MSP de la organización. Si intenta acceder a la consola desde una máquina distinta o desde otro navegador, deberá importar estas identidades y definiciones de organización.
{:important}

Siga los pasos siguientes desde la consola:  

1. Vaya a separador **Nodos** de la izquierda y pulse **Añadir entidad emisora de certificados**. Los paneles laterales le permitirán personalizar la CA que desea crear y la organización para la que esta CA emitirá claves.
2. Pulse **{{site.data.keyword.cloud_notm}}** en **Crear entidad emisora de certificados** y pulse **Siguiente**.
3. Utilice el segundo panel lateral para dar a la CA un **nombre de visualización**. El valor recomendado para esta CA es `CA de Org2`.
4. En el siguiente panel, proporcione sus credenciales de administrador de CA especificando un **ID de inscripción de administrador de CA** de `admin` y un secreto de `adminpw`. Una vez más, estos son los **valores recomendados**.
5. Si utiliza un clúster de pago, tiene la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, acepte todos los valores predeterminados y pulse **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, pasará directamente a la página **Resumen**.
6. Revise la página Resumen y luego pulse **Añadir entidad emisora de certificados**.

**Tarea: creación de la CA de la organización igual**

  | **Campo** | **Nombre de visualización** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crear CA** | CA de Org2  | admin | adminpw |

*Figura 2. Creación de la CA de la organización igual*  

Después de desplegar la CA, la utilizará cuando cree el MSP de la organización, registre usuarios y cree su punto de entrada en una red, el **igual**.

Es posible que los usuarios avanzados tengan ya su propia CA y que no deseen crear una nueva CA en la consola. Si la CA existente puede emitir certificados en formato `X.509`, puede utilizar su propia CA de terceros en lugar de crear una nueva aquí. Consulte este tema sobre la [Utilización de una CA de terceros con su igual o servicio de ordenación](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-identities) para obtener más información.

### Utilización de la CA para registrar identidades
{: #ibp-console-join-network-use-CA-org2}

Cada nodo o aplicación que desee crear necesita certificados y claves privadas para participar en la red blockchain. También tiene que crear identidades de administración para estos nodos y aplicaciones para que pueda gestionarlos desde la consola. Pasaremos por este proceso dos veces, una para cada CA que creemos. Y, para cada CA, creará dos identidades:

* **Un administrador de la organización**: esta identidad le permite trabajar con nodos utilizando la consola de la plataforma.
* **Una identidad de igual**: esta es la identidad del propio igual. Siempre que un igual realice una acción (por ejemplo, aprobar una transacción), la firmará utilizando este certificado.

En función de su tipo de clúster, el despliegue de la CA puede tardar hasta diez minutos. Cuando la CA se despliegue por primera vez (o cuando la CA no esté disponible), el recuadro en el mosaico de la CA estará en color gris. Cuando la CA se haya desplegado correctamente y esté en ejecución, el recuadro estará en color verde, indicando que está "En ejecución" y que se puede utilizar para registrar identidades. Antes de continuar con los pasos siguientes para registrar identidades, debe esperar a que el estado de la CA sea "En ejecución".
{:important}

Una vez que la CA esté en ejecución, tal como lo indica el recuadro verde del mosaico, genere estos certificados realizando los pasos siguientes:

1. Pulse sobre `CA de Org2` y asegúrese de que la identidad `admin` que ha creado para la CA sea visible en la tabla. A continuación, pulse el botón **Registrar usuario**.
2. En primer lugar, registraremos el administrador de la organización, lo cual se puede hacer proporcionando un **ID de inscripción** de `org2admin` y un **secreto** de `org2adminpw`. A continuación, establezca el `Tipo` de esta identidad en `cliente` (las identidades de administrador siempre se deben registrar como `cliente`, mientras que las identidades de nodo siempre se deben registrar utilizando el tipo `igual`). El campo de afiliación es para usuarios avanzados y no se utiliza en la guía de aprendizaje, por lo tanto, pulse el recuadro que indica **Utilizar afiliación raíz**. Si desea obtener más información sobre cómo utiliza la CA de Fabric las afiliaciones, consulte este tema en [Registro de una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Por ahora, seleccione cualquier afiliación de la lista (por ejemplo, `org1`). Además, no preste atención al campo **Inscripciones máximas**. Si desea obtener más información sobre las inscripciones, consulte [Registro de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Pulse **Siguiente**.
4. Para los fines de esta guía de aprendizaje, no es necesario utilizar **Añadir atributo**. Si desea obtener más información sobre los atributos de identidad, consulte [Registro de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
5. Una vez que se haya registrado el administrador de la organización, repita este mismo proceso para la identidad del igual (utilizando también `CA de Org2`). Para la identidad del igual, proporcione un ID de inscripción de `peer2` y un secreto de `peer2pw`. Se trata de una identidad de nodo, de modo que seleccione `igual` como **Tipo**. Pulse el recuadro que indica **Utilizar afiliación raíz** y no tenga en cuenta **Inscripciones máximas**. A continuación, en el siguiente panel, no asigne ningún **Atributo**, igual que antes.

El registro de estas identidades con la CA es solo el primer paso para **crear** una identidad. No podrá utilizar estas identidades hasta que se hayan **inscrito**. Para la identidad `org2admin`, esto se producirá durante la creación del MSP, que veremos en el siguiente paso. En el caso del igual, esto ocurre durante la creación del igual.
{:note}

**Tarea: registrar usuarios**

  |  **Campo** | **Descripción** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registrar usuarios** |  Admin de Org2 | org2admin | org2adminpw |
  | | Identidad del igual |  peer2 | peer2pw |

*Figura 3. Utilización de su CA para registrar usuarios*  

### Creación del MSP de la organización igual
{: #ibp-console-join-network-create-peers-org2}

Ahora que hemos creado la CA del igual y la hemos utilizado para **registrar** las identidades de nuestra organización, tenemos que crear una definición formal de la organización igual, que se conoce como la definición de Proveedor de Servicios de pertenencia (MSP). Muchos iguales pueden pertenecer a una organización. **No es necesario que cree una nueva organización cada vez que cree un igual.** Como esta es la primera vez que revisamos la guía de aprendizaje, crearemos el ID de MSP para esta organización. Durante el proceso de creación del MSP, vamos a generar certificados para la identidad `org2admin` y los vamos a añadir a nuestra cartera.

1. Vaya al separador **Organizaciones** en el panel de navegación izquierdo y pulse **Crear definición de MSP**.
2. Asigne a su MSP el nombre de visualización `MSP de Org2` y el ID de MSP `org2msp`. Si desea especificar su propio ID de MSP en este campo, asegúrese de seguir las especificaciones de la herramienta de sugerencias sobre las limitaciones de este nombre.
3. En **Detalles de la entidad emisora de certificados raíz**, especifique la CA que ha utilizado para registrar las identidades en el paso anterior. Si esta es su primera vez que examina esta guía de aprendizaje, solo debería ver una: `CA de Org2`.
4. Los campos **ID de inscripción** y **Secreto de inscripción** bajo la misma contendrán el ID y el secreto de inscripción del primer usuario que ha creado con la CA: `admin` y `adminpw`. No obstante, el uso de esta identidad haría que la organización tuviera la misma identidad que su identidad de CA, lo que no se recomienda por motivos de seguridad. En su lugar, seleccione el ID de inscripción que ha creado para el administrador de la organización en la lista desplegable, `org2admin`, y especifique su secreto asociado, `org2adminpw`. A continuación, asigne a esta identidad un nombre de visualización, `Admin de Org2`.
5. Pulse el botón **Generar** para inscribir esta identidad como administrador de la organización y exporte la identidad a la cartera, donde se utilizará cuando se cree el igual y cuando se creen canales.
6. Pulse **Exportar** para exportar los certificados de administrador al sistema de archivos. Como se ha dicho anteriormente, esta identidad no se almacena en el clúster ni la gestiona {{site.data.keyword.IBM_notm}}. Se almacena únicamente en el almacenamiento del navegador local. Si cambia de navegador, deberá importar esta identidad en su cartera para poder administrar el igual.
7. Pulse **Crear definición de MSP**.

**Tarea: crear la definición de MSP de la organización igual**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción**  | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear organización** | MSP de Org2 | org2msp |||
  | **CA raíz** | CA de Org2 ||||
  | **Cert de administrador de la org** | |  | org2admin | org2adminpw |
  | **Identidad** | Admin de Org2 |||||

  *Figura 4. Crear la definición de MSP de la organización igual*  

Una vez que haya creado el MSP, debe poder ver el administrador de la organización igual en la **cartera**, a la que se puede acceder pulsando sobre **Cartera** en la navegación de la izquierda.

**Tarea: comprobar la cartera**

  | **Campo** |  **Nombre de visualización** | **Descripción** |
  | ------------------------- |-----------|----------|
  | **Identidad** | Admin de Org2 | Identidad de Org2 |

  *Figura 5. Comprobar la cartera*  

Para obtener más información sobre los MSP, consulte [Gestión de organizaciones](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Es importante exportar la identidad del administrador de la organización porque usted es el responsable de gestionar y proteger estos certificados. Si exporta el servicio de ordenación y la definición de MSP del servicio de ordenación, se pueden importar en otra consola en la que otro operador pueda crear nuevos canales en el servicio de ordenación o unir iguales al canal.
{:important}

### Creación de un igual
{: #ibp-console-join-network-peer-create}

Después de [crear una CA](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), de utilizarla para registrar identidades y de crear el [MSP de la organización igual](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), está listo para crear un igual.

#### ¿Qué rol juegan los iguales?
{: #ibp-console-join-network-peer-role}

Es importante recordar que las propias organizaciones no mantienen libros mayores. Los iguales sí lo hacen. Las organizaciones también utilizan iguales para firmar propuestas de transacciones y para aprobar actualizaciones de configuraciones de canal. Como el hecho de tener al menos dos iguales en un canal hace que esté altamente disponible, se recomienda tener al menos dos iguales unidos a un canal para implementaciones de nivel de producción. En esta guía de aprendizaje, solo mostraremos el proceso para crear un único igual.

Desde una perspectiva de asignación de recursos, es posible unir los mismos iguales a varios canales. El diseño del igual garantiza que los datos procedentes de un canal no pueden pasar a otro a través del igual. Sin embargo, debido a que el igual almacenará un libro mayor independiente para cada canal, es necesario asegurarse de que el igual tiene suficiente potencia de proceso y almacenamiento para manejar la transacción y la carga de datos.

#### Despliegue del igual
{: #ibp-console-join-network-deploy-peer-role}

Utilice la consola para seguir los pasos siguientes:

1. En la página **Nodos**, pulse **Añadir igual**.
2. Pulse {{site.data.keyword.cloud_notm}} en **Crear un nuevo igual** y pulse **Siguiente**.
3. Asigne a su igual el **Nombre de visualización** `Org1 igual`.
4. En la pantalla siguiente, seleccione `CA de Org2`, ya que esta es la CA que ha utilizado para registrar la identidad del igual. Seleccione el **ID de inscripción** de la identidad de igual que ha creado para el igual en la lista desplegable, `peer2`, y especifique su **secreto** asociado, `peer2pw`. A continuación, seleccione `MSP de Org2` en la lista desplegable y pulse **Siguiente**.
5. El siguiente panel lateral solicita la información de la CA de TLS. Cuando haya creado la CA, se habrá creado una CA de TLS junto a ella. Esta CA se utiliza para crear certificados para la capa de comunicación segura para los nodos. Por lo tanto, seleccione el **ID de inscripción** de la identidad de igual que haya creado para el igual en la lista desplegable, `peer2`, y especifique el **secreto** asociado, `peer2pw`. El **Nombre de host de CSR de TLS** es una opción disponible para los usuarios avanzados que deseen especificar un nombre de dominio personalizado que se puede utilizar para direccionar el punto final del igual. Los nombres de dominio personalizados no forman parte de esta guía de aprendizaje, por lo que debe dejar el **Nombre de host de CSR de TLS** en blanco por ahora.
6. El siguiente panel lateral le solicita **Asociar una identidad** para convertirla en el administrador del igual. Para los fines de esta guía de aprendizaje, haga que el administrador de su organización, `Admin de Org2`, sea también el administrador del igual. Es posible registrar e inscribir una identidad distinta con `CA de Org2` y convertir dicha identidad en el administrador del igual, pero esta guía de aprendizaje utiliza la identidad `Admin de Org2`.
7. Si utiliza un clúster de pago, en el panel siguiente, tendrá la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, puede aceptar todos los valores predeterminados y pulsar **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, podrá ver la página **Resumen**.
8. Revise el resumen y pulse **Añadir igual**.

**Tarea: despliegue de un igual**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear igual** | Org2 igual | org2msp |||
  | **CA** | CA de Org2 ||||
  | **Identidad de igual** | |  | peer2 | peer2pw |
  | **Certificado de administrador** | org2msp ||||
  | **CA de TLS** | CA de Org2 ||||
  | **ID de CA de TLS** | || admin | adminpw |
  | **Asociar identidad** | Admin de Org2 |||||

  *Figura 6. Despliegue de un igual*  

En un escenario de producción, se recomienda desplegar tres iguales en cada canal. Eso es para permitir que un igual pueda estar inactivo (por ejemplo, durante un ciclo de mantenimiento) y seguir teniendo iguales de alta disponibilidad. Para desplegar más de un igual para una organización, utilice la misma CA que ha utilizado para registrar la primera identidad de igual. En esta guía de aprendizaje, esto sería `CA de Org2`. A continuación, registre una nueva identidad de igual utilizando un ID de inscripción y un secreto distintos. Por ejemplo, `org2secondpeer` y `org2secondpeerpw`. A continuación, al crear el igual, proporcione este ID de inscripción y este secreto. Debido a que este igual sigue estando asociado a Org2, elija `CA de Org2`, `MSP de Org2` y `Admin de Org2` en las listas desplegables. Puede optar por proporcionar a este nuevo igual un administrador distinto, que se puede registrar e inscribir con `CA de Org2`, pero esto es opcional. Esta serie de guías de aprendizaje solo mostrarán el proceso para crear un igual individual para cada organización de igual.
{:tip}

## Paso dos: añadir su organización a la lista de organizaciones que pueden realizar transacciones
{: #ibp-console-join-network-add-org2}

Como ya hemos observado anteriormente, una organización igual debe ser conocida por el servicio de ordenación para poder crear o unirse a un canal (esto se conoce también como unirse al "consorcio", la lista de organizaciones conocidas por el servicio de ordenación). Esto se debe a que los canales son, a nivel técnico, **vías de acceso de mensajería** entre iguales a través del servicio de ordenación. Del mismo modo que un igual se puede unir a varios canales sin que la información pase de un canal a otro, un servicio de ordenación puede tener varios canales que se ejecutan a través del mismo sin exponer datos a organizaciones de otros canales.

Debido a que únicamente los administradores del servicio de ordenación pueden añadir organizaciones iguales al consorcio, necesitará:

* **Ser** el administrador del servicio de ordenación. En ese caso, puede añadir la organización igual que haya creado al consorcio directamente.
* **Enviar** información de MSP al administrador del servicio de ordenación, que añadirá su organización al consorcio.

En el siguiente paso, mostraremos cómo añadir una organización igual que haya creado a un consorcio bajo su control. Para aprender a exportar su organización en una consola que aloja un servicio de ordenación consulte [Exportar la información de la organización](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote) a continuación.

### Añadir la organización del igual al servicio de ordenación en mi consola
{: #ibp-console-join-network-add-org2-local}

**Siga estos pasos únicamente si la consola ya incluye el servicio de ordenación y el canal que desea unir.** De lo contrario, continúe en [Exporta la información de la organización](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

Puesto que solo los administradores del servicio de ordenación pueden añadir organizaciones iguales al consorcio, tendrá que ser el administrador del servicio de ordenación, lo que significa que tiene la identidad admin de la organización del servicio de ordenación en la cartera.

1. Vaya al separador **Nodos**.
2. Desplácese hasta el servicio de ordenación que desea utilizar y pulse en el mismo para abrirlo.
3. En **Miembros del consorcio**, pulse **Añadir organización**.
4. En la lista desplegable, seleccione `MSP de Org2`, ya que este es el MSP que representa a `org2`.
5. Pulse **Añadir organización**.

Ahora añada la organización igual al canal.
1. Vaya al separador **Canales** y pulse `channel1`.
2. Pulse el icono **Valores** para actualizar el canal y añadir la organización igual al canal.
3. En la lista desplegable **Elegir entre los servicios de ordenación disponibles**, asegúrese de que `Servicio de ordenación` esté seleccionado.
4. En la lista desplegable **ID de MSP de actualizador de canal**, asegúrese de que `MSP de Org1` está seleccionado.
5. En la lista desplegable **Asociar identidad disponible**, asegúrese de que `Admin de Org1` está seleccionado.
6. Desplácese hacia abajo hasta la sección llamada `Añadir organizaciones al canal`, abra la lista desplegable `Seleccionar un miembro de canal` y seleccione el MSP de la organización igual, `MSP de Org2`.
7. Pulse **Añadir** y luego asigne permisos para dicha organización. Le recomendamos que la convierta en `Operador` para que pueda actualizar el canal.
8. Pulse **Actualizar canal**.

Cuando finalice este proceso, `org2` podrá crear o unir un canal alojado en el `Servicio de ordenación`. Puede continuar con el [Paso tres: unir el igual al canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Exportar la información de la organización
{: #ibp-console-join-network-add-org2-remote}

**Siga estos pasos solo si el servicio de ordenación y el canal al que se va a unir el igual residen en otra instancia del servicio {{site.data.keyword.blockchainfull_notm}} Platform.** De lo contrario, puede pasar al [Paso tres: unir el igual al canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

Tiene que enviar la definición de MSP de la organización al administrador del servicio de ordenación para que se añada al consorcio; para ello siga los pasos siguientes.

Cuando siga estos pasos, debe ser el administrador de la **organización igual**, lo que significa que tiene la identidad admin de la organización igual en la cartera:

1. Vaya al separador **Organizaciones**. Verá que las organizaciones que se pueden exportar aparecen en la lista bajo **Organizaciones disponibles**. Pulse el botón **descargar** en el mosaico de la organización para descargar el archivo de configuración JSON que representa el MSP de la organización igual.
2. Envíe este archivo al administrador del servicio de ordenación en una operación fuera de banda.

### Importar la definición de la organización
{: #ibp-console-join-network-import-remote-msp}

El administrador del servicio de ordenación tiene que importar este archivo JSON para añadir la organización a su consola:

1. Vaya al separador **Organizaciones**, pulse el botón **Importar definición de MSP** y seleccione el archivo JSON que representa la definición del MSP de la organización igual.
2. Vaya a la página **Nodos** y pulse el nodo servicio de ordenación. En el panel del servicio de ordenación, en **Miembros de consorcio**, pulse **Añadir organización**. En el panel lateral que se abre, seleccione `MSP de Org2` en la lista de definiciones de MSP desde el separador **Organizaciones**.
3. Vaya al separador **Canales**, pulse `channel1` y luego pulse el icono **Valores** para actualizar el canal y añadir la organización igual al canal.
4. En la lista desplegable **Elegir entre los servicios de ordenación disponibles**, asegúrese de que `Servicio de ordenación` esté seleccionado.
5. En la lista desplegable **ID de MSP de actualizador de canal**, asegúrese de que `MSP de Org1` está seleccionado.
6. En la lista desplegable **Asociar identidad disponible**, asegúrese de que `Admin de Org1` está seleccionado.
7. Desplácese hacia abajo hasta la sección llamada `Añadir organizaciones al canal`, abra la lista desplegable `Seleccionar un miembro de canal` y seleccione el MSP de la organización igual, `MSP de Org2` en esta guía de aprendizaje.
8. Pulse **Añadir** y luego asigne permisos para dicha organización. Le recomendamos que la convierta en `Operador` para que pueda actualizar el canal.
9. Pulse **Actualizar canal**.

Después de que el administrador del servicio de ordenación haya unido la organización del igual al consorcio y al canal, deberá importar el servicio de ordenación en la consola. A continuación, puede unir los canales alojados por el servicio de ordenación. Siga los siguientes pasos para **importar** el servicio de ordenación:

Primero el administrador de la **organización del servicio de ordenación** tiene que completar estos pasos:

1. Vaya al servicio de ordenación en el separador **Nodos** y pulse el icono **Valores** situado a la derecha del nombre del servicio de ordenación para abrir el panel lateral. Pulse el botón **Exportar** bajo **Acciones** para descargar un archivo de configuración JSON.
2. Envíe este archivo a la organización igual en una operación fuera de banda. Luego el administrador de la organización igual puede utilizar el archivo de configuración para añadir el servicio de ordenación a la consola.

### Importar el servicio de ordenación de otro clúster
{: #ibp-console-join-network-import-remote-orderer}

Siga estos pasos si es el administrador de la **organización igual**:

1. Vaya a la página **Nodos** y pulse **Añadir servicio de ordenación**.
2. Pulse {{site.data.keyword.cloud_notm}} en **Importar un servicio de ordenación existente**.
3. A continuación, pulse el botón **Cargar JSON** y seleccione el JSON que representa el propio nodo.
4. En el paso siguiente, cuando se le solicite que asocie una identidad, seleccione la identidad de la organización igual. En esta guía de aprendizaje, esto sería `Admin de Org2`. Pulse **Importar servicio de ordenación**.

## Paso tres: unir el igual al canal
{: #ibp-console-join-network-join-peer-org2}

Ya casi hemos terminado. Ahora el igual se puede unir a un canal existente. Debe obtener el `nombre del canal`, fuera de banda, de una organización que sea miembro del canal. En la guía de aprendizaje sobre cómo **crear una red**, hemos creado un canal denominado `channel1`. Si aún no está allí, vaya al separador **Canales** en el panel de navegación de la izquierda.

Siga los pasos siguientes desde la consola:

1. Pulse el botón **Unir canal** para iniciar los paneles laterales.
2. Seleccione el servicio de ordenación denominado `Servicio de ordenación` y pulse **Siguiente**.
3. Especifique el nombre del canal que desea unir, `channel1`, y pulse **Siguiente**.
4. Seleccione los iguales que desea unir al canal. A efectos de esta guía de aprendizaje, pulse `Org2 igual`.
5. Pulse **Unir canal**.

Si tiene intención de aprovechar las características de Hyperledger Fabric [Datos privados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "Datos privados") o [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery"), debe configurar iguales de ancla en las organizaciones desde el separador **Canales**. Para obtener más información sobre cómo configurar iguales de ancla para datos privados utilizando el separador **Canales** en la consola, consulte [Datos privados](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data).

También puede crear un nuevo canal si su organización es miembro del consorcio. Utilice los pasos para [crear un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) que se describen en la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Siguientes pasos
{: #ibp-console-join-network-next-steps}

Después de unir el igual a un canal, siga los pasos siguientes para desplegar un contrato inteligente y para empezar a enviar transacciones a blockchain:

- [Despliegue un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) mediante la consola.
- Una vez que haya instalado el contrato inteligente y haya creado instancias del mismo, puede [enviar transacciones utilizando la aplicación cliente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilice [el ejemplo de documento comercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) para desplegar un contrato inteligente de ejemplo y enviar transacciones desde el código de aplicación de ejemplo.

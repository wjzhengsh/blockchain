---

copyright:
  years: 2019

lastupdated: "2019-04-23"


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

# Guía de aprendizaje sobre cómo crear una red
{: #ibp-console-build-network}

{{site.data.keyword.blockchainfull}} Platform es una oferta de tipo blockchain-as-a-service que le permite desarrollar, desplegar y trabajar con redes y aplicaciones blockchain. Puede obtener más información sobre los componentes de blockchain y sobre cómo funcionan juntos en la [visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Esta guía de aprendizaje es la primera parte de la [serie de guías de aprendizaje de red de ejemplo](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) y describe cómo utilizar la consola de {{site.data.keyword.blockchainfull_notm}} Platform para crear una red totalmente funcional en un solo servicio Kubernetes de {{site.data.keyword.cloud_notm}}.
{:shortdesc}

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.   

Si aún no ha desplegado la consola en un clúster de Kubernetes utilizando el servicio Kubernetes de {{site.data.keyword.cloud_notm}}, consulte [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Puede crear un nuevo clúster de Kubernetes para el despliegue de la consola o puede utilizar uno existente en su cuenta de {{site.data.keyword.cloud_notm}}.  Después de desplegar {{site.data.keyword.blockchainfull}} Platform en su clúster de Kubernetes, puede iniciar la consola para crear y gestionar los componentes de blockchain.

Tanto si realiza el despliegue en un clúster de Kubernetes de pago como si lo hace en uno gratuito, utilice el panel de control de Kubernetes para descubrir los recursos disponibles cuando elija desplegar nodos y crear canales. Es su responsabilidad gestionar el clúster de Kubernetes y desplegar recursos adicionales si es necesario. Aunque los componentes se desplegarán correctamente en un clúster gratuito, cuantos más componentes añada más lenta será su ejecución.
{: note}


## Serie de guías de aprendizajes de red de ejemplo
{: #ibp-console-build-network-sample-tutorial}

Esta serie de guías de aprendizaje de tres partes le guía por el proceso de creación e interconexión de una red Hyperledger Fabric de varios nodos relativamente sencilla utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 para desplegar una red en el clúster de Kubernetes e instalar y crear una instancia de un contrato inteligente.

* **Guía de aprendizaje sobre cómo crear una red** Esta guía de aprendizaje le ayuda en el proceso de alojar una red mediante la creación de un clasificador y un igual.
* En la [guía de aprendizaje sobre cómo unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) se explica el proceso de unirse a una red existente creando un igual y uniéndolo a un canal.
* La guía para [Desplegar un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) contiene información sobre cómo escribir un contrato inteligente y cómo desplegarlo en la red.

Puede seguir los pasos de estas guías de aprendizaje para crear una red con varias organizaciones en un clúster con fines de desarrollo y prueba. Utilice la guía de aprendizaje sobre cómo **crear una red** si desea formar un consorcio de blockchain mediante la creación de un nodo clasificador y la adición de organizaciones. Utilice la guía de aprendizaje sobre cómo **unirse a una red** para conectar un igual a la red. Si sigue las guías de aprendizaje con distintos miembros del consorcio puede crear una red blockchain verdaderamente **distribuida**.  


### La estructura de esta red
{: #ibp-console-build-network-structure}

Si sigue todos los pasos de las guías de aprendizaje sobre cómo **crear una red** y cómo **unirse a una red**, su red se parecerá a la de la siguiente ilustración:
![Estructura básica de red de ejemplo](../images/ibp-v2-build-network.png "Estructura básica de red de ejemplo")  
*Figura 1. Estructura básica de red de ejemplo*  

Esta configuración es suficiente para probar aplicaciones y contratos inteligentes. La red contiene los componentes siguientes:

* **Dos organizaciones iguales**: `Org1` y `Org2`  
  En esta serie de guías de aprendizaje se describe cómo crear dos organizaciones iguales y dos iguales asociados. Imagine que las organizaciones de una red blockchain son como dos bancos distintos que tienen que realizar transacciones entre sí. Creamos las definiciones de proveedores de servicios de pertenencia (MSP) de Org1 y de Org2, que definen las organizaciones `Org1` y `Org2`.
* **Una organización clasificador**: `Org de clasificador`  
  Como estamos construyendo un libro mayor distribuido, los iguales y los clasificadores deben formar parte de organizaciones independientes. Por lo tanto, se crea una organización independiente para el clasificador.  Entre otras cosas, un nodo clasificador ordena los bloques de transacciones que se envían a los iguales para que se escriban en sus libros mayores y formen parte de blockchain. Creamos la definición del proveedor de servicios de pertenencia (MSP) del clasificador, que define la organización `Org de clasificador`.
* **Tres entidades emisoras de certificados (CA)**: `CA de Org1, CA de Org2, CA de clasificador`   
  Una CA es el nodo que emite certificados a todos los miembros de la organización. Debido a que se recomienda desplegar una CA por organización, desplegaremos tres CA en total: una para cada organización igual y otra para la organización del clasificador. También utilizaremos las CA para crear todos los nodos, las identidades y la definición de organización para cada organización.
* **Un clasificador:** `Clasificador`  
  Actualmente solo se puede desplegar un servicio de ordenación SOLO mediante la consola. Puede desplegar más de uno de estos nodos, suponiendo que tenga espacio en el clúster, pero cada clasificador tendrá su propio "consorcio", que es la lista de organizaciones iguales que pueden crear y unirse a los canales. No puede conectar varios clasificadores al mismo canal. Como administrador de este clasificador, añadirá la organización igual que utilice para crear el consorcio, lo que permite a la organización igual crear un canal. Si desea crear un canal que tenga organizaciones desplegadas en distintos clústeres, que es el modo en que se estructurarán la mayoría de las redes de producción, también tiene que importar una organización igual que se haya desplegado en otra consola en la consola. Esto permite a la organización igual unir el canal que se aloja en el servicio de ordenación.
* **Dos iguales:** `Org1 igual` y `Org2 igual`  
  El libro mayor de blockchain, `Libro mayor x` en la ilustración anterior, se mantiene mediante iguales distribuidos. Estos iguales se despliegan con [Couch DB ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) como base de datos.
* **Un canal**: `channel1`  
  Los canales proporcionan privacidad de datos. Permiten que los conjuntos de organizaciones realicen transacciones sin exponer sus datos a organizaciones que no sean miembros del canal. Cada canal tiene su propio libro mayor de blockchain, compartido entre los iguales que se han unido a dicho canal. En la guía de aprendizaje se crea un canal unido por ambas organizaciones, y crea una instancia del contrato inteligente en el canal que las organizaciones pueden utilizar para realizar transacciones.

Esta configuración no es obligatoria. {{site.data.keyword.blockchainfull_notm}} Platform 2.0 permite un alto grado de personalización. Si tiene recursos disponibles en el clúster de Kubernetes, puede utilizar la consola para desplegar un nodo clasificador y añadirle organizaciones, lo que también se conoce como formar un consorcio. También puede crear una organización igual que se conecte a varios servicios de ordenación. En esta guía de aprendizaje se muestran los pasos a seguir para crear su propia red, con referencias a temas que proporcionan información más detallada sobre {{site.data.keyword.blockchainfull_notm}} Platform y sobre la consola.


En esta guía de aprendizaje sobre cómo **crear una red**, solo creamos una parte de la red anterior, una red simple que se puede utilizar para alojar un clasificador y una sola organización igual y un igual en un solo canal. En la siguiente ilustración se muestra la parte de la red anterior que vamos a crear: ![Estructura de una red simple](../images/ibp2-simple-network.png "Estructura de una red simple")  
*Figura 2. Estructura de una red simple*  

Esta configuración resulta útil para empezar rápidamente a trabajar con un contrato inteligente y para probarlo, pero no tiene mucho sentido si añade otras organizaciones con las que realizar transacciones, creando un libro mayor realmente distribuido.  Por lo tanto, en la siguiente guía de aprendizaje sobre cómo [unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network), mostramos cómo crear organizaciones iguales e iguales adicionales y cómo añadir una nueva organización al canal.  

En esta guía de aprendizaje, suministramos **valores recomendados** para algunos de los campos de la consola. Esto permite reconocer más fácilmente los nombres e identidades en los separadores y listas desplegables. Estos valores no son obligatorios, pero los encontrará útiles. Ofrecemos una tabla de valores recomendados tras cada tarea.
{:tip}

## Paso uno: crear una organización y su punto de entrada al blockchain
{: #ibp-console-build-network-create-peer-org1}

Para cada organización que desee crear mediante la consola, debe desplegar al menos una CA. Una CA es el nodo que emite certificados a todos los participantes en la red (iguales, clasificadores, clientes, etc.). Estos certificados, que incluyen un par de claves pública y privada, permiten que los participantes de la red se comuniquen, se autentiquen y, en última instancia, realicen transacciones. Estas CA crearán todas las identidades y certificados que pertenecen a su organización, además de definir la propia organización. Luego puede utilizar dichas identidades para desplegar nodos, trabajar con la red y enviar transacciones al blockchain. Para obtener más información sobre la CA y las identidades que tendrá que crear, consulte [Gestión de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

En esta guía de aprendizaje, crearemos dos organizaciones: una será propietaria de un igual y la otra que será propietaria de un clasificador. Cada organización necesita una CA para emitir sus certificados, por lo tanto debemos crear **dos CA**. En esta guía de aprendizaje, **crearemos las CA de una en una**.

Vea el siguiente vídeo y conozca el proceso de crear la organización del igual y el igual.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeo de IBM Blockchain Platform gratuita 2.0 beta - guía de aprendizaje para el despliegue" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/JZj43n_JKIY" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>  
*Vídeo 1. Creación de la organización del igual y el igual*

### Creación de la entidad emisora de certificados de la organización igual
{: #ibp-console-build-network-create-CA-org1CA}

Como parte de esta guía de aprendizaje, su CA emite las claves públicas y privadas para sus usuarios y nodos. Estas identidades no están gestionadas por {{site.data.keyword.IBM_notm}} y las claves no se almacenan en el clúster de Kubernetes ni en la consola. Solo se almacenan en el almacenamiento local del navegador. Por lo tanto, asegúrese de exportar sus identidades y MSP de la organización. Si intenta acceder a la consola desde una máquina distinta o desde otro navegador, tiene que importar estas identidades y definiciones de organización.  
{:important}

Siga los pasos siguientes desde la consola:

1. Vaya a separador **Nodos** de la izquierda y pulse **Añadir entidad emisora de certificados**. Los paneles laterales le permitirán personalizar la CA que desea crear y la organización para la que esta CA emitirá claves.
2. Pulse **{{site.data.keyword.cloud_notm}}** en **Crear entidad emisora de certificados** y pulse **Siguiente**.
3. Utilice el segundo panel lateral para dar a la CA un **nombre de visualización**. El valor recomendado para esta CA es `CA de Org1`.
4. En el panel siguiente, especifique las credenciales de administrador de CA especificando el **ID de administrador** `admin` y proporcionando el secreto que desee. Recomendamos utilizar `adminpw` para ayudarle a seguir esta guía de aprendizaje.
5. Si utiliza un clúster de pago, tiene la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, puede aceptar todos los valores predeterminados y pulsar **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, ya podrá ver la página **Resumen**.
6. Revise la página Resumen y luego pulse **Añadir entidad emisora de certificados**.

**Tarea: creación de la CA de la organización igual**

  | **Campo** | **Nombre de visualización** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crear CA** | CA de Org1  | admin | adminpw |

  *Figura 3. Creación de la CA de la organización igual*

Después de desplegar la CA, la utilizará cuando cree el MSP de la organización, registre usuarios y cree su punto de entrada en una red, el **igual**.  

Es posible que los usuarios avanzados tengan ya su propia CA y que no deseen crear una nueva CA en la consola. Si la CA existente puede emitir certificados en formato `X.509`, puede utilizar su propia CA externa en lugar de crear una nueva aquí. Consulte este tema sobre la
[Utilización de certificados de una CA externa con su igual o clasificador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca) para obtener más información.

### Utilización de la CA para registrar identidades
{: #ibp-console-build-network-use-CA-org1}

Cada nodo o aplicación que desee crear necesita claves públicas y privadas para participar en la red blockchain. También tiene que crear claves de administración para estos nodos y aplicaciones para que pueda gestionarlos desde la consola. Pasaremos por este proceso dos veces, una para cada CA que creemos. Y, para cada CA, creará dos identidades:

* **Un administrador de la organización** Esta identidad le permite trabajar con nodos utilizando la consola de la plataforma.
* **Una identidad igual** Esta identidad le permite desplegar un igual.

En función de su tipo de clúster, el despliegue de la CA puede tardar hasta diez minutos. El cuadrado de color verde en el mosaico de la CA indica que está "En ejecución" y que se puede utilizar para registrar identidades. Antes de continuar con los pasos siguientes para registrar identidades, debe esperar a que el estado de la CA sea "En ejecución".
{:important}

Para generar estos certificados, tenemos que seguir los pasos siguientes:

1. En la consola, pulse el separador **Nodos**. Cuando el indicador de estado de la esquina superior derecha de `Org1 CA` sea de color verde y `En ejecución`, pulse sobre el mosaico para abrirlo.
2. Tras seleccionar la CA, asegúrese de que la identidad `admin` que ha creado para la CA sea visible en la tabla antes de continuar. Debe registrar otra identidad de administrador para la primera organización, `org1`, además de una identidad para el propio igual.  Para registrar nuestros nuevos usuarios, pulse el botón **Registrar usuario**.
3. Para el administrador de la organización, asígnele el ID de inscripción `org1admin`. Puede utilizar cualquier secreto, pero le recomendamos utilizar `org1adminpw` para seguir esta guía. Pulse **Siguiente**.
4. En el siguiente paso, defina el Tipo de esta identidad como `cliente` y seleccione entre las organizaciones afiliadas de la lista desplegable. El campo de afiliación es para usuarios avanzados y no se utiliza en la guía de aprendizaje, pero es un campo obligatorio para el panel. Los elementos de la lista son las afiliaciones predeterminadas de la CA de Fabric. Si desea obtener más información sobre cómo utiliza la CA de Fabric las afiliaciones, consulte este tema en [Registro de
una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Por ahora, seleccione cualquier afiliación de la lista, por ejemplo org1, y pulse Siguiente.
5. Puede dejar los campos **Número máximo de inscripciones** y **Añadir atributos** en blanco. No se utilizan en esta guía de aprendizaje, pero puede obtener más información sobre su función en este tema sobre [Registro de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
6. Una vez que se haya registrado el administrador de la organización, repita este mismo proceso, utilizando la misma `CA de Org1`, para la identidad del igual,
y especifique el ID de inscripción `peer1` y el secreto que desee. Como antes, recomendamos utilizar el secreto `peer1pw`. Se trata de una identidad de nodo, de modo que seleccione `igual` como **Tipo** en el paso siguiente. Seleccione la **Afiliación** que desee. Luego pase por alto **Número máximo de inscripciones** y **Atributos**.

**Tarea: registrar usuarios**

  |  **Campo** | **Descripción** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registrar usuarios** |  Admin de Org1 | org1admin | org1adminpw |
  | | Identidad del igual |  peer1 | peer1pw |

  *Figura 4. Utilización de su CA para registrar usuarios*

### Creación de la definición de MSP de la organización igual
{: #ibp-console-build-network-create-peers-org1}

Ahora que hemos creado la CA del igual y la hemos utilizado para **registrar** las identidades de nuestra organización, tenemos que crear una definición formal de la organización igual, que se conoce como Proveedor de Servicios de pertenencia (MSP). Muchos iguales pueden pertenecer a una organización. **No es necesario que cree una nueva organización cada vez que cree un igual.** Como esta es la primera vez que revisamos la guía de aprendizaje, crearemos el ID de MSP para esta organización. Durante el proceso de creación del MSP, vamos a generar certificados para la identidad `org1admin` y los vamos a añadir a la cartera de la consola.

1. Vaya al separador **Organizaciones** en el panel de navegación izquierdo y pulse **Crear definición de MSP**.
2. Asigne a su MSP el nombre de visualización `MSP de Org1` y el ID de MSP `org1msp`. Si desea especificar su propio ID de MSP en este campo, asegúrese de seguir las especificaciones de la herramienta de sugerencias sobre las limitaciones de este nombre.
3. En **Detalles de la entidad emisora de certificados raíz**, especifique la CA del igual que hemos creado como la CA raíz para la organización. Si esta es su primera vez que examina esta guía de aprendizaje, solo debería ver una: `CA de Org1`.
4. Los campos **ID de inscripción** y **Secreto de inscripción** bajo la misma contendrán el ID y el secreto de inscripción del primer usuario que ha creado con la CA. Podría utilizar estos valores, pero no le recomendamos que utilice la identidad de administrador de CA como administrador de la organización.  Por motivos de seguridad, recomendamos especificar el ID y el secreto de inscripción que ha creado para el administrador de la organización, `org1admin` y `org1adminpw`. A continuación, asigne a esta identidad un nombre de visualización, `Admin de Org1`.
5. Pulse el botón **Generar** para inscribir esta identidad como administrador de la organización y exporte la identidad a la cartera, donde se utilizará cuando se cree el igual y cuando se cree un canal.
6. Pulse **Exportar** para exportar los certificados de administrador al sistema de archivos para poder gestionar el archivo. Como se ha dicho anteriormente, esta identidad no se almacena en el clúster ni la gestiona {{site.data.keyword.IBM_notm}}. Solo se almacena en el navegador. Si cambia de navegador, tiene que importar esta identidad en la cartera de la consola para poder administrar el igual.
7. Pulse **Crear definición de MSP**.

**Tarea: crear el MSP de la organización igual**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción**  | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear organización** | MSP de Org1 | org1msp |||
  | **CA raíz** | CA de Org1 ||||
  | **Cert de administrador de la org** | |  | org1admin | org1adminpw |
  | **Identidad** | Admin de Org1 |||||

  *Figura 5. Crear la definición de MSP de la organización igual*

Una vez que haya creado el MSP, debe poder ver el administrador de la organización igual en la cartera de la consola.

**Tarea: comprobar la cartera de la consola**

  | **Campo** |  **Nombre de visualización** | **Descripción** |
  | ------------------------- |-----------|----------|
  | **Identidad** | Admin de Org1 | Identidad de administrador de Org1 |

  *Figura 6. Comprobar la cartera de la consola*

Para obtener más información sobre los MSP, consulte el apartado sobre [gestión de organizaciones](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Es importante exportar la identidad del administrador de la organización porque usted es el responsable de gestionar y proteger estos certificados.
{:important}

<!--
You are free to repeat this process as many times as times as you want. To create a network that is similar to [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about), for example, you will create two organizations and one peer per organization, and this tutorial will take you through those steps. This process involves repeating the steps above, to create the CA and the relevant identities, and below, to create another peer. Remember that the best practice is to have **one CA for each organization**. This CA can, however, be used to register and enroll multiple organization admins as well as multiple peer identities **for that organization**. Make sure to keep track of the resources you are using in your Kubernetes cluster, especially if using the free cluster.
{: note}
-->


### Creación de un igual
{: #ibp-console-build-network-peer-create}

Después de [crear una CA](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-CA-org1CA), de utilizarla para registrar identidades y de crear el [MSP de la organización igual](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peers-org1), está listo para crear un igual.

#### ¿Qué rol juegan los iguales?
{: #ibp-console-build-network-peer-role}

Es importante recordar que las propias organizaciones no mantienen libros mayores. Los iguales sí lo hacen. Las organizaciones también utilizan iguales para firmar propuestas de transacciones y para aprobar actualizaciones de configuraciones de canal. Como el hecho de tener al menos dos iguales en un canal hace que esté altamente disponible, se recomienda tener al menos dos iguales unidos a un canal para implementaciones de nivel de producción. En esta guía de aprendizaje, solo mostraremos el proceso para crear un único igual.

Desde una perspectiva de asignación de recursos, es posible unir los mismos iguales a varios canales. El diseño del igual garantiza que los datos procedentes de un canal no pueden pasar a otro a través del igual. Sin embargo, debido a que el igual almacenará un libro mayor independiente para cada canal, es necesario asegurarse de que el igual tiene suficiente potencia de proceso y almacenamiento para manejar la transacción y la carga de datos.

#### Despliegue del igual
{: #ibp-console-build-network-deploy-peer-role}

Utilice la consola para seguir los pasos siguientes:

1. En la página **Nodos**, pulse **Añadir igual**.
2. Pulse {{site.data.keyword.cloud_notm}} en **Crear un nuevo igual** y pulse **Siguiente**.
3. Asigne a su igual el **Nombre de visualización** `Org1 igual`.
4. En la pantalla siguiente, seleccione `CA de Org1` como CA. A continuación, asigne el ID y el secreto de inscripción que ha creado para el igual, `peer1` y `peer1pw`. Luego seleccione su MSP, `MSP de Org1`, en la lista desplegable y pulse **Siguiente**.
5. El siguiente panel lateral solicita la información de la CA de TLS. Aunque es posible crear administradores independientes para la CA de TLS que se ha desplegado con la CA, no es necesario hacerlo.
  - Asigne a **ID de inscripción de TLS** el valor `admin` y al secreto el valor `adminpw`, los mismos valores que el ID de inscripción y secreto de inscripción que ha asignado al crear la CA.
  - El **Nombre de host de CSR de TLS** sirve para que usuarios avanzados especifiquen un nombre de dominio personalizado que se puede utilizar para gestionar el punto final del igual. Por ahora deje el **Nombre de host de CSR de TLS** en blanco, ya que no se utiliza en esta guía de aprendizaje.
6. El siguiente panel lateral le solicita **Asociar una identidad** y convertirla en el administrador del igual. Seleccione la identidad de administrador del igual `Admin de Org1`.
7. Si utiliza un clúster de pago, en el panel siguiente, tendrá la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, puede aceptar todos los valores predeterminados y pulsar **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, podrá ver la página **Resumen**.
8. Revise el resumen y pulse **Añadir igual**.

**Tarea: despliegue de un igual**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear igual** | Org1 igual | org1msp |||
  | **CA** | CA de Org1 ||||
  | **Identidad de igual** | |  | peer1 | peer1pw |
  | **Certificado de administrador** | org1msp ||||
  | **CA de TLS** | CA de Org1 ||||
  | **ID de CA de TLS** | || admin | adminpw |
  | **Asociar identidad** | Admin de Org1 |||||

  *Figura 7. Despliegue de un igual*

## Paso dos: crear el nodo que ordena las transacciones
{: #ibp-console-build-network-create-orderer}

En otros blockchains distribuidos, como Ethereum y Bitcoin, no hay ninguna autoridad central que ordene las transacciones y las envíe a los iguales. Hyperledger Fabric, el blockchain en el que se basa {{site.data.keyword.blockchainfull_notm}} Platform, funciona de otra forma. Cuenta con un nodo denominado **clasificador**.

Los clasificadores son componentes clave de una red, ya que realizan algunas funciones esenciales:

- Literalmente **clasifican** los bloques de transacciones que se envían a los iguales para que se escriban en sus libros mayores. Este proceso se denomina "ordenación", por lo que el clasificador también recibe el nombre de "servicio de ordenación".
- Mantienen el **canal del sistema clasificador**, el lugar en el que reside el **consorcio**, la lista de organizaciones de iguales que tienen permiso para crear canales. Un consorcio es básicamente un vehículo de varios arrendamientos y un solo servicio de ordenación, por diseño, puede alojar varios consorcios.
- **Imponen las políticas** decididas por el consorcio o por los administradores del canal. Estas políticas controlan desde quién tiene acceso de lectura o de escritura en un canal hasta quién puede crear o modificar un canal. Por ejemplo, cuando un participante en la red solicita modificar un canal o una política de consorcio, el servicio de ordenación procesa la solicitud para ver si el participante tiene los derechos de administración adecuados para dicha actualización de la configuración, lo valida en la configuración existente, genera una nueva configuración y la transmite a los iguales.

Al igual que sucede con el igual, para poder crear un clasificador es necesario crear una CA para proporcionar las identidades y el MSP de nuestra organización del clasificador.

Vea el siguiente vídeo y conozca el proceso de crear la organización del clasificador y el clasificador.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeo de IBM Blockchain Platform gratuita 2.0 beta - guía de aprendizaje para el despliegue" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/Gomkn-JtNe8" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>  
*Vídeo 2. Creación de la organización del clasificador y el clasificador*

### Ordenación en la consola
{: #ibp-console-build-network-ordering-console}

Aunque existen diferentes implementaciones de clasificador, actualmente el único servicio de ordenación que recibe soporte es SOLO, que cuenta con un único nodo clasificador. Debido a que tener un solo nodo representa un punto único de anomalía, no se recomienda utilizar SOLO en redes de producción.

Puede crear tantos clasificadores como desee mediante la consola. Sin embargo, cada uno de estos clasificadores debe tener necesariamente su propio canal del sistema de ordenación y, por tanto, sus propios consorcios. Solo se puede conectar un clasificador SOLO a un canal.

En esta guía de aprendizaje, solo crearemos un clasificador.

### Creación de la entidad emisora de certificados de la organización del clasificador
{: #ibp-console-build-network-create-orderer-ca}

El proceso de creación de una CA para un clasificador es idéntico al proceso de creación de la misma para un igual.
1. Vaya a separador **Nodos** y pulse **Añadir entidad emisora de certificados**.
2. Pulse **{{site.data.keyword.cloud_notm}}** en **Crear una nueva entidad emisora de certificados** y pulse **Siguiente**
3. Asigne a esta CA un nombre de visualización exclusivo, `CA de clasificador`.
4. Puede utilizar el **ID de inscripción** que ha asignado a la otra CA, `admin`, y luego especificar el secreto que desee, aunque recomendamos utilizar `adminpw`.
5. Si utiliza un clúster de pago, en el panel siguiente, tendrá la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, puede aceptar todos los valores predeterminados y pulsar **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, ya podrá ver la página **Resumen**.
6. Revise la página Resumen y luego pulse **Añadir entidad emisora de certificados**.

Una vez más, es posible que los usuarios avanzados tengan ya su propia CA y que no deseen crear una nueva CA en la consola. Si la CA existente puede emitir certificados en formato `X.509`, puede utilizar su propia CA externa en lugar de crear una nueva aquí. Consulte este tema sobre la
[Utilización de certificados de una CA externa con su igual o clasificador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca) para obtener más información.

### Utilización de la CA para registrar las identidades del clasificador y del administrador del clasificador
{: #ibp-console-build-network-use-CA-orderer}

Al igual que hemos hecho con el igual, tenemos que registrar dos identidades con nuestra CA de clasificador.  Después de seleccionar la CA, tendrá que registrar un administrador para nuestra organización de clasificador, además de una identidad para el propio clasificador. Como antes, debería ver una identidad en el separador `CA de clasificador`; es el administrador que ha creado para la CA.

En función de su tipo de clúster, el despliegue de la CA puede tardar hasta diez minutos. El cuadrado de color verde en el mosaico de la CA indica que está "En ejecución" y que se puede utilizar para registrar identidades. Antes de continuar con los pasos siguientes para registrar identidades, debe esperar a que el estado de la CA sea "En ejecución".
{:important}

1. En la consola, pulse el separador **Nodos**. Cuando el indicador de estado de la esquina superior derecha de `Orderer CA` sea de color verde y `En ejecución`, pulse sobre el mosaico para abrirlo.
2. Espere a que la identidad `admin` que acaba de crear sea visible en la tabla y, a continuación, pulse el botón
**Registrar usuario** para registrar los nuevos usuarios.
3. Para el administrador de la organización, asígnele el ID de inscripción `ordereradmin`. Recomendamos utilizar el secreto `ordereradminpw`.
4. En el siguiente paso, defina el Tipo de esta identidad como `cliente` y seleccione entre las organizaciones afiliadas de la lista desplegable. El campo de afiliación es para usuarios avanzados y no se utiliza en la guía de aprendizaje, pero es un campo obligatorio para el panel. Los elementos de la lista son las afiliaciones predeterminadas de la CA de Fabric. Si desea obtener más información sobre cómo utiliza la CA de Fabric las afiliaciones, consulte este tema en [Registro de
una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Por ahora, seleccione cualquier afiliación de la lista y pulse Siguiente.
5. Puede dejar los campos **Número máximo de inscripciones** y **Añadir atributos** en blanco. No se utilizan en esta guía de aprendizaje, pero puede obtener más información sobre su función en este tema sobre [Registro de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) en la consola.
6. Una vez que se haya registrado el administrador de la organización, repita este mismo proceso, utilizando la misma `CA de clasificador`, para la identidad del clasificador
y especifique el ID de inscripción `orderer1`. Puede especificar el secreto que desee, pero le recomendamos utilizar `orderer1pw` para facilitar el seguimiento de la guía de aprendizaje. Se trata de una identidad de nodo, de modo que seleccione `igual` como **Tipo** en el paso siguiente. Como antes, pase por alto **Número máximo de inscripciones** y **Atributos**.

**Tarea: crear una CA y registrar usuarios**

  | **Campo** | **Descripción** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear CA** | CA de clasificador | admin | adminpw |
  | **Registrar usuarios** | Admin de clasificador | ordereradmin | ordereradminpw |
  |  | Identidad de clasificador |  orderer1 | orderer1pw |

*Figura 8. Crear una CA y registrar usuarios*

### Creación de la definición de MSP de la organización del clasificador
{: #ibp-console-build-network-create-orderer-org-msp}

Cree la definición de MSP de la organización del clasificador y especifique la identidad del administrador para la organización. Después de registrar los usuarios administrador del clasificador y clasificador, tenemos que crear el ID de MSP y registrar el usuario `ordereradmin`
que hemos registrado como administrador de nuestra organización.

1. Vaya al separador **Organizaciones** en el panel de navegación izquierdo y pulse **Crear definición de MSP**.
2. Asigne a la definición de MSP un nombre de visualización como `MSP de clasificador` y un ID como `orderermsp`,
asegurándose de seguir las especificaciones sobre las limitaciones de este nombre que se muestran en la herramienta de sugerencias.
3. En **Detalles de la entidad emisora de certificados raíz**, seleccione la `CA de clasificador` que hemos creado.
4. Los campos **ID de inscripción** y **Secreto de inscripción** bajo la misma contendrán el ID y el secreto de inscripción del primer usuario que ha creado con la CA. Podría utilizar estos valores, pero no le recomendamos que utilice la identidad de administrador de CA como administrador de la organización.  Por motivos de seguridad, recomendamos especificar el ID y el secreto de inscripción que ha creado para el administrador de la organización, `ordereradmin` y `ordereradminpw`. A continuación, asigne a esta identidad un nombre de visualización, como por ejemplo `Admin de clasificador`.
5. Pulse el botón **Generar** para inscribir esta identidad como administrador de la organización y añada la identidad a la cartera de la consola, donde se utilizará cuando se cree el clasificador.
6. Pulse **Exportar** para exportar la identidad del administrador de la organización del clasificador al sistema de archivos. Como se ha dicho anteriormente, esta identidad no se almacena en el clúster ni la gestiona {{site.data.keyword.IBM_notm}}. Solo se almacena en el almacenamiento local del navegador.  Si cambia de navegador, tiene que importar esta identidad en la cartera de la consola para poder administrar el clasificador.
7. Pulse **Crear definición de MSP**.

**Tarea: crear la definición de MSP de la organización del clasificador**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción**  | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear organización** | MSP de clasificador | orderermsp |||
  | **CA raíz** | CA de clasificador ||||
  | **Cert de administrador de la org** | |  | ordereradmin | ordereradminpw |
  | **Identidad** | Admin de clasificador |||||

  *Figura 9. Crear la definición de MSP de la organización del clasificador*

Una vez que haya creado el MSP, debe poder ver el administrador de la organización del clasificador en la cartera de la consola.

**Tarea: comprobar la cartera de la consola**

  | **Campo** |  **Nombre de visualización** | **Descripción** |
  | ------------------------- |-----------|----------|
  | **Identidad** | Admin de Org1 | Identidad de administrador de Org1 |
  | **Identidad** | Admin de clasificador | Identidad de administrador del clasificador |

  *Figura 10. Crear la definición de MSP de la organización del clasificador*

Es importante exportar la identidad del administrador de la organización porque usted es el responsable de gestionar y proteger estos certificados. Si exporta el nodo del clasificador y la definición de MSP del clasificador, se pueden importar en otra consola en la que otro operador pueda crear nuevos canales en el clasificador o unir iguales al canal.
{:tip}

### Creación de un clasificador
{: #ibp-console-build-network-create-an-orderer}

Siga los pasos siguientes desde la consola:

1. En la página **Nodos**, pulse **Añadir clasificador**.
2. Pulse el botón {{site.data.keyword.cloud_notm}} bajo **Crear un nuevo clasificador** y pulse **Siguiente**.
3. Asigne a su clasificador el **Nombre de visualización** `Clasificador`.
4. En el paso siguiente, seleccione `CA de clasificador` como CA. A continuación, asigne el ID y el secreto de inscripción que ha creado para el clasificador, `orderer1` y `orderer1pw`. Luego seleccione su MSP, `MSP de clasificador`, en la lista desplegable y pulse **Siguiente**.
5. El siguiente panel lateral solicita la información de la CA de TLS. Aunque es posible crear usuarios independientes para la CA de TLS que se ha desplegado con la CA, no es necesario hacerlo.
   - Asigne a **ID de inscripción de TLS** el valor `admin` y al secreto el valor `adminpw`. Son los valores de ID de inscripción y secreto de inscripción que ha asignado al crear la CA.
   - El **Nombre de host de CSR de TLS** sirve para que usuarios avanzados especifiquen un nombre de dominio personalizado que se puede utilizar para gestionar el punto final del clasificador. Por ahora deje el **Nombre de host de CSR de TLS** en blanco, ya que no se utiliza en esta guía de aprendizaje.
6. El paso **Asociar identidad** le permite elegir un administrador para el clasificador. Seleccione `Administrador del clasificador` como antes y pulse **Siguiente**.
7. Si utiliza un clúster de pago, en el panel siguiente, tendrá la oportunidad de configurar la asignación de recursos del nodo. A efectos de esta guía de aprendizaje, puede aceptar todos los valores predeterminados y pulsar **Siguiente**. Si desea obtener más información sobre cómo asignar recursos al nodo, consulte este tema sobre [Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-allocate-resources). Si utiliza un clúster gratuito, ya podrá ver la página **Resumen**.
7. Revise la página Resumen y pulse **Añadir clasificador**.

**Tarea: crear un clasificador**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear clasificador** | Clasificador | orderermsp |||
  | **CA** | CA de clasificador ||||
  | **Identidad del clasificador** | |  | orderer1 | orderer1pw |
  | **Certificado de administrador** | MSP de clasificador ||||
  | **CA de TLS** | CA de clasificador ||||
  | **ID de CA de TLS** | || admin | adminpw |
  | **Asociar identidad** | Admin de clasificador |||||

  *Figura 11. Crear un clasificador*

Una vez que se haya creado el clasificador, podrá verlo en el panel **Nodos**.

## Paso tres: añadir su organización a la lista de organizaciones que pueden realizar transacciones
{: #ibp-console-build-network-add-org}

Como ya hemos indicado anteriormente, una organización igual debe ser miembro de un consorcio del clasificador para que pueda crear o unirse a un canal. Esto se debe a que los canales son, a nivel técnico, **vías de acceso de mensajería** entre iguales a través del clasificador. Del mismo modo que un igual se puede unir a varios canales sin que la información pase de un canal a otro, un clasificador puede tener varios canales que se ejecutan a través del mismo sin exponer datos a organizaciones de otros canales.

Puesto que solo los administradores del clasificador pueden añadir organizaciones iguales al consorcio, tendrá que **ser** el administrador del clasificador o deberá **enviar** la información de MSP al administrador del clasificador.

Vea el siguiente vídeo y conozca el proceso de añadir la organización al consorcio, crear el canal y unir el igual al canal.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeo de IBM Blockchain Platform gratuita 2.0 beta - guía de aprendizaje para el despliegue" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/jO3V4K9DYpY" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>  
*Vídeo 3. Cómo añadir la organización al consorcio, crear el canal y unir el igual al canal.*               

Como es el administrador del clasificador, este proceso es relativamente sencillo:
1. Vaya al separador **Nodos**.
2. Desplácese hasta el clasificador que ha creado y pulse en el mismo para abrirlo.
3. En **Miembros del consorcio**, pulse **Añadir organización**.
4. En la lista desplegable, seleccione `MSP de Org1`, ya que este es el MSP que representa la organización igual `org1`.
5. Pulse **Añadir organización**.

Cuando finalice este proceso, `org1` podrá crear o unir un canal alojado en el `Clasificador`.

En esta guía de aprendizaje, podemos acceder fácilmente al `MSP de Org1` porque tanto la organización igual como la organización del clasificador se han creado en la misma consola. En un escenario real, se crearían otras definiciones de MSP mediante distintos operadores de red en su propio clúster utilizando su propia consola de {{site.data.keyword.blockchainfull_notm}}. Posteriormente, cuando la organización, como por ejemplo una organización igual, deseara unirse a su consorcio, el operador de red tendría que enviarle su definición de MSP de organización en una operación fuera de banda. Además, tendría que exportar el nodo clasificador para que lo importaran en su consola para que pudieran unir un igual a un canal o crear un nuevo canal. Este proceso se describe en la guía de aprendizaje sobre cómo unirse a una red, bajo [Exportación de la información de la organización](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).

## Paso cuatro: crear un canal
{: #ibp-console-build-network-create-channel}

Aunque los miembros de una red suelen ser entidades empresariales relacionadas que desean realizar transacciones entre sí, es posible que haya instancias en las que subconjuntos de sus miembros deseen realizar transacciones sin el conocimiento de los demás. Esto es posible mediante la creación de un **canal** en el que se llevarán a cabo estas transacciones. Los canales duplican la estructura de una red de blockchain en el sentido de que contienen miembros, iguales, un servicio de ordenación, un libro mayor, políticas y contratos inteligentes. Sin embargo, al restringir la pertenencia al grupo, e incluso el conocimiento del canal, a determinados subconjuntos de miembros de la red, los canales garantizan que los miembros de la red puedan aprovechar la estructura general de la red mientras se mantiene la privacidad donde sea necesario.

Tal como se ha indicado anteriormente, para unir un igual de `org1` a un canal, primero se debe añadir `org1` al consorcio. Si la organización no es miembro del consorcio en el momento de crear el canal, se puede crear el canal y añadir la organización más adelante pulsando el botón **Valores** de la página del canal relevante y pasando por el flujo del proceso para **Actualizar canal**.

Para obtener más información sobre los canales y sobre cómo utilizarlos, consulte la [documentación de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/channels.html).

Vea el vídeo 3 anterior y conozca el proceso para crear y unir el igual al canal.


<!--
Note that even though the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 uses Hyperledger Fabric v1.4 binaries, because the [gossip protocol ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html) is not being used with the console, Fabric functionalities that leverage gossip, such as [Private Data ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html)] and [Service Discovery ![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html)], are not available.
-->

### Creación de un canal: `channel1`
{: #ibp-console-build-network-create-channel1}

Como la consola utiliza iguales para recopilar información acerca de los canales a los que pertenecen los iguales, **a menos que una organización haya unido un igual a un canal, no puede ver ni interactuar con el canal mediante la consola**.

Como trabajará dentro del ámbito de nodos que creados mediante la consola, debe esperar hasta crear la CA, el nodo clasificador y el igual antes de crear un canal. También tiene que añadir su organización igual al consorcio.

Si ya lo ha hecho, vaya al separador **Canales** en el panel de navegación de la izquierda. Desde aquí se crean y se gestionan los canales.

Cuando vaya por primera vez a este separador, estará vacía excepto los botones **Crear canal** y **Unir a canal**. Esto se debe a que aún no ha creado un canal ni ha unido a un igual al mismo.

#### Creación del canal
{: #ibp-console-build-network-channels-create}

Siga los pasos siguientes desde la consola:

1. Pulse **Crear canal**. Se abrirá un panel lateral.
2. Asigne al canal el **nombre** `channel1`. Anote este valor, ya que no se almacena en la consola y tendrá que compartirlo con cualquiera que desee unirse a este canal.
3. Seleccione el **clasificador** que ha creado, `Clasificador` en la lista desplegable de clasificadores.
4. Seleccione el **MSP** que identifique la organización del creador del canal en la lista desplegable. Debería ser `MSP de Org1 (org1msp)`.
5. Especifique la identidad del creador del canal. Donde el paso anterior de MSP utiliza la organización que ha creado el canal, este paso utiliza **su** identidad de administrador, `Admin de Org1`.
6. Seleccione las organizaciones que desea unir al canal y los permisos que desea que tengan. Aunque ha especificado `MSP de Org1 (org1msp)` como creador del canal, también tiene que seleccionarlo aquí. Pulse **Añadir** y, a continuación, otorgue a su organización un nivel de permisos. En escenarios del mundo real, como creador del canal deseará elegir los permisos detenidamente, para que se ajusten a los requisitos de las organizaciones que se unen al canal. Como está creando un canal con un solo miembro, y cada canal debe tener al menos un operador, convierta su organización en **Operador**.

Cuando esté preparado, pulse **Crear canal**. Debería volver al separador Canales para poder ver un mosaico pendiente del canal que acaba de crear.

**Tarea: crear un canal**

  |  **Campo** | **Nombre** |
  | ------------------------- |-----------|
  | **Nombre del canal** | channel1 |
  | **Clasificador** | Clasificador |
  | **MSP del creador del canal** | MSP de Org1 |
  | **Asociar identidad disponible** | Admin de Org1|
  | **Miembro del canal** | MSP de Org1|

*Figura 12. Crear un canal*

El paso siguiente consiste en unir un igual a este canal.

## Paso cinco: unir el igual al canal
{: #ibp-console-build-network-join-peer}

Ya casi hemos terminado. Unir el igual al canal es el último paso de la configuración de la infraestructura básica de la red. Si aún no está allí, vaya al separador **Canales** en el panel de navegación de la izquierda.

Siga los pasos siguientes desde la consola:

1. Pulse el mosaico pendiente para que `channel1` inicie los paneles laterales.
2. Seleccione los iguales que desea unir al canal. A efectos de esta guía de aprendizaje, pulse `Org1 igual`.
3. Pulse **Unir canal**.

## Siguientes pasos
{: #ibp-console-build-network-next-steps}

Después de crear un canal y de unir un igual al mismo, tiene una red de blockchain básica totalmente funcional que puede utilizar para desarrollo y pruebas. Siga los pasos siguientes para desplegar un contrato inteligente y para empezar a enviar transacciones a blockchain:

- [Despliegue un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) mediante la consola.
- Una vez que haya instalado el contrato inteligente y haya creado instancias del mismo, puede [enviar transacciones utilizando la aplicación cliente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilice [el ejemplo de documento comercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) para desplegar un contrato inteligente de ejemplo y enviar transacciones utilizando el código de aplicación de ejemplo.

También puede crear otra organización igual utilizando la [guía de aprendizaje sobre cómo unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-structure). Puede añadir una segunda organización al canal para simular una red distribuida, con dos iguales que comparten un libro mayor de un solo canal.

## Utilización de certificados de una CA externa con su igual o clasificador
{: #ibp-console-build-network-third-party-ca}

En lugar de utilizar una entidad emisora de certificados de {{site.data.keyword.blockchainfull_notm}} Platform como CA del igual o el clasificador, puede utilizar certificados de una CA externa, una que no se aloje en
{{site.data.keyword.IBM_notm}}, siempre que la CA emita certificados en formato
[X.509 ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html#digital-certificates "Certificados digitales").

### Antes de empezar
{: #ibp-console-build-network-third-party-ca-prereq}

1. Necesita recopilar la información siguiente acerca de los certificados y guardarla en archivos individuales que se puedan cargar en la consola.   
**Nota:** los certificados dentro de los archivos pueden estar en formato `PEM` o `codificados en base64`.
 * **Certificado de identidad de igual o clasificador** Este es el certificado de firma público de la CA externa que utilizará el igual o el clasificador.
 * **Clave privada de identidad de igual o clasificador**  Esta es la clave privada que corresponde al certificado firmado de la CA de terceros que utilizará este igual o clasificador.
 * **Definición de MSP de organización del igual o clasificador** Debe generar manualmente este archivo utilizando las instrucciones proporcionadas en [Creación manual de un archivo JSON de MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-build-msp).
 * **Certificado de CA de TLS** Este es el certificado de firma público creado por su CA de TLS externa que utilizará este igual o clasificador.
  * **Clave privada de CA de TLS** Esta es la clave privada correspondiente al certificado firmado de la CA de TLS que utilizará este igual o clasificador para las comunicaciones seguras con otros miembros de la red.
 * **Certificado raíz de CA de TLS** (Opcional) Este es el certificado raíz de la CA de TLS externa. Debe proporcionar un certificado raíz de CA de TLS o un certificado de CA de TLS intermedio, o ambos.
 * **Certificado TLS intermedio** (Opcional) Este es el certificado TLS si el certificado TLS lo emite una CA de TLS intermedia. Cargue el certificado de CA de TLS intermedio. Debe proporcionar un certificado raíz de CA de TLS o un certificado de CA de TLS intermedio, o ambos.
 * **Certificado de identidad de administrador de igual o clasificador** Este es el certificado de firma público de la CA externa que utilizará la identidad de administrador del igual o del clasificador. Este certificado se conoce también como clave pública de identidad de administrador del igual o clasificador.
 * **Clave privada de identidad de administrador de igual o clasificador** Esta es la clave privada correspondiente al certificado firmado de la CA externa que utilizará la identidad de administrador del igual o del clasificador.

2. Importe el archivo de definición de MSP de organización de igual generado en la consola, pulsando el separador
**Organizaciones** seguido por **Importar definición de MSP**.

### Crear un nuevo igual o clasificador utilizando certificados de una CA externa
{: #ibp-console-build-network-create-peer-orderer-third-party-ca-}

Ahora que ha recopilado todos los certificados necesarios, está listo para crear un igual o clasificador que utilice dichos certificados. Siga estas instrucciones para crear el nodo igual o clasificador:

1. En el separador **Nodos**, pulse **Añadir igual** o **Añadir clasificador**.
2. Tras especificar un nombre de visualización para el nodo, seleccione la opción de utilizar una CA externa.
3. Pase a través de los paneles y cargue los archivos correspondientes a la información de certificados que ha recopilado.
4. Asegúrese de seleccionar la definición de MSP de organización de igual o clasificador que haya importado en la consola en la lista desplegable.
5. En el último paso, cuando se le solicite que asocie una identidad con el igual o el clasificador, deberá pulsar **Nueva identidad**.
6. Especifique cualquier valor como **Nombre de visualización** para esta identidad. El nombre de visualización estará visible en la cartera de la consola después de crear el nodo.
7. En el campo **Certificado**, cargue el archivo que contiene el **Certificado de identidad de administrador de igual o clasificador**.
8. En el campo **Clave privada**, cargue el archivo que contiene el **Clave privada de identidad de administrador de igual o clasificador**.
9. Revise la información en la página Resumen y pulse **Añadir igual** o **Añadir clasificador**.

### Qué hacer a continuación
{: #ibp-console-build-network-third-party-ca-next}

Ha recopilado todos los certificados de igual o clasificador de la CA de terceros, ha creado su definición de MSP de organización correspondiente y ha creado el nodo. Si está siguiendo las guías de aprendizaje, puede volver al paso siguiente.
- Si ha creado el nodo igual, el siguiente paso será [Crear el nodo que ordena las transacciones](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-orderer).
- Si ha creado el nodo para unirse a una red existente, el siguiente paso será
[Añadir su organización a la lista de organizaciones que pueden realizar transacciones](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2).
- Si ha creado un nodo clasificador, el siguiente paso será
[Crear un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel).

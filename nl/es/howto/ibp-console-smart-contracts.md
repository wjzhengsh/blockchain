---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Guía de aprendizaje sobre despliegue de un contrato inteligente en la red
{: #ibp-console-smart-contracts}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Un contrato inteligente es el código, al que a veces se hace referencia como código de encadenamiento, que le permite leer y actualizar datos en el libro mayor de blockchain. El contrato inteligente puede convertir la lógica empresarial en un programa ejecutable acordado y verificado por todos los miembros de una red blockchain. Esta guía de aprendizaje es la tercera parte de la [serie de guías de aprendizaje de red de ejemplo](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) y en ella se describe cómo desplegar contratos inteligentes para iniciar transacciones en la red blockchain. En las dos guías de aprendizaje anteriores se describe cómo crear componentes en la consola de {{site.data.keyword.blockchainfull_notm}} Platform y cómo conectarlos a componentes creados en otros clústeres para crear una red blockchain realmente **distribuida**
{:shortdesc}

Los contratos inteligentes se instalan en los iguales y luego se crean instancias de los mismos en los canales. **Todos los miembros que desean enviar transacciones o leer datos utilizando un contrato inteligente tienen que instalar el contrato en su igual.** Un contrato inteligente se define por su nombre y versión. Por lo tanto, tanto el nombre como la versión del contrato instalado deben ser coherentes entre los iguales de un canal que vayan a ejecutar el contrato inteligente.

Una vez se ha instalado el contrato inteligente en los iguales, un solo miembro de la red crea una instancia del contrato en el canal. El miembro de red debe haberse unido al canal para poder realizar esta acción. La creación de la instancia actualiza el libro mayor con los datos iniciales utilizados por el contrato inteligente y luego inicia los contenedores de contratos inteligentes en los iguales que se hayan unido al canal que tiene instalado el contrato. A continuación, los iguales pueden utilizar los contenedores en ejecución para realizar transacciones.  
- **Solo un miembro de la red tiene que crear una instancia de un contrato inteligente.**
- **Si un igual con un contrato inteligente instalado se une a un canal en el que ya se ha creado una instancia de la misma versión del contrato inteligente, el contenedor del contrato inteligente se inicia automáticamente.**  

En esta guía de aprendizaje se muestran los pasos para utilizar la consola de {{site.data.keyword.blockchainfull_notm}} Platform para gestionar el despliegue de contratos inteligentes en la red:

- [Instalar contratos inteligentes en los iguales](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Crear instancias de los mismos en los canales](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [Especificar políticas de aprobación](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Actualizar el código y las políticas del contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


**Antes de empezar**

Para poder instalar un contrato inteligente, asegúrese de que tiene preparado lo siguiente.

- Debe [crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) o [unirse a una red](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform.
- Como los contratos inteligentes se instalan en los iguales, asegúrese de [añadir iguales](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) a la consola.  
- Se crean instancias de los contratos inteligentes en un canal. Por lo tanto, debe [crear un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) o [unirse a un canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) utilizando la consola.

## Paso uno: escribir un contrato inteligente

La consola de {{site.data.keyword.blockchainfull_notm}} gestiona el *despliegue* de los contratos inteligentes en lugar del desarrollo. Si está interesado en el desarrollo de contratos inteligentes, puede empezar por utilizar las guías de aprendizaje que proporciona la comunidad de Hyperledger Fabric y las herramientas que proporciona {{site.data.keyword.IBM_notm}}.

- Para obtener información sobre cómo se pueden utilizar los contratos inteligentes para realizar transacciones entre varias partes, consulte el tema [tema sobre desarrollo de aplicaciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "tema sobre desarrollo de aplicaciones") de la documentación de Hyperledger Fabric.
- Para obtener una guía de aprendizaje completa sobre el uso de una aplicación para interactuar con contratos inteligentes, consulte la [guía de aprendizaje del documento comercial de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "guía de aprendizaje del documento comercial de Hyperledger Fabric").
- Para obtener información sobre cómo incorporar mecanismos de control de accesos al contrato inteligente, consulte el tema sobre [Código de encadenamiento para desarrolladores ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "Código de encadenamiento para desarrolladores").
- Cuando esté listo para empezar a crear contratos inteligentes, puede utilizar la [extensión de código {{site.data.keyword.blockchainfull_notm}} Visual Studio ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") para comenzar a crear su propio proyecto de contrato inteligente. También puede utilizar dicha extensión para [conectarse directamente a la red desde el código de Visual Studio](docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- Cuando esté listo para instalar, el contrato inteligente se debe empaquetar en [formato .cds ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "empaquetado de contratos inteligentes") para que se pueda instalar en iguales. Para obtener más información, consulte [Empaquetado de contratos inteligentes](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract).
<!-- Update the tutorial link to release1-4 when it is published -->


## Paso dos: instalar un contrato inteligente
{: #ibp-console-smart-contracts-install}

Utilice la consola para seguir estos pasos:

1. Pulse el separador **Contratos inteligentes** para instalar uno o varios contratos inteligentes.
2. Pulse **Instalar contrato inteligente** para cargar el archivo del paquete del contrato inteligente en [formato .cds ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "empaquetado de contratos inteligentes").
Puede utilizar la extensión de código de {{site.data.keyword.blockchainfull_notm}} Visual Studio para [crear un paquete de contrato inteligente](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Cuando instale el paquete desde el separador **Contratos inteligentes**, puede seleccionar uno o varios nodos iguales en los que instalar los contratos inteligentes.

Si solo existe un igual en la consola, el contrato inteligente se instalará en el mismo. No se le solicitará que seleccione un igual en el que instalar el contrato inteligente. Puede ir al separador Nodos y pulsar en un igual gestionado por la consola para ver la lista de contratos inteligentes que se han instalado en un igual individual.

Puede volver al separador **Contratos inteligentes** más adelante para instalar el contrato inteligente en otros iguales, incluso después de que se haya creado una instancia del contrato inteligente en el canal. Si la versión del contrato inteligente que se instala es la misma que la versión de la que se ha creado una instancia, estos iguales adicionales pueden procesar transacciones igual que los iguales existentes.
{:tip}

**Esta consola no se puede utilizar para instalar archivos `.bna` de Hyperledger Composer.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## Paso tres: crear una instancia de un contrato inteligente
{: #ibp-console-smart-contracts-instantiate}

Se crean instancias de los contratos inteligentes en un canal. Cualquier miembro de la consola que tenga iguales unidos a un canal puede crear una instancia de un contrato inteligente y especificar la [política de aprobación](/docs/services/blockchain/glossary.html#glossary-endorsement-policy) asociada.

Utilice la consola para seguir estos pasos:

1. En el separador de contratos inteligentes, busque el contrato inteligente en la lista instalada en sus iguales y pulse **Crear instancia** en el menú de desbordamiento de la parte derecha de la fila.
2. En el panel lateral que se abre, seleccione un canal, `channel1`, en el que crear la instancia del contrato inteligente, y seleccione el clasificador en el que reside el canal, `Clasificador`, si está siguiendo la nomenclatura de esta guía de aprendizaje. Pulse **Siguiente**.
3. Especifique la [política de aprobación para el contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse), que se describe en la sección siguiente.
4. También tiene que seleccionar los miembros de la organización que se incluirán en la política. Si sigue la guía de aprendizaje, sería `org1msp` y posiblemente `org2msp` si ha completado las guías de aprendizaje sobre **cómo crear una red** y **cómo unirse a una red**.
5. En el último panel se le solicitará que especifique la función de contrato inteligente que desea ejecutar cuando se inicie el contrato inteligente, junto con los argumentos asociados para pasar a dicha función.

Puede ver todos los contratos inteligentes que se han creado en un canal pulsando sobre el icono del canal en el panel de navegación de la izquierda, seleccionando un canal de la tabla y pulsando el separador **Detalles del canal**.

Tenga en cuenta que si utiliza un clúster del servicio Kubernetes de {{site.data.keyword.cloud_notm}} gratuito, la creación de una instancia puede tardar mucho más que en un clúster de pago. En función del número de iguales que haya desplegado en el clúster, esto puede tardar varios minutos.

Si el contrato inteligente incluye una definición de recopilación de datos privados, no se pueden crear instancias del contrato inteligente desde la consola de {{site.data.keyword.blockchainfull_notm}}. Consulte estas [recomendaciones](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) para ver cómo crear una instancia de un contrato inteligente que incluye datos privados.
{:note}

La combinación de **instalación y creación de instancias** es una función muy potente, porque permite a un igual utilizar un solo contrato inteligente en varios canales. Los iguales pueden unirse a varios canales que utilicen el mismo contrato inteligente, pero con distintos conjuntos de miembros de la red capaces de acceder a los datos. Un igual puede instalar el contrato inteligente una vez y, a continuación, utilizar el mismo contenedor de contrato inteligente en cualquier canal en el que se haya creado una instancia. Este enfoque ligero ahorra cálculo y espacio de almacenamiento, y le ayuda a escalar la red.

## Paso cuatro: enviar transacciones utilizando las aplicaciones cliente
{: #ibp-console-smart-contracts-connect-to-SDK}

Después de que se haya creado una instancia de un contrato inteligente en un canal, puede utilizar una aplicación cliente para realizar transacciones con otros miembros de la red. Las aplicaciones pueden invocar la lógica empresarial contenida en los contratos inteligentes para crear, transferir o actualizar activos en el libro mayor de blockchain.

### Conexión con SDK
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
El separador **Contratos inteligentes** contiene la información que necesita para conectarse a un contrato inteligente del que se ha creado una instancia desde una app cliente. Junto a cada contrato inteligente del que se ha creado una instancia, vaya al menú de desbordamiento. Pulse el botón **Conectar con el SDK**. Se abrirá un panel lateral que proporciona la información que necesita para conectarse a este contrato inteligente: el nombre del contrato, el nombre del canal y el perfil de conexión. Para obtener más información, consulte
[Creación de
aplicaciones](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Especificación de una política de aprobación
{: #ibp-console-smart-contracts-endorse}

Cada contrato inteligente debe tener una política de aprobación, que se especifica cuando se crea la instancia. La política de aprobación especifica el conjunto de organizaciones, los iguales, en un canal que puede ejecutar el contrato inteligente y validar de forma independiente la salida de la transacción. Por ejemplo, una política de aprobación puede especificar que una transacción se añada al libro mayor solo si la mayoría de los miembros del canal validan la transacción. La organización que crea la instancia del contrato inteligente puede seleccionar de entre los miembros que hayan creado una instancia del código de encadenamiento para que se conviertan en validadores, y establece la política de aprobación para todos los miembros del canal. Para actualizar la política de aprobación, siga los pasos para [actualizar el contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).

Cuando siga los pasos para [crear una instancia de un contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate), puede utilizar el panel lateral para establecer la política de aprobación de un contrato después de seleccionar el canal. Utilice este panel para especificar una política simple seleccionando los iguales que necesitan aprobar la transacción en la lista de iguales que han instalado el contrato inteligente en el canal. Puede utilizar este método para especificar una política de aprobación de todos los miembros del canal, una mayoría de ellos, un solo miembro o un simple +1 que impide a los miembros que se autofirmen. La política de aprobación predeterminada se establece en `1 de x`, lo que significa que solo se necesita un miembro para aprobar una transacción de contrato inteligente.

Pulse el botón **Avanzado** si desea especificar una política en formato JSON. Puede utilizar este método para especificar políticas de aprobación más complicadas, como la necesidad de que un miembro determinado del canal tenga que validar una transacción, junto con la mayoría de los demás miembros. Encontrará [ejemplos de políticas avanzadas de aprobación ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Políticas de aprobación de ejemplo") adicionales en la documentación de Hyperledger Fabric. Para obtener más información sobre cómo escribir políticas de aprobación en JSON, consulte la [documentación del SDK de nodo de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Documentación del SDK de nodo de Hyperledger Fabric").

## Actualización de un contrato inteligente
{: #ibp-console-smart-contracts-upgrade}

Puede actualizar un contrato inteligente para cambiar su código o su política de aprobación mientras mantiene su relación con los activos en el libro mayor. Hay diversas razones por las que es posible que desee actualizar un contrato inteligente del que se ha creado una instancia.
1. Puede actualizar el contrato inteligente para añadir o eliminar funciones de su código e iterar en la lógica de la red de negocio.
2. Siempre que se añada un nuevo miembro a un canal, la política de aprobación de los contratos inteligentes de los que se ha creado una instancia *debe* actualizarse para que incluya el nuevo miembro del canal. Para trabajar con el nuevo miembro del canal, el contrato inteligente se debe volver a empaquetar con un nuevo número de versión y se debe crear una instancia del mismo en el canal, aunque el propio contrato inteligente no se haya modificado. De lo contrario, la aprobación de la transacción no puede realizar correctamente.
3. Los argumentos de inicialización del contrato inteligente han cambiado.

**Para poder actualizar un contrato inteligente del que se ha creado una instancia, se debe instalar la nueva versión del contrato inteligente en todos los iguales del canal que ejecutan el nivel anterior del contrato inteligente.**

### Cómo actualizar un contrato inteligente
{: #ibp-console-smart-contracts-upgrade-howto}

Para actualizar un contrato inteligente, instale el código actualizado especificando el mismo nombre de contrato inteligente, pero con un nuevo número de versión. Si ha instalado una versión más reciente de un contrato inteligente en cualquier igual del canal, observe que ahora junto a la versión original aparece el botón `Actualización disponible` en la tabla **Contratos inteligentes instanciados**.

{:important}
Cuando un miembro nuevo que va a ejecutar el contrato inteligente se une al canal, es obligatorio actualizar el contrato inteligente instalando una nueva versión en todos los iguales y creando una instancia del mismo en el canal con una política de aprobación modificada que incluya el nuevo miembro.

- Desplácese hacia abajo hasta la tabla **Contratos inteligentes instanciados**.
- En la tabla **Contratos inteligentes instanciados**, localice la versión de contrato inteligente y el canal que desea actualizar. Debe aparecer junto al mismo la etiqueta **Actualización disponible**.
- Pulse el **menú de desbordamiento** en el lado derecho de la fila del contrato inteligente y pulse **Actualizar**
para realizar los pasos siguientes:  

 1. Seleccione la versión del contrato inteligente que desee actualizar en el canal en la lista desplegable.
 2. Actualice la política de aprobación añadiendo o eliminando miembros del canal. También puede pulsar **Avanzado** para pegar en una nueva serie en formato JSON que modifique la política existente.
 3. (Opcional) Modifique los valores de los argumentos de inicialización del contrato inteligente si los parámetros han cambiado. Si no está seguro de ello, consulte con el desarrollador de su contrato inteligente. Si no se han modificado, puede dejar este campo en blanco.

Después de actualizar el contrato inteligente, cambiará la versión del contrato del que se crea una instancia en el canal y cambiará el contenedor de contrato inteligente para todos los iguales que han instalado la nueva versión.

### Consideraciones al actualizar contratos inteligentes
{: #ibp-console-smart-contracts-upgrade-considerations}

1. ¿Tengo que instalar la nueva versión del contrato inteligente en todos mis iguales?  

  Cuando se invoca un contrato inteligente en un igual, este intenta ejecutar la versión de la que se ha creado una instancia en el canal. Si se está ejecutando una versión anterior en el igual, se produce un error. Por lo tanto, antes de actualizar un contrato inteligente en un canal, *se recomienda instalar la versión más reciente del contrato inteligente en todos los iguales que ejecutan la versión anterior.*  

2. ¿Puede una versión de contrato inteligente posterior seguir procesando datos en el libro mayor de una versión anterior del contrato inteligente?  

  Sí. Siempre que el nuevo código del contrato inteligente gestione los datos de forma compatible (añadiendo nuevos campos JSON y no cambiando la semántica ni el tipo de los campos existentes), cualquier versión posterior del contrato inteligente se puede ejecutar sobre los datos de una versión anterior.

3. ¿Qué les ocurre a mis iguales si me olvido de actualizarlos a la versión más reciente antes de actualizar el contrato inteligente en el canal?  

  Después de actualizar el canal para utilizar la nueva versión del contrato inteligente, si aún hay iguales en el canal que ejecutan la versión anterior, dichos iguales ya no pueden aprobar transacciones para el contrato inteligente. Además, se corre el riesgo de no tener suficientes aprobaciones para que las transacciones se confirmen en el libro mayor, en función de cómo se haya definido la política de aprobación del contrato inteligente. Sin embargo, se puede instalar la nueva versión del contrato inteligente en estos iguales más tarde y podrán aprobar de nuevo las transacciones, poniéndose al día de forma efectiva.

## Datos privados
{: #ibp-console-smart-contracts-private-data}

Los datos privados son una característica de las redes de Hyperledger Fabric en la versión 1.2 o superior y se utilizan para mantener la información confidencial privada de los miembros de otras organizaciones **en un canal**. La privacidad de los datos se consigue mediante el uso de [recopilaciones de datos privados  ![Icono de enlace externo")](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "¿Qué es una recopilación de datos privados?"). Por ejemplo, supongamos que varios mayoristas y un conjunto de agricultores se unen a un solo canal. Si un agricultor y un mayorista desean realizar una transacción en privado, pueden crear un canal para este fin. Pero también pueden optar por crear una recopilación de datos privados en el contrato inteligente que controle sus interacciones empresariales para mantener la privacidad de los aspectos confidenciales de la venta, como por ejemplo el precio, sin tener que crear un canal secundario. Para obtener más información sobre cuándo utilizar datos privados dentro de blockchain, visite el artículo sobre el concepto de [Datos privados ![Icono de enlace externo")](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "Datos privados") en la documentación de Fabric.

Para poder utilizar datos privados con {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta, se deben cumplir las tres condiciones siguientes:  
1. **Defina la recopilación de datos privados.** Se puede añadir un archivo de recopilación de datos privado a su contrato inteligente. Luego, en el momento de la ejecución, la aplicación cliente puede utilizar las API del código de encadenamiento específicos de los datos privados para especificar y recuperar datos de la recopilación. Para obtener más información sobre cómo utilizar recopilaciones de datos privados con su contrato inteligente, consulte la guía de aprendizaje sobre [Utilización de datos privados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "Utilización de datos privados") en la documentación de Fabric.  

2. **Instale y crea una instancia del contrato inteligente.** Una vez que se haya definido la recopilación de datos privados del contrato inteligente, deberá instalar el contrato inteligente en los iguales miembros del canal. Cuando cree una instancia del contrato inteligente en el canal, debe especificar la configuración de la recopilación. La consola de {{site.data.keyword.blockchainfull_notm}} no proporciona actualmente ninguna forma de especificar una definición de colección **durante** la creación de una instancia del contrato inteligente. No obstante, puede utilizar el SDK de Fabric para instalar, crear una instancia o actualizar un contrato inteligente que utilice datos privados. Para obtener más información, consulte [Cómo utilizar datos privados
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "Cómo utilizar datos privados") en la documentación del SDK de nodo.

 **Nota:** un cliente debe ser un administrador de su igual para poder instalar o crear una instancia de un contrato inteligente. Por lo tanto, tiene que descargar los certificados de la identidad de administrador del igual desde la cartera de la consola y pasar la clave pública y privada del administrador del igual directamente al SDK en lugar de crear una identidad de aplicación. Para ver un ejemplo de cómo pasar un par de claves al SDK, consulte [Conexión a la red utilizando las API de SDK de Fabric SDK de bajo nivel](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  

3. **Configure iguales de ancla.** Debido a que el [protocolo gossip ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Protocolo gossip de diseminación de datos") entre organizaciones debe estar habilitado para que los datos privados funcionen, debe existir un igual de ancla para cada organización de la definición de recopilación. Este igual de ancla no es un **tipo** especial de igual; es solo el igual que la organización da a conocer a otras organizaciones, y arrancar así el protocolo gossip entre organizaciones. Por lo tanto, se debe definir al menos un [igual de ancla ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Iguales de ancla") para cada organización de la definición de recopilación.
 - Para configurar un igual para que sea un igual de ancla, pulse el separador **Canales** y abra el canal en el que se ha creado la instancia del contrato inteligente.  
 - Pulse el separador **Detalles del canal**.
 - Desplácese hacia abajo hasta la tabla de iguales de ancla y pulse **Añadir igual de ancla**.
 - Seleccione al menos un igual de cada organización de la definición de recopilación que desee que sirva como igual de ancla para la organización. Por motivos de redundancia, tenga en cuenta la posibilidad de seleccionar más de un igual desde cada organización de la recopilación.

Ahora el canal está configurado para utilizar datos privados.

## Resolución de problemas
{: #console-operate-troubleshooting}

**Problema:** la instalación, creación de una instancia o actualización de un contrato inteligente falla con un error en la consola.  
**Solución:** si una de estas acciones sobre un contrato inteligente falla, [consulte los registros del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) para ver los errores.

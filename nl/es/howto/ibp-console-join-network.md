---

copyright:
  years: 2019

lastupdated: "2019-03-05"

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


**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.  

Si aún no ha desplegado la consola en un clúster de Kubernetes utilizando el servicio Kubernetes de {{site.data.keyword.cloud_notm}}, consulte [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). Puede crear un nuevo clúster de Kubernetes para el despliegue de la consola o puede utilizar uno existente en su cuenta de {{site.data.keyword.cloud_notm}}. Después de desplegar {{site.data.keyword.blockchainfull_notm}} Platform en su clúster de Kubernetes, puede iniciar la consola para crear y gestionar los componentes de blockchain.

Tanto si realiza el despliegue en un clúster de Kubernetes de pago como si lo hace en uno gratuito, utilice el panel de control de Kubernetes para descubrir los recursos disponibles cuando elija desplegar nodos y crear canales. Es su responsabilidad gestionar el clúster de Kubernetes y desplegar recursos adicionales si es necesario. Aunque los componentes se desplegarán correctamente en un clúster gratuito, cuantos más componentes añada más lenta será su ejecución.
{: note}

## Serie de guías de aprendizajes de red de ejemplo
{: #ibp-console-join-network-structure}

Esta serie de guías de aprendizaje de tres partes le guía por el proceso de creación e interconexión de una red Hyperledger Fabric de varios nodos relativamente sencilla utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 para desplegar una red en el clúster de Kubernetes e instalar y crear una instancia de un contrato inteligente.

* La [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) le ayuda en el proceso de alojar una red mediante la creación de un clasificador y un igual.
* **Cómo unirse a una red** En esta guía de aprendizaje se explica el proceso de unirse a una red existente creando un igual y uniéndolo a un canal.
* La guía para [Desplegar un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) contiene información sobre cómo escribir un contrato inteligente y cómo desplegarlo en la red.

Puede seguir los pasos de estas guías de aprendizaje para crear una red con varias organizaciones en un clúster con fines de desarrollo y prueba. Utilice la guía de aprendizaje sobre cómo **crear una red** si desea formar un consorcio de blockchain mediante la creación de un nodo clasificador y la adición de organizaciones. Utilice la guía de aprendizaje sobre cómo **unirse a una red** para conectar un igual a la red. Si sigue las guías de aprendizaje con distintos miembros del consorcio puede crear una red blockchain verdaderamente **distribuida**.  


Esta guía de aprendizaje está pensada para mostrar cómo unir un igual a una red **existente**. Se supone que ya existe un clasificador, que aloja la red, en el clúster o en otro clúster de {{site.data.keyword.blockchainfull_notm}} Platform. Si no dispone de una red existente a la que unirse, visite la guía de aprendizaje sobre [Creación de una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) para aprender a crear una. La guía de aprendizaje sobre cómo **unirse a una red** le guía por el proceso de crear los siguientes componentes de blockchain de `Org2`, resaltados en el recuadro azul: ![Estructura para unirse a una red](../images/ib2-join-network.png "Estructura para unirse a una red")  
*Figura 1. Estructura de unión de red*  
Siga los pasos de la guía de aprendizaje sobre cómo **unirse a una red** para crear los siguientes componentes y realizar las siguientes acciones:

* **Una organización igual** `Org2`  
  Cree la definición del proveedor de servicios de pertenencia (MSP) de Org2, la cual define la organización `Org2`.
* **Un igual** `Org2 igual`   
  El libro mayor de blockchain, `Libro mayor x` en la ilustración anterior, se mantiene mediante iguales distribuidos. El igual se despliega con [Couch DB ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) como base de datos.
* **Una autoridad emisora de certificados (CA)** `CA de Org2`
  Una CA es el nodo que emite certificados a todos los miembros de la organización. Creamos una CA por organización igual `Org2`.
* **Unión de un canal**
  En la guía de aprendizaje se describe cómo unir el canal creado por la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

En esta guía de aprendizaje, suministramos **valores recomendados** para algunos de los campos de la consola. Esto permite reconocer más fácilmente los nombres e identidades en los separadores y listas desplegables. Estos valores no son obligatorios, pero los encontrará útiles. Ofrecemos una tabla de valores recomendados tras cada tarea.
{:tip}

## Paso uno: crear una organización adicional y su punto de entrada al blockchain
{: #ibp-console-join-network-create-ca-org2}

Para cada organización que desee crear mediante la consola, debe desplegar al menos una CA. Una CA es el nodo que emite certificados a todos los participantes en la red (iguales, clasificadores, clientes, etc.). Estos certificados, que incluyen un par de claves pública y privada, permiten que los participantes de la red se comuniquen, se autentiquen y, en última instancia, realicen transacciones. Estas CA crearán todas las identidades y certificados que pertenecen a su organización, además de definir la propia organización. Luego puede utilizar dichas identidades para desplegar nodos, trabajar con la red y enviar transacciones al blockchain. Para obtener más información sobre la CA y las identidades que tendrá que crear, consulte [Gestión de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

En esta guía de aprendizaje, crearemos una organización y crearemos una **CA**.

### Creación de la entidad emisora de certificados de la organización igual
{: #ibp-console-join-network-create-CA-org2CA}

Como parte de esta guía de aprendizaje, su CA emite las claves públicas y privadas para sus usuarios y nodos. Estas identidades no están gestionadas por {{site.data.keyword.IBM_notm}} y las claves no se almacenan en el clúster de Kubernetes ni en la consola. Solo se almacenan en el almacenamiento local del navegador. Por lo tanto, asegúrese de exportar sus identidades y MSP de la organización. Si intenta acceder a la consola desde una máquina distinta o desde otro navegador, tiene que importar estas identidades y definiciones de organización.  
{:important}

Siga los pasos siguientes desde la consola:  

1. Vaya a separador **Nodos** de la izquierda y pulse **Añadir entidad emisora de certificados**. Los paneles laterales de cuatro pasos le permitirán personalizar la CA que desea crear y la organización para la que esta CA emitirá claves.
2. Pulse **{{site.data.keyword.cloud_notm}}** en **Crear entidad emisora de certificados**.
3. Utilice el segundo panel lateral para dar a la CA un **nombre de visualización**. El valor recomendado para esta CA es `CA de Org2`.
4. En el panel siguiente, especifique las credenciales de administrador de CA especificando el **ID de administrador** `admin` y proporcionando el secreto que desee, aunque recomendamos utilizar `adminpw` en esta guía de aprendizaje.
5. Pulse **Siguiente** y, a continuación, **Añadir entidad emisora de certificados**.

**Tarea: creación de la CA de la organización igual**

  | **Campo** | **Nombre de visualización** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|
  | **Crear CA** | CA de Org2  | admin | adminpw |

*Figura 2. Creación de la CA de la organización igual*  
Después de desplegar la CA, la utilizará cuando cree el MSP de la organización, registre usuarios y cree su punto de entrada en una red, el **igual**.

### Utilización de la CA para registrar identidades
{: #ibp-console-join-network-use-CA-org2}

Cada nodo o aplicación que desee crear necesita claves públicas y privadas para participar en la red blockchain. También tiene que crear claves de administración para estos nodos y aplicaciones para que pueda gestionarlos desde la consola. Utilizará la CA para crear dos identidades:

* **Un administrador de la organización** Esta identidad le permite trabajar con nodos utilizando la consola de la plataforma.
* **Una identidad igual** Esta identidad le permitirá desplegar un igual.

Para generar estos certificados, siga los pasos siguientes:

1. En la consola, utilice el separador **Nodos** para ir a la `CA de Org2` que ha creado.
2. Después de seleccionar la CA, tendrá que registrar un administrador para esta organización, `org2`, además de una identidad para el propio igual. Ya debería ver una identidad en esta página; es el administrador que ha creado para la CA. Para registrar nuestros nuevos usuarios, pulse el botón **Registrar usuario**.
3. Para el administrador de la organización, asígnele el ID de inscripción `org2admin`. Puede utilizar cualquier secreto, pero le recomendamos utilizar `org2adminpw` para seguir esta guía. Pulse **Siguiente**.
4. En el siguiente paso, defina el Tipo de esta identidad como `cliente` y seleccione entre las organizaciones afiliadas de la lista desplegable. El campo de afiliación es para usuarios avanzados y no se utiliza en la guía de aprendizaje. Los elementos de la lista son las afiliaciones predeterminadas de la CA de Fabric. Si desea obtener más información sobre cómo utiliza la CA de Fabric las afiliaciones, consulte este tema en [Registro de
una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Por ahora, seleccione cualquier afiliación de la lista, por ejemplo org2, y pulse Siguiente.
5. Puede dejar los campos **Número máximo de inscripciones** y **Añadir atributos** en blanco. No se utilizan en esta guía de aprendizaje, pero puede obtener más información sobre su función en este tema sobre [Registro de identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
6. Una vez que se haya registrado el administrador de la organización, repita los pasos del dos al cinco para la identidad del igual, utilizando la misma `CA de Org2`, proporcionándole el ID de inscripción `peer2`. Como antes, recomendamos utilizar el secreto `peer2pw` como ayuda para seguir esta guía. Se trata de una identidad de nodo, de modo que seleccione `igual` como **Tipo** en el paso siguiente. Como antes, pase por alto **Número máximo de inscripciones** y **Atributos**.

**Tarea: registrar usuarios**

  |  **Campo** | **Descripción** | **ID de inscripción** | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registrar usuarios** |  Admin de Org2 | org2admin | org2adminpw |
  | | Identidad del igual |  peer2 | peer2pw |

*Figura 3. Utilización de su CA para registrar usuarios*  

### Creación del MSP de la organización igual
{: #ibp-console-join-network-create-peers-org2}

Ahora que hemos creado la CA del igual y la hemos utilizado para **registrar** las identidades de nuestra organización, tenemos que crear una definición formal de la organización igual, que se conoce como la definición de Proveedor de Servicios de pertenencia (MSP). Muchos iguales pueden pertenecer a una organización. **No es necesario que cree una nueva organización cada vez que cree un igual.** Como esta es la primera vez que revisamos la guía de aprendizaje, crearemos el ID de MSP para esta organización. Durante el proceso de creación del MSP, vamos a generar certificados para la identidad `org2admin` y los vamos a añadir a la cartera de la consola.

1. Vaya al separador **Organizaciones** en el panel de navegación izquierdo y pulse **Crear definición de MSP**.
2. Asigne al MSP un nombre de visualización, como por ejemplo `MSP de Org2`, y un ID, como `org2msp`. Si desea especificar su propio ID de MSP en este campo, asegúrese de seguir las especificaciones de la herramienta de sugerencias sobre las limitaciones de este nombre.
3. En **Detalles de la entidad emisora de certificados raíz**, especifique la CA del igual que hemos creado como la CA raíz para la organización. Si esta es su primera vez que examina esta guía de aprendizaje, solo debería ver una: `CA de Org2`. Selecciónela.
4. Los campos **ID de inscripción** y **Secreto de inscripción** bajo la misma contendrán el ID y el secreto de inscripción del primer usuario que ha creado con la CA. Podría utilizar estos valores, pero no le recomendamos que utilice la identidad de administrador de CA como administrador de la organización.  Por motivos de seguridad, recomendamos especificar el ID y el secreto de inscripción que ha creado para el administrador de la organización, `org2admin` y `org2adminpw`. A continuación, asigne a esta identidad un nombre de visualización, como por ejemplo `Admin de Org2`.
5. Pulse el botón **Generar** para inscribir esta identidad como administrador de la organización y exporte la identidad a la cartera, donde se utilizará cuando se cree el igual y cuando se creen canales.
6. Pulse **Exportar** para exportar los certificados de administrador al sistema de archivos. Como se ha dicho anteriormente, esta identidad no se almacena en el clúster ni la gestiona {{site.data.keyword.IBM_notm}}. Solo se almacena en el almacenamiento local del navegador. Si cambia de navegador, tiene que importar esta identidad en la cartera de la consola para poder administrar el igual.
7. Pulse **Crear definición de MSP**.

**Tarea: crear la definición de MSP de la organización igual**

  |  | **Nombre de visualización** | **ID de MSP** | **ID de inscripción**  | **Secreto** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Crear organización** | MSP de Org2 | org2msp |||
  | **CA raíz** | CA de Org2 ||||
  | **Cert de administrador de la org** | |  | org2admin | org2adminpw |
  | **Identidad** | Admin de Org2 |||||

  *Figura 4. Crear la definición de MSP de la organización igual*  

Una vez que haya creado el MSP, debe poder ver el administrador de la organización igual en la cartera de la consola.

**Tarea: comprobar la cartera de la consola**

  | **Campo** |  **Nombre de visualización** | **Descripción** |
  | ------------------------- |-----------|----------|
  | **Identidad** | Admin de Org2 | Identidad de Org2 |

  *Figura 5. Comprobar la cartera de la consola*  

Para obtener más información sobre los MSP, consulte el apartado sobre [gestión de organizaciones](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

Es importarte exportar y guardar el MSP, porque esto permite añadir la organización a un consorcio alojado en otra consola.
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
3. Asigne a su igual el **Nombre de visualización** `Org2 igual`.
4. En la pantalla siguiente, seleccione `CA de Org2` como CA. A continuación, especifique el ID y el secreto de inscripción que ha creado para el igual, `peer2` y `peer2pw`. Luego seleccione su MSP, `MSP de Org2`, en la lista desplegable y pulse **Siguiente**.
5. El siguiente panel lateral solicita la información de la CA de TLS. Aunque es posible crear administradores independientes para la CA de TLS que se ha desplegado con la CA, no es necesario hacerlo.
   - Asigne a **ID de inscripción de TLS** el valor `admin` y al secreto el valor `adminpw`, los mismos valores que el ID de inscripción y el secreto de inscripción que ha asignado al crear la CA.
   - El **Nombre de host de CSR de TLS** es para usuarios avanzados y se utiliza para especificar un nombre de dominio personalizado que se puede utilizar para gestionar el punto final del igual. Por ahora deje el **Nombre de host de CSR de TLS** en blanco, ya que no se utiliza en esta guía de aprendizaje.
6. El último panel lateral le solicitará que **Asocie una identidad** y la convierta en administrador de su igual. Seleccione la identidad de administrador del igual `Admin de Org2`.
7. Revise el resumen y pulse **Añadir igual**.

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

## Paso dos: añadir su organización a la lista de organizaciones que pueden realizar transacciones
{: #ibp-console-join-network-add-org2}

Como ya hemos indicado anteriormente, una organización igual debe ser miembro de un consorcio del clasificador para que pueda crear o unirse a un canal. Esto se debe a que los canales son, a nivel técnico, **vías de acceso de mensajería** entre iguales a través del clasificador. Del mismo modo que un igual se puede unir a varios canales sin que la información pase de un canal a otro, un clasificador puede tener varios canales que se ejecutan a través del mismo sin exponer datos a organizaciones de otros canales.

Puesto que solo los administradores del clasificador pueden añadir organizaciones iguales al consorcio, tendrá que **ser** el administrador del clasificador o deberá **enviar** la información de MSP al administrador del clasificador. En esta guía de aprendizaje, puede **añadir la organización igual al clasificador en la consola**, si aloja el clasificador. O bien tendrá que **exportar la información de la organización** y facilitarla al administrador del clasificador, que ha creado la red en la que reside el clasificador. Luego el administrador del clasificador puede importar su organización y añadirle al consorcio.

### Añadir la organización del igual al clasificador en mi consola
{: #ibp-console-join-network-add-org2-local}

**Siga estos pasos únicamente si la consola ya incluye el clasificador y el canal que desea unir.** De lo contrario, continúe en [Exporta la información de la organización](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).  

Puesto que solo los administradores del clasificador pueden añadir organizaciones iguales al consorcio, tendrá que ser el administrador del clasificador, lo que significa que tiene la identidad admin de la organización del clasificador en la cartera de la consola.  

1. Vaya al separador **Nodos**.
2. Desplácese hasta el clasificador que desea utilizar y pulse en el mismo para abrirlo.
3. En **Miembros del consorcio**, pulse **Añadir organización**.
4. En la lista desplegable, seleccione `MSP de Org2`, ya que este es el MSP que representa la organización igual `org2`.
5. Pulse **Añadir organización**.

Ahora añada la organización igual al canal.
1. Vaya al separador **Canales** y pulse `channel1`.
2. Pulse el icono **Valores** para actualizar el canal y añadir la organización igual al canal.
3. En la lista desplegable **Elegir entre los clasificadores disponibles**, asegúrese de que `Clasificador` está seleccionado.
4. En la lista desplegable **ID de MSP de actualizador de canal**, asegúrese de que `org1MSP` está seleccionado.
5. En la lista desplegable **Asociar identidad disponible**, asegúrese de que `Org1Admin` está seleccionado.
6. Desplácese hacia abajo hasta la sección llamada `Añadir organizaciones al canal`, abra la lista desplegable `Seleccionar un miembro de canal` y seleccione el MSP de la organización igual, `MSP de Org2` en esta guía de aprendizaje.
7. Pulse **Añadir** y luego asigne permisos para dicha organización. Le recomendamos que la convierta en `Operador` para que pueda actualizar el canal.
8. Pulse **Actualizar canal**.

Cuando finalice este proceso, `org2` podrá crear o unir un canal alojado en el `Clasificador`. Puede continuar con el [Paso tres: unir el igual al canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Exportar la información de la organización
{: #ibp-console-join-network-add-org2-remote}

**Siga estos pasos solo si el clasificador y el canal al que se va a unir el igual residen en otra instancia del servicio {{site.data.keyword.blockchainfull_notm}} Platform.** De lo contrario, puede pasar al [Paso tres: unir el igual al canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

Tiene que enviar la definición de MSP de la organización al administrador del clasificador para que se añada al consorcio; para ello siga los pasos siguientes.  

Cuando siga estos pasos, debe ser el administrador de la **organización igual**, lo que significa que tiene la identidad admin de la organización igual en la cartera de la consola:  
1. Vaya al separador **Organizaciones**. Verá que las organizaciones que se pueden exportar aparecen en la lista bajo **Organizaciones disponibles**. Pulse el botón **descargar** en el mosaico de la organización para descargar el archivo de configuración JSON que representa el MSP de la organización igual.
2. Envíe este archivo al administrador del clasificador en una operación fuera de banda.

### Importar la definición de la organización
{: #ibp-console-join-network-import-remote-msp}
El administrador del clasificador tiene que importar este archivo JSON para añadir la organización a su consola:
1. Vaya al separador **Organizaciones**, pulse el botón **Importar definición de MSP** y seleccione el archivo JSON que representa la definición del MSP de la organización igual.  
2. Vaya a la página **Nodos** y pulse el nodo clasificador. En el panel del nodo de ordenación, en **Miembros de consorcio**, pulse **Añadir organización**. En el panel lateral que se abre, seleccione `MSP de Org2` en la lista de definiciones de MSP desde el separador **Organizaciones**.
3. Vaya al separador **Canales**, pulse `channel1` y luego pulse el icono **Valores** para actualizar el canal y añadir la organización igual al canal.
4. En la lista desplegable **Elegir entre los clasificadores disponibles**, asegúrese de que `Clasificador` está seleccionado.
5. En la lista desplegable **ID de MSP de actualizador de canal**, asegúrese de que `org1MSP` está seleccionado.
6. En la lista desplegable **Asociar identidad disponible**, asegúrese de que `Org1Admin` está seleccionado.
7. Desplácese hacia abajo hasta la sección llamada `Añadir organizaciones al canal`, abra la lista desplegable `Seleccionar un miembro de canal` y seleccione el MSP de la organización igual, `MSP de Org2` en esta guía de aprendizaje.
8. Pulse **Añadir** y luego asigne permisos para dicha organización. Le recomendamos que la convierta en `Operador` para que pueda actualizar el canal.
9. Pulse **Actualizar canal**.

Después de que el administrador del clasificador haya unido la organización del igual al consorcio y al canal, deberá importar el nodo de ordenación en la consola. A continuación, puede unir los canales alojados por el servicio de ordenación. Siga los siguientes pasos para **importar** el clasificador:

Primero el administrador de la **organización del clasificador** tiene que completar estos pasos:
1. Vaya al nodo del clasificador en el separador **Nodos** y pulse el icono **Valores** situado a la derecha del nombre de nodo para abrir el panel lateral. Pulse el botón **Exportar** bajo **Acciones** para descargar un archivo de configuración JSON.
2. Envíe este archivo a la organización igual en una operación fuera de banda. Luego el administrador de la organización igual puede utilizar el archivo de configuración para añadir el clasificador a la consola.

### Importar el clasificador de otro clúster
{: #ibp-console-join-network-import-remote-orderer}

Ahora puede seguir estos pasos si es el administrador de la **organización igual**:
1. Vaya a la página **Nodos** y pulse **Añadir clasificador**.
2. Pulse {{site.data.keyword.cloud_notm}} en **Importar un clasificador existente**.
3. A continuación, pulse el botón **Cargar JSON** y seleccione el JSON que representa el propio nodo.
4. En el paso siguiente, cuando se le solicite que asocie una identidad, seleccione la identidad de la organización igual. En esta guía de aprendizaje sería `Admin de Org2`; luego pulse **Importar clasificador**.


## Paso tres: unir el igual al canal
{: #ibp-console-join-network-join-peer-org2}

Ya casi hemos terminado. Ahora el igual se puede unir a un canal existente. Debe obtener el `nombre del canal`, fuera de banda, del operador de la red que ha creado el canal. En la guía de aprendizaje sobre cómo **crear una red**, hemos creado un canal denominado `channel1`. Si aún no está allí, vaya al separador **Canales** en el panel de navegación de la izquierda.

Siga los pasos siguientes desde la consola:

1. Pulse el botón **Unir canal** para iniciar los paneles laterales.
2. Seleccione el clasificador denominado 'Clasificador' y pulse **Siguiente**.
3. Especifique el nombre del canal que desea unir, `channel1`, y pulse **Siguiente**.
4. Seleccione los iguales que desea unir al canal. A efectos de esta guía de aprendizaje, pulse `Org2 igual`.
5. Pulse **Unir igual**.

Si tiene intención de aprovechar las características de Hyperledger Fabric [Datos privados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/private-data/private-data.html "Datos privados") o [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-overview.html "Service Discovery"), debe configurar iguales de ancla en las organizaciones desde el separador **Canales**. Consulte este tema sobre [cómo configurar iguales de ancla para datos privados](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) mediante el separador **Canales** de la consola.

También puede crear un nuevo canal una vez que su organización sea miembro del consorcio. Siga los pasos para [crear un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) de la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Siguientes pasos
{: #ibp-console-join-network-next-steps}

Después de unir el igual a un canal, siga los pasos siguientes para desplegar un contrato inteligente y para empezar a enviar transacciones a blockchain:

- [Despliegue un contrato inteligente en la red](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) mediante la consola.
- Una vez que haya instalado el contrato inteligente y haya creado instancias del mismo, puede [enviar transacciones utilizando la aplicación cliente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Utilice [el ejemplo de documento comercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) para desplegar un contrato inteligente de ejemplo y enviar transacciones desde el código de aplicación de ejemplo.

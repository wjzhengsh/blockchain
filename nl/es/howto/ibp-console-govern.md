---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: network components, IBM Cloud Kubernetes Service, allocate resources, batch timeout, channel update, reallocate resources

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

# Administración de componentes
{: #ibp-console-govern}

Tras crear CA, iguales, clasificadores, organizaciones y canales, puede utilizar la consola para actualizar estos componentes.
{:shortdesc}

Si utiliza la versión de prueba beta de {{site.data.keyword.blockchainfull_notm}} Platform, es probable que algunos paneles de la consola no coincidan con la documentación actual, que se mantiene actualizada con la instancia de servicio con disponibilidad general (GA). Para obtener las ventajas de todas las funciones más recientes, en este momento se recomienda que suministre una nueva instancia de servicio de GA siguiendo las instrucciones de [Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.

## Cómo interactúa el servicio {{site.data.keyword.cloud_notm}} Kubernetes con la consola
{: #ibp-console-govern-iks-console-interaction}

El operador de la red es responsable de supervisar el uso de CPU, memoria y almacenamiento, así como de garantizar que los recursos adecuados están disponibles **antes** de intentar crear o redimensionar un nodo.
{:important}

Debido a que la instancia de la consola de {{site.data.keyword.blockchainfull_notm}} Platform y el clúster del servicio {{site.data.keyword.cloud_notm}} Kubernetes no se comunican directamente en relación con los recursos que están disponibles en el clúster, el proceso para desplegar o redimensionar componentes utilizando la consola debe seguir este patrón:

1. **Redimensione el despliegue que desee realizar**. Los paneles **Asignación de recursos** para la CA, el igual y el clasificador en la consola ofrecen las asignaciones de CPU, memoria y almacenamiento para cada nodo. Es posible que necesite ajustar estos valores en función de su caso de uso. Si no está seguro, comience por las asignaciones predeterminadas y ajústelas a medida que sea consciente de sus necesidades. De forma similar, el panel **Reasignación de recursos** muestra las asignaciones de recursos existentes.

  Para tener una idea de cuánto almacenamiento y potencia de cálculo necesitará en su clúster, consulte el diagrama que aparece después de esta lista, que contiene los valores predeterminados actuales para el igual, el clasificador y la CA.

2. **Compruebe si tiene suficientes recursos en el clúster del servicio {{site.data.keyword.cloud_notm}} Kubernetes**. Para supervisar los recursos de Kubernetes, se recomienda que utilice la herramienta [{{site.data.keyword.cloud_notm}} SysDig ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring with Sysdig") junto con el panel de control de {{site.data.keyword.cloud_notm}} Kubernetes. Si no tiene espacio suficiente en el clúster para desplegar o redimensionar recursos, necesitará aumentar el tamaño del clúster del servicio {{site.data.keyword.cloud_notm}} Kubernetes. Para obtener más información sobre cómo aumentar el tamaño de un clúster, consulte [Escalado de clústeres ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-ca#ca "Escalado de clústeres"). Si tiene espacio suficiente en el clúster, puede continuar con el paso 3.
3. **Utilice la consola para desplegar o redimensionar el nodo**. Si el nodo trabajador en el que se ejecuta el pod se está quedando sin recursos, puede añadir un nuevo nodo trabajador de mayor tamaño al clúster y suprimir luego el nodo trabajador existente.

| **Componente** (todos los contenedores) | CPU  | Memoria (GB) | Almacenamiento (GB) |
|--------------------------------|---------------|-----------------------|------------------------|
| **Igual**                       |  1,1          | 2,2                   | 200 (incluye 100GB para el igual y 100GB para CouchDB)|
| **CA**                         | 0,1            | 0,2                    | 20                     |
| **Clasificador**                    | 0,45           | 0,9                    | 100                    |

En casos en los que un usuario desee minimizar los cargos sin llegar a desactivar del todo un nodo ni suprimirlo, es posible reducir la escala a un mínimo de 0,001 CPU (1 miliCPU). Tenga en cuenta que el nodo no será funcional cuando utilice esta cantidad de CPU.

Aunque las figuras de este tema tratan de ser precisas, tenga en cuenta que hay veces en las que no se puede desplegar un nodo aunque parezca que tiene espacio suficiente en el clúster. Asegúrese de acceder a su panel de control de Kubernetes para ver cuándo se despliegan los componentes y los mensajes de error cuando no se desplieguen. En los casos en los que no se despliega un componente debido a la falta de recursos, aunque parezca que hay espacio suficiente en el clúster, es probable que tenga que desplegar recursos de clúster adicionales para que se pueda desplegar el componente.
{:important}

## Asignación de recursos
{: #ibp-console-govern-allocate-resources}

Aunque los usuarios de un clúster gratuito **deben utilizar los tamaños predeterminados** para los contenedores asociados con sus nodos, los usuarios de clústeres de pago pueden establecer estos valores durante la creación de los nodos.

El panel **Asignación de recursos** de la consola proporciona los valores predeterminados para los diversos campos que se invocan en la creación de un nodo. Se eligen estos valores porque representan un buen modo de comenzar. No obstante, cada caso de uso es diferente. Aunque en este tema se proporcionará orientación sobre las formas de pensar sobre estos valores, en última instancia, supervisar los nodos y encontrar los tamaños adecuados para ellos será responsabilidad del usuario. Por lo tanto, salvo situaciones en las que los usuarios estén seguros de que necesitarán valores distintos a los predeterminados, una estrategia práctica es utilizar estos valores predeterminados y ajustar los valores más adelante. Para obtener una visión general del rendimiento y la escala de Hyperledger Fabric, en el que se basa {{site.data.keyword.blockchainfull_notm}} Platform, consulte [Respuesta a sus preguntas sobre rendimiento y escalado de Hyperledger Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Blog acerca del rendimiento y escalado de Hyperledger Fabric").

Todos los contenedores asociados a un nodo tienen **CPU** y **memoria**, mientras que determinados contenedores asociados con el igual, el clasificador y la CA tienen también **almacenamiento**. Para obtener más información sobre las distintas opciones de almacenamiento que están disponibles en {{site.data.keyword.cloud_notm}}, consulte las [opciones de almacenamiento ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-kube_concepts#kube_concepts "Opciones de almacenamiento de Kubernetes"). Estudie las opciones con detenimiento antes de decidir qué clase de almacenamiento utilizar.

Aunque se puede cambiar la CPU y la memoria utilizando la consola y el panel de control del servicio {{site.data.keyword.cloud_notm}} Kubernetes después de que se haya creado un nodo, únicamente se podrá cambiar el almacenamiento más adelante utilizando la CLI de {{site.data.keyword.cloud_notm}}.
{:note}

Cada nodo tiene un contenedor de proxy web gRPC que arranca la capa de comunicación entre la consola y un nodo. Este contenedor tiene valores de recursos fijos y se incluye en el panel Asignación de recursos para proporcionar una estimación precisa del espacio necesario en el clúster de Kubernetes para desplegar el nodo. Debido a que no se pueden cambiar los valores para este contenedor, no se describirá el proxy web gRPC en las secciones siguientes.

### Entidades emisoras de certificados (CA)
{: #ibp-console-govern-CA}

A diferencia de los iguales y los clasificadores, que están activamente implicados en el proceso de transacción, las CA solo participan en el registro y la inscripción de identidades, y en la creación de un MSP. Esto implica que requieren menos CPU y memoria. Para saturar una CA, un usuario necesitaría abrumarla con solicitudes (probablemente utilizando API y un script), o haber emitido tantos certificados como para agotar el almacenamiento. Con las operaciones típicas, no se producirá ninguno de estos casos, aunque, como siempre, estos valores deberían reflejar las necesidades de un caso de uso concreto.

La CA solo tiene un contenedor asociado que se puede ajustar:

* **La propia CA**: encapsula los procesos de CA internos, como el registro y la inscripción de nodos y usuarios, así como el almacenamiento de una copia de cada certificado que emite.

#### Dimensionamiento de una CA durante la creación
{: #ibp-console-govern-CA-sizing-creation}

La CA tiene solo un contenedor individual con valores de los que debe preocuparse, ya que no se puede modificar el servidor proxy web gRPC.

| Recursos | Condición para aumentar |
|-----------------|-----------------------|
| **CPU y memoria de contenedor de CA** | Cuando espera que se bombardee la CA con registros e inscripciones. |
| **Almacenamiento de CA** | Cuando tiene pensado utilizar esta CA para registrar un gran número de usuarios y aplicaciones. |

### Iguales
{: #ibp-console-govern-peers}

El igual tiene tres contenedores asociados que se pueden ajustar:

- **El propio igual**: encapsula los procesos internos de igual (como la validación de transacciones) y el blockchain (en otras palabras, el historial de transacciones) para todos los canales a los que pertenece. Tenga en cuenta que el almacenamiento del igual también incluye los contratos inteligentes instalados en el igual.
- **CouchDB**: donde se almacenan las bases de datos de estado del igual. Recuerde que cada canal tiene una base de datos de estado distinta.
- **Contrato inteligente**: recuerde que, durante una transacción, se "invoca" (es decir, se ejecuta) el contrato inteligente correspondiente. Tenga en cuenta que todos los contratos inteligentes que instale en el igual se ejecutarán en un contenedor independiente dentro del contenedor del contrato inteligente, lo que se conoce como contenedor Docker-in-Docker.

#### Dimensionamiento de un igual durante la creación
{: #ibp-console-govern-peers-sizing-creation}

Como hemos observado en la sección sobre [Cómo interactúa el servicio Kubernetes de {{site.data.keyword.cloud_notm}} con la consola](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction), se recomienda utilizar los valores predeterminados para estos contenedores de igual y ajustarlos más adelante a medida que su modo de utilización se haga evidente.

| Recursos | Condición para aumentar |
|-----------------|-----------------------|
| **CPU y memoria de contenedor de igual** | Cuando se prevé un alto rendimiento de transacciones de inmediato. |
| **Almacenamiento de igual** | Cuando se prevé la instalación de muchos contratos inteligentes en este igual y para unirlo a muchos canales. Recuerde que este almacenamiento se utilizará también para almacenar contratos inteligentes de todos los canales a los que se ha unido el igual. Tenga en cuenta que consideraremos una transacción como "pequeña" si está en el rango de 10.000 bytes (10k). Debido a que el almacenamiento predeterminado es de 100G, esto implica que el almacenamiento del igual tendrá cabida para hasta 10 millones de transacciones totales antes de que sea necesario expandirlo (en la práctica, el número máximo será menor que este, ya que las transacciones pueden variar en tamaño y el número no incluye contratos inteligentes). Por lo tanto, aunque 100G podría parecer mucho más almacenamiento del necesario, tenga en cuenta que el almacenamiento es relativamente barato y que el proceso para aumentarlo es más difícil (requiere línea de mandatos) que aumentar la CPU o la memoria. |
| **CPU y memoria de contenedor de CouchDB** | Cuando se prevé un alto volumen de consultas en una base de datos de estado grande. Este efecto se puede mitigar en cierta medida a través del uso de [índices ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes "Documentación sobre índices de Hyperledger Fabric"). Sin embargo, volúmenes altos podrían afectar a CouchDB, lo que puede provocar que se agote el tiempo de espera de consulta y de transacción. |
| **Almacenamiento de CouchDB (datos de libro mayor)** | Cuando espera un rendimiento alto en muchos canales y no tiene pensado utilizar índices. No obstante, al igual que el almacenamiento del igual, el almacenamiento predeterminado de CouchDB es de 100G, lo que es significativo. |
| **CPU y memoria de contenedor de contrato inteligente** | Cuando espera un rendimiento alto en un canal, especialmente en casos donde se invocarán varios contratos al mismo tiempo.|

### Nodos de clasificación
{: #ibp-console-govern-ordering-nodes}

Debido a que los clasificadores no mantienen la base de datos de estado y no alojan contratos inteligentes, requieren menos contenedores que los iguales. Sin embargo, sí que incluyen el blockchain (el historial de transacciones), debido a que el blockchain es donde se almacena la configuración del canal y el clasificador debe conocer la configuración más reciente del canal para llevar a cabo su papel.

Al igual que ocurre con la CA, el clasificador solo tiene un contenedor asociado que se puede ajustar:

* **El propio clasificador**: encapsula los procesos internos de clasificador (como la validación de transacciones) y el blockchain para todos los canales que aloja.

#### Dimensionamiento de un clasificador durante la creación
{: #ibp-console-govern-orderer-sizing-creation}

Como hemos observado en la sección sobre [Cómo interactúa el servicio Kubernetes de {{site.data.keyword.cloud_notm}} con la consola](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-iks-console-interaction), se recomienda utilizar los valores predeterminados para estos contenedores de clasificador y ajustarlos más adelante a medida que su modo de utilización se haga evidente.

| Recursos | Condición para aumentar |
|-----------------|-----------------------|
| **CPU y memoria de contenedor de clasificador** | Cuando se prevé un alto rendimiento de transacciones de inmediato. |
| **Almacenamiento de clasificador** | Cuando se prevé que este clasificador formará parte de un servicio de ordenación en muchos canales. Recuerde que los clasificadores guardan una copia del blockchain para cada canal del que forman parte. El almacenamiento predeterminado de clasificador es de 100G, igual que para el contenedor del propio igual. |

Hacer que la CPU y la memoria de los nodos de ordenación sea casi el doble del tamaño de los iguales, aunque no es obligatorio, se considera una práctica recomendada. Si se satura un servicio de ordenación, es posible que se agoten los tiempos de espera y que se empiecen a descartar transacciones, lo que requiere que se vuelvan a enviar las transacciones. Esto provoca un daño aún mayor en una red que un solo igual que lucha por mantener el ritmo. En una configuración de servicio de ordenación Raft, un nodo líder saturado podría dejar de enviar mensajes de latido, desencadenando una elección de líder y un cese temporal de la ordenación de transacciones. De la misma manera, un nodo de seguidor podría perder mensajes e intentar desencadenar una elección de líder cuando no se necesita ninguna.
{:important}

## Reasignación de recursos
{: #ibp-console-govern-reallocate-resources}

Tras redimensionar un nodo, es posible que observe un retardo antes de que tenga efecto, ya que se están reconstruyendo los contenedores.
{:important}

Como se ha indicado anteriormente, se recomienda utilizar la herramienta [{{site.data.keyword.cloud_notm}} SysDig ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/cloud/sysdig "{{site.data.keyword.cloud_notm}} Monitoring with Sysdig") junto con el panel de control de {{site.data.keyword.cloud_notm}} Kubernetes para supervisar el uso de recursos de Kubernetes. Si determina que un nodo trabajador se está quedando sin recursos, puede añadir un nuevo nodo trabajador de mayor tamaño al clúster y suprimir luego el nodo trabajador existente.
{:note}

Aunque es más fácil tener bastantes recursos desplegados en el servicio {{site.data.keyword.cloud_notm}} Kubernetes y poder expandir los pods y nodos trabajadores a medida que haga falta sin tener que aumentar primero el despliegue en el servicio {{site.data.keyword.cloud_notm}} Kubernetes, cuanto mayor sea el despliegue en el servicio {{site.data.keyword.cloud_notm}} Kubernetes, mayor será su coste. Los usuarios deben estudiar detenidamente sus opciones y reconocer qué están sacrificando independientemente de la opción que elijan.

Puede utilizar uno de los métodos siguientes para reasignar los recursos que asigne a los contenedores asociados con el nodo.

1. **Utilizar el escalador automático del servicio {{site.data.keyword.cloud_notm}} Kubernetes**. El escalador automático aumentará o reducirá la escala de los nodos trabajadores en respuesta a los valores de especificación del pod y a las solicitudes de recursos. Para obtener más información sobre el escalador automático del servicio {{site.data.keyword.cloud_notm}} Kubernetes y cómo configurarlo, consulte [Escalado de clústeres ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-ca#ca "Escalado de clústeres") en la documentación de {{site.data.keyword.cloud_notm}}. Tenga en cuenta que al permitir que el escalador automático ajuste los recursos puede incurrir en cargos en su cuenta del servicio {{site.data.keyword.cloud_notm}} Kubernetes que variarán según el uso.
2. **Escalar de forma manual**. Esto implica la supervisión del uso en la consola y en el clúster del servicio {{site.data.keyword.cloud_notm}} Kubernetes. Aunque esto implicará más pasos manuales que utilizar el escalador automático, tiene la ventaja de permitir que el usuario siempre sepa qué se va a cargar en su cuenta del servicio {{site.data.keyword.cloud_notm}} Kubernetes.

Para realizar el escalado manual, pulse el nodo que desee ajustar en la página **Nodos** y, a continuación, pulse el separador **Uso**. Podrá ver un botón denominado **Reasignar**, que mostrará un separador **Asignación de recursos** muy similar al que ha visto al crear el nodo. Si desea reducir la cantidad de recursos disponibles, simplemente proporcione valores más bajos y pulse **Reasignar recursos** en dicho separador y en la página **Resumen** resultante.

Si desea aumentar la CPU y la memoria para un nodo, utilice el separador **Asignación de recursos** para aumentar los valores. El recuadro blanco de la parte inferior de la página añadirá los nuevos valores. Tras pulsar **Reasignar recursos**, la página **Resumen** convertirá este valor en una cantidad **VPC**, que se utilizará para calcular su factura. A continuación, deberá acceder al panel de control del servicio {{site.data.keyword.cloud_notm}} Kubernetes para asegurarse de que el clúster tiene recursos suficientes para esta reasignación. Si es así, puede pulsar **Reasignar recursos**. Si no hay suficientes recursos disponibles, deberá aumentar el tamaño del clúster utilizando el panel de control del servicio {{site.data.keyword.cloud_notm}} Kubernetes.

El método que utilizará para aumentar el almacenamiento dependerá de la clase de almacenamiento que haya elegido para el clúster. Vuelva a consultar la documentación sobre las [opciones de almacenamiento ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-kube_concepts#kube_concepts "opciones de almacenamiento") para obtener información sobre cómo aumentar el almacenamiento. Si está a punto de agotar el almacenamiento en el igual o clasificador, debe desplegar un nuevo igual o clasificador con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

A diferencia de la CPU y la memoria, que se pueden aumentar utilizando la consola (si tiene recursos disponibles en el clúster del servicio {{site.data.keyword.cloud_notm}} Kubernetes), necesitará utilizar la CLI de {{site.data.keyword.cloud_notm}} para aumentar el almacenamiento de los nodos. Para ver una guía de aprendizaje sobre cómo hacer esto, consulte [Cambiar el tamaño y el IOPS del dispositivo de almacenamiento existente ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-file_storage#file_change_storage_configuration "Cambiar el tamaño y el IOPS del dispositivo de almacenamiento existente").

## Actualización de la configuración de un canal
{: #ibp-console-govern-update-channel}

Aunque la creación de un canal y la actualización de un canal tienen el mismo objetivo, al ofrecer a los usuarios la posibilidad de asegurarse de que la configuración de su canal sea la más adecuada posible para su caso de uso, los dos procesos son en realidad muy distintos **como tareas** en la consola. Recuerde que la documentación sobre la [Creación de un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) indicaba que se trata de un proceso llevado a cabo por una **única organización**. Siempre que una organización sea miembro del consorcio del servicio de ordenación que pasará a ser el servicio de ordenación de un canal, podrá crear el canal de la manera que desee. Puede proporcionarle cualquier nombre, añadir las organizaciones que quiera (siempre que sean miembros del consorcio), asignar permisos a dichas organizaciones, establecer listas de control de acceso, etc.

Las demás organizaciones tienen la opción de participar en este canal (por ejemplo, uniendo iguales al canal), pero se presupone que el proceso colaborativo de elegir los parámetros del canal ocurrirá fuera de banda, antes de que una organización utilice la consola para crear el canal.

La actualización de un canal es diferente. Ocurre **dentro de la consola** y sigue los procedimientos de control colaborativos que constituyen la base sobre la que funciona {{site.data.keyword.blockchainfull_notm}} Platform. Este proceso colaborativo implica el envío de solicitudes de actualización de la configuración del canal a organizaciones que tienen un rol administrativo en el canal. Estas organizaciones también se conocen como **operadores** del canal.

Para actualizar un canal, pulse sobre el canal dentro del separador **Canales**. Pulse sobre el botón **Valores** situado junto al nombre del canal en la parte superior de la página. Aparecerá un panel que tiene un aspecto muy similar al panel que se utiliza para crear un canal.

### Parámetros de configuración de canal que puede actualizar
{: #ibp-console-govern-update-channel-available-parameters}

Es posible cambiar algunos, pero no todos, parámetros de configuración de un canal después de que se haya creado el canal. Y solo existe un parámetro que es posible actualizar y que no está disponible durante la creación del canal.

Verá que el **Nombre del canal** está atenuado y no se puede editar. Esto refleja el hecho de que el nombre del canal no se puede cambiar una vez que se haya creado. Además, observe que el nombre de visualización del servicio de ordenación no está presente, ya que el servicio de ordenación de un canal tampoco se puede cambiar una vez que se haya creado el canal.

No obstante, puede cambiar los parámetros de configuración de canal siguientes:

* **Organizaciones**. Esta sección del panel incluye cómo se añaden o eliminan organizaciones de un canal. Las organizaciones que se pueden añadir se pueden ver en la lista desplegable. Tenga en cuenta que una organización debe ser miembro del consorcio del servicio de ordenación para que se pueda añadir a un canal. Para obtener más información sobre cómo añadir una organización al consorcio, consulte [Añadir su organización a la lista de organizaciones que pueden realizar transacciones](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-add-org).

* **Política de actualización del canal**. La política de actualización de un canal especifica cuántas organizaciones (del número total de organizaciones del canal) deben aprobar una actualización en la configuración del canal. Para garantizar un buen equilibrio entre la administración colaborativa y el proceso eficiente de las actualizaciones de configuración del canal, plantéese establecer esta política en un voto por mayoría de administradores. Por ejemplo, si hay cinco administradores en un canal, elija `3 de 5`.

* **Parámetros de corte de bloque**. (Opción avanzada) Debido a que un cambio en los parámetros de corte de bloque predeterminados lo debe firmar un administrador de la organización del servicio de ordenación, estos campos no están presentes en el panel de creación del canal. No obstante, debido a que la configuración de este canal se enviará a todas las organizaciones relevantes del canal, es posible enviar una solicitud de actualización de configuración del canal con cambios en los parámetros de corte de bloque. Estos parámetros determinan las condiciones bajo las cuales el servicio de ordenación corta un nuevo bloque. Para obtener información sobre cómo afecta a estos campos que se corten los bloques, consulte [Parámetros de corte de bloque](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-orderer-tuning-batch-size).

* **Listas de control de acceso**. (Opción avanzada) Para especificar un control más preciso sobre los recursos, puede restringir el acceso a un recurso a una organización y un rol dentro de dicha organización. Por ejemplo, el establecimiento del acceso al recurso `ChaincodeExists` en `Application/Admins` implicaría que únicamente el administrador de la aplicación podría acceder al recurso `ChaincodeExists`.

Si restringe el acceso a un recurso a una organización concreta, tenga en cuenta que únicamente dicha organización podrá acceder al recurso. Si desea que otras organizaciones puedan acceder al recurso, deberá añadirlas una por una utilizando los campos que se indican a continuación. Como resultado, piense sus decisiones de control de acceso cuidadosamente. La restricción del acceso a determinados recursos de determinadas maneras puede tener un efecto muy negativo en el funcionamiento del canal.
{:important}

Debido a que la consola proporciona a un único usuario la posibilidad de poseer y controlar varias organizaciones, debe especificar la organización que esté utilizando al firmar una actualización de canal en la sección **Organización de actualizador de canal**. Si es propietario de más de una organización en este canal, puede elegir cualquiera de las organizaciones de las que sea propietario en el canal para realizar la firma. En función de la **política de actualización de canal** que haya seleccionado, puede recibir una notificación que le solicite firmar la solicitud como una o más de las organizaciones de las que es propietario.

Si intenta cambiar cualquiera de los **Parámetros de corte de bloque** y es propietario de la organización del servicio de ordenación de este canal, verá un campo para la organización del servicio de ordenación. Seleccione el MSP de la organización del servicio de ordenación adecuado en la lista desplegable. Si no es un administrador de la organización del servicio de ordenación, aún así podrá realizar una solicitud para cambiar uno de los parámetros de corte de bloque, pero la solicitud la enviará, y deberá firmarla, un administrador del servicio de ordenación.

### Flujo de recopilación de firmas
{: #ibp-console-govern-update-channel-signature-collection}

Para que se verifiquen las firmas, las organizaciones de un canal deben exportar los MSP (en formato JSON) que representan sus organizaciones a las demás organizaciones del canal, así como importar los MSP de las otras organizaciones. Para exportar un MSP, pulse el botón de descarga en el MSP (en la pantalla **Organizaciones**) y, a continuación, envíelo a las demás organizaciones fuera de banda. Al recibir un JSON de un MSP, impórtelo utilizando la pantalla **Organizaciones**.
{: important}

Después de que se haya realizado una solicitud de actualización de la configuración de un canal, se enviará a las organizaciones del canal que tengan el derecho de firmarla. Por ejemplo, si hay cinco operadores (administradores de canal) en un canal, se enviará a los cinco. Para que se apruebe la actualización de la configuración del canal, la deben firmar el número de operadores que aparecen en la **política de actualización del canal**. Si esta política indica `3 de 5`, entonces la actualización de la configuración del canal se enviará a los cinco operadores y, cuando tres de ellos la firmen, la actualización de la configuración del canal entrará en vigor.

Este proceso de conocer cuándo tiene una actualización que firmar, así como de firmarla, se gestiona a través del botón **Notificaciones** (con aspecto de campana) de la parte superior derecha de la consola. Cuando vea un punto azul sobre el botón **Notificaciones**, significa que tiene una solicitud pendiente que evaluar o que tiene una notificación sobre un suceso de actualización de canal.

Al pulsar sobre el botón **Notificaciones**, puede disponer de una o más acciones que puede realizar. Si se ha realizado una solicitud de actualización de la configuración de un canal, tendrá la posibilidad de pulsar sobre `Revisar y actualizar la configuración del canal` y ver los cambios en la actualización de la configuración del canal que se han propuesto o que se han llevado a cabo (si se ha aprobado la nueva configuración del canal). Si es un operador del canal y no se han recopilado firmas suficientes para aprobar la solicitud de actualización de la configuración del canal, tendrá la posibilidad de firmar la solicitud de actualización.

No tiene obligación de firmar una actualización de la configuración del canal, pero no hay manera de firmar **en contra** de la actualización de un canal. Si no aprueba una actualización de configuración del canal, simplemente cierre el panel y póngase en contacto con otros operadores del canal fuera de banda para expresar sus preocupaciones. No obstante, si un número suficiente de operadores del canal para que se satisfaga la política de actualización del canal aprueba la actualización, la nueva configuración entrará en vigor.
{:note}

## Ajuste del clasificador
{: #ibp-console-govern-orderer-tuning}

El rendimiento de una plataforma blockchain se puede ver afectado por muchas variables, como el tamaño de transacción, el tamaño de bloque, el tamaño de red y los límites del hardware. El nodo clasificador incluye un conjunto de parámetros de ajuste que se pueden utilizar de forma conjunta para controlar la producción y el rendimiento del clasificador.  Puede utilizar estos parámetros para personalizar la manera en la que el clasificador procesa las transacciones en función de si tiene muchas transacciones pequeñas frecuentes o menos transacciones más grandes que llegan con menor frecuencia. Básicamente, tiene el control para decidir cuándo se cortan los bloques según el tamaño, la cantidad y la tasa de llegada de las transacciones.

Los parámetros siguientes están disponibles en la consola pulsando sobre el nodo clasificador en el separador **Nodos** y, a continuación, pulsando sobre su icono **Valores**. Pulse el botón **Avanzado** para abrir la **Configuración de canal avanzada** para el clasificador.

### Parámetros de corte de bloque
{: #ibp-console-govern-orderer-tuning-batch-size}

Los tres parámetros siguientes se combinan para controlar cuándo se corta un bloque, según una combinación entre establecer el número máximo de transacciones en un bloque y el propio tamaño del bloque.

- **Máximo absoluto de bytes**  
  Establezca este valor en el tamaño de bloque más grande en bytes que puede cortar el clasificador.  Ninguna transacción será mayor que el valor de `Máximo absoluto de bytes`. Normalmente, este valor se puede establecer de forma segura en un valor de dos a diez veces mayor que el `Máximo preferido de bytes`.    
  **Nota**: el tamaño máximo permitido es de 99MB.
- **Recuento máximo de mensajes**   
  Establezca este valor en el número máximo de transacciones que se pueden incluir en un solo bloque.
- **Máximo preferido de bytes**  
  Establezca este valor en el tamaño ideal de bloque en bytes, pero debe ser menor que el `Máximo absoluto de bytes`. El tamaño de una transacción mínima, una que no contiene ninguna aprobación, es de alrededor de 1KB.  Si añade 1KB por aprobación necesaria, un tamaño de transacción típico es de aproximadamente 3-4KB. Por lo tanto, se recomienda establecer el valor de `Máximo preferido de bytes` en aproximadamente `Recuento máximo de mensajes * Tamaño de transmisión promedio esperado`. En tiempo de ejecución, siempre que sea posible, los bloques no sobrepasarán este tamaño. Si llega una transacción que provoca que el bloque supere este tamaño, se corta el bloque y se crea un nuevo bloque para dicha transacción. Sin embargo, si llega una transacción que supera este valor sin superar el `Máximo absoluto de bytes`, se incluirá la transacción. Si llega un bloque mayor que `Máximo preferido de bytes`, solo contendrá una única transacción, y el tamaño de dicha transacción no puede ser mayor de `Máximo absoluto de bytes`.

Estos parámetros se pueden configurar conjuntamente para optimizar el rendimiento del clasificador.

### Tiempo de espera de lote
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Establezca el valor de **Tiempo de espera** en la cantidad de tiempo, en segundos, que se debe esperar después de que llegue la primera transacción antes de cortar el bloque. Si establece un valor demasiado bajo, se arriesga a evitar que se llenen los lotes hasta el tamaño preferido. Establecer un valor demasiado alto puede provocar que el clasificador espere a los bloques y que se degrade el rendimiento global. En general, se recomienda establecer el valor de `Tiempo de espera de lote` en al menos `Recuento máximo de mensajes / Transacciones máximas por segundo`.

Al modificar estos parámetros, no se verá afectado el comportamiento de los canales existentes en el clasificador; en lugar de ello, los cambios que realice en la configuración del clasificador se aplicarán únicamente a los nuevos canales que cree en este clasificador.
{:important}

## Modificación de canales
{: #ibp-console-govern-channels}

### Configuración de iguales de ancla
{: #ibp-console-govern-channels-anchor-peers}

Debido a que el [gossip (rumores) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Protocolo gossip de diseminación de datos") entre organizaciones debe estar habilitado para que funcionen el descubrimiento de servicios y los datos privados, debe existir un igual de ancla para cada organización. Este igual de ancla no es un **tipo** especial de igual, sino simplemente el igual que la organización pone en conocimiento de las demás organizaciones y que inicia los rumores (gossip) entre organizaciones. Por lo tanto, es necesario definir al menos un [igual de ancla ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Iguales de ancla") para cada organización en la definición de la colección.

Para configurar un igual para que sea un igual de ancla, pulse el separador **Canales** y abra el canal donde se haya creado la instancia del contrato inteligente.
 - Pulse el separador **Detalles del canal**.
 - Desplácese hacia abajo a la tabla Iguales de ancla y pulse **Añadir igual de ancla**.
 - Seleccione al menos un igual de cada organización en la definición de colección que desee que sirva como igual de ancla para la organización. Por motivos de redundancia, puede plantearse seleccionar más de un igual para cada organización de la colección.

---

copyright:
  years: 2017, 2019
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

# Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform para AWS
{: #remote-peer-aws}

En estas instrucciones se describe cómo utilizar una plantilla de inicio rápido de Amazon Web Services (AWS) para crear un igual de la plataforma {{site.data.keyword.blockchainfull}} para AWS y, a continuación, conectarlo a una red en una plataforma {{site.data.keyword.blockchainfull_notm}}.
{:shortdesc}

Para obtener más información sobre AWS, consulte el [documento de visión general de AWS ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "Documento de visión general de AWS").

{{site.data.keyword.blockchainfull_notm}} Platform para AWS permite que los iguales puedan aprovechar el perfil de conexión, las entidades emisoras de certificados (CA) de Hyperledger Fabric y el servicio de ordenación de una red existente del Plan inicial o empresarial en {{site.data.keyword.cloud_notm}} para procesar transacciones a través de una plantilla de Inicio rápido de AWS. El Inicio rápido le permite desplegar iguales utilizando plantillas de AWS CloudFormation. Esta plantilla está pensada para los responsables de la toma de decisiones sobre la infraestructura de TI y los administradores del sistema que deseen configurar, desplegar y ejecutar rápidamente iguales de {{site.data.keyword.blockchainfull_notm}} Platform alojados en AWS que estén conectados a una red de Plan inicial o Plan empresarial. Puede utilizar la plantilla para crear una nueva nube privada virtual (VPC) en AWS, o desplegar el igual en una VPC existente.

El Inicio rápido completa las configuraciones siguientes:
 * Una arquitectura de alta disponibilidad que abarca dos zonas de disponibilidad.
 * Una VPC configurada con subredes públicas de acuerdo con las prácticas recomendadas por AWS. Esto le proporciona su propia red virtual en AWS.
 * Una pasarela de Internet para permitir el acceso a Internet.
 * En las subredes públicas, dos iguales en dos zonas de disponibilidad (un igual en cada subred).
 * En cada subred pública, un contenedor de igual con una base de datos LevelDB incorporada o un contenedor de CouchDB secundario.

Antes de desplegar iguales de {{site.data.keyword.blockchainfull_notm}} Platform para AWS, revise las [consideraciones de igual](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-limitations).

## Requisitos previos
{: #remote-peer-aws-prerequisites}

Para utilizar un igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS (igual remoto), debe tener una organización que sea miembro de una red blockchain que esté alojada en la plataforma IBM Blockchain. Es necesario utilizar el supervisor de red en IBM Cloud para acceder a las credenciales de red y a los puntos finales de API de la red. Si no es miembro de ninguna red blockchain, tiene que crear o unirse a una red. Para obtener más información, consulte [Creación de una red](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) o [Cómo unirse a una red](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

El tipo de instancia de VPC predeterminado para el igual es `m4.xlarge`.  Debe optimizar el tipo de instancia que elija en función de sus requisitos de CPU, memoria y almacenamiento. El igual necesita al menos:  
-	2x CPU
-	2 GB de RAM
-	4 GB de espacio para el código de encadenamiento
-	10 GB de espacio para el libro mayor con capacidad para crecer a medida que se expanda el libro mayor.

Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno de producción, es importante asignar una cantidad de almacenamiento lo suficientemente grande, por ejemplo de 100 GB. La cantidad de almacenamiento utilizada dependerá del número de transacciones y del número de firmas necesarias de la red. Si agota el almacenamiento en el igual, <!-- either expand the storage or --> despliegue un nuevo igual con un sistema de archivos mayor y deje que se sincronice a través de los demás iguales en los mismos canales.


## Opciones de despliegue
{: #remote-peer-aws-deploy-options}

El Inicio rápido proporciona dos opciones de despliegue:

* Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS dentro de un nuevo VPC (despliegue de extremo a extremo). Esta opción crea un nuevo entorno de AWS que consta del VPC, subredes, pasarelas NAT, grupos de seguridad, hosts de bastiones y otros componentes de infraestructura y, a continuación, despliega el igual en este nuevo VPC.

* Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente. Esta opción suministra el igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS en la infraestructura existente de AWS. Hay plantillas independientes para estas opciones en las que puede configurar bloques CIDR, tipos de instancias y valores de igual, tal como se explica más adelante en esta guía.

## Paso uno: preparar la cuenta de AWS
{: #remote-peer-aws-account}

1. Si aún no tiene una cuenta de AWS, cree una [aquí ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://aws.amazon.com "https//aws/amazon.com") siguiendo las instrucciones en pantalla.

2. Utilice el selector de región de la barra de navegación para elegir la región de AWS donde desea desplegar el igual en AWS.

3. Cree un par de claves en la región preferida.

4. Si es necesario, solicite un aumento del límite de servicio para el tipo de instancia Amazon EC2 <type>. Es posible que necesite hacer esto si ya tiene un despliegue existente que utilice este tipo de instancia y piensa que podría superar el límite predeterminado con este despliegue.

## Paso dos: recuperar la información de configuración del igual remoto
{: #remote-peer-aws-network-endpoints}

Debe proporcionar los puntos finales de API de la red a su igual durante la configuración. Estos puntos finales permiten que un igual encuentre y se conecte a la red en la plataforma {{site.data.keyword.blockchainfull_notm}}. En la pantalla **Visión general** de su supervisor de red, pulse el botón **Configuración de igual remoto**.

![Configuración de igual remoto](../images/myresources_starter.png "Configuración de igual remoto")
*Figura 1. Panel de configuración de igual remoto*

Se abre una ventana emergente que muestra los valores de los campos siguientes. Guarde los valores de los campos siguientes, pues serán necesarios al configurar el igual utilizando la plantilla de inicio rápido de AWS.

- **MSP de la organización**
- **Nombre de la entidad emisora de certificados (CA)**
- **URL de la entidad emisora de certificados (CA)**
- **Certificado TLS de la entidad emisora de certificados (CA)**

Puede copiar y pegar cada campo directamente en la plantilla de inicio rápido, o guardarlos en un archivo JSON pulsando el enlace **Descargar**.

La plantilla de inicio rápido espera que el certificado TLS esté formateado con saltos de línea `\r\n`. Si utiliza un navegador con el Supervisor de red en un sistema operativo `*NIX`, tiene que volver a formatear el certificado que copia desde la interfaz de usuario. Sustituya todas las apariciones de `\n` por `\r\n` y pegue la serie resultante en el campo `Certificate Authority (CA) TLS Certificate Chain`.
{:important}

**Nota:** si descarga la información en JSON, tiene que convertir el certificado TLS en formato PEM antes de proporcionarlo al igual. Convierta el **certificado TLS de la entidad emisora de certificados (CA)** del archivo JSON que ha descargado al formato PEM emitiendo el mandato siguiente:
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

Sustituya `<CERT>` por el valor del **certificado TLS de la entidad emisora de certificados (CA)**. A continuación, cuando se le solicite el **certificado TLS de la entidad emisora de certificados (CA)** en la plantilla de inicio rápido, utilice `cat` con el archivo admin.pem y copie y pegue el contenido en el campo.  

## Paso tres: registrar un igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS
{: #remote-peer-aws-register-peer}

Tiene que añadir una nueva identidad de igual a la red en {{site.data.keyword.blockchainfull_notm}} Platform para que el igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS se pueda unir a la red. Realice los pasos siguientes para registrar un igual.

**Nota:** para obtener una alta disponibilidad, la plantilla de inicio rápido crea dos nodos de igual en dos zonas de disponibilidad. Por lo tanto, se necesitan dos ID de inscripción y dos secretos. **Repita este proceso dos veces para generar dos ID de inscripción y dos secretos**

1. Inicie sesión en el supervisor de su red en {{site.data.keyword.blockchainfull_notm}} Platform. En la pantalla "Entidad emisora de certificados" del supervisor de red, puede ver todas las identidades que se han registrado con la red, como las aplicaciones de administrador o cliente. ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
  *Figura 2. Pantalla CA*

2. Pulse el botón **Añadir usuario** del panel. Se abrirá una pantalla emergente que le permite registrar el igual en la red tras rellenar los campos que se indican a continuación. **Guarde el valor del ID y el secreto para utilizarlos más adelante cuando configure el igual en la plantilla de inicio rápido.**
  - **ID de inscripción:** nombre que desea utilizar para el igual, al que se hace referencia como `enroll ID` al configurar el igual. **Guarde este valor** para utilizarlo más adelante.
  - **Secreto de inscripción:** contraseña que desea utilizar para el igual, a la que se hace referencia como `enroll Secret` al configurar el igual. **Guarde este valor** para utilizarlo más adelante.
  - **Tipo:** seleccione `peer` para este campo.
  - **Afiliación:** es la afiliación bajo su organización, por ejemplo `org1`, a la que pertenece el igual. Puede especificar una nueva afiliación o utilizar una existente.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si no se especifica, el valor predeterminado es un número ilimitado de inscripciones.

  Una vez que haya rellenado los campos, pulse **Enviar** para registrar el igual. A continuación, el igual registrado aparece en la tabla como una identidad en la red. Como medida de seguridad, utilice cada identidad, y el ID de inscripción y el secreto que la acompañan, para desplegar únicamente un igual. No reutilice los ID y contraseñas de igual.

## Paso cuatro: iniciar el Inicio rápido
{: #remote-peer-aws-launchqs}

Es responsable del coste de los servicios de AWS que utilice mientras ejecuta este despliegue de referencia de Inicio rápido. No hay ningún coste adicional para el uso de este Inicio rápido. Para ver todos los detalles, consulte las páginas de precios de cada servicio de AWS que necesite utilizar en este Inicio rápido. Los precios pueden variar.

1. Elija una de las opciones siguientes para iniciar la plantilla de AWS CloudFormation en su cuenta de AWS. Para obtener ayuda para elegir una opción, consulte las opciones de despliegue que se han descrito con anterioridad en esta guía. Cada despliegue tarda en finalizar unos 10 minutos.  

  * [Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un nuevo VPC en AWS ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fwd.aws/v43nk "Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un nuevo VPC en AWS").  

  * [Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente en AWS ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fwd.aws/zrP4g "Despliegue {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente en AWS").

  **Importante:**     
  Si va a desplegar {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente, asegúrese de que el VPC tiene dos subredes públicas en distintas zonas de disponibilidad para las instancias de la base de datos. Estas subredes requieren pasarelas NAT o instancias de NAT en sus tablas de rutas, para permitir que las instancias puedan descargar paquetes y software sin exponerlos a Internet. También necesitará tener la opción de nombre de dominio configurada en las opciones de DHCP, tal como se explica en la documentación de VPC de Amazon.  

  Además, asegúrese de crear un grupo de seguridad enlazado con su VPC existente y de añadir reglas de entrada en los puertos 22 y 7051 para este grupo de seguridad. Las conexiones TCP en el puerto 22 permiten el acceso SSH a la instancia generada, mientras que las conexiones TCP en el puerto 7051 permiten el acceso gRPC externo a la instancia del igual (necesario para trabajar con el igual utilizando la CLI de herramientas de Fabric y los SDK de Fabric). Se le solicitarán estos valores de VPC al iniciar el Inicio rápido.

2. Compruebe la región que aparece en la esquina superior derecha de la barra de navegación y cámbiela si es necesario. Es aquí donde se creará la infraestructura de red para el igual. La plantilla se lanza en la región EE. UU. este (Ohio) de forma predeterminada.

3. En la página Seleccionar plantilla, mantenga el valor predeterminado para el URL de plantilla y, a continuación, selecciona `Siguiente`.

4. En la página Especificar detalles, cambie el nombre de pila si es necesario. Revise los parámetros de la plantilla. Proporcione valores para los parámetros que requieran entrada. Para los demás parámetros, revise los valores predeterminados y personalícelos según sea necesario. Cuando haya terminado de revisar y personalizar los parámetros, seleccione `Siguiente`.

En las tablas siguientes, se muestran los parámetros por categoría y se describen de forma independiente para las dos opciones de despliegue:

  * [Parámetros para desplegar {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un nuevo VPC](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc)

  * [Parámetros para desplegar {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-existvpc).

### Parámetros para desplegar {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un nuevo VPC
{: #remote-peer-aws-parameters-newvpc}

En la tabla siguiente se muestran los parámetros configurables del diagrama de AWS y sus valores predeterminados. Todos los valores son necesarios.

|  Parámetro    | Descripción | Valor predeterminado |
| --------------|-------------|---------|
| `Stack name` |El nombre de pila es un identificador que le ayuda a encontrar una pila concreta de una lista de pilas. Un nombre de pila solo puede contener caracteres alfanuméricos (se distingue entre mayúsculas y minúsculas) y guiones. Debe empezar por el carácter alfabético y no puede tener más de 128 caracteres.| |
| | | |
| **Configuración de red** | |
| `Availability Zones` |Las dos zonas de disponibilidad a utilizar para las subredes en el VPC. Nota: se conserva el orden lógico. | |
| `Allowed SSH access CIDR` | [Bloque CIDR ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC y subredes") permitido para el acceso SSH externo a las instancias de igual de IBM Blockchain. Se puede establecer en 0.0.0.0/0 para permitir el acceso desde cualquier lugar (no se recomienda). | |
| `PeerEndpointAccessCIDR` | [Bloque CIDR ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC y subredes") permitido para el acceso gRPC externo a las instancias de igual de IBM Blockchain. Normalmente se establece en 0.0.0.0/0 para permitir el acceso desde cualquier lugar (no se recomienda). | |
| | | |
| **Configuración de Amazon EC2** | | |
| `InstanceType` | Tipo de instancia EC2 para las instancias de igual. | m4.xlarge |
| `KeyPairName` | Nombre de un par de claves EC2 existente dentro de la región de AWS. Debe generar esto. | |
| | | |
|**Configuración de IBM Blockchain** | |
| `IBMBlockchainVersion` | Versión de IBM Blockchain a desplegar. | 1.2.1 |
| `StateDatabase` | Tipo de base de datos a utilizar para almacenar el estado de blockchain. Esta selección debe coincidir con el tipo de base de datos de estado utilizado por el resto de la red. | CouchDB|
| `PeerVolumeSize` | Tamaño del volumen de EBS utilizado para almacenar datos persistentes (libro mayor, base de datos de estado, MSP) para el igual en GB. | 100 |
| `Peer 1 enroll ID`| ID de inscripción que ha especificado en el panel de entidad emisora de certificados de la interfaz de usuario de la plataforma IBM Blockchain para el primer igual. |  |
| `Peer 1 enroll secret` | Secreto de inscripción que ha especificado en el panel de entidad emisora de certificados de la interfaz de usuario de la plataforma IBM Blockchain para el primer igual. | |
| `Peer 2 enroll ID` | ID de inscripción que ha especificado en el panel de entidad emisora de certificados de la interfaz de usuario de la plataforma IBM Blockchain para el segundo igual. | |
| `Peer 2 enroll secret` | Secreto de inscripción que ha especificado en el panel de entidad emisora de certificados de la interfaz de usuario de la plataforma IBM Blockchain para el segundo igual. | |
| | | |
|**Credenciales de servicio de IBM Blockchain**| | |
| `Organization MSP` | Este valor se puede encontrar en la interfaz de usuario de la plataforma IBM Blockchain. Pulse sobre el botón Configuración de igual remoto del panel Visión general y copie y pegue la información aquí. | |
| `Certificate Authority (CA) Name` | Este valor se puede encontrar en la interfaz de usuario de la plataforma IBM Blockchain. Pulse sobre el botón Configuración de igual remoto del panel Visión general y copie y pegue la información aquí.| |
| `Certificate Authority (CA) URL` | Este valor se puede encontrar en la interfaz de usuario de la plataforma IBM Blockchain. Pulse sobre el botón Configuración de igual remoto del panel Visión general y copie y pegue la información aquí, incluyendo el puerto. Si no se especifica, el puerto predeterminado es el 443. | |
| `Certificate Authority (CA)  TLS Certificate`| Este valor se puede encontrar en la interfaz de usuario de la plataforma IBM Blockchain. Pulse sobre el botón Configuración de igual remoto del panel Visión general y copie y pegue la información aquí.| |
| | | |
|**Otros parámetros**| | |
| `QSS3BucketName` | Nombre de grupo de S3 para los activos de Inicio rápido. El nombre de grupo de Inicio rápido puede incluir números, letras en minúscula, letras en mayúscula y guiones (-). No puede empezar ni terminar por un guión (-). | `aws-quickstart` |
| `QSS3KeyPrefix` | Prefijo de clave de S3 para los activos de Inicio rápido. El prefijo de clave de Inicio rápido puede incluir números, letras en minúscula, letras en mayúscula, guiones (-) y barras inclinadas (/). | `quickstart-ibm-fabric/` |

1. En la página Opciones, puede especificar etiquetas (pares clave-valor) para los recursos de la pila y establecer opciones avanzadas. Cuando haya terminado, seleccione Siguiente.

2. En la página Revisión, revise y confirme los valores de plantilla. En Funciones, marque el recuadro de selección para reconocer que la plantilla creará recursos de IAM.

3. Seleccione Crear para desplegar la pila.

4. Supervise el estado de la pila. Cuando el estado de todas las pilas sea `CREATE_COMPLETE`, el clúster del igual estará listo. Una vez que haya finalizado correctamente, debe tener una pila raíz con cuatro pilas anidadas si se ha optado por couchDB o dos pilas anidadas si se ha optado por levelDB.

5. Utilice la información mostrada en el separador Salidas de la pila para ver los recursos que se han creado.


### Parámetros para desplegar un igual de {{site.data.keyword.blockchainfull_notm}} Platform en un VPC existente
{: #remote-peer-aws-parameters-existvpc}

Si va a desplegar el igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente, debe tener en cuenta las consideraciones siguientes:

 - Asegúrese de que el VPC tiene dos subredes privadas en distintas zonas de disponibilidad para las instancias de la base de datos. Estas subredes requieren pasarelas NAT o instancias de NAT en sus tablas de rutas, para permitir que las instancias puedan descargar paquetes y software sin exponerlos a Internet.

 - Configure la opción de nombre de dominio en las opciones de DHCP tal como se describe en la [documentación de VPC de Amazon ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "Conjuntos de opciones de DHCP").  

- Cree un grupo de seguridad enlazado con su VPC existente y de añadir reglas de entrada en los puertos 22 y 7051 para este grupo de seguridad. Las conexiones TCP en el puerto 22 permiten el acceso SSH a la instancia generada, mientras que las conexiones TCP en el puerto 7051 permiten el acceso gRPC externo a la instancia del igual (necesario para trabajar con el igual utilizando la CLI de herramientas de Fabric y los SDK de Fabric). Se le solicitarán estos valores de VPC al iniciar el Inicio rápido.

 Al desplegar un igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS en un VPC existente, los parámetros siguientes sustituyen a los parámetros de las secciones [anteriores](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc) correspondientes:

|  Parámetro    | Descripción | Valor predeterminado |
| --------------|-------------|---------|
| **Configuración de red** | | |
| `VPCID` |	ID del VPC existente para el despliegue.| |
| `AvailabilityZone1` | Zona de disponibilidad en la que desplegar el primer nodo de igual. | |
| `SubnetID1` |	ID de la subred a utilizar para el primer nodo de igual. La subred debe pertenecer al VPC elegido. Para encontrar las subredes que pertenecen al VPC, compruebe el panel de control de VPC de AWS y seleccione el menú "subredes".| |
| `AvailabilityZone2` | Zona de disponibilidad en la que desplegar el segundo nodo de igual. | |
| `SubnetID2` |	ID de la subred a utilizar para el segundo nodo de igual. La subred debe pertenecer al VPC elegido. Para encontrar las subredes que pertenecen al VPC, compruebe el panel de control de VPC de AWS y seleccione el menú "subredes". | |
| | | |
| **Configuración de Amazon EC2**| | |
| `InstanceType` 	| Tipo de instancia EC2 para las instancias de igual. 	| m4.xlarge |
| `KeyPairName` |	Nombre de un par de claves EC2 existente dentro de la región de AWS. Debe generar esto. | |
| `SecurityGroup` | ID de un grupo de seguridad de EC2 existente dentro de la región de AWS. Debe permitir conexiones de TCP entrantes en los puertos 22 y 7051. |	| |

## Paso cinco: probar el despliegue
{: #remote-peer-aws-test}

Cuando la plantilla de AWS CloudFormation haya creado con éxito la pila, se ejecutarán dos instancias de igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS en la cuenta de AWS. El nombre de las instancias estará basado en una combinación los valores de `Organization MSP` y de `Peer enroll id` que se especifican en la plantilla de Inicio rápido. Por ejemplo, `org1-remotepeer1`.  

![Igual en instancias de AWS EC2](../images/remote_peer_AWS_EC2_instances.png "Igual en instancias de AWS EC2")  
*Figura 3. Igual en instancias de AWS EC2*

Para verificar que el igual está en ejecución:

  * Utilice SSH en el VPC recién creado seleccionando la instancia en la consola de AWS (pulse **Servicios > EC2 > Instancias**) y, a continuación, pulsando el botón **Conectar**. Siga las instrucciones de AWS para emitir el mandato `ssh`.  
  * Desde la línea de mandatos, ejecute `docker ps` para ver los contenedores en ejecución. Cada máquina virtual incluye un contenedor de igual y un ecs-agent. Si ha seleccionado CouchDB como base de datos del libro mayor, también habrá un contenedor de CouchDB.

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * Puede crear una sesión de shell dentro del contenedor del igual ejecutando `docker exec -it peer sh`.

De forma opcional, si desea realizar una verificación adicional de que la conexión del igual está trabajando en la red de {{site.data.keyword.blockchainfull_notm}} Platform, puede ejecutar el mandato de CLI `peer channel fetch` desde dentro del contenedor del igual. De lo contrario, puede pasar a las instrucciones sobre [Funcionamiento del igual](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate).  

Ejecute el mandato de CLI `peer channel fetch` para recuperar el bloque de origen del canal:

1. Recupere la información de configuración del `Perfil de conexión` disponible en el panel Visión general del supervisor de red. Pulse **Perfil de conexión** y luego **Descargar**.

   - Localice el URL de clasificador buscando **orderers**, que se encuentra bajo `orderers > url`. Anote el URL que comienza por el nombre de la red. El URL se parece al del siguiente ejemplo:

   ```
   ash-zbc07b.4.secure.blockchain.ibm.com:21239
   ```

   - Localice el nombre de su organización buscando **organizations**. Debe ser la misma organización que utilice para registrar el igual. Encontrará el nombre de la organización junto con su `mspid` asociado. Este valor también está disponible en el panel Visión general del supervisor de red. Pulse el botón **Configuración de igual remoto**. El valor se muestra en
`MSP de la organización`. Anote el valor del `mspid`.

   - Si aún no lo ha hecho, cree una sesión de shell dentro del contenedor del igual ejecutando `docker exec -it peer sh`.

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - Copie el certificado TLS del clasificador del perfil de conexiones en el igual. Vaya a la sección **clasificadores**. Copie el certificado que sigue a "pem:", que comienza por -----BEGIN CERTIFICATE----- y termina por -----END CERTIFICATE-----. No incluya las comillas. Ejecute el mandato siguiente desde la línea de mandatos, sustituyendo `<orderer cert>` por el contenido que ha copiado del archivo creds.json.

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   Sustituya `<PEER_ENROLL_ID>` por el ID de inscripción que se especifica en la plantilla de Inicio rápido y que está asociado con esta instancia del igual.

2. Es necesario añadir la organización a un canal de la red para poder capturar el bloque de origen.

  - Puede iniciar un nuevo canal para el igual. Como iniciador de canal, puede incluir automáticamente su organización durante la [creación del canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).

  - Otro miembro de la red blockchain también puede añadir su organización a un canal existente mediante una [actualización de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

  - Una vez que la organización se haya añadido a un canal, deberá añadir el certificado de firma del igual al canal. El igual carga el certificado de firma durante la instalación, de modo que solo necesita sincronizar el certificado con el canal. En la pantalla "Canales" del supervisor de red, localice el canal al que se ha unido su organización y seleccione **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acción**. Esta acción sincroniza los certificados entre todos los iguales del canal.

3. Ejecute los siguientes mandatos para definir las variables de entorno en el contenedor del igual.

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   Sustituya los campos por su propia información.
     - Sustituya `<ORDERER_URL>` por el nombre de host y el puerto del clasificador que encontrará en el archivo `creds.json`.
     - Sustituya `<CHANNEL_NAME>` por el nombre del canal al que se unirá el igual.
     - Sustituya `<ORGANIZATION_MSP_ID>` por el nombre de la organización que encontrará en el archivo `creds.json`.
     - Sustituya `<PEER_ADDR>` por `localhost:7051`

   Por ejemplo:

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. Ejecute el mandato de CLI peer siguiente para capturar el bloque de origen del canal.

   **IMPORTANTE:** en el mandato siguiente, sustituya cada aparición de `<PEER_ENROLL_ID>` por el ID de inscripción asociado con esta instancia de igual que se ha especificado en la plantilla de Inicio rápido. Este valor se puede localizar ejecutando el mandato `ls /etc/hyperledger/`. Se mostrarán dos carpetas: `fabric`, y la segunda es `<PEER_ENROLL_ID>`.

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **Nota:** es posible que, al ejecutar cualquiera de estos mandatos de CLI, reciba el aviso siguiente, que puede pasar por alto sin problema.

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   Compruebe que el mandato ha funcionado bien y que el bloque de origen se ha añadido al contenedor del igual ejecutando el siguiente mandato:

   ```
   ls *.block
   ```
   {:codeblock}

   Sabrá que el bloque de origen se ha añadido correctamente cuando vea algo parecido al ejemplo siguiente:

   ```
   defaultchannel_0.block
   ```

   Enhorabuena. El igual de {{site.data.keyword.blockchainfull_notm}} Platform para AWS se ha conectado correctamente a la red de la plataforma {{site.data.keyword.blockchainfull_notm}}.

## Preguntas más frecuentes
{: #remote-peer-aws-faq}

* **P**. He recibido un error CREATE_FAILED al iniciar el Inicio rápido.
* **R**. Si AWS CloudFormation no ha podido crear la pila, se recomienda que vuelva a iniciar la plantilla con la opción Retrotraer en caso de error (Rollback on failure) establecida en `No`. (Este valor se encuentra en Avanzado en la consola de AWS CloudFormation, en la página Opciones). Con este valor, el estado de la pila se conservará y la instancia se dejará en ejecución, por lo que puede resolver el problema. (Consulte los archivos de registro en `%ProgramFiles%\Amazon\EC2ConfigService` y `C:\cfn\log`).

  - Al establecer Retrotraer en caso de error en `No`, seguirá incurriendo en gastos de AWS para esta pila. Asegúrese de suprimir la pila cuando termine de resolver el problema. Para obtener información adicional, consulte
[Resolución de problemas de AWS CloudFormation ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "Resolución de problemas de AWS CloudFormation") en el sitio web de AWS.

* **P**. He recibido un error de limitación de tamaño al desplegar las plantillas de AWS Cloudformation.
* **R**. Se recomienda que inicie las plantillas de Inicio rápido desde la ubicación que hemos proporcionado o desde otro grupo de S3. Si despliega las plantillas desde una copia local en su sistema o desde una ubicación que no sea S3, es posible que encuentre limitaciones de tamaño de plantilla al crear la pila. Para obtener más información sobre los límites de AWS CloudFormation, consulte la [documentación de AWS ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "Límites de AWS CloudFormation").

## Qué hacer a continuación
{: #remote-peer-aws-whats-next}

Tras configurar el igual en AWS, puede realizar varios pasos operativos antes de enviar transacciones y leer el libro mayor distribuido de la red blockchain. Para obtener más información, consulte [Funcionamiento de iguales en AWS](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate).

## Alta disponibilidad (HA)
{: #remote-peer-aws-high-availability}

De forma predeterminada, para el soporte de la alta disponibilidad, la plantilla de Inicio rápido despliega dos instancias del igual, en dos zonas de disponibilidad distintas.
Para aprovechar este soporte de alta disponibilidad, también debe configurar las [aplicaciones cliente para la alta disponibilidad](/docs/services/blockchain/v10_application.html#dev-app-ha-app).

## Consideraciones sobre seguridad
{: #remote-peer-aws-security}

La nube de AWS proporciona una plataforma escalable y de alta fiabilidad que ayuda a los clientes a desplegar aplicaciones y datos rápidamente y de forma segura. Al crear sistemas en la infraestructura de AWS, las responsabilidades en cuanto a seguridad se comparten entre usted y AWS. Este modelo compartido puede reducir su carga operativa, ya que AWS opera, gestiona y controla los componentes desde el sistema operativo de host y la capa de virtualización hasta la seguridad física de las instalaciones en las que funcionan los servicios. A su vez, usted asume la responsabilidad y la gestión del sistema operativo de invitado (incluyendo las actualizaciones y parches de seguridad), otras aplicaciones asociadas y la configuración del cortafuegos del grupo de seguridad proporcionado por AWS. Para obtener más información sobre la seguridad en AWS, consulte [Seguridad de la nube de AWS ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://aws.amazon.com/security/ "Seguridad de la nube de AWS").

### Identity and Access Management (IAM) de AWS
{: #remote-peer-aws-iam}

Esta solución hace uso de un rol de IAM con el mínimo acceso con privilegios. No es necesario ni se recomienda almacenar claves SSH, claves de secreto ni claves de acceso en las instancias proporcionadas.

### Seguridad del sistema operativo
{: #remote-peer-aws-ossecurity}

Solo se puede acceder al usuario root en los nodos del clúster mediante la clave SSH especificada durante el proceso de despliegue. AWS no almacena estas claves SSH, por lo que si pierde la clave SSH, perderá el acceso a estas instancias. Los parches del sistema operativo son responsabilidad del usuario y deben aplicarse de forma periódica.

### Grupos de seguridad
{: #remote-peer-aws-securitygroups}

Un grupo de seguridad actúa como un cortafuegos que controla el tráfico para una o más instancias. Al iniciar una instancia, asocie uno o más grupos de seguridad a la instancia. Añada reglas a cada grupo de seguridad que permitan el tráfico con sus instancias asociadas. Puede modificar las reglas de un grupo de seguridad en cualquier momento. Las nuevas reglas se aplicarán automáticamente a todas las instancias asociadas con el grupo de seguridad. Los grupos de seguridad creados y asignados a las instancias individuales como parte de esta solución están restringidos en la medida de lo posible, al tiempo que permiten el acceso a las diversas funciones que necesita el igual. Se recomienda revisar los grupos de seguridad para restringir aún más el acceso según sea necesario una vez que el clúster esté activo y en ejecución.

### Seguridad del igual
{: #remote-peer-aws-peer-security}

Los iguales se despliegan fuera de la plataforma {{site.data.keyword.blockchainfull_notm}}; por lo tanto, usted será responsable de gestionar la seguridad del igual. Esto incluye áreas importantes de seguridad que proporcionan las redes de Plan empresarial, como la gestión de claves y el cifrado de datos. Examine los temas siguientes cuando tenga en cuenta la seguridad para los iguales.

#### Seguridad de datos
{: #remote-peer-aws-security-data}

El Plan empresarial de {{site.data.keyword.blockchainfull_notm}} Platform utiliza el cifrado de disco completo que se basa en el [cifrado simétrico de claves ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cifrado simétrico") para proteger todos los datos que utilizan las redes. Debe seguir pasos similares en su propio entorno para proteger los datos de los iguales.

Los datos de la base de datos de estado, independientemente de si utiliza levelDB o couchDB, no se cifran. Puede utilizar un cifrado a nivel de aplicación para proteger los datos en reposo en la base de datos de estado.

#### Residencia de datos
{: #remote-peer-aws-data-residency}

La residencia de los datos impone que el proceso y el almacenamiento de todos los datos del libro mayor de blockchain permanezcan dentro de los límites de un solo país.
Para obtener más información sobre cómo conseguirlo, consulte [Residencia de los datos](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-data-residency).

#### Gestión de claves
{: #remote-peer-aws-security-key-management}

La gestión de claves es un aspecto crítico de la seguridad del igual. Si una clave privada se ve comprometida o se pierde, es posible que usuarios hostiles accedan a los datos y a las funciones del igual. El plan empresarial de {{site.data.keyword.blockchainfull_notm}} Platform utiliza [Módulos de seguridad de hardware](/docs/services/blockchain/glossary.html#glossary-hsm) (HSM) para almacenar las claves privadas de la red. HSM es un dispositivo físico que evita que otro accedan a su clave privada.

Cuando despliega un igual en AWS, usted es el responsable de gestionar las claves privadas. Aunque {{site.data.keyword.blockchainfull_notm}} Platform genera sus claves privadas, dichas claves no se almacenan en la plataforma. Resulta esencial asegurarse de que las claves se almacenan
en una ubicación segura para que no se vean comprometidas. Encontrará la clave privada de su igual en la carpeta del almacén de claves del MSP del igual, en el directorio `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` dentro del contenedor del igual. Para obtener más información sobre los certificados que hay dentro de su igual, consulte la sección [Proveedor de servicios de pertenencia](/docs/services/blockchain/certificates.html#managing-certificates-msp) del tema sobre [Gestión de certificados en {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

Puede utilizar Key Escrow para recuperar claves privadas perdidas. Esto hay que hacerlo antes de perder ninguna clave. Si una clave privada no se puede recuperar, tiene que obtener nuevas claves privadas obteniendo un nuevo signCert de la entidad emisora de certificados. También debe eliminar y sustituir el certificado de administrador de cualquier canal al que se haya unido.

<!---
In {{site.data.keyword.blockchainfull_notm}} Platform when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #remote-peer-aws-security-tls}

[La seguridad de capa de transporte ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una visión general del reconocimiento SSL o TLS") (TLS) está integrada en el modelo de confianza de Hyperledger Fabric. Todos los componentes de {{site.data.keyword.blockchainfull_notm}} Platform utilizan TLS para autenticarse y comunicarse entre sí. Por lo tanto, los componentes de red de {{site.data.keyword.blockchainfull_notm}} Platform deben ser capaces de completar un reconocimiento de TLS con sus iguales. Una implicación de este enfoque es que necesita activar el paso a través, usando por ejemplo una lista blanca, en el cortafuegos de las apps cliente a su igual.


#### Configuración del proveedor de servicios de pertenencia
{: #remote-peer-aws-security-MSP}

Los componentes de la plataforma IBM Blockchain consumen identidades a través de proveedores de servicios de pertenencia (MSP). Los MSP asocian los certificados que emiten las entidades emisoras de certificados con roles de red y de canal. Consulte este [tema](/docs/services/blockchain/certificates.html#managing-certificates-msp) para obtener más información sobre cómo trabajan los MSP con el igual.

#### Seguridad de las aplicaciones
{: #remote-peer-aws-security-appl}

Puesto que todas las invocaciones de código de encadenamiento están firmadas, Fabric gestiona la seguridad de las aplicaciones. Además, Fabric también incluye comprobaciones de nivel de aplicación basadas en ACL.

## Licencias y precios
{: #remote-peer-aws-license-pricing-aws}

Debe aceptar una versión de la licencia de Community Edition de {{site.data.keyword.blockchainfull_notm}} Platform para AWS para poder utilizar la solución de despliegue habilitada por el Inicio rápido. El uso de {{site.data.keyword.blockchainfull_notm}} Platform para AWS (incluidos todos los paquetes proporcionados a través de la oferta de Inicio rápido y los paquetes derivados de estos) no está pensado que sea para producción. IBM puede decidir desautorizar el acceso al código, y al uso de este código, en cualquier momento.
El acuerdo de licencia de software de {{site.data.keyword.blockchainfull_notm}} Platform para AWS contiene más detalles sobre los términos de la licencia. Al iniciar el Inicio rápido, se le solicitará que lea y acepte los términos del acuerdo.

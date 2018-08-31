---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Instalación, creación de instancias y actualización de un código de encadenamiento

El código de encadenamiento es un software, escrito en Go, Java o Node.js, que combina lógica empresarial e instrucciones transaccionales para crear y modificar activos en libro mayor. Se ejecuta en un contenedor docker asociado a cualquier igual que necesite interactuar con el mismo.  Para obtener más información sobre el desarrollo de código de encadenamiento, consulte [Guías de aprendizaje de código de encadenamiento ![icono de enlace externo](../images/external_link.svg "icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

El código de encadenamiento se instala en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal. **Todos los miembros del canal deben instalar el código de encadenamiento en cada igual que vaya a ejecutar este código de encadenamiento.** Para que utilicen el mismo código de encadenamiento, los miembros del canal deben asignar al código de encadenamiento el mismo nombre y versión cuando lo instalen. A continuación, el paso de creación de la instancia implica la inicialización de pares de clave y valor y el despliegue del contenedor del código de encadenamiento. Los iguales, a través de los cuales las aplicaciones interactuarán con un código de encadenamiento, deben tener instalado el código de encadenamiento en el sistema de archivos del igual y disponer de un contenedor en ejecución. **Sin embargo, si un igual desea utilizar el mismo código de encadenamiento en varios canales, solo necesita una instancia del contenedor de código de encadenamiento.**.

La **combinación de instalación y creación de instancias** constituye una potente característica, ya que permite a un igual interactuar con el mismo contenedor de código de encadenamiento entre diversos canales. El único requisito previo es que los archivos de origen del código de encadenamiento real estén instalados en el sistema de archivos del nodo igual. De este modo, si una parte del código de encadenamiento común se utiliza en docenas de canales, un nodo igual solo necesitaría un contenedor de código de encadenamiento para realizar operaciones de lectura y escritura en todos los libros mayores del canal. Este enfoque resulta muy positivo para el rendimiento del sistema de cálculo a medida que las redes crecen y el código de encadenamiento resulta cada vez más elaborado.

**Nota**: Si desarrolla el código de encadenamiento de forma iterativa y necesita actualizar un código de encadenamiento, debe repetir los pasos de instalación y de creación de instancias del código de encadenamiento.


## Instalación de código de encadenamiento
{: #installchaincode}

Debe instalar el código de encadenamiento en cada igual que vaya a ejecutar dicho código de encadenamiento. Para ello, complete los siguientes pasos:
1. En la pantalla "Instalar código" del Supervisor de red, seleccione un igual en la lista desplegable para instalar en el mismo el código de encadenamiento. Pulse el botón **Instalar código de encadenamiento**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. En el panel emergente **Instalar código de encadenamiento**, especifique el nombre y la versión. **Observe** que las series de nombre y versión se utilizarán en las aplicaciones para que interactúen con el código de encadenamiento instalado. Pulse el botón **Examinar** y navegue por el sistema de archivos hasta el lugar en el que se han guardado los archivos de origen del código de encadenamiento. Seleccione uno o varios archivos de origen de código de encadenamiento para instalarlos en el igual. A continuación, seleccione el lenguaje del código de encadenamiento desde el desplegable **Tipo de código de encadenamiento**.

Puede instalar el código de encadenamiento cargando un único o varios archivos GO o NODE, o puede cargar código de encadenamiento en un archivo .zip. Utilizar un archivo .zip mantendrá el código de encadenamiento con una estructura de directorios completa. Esto será útil si desea incluir paquetes de dependencias, o utilizar índices con CouchDB. Para encontrar un ejemplo de cómo incluir índices con el código de encadenamiento, consulte
[Uso de CouchDB desde el código de encadenamiento![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html#using-couchdb-from-chaincode){:new_window} en la documentación de Fabric. También puede encontrar información sobre la [gestión de dependencias externas para el código de encadenamiento escrito en GO![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html#managing-external-dependencies-for-chaincode-written-in-go){:new_window}

  ![Instalar código de encadenamiento](../images/chaincode_install.png "Instalar código de encadenamiento")

## Creación de instancia de código de encadenamiento
Tras la instalación de un código de encadenamiento en el sistema de archivos de cada igual que se une a un canal, debe crearse una instancia del código de encadenamiento en el canal para que los iguales puedan interactuar con el libro mayor a través del contenedor. La creación de instancia lleva a cabo la inicialización necesaria del código de encadenamiento. A menudo esto implicará la definición de pares de clave y valor que comprendan el estado inicial del código de encadenamiento.

Debe tener permiso de **Operador** o **Escritor** en el canal para crear la instancia del código de encadenamiento. Debe crearse solo una instancia a la vez de este código de encadenamiento, que tiene el mismo nombre y versión en diferentes iguales, para desplegar el contenedor. Para ello, realice los siguientes pasos:
1. En la pantalla "Instalar código" del Supervisor de red, seleccione el igual en el que instaló el código de encadenamiento y localice el código de encadenamiento del cual desea crear una instancia en la tabla de código de encadenamiento. Luego pulse el botón **Crear instancia** situado bajo la cabecera **Acción**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. En el panel emergente **Crear instancia de código de encadenamiento**, defina los pares de valores clave como argumentos para la inicialización del código de encadenamiento y seleccione el canal en el que se creará la instancia.  Pulse **Siguiente**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

3. Especifique la [política de aprobación](../glossary.html#endorsement-policy) del código de encadenamiento. Puede obtener más información sobre cómo establecer políticas de aprobación en la [sección siguiente](#specifying-chaincode-endorsement-policies).

## Especificación de las políticas de aprobación del código de encadenamiento

Puede utilizar políticas de aprobación para especificar qué conjunto de iguales se necesita para validar una transacción nueva. Por ejemplo, una política de aprobación puede especificar que una transacción se añada al libro mayor solo si una mayoría de los miembros del canal validan la transacción.

La política de aprobación se establece cuando se instancia un código de encadenamiento en un canal. La organización que instancia el código de encadenamiento puede seleccionar de entre los miembros del canal que hayan instalado el código de encadenamiento para que se conviertan en validadores, y establece la política de aprobación para todos los miembros del canal. Puede actualizar la política de aprobación siguiendo los pasos para [actualizar el código de encadenamiento](#updating-a-chaincode), y a continuación especificar una política nueva al reinstanciar el código de encadenamiento en el segundo paso.

Cuando utilice el Supervisor de red para establecer la política de aprobación, puede utilizar la IU para especificar una **Política simple**, o utilizar JSON para especificar una **Política avanzada**.

* **Utilice la IU para especificar una Política simple:** En primer lugar, pulse el botón **Añadir miembro** para seleccionar el conjunto de miembros que pueden validar las transacciones. A continuación, en la sección **Política de aprobación**, determine cuántos miembros de la lista deben validar la transacción antes de que esta se apruebe. Puede utilizar este método para especificar una política de aprobación de todos los miembros del canal, una mayoría de ellos, un único miembro o un +1 simple que impide a los miembros autofirmarse (dos de cinco miembros, por ejemplo). Si no realiza ningún cambio, la política predeterminada permite a todos los miembros del canal aprobar una transacción.

  ![Política de aprobación simple](../images/simple_endorsement.png "Política de aprobación simple")

* **Utilice JSON para especificar una Política avanzada:** Utilice las políticas avanzadas para pedir recomendaciones de miembros o administradores importantes, o para dar a las recomendaciones de ciertos miembros más peso.

  La manera más fácil de especificar una política avanzada es empezar por crear una política simple mediante la pantalla de la IU. A continuación, pulse el botón **Política avanzada**, que rellena automáticamente una versión de JSON de la política con los mismos miembros y reglas que se establecen en la política simple. A continuación, puede editar el JSON para grabar una versión más avanzada. Para obtener más información sobre cómo escribir políticas de aprobación en JSON, consulte la [documentación del SDK de nodo de Hyperledger Fabric![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest). <!--You can also find examples of advanced endorsement policies in the main [Hyperledger Fabric documentation![External link icon](../images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/arch-deep-dive.html#example-endorsement-policies)-->

  ![Política de aprobación avanzada](../images/advanced_endorsement.png "Política de aprobación avanzada")

## Actualización de un código de encadenamiento

Puede actualizar un código de encadenamiento para cambiar la programación del mismo mientras que se mantiene su relación con los activos del libro mayor. Debido a la combinación de instalación y de creación de instancias, debe actualizar el código de encadenamiento en todos los iguales que se encuentren en el canal con este código de encadenamiento. Siga estos pasos para actualizar el código de encadenamiento:

1. Instale un código de encadenamiento con el mismo nombre que su antiguo código de encadenamiento, pero con una versión distinta. Puede seguir los mismos pasos que en [Instalación de código de encadenamiento](install_instatiate_chaincode.html#Installing a chaincode). Asegúrese de seleccionar el mismo canal que su código de encadenamiento original.

  ![Actualizar código de encadenamiento](../images/upgrade_chaincode.png "Actualizar código de encadenamiento")

2. Busque el nuevo código de encadenamiento en la tabla y pulse el botón **Actualizar** bajo la cabecera **Acción**. Esta acción vuelve a instanciar el código de encadenamiento y sustituye el contenedor del código de encadenamiento por uno nuevo. Observe que no tiene que especificar ningún argumento nuevo como parte de la función de actualización.

  ![Botón de actualización](../images/upgrade_button.png "Botón de actualización")

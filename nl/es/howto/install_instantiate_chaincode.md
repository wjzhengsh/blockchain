---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"
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

  ![Instalar código de encadenamiento](../images/chaincode_install.png "Instalar código de encadenamiento")



## Creación de instancia de código de encadenamiento
Tras la instalación de un código de encadenamiento en el sistema de archivos de cada igual que se une a un canal, debe crearse una instancia del código de encadenamiento en el canal para que los iguales puedan interactuar con el libro mayor a través del contenedor. La creación de instancia lleva a cabo la inicialización necesaria del código de encadenamiento. A menudo esto implicará la definición de pares de clave y valor que comprendan el estado inicial del código de encadenamiento.

Debe tener permiso de **Operador** o **Escritor** en el canal para crear la instancia del código de encadenamiento. Debe crearse solo una instancia a la vez de este código de encadenamiento, que tiene el mismo nombre y versión en diferentes iguales, para desplegar el contenedor. Para ello, realice los siguientes pasos:
1. En la pantalla "Instalar código" del Supervisor de red, seleccione el igual en el que instaló el código de encadenamiento y localice el código de encadenamiento del cual desea crear una instancia en la tabla de código de encadenamiento. Luego pulse el botón **Crear instancia** situado bajo la cabecera **Acción**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. En el panel emergente **Crear instancia de código de encadenamiento**, defina los pares de valores clave como argumentos para la inicialización del código de encadenamiento y seleccione el canal en el que se creará la instancia.  Pulse **Enviar**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

## Actualización de un código de encadenamiento
Puede actualizar un código de encadenamiento para cambiar la programación del mismo mientras que se mantiene su relación con los activos del libro mayor. Debido a la combinación de instalación y de creación de instancias, debe actualizar el código de encadenamiento en todos los iguales que se encuentren en el canal con este código de encadenamiento. Siga estos pasos para actualizar el código de encadenamiento:

1. Instale un código de encadenamiento con el mismo nombre que su antiguo código de encadenamiento, pero con una versión distinta. Puede seguir los mismos pasos que en [Instalación de código de encadenamiento](install_instatiate_chaincode.html#Installing a chaincode). Asegúrese de seleccionar el mismo canal que su código de encadenamiento original.

  ![Actualizar código de encadenamiento](../images/upgrade_chaincode.png "Actualizar código de encadenamiento")

2. Busque el nuevo código de encadenamiento en la tabla y pulse el botón **Actualizar** bajo la cabecera **Acción**. Esta acción vuelve a instanciar el código de encadenamiento y sustituye el contenedor de código de encadenamiento por uno nuevo. Observe que no tiene que especificar ningún argumento nuevo como parte de la función de actualización.

  ![Botón de actualización](../images/upgrade_button.png "Botón de actualización")

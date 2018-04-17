---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Instalación y creación de instancia de código de encadenamiento

El código de encadenamiento es un software, escrito en Go o Java, que combina lógica empresarial e instrucciones transaccionales para crear y modificar activos en libro mayor.  Se ejecuta en un contenedor docker asociado a cualquier igual que necesite interactuar con el mismo.  Para obtener más información sobre el desarrollo de códigos de encadenamiento, consulte las [guías de aprendizaje de código de encadenamiento ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

El código de encadenamiento se instala en primer lugar en el sistema de archivos del igual y, a continuación, se crea una instancia en un canal.  **Todos los miembros del canal deben instalar el código de encadenamiento en cada igual que vaya a ejecutar este código de encadenamiento.**  Para que utilicen el mismo código de encadenamiento, los miembros del canal deben asignar al código de encadenamiento el mismo nombre y versión cuando lo instalen.  A continuación, el paso de creación de la instancia implica la inicialización de pares de clave y valor y el despliegue del contenedor del código de encadenamiento.  Los iguales, a través de los cuales las aplicaciones interactuarán con un código de encadenamiento, deben tener instalado el código de encadenamiento en el sistema de archivos del igual y disponer de un contenedor en ejecución.  **Sin embargo, si un igual desea utilizar el mismo código de encadenamiento en varios canales, solo necesita una instancia del contenedor de código de encadenamiento.**.  

La **combinación de instalación y creación de instancias** constituye una potente característica, ya que permite a un igual interactuar con el mismo contenedor de código de encadenamiento entre diversos canales.  El único requisito previo es que los archivos de origen del código de encadenamiento real estén instalados en el sistema de archivos del nodo igual.  De este modo, si una parte del código de encadenamiento común se utiliza en docenas de canales, un nodo igual solo necesitaría un contenedor de código de encadenamiento para realizar operaciones de lectura y escritura en todos los libros mayores del canal.  Este enfoque resulta muy positivo para el rendimiento del sistema de cálculo a medida que las redes crecen y los códigos de encadenamiento resultan cada vez más elaborados.  

**Nota**: si desarrolla el código de encadenamiento de forma interactiva y necesita actualizar un código de encadenamiento, debe repetir los pasos de instalación y de creación de instancias del código de encadenamiento.


## Instalación de código de encadenamiento
{: #installchaincode}

Debe instalar el código de encadenamiento en cada igual que vaya a ejecutar dicho código de encadenamiento.  Para ello, complete los siguientes pasos:
1. En la pantalla "Código de encadenamiento" del Supervisor de red, seleccione un igual en la lista desplegable para instalar en el mismo el código de encadenamiento.  Pulse el botón **Instalar código de encadenamiento**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode scren")  
-->

2. En el panel emergente **Instalar código de encadenamiento**, especifique el nombre y la versión. **Observe** que las series de nombre y versión se utilizarán en las aplicaciones para que interactúen con el código de encadenamiento instalado.  Pulse el botón **Examinar** y navegue por el sistema de archivos hasta el lugar en el que se han guardado los archivos de origen del código de encadenamiento.  Seleccione uno o varios archivos de origen de código de encadenamiento para instalarlos en el igual.  En este ejemplo, se muestra un archivo de origen de código de encadenamiento `fabcar.go` con el nombre `Chaincode1` y la versión `v001` que se va a instalar en el igual `fabric-peer-org2-17439a`.  

  ![Instalar código de encadenamiento](../images/chaincode_install.png "Instalar código de encadenamiento")



## Creación de instancia de código de encadenamiento
Tras la instalación de un código de encadenamiento en el sistema de archivos de cada igual que se une a un canal, debe crearse una instancia del código de encadenamiento en el canal para que los iguales puedan interactuar con el libro mayor a través del contenedor.  La creación de instancia lleva a cabo la inicialización necesaria del código de encadenamiento.  A menudo esto implicará la definición de pares de clave y valor que comprendan el estado inicial del código de encadenamiento.  

Debe tener permiso de **Operador** o **Escritor** en el canal para crear la instancia del código de encadenamiento.  Debe crearse solo una instancia a la vez de este código de encadenamiento, que tiene el mismo nombre y versión en diferentes iguales, para desplegar el contenedor.  Para ello, realice los siguientes pasos:
1. En la pantalla "Código de encadenamiento" del supervisor de red, seleccione el igual en el que instaló el código de encadenamiento y localice el código de encadenamiento del cual desea crear una instancia en la tabla de códigos de encadenamiento.  Luego pulse el botón **Crear instancia** situado bajo la cabecera **Acción**.  
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")  
-->

2. En el panel emergente **Crear instancia de código de encadenamiento**, defina los pares de valores clave como argumentos para la inicialización del código de encadenamiento y seleccione el canal en el que se creará la instancia.  Pulse **Enviar**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")   
-->

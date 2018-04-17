---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Supervisión de una red de {{site.data.keyword.blockchain}}

En esta guía de aprendizaje se muestra cómo visualizar la información de estado de la red de {{site.data.keyword.blockchain}} on {{site.data.keyword.Bluemix_short}}.
{:shortdesc}


## Supervisión de canales

Entre en el Supervisor de red y localice el canal que desea ver y supervisar en la pantalla "Canal".  En la pantalla del canal específico, puede ver la información de estado de los datos, los miembros así como una instancia del código de encadenamiento de este canal en tres separadores:

* **Visión general del canal**  
  El separador "Visión general del canal" muestra la información de bloque de este canal:
    * Una serie de puntos de datos, que incluyen el número total de bloques que se crean, el intervalo de tiempo desde la última transacción, el número de instancias de código de encadenamiento y el número de invocaciones al mismo.
    * Una tabla con todos los bloques de este canal.  Expanda un bloque para ver su información detallada.  

  ![Visión general del canal](../images/channel_overview_detail.png "Visión general del canal")  

* **Miembros**  
  El separador "Miembros" muestra la información de los miembros de este canal, incluidas las direcciones de correo electrónico de los operadores de la organización.   ![Miembros del canal](../images/channel_members.png "Miembros del canal")  

* **Código de encadenamiento**  
  El separador "Código de encadenamiento" presenta todos los códigos de encadenamiento de los que se han creado instancias en este canal con el ID y la versión del código de encadenamiento, además del número de iguales que lo ejecutan.   

  Expanda una fila del código de encadenamiento para obtener información detallada sobre el mismo:  
    * Puede pulsar **JSON** para ver el archivo JSON del código de encadenamiento.
    * Puede pulsar **Registros** para ver los registros del código de encadenamiento.
    * Puede pulsar **Suprimir** para eliminar el contenedor del código de encadenamiento.
    **Nota**: Cuando se suprime el contenedor del código de encadenamiento en ejecución no se suprime realmente el código de encadenamiento. Un código de encadenamiento del que se ha creado una instancia en una red de blockchain no se puede suprimir.

  ![Código de encadenamiento del canal](../images/channel_chaincode.png "Código de encadenamiento del canal")


## Supervisión de códigos de encadenamiento

Entre en el supervisor de red y abra la pantalla "Código de instalación" (pantalla "Código de encadenamiento" en la red del Plan empresarial). Si tiene códigos de encadenamiento en ejecución, elija un igual en la lista desplegable y verá todos los códigos de encadenamiento correspondientes a este igual en la tabla con los ID y las versiones del código de encadenamiento.  En esta pantalla puede realizar tareas de instalación y creación de instancias para su código de encadenamiento.  Para obtener más información, consulte [Instalación y creación de una instancia de código de encadenamiento](install_instantiate_chaincode.html).

  ![Código de encadenamiento](../images/chaincode_install_overview.png "Código de encadenamiento")


## Supervisión de aplicaciones de ejemplo

En una red del Plan inicial, puede ver aplicaciones de ejemplo y acceder a las mismas en la pantalla "Probar ejemplos" del supervisor de red.  Después de desplegar una aplicación de ejemplo, puede pulsar el botón **Iniciar** para especificar la interfaz de aplicación o el enlace **Ver en GitHub** para visitar el repositorio de código.  Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](prebuilt_samples.html).

  ![Aplicaciones de ejemplo](../images/sampleappflow0.png "Aplicaciones de ejemplo")

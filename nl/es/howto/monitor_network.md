---

copyright:
  years: 2017
lastupdated: "2017-07-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Supervisión de una red de {{site.data.keyword.blockchain}}

En esta guía de aprendizaje se muestra cómo visualizar la información de estado de la red de {{site.data.keyword.blockchain}} on {{site.data.keyword.Bluemix_short}}.
{:shortdesc}

Entre en el Supervisor de red y localice el canal que desea ver y supervisar en la pantalla "Canal". En la pantalla del canal específico, puede ver la información de estado de los datos, los miembros así como una instancia del código de encadenamiento de este canal en tres separadores:

* **Visión general del canal**  
  El separador "Visión general del canal" muestra la información de bloque de este canal:
    * Una serie de puntos de datos, incluidos el número total de bloques que se crean, el intervalo de tiempo desde la última transacción, el número de instancias de código de encadenamiento y el número de invocaciones al mismo.
    * Una tabla con todos los bloques de este canal. Expanda un bloque para ver su información detallada.  

  ![Visión general del canal](../images/channel_overview_detail.png "Visión general del canal")  

* **Miembros**  
  El separador "Miembros" muestra la información de los miembros de este canal, incluidas las direcciones de correo electrónico de los operadores de la organización.   ![Miembros del canal](../images/channel_members.png "Miembros del canal")  
  
* **Código de encadenamiento**  
  El separador "Código de encadenamiento" presenta todos los códigos de encadenamiento de los que se han creado instancias en este canal con el ID y la versión del código de encadenamiento, además del número de iguales que lo ejecutan.    
    
  Expanda una fila del código de encadenamiento para obtener información detallada sobre el mismo:  
    * Puede pulsar **JSON** para ver el archivo JSON del código de encadenamiento.
    * Puede pulsar **Registros** para ver los registros del código de encadenamiento.
    * Puede pulsar **Suprimir** para eliminar el contenedor del código de encadenamiento.
  
  ![Código de encadenamiento del canal](../images/channel_chaincode.png "Código de encadenamiento del canal") 
  

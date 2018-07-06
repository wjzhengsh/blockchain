---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Supervisión de una red blockchain

En esta guía de aprendizaje se muestra cómo visualizar la información de estado de la red de {{site.data.keyword.blockchain}} on {{site.data.keyword.cloud_short}}.
{:shortdesc}


## Supervisión de iguales, pedidos y CA
{: #monitor-nodes}

Puede emitir una solicitud **HEAD** HTTP en uno de los nodos de red para comprobar el estado del nodo. Un nodo de red puede ser un igual, un solicitante o una CA en la red blockchain. Una solicitud **HEAD** es similar a una solicitud GET y solo envía las cabeceras sin cuerpos. Puede obtener una respuesta 200 si el nodo funciona normalmente.

1. En la pantalla "Visión general" del Supervisor de red, pulse **Perfil de conexión**. A continuación, puede pulsar **JSON en bruto** para ver el perfil de conexión en el navegador web o pulse **Descargar** para guardar el perfil de conexión localmente.
2. En el perfil de conexión, busque la información de URL del nodo de red que desea comprobar. Por ejemplo, el URL del solicitante `fabric-orderer-20190b` es `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Ejemplo de URL de solicitante](../images/orderer_url.png "Ejemplo de URL de solicitante")
3. Sustituya **grpcs** por **https** en el URL. En el ejemplo anterior, el URL se convierte en `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Emita la solicitud **HEAD** en el URL con una herramienta como curl o una app Postman de Chrome.
    - Si obtiene una respuesta de estado 200, el nodo de red funcionará normalmente.
    - Si la solicitud **HEAD** falla con un error de conexión, el nodo de red podría no estar en ejecución, el URL del nodo podría ser incorrecto, o un cortafuegos bloquea el acceso al nodo.  Debe resolver este error; de lo contrario, las aplicaciones no pueden conectarse al nodo.

El ejemplo siguiente muestra una solicitud **HEAD** con una respuesta 200 en curl. Tenga en cuenta que puede ignorar el error grpc porque la solicitud **HEAD** HTTP comprueba si el nodo es accesible. Si lo es, la solicitud grpc al nodo funcionará también en la aplicación.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

El ejemplo siguiente muestra una solicitud **HEAD** con un error de conexión en curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

La figura siguiente muestra una solicitud **HEAD** con una respuesta 200 en la app Postman de Chrome.  

![Ejemplo de Postman de solicitud HEAD](../images/orderer_head_postman.png "Ejemplo de Postman de solicitud HEAD")


## Supervisión de canales
{: #monitor-channnels}

Entre en el Supervisor de red y localice el canal que desea ver y supervisar en la pantalla "Canal".  En la pantalla del canal específico, puede ver la información de estado de los datos, los miembros así como una instancia del código de encadenamiento de este canal en tres separadores:

* **Visión general del canal**  
  El separador "Visión general del canal" muestra la información de bloque de este canal:
    * Una serie de puntos de datos, que incluyen el número total de bloques que se crean, el intervalo de tiempo desde la última transacción, el número de instancias de código de encadenamiento y el número de invocaciones al mismo.
    * Una tabla con todos los bloques de este canal.  Expanda un bloque para ver su información detallada.  

  ![Visión general del canal](../images/channel_overview_detail.png "Visión general del canal")  

* **Miembros**  
  El separador "Miembros" muestra la información de los miembros de este canal, incluidas las direcciones de correo electrónico de los operadores de la organización.   ![Miembros del canal](../images/channel_members.png "Miembros del canal")  

* **Código de encadenamiento**  
  El separador "Código de encadenamiento" presenta todo el código de encadenamiento del que se ha creado instancias en este canal con el ID y la versión del código de encadenamiento, además del número de iguales que lo ejecutan.   

  Expanda una fila del código de encadenamiento para obtener información detallada sobre el mismo:  
    * Puede pulsar **JSON** para ver el archivo JSON del código de encadenamiento.
    * Puede pulsar **Registros** para ver los registros del código de encadenamiento.
    * Puede pulsar **Suprimir** para eliminar el contenedor del código de encadenamiento.
    **Nota**: Cuando se suprime el contenedor del código de encadenamiento en ejecución no se suprime realmente el código de encadenamiento. Un código de encadenamiento del que se ha creado una instancia en una red blockchain no se puede suprimir.

  ![Código de encadenamiento del canal](../images/channel_chaincode.png "Código de encadenamiento del canal")


## Supervisión del código de encadenamiento
{: #monitor-chaincode}

Especifique el Supervisor de red y abra la pantalla "Instalar código". Si tiene código de encadenamiento en ejecución, elija un igual en la lista desplegable y verá todo el código de encadenamiento correspondiente a este igual en la tabla con los ID y las versiones del código de encadenamiento.  En esta pantalla puede realizar tareas de instalación y creación de instancias para su código de encadenamiento.  Para obtener más información, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](install_instantiate_chaincode.html).

  ![Código de encadenamiento](../images/chaincode_install_overview.png "Código de encadenamiento")


## Supervisión de aplicaciones de ejemplo
{: #monitor-apps}

En una red del Plan inicial, puede ver aplicaciones de ejemplo y acceder a las mismas en la pantalla "Probar ejemplos" del supervisor de red.  Después de desplegar una aplicación de ejemplo, puede pulsar el botón **Iniciar** para especificar la interfaz de aplicación o el enlace **Ver en GitHub** para visitar el repositorio de código.  Para obtener más información, consulte [Despliegue de aplicaciones de ejemplo](prebuilt_samples.html).

  ![Aplicaciones de ejemplo](../images/sampleappflow0.png "Aplicaciones de ejemplo")

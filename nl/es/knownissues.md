---

copyright:
  years: 2017, 2018
lastupdated: "2018-10-04"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Problemas conocidos


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Los siguientes problemas ya se han notificado:
- **Todavía no se admite la configuración de una entidad emisora de certificados externa**. Como alternativa, puede generar y subir los certificados de administración a través del Supervisor de red. Para obtener más información, consulte [Generación de certificados del lado del cliente](v10_application.html#enroll-app) y la descripción del [separador "Certificados" de la pantalla "Miembros"](v10_dashboard.html#members) del supervisor de red.
- En el Supervisor de red de una red de Plan inicial, al pulsar **Ver registros** en los nodos que se listan en la pantalla "Visión general", se abrirá la interfaz Registro de Kibana de {{site.data.keyword.cloud}}. **De forma predeterminada, kibana está preconfigurado para mostrar registros de los últimos 30 días de actividad**. Si no ha habido actividad en los últimos 30 días, verá un mensaje que indica que *No se han encontrado resultados*. Para ver otros registros, puede pulsar el icono de temporizador en la esquina superior derecha bajo el nombre de usuario y establecer un rango de tiempo más amplio, por ejemplo *Año hasta la fecha*
- Los registros de la red del Plan inicial los recopila el [servicio {{site.data.keyword.cloud_notm}} Log Analysis ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/log-analysis). De forma predeterminada, los registros los recopila el Plan Lite del servicio Log Analysis. Este plan es gratuito y **solo le permite buscar los primeros 500 MB de sus registros por día**. Si los registros de la red superan los 500 MB, no podrá ver registros nuevos en Kibana. Si la red genera más de 500 MB de registros, puede actualizar a una versión de pago del servicio Log Analysis.
- Dado que el Plan inicial no es un entorno de producción, **las aplicaciones podrían no llegar inmediatamente a un recurso de red**.
  - Si esto sucede, se recomienda como primer paso aumentar los valores de tiempo de espera predeterminados en el SDK de Fabric. Para obtener más información sobre cómo establecer valores de tiempo de espera, consulte [Establecimiento de valores de tiempo de espera en los SDK de Fabric](v10_application.html#set-timeout-in-sdk).
  - También puede volver a intentar la solicitud a nivel de aplicación.
- **Los contenedores de código de encadenamiento pueden detenerse a veces** mediante un problema de red en segundo plano y pueden necesitar reconstruirse y reiniciarse una vez invocados por un usuario. Si sucede esto, su código de encadenamiento puede tardar unos minutos en responder.
- Dado a la limitación de recursos en la red del Plan inicial, es decir, 1 CPU y 4 Gi de RAM para cada igual, **puede encontrar un error `REQUEST_TIMEOUT` durante la instanciación del código de encadenamiento**. Si esto ocurre, vuelva a intentar el paso de instanciación. Si el error continúa, puede aumentar el tiempo de espera de la instanciación del código de encadenamiento. En el perfil de conexión, el tiempo de espera de la instanciación del código de encadenamiento se establece en 300 segundos.
  - Si utiliza el valor de tiempo de espera predeterminado en SDK, copie la sección **cliente** del perfil de conexión como se muestra a continuación, lo que establece el tiempo de espera en 300 segundos, y asegúrese de que el SDK lo lee. Tenga en cuenta que para el SDK de nodo, este valor de tiempo de espera del perfil de conexión afecta a todas las llamadas, como `invocar`, `consultas`, etc.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - Si sobrescribe el valor de tiempo de espera de los mandatos de instanciación del código de encadenamiento en los SDK, establézcalo de nuevo en el valor predeterminado o cámbielo a 300 segundos o más.
    - Si utiliza el SDK de nodo, puede cambiar el valor de tiempo de espera del método `sendInstantiateProposal(request, timeout)`. Para obtener más información, consulte [sendInstantiateProposal(request, timeout) ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - Si utiliza el SDK de Java, puede cambiar el valor de tiempo de espera del mandato `instantiateProposalRequest.setProposalWaitTime(DEPLOYWAITTIME)`, que se encuentra en el archivo `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java`.

Para obtener soporte y ayuda con la red de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}}, consulte [Obtención de soporte](ibmblockchain_support.html).


## Actualización del código de encadenamiento para la actualización de la red del Plan empresarial
{: #chaincode-migration}

Si la red del Plan empresarial está en el nivel Hyperledger Fabric V1.0, la plataforma de {{site.data.keyword.blockchainfull_notm}} planificará una actualización para la red para migrar a Hyperledger Fabric V1.1. Con la actualización de red, si utiliza código de encadenamiento complejo con dependencias, debe actualizar las dependencias en el código de encadenamiento. De lo contrario, puede encontrar una interrupción del servicio.

**Notas**:
- Si utiliza código de encadenamiento simple con un único archivo `.go` o `.js`, no tiene que actualizar el código de encadenamiento.
- El código de encadenamiento actualizado podría no funcionar en la red antigua; por lo tanto, debe actualizar el código de encadenamiento después de la actualización de la red planificada.
- Puede probar el código de encadenamiento instalándolo e instanciándolo en una red del Plan inicial. Todo el código de encadenamiento que funciona en el Plan inicial también funcionará en el Plan empresarial tras la actualización de la red.

Realice los pasos siguientes para actualizar el código de encadenamiento:
1. Utilice cualquier herramienta de gestión de dependencias de terceros de Golang para actualizar el código de encadenamiento. Lo más sencillo es utilizar la misma herramienta utilizada para incluir dependencias en el archivo original. Por ejemplo, si el código de encadenamiento utiliza la herramienta **govendor** que utilizan muchas muestras de Fabric, puede ejecutar el mandato siguiente en el directorio por encima de la carpeta del proveedor para actualizar las dependencias del código de encadenamiento:
    ```
    govendor update all +v
    ```
    {:codeblock}

    A continuación, puede utilizar `go build` para comprobar si el código nuevo se compila y si funciona la actualización del código de encadenamiento.

2. Después de la actualización de red, puede [actualizar el código de encadenamiento](howto/install_instantiate_chaincode.html#updating-a-chaincode) en el Supervisor de red.

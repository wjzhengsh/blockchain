---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}


# Administración de la consola
{: #ibp-console-manage-console}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Hay varias acciones que puede realizar para gestionar el comportamiento de la consola. En este tema se describen las acciones externas a las operaciones de nodo de blockchain que le ayudan en el uso cotidiano de la consola.
{:shortdesc}

## Adición y eliminación de usuarios de la consola

Esta consola de {{site.data.keyword.blockchainfull}} Platform se suministra con la dirección de correo electrónico del propietario de {{site.data.keyword.IBM_notm}} como administrador de la consola. Luego el administrador puede otorgar a otros usuarios acceso a la consola.

Pulse **Usuarios** en el panel de navegación de la izquierda para otorgar acceso a nuevos usuarios a la consola a través de su dirección de correo electrónico. Pulse **Añadir nuevos usuarios** y especifique una lista de direcciones de correo electrónico que desee autorizar junto con su tipo de usuario: `Admin` o `General`. Los usuarios con el tipo `Admin`
pueden añadir nuevos usuarios o suprimir usuarios existentes y ver los registros de la consola. Los usuarios con el tipo `General` pueden iniciar una sesión en la consola, pero no pueden añadir ni eliminar usuarios ni ver los registros.


Los usuarios que se muestran en este panel son simplemente direcciones de correo electrónico de usuarios que pueden iniciar una sesión en la consola. No tienen ninguna relación con las **Organizaciones disponibles** en el separador Organizaciones ni con las identidades almacenadas por la cartera de la consola.
{:note}

## Actualización de la dirección de correo electrónico del administrador

Si utilizan la consola varias personas u organizaciones, se recomienda crear una dirección de correo electrónico funcional para acceder a la red. El uso de un correo electrónico funcional le permite acceder a la consola si el administrador original abandona la organización o si su dirección de correo electrónico queda suspendida. Si no se puede crear un correo electrónico funcional, puede proporcionar acceso de `Admin` a varios usuarios para evitar depender de un solo individuo.

Para actualizar la dirección de correo electrónico del administrador de la consola que se ha configurado al desplegar la consola, debe haber iniciado la sesión como administrador de la consola. Vaya al separador **Usuarios** y pulse **Configurar**
en el mosaico **ID de IBM**. En el panel que se abre, especifique una nueva dirección de correo electrónico para el administrador de la consola.


## Visualización de los registros de la consola

Puede acceder fácilmente a los registros de la consola si tiene que depurar los problemas que encuentre al utilizar la consola o al trabajar con los nodos. También puede aumentar o reducir la cantidad de registros recopilados por la consola estableciendo el nivel de registro. Los registros de la consola se recopilan por separado de los [registros de los nodos](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs),
que recopila el servicio Kubernetes de {{site.data.keyword.IBM_notm}}.

Vaya al separador **Valores** en el navegador de la consola para cambiar los valores de registro. Los registros de la consola se recopilan de dos fuentes distintas:

  * **Registros de cliente:** estos registros se recopilan cuando se envían mandatos desde el navegador a la consola.
  * **Registros de servidor:** estos registros se recopilan cuando la consola envía mandatos a los nodos y desde el despliegue de la consola. Estos registros incluyen la salida de registro de Hyperledger Fabric.

Establezca la cantidad de registros recopilados utilizando la lista desplegable que hay bajo cada tipo de registro. Por ejemplo, **Error**
y **Aviso** recopilan la cantidad mínima de registros, mientras que **Depuración** y **Todos** recopilan la mayor cantidad.

Solo puede ver los registros de la consola si ha iniciado la sesión como administrador de la consola. Para ver los registros desde el separador **Valores**, sustituya la palabra `settings` en el URL del navegador por `api/v1/logs`. Por ejemplo, si el url de su consola es `localhost:3001/settings`, para ver los registros debe ir a `localhost:3001/api/v1/logs`. Los registros de cliente y de servidor se recopilan en archivos independientes. Los registros más recientes se encuentran en la parte superior de la página.


## Visualización de los registros de la consola
{: #ibp-console-manage-console-node-logs}

El servicio Kubernetes de {{site.data.keyword.IBM_notm}} recopila los registros de sus iguales, clasificadores y entidades emisoras de certificados. Siga los pasos siguientes para ver los registros de los nodos desde el clúster en el que ha desplegado la red de
{{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Abra el [panel de control de {{site.data.keyword.cloud_notm}}
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/dashboard/apps/) y vaya a la pantalla de visión general del clúster del servicio Kubernetes de {{site.data.keyword.IBM_notm}}.
2. Sobre la pantalla de visión general del clúster, pulse **Panel de control de Kubernetes**.
3. Dentro del panel de control de Kubernetes, utilice la lista desplegable de espacio de nombres del panel de navegación de la izquierda para cambiar el espacio de nombres por la instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform. El nombre de la instancia de servicio será una serie larga de letras y números. Encontrará el nombre de la instancia de servicio al principio del URL de la consola de {{site.data.keyword.blockchainfull_notm}} Platform.
4. En el panel de navegación izquierdo, pulse **Pods** para ver la lista de los pods de nodo que ha desplegado.
5. Pulse un pod. Luego pulse **Ver registros** en el menú superior para abrir los registros del nodo. Sobre los registros, puede utilizar el menú desplegable que hay después de **Registros de** para ver los registros de los distintos contenedores del pod. Por ejemplo, el igual y la base de datos de estado (CouchDB, por ejemplo) se ejecutan en distintos contenedores y generan registros diferentes.

De forma predeterminada, los registros de los nodos se recopilan localmente dentro del clúster. También puede utilizar el servicio Log Analysis de {{site.data.keyword.cloud_notm}} o un servicio de terceros para recopilar, almacenar y analizar los registros de la red. Para obtener información, consulte [Registro y supervisión del servicio Kubernetes de {{site.data.keyword.IBM_notm}}![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Registro y supervisión del servicio Kubernetes de {{site.data.keyword.IBM_notm}}").


## Supervisión del uso de recursos

Cuando se despliegan los nodos de {{site.data.keyword.cloud_notm}} Platform, se preconfiguran con valores predeterminados de CPU, memoria y almacenamiento y estos valores no se pueden modificar. Puede supervisar el uso de recursos utilizando el panel de control del servicio Kubernetes de {{site.data.keyword.IBM_notm}}. Si intenta crear un nuevo nodo en la consola de {{site.data.keyword.cloud_notm}} Platform y recibe un error que indica que tiene que ampliar el clúster de Kubernetes, puede añadir más almacenamiento al clúster de Kubernetes. Consulte esta [información ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Cambio del tamaño e IPS del dispositivo de almacenamiento existente") sobre cómo aumentar la capacidad de almacenamiento o el rendimiento del volumen existente.

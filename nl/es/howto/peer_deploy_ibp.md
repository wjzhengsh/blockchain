---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

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

# Despliegue de iguales en {{site.data.keyword.cloud_notm}} Private y conexión con el Plan inicial o Plan empresarial
{: #ibp-peer-deploy}

En las instrucciones siguientes se describe cómo desplegar un igual de {{site.data.keyword.blockchainfull}} Platform en {{site.data.keyword.cloud_notm}} Private y cómo conectar el igual a la red del Plan inicial o del Plan empresarial en {{site.data.keyword.cloud_notm}} o en {{site.data.keyword.cloud_notm}} Private local.
{:shortdesc}

Antes de desplegar un igual, revise las [Consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

La red del Plan inicial o Plan empresarial debe estar ejecutando Hyperledger Fabric v1.1 o v1.2.1. Puede encontrar la versión de Hyperledger Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) en el supervisor de red.

## Recursos necesarios
{: #ibp-peer-deploy-resources-required}

Asegúrese de que el sistema {{site.data.keyword.cloud_notm}} Private cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| Igual | 2 | 2 GB | 50 GB con posibilidad de ampliación |
| CouchDB para igual<br>(Aplicable solo si utiliza CouchDB) | 2| 2 GB | 50 GB con posibilidad de ampliación |

 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una
máquina virtual o a un núcleo de procesador físico si el servidor no está particionado
para máquinas virtuales. Debe tener en cuenta los requisitos de vCPU cuando decida el núcleo de procesador virtual (VPC) para su despliegue en {{site.data.keyword.cloud_notm}} Private. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; 250 GB para el igual como ejemplo. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el igual, debe desplegar un nuevo igual con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

## Almacenamiento
{: #ibp-peer-deploy-storage}

Debe determinar el almacenamiento que utilizará el igual. Si utiliza los valores predeterminados, el diagrama de Helm creará una nueva reclamación de volumen persistente (PVC) de 8 Gi con el nombre `my-data-pvc` para los datos del igual, y otra PVC de 8 Gi con el nombre
`statedb-pvc` para la base de datos de estado.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de {{site.data.keyword.cloud_notm}} Private; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.

Puede optar por desplegar el igual en las plataformas amd64 o s390x. Sin embargo, tenga en cuenta que el [suministro dinámico ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Suministro dinámico") solo está disponible para los nodos amd64 en {{site.data.keyword.cloud_notm}} Private. Si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, los [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volúmenes persistentes") se
deben crear y configurar con etiquetas que se puedan utilizar para adaptar el proceso de vinculación de PVC de Kubernetes.

## Requisitos previos para desplegar un igual
{: #ibp-peer-deploy-prerequisites}

1. Para poder instalar un igual en {{site.data.keyword.cloud_notm}} Private, debe [instalar {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [instalar el diagrama de Helm de {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Si utiliza Community Edition y desea ejecutar este diagrama de Helm en un clúster de {{site.data.keyword.cloud_notm}} Private sin conexión a Internet, debe crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de {{site.data.keyword.cloud_notm}} Private. Para obtener más información, consulte
[Adición de aplicaciones destacadas a clústeres sin conexión a Internet
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "Adición de aplicaciones destacadas a clústeres sin conexión a Internet"){:new_window}. Tenga en cuenta que puede encontrar el archivo de especificación manifest.yaml en ibm-blockchain-platform-dev/ibm_cloud_pak en el diagrama de Helm.

3. Debe tener una organización que sea miembro de una red de Plan inicial o de Plan empresarial en {{site.data.keyword.cloud_notm}}. El igual aprovecha los puntos finales de API, las CA de Hyperledger Fabric y el servicio de ordenación de la red de {{site.data.keyword.blockchainfull_notm}} Platform para funcionar. Si no es miembro de ninguna red blockchain, tiene que crear o unirse a una red. Para obtener más información, consulte [Creación de una red](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) o [Cómo unirse a una red](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

4. En primer lugar, debe [desplegar una CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy) en {{site.data.keyword.cloud_notm}} Private. Utilizará esta CA como una CA de TLS. Siga los [pasos de requisito previo](/docs/services/blockchain/howto/CA_operate.html#ca-operate-prerequisites) para trabajar con una CA en {{site.data.keyword.cloud_notm}} Private antes de desplegar el igual. No será necesario que continúe más allá de estos pasos.

5. Recupere el valor de la dirección IP de proxy de clúster de la CA de TLS desde la consola de {{site.data.keyword.cloud_notm}} Private. **Nota:** necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Acciones y roles de administrador de clúster") para acceder a la IP de proxy. Inicie sesión en el clúster de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla. **Importante:** guarde este valor, ya que lo utilizará cuando configure el campo `Proxy IP` del diagrama de Helm.

6. Cree un archivo de configuración de igual y almacénelo como secreto de Kubernetes en {{site.data.keyword.cloud_notm}} Private. Puede encontrar los pasos para crear este archivo en la [siguiente sección](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file).

## Creación del archivo de configuración
{: #ibp-peer-deploy-config-file}

Antes de desplegar un igual, tiene que crear un archivo JSON de configuración que contenga información importante sobre la identidad del igual y la entidad emisora de certificados en {{site.data.keyword.cloud_notm}}. A continuación, debe pasar este archivo al diagrama de Helm durante la configuración utilizando un objeto de [secreto de Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Secretos"). Este archivo permite que el igual pueda obtener los certificados que necesita de la entidad emisora de certificados en
{{site.data.keyword.cloud_notm}} para unirse a una red de Plan inicial o Plan empresarial. Este archivo contiene también un certificado de administrador que le permite utilizar el igual como usuario administrador.

Le proporcionaremos un JSON de plantilla en estas instrucciones, que puede editar y guardar en el sistema de archivos local. A continuación, le indicaremos cómo utilizar la CA para completar el archivo de configuración.

### Archivo de configuración

A continuación puede encontrar la plantilla para el archivo de configuración:
```
{
	"enrollment": {
		"component": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"admincerts": [""]
		},
		"tls": {
			"cahost": "",
			"caport": "",
			"caname": "",
			"catls": {
				"cacert": ""
			},
			"enrollid": "",
			"enrollsecret": "",
			"csr": {
				"hosts": [""]
			}
		}
	}
}
```
{:codeblock}

Copie este archivo completo en un editor de texto, donde puede editarlo y guardarlo en el sistema de archivos local como archivo JSON. Solo tendrá que rellenar las dos secciones de la parte superior del archivo: `"enrollment"` y `"tls"`.

Para completar el archivo de configuración, deberá realizar varios pasos desde el supervisor de red de la red de Plan inicial o Plan empresarial.

1. [Recupere la información de punto final de la CA de Plan inicial o Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-ibp-ca-endpoint).
2. [Registre el igual con la CA](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-peer).
3. [Registre el administrador de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) que vaya a utilizar para trabajar con el igual. No es necesario que complete este paso si ya ha registrado un administrador para desplegar otro igual.

También debe realizar varios pasos utilizando el cliente de CA de Fabric y la CA de TLS desplegada en {{site.data.keyword.cloud_notm}} Private.

1. Utilice el cliente de CA de Fabric para [generar la carpeta de MSP del administrador de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin).
2. [Recupere la información de punto final de la CA de TLS](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-ca-endpoint).
3. Utilice el cliente de CA de Fabric para [registrar el igual con la CA de TLS](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-register-peer).


### Información de CA de Plan inicial o Plan empresarial
{: #ibp-peer-deploy-ibp-ca-endpoint}

En primer lugar, es necesario proporcionar la información de conexión de la CA en
{{site.data.keyword.cloud_notm}} en el archivo de configuración. Inicie sesión en la interfaz de usuario del supervisor de red en el Plan inicial o empresarial. En la pantalla **Visión general** de su supervisor de red, pulse el botón **Configuración de igual remoto**. Esto abrirá una ventana emergente que contiene la información necesaria sobre la CA.

![Configuración de igual remoto](../images/myresources_starter.png "Configuración de igual remoto")
*Figura 1. Panel de configuración de igual remoto*

La ventana emergente contiene los campos siguientes:

  - **MSP de la organización**
  - **Nombre de la entidad emisora de certificados (CA)**
  - **URL de la entidad emisora de certificados (CA)**
  - **Certificado TLS de la entidad emisora de certificados (CA)**

En el archivo, en la sección `"components"`, especifique los valores siguientes de arriba:
- `"caname"` es el valor del **Nombre de la entidad emisora de certificados (CA)**
- `"cahost"` es el nombre de host del URL de CA. Por ejemplo, si el URL de CA es
`https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, el valor de `"cahost"` sería
`ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`
- `"caport"` es el puerto de `"cahost"`. Por ejemplo, si el URL de CA es
`https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, `"caport"` sería
`31011`.
- `"cacert"` es el valor del campo **Certificado TLS de la entidad emisora de certificados (CA)**. Antes de insertar el certificado en el archivo, debe codificarlo en formato base64 ejecutando los mandatos siguientes y sustituyendo la serie `<paste in Certificate Authority (CA) TLS Certificate>` por el valor que haya copiado del supervisor de red.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** es importante que la serie que genera el mandato anterior tenga un formato de una sola línea. Debe tener un aspecto similar a:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```

  Pero no similar a:
  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
  ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
  Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
  VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
  WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

Pegue la serie resultante en el campo `"cacert"` situado bajo `"catls"` en el archivo. Tras la actualización, el campo `"cacert"` tiene un aspecto similar al del ejemplo siguiente:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registrar el igual
{: #ibp-peer-deploy-register-peer}

Para hacer que sus iguales se unan a canales e instalar y crear una instancia del código de encadenamiento, primero necesita registrar el igual con la CA en {{site.data.keyword.cloud_notm}}. A continuación, el despliegue del igual puede generar certificados necesarios para que el igual pueda participar en una red de Plan inicial o Plan empresarial. Complete los pasos siguientes para registrar un igual con un
`enroll ID` (ID de inscripción) y `enroll secret` (Secreto de inscripción). Pegará estos dos valores en el archivo de configuración.

1. Inicie una sesión en {{site.data.keyword.blockchainfull_notm}} Platform y acceda a su supervisor de red. En la pantalla "Entidad emisora de certificados", puede ver todas las identidades que se han registrado con su organización, como las aplicaciones de administrador o cliente.  ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
  *Figura 2. Pantalla CA*

2. Pulse el botón **Añadir usuario** de la pantalla. Se abre una pantalla emergente que le permite registrar el igual después de rellenar los campos siguientes. Guarde los valores de ID y secreto; tendrá que utilizarlos cuando configure el igual.
  - **ID de inscripción:** nombre de usuario del igual, al que se hace referencia como
`enroll ID` al configurar el igual. **Guarde este valor** para el archivo de configuración.
  - **Secreto de inscripción:** contraseña del igual, a la que se hace referencia como
`enroll Secret` al configurar el igual. **Guarde este valor** para el archivo de configuración.
  - **Tipo:** seleccione `Peer` para este campo.
  - **Afiliación:** es la afiliación bajo su organización, por ejemplo `org1`, a la que pertenecerá el igual. Seleccione una afiliación existente de la lista desplegable o escriba una nueva.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si no se especifica, el valor predeterminado es un número ilimitado de inscripciones.

  Una vez que haya completado los campos, pulse **Enviar** para registrar el igual. A continuación, el igual registrado aparece en la tabla como una identidad de la organización. Como medida de seguridad, utilice cada identidad, y el ID de inscripción y el secreto que la acompañan, para desplegar únicamente un igual. No reutilice los ID y contraseñas de igual.

3. En el archivo de configuración, en la sección `"components"`, especifique los valores siguientes:
  - `"enrollid"` es el valor del `enroll ID` (ID de inscripción) del paso anterior.
  - `"enrollsecret"` es el valor del `enroll secret` (Secreto de inscripción) del paso anterior.


### Creación de un administrador
{: #ibp-peer-deploy-register-admin}

Tras registrar la identidad del igual, también deberá crear una identidad de administrador que utilizará para trabajar con el igual. En primer lugar necesita registrar esta nueva identidad con la CA y utilizarla para generar una carpeta de MSP. A continuación, añadirá el signCert de los usuarios administradores al archivo de configuración, donde se establecerá como certificado de administrador del igual durante el despliegue. Esto le permite utilizar los certificados de la identidad de administrador para operar con la red de código de encadenamiento, por ejemplo, iniciando un nuevo canal o instalando código de encadenamiento en los iguales.

Solo necesita crear una identidad de administrador para los componentes que pertenecen a su organización. Si va a desplegar varios iguales, solo es necesario que realice estos pasos una vez. Puede utilizar el signCert que haya generado para un igual para desplegar cualquier otro igual que pertenezca a su organización.

1. Inicie una sesión en {{site.data.keyword.blockchainfull_notm}} Platform y acceda a su supervisor de red. En la pantalla "Entidad emisora de certificados", puede ver todas las identidades que se han registrado con su organización, como las aplicaciones de administrador o cliente.  ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
  *Figura 2. Pantalla CA*

2. Pulse el botón **Añadir usuario** de la pantalla. Se abre una pantalla emergente que le permite registrar el igual después de rellenar los campos siguientes. Guarde los valores de ID y secreto; tendrá que utilizarlos cuando configure el igual.
  - **ID de inscripción:** nombre de usuario del administrador de igual, al que se hace referencia como
`enroll ID` al configurar el igual. **Guarde este valor** para utilizarlo al generar la carpeta de MSP de administrador.
  - **Secreto de inscripción:** contraseña del administrador de igual, a la que se hace referencia como
`enroll Secret` al configurar el igual. **Guarde este valor** para utilizarlo al generar la carpeta de MSP de administrador.
  - **Tipo:** seleccione `Peer` para este campo.
  - **Afiliación:** es la afiliación bajo su organización, por ejemplo `org1`, a la que pertenecerá el igual. Seleccione una afiliación existente de la lista desplegable o escriba una nueva.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si no se especifica, el valor predeterminado es un número ilimitado de inscripciones.

  Tras especificar estos campos, pulse **Enviar** para crear el administrador. A continuación, el administrador creado aparece en la tabla como una identidad de la organización.

### Generación de la carpeta de MSP de administrador de igual
{: #ibp-peer-deploy-enroll-admin}

Tras registrar la identidad de administrador, debe generar la carpeta de MSP de administrador de igual y el signCert. Por lo tanto, debe ejecutar un mandato de inscripción en la CA del Plan inicial o el Plan empresarial.

1. Descargue el [cliente de CA de Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) si aún no lo ha hecho.
2. Vaya al directorio donde vaya a almacenar el material criptográfico y cree la carpeta donde vaya a almacenar la carpeta de MSP del administrador de igual.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. Establezca la vía de acceso donde el cliente puede crear sus certificados como
`$FABRIC_CA_CLIENT_HOME`. Asegúrese de eliminar el material de configuración que se pueda haber creado en intentos anteriores. Si no ha ejecutado el mandato `enroll` anteriormente, la carpeta `msp` y el archivo `.yaml` no existen.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. Abra el archivo JSON de **Perfil de conexión** desde la pantalla "Visión general" del supervisor de red y busque las variables siguientes:
  - URL para la CA: `url` en `certificateAuthorities`
  - Nombre de la CA: `caName`

5. Descargue los certificados TLS de {{site.data.keyword.cloud_notm}} dependiendo del plan de servicio, la ubicación y el clúster que utilice. Puede encontrar el clúster basándose en el nombre de dominio del URL de la entidad emisora de certificados:
`us01.blockchain.ibm.com:31011` o `us02.blockchain.ibm.com:31011` por ejemplo.

  - Certificado TLS para el Plan inicial
    - EE.UU.: [us01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
    [us05.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert"); [uk03.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - Sídney: [aus01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert");
  - [Certificado TLS para el Plan empresarial ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://public-certs.us-south.ibm-blockchain-5-prod.cloud.ibm.com/3.secure.blockchain.ibm.com.rootcert)

  Guarde el contenido en un directorio donde pueda hacer referencia a él en mandatos posteriores.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. Emita el mandato siguiente para generar certificados con la identidad de administrador de igual:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  Los valores de `<enroll_id>` y `<enroll_password>` anteriores son el **ID** y el
**Secreto** del administrador de igual que se ha [registrado utilizando el supervisor de red](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin). Los valores de `<ca_name>` y `<ca_url_with_port>` son los valores de `caName` y
`url` del perfil de conexión. Excluya la parte de `http://` al principio del URL de CA.

  Una llamada real es similar al siguiente mandato de ejemplo:

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  Después de que este mandato finalice correctamente, generará una nueva carpeta de MSP en el directorio que haya especificado como
`$FABRIC_CA_CLIENT_HOME`. Este directorio contiene los certificados que utilizará para trabajar con el igual.

7. En la carpeta de MSP que se acaba de crear, abra el archivo de signCert del nuevo usuario y conviértalo al formato base64. Encontrará el signCert en la carpeta `signcerts` del directorio de MSP. Si está utilizando los pasos de ejemplo, puede utilizar los mandatos siguientes:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** es importante que la serie generada al utilizar el mandato anterior tenga un formato de una sola línea. Debe tener un aspecto similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   y no similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Este mandato imprimirá una serie similar a la del ejemplo siguiente:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Especifique esta serie en el campo `"admincerts"` situado bajo la sección "component" del archivo de configuración.

### Información de CA de TLS
{: #ibp-peer-deploy-tls-ca-endpoint}

Los campos de `"tls"` del archivo de configuración necesitan información de la CA que ha desplegado en {{site.data.keyword.cloud_notm}} Private. Utilizará esta CA como una CA de TLS independiente, lo cual hace que el igual sea más seguro. Utilice las instrucciones siguientes para generar la información pertinente:

- Los valores de `"cahost"` y `"caport"` son el URL y el puerto del
[URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Por ejemplo, si el URL de CA es `http://9.30.94.174:30167`, el valor de
`cahost` sería `9.30.94.174` y el valor de `caport` sería `30167`.
- `"caname"` es el nombre de CA de TLS de la CA que ha desplegado en {{site.data.keyword.cloud_notm}} Private. El nombre de CA de TLS es el valor que ha proporcionado en el campo `CA TLS instance name` (nombre de instancia de TLS de CA) durante la configuración de CA.
- `"cacert"` es el certificado TLS codificado en base64 de la CA. Actualice la sección siguiente con el valor de la salida del mandato cuando recupere el [certificado TLS de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) como requisito previo.

  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Tras la actualización, el campo `"cacert"` tiene un aspecto similar al del ejemplo siguiente:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registro del igual con la CA de TLS
{: #ibp-peer-deploy-tls-register-peer}

Necesita registrar el igual con la CA de TLS en {{site.data.keyword.cloud_notm}} Private utilizando el cliente de CA de Fabric.

1. Por ahora, debe tener el archivo de certificado TLS `tls.pem` en la carpeta
`$HOME/fabric-ca-client/catls`. Si no es así, copie el certificado TLS que ha
[descargado de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) en un directorio en el que pueda hacer referencia al mismo en mandatos posteriores. Asegúrese de que se encuentra en el directorio `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. Es necesario realizar la inscripción utilizando el administrador de esta CA de TLS. Cambie `$FABRIC_CA_CLIENT_HOME` al directorio en el que desee almacenar los certificados de administrador de CA de TLS.

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. Ejecute el mandato siguiente para generar certificados con el administrador de CA de TLS.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  Los valores de `<enroll_id>` y `<enroll_password>` en el mandato son el
[nombre de usuario administrador de CA y contraseña](/docs/services/blockchain/howto/CA_deploy.html#ca-deploy-admin-secret) que haya pasado al secreto de Kubernetes al desplegar la entidad emisora de certificados. Inserte el
[URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) dentro de `<ca_url_with_port>`. Excluya la parte de `http://` al principio. El `<tls_ca_name>` es el que ha especificado durante la [configuración de la CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms).

  El `<ca_tls_cert_file>` es el nombre de archivo del [certificado TLS de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) con su vía de acceso completa.

  Una llamada real será similar al ejemplo siguiente:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Después de inscribirse, tendrá los certificados necesarios para registrar el igual con la CA de TLS.

4. Emita el mandato siguiente para encontrar su afiliación y el nombre de su organización.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  El mandato puede tener un aspecto similar al del ejemplo siguiente:

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Debería ver información parecida a la del siguiente ejemplo:

  ```
  affiliation: org1
      affiliation: org1.department1
  ```
  {:codeblock}

  Tome nota del valor de **affiliation** de la organización, por ejemplo,
`org1.department1` para la organización `org1` en el ejemplo anterior. Debe utilizar este valor en el mandato siguiente.

5. Ejecute el mandato siguiente para registrar el igual.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Debe utilizar su nombre de CA de TLS en el campo `--caname`.  Cree un nombre y una contraseña para el igual y utilícelos para sustituir los valores de `name` y `secret`. **Importante:** tome nota de esta información. Debe especificar los valores de `name` y `secret` como `"enrollid"` y `"enrollsecret"` en la sección `"tls"` del archivo de configuración.

  Un mandato de ejemplo puede tener un aspecto similar al siguiente:

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Tras emitir este mandato, puede actualizar el archivo utilizando los valores anteriores:

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  Solo puede registrar una identidad una vez. Si tiene algún problema, intente un mandato con un nuevo nombre de usuario y contraseña. Como medida de seguridad, utilice cada identidad, y el ID de inscripción y el secreto que la acompañan, para desplegar únicamente un igual. No reutilice los ID y contraseñas de igual.

  Cuando el mandato finalice correctamente, debería ver información parecida a la del siguiente ejemplo:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

Puede ejecutar un mandato de árbol (tree) para verificar el trabajo que ha realizado para preparar el archivo de configuración. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│       └── signcerts
│       |   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│       │   └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Hosts de CSR (Solicitud de firma de certificado)
{: #ibp-peer-deploy-csr-hosts}

Debe proporcionar los nombres de host de CSR para desplegar un igual. Los nombres de host de CSR incluyen la dirección IP de proxy del clúster donde va a desplegar el componente, así como el `nombre de host de servicio` que será el nombre de host del diagrama de Helm.

#### Localización del valor de la dirección IP de proxy del clúster

Si desea desplegar un igual en el mismo clúster de {{site.data.keyword.cloud_notm}} Private en el que ha desplegado la CA de TLS, especifique la misma IP de proxy que haya utilizado al
[configurar la CA de TLS](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms). Si desea desplegar el componente en un clúster distinto, puede recuperar el valor de la dirección IP de proxy del clúster desde la consola de {{site.data.keyword.cloud_notm}} Private. Es necesario que tenga el rol de administrador de clúster del clúster de {{site.data.keyword.cloud_notm}} Private donde se vaya a desplegar el igual.

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster.
2. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla.
3. Inserte el `Host IP` como valor de `"hosts"` en la sección `"csr"` del archivo de configuración siguiente:

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Determinación del nombre de host de servicio
{: #ibp-peer-deploy-determine-svc-host-name}

El `nombre de host de servicio` será el `nombre de release de Helm` que especifique durante el despliegue. Si la dirección IP de proxy del clúster es 9.42.134.44" y el `nombre de release de Helm` es `org1peer1`, debe insertar lo siguiente en la sección `"csr"` del archivo:

```
"csr": {
  "hosts": [
     "9.42.134.44",
     "org1peer1"
  ]
}
```
{:codeblock}

### Cómo completar el archivo de configuración
{: #ibp-peer-deploy-complete-config}

Una vez que haya completado todos los pasos, el archivo de configuración actualizado tendrá un aspecto similar al del ejemplo siguiente:

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer1",
			"enrollsecret": "peer1pw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		},
		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

Después de haber terminado de rellenar este archivo, deberá guardarlo en formato JSON y pasarlo al despliegue del igual como secreto de Kubernetes. Cree el secreto utilizando los pasos de la [sección siguiente](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp).

## Creación del secreto de configuración
{: #ibp-peer-deploy-config-file-ibp}

Un [secreto de Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Secretos") le permite proteger y compartir información sin tener que pasarla directamente al despliegue. Una vez que haya guardado el archivo de configuración, debe codificarlo en `base64`.

1. Para codificar el archivo de configuración en el formato base64, ejecute los mandatos siguientes desde una ventana de terminal:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** es importante que la serie generada al utilizar el mandato anterior tenga un formato de una sola línea. Debe tener un aspecto similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   y no similar a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Guarde la salida resultante para el paso cuatro.

2. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Configuración** y, a continuación, pulse **Secretos**. Pulse el botón **Crear secreto** para abrir un panel emergente que le permite generar un nuevo objeto de secreto.

3. En el separador **General**, complete los campos siguientes:
  - **Nombre:** proporcione un nombre exclusivo dentro del clúster para el secreto. Utilizará este nombre cuando despliegue el igual. El nombre debe estar en minúsculas.  
  **Nota:** al desplegar un igual, el despliegue genera automáticamente un nuevo secreto con el nombre `<helm release name you intend to use>-secret`. Por lo tanto, cuando establezca el nombre de este secreto, asegúrese de que el nombre del secreto sea distinto a `<helm release name you intend to use>-secret`. De lo contrario, el despliegue del diagrama de Helm fallará debido a que el secreto que intenta crear ya existe.
  - **Espacio de nombres:** el espacio de nombres para añadir el secreto. Seleccione el `espacio de nombres` en el que desee desplegar el igual.
  - **Tipo:** especifique el valor `Opaque`.

4. Pulse el separador **Datos** del panel de navegación de la izquierda de esta ventana. En el campo de `Nombre`, especifique `secret.json` y, en el campo de valor, pegue la serie codificada en
`base64` del archivo de configuración.

5. (Opcional) Si tiene pensado utilizar `CouchDB` como base de datos de estado, debe añadir dos valores adicionales a este objeto de secreto. En el separador **Datos**, pulse el botón **Añadir datos** para añadir el ID de usuario y la contraseña de CouchDB que se utilizará para la base de datos cuando se despliegue el contenedor.
  1. Cree el nombre de usuario y la contraseña y codifique los valores en el formato base64. Ejecute los mandatos siguientes en una ventana de terminal y sustituya `admin` y `adminpw` por los valores que desee utilizar.
    ```
    echo -n 'couch' | base64 $FLAG
    echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. En el campo **Nombre**, especifique el valor `couchdbuser`. En el campo **Valor** correspondiente, especifique el resultado de `echo -n 'couch' | base64 $FLAG` del paso uno anterior.
   3. Pulse el botón **Añadir datos** para añadir un segundo par de clave-valor.
   4. En el segundo campo de **Nombre**, especifique el valor `couchdbpwd`. En el campo **Valor** correspondiente, especifique el resultado de `echo -n 'couchpw' | base64 $FLAG` del paso uno anterior.

6. Pulse **Crear** para guardar su objeto de secreto.

**Nota:** el secreto de configuración del igual no se eliminará del clúster de {{site.data.keyword.cloud_notm}} Private cuando suprima el release de Helm. Si suprime el igual, deberá suprimir este secreto también.

## Configuración
{: #ibp-peer-deploy-peer-configuration}

Después de crear el objeto de secreto de configuración del igual, puede configurar el igual en {{site.data.keyword.cloud_notm}} Private con los pasos siguientes. Únicamente puede instalar un igual al mismo tiempo.

1. Inicie una sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico etiquetado como
`ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` si ha descargado Community Edition. Pulse sobre el mosaico para abrirlo y podrá ver un archivo Readme que incluye información sobre la instalación y configuración del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Especifique los valores para los [parámetros de configuración generales](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-configuration-parms) y acepte el acuerdo de licencia.
5. Abra el triángulo `Todos los parámetros` y especifique el valor de los [parámetros de configuración global](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-global-parameters).
6. Desplácese hacia abajo hasta la sección **Configuración de igual**. Marque el recuadro de selección `Instalar igual` y rellene los [valores de configuración](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-parameters) del igual.
7. Pulse **Instalar**.

### Parámetros de configuración
{: #ibp-peer-deploy-peer-configuration-parms}

En la tabla siguiente se muestran los parámetros configurables de la plataforma {{site.data.keyword.blockchainfull_notm}}, **específicos del componente de igual**, y sus valores predeterminados. Si está experimentando con el igual o es la primera vez que lo utiliza, use los valores predeterminados de los parámetros de base de datos y de almacenamiento. Desplácese hasta la sección del componente de igual para marcar el recuadro de selección `Instalar igual` y proporcionar la información de configuración asociada para dicho componente únicamente. **Aunque la interfaz de usuario del diagrama de Helm indica que no se necesita ninguna configuración adicional, debe especificar determinados parámetros para desplegar un igual.**

#### Parámetros de configuración generales y globales
{: #ibp-peer-deploy-global-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
|**Parámetros generales**| Parámetros que configuran el diagrama de Helm | | |
| `Nombre de release de Helm`| Nombre del release de Helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez que intente instalar un componente. **Importante:** Este valor debe coincidir con el valor que ha utilizado para generar el 'nombre de host de servicio' para el campo "hosts" del [archivo de secreto JSON.](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-csr-hosts) | ninguno | sí  |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
| `Políticas de espacio de nombres de destino`| Muestra las políticas de seguridad de pod del espacio de nombres elegido, que deben incluir una política **`ibm-privileged-psp`**. De lo contrario,
[enlace una PodSecurityPolicy](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp) con el espacio de nombres. | ninguno | no |
|**Configuración global**| Parámetros que se aplican a todos los componentes del diagrama de Helm|||
| `Nombre de cuenta de servicio`| Especifique el nombre de la [cuenta de servicio ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configurar cuentas de servicio para pods") que utilizará para ejecutar el pod. | predeterminado | no |

#### Parámetros de configuración de igual
{: #ibp-peer-deploy-peer-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
|**Configuración del clúster** |**Información de configuración del clúster** | ||
| `Instalar igual` | Selecciónelo para instalar un igual|sin marcar | sí, si desea instalar un igual |
| `Arquitectura del nodo trabajador de igual`| Seleccione la arquitectura de la plataforma de nube (AMD64 o S390x)| AMD64 | sí |
| `Repositorio de imágenes de igual`| Ubicación del diagrama de Helm de igual. Este campo se rellena automáticamente con la vía de acceso instalada. Si utiliza Community Edition y no tiene acceso a Internet, debe coincidir con el directorio donde ha descargado la imagen de igual de Fabric. | ibmcom/ibp-fabric-peer | sí |
| `Etiqueta de imagen de Docker de igual`|Valor de la etiqueta asociada con la imagen de igual |1.4.0, se rellena automáticamente con el valor correcto.|sí|
| `Configuración de igual`|Puede personalizar la configuración del igual pegando su propio archivo de configuración
`core.yaml` en este campo. Para ver un archivo `core.yaml` de ejemplo, consulte la
[configuración de ejemplo de `core.yaml`
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/core.yaml "hyperledger/fabric/core.yaml") Solo para usuarios avanzados. |ninguno|no|
| `Secreto de configuración de igual (obligatorio)`| Nombre del [Secreto de configuración de igual](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp) que ha creado en {{site.data.keyword.cloud_notm}} Private. | ninguno | sí |
|`MSP de organización (obligatorio)`|Este valor se puede encontrar en el Supervisor de red (interfaz de usuario del Plan inicial y del Plan empresarial) pulsando "Configuración de igual remoto" en la pantalla Visión general.  |ninguno|sí|
|`Tipo de servicio de igual`| Se utiliza para especificar si los [puertos externos deben estar expuestos ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types "Servicios de publicación - tipos de servicios") en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release. | NodePort |sí|
| `Base de datos de estado`| La [base de datos de estado](/docs/services/blockchain/glossary.html#glossary-state-database) utilizada para almacenar el libro mayor del canal. El igual tiene que utilizar la misma base de datos que la [red blockchain](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences). | LevelDB | sí |
|`Repositorio de imágenes de CouchDB`| Solo se aplica si se ha seleccionado CouchDB como base de datos de libro mayor. Este campo se rellena automáticamente con la vía de acceso instalada. Si utiliza Community Edition y no tiene acceso a Internet, debe coincidir con el directorio donde ha descargado la imagen de CouchDB de Fabric.| ibmcom/ibp-fabric-couchdb | sí |
| `Etiqueta de imagen de Docker de CouchDB`| Solo se aplica si se ha seleccionado CouchDB como base de datos de libro mayor. Valor de la etiqueta asociada con la imagen de CouchDB. | Se rellena automáticamente con el valor correcto.| sí |
| `Persistencia de datos de igual habilitada`| Habilita la posibilidad de guardar permanentemente los datos después de que el clúster se reinicie o falle. Consulte [almacenamiento en Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/ "Volúmenes") para obtener más información.  *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.* | marcada | no |
| `Utilizar suministro dinámico de igual`| Marcar para habilitar el suministro dinámico para volúmenes de almacenamiento. | marcada | no |
| `Reclamación de volumen persistente de igual`| Solo para una reclamación nueva. Especifique un nombre para la nueva reclamación de volumen persistente (PVC) que se va a crear. | my-data-pvc | no |
| `Nombre de clase de almacenamiento de igual`| Especifique un nombre de clase de almacenamiento para el igual. | En blanco si se crea una nueva PVC; de lo contrario, especifique la clase de almacenamiento que está asociada con la PVC existente. | no |
| `Reclamación de volumen existente de igual`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de igual`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC.| ninguno | no |
| `Valor de selector de igual`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Modalidad de acceso de almacenamiento de igual`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC.  | ReadWriteMany| no |
| `Tamaño de reclamación de volumen de igual`| Tamaño de la reclamación de volumen, que debe ser mayor que 2 Gi. | 8 Gi  | sí |
| `Reclamación de volumen persistente de base de datos de estado`| Solo para una reclamación nueva. Especifique un nombre para la nueva reclamación de volumen persistente (PVC) que se va a crear. | statedb-pvc | no |
| `Nombre de clase de almacenamiento de base de datos de estado`| Especifique un nombre de clase de almacenamiento para la base de datos de estado. | ninguno | no |
| `Reclamación de volumen existente de base de datos de estado`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector de base de datos de estado`| [Etiqueta de selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC| ninguno | no |
| `Valor de selector de base de datos de estado`| [Valor de selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para la PVC | ninguno | no |
| `Modalidad de acceso al almacenamiento de base de datos de estado`| Especifique la [modalidad de acceso ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC. | ReadWriteMany| no |
| `Tamaño de reclamación de volumen de base de datos de estado`| Elija el tamaño de disco a utilizar. | 8 Gi | sí |
| `CouchDB - Persistencia de datos habilitada`| Para el contenedor de CouchDB, los datos del libro mayor estarán disponibles cuando se reinicie el contenedor. *Si no está marcada esta opción, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod.*| marcada | no |
| `CouchDB - Utilizar suministro dinámico`| Para el contenedor de CouchDB, utilizar el almacenamiento dinámico de Kubernetes.| marcada | no |
| `Solicitud de CPU de Docker-in-Docker`| Especifique el número mínimo de CPU a asignar al contenedor donde se ejecuta el código de encadenamiento. | 1 | sí |
| `Límite de CPU de Docker-in-Docker`| Especifique el número máximo de CPU a asignar al contenedor donde se ejecuta el código de encadenamiento. | 2 | sí |
| `Solicitud de memoria de Docker-in-Docker`| Especifique la cantidad mínima de memoria a asignar al contenedor donde se ejecuta el código de encadenamiento. | 1Gi | sí |
| `Límite de memoria de Docker-in-Docker`| Especifique la cantidad máxima de memoria a asignar al contenedor donde se ejecuta el código de encadenamiento. | 4 Gi | sí |
| `Solicitud de CPU de proxy web gRPC`| Especifique el número mínimo de CPU en milicpus (m) a asignar al proxy web gRPC. | 100m | sí |
| `Límite de CPU de proxy web gRPC`| Especifique el número máximo de CPU en milicpus (m) a asignar al proxy web gRPC. | 200m | sí |
| `Solicitud de memoria de proxy web gRPC`| Especifique la cantidad mínima de memoria a asignar al proxy web gRPC. | 100Mi | sí |
| `Límite de memoria de proxy web gRPC`| Especifique la cantidad máxima de memoria a asignar al proxy web gRPC. | 200Mi | sí |
| `Solicitud de CPU del igual` | Número mínimo de CPU que se asignarán al igual. | 1 | sí |
| `Límite de CPU del igual` | Número máximo de CPU que se asignarán al igual.| 2 | sí |
| `Solicitud de memoria del igual` | Cantidad mínima de memoria que se asignará al igual. | 1Gi | sí |
| `Límite de memoria del igual` | Cantidad máxima de memoria que se asignará al igual. | 4 Gi | sí |
| `Solicitud de CPU de CouchDB` | Número mínimo de CPU que se asignarán a CouchDB.| 1 | sí |
| `Límite de CPU de CouchDB` | Número máximo de CPU que se asignarán a CouchDB. | 2 | sí |
| `Solicitud de memoria de CouchDB` | Cantidad mínima de memoria que se asignará a CouchDB.| 1Gi | sí |
| `Límite de memoria de CouchDB` | Cantidad máxima de memoria que se asignará a CouchDB. | 4 Gi | sí |


Cuando se selecciona CouchDB como base de datos de igual, se crean dos contenedores en el pod, uno para el igual y otro para CouchDB. El contenedor del igual incluye un montaje de un solo volumen en el PVC del igual que almacena los bloques y las transacciones en el sistema de archivos. El contenedor de CouchDB monta la PVC de la base de datos de estado del igual que contiene los datos del libro mayor.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Utilización de la línea de mandatos de Helm para instalar el release de Helm
{: #ibp-peer-deploy-helm-cli}

Como alternativa, puede utilizar la CLI de Helm para instalar el release de Helm. Antes de ejecutar el mandato `helm install`, asegúrese de
[añadir el repositorio de Helm del clúster al entorno de CLI de Helm
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Adición del repositorio interno de Helm a la CLI de Helm").

Puede establecer los parámetros necesarios para la instalación creando un archivo
`yaml` y pasándolo al mandato `helm install` siguiente.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

donde:
- `<helm_release name>` representa el nombre que desea para el release de Helm.
- `<helm_chart>` representa el nombre del diagrama de Helm importado en el catálogo.
- `<helm_chart_version>` representa la versión del diagrama de Helm importado en el catálogo.
- `<customvalues.yaml>` es el nombre del archivo yaml que contiene los parámetros de configuración.

Por ejemplo:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
Puede crear un nuevo archivo `yaml` editando el archivo `values.yaml` incluido en el archivo de archivado descargado, que incluye todos los parámetros necesarios con sus valores predeterminados.

## Verificar la instalación del igual
{: #ibp-peer-deploy-verify-installation-ibp}

Tras completar los parámetros de configuración y pulsar el botón **Instalar**, pulse el botón **Ver release de Helm** para ver el despliegue. Si se ha realizado correctamente, debe aparecer el valor 1 en los campos
`DESIRED`, `CURRENT`, `UP TO DATE` y `AVAILABLE` de la tabla Despliegue. Es posible que tenga que pulsar Renovar y esperar a que se actualice la tabla. También puede encontrar la tabla Despliegue pulsando el icono
**Menú** de la esquina superior izquierda de la consola de {{site.data.keyword.cloud_notm}} Private. En la lista de menús, pulse
**Cargas de trabajo** y, a continuación, **Releases de Helm**.

Si se desplaza hacia abajo hasta la sección `Notas`, hay información importante que utilizará al
[trabajar con el igual](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).

## Visualización de los registros de igual
{: #ibp-peer-deploy-view-logs}

Los registros de los componentes se pueden consultar desde la línea de mandatos mediante [`mandatos de CLI kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que está incluido en su clúster de {{site.data.keyword.cloud_notm}} Private. Para obtener más información, consulte estas
[instrucciones para acceder a los registros](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-view-logs).

## Qué hacer a continuación
{: #ibp-peer-deploy-whats-next}

Después de haber desplegado el igual, deberá realizar varios pasos operativos para poder enviar transacciones y leer el libro mayor distribuido de la red blockchain. Para obtener más información, consulte [Funcionamiento de iguales con el Plan inicial o el Plan empresarial](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).


## Resolución de problemas
{: #ibp-peer-deploy-troubleshooting}

### **Problema:** error con el URL de CA al ejecutar el mandato `enroll`
{: #ibp-peer-deploy-ca-enroll-error}

Es posible que el mandato de inscripción del cliente de CA de Fabric falle si el URL de inscripción, el valor del parámetro `-u`, contiene un carácter especial. Por ejemplo, el mandato siguiente con el ID de inscripción y la contraseña `admin:C25A06287!0`,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

fallará y generará el error siguiente:

```
!pw@9.12.19.115: event not found
```

### **Solución:**
Debe codificar el carácter especial o especificar el URL entre las comillas simples. Por ejemplo, `!` se convierte en `%21`, o el mandato se parece al siguiente:

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

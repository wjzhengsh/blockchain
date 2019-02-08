---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Despliegue de iguales en {{site.data.keyword.cloud_notm}} privado y conexión con el Plan inicial o Plan empresarial
{: #peer-ibp}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

En las instrucciones siguientes se describe cómo desplegar un igual de la plataforma
{{site.data.keyword.blockchainfull}} en {{site.data.keyword.cloud_notm}} privado (ICP) que se conectará a una red de Plan inicial o Plan empresarial en {{site.data.keyword.cloud_notm}} o su ICP local.
{:shortdesc}

Antes de desplegar un igual, revise las [Consideraciones y limitaciones](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations).

La red del Plan inicial o Plan empresarial debe estar ejecutando Hyperledger Fabric v1.1 o v1.2.1. Puede encontrar la versión de Hyperledger Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#network-preferences) en el supervisor de red.

## Recursos necesarios
{: #peer-resources-required}

Asegúrese de que el sistema ICP cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| Igual | 2 | 2 GB | 50 GB con posibilidad de ampliación |
| CouchDB para igual | 2| 2 GB |50 GB con posibilidad de ampliación |

 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una
máquina virtual o a un núcleo de procesador físico si el servidor no está particionado
para máquinas virtuales. Necesita tener en cuenta los requisitos de vCPU al decidir el núcleo de procesador virtual (VPC) para su despliegue en ICP. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; 250 GB para el igual como ejemplo. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el igual, debe desplegar un nuevo igual con un sistema de archivos mayor y dejar que se sincronice a través de los demás componentes en los mismos canales.

## Almacenamiento
{: #storage}

Debe determinar el almacenamiento que utilizará el igual. Si utiliza los valores predeterminados, el diagrama de Helm creará una nueva reclamación de volumen persistente (PVC) de 8 Gi con el nombre `my-data-pvc` para los datos del igual, y otra PVC de 8 Gi con el nombre
`statedb-pvc` para la base de datos de estado.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de ICP; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.

Puede optar por desplegar el igual en las plataformas amd64 o s390x. No obstante, debe tener en cuenta que el
[suministro dinámico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) solo está disponible para los nodos amd64 en ICP. Si el clúster incluye una combinación de nodos trabajadores s390x y amd64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, deberán crearse [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y configurarse con etiquetas que se puedan utilizar para refinar el proceso de enlace de PVC de Kubernetes.

## Requisitos previos para desplegar un igual
{: #prerequisites-peer-ibp}

1. Para poder instalar un igual en ICP, primero debe [instalar ICP](/docs/services/blockchain/ICP_setup.html) e
[instalar el diagrama de Helm de la plataforma {{site.data.keyword.blockchainfull_notm}}](/docs/services/blockchain/howto/helm_install_icp.html).

2. Si utiliza Community Edition y desea ejecutar este diagrama de Helm en un clúster de ICP sin conexión a Internet, debe crear archivados en una máquina conectada a Internet para poder instalar los archivados en el clúster de ICP. Para obtener más información, consulte
[Adición de aplicaciones destacadas a clústeres sin conexión a Internet
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Adición de aplicaciones destacadas a clústeres sin conexión a Internet"){:new_window}. Tenga en cuenta que puede encontrar el archivo de especificación manifest.yaml en ibm-blockchain-platform-dev/ibm_cloud_pak en el diagrama de Helm.

3. Debe tener una organización que sea miembro de una red de Plan inicial o de Plan empresarial en {{site.data.keyword.cloud_notm}}. El igual aprovecha los puntos finales de API, las CA de Hyperledger Fabric y el servicio de ordenación de la red de la plataforma {{site.data.keyword.blockchainfull_notm}} para funcionar. Si no es miembro de ninguna red blockchain, tiene que crear o unirse a una red. Para obtener más información, consulte [Creación de una red](/docs/services/blockchain/get_start.html#creating-a-network) o [Cómo unirse a una red](/docs/services/blockchain/get_start.html#joining-a-network).

4. En primer lugar, debe [desplegar una CA](/docs/services/blockchain/howto/CA_deploy_icp.html) en ICP. Utilizará esta CA como una CA de TLS. Siga los pasos de requisito previo para [trabajar con una CA en ICP](/docs/services/blockchain/howto/CA_operate.html#prerequisites) antes de desplegar el igual. No será necesario que continúe más allá de estos pasos.

5. Recupere el valor de la dirección IP de proxy de clúster de la CA de TLS desde la consola de ICP. **Nota:** necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Acciones y roles de administrador de clúster") para acceder a la IP de proxy. Inicie sesión en el clúster de ICP. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla. **Importante:** guarde este valor, ya que lo utilizará cuando configure el campo `Proxy IP` del diagrama de Helm.

6. Cree un archivo de configuración de igual y almacénelo como secreto de Kubernetes en ICP. Puede encontrar los pasos para crear este archivo en la [siguiente sección](#peer-config-file).

## Creación del archivo de configuración
{: #peer-config-file}

Antes de desplegar un igual, necesita crear un archivo JSON de configuración que contenga información importante sobre la identidad del igual y la entidad emisora de certificados en {{site.data.keyword.cloud_notm}}. A continuación, debe pasar este archivo al diagrama de Helm durante la configuración utilizando un objeto de [secreto de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Este archivo permite que el igual pueda obtener los certificados que necesita de la entidad emisora de certificados en
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

1. [Recupere la información de punto final de la CA de Plan inicial o Plan empresarial](#ibp-ca-endpoint).
2. [Registre el igual con la CA](#register-peer).
3. [Registre el administrador de igual](#register-admin) que vaya a utilizar para trabajar con el igual. No es necesario que complete este paso si ya ha registrado un administrador para desplegar otro igual.

También debe realizar varios pasos utilizando el cliente de CA de Fabric y la CA de TLS desplegada en ICP.

1. Utilice el cliente de CA de Fabric para [generar la carpeta de MSP del administrador de igual](#enroll-admin).
2. [Recupere la información de punto final de la CA de TLS](#tls-ca-endpoint).
3. Utilice el cliente de CA de Fabric para [registrar el igual con la CA de TLS](#tls-register-peer).


### Información de CA de Plan inicial o Plan empresarial
{: #ibp-ca-endpoint}

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
- `"cacert"` es el valor del campo **Certificado TLS de la entidad emisora de certificados (CA)**. Antes de insertar el certificado en el archivo, debe codificarlo en formato base64 ejecutando los mandatos siguientes y sustituyendo la serie `<paste in Certificate Authority (CA) TLS Certificate>` por el valor que ha copiado del supervisor de red.

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
{: #register-peer}

Para hacer que sus iguales se unan a canales e instalar y crear una instancia del código de encadenamiento, primero necesita registrar el igual con la CA en {{site.data.keyword.cloud_notm}}. A continuación, el despliegue del igual puede generar certificados necesarios para que el igual pueda participar en una red de Plan inicial o Plan empresarial. Complete los pasos siguientes para registrar un igual con un
`enroll ID` (ID de inscripción) y `enroll secret` (Secreto de inscripción). Pegará estos dos valores en el archivo de configuración.

1. Inicie una sesión en la plataforma {{site.data.keyword.blockchainfull_notm}} y acceda a su supervisor de red. En la pantalla "Entidad emisora de certificados", puede ver todas las identidades que se han registrado con su organización, como las aplicaciones de administrador o cliente.  ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
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
{: #register-admin}

Tras registrar la identidad del igual, también deberá crear una identidad de administrador que utilizará para trabajar con el igual. En primer lugar necesita registrar esta nueva identidad con la CA y utilizarla para generar una carpeta de MSP. A continuación, añadirá el signCert de los usuarios administradores al archivo de configuración, donde se establecerá como certificado de administrador del igual durante el despliegue. Esto le permite utilizar los certificados de la identidad de administrador para operar con la red de código de encadenamiento, por ejemplo, iniciando un nuevo canal o instalando código de encadenamiento en los iguales.

Solo necesita crear una identidad de administrador para los componentes que pertenecen a su organización. Si va a desplegar varios iguales, solo es necesario que realice estos pasos una vez. Puede utilizar el signCert que haya generado para un igual para desplegar cualquier otro igual que pertenezca a su organización.

1. Inicie una sesión en la plataforma {{site.data.keyword.blockchainfull_notm}} y acceda a su supervisor de red. En la pantalla "Entidad emisora de certificados", puede ver todas las identidades que se han registrado con su organización, como las aplicaciones de administrador o cliente.  ![Pantalla CA](../images/CA_screen_starter.png "Pantalla CA")
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
{: #enroll-admin}

Tras registrar la identidad de administrador, debe generar la carpeta de MSP de administrador de igual y el signCert. Por lo tanto, debe ejecutar un mandato de inscripción en la CA del Plan inicial o el Plan empresarial.

1. Descargue el [cliente de CA de Fabric](/docs/services/blockchain/howto/CA_operate.html#fabric-ca-client) si aún no lo ha hecho.
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

  - Certificado TLS raíz para el Plan inicial
    - EEUU: [us01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sídney: [aus01.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [Certificado TLS raíz para el Plan empresarial ![icono de enlace externo](../images/external_link.svg "icono de enlace externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

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
**Secreto** del administrador de igual que se ha [registrado utilizando el supervisor de red](#register-admin). `<ca_name>` y `<ca_url_with_port>` son los valores de `caName` y `url` del perfil de conexión. Excluya la parte de `http://` al principio del URL de CA.

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
{: #tls-ca-endpoint}

Los campos de `"tls"` del archivo de configuración necesitan información de la CA que ha desplegado en ICP. Utilizará esta CA como una CA de TLS independiente, lo cual hace que el igual sea más seguro. Utilice las instrucciones siguientes para generar la información pertinente:

- Los valores de `"cahost"` y `"caport"` son el URL y el puerto del
[URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-url). Por ejemplo, si el URL de CA es `http://9.30.94.174:30167`, el valor de
`cahost` sería `9.30.94.174` y el valor de `caport` sería `30167`.
- `"caname"` es el nombre de CA de TLS de la CA que ha desplegado en ICP. El nombre de CA de TLS es el valor que ha proporcionado en el campo `CA TLS instance name` (nombre de instancia de TLS de CA) durante la configuración de CA.
- `"cacert"` es el certificado TLS codificado en base64 de la CA. Actualice la sección siguiente con el valor de la salida del mandato cuando recupere el [certificado TLS de CA](/docs/services/blockchain/howto/CA_operate.html#ca-tls) como requisito previo.

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
{: #tls-register-peer}

Necesita registrar el igual con la CA de TLS en ICP utilizando el cliente de CA de Fabric.

1. Por ahora, debe tener el archivo de certificado TLS `tls.pem` en la carpeta
`$HOME/fabric-ca-client/catls`. Si no es así, copie el certificado TLS que ha
[descargado de ICP](/docs/services/blockchain/howto/CA_operate.html#ca-tls) en un directorio en el que pueda hacer referencia a él en mandatos posteriores. Asegúrese de que se encuentra en el directorio `$HOME/fabric-ca-client`.

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

  Los valores de `<enroll_id>` y `<enroll_password>` en el mandato son [el nombre de usuario y la contraseña del administrador de CA](/docs/services/blockchain/howto/CA_deploy_icp.html#admin-secret) que ha pasado al secreto de Kubernetes al desplegar la entidad emisora de certificados. Inserte el [URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-url) dentro de `<ca_url_with_port>`. Excluya la parte de `http://` al principio. El valor de `<tls_ca_name>` es el que ha especificado durante la
[configuración de CA](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms).

  El valor de `<ca_tls_cert_file>` es el nombre de archivo del [certificado TLS de CA](/docs/services/blockchain/howto/CA_operate.html#ca-tls) con su vía de acceso completa.

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
        ├── keystore
        │   └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Hosts de CSR (Solicitud de firma de certificado)
{: #csr-hosts}

Debe proporcionar los nombres de host de CSR para desplegar un igual. Los nombres de host de CSR incluyen la dirección IP de proxy del clúster donde va a desplegar el componente, así como el `nombre de host de servicio` que será el nombre de host del diagrama de Helm.

#### Localización del valor de la dirección IP de proxy del clúster

Si desea desplegar un igual en el mismo clúster de ICP en el que ha desplegado la CA de TLS, especifique la misma IP de proxy que haya utilizado al
[configurar la CA de TLS](/docs/services/blockchain/howto/CA_deploy_icp.html#icp-ca-configuration-parms). Si desea desplegar el componente en un clúster distinto, puede recuperar el valor de la dirección IP de proxy del clúster desde la consola de ICP. Es necesario que tenga el rol de administrador de clúster del clúster de ICP donde se vaya a desplegar el igual.

1. Inicie sesión en la consola de ICP. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster.
2. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla.
3. Inserte el `Host IP` como valor de `"hosts"` en la sección `"csr"` del archivo de configuración siguiente:

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Determinación del nombre de host de servicio

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
			"enrollid": "peer",
			"enrollsecret": "peerpw",
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

Después de haber terminado de rellenar este archivo, deberá guardarlo en formato JSON y pasarlo al despliegue del igual como secreto de Kubernetes. Cree el secreto utilizando los pasos de la [sección siguiente](#peer-config-file-ibp).

## Creación del secreto de configuración
{: #peer-config-file-ibp}

Un [secreto de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/) le permite proteger y compartir información sin tener que pasarla directamente al despliegue. Una vez que haya guardado el archivo de configuración, debe codificarlo en base64.

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

2. Inicie sesión en la consola de ICP. En el panel de navegación de la izquierda, pulse
**Configuración** y, a continuación, pulse **Secretos**. Pulse el botón **Crear secreto** para abrir un panel emergente que le permite generar un nuevo objeto de secreto.

3. En el separador **General**, complete los campos siguientes:
  - **Nombre:** proporcione un nombre exclusivo dentro del clúster para el secreto. Utilizará este nombre cuando despliegue el igual. El nombre debe estar en minúsculas.  
  **Nota:** al desplegar un igual, el despliegue genera automáticamente un nuevo secreto con el nombre `<helm_release_name>-secret`. Por lo tanto, cuando establezca el nombre del secreto, asegúrese de que sea distinto a `<helm_release_name>-secret`. De lo contrario, el despliegue del diagrama de Helm fallará debido a que el secreto que intenta crear ya existe.
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

**Nota:** el secreto de configuración de igual no se eliminará del clúster de ICP cuando suprima el release de Helm. Si suprime el igual, deberá suprimir este secreto también.

## Configuración
{: #peer-configuration}

Después de crear el objeto de secreto de configuración de igual, puede configurar el igual en ICP con los pasos siguientes. Únicamente puede instalar un igual al mismo tiempo.

1. Inicie una sesión en la consola ICP y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico etiquetado como
`ibm-blockchain-platform-prod` o `ibm-blockchain-platform-dev` si ha descargado Community Edition. Pulse sobre el mosaico para abrirlo y podrá ver un archivo Readme que incluye información sobre la instalación y configuración del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Especifique los valores para los [parámetros de configuración generales](#peer-global-parameters) y acepte el acuerdo de licencia.
5. Abra el triángulo `Todos los parámetros` y especifique el valor de los [parámetros de configuración global](#peer-global-parameters).
6. Desplácese hacia abajo hasta la sección **Configuración de igual**. Marque el recuadro de selección `Instalar igual` y rellene los [valores de configuración](#peer-parameters) del igual.
7. Pulse **Instalar**.

### Parámetros de configuración
{: #ibp-peer-configuration-parms}

En la tabla siguiente se muestran los parámetros configurables de la plataforma {{site.data.keyword.blockchainfull_notm}}, **específicos del componente de igual**, y sus valores predeterminados. Si está experimentando con el igual o es la primera vez que lo utiliza, use los valores predeterminados de los parámetros de base de datos y de almacenamiento. Desplácese hasta la sección del componente de igual para marcar el recuadro de selección `Instalar igual` y proporcionar la información de configuración asociada para dicho componente únicamente. **Aunque la interfaz de usuario del diagrama de Helm indica que no se necesita ninguna configuración adicional, debe especificar determinados parámetros para desplegar un igual.**

#### Parámetros de configuración generales y globales
{: #peer-global-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Nombre de release de Helm`| Nombre del release de Helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez que intente instalar un componente. **Importante:** Este valor debe coincidir con el valor que ha utilizado para generar el 'nombre de host de servicio' para el campo "hosts" del [archivo de secreto JSON.](#csr-hosts) | ninguno | sí  |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
|**Configuración global**| Parámetros que se aplican a todos los componentes del diagrama de Helm|||
| `Nombre de cuenta de servicio`| Especifique el nombre de la
[cuenta de servicio
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que va a utilizar para ejecutar el pod. | predeterminado | no |

#### Parámetros de configuración de igual
{: #peer-parameters}

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
|**Configuración del clúster** |**Información de configuración del clúster** | ||
| `Instalar igual` | Selecciónelo para instalar un igual|sin marcar | sí, si desea instalar un igual |
| `Arquitectura del nodo trabajador de igual`| Seleccione la arquitectura de la plataforma de nube (AMD64 o S390x)| AMD64 | sí |
| `Repositorio de imágenes de igual`| Ubicación del diagrama de Helm de igual. Este campo se rellena automáticamente con la vía de acceso instalada. Si utiliza Community Edition y no tiene acceso a Internet, debe coincidir con el directorio donde ha descargado la imagen de igual de Fabric. | ibmcom/ibp-fabric-peer | sí |
| `Etiqueta de imagen de Docker de igual`|Valor de la etiqueta asociada con la imagen de igual |1.2.1, se rellena automáticamente con el valor correcto.|sí|
| `Configuración de igual`|Puede personalizar la configuración del igual pegando su propio archivo de configuración
`core.yaml` en este campo. Para ver un archivo `core.yaml` de ejemplo, consulte la
[configuración de ejemplo de `core.yaml`
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) Solo para usuarios avanzados. |ninguno|no|
| `Secreto de configuración de igual (obligatorio)`| Nombre del [Secreto de configuración de igual](#peer-config-secret) que ha creado en ICP.  |ninguno|sí|
|`MSP de organización (obligatorio)`|Este valor se puede encontrar en el supervisor de red (interfaz de usuario de IBP) pulsando "Configuración de igual remoto" en la pantalla Visión general.  |ninguno|sí|
|`Tipo de servicio de igual`| Se usa para especificar si los
[puertos externos se deben exponer
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release. | NodePort |sí|
| `Base de datos de estado`| La [base de datos de estado](/docs/services/blockchain/glossary.html#state-database) utilizada para almacenar el libro mayor del canal. El igual tiene que utilizar la misma base de datos que la [red blockchain](/docs/services/blockchain/v10_dashboard.html#network-preferences). | LevelDB | sí |
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
{: #ibp-helm-cli}

Como alternativa, puede utilizar la CLI de Helm para instalar el release de Helm. Antes de ejecutar el mandato `helm install`, asegúrese de
[añadir el repositorio de Helm del clúster al entorno de CLI de Helm
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Adición del repositorio interno de Helm a la CLI de Helm").

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
{: #verify-installation-ibp}

Tras completar los parámetros de configuración y pulsar el botón **Instalar**, pulse el botón **Ver release de Helm** para ver el despliegue. Si se ha realizado correctamente, debe aparecer el valor 1 en los campos
`DESIRED`, `CURRENT`, `UP TO DATE` y `AVAILABLE` de la tabla Despliegue. Es posible que tenga que pulsar Renovar y esperar a que se actualice la tabla. También puede encontrar la tabla Despliegue pulsando el icono de
**Menú** de la esquina superior izquierda de la consola de ICP. En la lista de menús, pulse
**Cargas de trabajo** y, a continuación, **Releases de Helm**.

Si se desplaza hacia abajo hasta la sección `Notas`, hay información importante que utilizará al
[trabajar con el igual](/docs/services/blockchain/howto/peer_operate_ibp.html).

<!--
### Verifying the peer can connect to Starter or Enterprise Plan network

You can run a peer CLI command from inside the peer container to verify that your peer has connected to your network on the {{site.data.keyword.blockchainfull_notm}} Platform. Complete the following instructions to run the `peer channel fetch` command to fetch the genesis block from a channel:

1. If you have not already connected to your ICP cluster, follow these [instructions](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) to connect to it and use the cli from inside the peer container.

2. If you deploy your peer behind a firewall, you need to open a passthru to enable the network on the platform to complete a TlS handshake with your peer. You only need to enable a passthru for the Node port bound to port 7051 of your peer. For more information, see [finding your peer endpoint information](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-endpoint).

3. Your organization needs to be added to a channel in the network before you can fetch the genesis block.

  - You can start a new channel for the peer. If you are using IBP Starter or Enterprise Plan, you can automatically include your organization during [channel creation](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Another member of the blockchain network can also add your organization to an existing channel by using a [channel update](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    The peer uploads its signCert during installation, so that you need to only synchronize the certificate to the channel. On the "Channels" screen of the Network Monitor, locate the channel that your organization joined and select **Sync Certificate** from the drop-down list under the **Action** header. This action synchronizes the certificates across all the peers on the channel.

4. You also need to upload the signCert of your peer admin to Starter Plan or Enterprise Plan in order for a remote CLI or application to perform channel operations such as fetching the channel genesis block and instantiating chaincode.
    1. On your local machine, cat the file `HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem` and then copy it to the clipboard.
    2. Enter the Network Monitor of your network on {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Click **Members** in the left navigator and click the **Certificates** tab.
    4. Click the **Add Certificate** button.
    5. In the **Add certificate** window, give your certificate a name, such as "peer-admin", paste in the certificate you just copied to the clipboard and click **Submit**.
    6. You will be asked whether you want to restart the peers. Click **Restart**.
    7. Click **Channels** in the left navigator and locate the channel that the peer will join.
    8. Click the three dots under the **Actions** header and click **Sync Certificate**. In the **Sync certificate** window, click **Submit**.

5. Retrieve configuration information from your Connection profile available on the "Overview" screen of your Network Monitor. Click **Connection Profile** and then **Download**.

  - Find the orderer URL by searching for **orderers**, which is located under `orderers > url`. Make a note of the URL that begins with the network name. The URL is similar to the following example:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Find the name of your organization by searching for **organizations**. This should be the same organization as you use to register your peer. You can find your organization's name together with its associated `mspid`. Make a note of the value of the `mspid`.

6. Run the following commands to set the environment variables in the peer container.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Replace the fields with your own information.
  - Replace `<ORDERER_URL>` with the hostname and port of the orderer from the `creds.json` file.
  - Replace `<CHANNEL_NAME>` with the name of the channel that the peer joins.
  - Replace `<CC_NAME>` with any name to refer to your chaincode.
  - Replace `<ORGANIZATION_MSP_ID>` with the name of the organization from the `creds.json` file.
  - Replace `<PEER_ADDR>` with `localhost:7051`

  For example:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=org1
  export PEERADDR=localhost:7051
  ```
  {:codeblock}

7. Run the following peer CLI command to fetch the genesis block of the channel.

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
  ```
  {:codeblock}

  **Note:** It is possible that when you run any of these CLI commands, you might experience the following warning that can be safely ignored.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
  ```

  Verify that the command worked successfully and that genesis block is added to your peer container by running the following command:

  ```
  ls *.block
  ```
  {:codeblock}

  You know that the genesis block is added successfully when you see something that is similar to the following example:

  ```
  defaultchannel_0.block
  ```

  Successfully fetching the genesis block indicates that your peer can connect to your Starter or Enterprise Plan network.
  You still need to join the peer to the channel and install chaincode. See [operating your peer](/docs/services/blockchain/howto/peer_operate_ibp.html) for more information.

-->

## Visualización de los registros de igual
{: #peer-ibp-view-logs}

Los registros de componentes se pueden visualizar desde la línea de mandatos utilizando los
[`mandatos de la CLI kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-kubectl-configure) o a través de
[Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que se incluye en el clúster de ICP. Para obtener más información, consulte estas
[instrucciones para acceder a los registros](/docs/services/blockchain/howto/peer_operate_ibp.html#peer-ibp-view-logs).

## Qué hacer a continuación

Después de haber desplegado el igual, deberá realizar varios pasos operativos para poder enviar transacciones y leer el libro mayor distribuido de la red blockchain. Para obtener más información, consulte [Funcionamiento de iguales con el Plan inicial o el Plan empresarial](/docs/services/blockchain/howto/peer_operate_ibp.html).

---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-16"

keywords: IBM Cloud Private, Certificate Authority, operate CA, CA admin secret, CA logs, Helm chart, on-prem, CLI, CA TLS cert, CA endpoint, TLS CA

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Funcionamiento de una entidad emisora de certificados en {{site.data.keyword.cloud_notm}} Private
{: #ca-operate}

Las entidades emisoras de certificados (CA) proporcionan las identidades en la red. Una CA se puede considerar como un notario público de confianza que se utiliza para establecer la confianza entre varias partes. Se proporciona un certificado a cada entidad de la red, que una CA raíz firmará para encapsular la identidad digital de la entidad. Este certificado es la raíz de la confianza para todas las operaciones de firma y verificación que se realizan en la red.
{:shortdesc}

Cada organización de una red blockchain multinube debe desplegar su propia CA. La CA de su organización firmará las solicitudes de los componentes con los que trabaje la organización, como un servicio de ordenación, los iguales o las aplicaciones. Por lo tanto, debe desplegar la CA antes de desplegar cualquier otro componente.

Es necesario realizar algunos pasos de requisito previo para poder utilizar la entidad emisora de certificados:

- [Configure las CLI](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure)
- [Recupere el URL de la entidad emisora de certificados](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url)
- [Descargue el certificado TLS de la CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)
- [Configure el cliente de CA de Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client)
- [Genere certificados con el administrador de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin)

Tras realizar los pasos de requisito previo, puede utilizar la CA para registrar nuevas identidades en la red con la organización y generar los certificados que pueden utilizar las aplicaciones.

- [Utilización de la CA para desplegar un clasificador o un igual](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer)

En esta guía también se proporciona información conceptual importante acerca de cómo trabajar con la CA y cómo utilizar sus certificados para gestionar una red blockchain.

- [Proveedores de servicios de pertenencia (MSP)](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp)
- [Certificados TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls)

## Requisitos previos
{: #ca-operate-prerequisites}

Independientemente de si va a crear o a unirse a una red, deberá completar estos pasos de requisito previo para utilizar la CA y desplegar otros componentes e identidades que pertenecen a la organización.

### Configuración de las CLI
{: #ca-operate-kubectl-configure}

Necesitará utilizar la herramienta de línea de mandatos **kubectl** para conectarse al contenedor de CA que se ejecuta en {{site.data.keyword.cloud_notm}} Private.

1. Inicie sesión en la interfaz de usuario del clúster de {{site.data.keyword.cloud_notm}} Private. Vaya al separador **Herramientas de línea de mandatos** y pulse **CLI de Cloud Private**. Verá las herramientas siguientes, las cuales puede descargar.

   * Instalar la CLI y los plugins de IBM Cloud Private
   * Instalar la CLI de Kubectl
   * Instalar Helm
   * Instalar la CLI de Istio

  Para operar con una CA, necesitará utilizar las primeras **tres** herramientas, entre las que Helm es opcional. Pulse sobre cada una de ellas y ejecute los mandatos de `curl` para el tipo de máquina que utilice. A continuación, emita los mandatos
`chmod` y `sudo mv` para cada herramienta. El mandato `chmod` cambiará el permiso de la CLI en cuestión para hacerla ejecutable y el mandato `sudo mv` moverá el archivo y lo renombrará.

  Los mandatos de la primera herramienta **cloudctl** pueden tener un aspecto similar al del ejemplo siguiente:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary>
  sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Los mandatos de la segunda herramienta **kubectl** pueden tener un aspecto similar al del ejemplo siguiente:

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary>
  sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Tras configurar **kubectl**, ejecute el mandato siguiente:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Si tiene éxito, el mandato debe devolver una respuesta similar al ejemplo siguiente, donde
`ca-6cf85f6687-hcxpl` es el nombre del contenedor de CA.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Ahora está listo para utilizar la herramienta **kubectl** para obtener el URL de CA.

3. Si lo prefiere, si desea utilizar **Helm**, realice algunos pasos más. Tenga en cuenta que la instalación de Helm es opcional y no es necesario utilizarla en estas instrucciones. No obstante, puede resultar útil para gestionar los releases de Helm y crear sus propios archivados para su despliegue en {{site.data.keyword.cloud_notm}} Private.

  1. Pulse "Instalar Helm" y ejecute el mandato `curl` desde la interfaz de usuario de {{site.data.keyword.cloud_notm}} Private.
  2. Desempaquete el archivo `tar` ejecutando el mandato siguiente:  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. Ejecute el mandato `sudo mv` añadiendo `/helm` a `darwin-amd64`. Tenga en cuenta que no es necesario que ejecute el mandato `chmod` para Helm. Por ejemplo:  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  Puede ejecutar el mandato `helm help` para confirmar que Helm se ha instalado correctamente.

### Recuperación del URL de la entidad emisora de certificados
{: #ca-operate-url}

Deberá hacer referencia al URL de CA para que las solicitudes puedan generar certificados o registrarse con una nueva identidad. Puede encontrar el URL de CA utilizando la interfaz de usuario de la consola de {{site.data.keyword.cloud_notm}} Private. Necesitará ser un
[administrador del clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Acciones y roles de administrador de clúster") para realizar los pasos siguientes:

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue de la CA.
5. Si no lo ha hecho aún, siga los pasos para configurar la [CLI kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), que deberá utilizar para interactuar con el contenedor de CA.
6. En la CLI, ejecute el primer mandato de la nota, que sigue a **1. Obtenga el URL de aplicación ejecutando estos mandatos:** Este mandato imprimirá el URL y el puerto de la CA, que son similares a los del ejemplo siguiente. Guarde este valor como URL de aplicación para utilizarlo en mandatos posteriores.

  ```
  http://9.30.94.174:30167
  ```

**Nota:** si despliega la CA detrás de un cortafuegos, necesitará abrir un paso a través para habilitar la red en la plataforma para completar un reconocimiento de TLS con la CA. Debe habilitar un paso a través únicamente para el puerto que se ha concatenado con el URL de CA.


### Recuperación del certificado TLS de CA
{: #ca-operate-tls}

Necesita descargar el certificado TLS de CA y pasarlo con los mandatos para comunicarse con la CA desde un cliente remoto.

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private. Pulse el icono **Menú** en la esquina superior izquierda.
2. Pulse **Carga de trabajo** > **Releases de Helm**.
3. Busque el nombre del release de Helm y abra el panel de detalles del release de Helm.
4. Desplácese hacia abajo hasta la sección **Notas** en la parte inferior del panel. En la sección **Notas**, puede ver un conjunto de mandatos que le ayudarán a utilizar el despliegue de la CA.
5. Si no lo ha hecho aún, siga los pasos para configurar la [CLI kubectl](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure), que deberá utilizar para interactuar con el contenedor de CA.
6. En la CLI, ejecute los mandatos de la tercera nota, que sigue a **3. Obtenga el certificado TLS:** Este mandato guarda el certificado TLS como el archivo `tls.pem` en el directorio local. Deberá hacer referencia a este certificado en un mandato posterior. Tome nota de esta ubicación.
7. El mandato también convierte el certificado al formato base64 y lo imprime. El resultado tiene un aspecto similar a la serie siguiente:

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  Debe copiar esta serie y guardarla como su certificado de TLS de CA de **{{site.data.keyword.cloud_notm}} Private**. Lo utilizará al desplegar componentes adicionales.
  {:important}

### Configuración del cliente de CA de Fabric
{: #ca-operate-fabric-ca-client}

Puede utilizar el cliente de CA de Fabric para trabajar con la CA. En estas instrucciones se explica cómo utilizar el cliente de CA de Fabric para inscribir y registrar identidades para otros componentes que pertenezcan a la organización. También puede utilizar los SDK de Fabric para completar los pasos de requisito previo.

1. Debe descargar el [cliente de CA de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Descargar el cliente de CA de Fabric") en el sistema de archivos local.

  La forma más fácil de obtener el cliente de CA de Fabric es descargar todos los binarios de herramientas de Fabric directamente. Vaya al directorio en el que desee descargar los binarios con la línea de mandatos, y obténgalos ejecutando el mandato [Curl ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/prereqs.html#install-curl "Curl") siguiente.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.4.0 1.4.0 -d -s
  ```
  {:codeblock}

  Este mandato instala los binarios en un directorio `bin/`.

2. Establezca la vía de acceso de `PATH` en el directorio donde haya descargado los binarios de herramientas de Fabric:

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  Por ejemplo, si ha instalado los binarios en el directorio de inicio, debe establecer la variable `PATH` como:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Cree la carpeta donde vaya a almacenar los certificados del administrador de CA.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Establezca el valor de la variable de entorno `$FABRIC_CA_CLIENT_HOME` en la vía de acceso donde el cliente de CA va a almacenar los certificados de [MSP](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) generados. Asegúrese de eliminar el material de configuración que se pueda haber creado en intentos anteriores. Si no ha ejecutado el mandato `enroll` anteriormente, la carpeta `msp` y el archivo `.yaml` no existen.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Copie el certificado TLS que ha [descargado de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) en un directorio donde pueda hacer referencia al mismo en mandatos posteriores. Asegúrese de que se encuentra en el directorio `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### Generación de certificados con el administrador de CA
{: #ca-operate-enroll-ca-admin}

Antes de desplegar componentes o aplicaciones de cliente con la CA, debe generar certificados que le autentiquen como administrador con capacidad para registrar nuevos usuarios. El proceso de generación de los certificados necesarios, la clave privada y el certificado (también conocido como certificado de inscripción o signCert), se denomina **inscripción**.

Solo puede generar certificados utilizando identidades que se hayan registrado con la entidad emisora de certificados. Proporcione el nombre y el secreto de la identidad para generar certificados. Al desplegar la CA, se registró automáticamente una identidad de **administrador de CA**. Ahora puede utilizar el nombre y contraseña de dicho administrador para emitir un mandato
`enroll` con el cliente de CA de Fabric para generar una carpeta de MSP con certificados que se utilizarán luego para registrar otras identidades de igual o de clasificador.

1. Asegúrese de completar los pasos para [configurar el cliente de CA de Fabric](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) y de que
`$FABRIC_CA_CLIENT_HOME` esté establecido en el directorio en el que desee almacenar los certificados de administrador de CA.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Ejecute el mandato siguiente para generar certificados:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  Los valores de `<enroll_id>` y `<enroll_password>` en el mandato son el
[nombre de usuario administrador de CA y contraseña](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret) que haya pasado al secreto de Kubernetes al desplegar la entidad emisora de certificados. Inserte el
[URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) dentro de `<ca_url_with_port>`. Excluya la parte de `http://` al principio. `<ca_name>` es el valor que ha proporcionado en el campo
`CA Name` al desplegar la CA.

  `<ca_tls_cert_path>` es la vía de acceso completa del [certificado TLS de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls).

  Una llamada real es similar al siguiente mandato de ejemplo:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  **Sugerencia:** si el valor del URL de inscripción, el valor del parámetro `-u`, contiene un carácter especial, debe codificar el carácter especial o especificar el URL entre las comillas simples. Por ejemplo, `!` se convierte en `%21`, o el mandato se parece al siguiente:

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
  ```
  {:codeblock}

  El mandato `enroll` genera un conjunto completo de certificados, que se conoce como carpeta de proveedor de servicios de pertenencia (MSP), ubicado dentro del directorio donde ha establecido la vía de acceso `$HOME` del cliente de CA de Fabric. Por ejemplo, `$HOME/fabric-ca-client/ca-admin`. Para obtener más información sobre los MSP y el contenido de la carpeta de MSP, consulte
[Proveedores de servicios de pertenencia](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp).

  Si el mandato `enroll` falla, consulte el [Tema de resolución de problemas](/docs/services/blockchain/howto/CA_operate.html#ca-operate-troubleshooting) para ver las causas posibles.

Puede ejecutar un mandato de árbol (tree) para verificar que se han completado todos los pasos de requisito previo. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── catls
    └── tls.pem    
```

## Utilización de la CA para desplegar un clasificador o un igual
{: #ca-operate-deploy-orderer-peer}

Antes de desplegar un clasificador o un igual, debe crear un archivo JSON de configuración que contenga información importante acerca de la identidad del componente y de la CA. A continuación, debe pasar este archivo al diagrama de Helm durante la configuración utilizando un objeto de [secreto de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Este archivo permitirá que el clasificador o el igual pueda obtener los certificados que necesita de la CA para unirse a una red blockchain. Este archivo contiene también un certificado de administrador que le permite utilizar el componente como usuario administrador.

En las instrucciones siguientes se proporciona un [archivo de configuración JSON de plantilla](/docs/services/blockchain/howto/CA_operate.html#ca-operate-config-file-template) que puede editar y guardar en el sistema de archivos local, y que le indica cómo utilizar la CA para completar este archivo.

- Siga las instrucciones que se muestran a continuación si va a desplegar un clasificador en {{site.data.keyword.cloud_notm}} Private o va a desplegar un igual para conectarse a un consorcio que se aloja en {{site.data.keyword.cloud_notm}} Private.
- Si desea desplegar un igual para conectarse a un Plan inicial o un Plan empresarial, siga las instrucciones de
[Despliegue de iguales en IBM Cloud Private para conectarse a un Plan inicial o un Plan empresarial](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) en su lugar. Estos pasos le indicarán cómo utilizar la CA en {{site.data.keyword.blockchainfull_notm}} Platform para desplegar el igual en {{site.data.keyword.cloud_notm}} Private.

### Archivo de configuración
{: #ca-operate-config-file-template}

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

Copie este archivo completo en un editor de texto, donde puede editarlo y guardarlo en el sistema de archivos local como archivo JSON.

### Información de conexión de CA

En primer lugar, es necesario proporcionar la información de conexión de la CA en {{site.data.keyword.cloud_notm}} Private. Utilice la información siguiente para completar los valores de la sección `"component"` del archivo:

- Los valores de `"cahost"` y `"caport"` son el URL y el puerto del
[URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Por ejemplo, si el URL de CA es http://9.30.94.174:30167, el valor de `"cahost"` sería `9.30.94.174` y el valor de
`"caport"` sería `30167`.
- `"caname"` es el nombre de la CA que se ha especificado al desplegar el diagrama de Helm de CA. Ha hecho referencia al nombre de CA en el mandato cuando se ha inscrito utilizando la [identidad de administrador de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin).
- `"cacert"` es el certificado TLS codificado en base64 de la CA. [Recupere el certificado TLS de la CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) desde la consola de {{site.data.keyword.cloud_notm}} Private utilizando la nota del release de Helm, e inserte la salida en base64 de los mandatos de la nota en la sección siguiente.
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

- Los campos de la sección `"tls"` siguiente necesitan la misma información que la que ha pasado a las secciones "component" anteriores, excepto que debe utilizar el valor del nombre de instancia de TLS de CA que se ha especificado durante el despliegue de la CA. Utilice el mismo certificado TLS para el valor de `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

Tras recuperar la información de conexión de la entidad emisora de certificados, debe utilizar el cliente de CA de Fabric para realizar varios pasos operativos:

1. [Registre el nuevo componente con la CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-component).

2. También deberá [registrar un administrador de componentes](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin) y, a continuación,
[generar certificados para el administrador de componentes](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin) dentro de una carpeta de MSP. No es necesario que complete este paso si ya ha registrado un administrador para desplegar otro componente.

3. [Registre el nuevo componente con la CA de TLS](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls-register-component).

### Registro de la identidad de componente con la CA
{: #ca-operate-register-component}

Si desea formar un consorcio desplegando un servicio de ordenación y añadiendo organizaciones al mismo, o desplegar iguales y hacer que se unan a canales, en primer lugar debe registrar la identidad del componente con la CA. A continuación, el despliegue del componente puede generar los certificados necesarios para que el igual o el clasificador pueda participar en una red.

1. [Genere certificados con el administrador de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin) utilizando el cliente de CA de Fabric. Utilice estos certificados de administrador para emitir los mandatos siguientes. Asegúrese de que `$FABRIC_CA_CLIENT_HOME` esté establecido en
`$HOME/fabric-ca-client/ca-admin`.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Emita el mandato siguiente para encontrar su afiliación y el nombre de su organización.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  El mandato puede tener un aspecto similar al del ejemplo siguiente:
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Debería ver información parecida a la del siguiente ejemplo:
  ```
  affiliation: org1
      affiliation: org1.department1
  ```

  Anote el segundo valor de **affiliation**, por ejemplo `org1.department1`. Debe utilizar este valor en el mandato siguiente.

3. Ejecute el mandato siguiente para registrar el clasificador o el igual.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Cree un nombre y una contraseña para el componente y utilícelos para sustituir los valores de `name` y `secret`. **Importante:** tome nota de esta información. Establezca `--id.type` en `orderer` si va a desplegar un clasificador, o establézcalo en `peer` si va a desplegar un igual. El mandato puede tener un aspecto similar al del ejemplo siguiente:

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Tras ejecutar este mandato, debe especificar los valores de `name` y
`secret` como `"enrollid"` y `"enrollsecret"` en el archivo de configuración, en la sección
`"component"`:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```

  Solo puede registrar una identidad una vez. Si tiene algún problema, intente un mandato con un nuevo nombre de usuario y contraseña. Como medida de seguridad, utilice cada identidad, y el ID de inscripción y el secreto que la acompañan, para desplegar únicamente un igual. Aunque puede utilizar una identidad de **administrador** para varios componentes (esta es la estrategia de despliegue recomendada), no reutilice los ID ni las contraseñas de igual.

  Cuando el mandato finalice correctamente, debería ver información parecida a la del siguiente ejemplo:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### Registro del administrador
{: #ca-operate-register-admin}

Tras registrar el componente, necesitará crear también una identidad de administrador que puede utilizar para trabajar con el componente. En primer lugar, necesita registrar esta nueva identidad con la CA y utilizarla para generar una carpeta de MSP. A continuación, deberá añadir el signCert de los usuarios administradores al archivo de configuración, donde se establecerá como certificado de administrador del clasificador o del igual durante el despliegue. Esto le permite utilizar los certificados de la identidad de administrador para operar con la red de código de encadenamiento, por ejemplo, iniciando un nuevo canal o instalando código de encadenamiento en los iguales.

Solo necesita crear una identidad de administrador para los componentes que pertenecen a su organización. Si va a desplegar varios iguales o un clasificador, solo es necesario que realice estos pasos una vez. Puede utilizar el signCert que haya generado para un componente para desplegar cualquiera de los iguales o un clasificador.

Asegúrese de que `$FABRIC_CA_CLIENT_HOME` esté establecido en la vía de acceso al MSP del administrador de CA.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registre la identidad de administrador de componentes con la CA ejecutando el mandato siguiente:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Cree nuevos valores de `name` y `secret` de identidad de usuario para el administrador. Esta identidad pasará a ser el administrador para todos los componentes que despliegue. Asegúrese de utilizar valores distintos a los de la identidad del igual o el clasificador que acaba de registrar. El mandato tiene un aspecto similar al del ejemplo siguiente.

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**Importante:** tome nota de esta información. Utilizará estos valores de `name` y
`secret` para generar la carpeta de MSP utilizando el mandato `enroll`.

#### Generación de la carpeta de MSP de administrador
{: #ca-operate-enroll-admin}

Tras registrar el administrador de componentes, puede generar los certificados utilizando el mandato siguiente:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Por ejemplo:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Una vez que este mandato haya finalizado correctamente, generará una nueva carpeta de MSP en el directorio que ha especificado utilizando el distintivo `-M`. Este directorio contiene los certificados que utilizará para trabajar con los componentes. Puede verificar que el mandato enroll ha funcionado utilizando un mandato tree. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:


```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── IssuerPublicKey
│       ├── IssuerRevocationPublicKey
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── IssuerPublicKey
        ├── IssuerRevocationPublicKey
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```


En este directorio de MSP, abra el archivo de signCert del nuevo usuario y conviértalo al formato base64 utilizando los mandatos siguientes:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
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

Por ejemplo:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

Este mandato imprime una serie similar a la del ejemplo siguiente:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Copie esta serie en el campo `"admincerts"` situado bajo la sección "component" del archivo de configuración.

#### Registro de la identidad de componente con la CA de TLS
{: #ca-operate-tls-register-component}

También debe registrar el clasificador o el igual con la CA de TLS. Para ello, primero deberá inscribirse utilizando el administrador de la CA de TLS. Cambie `$FABRIC_CA_CLIENT_HOME` al directorio en el que desee almacenar los certificados de administrador de CA de TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Ejecute el mandato siguiente para inscribirse como administrador en la CA de TLS. El mandato es el mismo que el que ha utilizado para inscribirse como [administrador de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin), excepto por el hecho de que debe utilizar el nombre de instancia de TLS de CA que ha especificado durante la [configuración de CA.](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms) Asegúrese de utilizar los mismos valores de `ca-admin-name` y `ca-admin-password` en el secreto de CA.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Una llamada real será similar al ejemplo siguiente:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Después de inscribirse, tendrá los certificados necesarios para registrar el componente con la CA de TLS. Ejecute el mandato siguiente para registrar el clasificador o el igual:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Este mandato es similar al que ha utilizado para registrar la identidad de componente con la CA, excepto por el uso del nombre de CA de TLS. Si va a desplegar un clasificador en lugar de un igual, establezca `--id.type` en `orderer` en lugar de en `peer`. Debe proporcionar a esta identidad un nombre de usuario y una contraseña distintos a los que ha utilizado para la CA predeterminada. Un registro real puede tener un aspecto similar al del mandato siguiente:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Copie los valores de `name` y `secret` en `"enrollid"` y
`"enrollsecret"` en la sección `"tls"` del archivo de configuración:

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### Hosts de CSR (Solicitud de firma de certificado)
{: #ca-operate-csr-hosts}

Debe proporcionar un nombre de host de CSR para desplegar un clasificador o un igual. Este nombre de host incluye la dirección IP de proxy del clúster donde va a desplegar el componente y el `nombre de host de servicio` que se ha generado al desplegar el diagrama de Helm.

#### Localización del valor de la dirección IP de proxy del clúster
{: #ca-operate-cluster-proxy-ip}

Si desea desplegar un clasificador o un igual en el mismo clúster de {{site.data.keyword.cloud_notm}} Private en el que ha desplegado la CA, asegúrese de tener un rol de
[administrador de clúster ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Acciones y roles de administrador de clúster") en el clúster de {{site.data.keyword.cloud_notm}} Private donde se va a desplegar el clasificador o el igual. A continuación, especifique la misma IP de proxy que ha utilizado en la [configuración para la CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms). Si desea desplegar el clasificador o el igual en un clúster distinto, puede recuperar el valor de la dirección IP de proxy del clúster desde la consola de {{site.data.keyword.cloud_notm}} Private realizando los pasos siguientes:

1. Inicie sesión en la consola de {{site.data.keyword.cloud_notm}} Private. En el panel de navegación de la izquierda, pulse
**Plataforma** y, a continuación, pulse **Nodos** para ver los nodos que están definidos en el clúster.
2. Pulse sobre el nodo que tenga el rol `proxy` y, a continuación, copie el valor de `Host IP` de la tabla.
3. Inserte el `Host IP` como valor de `"hosts"` en la sección `"csr"` del archivo de configuración siguiente:

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### Determinación del nombre de host de servicio

Se genera un `nombre de host de servicio` al desplegar un diagrama de Helm. Está basado en el `nombre de release de Helm` que se utilice al desplegar el diagrama de Helm.

- Si va a desplegar un clasificador, el `nombre de host de servicio` es el
`helm_release_name` que especifique durante el despliegue con la serie `-orderer` añadida al final. Por ejemplo, si la dirección IP de proxy del clúster es `9.42.134.44` y especifica un `nombre de release de Helm` de
`org1orderer`, puede insertar los valores siguientes en la sección `"csr"` del archivo:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- Si va a desplegar un igual, el `nombre de host de servicio` es el `nombre de release de Helm` que especifique durante el despliegue sin ninguna serie suplementaria. Por ejemplo, si la dirección IP de proxy del clúster es 9.42.134.44" y el `nombre de release de Helm` es
`org1peer1`, puede insertar los valores siguientes en la parte de "hosts" de "csr":

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
{: #ca-operate-config-file}

Una vez que haya completado todos los pasos anteriores, el archivo de configuración actualizado puede tener un aspecto similar al del ejemplo siguiente:


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer1",
            "enrollsecret": "peer1pw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}



Puede dejar los demás campos en
blanco. Guarde este archivo, ya que necesitará utilizarlo al desplegar un
[clasificador](/docs/services/blockchain/howto/orderer_deploy_icp.html) o un [igual](/docs/services/blockchain/howto/peer_deploy_ibp.html).

## Proveedores de servicios de pertenencia (MSP)
{: #ca-operate-msp}

Los componentes de {{site.data.keyword.blockchainfull_notm}} Platform consumen identidades a través de proveedores de servicios de pertenencia (MSP). Los MSP asocian los certificados que emiten las entidades emisoras de certificados con permisos y roles. Para obtener más información sobre los MSP, consulte [el tema Pertenencia de la documentación de Hyperledger Fabric
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "el tema sobre Pertenencia en la documentación de Hyperledger Fabric").

Las carpetas de MSP deben tener una estructura definida para que las utilicen los componentes de Fabric. El cliente de CA de Fabric establece esta estructura creando las carpetas de MSP siguientes:

- **cacerts:** contiene el certificado de la CA raíz de la red.
- **intermediatecerts:** son los certificados de las CA intermedias de la cadena de confianza (que vuelve a una o varias CA raíz). Cada organización de plan empresarial tiene dos CA intermedias para la migración tras error y la alta disponibilidad.
- **signCerts:** esta carpeta contiene el certificado para firmas, también conocido como signCert o certificado de inscripción. Este certificado se adjunta a las llamadas a la red (por ejemplo, una invocación de código de encadenamiento) cuando se hace referencia al directorio MSP desde la línea de mandatos o se crea un objeto de contexto de usuario con los SDK. Puede cargar este certificado en {{site.data.keyword.blockchainfull_notm}} Platform si desea trabajar con una red desde el SDK o desde la línea de mandatos.
- **keystore:** esta carpeta contiene la clave privada. Esta clave se utiliza para firmar llamadas a la red cuando se hace referencia al directorio MSP desde la línea de mandatos o se crea un objeto de contexto de usuario con los SDK, pero se adjunta a las llamadas del modo en que se adjunta un signCert. Es **vital** que esta clave se mantenga segura. Si se ve comprometida, se puede utilizar para suplantar su identidad.

También puede crear una carpeta de MSP a la que puede hacer referencia el cliente fabric-ca-client utilizando el supervisor de red y las API de Swagger.

- **cacerts** e **intermediatecerts**: puede recuperar estos certificados con las [API de Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) emitiendo una solicitud `Get` a la API de MSP.
- **signCerts** y **keystore**: para generar estos certificados, pulse el botón **Generar certificados** en el panel "Entidad emisora de certificados". Se abre una ventana emergente con dos certificados. Copie y almacene el **Certificado** en signCerts y la **Clave privada** en el almacén de claves. Guarde estos certificados en un lugar seguro, ya que no se almacenan en la plataforma.

Muchos componentes de Fabric contienen información adicional dentro de su carpeta de MSP. Por ejemplo, si trabaja con un igual remoto, es posible que vea las carpetas siguientes:

- **admincerts:** esta carpeta contiene la lista de administradores para esta organización o componente. Debe cargar el signCert en esta carpeta si trabaja con un igual remoto desde la línea de mandatos o desde los SDK. Si utiliza el cliente de CA de Fabric, también necesitará una carpeta de certificados de administrador en el MSP que contiene los signCerts correspondientes que se deben reconocer como certificados de
administrador.
- **tls:** esta es una carpeta donde se almacenan los certificados TLS que se utilizan para comunicarse con otros componentes de la red.

Para obtener más información sobre la estructura de los MSP, consulte [Pertenencia ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Pertenencia") y [Proveedores de servicios de pertenencia ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/msp.html "Proveedores de servicios de pertenencia") en la documentación de Hyperledger Fabric.


## Visualización de los registros de CA
{: #ca-operate-view-logs}

Los registros de los componentes se pueden consultar desde la línea de mandatos mediante [`mandatos de CLI kubectl`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) o a través de [Kibana ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.elastic.co/products/kibana "Su ventana en Elastic Search"), que está incluido en su clúster de {{site.data.keyword.cloud_notm}} Private.

- Utilice el mandato `kubectl logs` para ver los registros de contenedor dentro del pod. Si no está seguro de cuál es el nombre del pod, ejecute el mandato siguiente para ver la lista de pods.

   ```
   kubectl get pods
   ```
   {:codeblock}

   A continuación, ejecute el mandato siguiente para recuperar los registros del contenedor de CA que se encuentra dentro del pod, sustituyendo `pod_name` por el nombre del pod de la salida del mandato anterior:

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   Para obtener más información sobre el mandato `kubectl logs`, consulte la
[Documentación de Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, puede acceder a los sucesos y registros de despliegue mediante la [consola de gestión del clúster de {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/troubleshoot/events.html), que abre los registros en Kibana.

  **Nota:** al visualizar los registros en Kibana, es posible que reciba la respuesta `No results found`. Esta
condición se puede producir si {{site.data.keyword.cloud_notm}} Private utiliza la dirección IP del nodo trabajador como su nombre de host. Para resolver este problema, elimine el filtro que comienza por `node.hostname.keyword` al principio del panel y los registros se volverán visibles.

## Seguridad
{: #ca-operate-security}

Si se emite solo un número limitado de certificados (por ejemplo, solo se emiten certificados de iguales, del servidor Node.js y de las aplicaciones cliente), es posible que se pueda dejar la CA fuera de línea para garantizar una mayor seguridad y evitar que el material de claves de CA se vea comprometido. Sin embargo, la CA debería estar en línea cuando se tengan que emitir solicitudes de certificados bajo demanda a los usuarios. Se recomienda encarecidamente proteger la clave privada del administrador de CA con [HSM](/docs/services/blockchain/glossary.html#glossary-hsm) si es posible.

## Resolución de problemas
{: #ca-operate-troubleshooting}

### **Problema:** error al ejecutar el mandato `enroll`
{: #ca-operate-enroll-error1}

Al ejecutar el mandato enroll del cliente de CA de Fabric, es posible que el mandato falle con el error siguiente:

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**Solución:**

Este error se puede producir cuando el cliente de CA de Fabric intenta inscribirse pero no se puede conectar con la CA. Esto puede ocurrir si:   

- El mandato `enroll` contiene una parte `https://` adicional en el parámetro `-u`.
- El nombre de CA no es correcto.
- El nombre de usuario o la contraseña son incorrectos.

Revise los parámetros que ha especificado en el mandato `enroll` y asegúrese de que no exista ninguna de estas condiciones.

### **Problema:** error con el URL de CA al ejecutar el mandato `enroll`
{: #ca-operate-enroll-error2}

Es posible que el mandato de inscripción del cliente de CA de Fabric falle si el URL de inscripción, el valor del parámetro `-u`, contiene un carácter especial. Por ejemplo, el mandato siguiente con el ID de inscripción y la contraseña `admin:C25A06287!0`,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

fallará y generará el error siguiente:

```
!pw@9.12.19.115: event not found
```

### **Solución:**
{: #ca-operate-enroll-error2-solution}

Debe codificar el carácter especial o especificar el URL entre las comillas simples. Por ejemplo, `!` se convierte en `%21`, o el mandato se parece al siguiente:

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

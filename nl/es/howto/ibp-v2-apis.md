---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: APIs, build a network, authentication, service credentials, API key, API endpoint, IAM access token, Fabric CA client, import a network, generate certificates

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:faq: data-hd-content-type='faq'}
{:pre: .pre}
{:curl: #curl .ph data-hd-programlang='curl'}

# Creación de una red con API
{: #ibp-v2-apis}

{{site.data.keyword.blockchainfull}} Platform expone API RESTful para que pueda crear, importar, editar y suprimir componentes de blockchain, así como para gestionar el registro, las notificaciones y los valores de la consola. Puede utilizar las API, y los SDK correspondientes, para desarrollar aplicaciones que interactúan con la red blockchain.
{: shortdesc}

Esta guía de aprendizaje presenta el flujo genérico para crear una red blockchain con las API de
{{site.data.keyword.blockchainfull_notm}} Platform. Para obtener más información sobre cada API, consulte la
[Documentación de referencia de API de {{site.data.keyword.blockchainfull_notm}} Platform ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/apidocs/blockchain "Documentación de referencia de API de {{site.data.keyword.blockchainfull_notm}} "){: new_window}.

Estas API son compatibles únicamente con {{site.data.keyword.blockchainfull_notm}} Platform on
{{site.data.keyword.cloud_notm}} v0.1.77 o superior. Para comprobar la versión, vaya a
`https://[your_console_url]/version.txt`, donde
*`[your_console_url]`* es el URL de la consola de {{site.data.keyword.blockchainfull_notm}} Platform. Por ejemplo: https://1ee1869ffa6496d6bc1ce4b-optools.bp01.blockchain.cloud.ibm.com/version.txt
{:note}

## Antes de empezar
{: #ibp-v2-apis-prereq}

Debe tener una instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform en
{{site.data.keyword.cloud_notm}} para poder emitir llamadas de API para interactuar con la red. Si aún no tiene una instancia de servicio, siga las [Instrucciones de inicio](/docs/services/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks) para crear una.

## Autenticación
{: #ibp-v2-apis-authentication}

Para poder utilizar las API para acceder a la red blockchain que cree con
{{site.data.keyword.blockchainfull_notm}} Platform, la aplicación debe poder autenticarse con
{{site.data.keyword.cloud_notm}} y conectarse a su instancia de servicio.

### Recuperación de credenciales de servicio
{: #ibp-v2-apis-retrieve-service-credentials}

Necesita una credencial de autenticación básica para asegurarse de tener acceso a la instancia de servicio de
{{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}}.

1. En la lista de recursos de [{{site.data.keyword.cloud_notm}}
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/resources), abra la instancia de servicio de blockchain que haya creado.
2. Pulse **Credenciales de servicio** en el navegador izquierdo.
3. Pulse el botón "Nueva credencial" en la página **Credenciales de servicio** para crear una credencial nueva.
  1. Proporcione un nombre para la credencial, por ejemplo, *UseAPIs*.
  2. Puede dejar el campo "Añadir parámetro de configuración en línea" en blanco.
  3. Pulse el botón **Añadir**.
4. Una vez que se cree la nueva credencial, pulse **Ver credenciales** bajo la cabecera **ACTIONS** de esta credencial. El contenido de la credencial es similar al ejemplo siguiente:

  ```
  {
    "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com"
    "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
    "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
    "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
    "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
    "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
    "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
  }
  ```

En la credencial de servicio, puede buscar la **Clave de API** (`apikey`) y el
**Punto final de API** (`api_endpoint`) que necesite para recuperar una señal de acceso de {{site.data.keyword.iamshort}} (IAM).

### Recuperación de una señal de acceso
{: #ibp-v2-apis-retrieve-token}

Puede autenticarse con {{site.data.keyword.blockchainfull_notm}} Platform recuperando una señal de acceso de IAM. Con una señal de acceso, puede trabajar con la API de {{site.data.keyword.blockchainfull_notm}} Platform en nombre de su servicio o aplicación dentro o fuera de {{site.data.keyword.cloud_notm}}, sin necesidad de compartir sus credenciales de usuario personales.  

Llame a la API de {{site.data.keyword.iamshort}} para recuperar su señal de acceso.

```cURL
curl -X POST \
  "https://iam.cloud.ibm.com/identity/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Accept: application/json" \
  -d "grant_type=urn%3Aibm%3Aparams%3Aoauth%3Agrant-type%3Aapikey&apikey=<API_KEY>" \
```
{: codeblock}

En la solicitud, sustituya `<API_KEY>` por el valor de `apikey` de las credenciales de servicio. El ejemplo siguiente truncado muestra la salida de la señal:

```
{
"access_token": "eyJraWQiOiIyM...",
"refresh_token": "...",
"expires_in":3600,
"expiration":1555558683,
"scope":"ibm openid"}
```
{: screen}

Utilice el valor de `access_token` completo, con el tipo de señal de `Bearer` como prefijo, para trabajar mediante programación con la red blockchain utilizando las API.

Las señales de acceso son válidas durante una hora, pero puede volver a generarlas según sea necesario. Para mantener el acceso al servicio, renueve la señal de acceso de su clave de API de forma periódica llamando la API de {{site.data.keyword.iamshort}}.   
{: tip}

## Cómo crear una solicitud de API
{: #ibp-v2-apis-form-api-request}

Cuando haga una llamada de API al servicio, estructure la solicitud de API en función de cómo haya suministrado inicialmente su instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform.

Para crear la solicitud, empareje un punto final de API con las credenciales de autenticación adecuadas con el formato siguiente:

```cURL
curl -X <API method> \
    <API_endpoint>/<API> \
    -H 'authorization: Bearer <access_token>' \
    -H 'Content-Type: application/json' \
    -d '<request body>' \
```
{: codeblock}

Se proporcionan mandatos curl de ejemplo para cada API de la [Documentación de referencia de API de {{site.data.keyword.blockchainfull_notm}} Platform
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/apidocs/blockchain "Documentación de referencia de API de {{site.data.keyword.blockchainfull_notm}} ").

Además, puede utilizar la función **Pruébelo** de la documentación de referencia de API para probar las llamadas a la API antes de añadirlas a las aplicaciones. Necesita haber iniciado sesión en {{site.data.keyword.cloud_notm}} para poder utilizar la función
**Pruébelo**. Puede seleccionar cualquier instancia de servicio en la lista desplegable. Todas las solicitudes de API se envían a la red especificada en el punto final de API.

## Limitaciones
{: #ibp-v2-apis-limitations}

Puede importar únicamente nodos de CA, igual y clasificador existentes de otras redes de
{{site.data.keyword.blockchainfull_notm}} Platform on {{site.data.keyword.cloud_notm}}.

## Creación de una red utilizando las API
{: #ibp-v2-apis-build-with-apis}

Puede utilizar API para crear componentes de blockchain en su instancia de
{{site.data.keyword.blockchainfull_notm}} Platform. Utilice los pasos siguientes para crear una red blockchain utilizando las API de {{site.data.keyword.blockchainfull_notm}}.

1. Cree una entidad emisora de certificados (CA) llamando a [`POST /ak/api/v1/kubernetes/components/ca`](/apidocs/blockchain?code=try#create-a-ca).

  Recuerde su entrada y la respuesta, ya que las necesitará más adelante.
  {: tip}

  Debe esperar a la CA para empezar. Es posible que tarde varios minutos dependiendo del entorno. Puede llamar a la API `GET <ca_url>/cainfo` para comprobar el estado de la CA. Obtendrá errores repetidos y luego un código de estado
`200`, que implica que puede continuar con el paso siguiente. Tenga en cuenta que esta llamada de API superará el tiempo de espera después de un minuto.

2. Utilice la CA para registrar sus identidades de componente y administrador, y genere los certificados necesarios. Puede utilizar el cliente de CA de Fabric para completar los pasos siguientes:

  - [Configure el cliente de CA de Fabric](#ibp-v2-apis-config-fabric-ca-client).
  - [Genere certificados con el administrador de CA](#ibp-v2-apis-enroll-ca-admin).
  - [Registre el nuevo componente con la CA](#ibp-v2-apis-config-register-component).
  - También deberá [registrar un administrador de organización](#ibp-v2-apis-config-register-admin) y, a continuación,
[generar certificados para el administrador](#ibp-v2-apis-config-enroll-admin) dentro de una carpeta de MSP. No tiene que completar este paso si ya ha registrado la identidad de administrador.
  - [Registre el nuevo componente con la CA de TLS](#ibp-v2-apis-config-register-component-tls).

  También puede realizar estos pasos utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform. Para obtener más información, consulte [Creación y gestión de identidades](/docs/services/blockchain/howto/ibp-console-identities.html).

3. [Cree una definición de MSP para la organización](#ibp-v2-apis-msp) llamando a
[`POST /ak/api/v1/components/msp`](/apidocs/blockchain?#import-a-membership-service-provide-msp).

4. [Cree el archivo de configuración](#ibp-v2-apis-config) necesario para crear un clasificador o un igual. Debe crear un archivo de configuración exclusivo para cada clasificador o igual que desee crear.

5. Cree un clasificador llamando a [`POST /ak/api/v1/kubernetes/components/orderer`](/apidocs/blockchain?code=try#create-an-orderer).

6. Cree un igual llamando a [`POST /ak/api/v1/kubernetes/components/peer`](/apidocs/blockchain?code=try#create-a-peer).

7. Si desea utilizar la consola para trabajar con los componentes de blockchain, debe importar la identidad de administrador en la cartera de la consola. Utilice el separador de la cartera para importar el certificado y la clave privada del administrador del nodo en la consola y crear una identidad. A continuación, debe utilizar la consola para asociar esta identidad con los componentes que haya creado. Para obtener más información, consulte [Importación de una identidad de administrador en la consola
de {{site.data.keyword.blockchainfull_notm}} Platform](#ibp-v2-apis-admin-console).

8. Tras desplegar la red, puede utilizar los SDK de Fabric, la CLI del igual o la interfaz de usuario de la consola para crear canales e instalar o crear instancias de contratos inteligentes.

La credencial de servicio utilizada para la autenticación de API debe tener el rol `Gestor` en IAM para poder crear componentes. Consulte la tabla de este tema sobre [roles de usuario](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) para obtener más información.
{: note}

## Importar una red utilizando las API
{: #ibp-v2-apis-import-with-apis}

También puede utilizar las API para importar componentes de {{site.data.keyword.blockchainfull_notm}} creados mediante las API o la consola de {{site.data.keyword.blockchainfull_notm}} Platform en otra instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Importe una CA llamando a [`POST /ak/api/v1/components/ca`](/apidocs/blockchain?code=try#import-a-ca).

  Recuerde su entrada y la respuesta, ya que las necesitará más adelante.
  {: tip}

  Debe esperar a la CA para empezar. Es posible que tarde varios minutos dependiendo del entorno. Puede llamar a
[`GET /components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) para comprobar el estado de la CA. Obtendrá errores repetidos antes de obtener un código de estado `200` para poder continuar con el paso siguiente. Tenga en cuenta que esta llamada de API superará el tiempo de espera después de un minuto.

2. Importe una definición de MSP de organización llamando a [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp).

3. Importe un clasificador llamando a [`POST /ak/api/v1/components/orderer`](/apidocs/blockchain?code=try#import-a-orderer).

4. Importe un igual llamando a [`POST /ak/api/v1/components/peer`](/apidocs/blockchain?code=try#import-a-peer).

5. Si tiene pensado utilizar la consola de {{site.data.keyword.blockchainfull_notm}} Platform para trabajar con los componentes de blockchain, debe importar las identidades de administrador de componentes en la cartera de la consola. Para obtener más información, consulte [Importación de una identidad de administrador en la consola
de {{site.data.keyword.blockchainfull_notm}} Platform](#ibp-v2-apis-admin-console).

6. Tras desplegar la red, puede utilizar los SDK de Fabric, la CLI del igual o la interfaz de usuario de la consola para crear canales e instalar o crear instancias de contratos inteligentes.

La credencial de servicio utilizada para la autenticación de API debe tener el rol `Escritor` en IAM para poder importar componentes. Consulte la tabla de este tema sobre [roles de usuario](/docs/services/blockchain/howto?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) para obtener más información.
{: note}

## Cómo trabajar con la CA utilizando el cliente de CA de Fabric
{: #ibp-v2-apis-config-fabric-ca-client}

Puede utilizar el cliente de CA de Fabric para trabajar con las CA. Ejecute los mandatos del cliente de CA de Fabric siguientes para registrar sus identidades de componentes y de administrador y generar los certificados necesarios.

### Configurar el cliente de CA de Fabric
{: #ibp-v2-apis-setup-fabric-ca-client}

1. Descargue el [cliente de CA de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#fabric-ca-client "Descargar el cliente de CA de Fabric") en el sistema de archivos local.

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

5. Recupere el certificado TLS de la CA que va a utilizar el cliente de CA de Fabric. Si utiliza la consola de
{{site.data.keyword.blockchainfull_notm}} Platform, abra la CA y pulse **Valores**, y busque el certificado en formato
base64 en el campo **Certificado TLS**. Si utiliza las API, puede llamar a
[`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) y encontrar el certificado TLS de CA en el campo `"PEM"`. Si ha creado la CA utilizando la API `Create a Fabric CA`, también puede encontrar el certificado TLS en el cuerpo de la respuesta.

  Necesitará convertir el certificado de base64 a formato PEM para utilizarlo para comunicarse con la CA. Inserte la serie codificada en base64 del certificado TLS en el mandato siguiente. Asegúrese de que se encuentra en el directorio `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > tls.pem
  ```
  {:codeblock}

### Generar certificados con el administrador de CA
{: #ibp-v2-apis-enroll-ca-admin}

Al crear la CA, se registró automáticamente una identidad de **administrador de CA**. Ahora puede utilizar el nombre y contraseña de dicho administrador para emitir un mandato
`enroll` con el cliente de CA de Fabric para generar una carpeta de MSP con certificados que se podrán utilizar luego para registrar otras identidades de igual o de clasificador.

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

  `<enroll_id>` y `<enroll_password>` en el mandato son el `enroll_id` y el
`enroll_secret` que ha especificado al crear la CA. Utilice el **URL de punto final de entidad emisora de certificados** de la consola de {{site.data.keyword.blockchainfull_notm}} Platform o el `"ca_url"` devuelto por la llamada de API como valor para `<ca_url_with_port>`. Excluya la parte de `http://` al principio. `<ca_name>` es el **Nombre de CA** de la consola, o el `"ca_name"` devuelto por las API.

  `<ca_tls_cert_path>` es la vía de acceso completa del certificado TLS de CA.

  Una llamada real es similar al siguiente mandato de ejemplo:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```  
  {:codeblock}

**Sugerencia:** si el valor del URL de inscripción, que es el valor del parámetro `-u`, contiene un carácter especial, debe codificar el carácter especial o especificar el URL entre las comillas simples. Por ejemplo, `!` se convierte en `%21`, o el mandato se parece al siguiente:

  ```
  ./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname ca
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

### Registro de la identidad de componente con la CA
{: #ibp-v2-apis-config-register-component}

En primer lugar, debe registrar una identidad de componente con la CA. El componente utilizará esta identidad para generar los certificados que necesite cuando se despliegue.

1. [Genere certificados con el administrador de CA](#ibp-v2-apis-enroll-ca-admin) utilizando el cliente de CA de Fabric. Utilice estos certificados de administrador para emitir los mandatos siguientes. Asegúrese de que `$FABRIC_CA_CLIENT_HOME` esté establecido en
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
  fabric-ca-client affiliation list --caname ca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
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

  Cree un nombre y una contraseña para el componente y utilícelos para sustituir los valores de `name` y `secret`.  Anote esta información. Establezca `--id.type` en `orderer` si va a desplegar un clasificador, o establézcalo en `peer` si va a desplegar un igual. El mandato puede tener un aspecto similar al del ejemplo siguiente:

  ```
  fabric-ca-client register --caname ca --id.affiliation org1.department1 --id.name peer1 --id.secret peer1pw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Necesita guardar los valores de `"enrollid"` y `"enrollsecret"` del mandato anterior para cuando cree el archivo de configuración.
  {: important}

  Solo puede registrar una identidad una vez. Si tiene algún problema, intente un mandato con un nuevo nombre de usuario y contraseña. Como medida de seguridad, utilice cada identidad, y el ID de inscripción y el secreto que la acompañan, para desplegar únicamente un igual. Aunque puede utilizar una identidad de **administrador** para varios componentes (esta es la estrategia de despliegue recomendada), no reutilice los ID ni las contraseñas de igual.

  Cuando el mandato finalice correctamente, debería ver información parecida a la del siguiente ejemplo:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

### Registro del administrador de organización
{: #ibp-v2-apis-config-register-admin}

También debe crear una identidad de administrador que pueda utilizar para trabajar con la red. Utilizará esta identidad para trabajar con componentes específicos, como para la instalación de un contrato inteligente en el igual. También puede utilizar esta identidad como administrador de la organización y utilizarla para crear y editar canales.  

Necesita registrar esta nueva identidad con la CA y utilizarla para generar una carpeta de MSP. Puede convertir esta identidad en un administrador de organización añadiendo su signCert al MSP de organización. También deberá añadir el signCert al archivo de configuración para que pueda pasar a ser el certificado de administrador del clasificador o el igual durante el despliegue. Solo necesita crear una identidad de administrador para su organización. Como resultado, debe realizar estos pasos solo una vez. Puede utilizar el signCert que ha generado para desplegar muchos iguales o clasificadores.

Asegúrese de que `$FABRIC_CA_CLIENT_HOME` esté establecido en la vía de acceso al MSP del administrador de CA.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registre la identidad de administrador de componentes con la CA ejecutando el mandato siguiente:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type client --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Cree nuevos valores de `name` y `secret` de identidad de usuario para el administrador. Asegúrese de utilizar valores distintos a los de la identidad del igual o el clasificador que acaba de registrar. El mandato tiene un aspecto similar al del ejemplo siguiente:

```
fabric-ca-client register --caname ca --id.name peeradmin --id.affiliation org1.department1 --id.type client --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Anote esta información. Utilizará estos valores de `name` y
`secret` para generar la carpeta de MSP utilizando el mandato `enroll`.
{: important}

### Generación de la carpeta de MSP (proveedor de servicios de pertenencia) de administrador
{: #ibp-v2-apis-config-enroll-admin}

Tras registrar el administrador de componentes, puede generar certificados ejecutando el mandato siguiente:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Por ejemplo:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname ca -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```
{:codeblock}

Una vez que este mandato haya finalizado correctamente, generará una nueva carpeta de MSP en el directorio que ha especificado utilizando el distintivo `-M`. Este directorio contiene los certificados que deberá utilizar para trabajar con los componentes. Puede verificar que el mandato enroll ha funcionado utilizando un mandato tree. Vaya al directorio donde ha almacenado los certificados. Un mandato tree debe generar un resultado similar a la estructura siguiente:


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

Deberá volver a esta carpeta cuando cree los archivos de configuración y de definición del MSP de organización.
{: important}

### Registro de la identidad de componente con la CA de TLS
{: #ibp-v2-apis-config-register-component-tls}

Cuando haya creado la CA, se habrá desplegado una CA de TLS junto con la CA predeterminada. También debe registrar el clasificador o el igual con la CA de TLS. Para ello, primero deberá inscribirse utilizando el administrador de la CA de TLS. Cambie `$FABRIC_CA_CLIENT_HOME` al directorio en el que desee almacenar los certificados de administrador de CA de TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Ejecute el mandato siguiente para inscribirse como administrador en la CA de TLS. El ID de inscripción y la contraseña del administrador de la CA de TLS son los mismos que los de la CA predeterminada. Como resultado, el mandato siguiente es el mismo que el que ha utilizado para la inscripción como
[administrador de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-ca-admin), pero con el nombre de la CA de TLS. El nombre de la CA de TLS es el valor de **Nombre de CA de TLS** del panel **Valores** de la CA de la consola, o el valor de `"tlsca_name"` devuelto por la API `Create a CA`.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Una llamada real será similar al ejemplo siguiente:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
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

Necesita guardar los valores de `"enrollid"` y `"enrollsecret"` del mandato anterior para cuando cree el archivo de configuración.
{: important}

## Creación de una definición de MSP de organización
{: #ibp-v2-apis-msp}

Puede utilizar las API para crear una definición de MSP de organización llamando a [`POST /ak/api/v1/components/msp`](/apidocs/blockchain?code=try#import-an-msp). Este MSP contiene certificados que definen su organización en un consorcio de blockchain, así como los certificados de administrador que puede utilizar para trabajar con la red. Si ha seguido el paso anterior, ya habrá generado los certificados necesarios para crear un MSP de organización. Utilice los pasos siguientes para completar el cuerpo de solicitud de la llamada de API.

1. Especifique `msp` como tipo (`type`) de solicitud.

2. Seleccione un ID de MSP para la organización. El ID de MSP es el nombre formal de su organización dentro del consorcio. El ID de MSP utilizado para crear el MSP de organización debe ser el mismo que el utilizado para desplegar los iguales.

3. Seleccione un nombre de visualización para el MSP.

4. Debe proporcionar el signCert, en formato base64, del administrador de la organización que ha registrado e inscrito utilizando el cliente de CA de Fabric.  

  Vaya al directorio de MSP que se ha creado al [generar certificados utilizando el administrador de la organización](#ibp-v2-apis-config-enroll-admin).
  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
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
  Pero no a:

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

  Proporcione esta serie en el campo `admins` de la solicitud de API.

5. También deberá proporcionar el certificado raíz de la CA. Este certificado se ha creado al
[generar certificados utilizando el administrador de CA](#ibp-v2-apis-enroll-ca-admin).

  Vaya al directorio de MSP del administrador de CA.
  ```
  cd $HOME/fabric-ca-client/ca-admin/msp
  ```
  En este directorio, abra la carpeta cacerts y convierta el certificado que incluye en formato base64 utilizando los mandatos siguientes:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-ca-admin>/msp/cacerts/<ca_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  Esto imprimirá el certificado raíz como una serie codificada en base64.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGekNDQWIyZ0F3SUJBZ0lVQmZnZzcvVnIrL25OVEFNQlQ4UUtHL00wQU8wd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU1TURVd016RXpNamt3TUZvWERUTTBNRFF5T1RFek1qa3dNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUVXMUtvN2lWeVE2VWkwdDVqbU5KaWVuSUwKR3pNM1BDWHlhL2VSQ0NWMmFQb0dTZ1lrVUg2UWN5RjAzbFlMZFU4Y0drNTQ0alViVC9KT1lYeVgzTWc4bHFORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZDK2lJR0NSb2Zvb3FsVkZoU3dOMmk2MXNJaVBNQW9HQ0NxR1NNNDlCQU1DQTBnQU1FVUNJUURTYW9RL1E0QzkKbFl1VGNhVXVHb3d6YmhUZHBuN2F3S2lHN1Nvd2lSQXVld0lnUWlyM3RNR3IvYWo2aU5lRXJFN2NyOVowQ0gvTwp3QnNQcWd4RVR3MjVqZUU9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Proporcione esta serie en el campo `root_certs` de la solicitud de API.

6. También debe proporcionar el certificado raíz de la CA de TLS. El certificado raíz de TLS permite que los iguales puedan participar en los rumores (gossip) de un canal.

  Vaya al directorio de MSP que se ha generado al [inscribir el administrador de la CA de TLS](#ibp-v2-apis-config-register-component-tls).
  ```
  cd $HOME/fabric-ca-client/tlsca-admin/msp
  ```
  En este directorio, abra la carpeta cacerts y convierta el certificado que incluye en formato base64 utilizando los mandatos siguientes:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/<path-to-tlsca-admin>/msp/cacerts/<tls_root_cert>.pem | base64 $FLAG
  ```
  {:codeblock}

  Esto imprimirá el certificado raíz de CA de TLS como una serie codificada en base64.

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVWUVQWnprNXV2b3dobEtacG5JMXplODdIQUlnd0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWURWUVFERXdWMGJITmpZVEFlCkZ3MHhPVEExTURNeE16STVNREJhRncwek5EQTBNamt4TXpJNU1EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdzVPYjNKMGFDQkRZWEp2YkdsdVlURVVNQklHQTFVRUNoTUxTSGx3WlhKc1pXUm5aWEl4RHpBTgpCZ05WQkFzVEJrWmhZbkpwWXpFT01Bd0dBMVVFQXhNRmRHeHpZMkV3V1RBVEJnY3Foa2pPUFFJQkJnZ3Foa2pPClBRTUJCd05DQUFRdSs2UnZWd2w5T2dDVlAraEVxbjVxdExRVG9LWkw4a1lic0pOeU1JbERoc3hlNWx6cW1zQkoKbTk2eUR2TVV6OSsxL2pzb1M4M1JqMVAwc3M2TnJNb3FvMXd3V2pBT0JnTlZIUThCQWY4RUJBTUNBUVl3RWdZRApWUjBUQVFIL0JBZ3dCZ0VCL3dJQkFUQWRCZ05WSFE0RUZnUVVnUEc4anJEK1BxVjdoelc3WDlsbTFrMS91WjR3CkZRWURWUjBSQkE0d0RJY0VxVGJESW9jRUNwbnBkVEFLQmdncWhrak9QUVFEQWdOSUFEQkZBaUVBenk3cHJZaVMKQmlDVWdYeWRkY09WMm9mZmtqaEI0N091QXFjQWNqZS9SWkVDSUdKZFgzZ1ErTDRIN3duY1RoZkwrenU1ejV1UApGUWhXTmlNS3hQWEYrZnYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  ```

  Proporcione esta serie en el campo `tls_root_certs` de la solicitud de API.

## Creación de un archivo de configuración
{: #ibp-v2-apis-config}

Debe completar un archivo de configuración para poder crear un igual o un clasificador utilizando las API. Este archivo se proporciona a las API como el objeto `config` del cuerpo de solicitud de la llamada de API. Debe desplegar una CA en la instancia de servicio de
{{site.data.keyword.cloud_notm}} Platform y seguir los pasos para registrar e inscribir las identidades necesarias antes de completar el archivo.

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

Copie este archivo completo en un editor de texto, donde puede editarlo y guardarlo en el sistema de archivos local como archivo JSON. Utilice los pasos siguientes para completar este archivo de configuración y utilizarlo para desplegar un clasificador o igual.

### Recuperar la información de conexión de CA
{: #ibp-v2-apis-config-connx-info}

En primer lugar, es necesario proporcionar la información de conexión de la CA en {{site.data.keyword.cloud_notm}} Platform. Puede utilizar la interfaz de usuario de la consola o las API para obtener la información necesaria sobre la CA.

**Si utiliza la consola de {{site.data.keyword.blockchainfull_notm}} Platform:**
abra la CA en la consola, pulse **Valores** y, a continuación, pulse el botón **Exportar** para exportar la información de la CA en un archivo JSON. Puede utilizar los valores de este archivo para completar el archivo de configuración.

**Si utiliza las API:**
puede llamar a [`GET /ak/api/v1/components`](https://test.cloud.ibm.com/apidocs/blockchain?code=try#get-all-components) para obtener la información de conexión de la CA. Si ha creado la CA utilizando la API
`Create a Fabric CA`, también podrá encontrar la información necesaria en el cuerpo de la respuesta.

- Los valores de `"cahost"` y `"caport"` son visibles en el campo `ca_url` del cuerpo de respuesta o el archivo JSON de CA que ha exportado.  Por ejemplo, si el `ca_url` es https://9.30.94.174:30167, el valor de
`"cahost"` sería `9.30.94.174` y el valor de `"caport"` sería `30167`.
- `"caname"` es el nombre de la CA que se ha especificado al desplegar la CA. Este es el valor del campo
`ca_name` en el cuerpo de respuesta o el archivo JSON exportado.
- `"cacert"` es el certificado TLS codificado en base64 de la CA. Este es el valor del campo
`pem` en el cuerpo de respuesta o el archivo JSON exportado.

- Los campos de la sección `"tls"` siguiente necesitan la misma información que la que ha pasado a las secciones "component" anteriores, excepto que debe utilizar el valor del nombre de instancia de TLS de CA que se ha especificado durante el despliegue de la CA. Sustituya
`caname` por el valor de `tlsca_name` del cuerpo de respuesta o el archivo JSON exportado. Utilice el mismo certificado TLS para el valor de `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

### Proporcionar el ID de inscripción y el secreto del componente

1. Pegue los valores de `name` y `secret` que haya utilizado para
[registrar el componente con la CA predeterminada](#ibp-v2-apis-config-register-component) como valores de
`"enrollid"` y `"enrollsecret"` en el archivo de configuración, en la sección
`"component"`:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peer1pw",
  ```
2. Pegue los valores de `name` y `secret` que haya utilizado para
[registrar el componente con la CA de TLS](#ibp-v2-apis-config-register-component-tls) como valores de
`"enrollid"` y `"enrollsecret"` en el archivo de configuración, en la sección `"tls"`:

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

### Proporcionar el signCert del administrador de organización

Vaya al directorio de MSP que se ha creado al [generar certificados utilizando el administrador de la organización](#ibp-v2-apis-config-enroll-admin).
```
cd $HOME/fabric-ca-client/peer-admin/msp
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
Pero no a:

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

### Hosts de CSR (Solicitud de firma de certificado)

Tiene la opción de proporcionar un dominio personalizado para el componente utilizando la sección
`"csr"` del archivo de componentes.

```
"csr": {
  "hosts": [""]
  }
```
{:codeblock}

Esta sección se proporciona para que los usuarios avanzados puedan especificar un nombre de host personalizado que se puede utilizar para direccionar el punto final del igual. La mayoría de los usuarios pueden dejar esta sección en blanco.

### Cómo completar el archivo de configuración
{: #ibp-v2-apis-config-file}

Una vez que haya completado todos los pasos anteriores, el archivo de configuración actualizado puede tener un aspecto similar al del ejemplo siguiente:


```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "ca",
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
                ]
            }
        }
    }
}
```
{:codeblock}

Puede dejar los demás campos en
blanco. Tras completar este archivo, puede pasar este archivo como campo `config` en el cuerpo de respuesta de la API
`Create an orderer` o de la API `Create a peer`.

### Importación de una identidad de administrador en la consola de {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-v2-apis-admin-console}

Si desea utilizar la consola de {{site.data.keyword.blockchainfull_notm}} Platform para trabajar con los componentes de blockchain, debe importar la identidad de administrador en la cartera de la consola. Abra el panel de cartera de la consola. Pulse el botón **Añadir identidad** de la pantalla de visión general. Al pulsar este botón, se abrirá un panel lateral que le permite añadir el certificado para firmas y la clave privada de una identidad directamente a la consola.
- **Nombre:** especifique un nombre de visualización para la identidad utilizada solo como referencia.
- **Certificado:** cargue el certificado para firmas del administrador. Si ha seguido las instrucciones anteriores, puede encontrar esta clave en la carpeta `$HOME/fabric-ca-client/peer-admin/msp/signcerts/`.
- **Clave privada:** cargue la clave privada del administrador. Si ha seguido las instrucciones anteriores, puede encontrar esta clave en la carpeta `$HOME/fabric-ca-client/peer-admin/msp/keystore/`.

Tras importar la identidad de administrador, puede asociar dicha identidad a los componentes que haya creado. A continuación, puede utilizar la consola para trabajar con la red.

---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Gestión de certificados en la plataforma {{site.data.keyword.blockchainfull_notm}}
{: #certificates}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


La plataforma {{site.data.keyword.blockchainfull}} se basa en Hyperledger Fabric y crea redes blockchain con permisos. Los participantes reciben el nombre de miembros de la red, y sus actividades y su acceso a los recursos de la red se verifican continuamente. La identidad de un participante se proporciona en forma de un certificado digital x509 de confianza. La verificación se proporciona mediante una infraestructura de claves públicas subyacente y operaciones de firma/verificación que protegen las transacciones y la gestión dentro de la red.
{:shortdesc}

La plataforma {{site.data.keyword.blockchainfull_notm}} gestiona la mayoría de las operaciones de certificado sin que los usuarios tengan que manejar sus certificados. Sin embargo, hay ocasiones en las que tendrá que gestionar los certificados que le permiten comunicarse con la red, como el desarrollo de aplicaciones o la ampliación de la red con un igual remoto. A continuación encontrará una breve guía sobre la entidad emisora de certificados y la infraestructura de certificados subyacente. Esta guía de aprendizaje puede ayudarle a comprender los certificados con los que va a trabajar y las tareas que debe realizar.

## Entidades emisoras de certificados
{: #network-ca}

Las entidades emisoras de certificados (CA) proporcionan la identidad en la red. Una CA se puede considerar un notario público de confianza que actúa como punto de confianza entre las múltiples partes. A todas las entidades de la red se les proporciona un certificado firmado por una CA raíz que encapsula su identidad digital. Este certificado es la raíz de la confianza para todas las operaciones de firma y verificación que se realizan en la red. Para obtener más información sobre el modo en que se utilizan las entidades emisoras de certificados para establecer identidades, consulte la [documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Cada miembro de la red posee su propia CA. La CA de su organización firma solicitudes para todas las entidades y componentes que posee, como el administrador, los iguales o las aplicaciones. Si desea añadir un igual remoto o una nueva aplicación a la red, debe registrar la nueva identidad con la entidad emisora de certificados (registro). A continuación, la CA puede proporcionar a la nueva entidad los certificados que necesitará para interactuar con la red (inscripción).

### Registro con el supervisor de red
{: #ca-panel}

Puede utilizar el supervisor de red de la plataforma {{site.data.keyword.blockchainfull_notm}} para ver las identidades registradas con la CA y añadir otras nuevas. Vaya al panel "Entidad emisora de certificados" del supervisor de red. Este panel muestra todas las identidades que se han registrado con la CA, incluidos el administrador de la organización, los iguales y las aplicaciones cliente. Para registrar una nueva identidad en la organización, pulse el botón **Añadir usuario** del panel. Se abre una ventana emergente y se muestran los campos siguientes que son necesarios para registrar una nueva identidad.
  - **ID de inscripción:** el nombre de la nueva identidad, a la que a veces se denomina `ID de inscripción`. **Guarde este valor** para cuando configure un igual remoto o inscriba una nueva aplicación.
  - **Secreto de inscripción:** la contraseña de la identidad, que a veces recibe el nombre de `Secreto de inscripción`. **Guarde este valor** para cuando configure un igual remoto o inscriba una nueva aplicación.
  - **Tipo:** seleccione el tipo de identidad que desea registrar, que puede ser una aplicación igual o de cliente.
  - **Afiliación** la afiliación dentro de la organización, como por ejemplo `org1`, a la que pertenece la identidad.
  - **Número máximo de inscripciones:** puede utilizar este campo para limitar el número de veces que puede inscribir o generar certificados utilizando esta identidad. Si deja el campo en blanco, el valor predeterminado es un número ilimitado de inscripciones.

Puede utilizar este panel para registrar una nueva identidad de igual si está desplegando un [igual remoto](/docs/services/blockchain/howto/remote_peer.html). Como alternativa, puede registrar un cliente si está desarrollando una aplicación que puede enviar transacciones a la red. Visite la [guía de aprendizaje de desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) para obtener más información sobre el uso de los SDK de Fabric con la plataforma.

### Generación de certificados del lado del cliente (inscripción)
{: #enrollment}
Para poder conectar un cliente de terceros a la plataforma {{site.data.keyword.blockchainfull_notm}}, es necesario que se autentique. El proceso de generación de los certificados necesarios, la clave privada y el certificado público (también conocido como certificado de inscripción o signCert), se denomina inscripción. Estos certificados se necesitarán siempre que el cliente se comunique con la red. Cualquier cliente que envíe llamadas a la red tendrá que firmar cargas útiles utilizando una clave privada y adjuntar un certificado de x509 correctamente firmado.

Visite la [guía de aprendizaje de desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) para aprender a [inscribir mediante el SDK de nodo de Fabric](/docs/services/blockchain/v10_application.html#enroll-app). La inscripción con el SDK genera 3 elementos independientes: una clave privada, un signCert y una clave pública que se ha utilizado para crear el signCert.

También puede generar certificados desde la línea de mandatos utilizando el [cliente de CA de Fabric](#enroll-register-caclient). El cliente de CA de Fabric devuelve un conjunto más completo de certificados dentro de una carpeta de Proveedor de servicios de pertenencia (MSP). Esta carpeta contiene el certificado raíz que ha firmado la CA, certificados intermedios, una clave privada y su signCert. Para obtener más información sobre los MSP y el contenido de la carpeta de MSP, consulte
[Proveedores de servicios de pertenencia](#msp).

Solo puede generar certificados utilizando identidades que se hayan registrado con la entidad emisora de certificados, utilizando el nombre y el secreto de dicha identidad. De forma predeterminada, ya se ha registrado una identidad **admin** con la CA, que aparecerá en la pantalla "Entidad emisora de certificados". Puede encontrar el secreto de la identidad de administrador (admin) en el perfil de conexión pulsando el botón **Perfil de conexión** de la pantalla "Visión general" del supervisor de red. También puede registrar una nueva identidad pulsando el botón [Añadir usuario](#ca-panel) de la pantalla "Entidad emisora de certificados" del supervisor de red y, a continuación, generar certificados con el nombre y el secreto de la nueva identidad.

**Nota:** si sigue las instrucciones para generar certificados utilizando el SDK de Node de Fabric o el cliente de CA de Fabric indicadas anteriormente, comience realizando la inscripción con la identidad de administrador. A continuación, utilice estos certificados para registrar una nueva identidad de cliente con la CA. Si utiliza las instrucciones del SDK en [Desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html), volverá a realizar la inscripción utilizando la identidad de cliente. A continuación, utilizará estos certificados para enviar transacciones a la red.
<!---You can an illustration of how the developing applications tutorial interacts with your organization CA in the diagram below.--->

### Generación de certificados mediante el supervisor de red
{: #certs-panel}

Puede utilizar el supervisor de red para generar certificados utilizando la identidad de administrador y, a continuación, pasar dichos certificados directamente al SDK. Pulse el botón **Generar certificado** situado junto a la identidad de administrador para obtener un nuevo signCert y una clave privada de la entidad emisora de certificados. El campo
**Certificado** contiene el signCert, justo encima de la **Clave privada**. Puede pulsar el icono de copia que hay al final de cada campo para copiar el valor. A continuación, debe guardar estos certificados en un lugar desde el que pueda importarlos luego en la aplicación. Para obtener más información, consulte la [guía de aprendizaje de desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html#enroll-panel). **Tenga en cuenta** que la plataforma {{site.data.keyword.blockchainfull_notm}} no almacena estos certificados. Tiene que guardarlos y almacenarlos de forma segura.

### Carga de certificados de firma en la plataforma {{site.data.keyword.blockchainfull_notm}}
{: #upload-certs}

Una aplicación solo requiere un signCert válido para enviar transacciones a la red. Sin embargo, si un cliente desea trabajar en la red, por ejemplo instalando código de encadenamiento en iguales o uniendo iguales a canales, debe ser reconocido como administrador. Cada componente reconoce un conjunto de signCerts que son propiedad de un administrador. Si necesita trabajar en la red desde un cliente, tendrá que cargar su signCert y añadirlo a la lista de certificados de administración. Puede hacerlo en la plataforma cargando su signCert en el separador **Certificados** del [panel "Visión general"](/docs/services/blockchain/v10_dashboard.html#members) del supervisor de red. Sincronice este certificado con sus iguales con el botón de reinicio que aparece cuando finaliza la carga. Tras ello, el cliente podrá trabajar con la red. También puede cargar el signCert utilizando la
[API de Swagger](/docs/services/blockchain/howto/swagger_apis.html) para añadir un certificado de administrador.

Los canales también reconocen un conjunto de certificados de administrador de las identidades que tienen permitido operar con el canal, incluyendo la capacidad de crear una instancia de código de encadenamiento en el canal. Si utiliza un nuevo signCert con un cliente remoto, debe sincronizar el certificado con el canal para poder crear una instancia del código de encadenamiento. Realice los pasos siguientes en el supervisor de red para añadir el certificado al canal:

1. Vaya al separador **Certificados** de la pantalla "Miembros". Pulse el botón **Añadir certificado** para cargar el signCert en la plataforma.
2. Vaya a la pantalla "Canales" y busque el nombre del canal apropiado.
3. Pulse **Sincronizar certificado** en la lista desplegable bajo la cabecera **Acciones** para añadir el nuevo certificado a la lista de certificados de administrador del canal.

**Nota:** los certificados que se generan utilizando la identidad de administrador de la organización no se consideran certificados de administrador de forma automática. Un signCert y una clave privada generados mediante el uso del botón
**Generar certificados** no permitirán que el SDK pueda trabajar con la red. Se han generado mediante el cliente de CA de Fabric, el SDK o el supervisor de red, y no tienen conexión con la lista preexistente de certificados de administrador que reconocen los componentes de red. Debe cargar el signCert en el supervisor de red para que el SDK pueda trabajar con la red.

## Utilización de certificados TLS
{: #tls}

[Transport Layer Security ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) está incorporado en el modelo de confianza de Hyperledger Fabric. Todos los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizan TLS para autenticarse y comunicarse entre sí. Por lo tanto, debe adjuntar un certificado TLS emitido por la plataforma a sus llamadas para poder validar y cifrar la comunicación. Los otros certificados que se explican en esta guía de aprendizaje protegen su capacidad de realizar transacciones y de gestionar la red. Los certificados TLS se utilizan para proteger las llamadas a la red.

Los certificados TLS los emite públicamente la plataforma y son los mismos para todos los componentes de la red. Puede descargar los certificados TLS con los enlaces siguientes, en función del plan de pertenencia y de la ubicación de la nube. También encontrará los certificados TLS en su [perfil de credenciales](/docs/services/blockchain/v10_dashboard.html#connection-profile "perfil de credenciales"). Este certificado puede residir en cualquier lugar, siempre que se pueda hacer referencia al mismo desde la aplicación o línea de mandatos.

- Certificado TLS raíz para el Plan inicial
  - EEUU: [us01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - Reino Unido: [uk01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sídney: [aus01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
- [Certificado TLS raíz para el Plan empresarial ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Todas las redes de la plataforma {{site.data.keyword.blockchainfull_notm}} utilizan TLS del lado del servidor, en el que la red debe autenticar los clientes. Las redes del plan empresarial también pueden habilitar el TLS mutuo, en el que el cliente y el servidor se autentican entre sí, para garantizar la seguridad de las aplicaciones. Los certificados TLS del lado del cliente (para TLS mutuo) los emite la entidad emisora de certificados del cliente y son exclusivos de la red. Si utiliza una red del plan empresarial, se recomienda habilitar TLS mutuo. Para obtener más información sobre TLS mutuo, consulte estas [instrucciones](/docs/services/blockchain/v10_dashboard.html#mutual-tls "instrucciones sobre TLS mutuo").

### Recuperación del nombre de dominio de los certificados TLS

Cuando se comunique con un componente de Hyperledger Fabric que se encuentre fuera de la plataforma
{{site.data.keyword.blockchainfull_notm}}, la llamada debe enviarse utilizando el nombre de dominio correcto. Si se envía una llamada a la dirección IP de un componente sin que se resuelva al nombre de dominio del componente, se rechazará la llamada y se devolverá un error.

Puede encontrar el URL completo y el nombre de dominio de los componentes alojados en la plataforma en el perfil de conexión. El ejemplo siguiente muestra el URL completo de un igual, que se concatena con el nombre de dominio del igual, `us01.blockchain.ibm.com`.
```
grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us01.blockchain.ibm.com:31002"
```

También puede encontrar el nombre de dominio de un componente a partir de su certificado TLS.

1. Descargue uno de los certificados TLS raíz de la lista anterior y guárdelo en la máquina local.
2. En el mismo directorio que los certificados TLS raíz, ejecute el mandato siguiente. Este mandato puede mostrar el certificado en un formato legible en la línea de mandatos. A continuación, puede encontrar información importante, como el nombre de dominio, en la línea de mandatos.
  ```
  openssl x509 -in <certificate file> -text
  ```
  {:codeblock}

Por ejemplo, si descarga el primer certificado de la lista y emite el mandato siguiente, `openssl x509 -in us01.blockchain.ibm.com.cert -text`, puede ver el código siguiente como parte de la salida.

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            05:5c:42:fb:90:b9:cb:3d:60:7c:e4:ec:7f:7f:8e:38
    Signature Algorithm: ecdsa-with-SHA256
        Issuer: C=US, O=DigiCert Inc, CN=DigiCert ECC Secure Server CA
        Validity
            Not Before: Jun 11 00:00:00 2018 GMT
            Not After : Jun 19 12:00:00 2019 GMT
        Subject: C=US, ST=New York, L=Armonk, O=International Business Machines Corporation, CN=*.us01.blockchain.ibm.com
    ...
    ...
```

El nombre de dominio aparece en la línea de asunto (Subject) como `CN=*.us01.blockchain.ibm.com`. También puede encontrar nombres de dominio alternativos que aparecen más abajo en el certificado.

La recuperación del nombre de dominio de un componente a partir de los certificados TLS puede resultar útil si se comunica con un igual remoto o un componente de Fabric que esté alojado fuera de la plataforma {{site.data.keyword.blockchainfull_notm}}. A continuación, puede añadir el nombre de dominio a una alteración temporal de SSL cuando utilice los SDK, o añadir el nombre de dominio y la dirección IP correspondiente al archivo `etc.hosts`.

## Proveedores de servicios de pertenencia (MSP)
{: #msp}

Los componentes de la plataforma {{site.data.keyword.blockchainfull_notm}} consumen identidades a través de proveedores de servicios de pertenencia (MSP). Los MSP asocian los certificados que emiten las entidades emisoras de certificados con roles de red y de canal. Para obtener más información sobre los MSP, consulte el [tema sobre el concepto de pertenencia en la documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "tema sobre concepto pertenencia en la documentación de Hyperledger Fabric").

Las carpetas de MSP de Fabric tienen una estructura definida. Cuando realiza la inscripción mediante el cliente de CA de Fabric, el cliente almacena los certificados en una carpeta de MSP en el sistema de archivos local con las subcarpetas siguientes:

- **cacerts:** esta carpeta contiene el certificado raíz de la CA raíz de la red.
- **intermediatecerts:** son los certificados de las CA intermedias de la red. Estas CA intermedias están enlazadas con la CA raíz y forman una cadena de confianza. Cada organización de plan empresarial tiene dos CA intermedias para la migración tras error y la alta disponibilidad.
- **signcerts:** esta carpeta contiene el certificado de firma público, que también se denomina signCert o certificado de inscripción. Este certificado se adjunta a las llamadas a la red (por ejemplo, una invocación de código de encadenamiento) cuando se hace referencia al directorio MSP desde la línea de mandatos o se crea un objeto de contexto de usuario con los SDK. Puede cargar este certificado en la plataforma si desea trabajar con una red desde el SDK o la línea de mandatos.
- **keystore:** esta carpeta contiene la clave privada. Esta clave se utiliza para firmar las llamadas a la red cuando se hace referencia al directorio MSP desde la línea de mandatos o se crea un objeto de contexto de usuario con los SDK. Mantenga esta clave en un lugar seguro para proteger su red y sus datos.

También puede crear una carpeta de MSP a la que puede hacer referencia el cliente de CA de Fabric utilizando el supervisor de red y las API de Swagger.

- **cacerts** e **intermediatecerts**: puede recuperar estos certificados con las [API de Swagger](/docs/services/blockchain/howto/swagger_apis.html) emitiendo una solicitud Get a la API de MSP.
- **signcerts** y **keystore**: para generar estos certificados, pulse el botón **Generar certificados** en el panel "Entidad emisora de certificados". Se abre una ventana emergente con dos certificados. Copie y almacene el **Certificado** en signcerts y la **Clave privada** en el almacén de claves. Guarde estos certificados en un lugar seguro, ya que no se almacenan en la plataforma.

Muchos componentes de Fabric contienen información adicional dentro de su carpeta de MSP. Por ejemplo, si trabaja con un igual remoto, es posible que vea las carpetas siguientes:

- **admincerts:** esta carpeta contiene la lista de administradores para esta organización o componente. Debe cargar el signCert en esta carpeta si trabaja con un igual remoto desde la línea de mandatos o desde los SDK. Si utiliza el cliente de CA de Fabric, también necesitará una carpeta de certificados de administrador en el MSP que contiene los signCerts correspondientes que se deben reconocer como certificados de
administrador.
- **tls:** esta es una carpeta donde se almacenan los certificados TLS que se utilizan para comunicarse con otros componentes de la red.

Para obtener más información sobre la estructura de los MSP, consulte [Pertenencia ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Pertenencia") y [Proveedores de servicios de pertenencia ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") en la documentación de Hyperledger Fabric.

## Inscripción y registro mediante el cliente de CA de Fabric
{: #enroll-register-caclient}

También puede utilizar el cliente de CA de Fabric para generar certificados y registrar una nueva identidad con la entidad emisora de certificados. En las instrucciones siguientes se generan certificados mediante su identidad de administración y luego se utilizan dichos certificados para registrar un nuevo cliente. Para obtener más información sobre la inscripción mediante el cliente de CA de Fabric y la generación de certificados, consulte
[Proveedores de servicios de pertenencia](#msp).

### Inscripción mediante el cliente de CA de Fabric
{: #enroll-app-caclient}

1. Descargue los [binarios de fabric-ca](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) en la máquina local, extráigalos y muévalos a una carpeta como `$HOME/fabric-ca-platform/`. Vaya al directorio al que ha movido los binarios del cliente para poder hacer referencia a los mismos directamente en sus mandatos.
    ```
    cd $HOME/fabric-ca-platform/
    ```
    {:codeblock}

2.  Establezca la vía de acceso en la que el cliente puede crear las claves. Asegúrese de eliminar el material de configuración que ha creado el intento anterior. Si no ha ejecutado el mandato `enroll` anteriormente, la carpeta `msp` y el archivo `.yaml` no existen.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3. Descargue los certificados TLS de {{site.data.keyword.cloud_notm}} dependiendo del plan de servicio, la ubicación y el clúster que utilice. Puede encontrar su plan de servicio basándose en el URL de la entidad emisora de certificados.
  - Certificado TLS raíz para el Plan inicial
    - EEUU: [us01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sídney: [aus01.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert")<!--; [aus02.blockchain.ibm.com.cert ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")-->
  - [Certificado TLS raíz para el Plan empresarial ![icono de enlace externo](images/external_link.svg "icono de enlace externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Guarde el contenido en una carpeta, por ejemplo ``$HOME/tls``. Este paso permite que se cifre el flujo de datos en la conexión.

4. Abra el archivo JSON de **Perfil de conexión** desde la pantalla "Visión general" del supervisor de red y busque las variables siguientes:
  * URL para la CA: `url` en `certificateAuthorities`
  * ID de usuario administrador: ``enrollId``
  * Contraseña de administración: ``enrollSecret``
  * Nombre de la CA: ``caName``

5. Puede utilizar el cliente de CA de Fabric para enviar una llamada `enroll` a la entidad emisora de certificados pasando la vía de acceso de certificados TLS y las cuatro series anteriores con el mandato siguiente:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Debe ejecutar este mandato en el directorio al que ha movido el cliente de CA de Fabric. Una llamada real es similar al siguiente mandato de ejemplo:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

6. Busque el certificado de administración en `$FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem/.fabric-ca-client/msp/signcerts/cert.pem`. Luego podrá subir el certificado de administración a la red blockchain del Supervisor de red. Para obtener más información sobre la adición de certificados, consulte [el separador "Certificados" del panel "Miembro"](/docs/services/blockchain/v10_dashboard.html#members) en el supervisor de red.

  También puede encontrar el certificado raíz de la CA y la clave privada de administración en los directorios siguientes:
  * Certificado raíz de la CA: `$FABRIC_CA_CLIENT_HOME/msp/cacerts/--<ca_name>.pem`
  * La clave privada de administración: `$FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Para ver un ejemplo de dónde realizaría la inscripción utilizando el cliente de CA de Fabric y utilizaría los certificados generados para trabajar con un componente de la red, consulte las instrucciones sobre cómo
[trabajar con un igual remoto](/docs/services/blockchain/howto/peer_operate_icp.html#peer-cli-operate).

### Registro mediante el cliente de CA de Fabric
{: #register-app-caclient}

1. Emita el mandato siguiente para encontrar su afiliación y el nombre de su organización en la red blockchain.
  ```
  ./fabric-ca-client affiliation list --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Debería ver información parecida a la del siguiente ejemplo:
  ```
  affiliation: ibp
      affiliation: ibp.PeerOrg1
  ```

  Anote el segundo valor de **affiliation**, por ejemplo `ibp.PeerOrg1`. Debe utilizar este valor en el mandato siguiente.

2. Ejecute el mandato siguiente para registrar el igual.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Especifique un nombre y una contraseña para el igual y sustituya `name` y `password` por el nombre y la contraseña del igual. Anote esta información. La necesitará cuando configure el igual. Por ejemplo:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw  --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```

  Solo puede registrar una identidad una vez. Si tiene algún problema, intente un mandato con un nuevo nombre de usuario y contraseña.

  Cuando el mandato finalice correctamente, debería ver información parecida a la del siguiente ejemplo:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```

---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: create identities, manage identities, Certificate Authorities, register, enroll, TLS CA, wallet, certificate expiration

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:important: .important}
{:note: .note}
{:pre: .pre}

# Creación y gestión de identidades
{: #ibp-console-identities}

Los nodos de {{site.data.keyword.blockchainfull_notm}} Platform se basan en Hyperledger Fabric y crean redes blockchain con permisos. Esto significa que todos los participantes del consorcio de blockchain necesitan tener identidades que sean verificadas continuamente por la infraestructura de claves públicas. La consola de {{site.data.keyword.blockchainfull_notm}} Platform le permite crear estas identidades mediante las entidades emisoras de certificados (CA) de la organización. Debe almacenar estas identidades en la cartera de la consola para poderlas utilizar cuando trabaje con la red.

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.

## Gestión de entidades emisoras de certificados
{: #ibp-console-identities-manage-ca}

Una CA es similar a un notario público que actúa como punto de confianza entre las múltiples partes, cada una de las cuales es una organización de un consorcio que mantiene su propia CA. La CA crea las identidades que pertenecen a su organización y emite para cada identidad un certificado para firmas y una clave privada. Estas claves son las que permiten que todos los nodos y aplicaciones firmen y verifiquen sus acciones. Para obtener más información sobre cómo se utilizan las CA para establecer una identidad, visite [el tema sobre identidades ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "identidad") en la documentación de Hyperledger Fabric.

Cuando crea una CA utilizando la consola de {{site.data.keyword.blockchainfull_notm}} Platform, se crean dos CA con el mismo URL de punto final: una CA raíz y una CA de TLS. Puede ver ambas CA bajo el mismo nombre de visualización en la página **Nodos** de la consola. La CA raíz proporciona claves a los nodos y a las aplicaciones. La CA de TLS proporciona certificados que se utilizan para proteger la comunicación dentro de la red. Para obtener más información sobre TLS en la red, consulte [Utilización de la CA de TLS](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).

Cuando cree nodos, debe utilizar la CA raíz para crear las siguientes identidades necesarias para desplegar, operar e interactuar con la red:
- **Administrador de CA:** la CA contiene una identidad de administrador de CA predeterminada. Esta identidad tiene un ID y un secreto de inscripción, análogos a un nombre de usuario y una contraseña, que se especifican durante el despliegue de la CA. Puede utilizar este nombre de usuario y contraseña de administrador para trabajar con la CA y crear nuevas identidades. Si ha creado una CA mediante la consola, el administrador de CA de la CA raíz es también el administrador de la CA de TLS, a menos que decida crear un administrador de CA de TLS independiente.
- **Iguales o clasificadores:** tiene que registrar una identidad para cada uno de los iguales y los clasificadores que pertenecen a su organización. Luego los nodos utilizarán estas identidades durante el despliegue para generar las claves que necesitan para participar en la red. Para motivos de seguridad, cree un ID y un secreto de inscripción exclusivos para cada nodo que despliegue.
- **Administradores de iguales o de clasificadores:** los nodos de {{site.data.keyword.blockchainfull_notm}} Platform se despliegan con los certificados para firmas de las identidades de los administradores de componentes que contienen. Estos certificados permiten a los administradores trabajar con el componente desde un cliente remoto o mediante la consola.
- **Administradores de organización:** cuando se une a un consorcio alojado por un servicio de ordenación, proporciona los certificados para firmas de las identidades que se convertirán en los administradores de la organización. Puede utilizar estas identidades para crear o editar canales.
- **Aplicaciones:** las aplicaciones deben firmar sus transacciones antes de enviarlas para que la red las valide. Tiene que crear identidades que pueda utilizar para firmar transacciones procedentes de sus aplicaciones cliente.

Puede utilizar la consola para crear estas identidades siguiendo el [proceso de registro](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Después de registrar las identidades de administrador, debe emitir para cada identidad un certificado para firmas y una clave privada, proporcionar el certificado para firmas a la definición de MSP de la organización y añadir la identidad a la cartera de la consola. Puede completar estos pasos para una identidad de administrador cuando [cree su MSP de organización](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Puede utilizar identidades independientes como administradores de organización o administradores de nodo, o bien puede utilizar una identidad para realizar ambas tareas. En la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) se utiliza una identidad que es el administrador de cada organización creada en la guía de aprendizaje.

## Establecimiento de una identidad de CA
{: #ibp-console-identities-ca-identity}

Antes de trabajar con identidades, tiene que establecer la identidad del administrador de CA, ya sea utilizando la identidad de administrador creada durante la creación de la CA o estableciendo una nueva. Abra la CA en el separador **Nodos**.

El ID de inscripción de la identidad activa actualmente aparece en la parte izquierda del panel de la CA, bajo el nombre de CA, la ubicación del nodo y la versión de Fabric. Puede utilizar esta identidad para crear otras identidades utilizando el botón **Registrar**. El administrador de CA también le permite obtener la lista de identidades registradas y, a continuación, utilizar dichas identidades para generar certificados utilizando el botón
**Inscribir**.

Para cambiar a una identidad distinta que desee utilizar como administrador de CA, pulse sobre la identidad establecida actualmente como administrador de CA. Esto abrirá el control deslizante **Asociar identidad**. Puede utilizar el separador **ID de inscripción** para proporcionar el ID de inscripción y el secreto de otro administrador de CA. Puede especificar una identidad existente en la cartera mediante el separador **Identidad existente**. Como alternativa, puede utilizar el separador **Nueva identidad** para cargar un archivo que contiene los certificados, en formato base64 o PEM, para un administrador nuevo, o para cargar un archivo JSON que contiene los certificados.

No todas las identidades tienen la capacidad de registrar nuevos usuarios. Para obtener más información, incluida la forma de establecer un administrador de CA adicional, consulte [Creación
de nuevos administradores de CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin).
{: note}

## Registro de identidades
{: #ibp-console-identities-register}

El primer paso en la creación de una identidad se conoce como **registro**. Durante el registro, se crea un ID y un secreto de inscripción que pueden ser utilizados por un nodo o por un administrador de organización para **inscribir** esta identidad mediante la generación de un certificado para firmas y una clave privada.  

Debe especificar la información siguiente cuando registre una nueva identidad con la CA.

- **ID de inscripción** y **Secreto de inscripción**: esta identidad tiene un ID y un secreto, análogos a un nombre de usuario y una contraseña, que se especifican durante el despliegue de la CA. Puede utilizar este ID y secreto de administrador para crear certificados con esta identidad utilizando esta consola o el cliente de CA de Fabric.
- **Tipo**: cuando se desplegó la CA, el administrador especificó los tipos de identidades emitidos por la CA. Algunos tipos de identidad comunes de ejemplo son el igual, el clasificador y el cliente (aplicaciones). Seleccione el tipo de identidad para este usuario en la lista de tipos disponibles.
- **Afiliación**: (Opcional) solo para usuarios avanzados. Este campo solo es visible si se han definido afiliaciones para la CA. Una afiliación es la parte de la organización que desea asociar con este usuario. Podría ser, por ejemplo, un departamento o una unidad que trabaja con una aplicación o con un igual. Se puede restringir un administrador de CA en particular para que solo pueda registrar usuarios dentro de su propio departamento dentro de una organización estableciendo su afiliación. Las afiliaciones de CA se definen utilizando el
[Mandato de afiliación ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/clientcli.html#affiliation-command "Mandato de afiliación") de la CA de Fabric.
- **Número máximo de inscripciones**: si lo desea, especifique el número de veces que puede inscribir o generar certificados utilizando esta identidad. El hecho de especificar un número limitado de inscripciones ayuda a proteger la seguridad de los nodos y de las aplicaciones. El valor predeterminado es un número ilimitado de inscripciones.
- **Atributos**: opcionalmente, puede especificar los atributos de [control de acceso basado en atributo ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "control de acceso basado en atributo") que desee para el usuario. Por ejemplo, puede utilizar esta sección para [crear otro administrador de CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) con autorización para registrar e inscribir nuevas identidades. Encontrará una lista completa de los atributos de CA de Fabric disponibles en la sección [Registro de una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registro de una nueva identidad") de la guía del usuario de CA de Fabric.

Pulse el botón **Registrar usuario** en el panel de visión general de la CA para crear un nuevo usuario. Asegúrese de que ha [establecido la identidad](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) utilizando una identidad que tenga capacidad para registrar nuevos usuarios antes de intentar esta tarea. Generalmente es el usuario `admin`. Si el botón es gris, significa que no ha establecido una identidad o que la identidad no puede crear nuevas identidades.  

Al pulsar **Registrar usuario** se abrirá una serie de paneles laterales:
1. En el primer panel lateral, especifique el **ID de inscripción** y el **Secreto de inscripción** de la nueva identidad. **Guarde estos valores**, ya que no se almacenan en la consola.
2. Seleccione el **Tipo** de identidad. La lista desplegable contiene la lista de tipos a los que da soporte esta CA.
3. Si se han definido afiliaciones para esta CA, tendrá la opción de asociar una afiliación con el usuario. De lo contrario, no se mostrará la lista desplegable de afiliaciones. Marque el recuadro de selección **Utilizar afiliación raíz** para el usuario si desea que tenga la afiliación raíz y que pueda ver a todos los demás usuarios registrados con esta CA. Al deseleccionar el recuadro de selección **Utilizar afiliación raíz**, puede seleccionar una afiliación específica de la lista que asociar a este usuario.
4. Especifique las **Inscripciones máximas** permitidas para esta identidad. Si no se especifica, el valor predeterminado es un número ilimitado de inscripciones.
5. En el último panel lateral, añada los **Atributos** de la identidad que está creando.

Después de pulsar **Registrar**, la nueva identidad se añadirá a la lista de **Usuarios autenticados**
que ha creado la CA. Estas identidades aparecen listadas por su **ID de inscripción**, junto con su **Tipo** y **Afiliación**. Al pulsar sobre una identidad en la tabla se abrirá un panel lateral que le permite ver el **Número
máximo de inscripciones** y los **Atributos** que se han creado durante el registro.

### Creación de nuevos administradores de CA
{: #ibp-console-identities-ca-admin}

De forma predeterminada, solo el administrador de CA que se ha creado durante el despliegue tiene capacidad para registrar nuevas identidades. También puede crear otras identidades con capacidad para registrar usuarios nuevos mediante el panel **Atributos**
del proceso de registro.

En el panel lateral 4, pulse el botón **Añadir atributo**. Especifique el **nombre de atributo** `hf.Registrar.Roles`. Especifique el **valor de atributo** `*`. También puede utilizar este panel para crear una identidad que solo pueda registrar determinados tipos de tipos de identidad, como clientes o iguales, o dentro de una determinada afiliación. También puede crear una identidad que tenga capacidad para revocar una identidad y todos los certificados que ha emitido la identidad. Encontrará una lista completa de los atributos en la sección [Registro de una nueva identidad ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registro de una nueva identidad") de la guía del usuario de CA de Fabric.

## Inscripción de una identidad
{: #ibp-console-identities-enroll}

Puede generar el certificado para firmas y la clave privada para cada identidad que se haya registrado con la CA. Si ha registrado identidades de administrador adicionales con la CA, puede generar las claves para la identidad de administrador y luego incluirlas adicionalmente cuando [cree la organización MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp).

Antes de inscribir una identidad, debe [establecer la identidad](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) para poder trabajar con la CA. Normalmente, se establece en la identidad de administrador que se ha especificado al crear la CA. Puede confirmar que la CA se ha establecido en dicha identidad examinando la página de detalles de la CA y viendo el ID de inscripción de la identidad actualmente activa que hay junto al nombre de CA. Después de confirmar que la identidad está establecida en la identidad del administrador, pulse **Inscribir identidad** en el menú de desbordamiento del usuario para generar el certificado y la clave para cualquier usuario registrado con la CA.

- Especifique el `Secreto de inscripción` del usuario.
- En el paso siguiente se muestran las claves generadas.
  - El certificado para firmas se muestra en el campo **Certificado**. Este certificado también se conoce como certificado de inscripción, certificado de firma o signCert. Debe exportar el signCert a un archivo en el sistema local para que se pueda utilizar al crear una aplicación cliente con la extensión de VS Code.
  - Encontrará la clave privada correspondiente en el campo **Clave privada**. De nuevo, tiene que exportar la clave privada al sistema local para utilizarla con una aplicación cliente creada con la extensión de VS Code.
  - El certificado y la clave privada que se han creado pulsando **Inscribir identidad** solo se generan una vez y no se almacenan ni en la consola ni en el navegador. Cuando se pulsa el botón **Inscribir identidad**, se contabiliza en el número máximo de inscripciones establecido para el administrador de CA. Como parte de este proceso de inscripción, debe almacenar el certificado para firmas y la clave privada descargando la identidad en el sistema de archivos local o añadiendo la identidad a la cartera de la consola. Especifique un nuevo nombre para este certificado para firmas y clave privada en el campo **Nombre** para poder recuperarlos.
- **Importante:** pulse **Exportar identidad** para descargar el certificado y la clave en el sistema de archivos local como un solo archivo en formato JSON. Usted es el responsable de proteger y gestionar estas claves.
- Pulse **Añadir identidad a la cartera** para añadir estos certificados a la cartera de la consola. Luego podrá encontrar el nombre y las claves de esta identidad en un nuevo mosaico en el [separador cartera](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)

También puede utilizar el cliente CA de Fabric o los SDK de Fabric para inscribir las identidades que ha creado en la consola. La consola le proporciona toda la información que necesitará para poder competir en estos pasos. Asegúrese de que ha guardado el **ID de inscripción**
y el **Secreto de inscripción** que ha especificado durante el registro.

## Utilización de la CA de TLS
{: #ibp-console-identities-tlsca}

La comunicación dentro de la red se protege mediante certificados TLS. TLS cifra la comunicación entre los nodos y entre los nodos y las aplicaciones. El uso de TLS impide a los atacantes interrumpir la comunicación entre los nodos o leer las transacciones enviadas desde sus aplicaciones. Las claves y los certificados utilizados para TLS son distintos de los certificados utilizados para firmar y validar las transacciones y los emite otra entidad emisora de certificados.

Cada CA creada por la consola de {{site.data.keyword.blockchainfull_notm}} Platform contiene una CA raíz y una CA de TLS. Puede ver ambas CA bajo el mismo nombre de visualización en el separador Nodos de la consola. Pulse el panel de visión general de la CA y luego pulse **Entidad emisora de certificados de TLS** para trabajar con la CA de TLS. La CA de TLS tiene el [mismo registro](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) y [proceso de inscripción](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) que la CA raíz. Ambas CA se despliegan con el mismo ID de administrador de CA y con el mismo secreto.

Cada nodo igual o clasificador que despliegue necesita generar un certificado TLS público.  Al crear los nodos utilizando la consola, puede utilizar el mismo ID de inscripción y el mismo secreto que los que ha utilizado para generar la identidad del igual o el clasificador para el ID de inscripción y secreto de TLS, ya que la CA de TLS utiliza el mismo repositorio de usuarios como CA de organización. El nodo utilizará luego esta identidad para generar su certificado TLS durante el despliegue. Cualquier aplicación que tenga que comunicarse con el clasificador o con el igual necesitará este certificado. Para encontrar el certificado TLS de un nodo, vaya al panel de visión general del nodo y pulse Valores. También puede encontrar los certificados TLS de sus iguales y clasificadores en el perfil de conexión que se puede descargar [desde el separador de contratos inteligentes](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).

Al crear un igual o un clasificador utilizando la consola, también puede utilizar la CA de TLS para especificar un nombre de dominio adicional para cada nodo. Especifique el nuevo nombre de dominio en el campo **Nombre de host de CSR de TLS** cuando despliegue el clasificador o el igual. Este nombre de host se añadirá a la lista de nombres comunes en el certificado TLS que se ha emitido en el nodo.

## Caducidad de los certificados
{: #ibp-console-identities-expiration}

Los certificados generados mediante las CA de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 caducan después de un año. El periodo de caducidad es el mismo para los certificados generados mediante los SDK de Fabric, mediante el cliente CA de Fabric o mediante la consola. Si los certificados caducan, las aplicaciones ya no podrán interactuar con la red y tendrá que volverse a inscribir para generar nuevos certificados. Si ha alcanzado el límite de inscripción de un usuario, puede registrar un nuevo usuario y luego inscribirlo.

Puede utilizar la línea de mandatos para comprobar la fecha de caducidad de los certificados. En primer lugar, deberá convertir los certificados que se encuentran en base 64 a formato PEM con el mandato siguiente en la máquina local:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
echo <base64_string> | base64 --decode $FLAG > <key>.pem
```
{:codeblock}

Ejecute el mandato siguiente para visualizar el certificado codificado de PEM en un formato legible por el usuario:
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

El certificado se parecerá al del ejemplo siguiente:

```
Certificate:
    Data:
        Version: 3 (0x2)
    Serial Number:
        20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18
Signature Algorithm: ecdsa-with-SHA256
    Issuer: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA
    Validity
        Not Before: Nov 28 19:18:00 2018 GMT
        Not After : Nov 28 19:23:00 2019 GMT
    Subject: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin
    ...
    ...
```

Encontrará la fecha de caducidad en la sección **Validity**, después de `Not After:`. En este ejemplo, el certificado caducará el 28 de noviembre de 2019.

## Almacenamiento de identidades en la cartera de la consola
{: #ibp-console-identities-wallet}

La cartera almacena las identidades y las claves que utiliza la consola de {{site.data.keyword.blockchainfull_notm}} Platform para trabajar con los nodos de la red. Debe añadir el igual, el clasificador y los administradores de la organización a esta cartera para poder utilizar la consola para trabajar con canales y contratos inteligentes. También puede utilizar la cartera para almacenar convenientemente las identidades que utilizarán las aplicaciones. Puede utilizar la cartera para exportarlos en cualquier momento. Utilice el panel de navegación de la izquierda para examinar el panel de visión general de la cartera. Puede añadir, actualizar y exportar identidades desde esta cartera utilizando la pantalla de visión general.

La cartera es un componente de la consola, no un servicio independiente. Guarda en el almacenamiento local del navegador las claves que se utilizan para acceder a la consola, en lugar de hacerlo en el sistema de archivos local. Si accede a la consola desde otro navegador, o si inicia una sesión de navegación privada, no podrá acceder a las identidades almacenadas en la cartera. **Se recomienda exportar las identidades de administrador de la consola y guardarlas en un lugar seguro**.
{:important}

### Adición de identidades
{: #ibp-console-identities-add}

Puede añadir una identidad de administrador a la cartera cuando [cree su MSP de organización](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Una CA gestionada por la consola también puede añadir una identidad a su cartera durante el [proceso de inscripción](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

Puede utilizar el botón **Añadir identidad** en la pantalla de visión general para importar una identidad directamente en la cartera. Al pulsar este botón, se abrirá un panel lateral que le permite añadir el certificado para firmas y la clave privada de una identidad.
- **Nombre:** especifique un nombre de identidad que se utiliza únicamente para la referencia.
- **Certificado:** cargue un archivo que contenga el certificado para firmas de la identidad (en formato base64 o PEM) que haya generado utilizando la CA.
- **Clave privada:** cargue un archivo que contenga la clave privada de la identidad (en formato base64 o PEM) que haya generado utilizando la CA.


También puede añadir una identidad cargando un archivo JSON en el formato siguiente. También puede utilizar el botón **Cargar JSON**
para cargar varias identidades a la vez.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Si ha inscrito una identidad utilizando el cliente de CA de Fabric o los SDK de Fabric, las claves se deben convertir del formato PEM al formato base64. Puede convertir certificados al formato base64 con el mandato siguiente en la máquina local:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### Visualización y actualización de identidades
{: #ibp-console-identities-update-identities}

En el separador **Cartera**, pulse en un mosaico para ver, actualizar o eliminar una identidad de la cartera. Es posible que sea necesario actualizar sus identidades si sus certificados han caducado y necesitan que se emitan nuevas claves de la CA. También puede utilizar este separador para suprimir claves de la consola y del sistema local.

Al pulsar una identidad se abre un panel lateral que muestras sus certificados y claves privadas en formato base64. Pulse **Exportar** para descargar los certificados de la identidad en el sistema de archivos local. Pulse **Actualizar** para cambiar el nombre de la identidad en la cartera o para pegar un nuevo conjunto de claves en el panel. Pulse **Eliminar** cuando ya no tenga que utilizar esta identidad y desee suprimir sus claves.

## Asociación de identidades
{: #ibp-console-identities-associate-admin}

Debe proporcionar el certificado para firmas de su organización y los administradores de nodos [a la definición de MSP de la organización](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Los nodos o canales que cree la consola utilizan certificados de la definición de MSP para comprobar quién es un administrador válido. Como resultado, el mismo certificado para firmas y la misma clave privada que haya utilizado para añadir un certificado de administrador a la definición de MSP se deben almacenar en la cartera de la consola.

Cuando utilice la consola para crear un clasificador o un igual, encontrará un panel **Asociar identidad**. Seleccione una identidad en la cartera cuyo certificado también esté dentro de la definición de MSP de la organización. También tendrá que seleccionar una identidad de administrador en la sección **Asociar identidad** cuando cree o edite un canal. Esto permitirá que la consola sepa qué identidad utilizar cuando se comunique con sus iguales, clasificadores y consorcio de servicio de ordenación. La identidad asociada actualmente con un igual o un servicio de ordenación será visible en la parte izquierda del panel de nodo, bajo el nombre, la ubicación del nodo y la versión de Fabric.
